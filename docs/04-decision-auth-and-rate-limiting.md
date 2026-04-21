# 04 · Decision · Auth & Rate Limiting

## Problem

A commercial API needs two things that most v0 APIs either skip or over-engineer:

1. **Authentication** — who's this, and what can they do?
2. **Rate limiting** — stop them from accidentally or maliciously exhausting our resources

Most startups reach for a managed gateway (Kong, Tyk, AWS API Gateway) or build an OAuth2 server (Auth0, Supabase Auth, homebrew). Both are overkill for what this actually needs: *verify a key exists, check per-key quotas, meter requests*. No user accounts, no permissions tree, no password reset.

## What I picked

**Stateless HS256 JWT** with embedded per-key quotas, plus a **dual-backend sliding-window rate limiter** (Upstash Redis with in-process fallback).

Total: three files under `lib/api/`. No database. No gateway. No vendor lock-in.

## How it works

### Auth

```ts
// Minted by scripts/mint-api-key.mjs
jwt.payload = {
  sub: "key_xyz",        // identifies the key
  quotaPerMin: 600,      // per-minute burst
  quotaPerDay: 100000,   // per-day sustained
  scopes: ["*"],         // optional endpoint allowlist
  exp: 1900000000        // optional expiration
}
```

Every API request:
1. Extract `Authorization: Bearer <jwt>`
2. Verify HS256 signature via Web Crypto (no Node crypto needed → runs on Edge)
3. Check `sub` against `ASTROLOGY_API_DENYLIST` env var (CSV of revoked IDs)
4. Pass `{ sub, quotaPerMin, quotaPerDay }` to the rate limiter

**No database lookup.** The JWT is self-contained. Horizontal scaling is zero-configuration — every serverless instance verifies the same token independently.

### Rate limiting

Sliding window, dual buckets (per-minute + per-day), dual backend:

```ts
if (UPSTASH_REDIS_REST_URL && UPSTASH_REDIS_REST_TOKEN) {
  // Global sliding window across all instances
  return upstashBackend()
}
// Fallback: in-process token bucket, per-instance
return inMemoryBackend()
```

Response headers on every call:
- `x-ratelimit-limit-minute`, `x-ratelimit-remaining-minute`
- `x-ratelimit-limit-day`, `x-ratelimit-remaining-day`
- `retry-after` on 429

## Why this shape

### Why JWT over opaque keys backed by a database?

| | JWT (chosen) | Opaque key + DB |
|---|:-:|:-:|
| DB dependency on hot path | 🟢 none | 🔴 every request |
| Horizontal scaling | 🟢 trivial | 🟡 cache required |
| Revocation speed | 🟡 next deploy (or Upstash denylist) | 🟢 instant |
| Rotation | 🟢 new secret + denylist | 🟡 coordinate writes |
| Quota changes per key | 🟡 mint new JWT | 🟢 UPDATE row |
| Operational complexity | 🟢 zero | 🔴 schema + migrations + cache |
| Cost at scale | 🟢 zero infra | 🔴 DB reads |

The tradeoff I'm accepting: quota changes and revocations require either a new JWT or a denylist env update. For a v1 with a handful of paid customers, this is fine — and the email-based manual workflow matches the CLI-minting workflow. When we have hundreds of customers, the denylist moves to Upstash (already configured) and we're still database-free.

### Why Upstash + in-process fallback, not one or the other?

- **Upstash only**: a single region outage takes down the whole API. Rate-limit reads are on the hot path.
- **In-process only**: doesn't hold across serverless instances or cold starts. A customer at their quota limit can get N× quota just by hitting a different Lambda.
- **Dual-backend**: Upstash for correctness when available. Graceful degradation to per-instance when not. The interface is identical — `consume(key, limit)` returns `{ ok, remaining }` either way. The calling code doesn't know which backend served it.

This is the exact pattern the Supabase + optional Sentry observability layer uses in the sister SaaS — I reused the mental model.

### Why a demo guard instead of just gating the whole API behind JWT?

The `/dashboard` live demo exists specifically so a prospective customer can get a natal chart without signing up. That conversion moment is critical; killing it with "please sign up to try" is a documented DX anti-pattern.

But an unauthenticated endpoint is a gift to scrapers. So the `/api/birth-chart` endpoint has three layers of abuse protection, none of which are JWT:

1. **Origin check** — `Origin` or `Referer` must match our domain list (localhost, our Vercel domain, configurable via `DEMO_ALLOWED_ORIGINS`). Browsers auto-set this; curl attackers have to fake it.
2. **Per-IP rate limit** — 5/min, 50/day. Uses the same rate-limit module with a distinct `demo:ip:` prefix, so it never touches paid customer quotas.
3. **Kill switch** — `DISABLE_DEMO=true` env var returns 503 immediately. No redeploy needed if abuse spikes.

The paid `/api/v1/*` surface keeps the JWT + quota stack unchanged.

## What I considered and rejected

### Kong / Tyk / managed gateway

- Adds a deploy target and a config language
- Ties our surface to theirs; harder to move later
- Cost scales with request volume
- For 43 endpoints and one backend, the abstraction cost exceeds the benefit

### Auth0 or Supabase Auth

- Designed for user accounts, not API keys
- Extra network hop per request to verify a session
- Conflates two problems (end-user auth vs. programmatic API auth) and we don't have the first

### Clerk / WorkOS / SSO-first platforms

- Same critique as above, plus enterprise-feature pricing that doesn't match a v1 API product

### Full OAuth2 client_credentials flow

- The correct answer for an enterprise API in two years
- Overkill at v1; none of our target segments need delegated access or refresh tokens
- Happy path: upgrade to OAuth2 later, keep JWT structure

## Retrospective signals to watch

After 30 days in production, check:

- How often did the Upstash fallback fire? (If > 0.1%, pin to a specific Upstash region or add a second backend)
- Did any customer hit quota unexpectedly because of per-instance fallback drift?
- Did the denylist stay small enough to live in env vars? (Threshold: 100 revoked keys; beyond that, move to Upstash)
- How many support emails were about "why do I have a rate limit?" vs "how do I get a higher limit?" (High first number = docs problem; high second = pricing conversation)

## Decision

**Stateless JWT + dual-backend rate limiter.** Three files. No database. Scales horizontally from day one. Keeps the "commercial API without the commercial-API overhead" promise.

---

**Next:** [05 · Decision · licensing split](./05-decision-licensing-split.md)
