# 05 · Decision · Licensing Split

## The choice

- **Server code** (the engine + API) → **proprietary source**, commercial license required for re-use
- **SDK code** (TypeScript, Python, Go) → **MIT**, embed anywhere

Two licenses in one repo. Deliberate.

## Why not one license

### All-MIT (simplest)

Would let anyone fork the engine, strip the pricing page, and relaunch. The one-day rewrite becomes commoditized overnight. The moat exists only if it's licensed.

### All-proprietary (easiest to write)

Would kill SDK adoption. Developers will not embed a proprietary dependency in their app without a 30-minute legal review. Every integration becomes friction. That's the exact problem I'm trying to solve vs the dominant astronomy library (see [03 · Decision · engine from scratch](./03-decision-engine-from-scratch.md)).

### GPL

Would contaminate anything customers build on top. Unusable in commercial apps. Guarantees zero paying customers.

### Source-available with commercial carve-out (Elastic, BSL)

The server-side model is roughly this — personal/educational use permitted with attribution, commercial use requires written license. Less absolute than pure proprietary, more defensible than MIT. Matches what customers actually want: clarity about what they're paying for.

## How the split works in practice

The root `LICENSE` file defines the server terms — personal use, evaluation, education-with-attribution permitted; commercial use requires a paid license. Every path except `sdk/typescript/` and `sdk/python/` (and the Go SDK in its separate repo) falls under it.

Each SDK directory carries its own `LICENSE` — MIT, full terms, own copyright notice. When an SDK is published to npm / PyPI / Go modules, only that directory ships. The server license doesn't travel with it.

```
tuffys-ai-astrology/
├── LICENSE                       # ← proprietary-source
├── NOTICE                        # ← public-domain attributions
├── sdk/
│   ├── typescript/
│   │   └── LICENSE               # ← MIT
│   └── python/
│       └── LICENSE               # ← MIT
└── ...everything else            # covered by root LICENSE
```

The Go SDK lives in its own public GitHub repo (`kriya-go`) with its own MIT `LICENSE`. This is a Go-idiom choice more than a licensing one — Go packages are discovered by repo URL, not registry name.

## What the NOTICE file does

The engine is built from public-domain sources, but attribution is both legally and ethically owed:

- **Meeus** — *Astronomical Algorithms* (textbook, algorithms public domain)
- **Bretagnon & Francou** — VSOP87 theory (IMCCE public FTP mirror)
- **Chapront-Touzé & Chapront** — ELP2000 lunar theory
- **Standish** — JPL Keplerian element tables (public domain)
- **Espenak & Meeus** — eclipse canon

Every author is credited in `/NOTICE`. The file also credits every runtime dependency (Next.js, Tailwind, etc.) per standard open-source hygiene. Customers who paste the project into a legal review see a clean, specific, source-cited accounting of every input.

## Why the SDKs are MIT specifically (not Apache 2.0 or BSD)

The choice here is almost mechanical. MIT is what developers expect for a client library. It is the lowest-friction choice for anyone copy-pasting into a commercial app. Apache 2.0's patent grant is a stronger protection in theory, but in the categories of companies likely to adopt this API, "MIT-licensed" is a shorter conversation than "Apache-2.0-licensed" — and the patent risk is negligible for a wrapper over a REST API.

## The integration-friction frame

Every licensing decision should answer one question: *what happens when a prospective customer's legal team reviews this?*

| Under MIT SDK + proprietary server | Under the dominant library (GPL) | Under pure proprietary |
|---|---|---|
| ✅ Embed SDK in product without review | ❌ Review required, GPL contamination risk | ❌ Review required, custom license |
| ✅ Server is a SaaS — their code never touches ours | ❌ If they self-host, they're contaminated | ❌ Review required |
| ✅ Clear separation: they ship SDK, pay for SaaS | ❌ Dual-licensing rabbit hole | ❌ Per-seat licensing discussions |
| ✅ 10-second yes/no from their legal | ❌ 10-day legal review | ❌ 10-day legal review |

The shape customers want is: *I pay you for the API, you give me a tiny MIT library to call it, nobody has to talk to lawyers.* The licensing split produces exactly that shape.

## What this means for pricing power

Because the server is proprietary, the moat is the engine. That means:

- Pricing can reflect the underlying work (VSOP87 + ELP2000 + Vedic math isn't cheap to rebuild)
- We don't have to compete with free forks
- Enterprise customers wanting self-hosted can buy a commercial source license — and it's a separate SKU with different margins

Because the SDKs are MIT, the entire developer-adoption surface is frictionless. Developers find the Go package on pkg.go.dev, run `go get`, ship to production. No legal. No meetings. No approval chain.

## What would change this

The decision holds as long as these assumptions hold:

- Dev-tool buyers care about licensing clarity more than features
- the dominant astronomy library stays GPL-or-pay-per-deal
- No customer demands a self-hosted version at v1 scale

If a Fortune-500 customer asks for self-hosting in Q3 2026, the server license becomes a negotiated SKU. That's a good problem.

## Decision

**Proprietary-source server + MIT SDKs.** Written with the explicit frame: every licensing choice is answering a legal-review question in the head of a CTO reading this on a Thursday afternoon.

---

**Next:** [06 · SDK strategy](./06-sdk-strategy.md)
