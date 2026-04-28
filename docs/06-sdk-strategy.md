# 06 · SDK Strategy

## Three SDKs, shipped with v1, grown with the engine

- **TypeScript** — `npm install insightsbyomkar` · zero runtime deps · ~150 LOC at v1, generated from the OpenAPI spec going forward
- **Python** — `pip install insightsbyomkar` · stdlib-only · ~190 LOC at v1, regenerated each release
- **Go** — `go get github.com/omkarjaliparthi/tuffys-astrology-go/tuffys` (legacy module path) · `net/http` only · ~250 LOC

All three MIT-licensed. All three publish automatically on GitHub release. v1 launch covered the 43-endpoint surface; current versions cover the **109+ endpoints** the API has grown to over nine semver versions. The TS and Python SDKs are now generated from the OpenAPI 3.1 spec on every release, so endpoint coverage tracks the server automatically.

## Why three

Most v1 APIs ship one SDK (usually TypeScript, because the founder's native stack). That's a mistake when your buyer segmentation looks like this:

| Segment | Preferred language |
|---|---|
| Wellness/spiritual startups | TypeScript (Next.js/React shops) |
| AI product teams | Python (model infra) |
| Dating/compatibility apps at scale | Go (backend services) |

One SDK per segment. None of them will adopt an API that forces them to hand-roll a client. One SDK per segment also signals that the API is "for real" — it's the clearest quality bar a developer uses to judge whether a company is serious about its API product or just running a REST backend.

The cost of maintaining three SDKs of this shape is small because each one is intentionally tiny. They do one thing: wrap fetch/requests/net-http, add auth header, decode errors. No retries, no logging, no caching — by design. Those are the consumer's concerns, not the SDK's.

## Zero-dependency principle

Every SDK sits at zero runtime dependencies.

- **TypeScript** — uses the platform `fetch` API, exposes a `fetchImpl` option for Edge/Bun/Deno
- **Python** — uses `urllib.request` (stdlib); the `requests` path is optional and only activates if already installed
- **Go** — `net/http` from stdlib, nothing else

This is a religion. Every dependency added:
- Raises install size
- Introduces a supply-chain attack vector
- Adds breaking-change exposure
- Creates a transitive license to track

For an SDK that does `HTTP POST + JSON decode`, there is no dependency that's worth any of that. The entire argument for adding e.g. axios to the TS SDK is "it's nicer to write" — and the ~20 lines it costs in the client is a one-time write I did once, vs a permanent tax every consumer pays forever.

## API surface per SDK

Every method maps 1:1 to an endpoint. No cleverness, no aggregations, no "helpful" abstractions.

```ts
// TypeScript
const client = new InsightsByOmkarClient({ baseURL, apiKey });
await client.natalChart({ datetime: "1990-06-15T12:00:00Z", latitude: 51.5, longitude: 0 });
await client.transits({ from, to });
await client.synastry({ personA, personB });
// ... 100+ more methods, one per endpoint
```

```py
# Python
from insightsbyomkar import InsightsByOmkarClient
client = InsightsByOmkarClient(base_url, api_key=api_key)
client.natal_chart(datetime="1990-06-15T12:00:00Z", latitude=51.5, longitude=0)
client.transits(from_=from_dt, to=to_dt)
```

```go
// Go
client := tuffys.New(baseURL, tuffys.WithAPIKey(apiKey))
chart, err := client.NatalChart(ctx, tuffys.Person{...})
```

## Error shape — consistent across all three

```ts
// TS
class InsightsByOmkarError extends Error {
  status: number;
  code: string;
  message: string;
  details?: unknown;
}
```

```py
# Python
class InsightsByOmkarError(Exception):
    status: int
    code: str
    message: str
    details: Optional[Any]
```

```go
// Go
type APIError struct {
    Status  int
    Code    string
    Message string
    Details any
}
```

Every consumer gets the same five fields. A dating app running Go won't be surprised when they later add a Python ML service and see the same shape on the other side.

## Publishing automation

**TypeScript** — `.github/workflows/publish-npm.yml`, triggered on GitHub release. Uses npm provenance (SLSA attestation) — every published version can be traced back to the commit that built it. Requires `NPM_TOKEN` repo secret.

**Python** — `.github/workflows/publish-pypi.yml`, triggered on release. Uses **OIDC trusted publishing** — no PyPI token stored anywhere. GitHub signs an OIDC token, PyPI verifies it against the configured publisher, no secret rotation needed. This is the modern best practice; most teams are still shipping with tokens.

**Go** — no registry. Tag a version (`git tag v0.1.0 && git push --tags`), consumers get it via `go get …@latest`. The separate `tuffys-astrology-go` repo isolates the version history from the main product, which is what Go modules want anyway.

Each SDK has its own `LICENSE` file at the directory root — the proprietary-source server license doesn't travel with it when published. See [05 · Licensing split](./05-decision-licensing-split.md).

## Why not also Ruby / PHP / Java / Rust?

- **Ruby** — small developer population for this category; Rails usage in AI/wellness is low
- **PHP** — same; most adopters would already be Python or Node
- **Java** — enterprise-only; not the v1 target segment
- **Rust** — small audience overlap with "astrology API consumers"; if demand appears, the OpenAPI spec enables rapid generation

The OpenAPI 3.1 spec at `/openapi.json` is the escape hatch. Anyone who needs an SDK in a language we don't ship can run their preferred generator over the spec. We test three; others are customer-driven.

## What the SDKs don't do

**No retries.** Consumer's concern. Different applications have different retry semantics (idempotent GETs vs. billing-tied POSTs) and guessing wrong causes duplicate charges or silent data loss. The APIError carries `status`, the consumer decides.

**No logging.** Consumer's concern. Apps have their own structured logging, their own PII rules. SDK `console.log` is a bug generator.

**No caching.** Consumer's concern. Caching a natal chart is fine; caching a daily transit for the current day is wrong.

**No rate-limit tracking on the client.** The server already returns rate-limit headers. Consumers who want to implement their own backoff can — the data is right there.

**No dependency injection framework, no plugin system, no middleware.** This is a function call wrapper. Every layer added is a layer the consumer would have wanted to control anyway.

## What could break this strategy

- **A customer asking for a framework-specific SDK** (react-query hooks, Vercel-AI-SDK integration). That's a *library on top of the SDK*, not a change to the SDK itself. Ship it separately, still free.
- **OpenAPI spec drift** — if handlers and the spec diverge, consumers break. The CI gate runs spec generation on every PR so divergence is caught early.
- **Demand for async iterators / streaming endpoints** — we don't have any yet. If we add an events stream (e.g., transits over a range), each SDK will need a streaming variant. One sprint each, isolated work.

## Decision

**Three SDKs, zero runtime deps each, MIT-licensed, auto-published via GitHub Actions.** The bet: adopter friction in the first 10 minutes is what converts a visitor into a paying customer, and SDKs are the tool that controls that first 10 minutes.

---

**Next:** [07 · Test strategy](./07-test-strategy.md)
