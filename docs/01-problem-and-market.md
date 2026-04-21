# 01 · Problem & Market

## The gap

Astrology is a surprisingly resilient vertical. Spiritual wellness apps pulled in $1B+ in 2024. Dating apps use birth charts as a retention and engagement lever. AI character products want ground-truth astrological structure instead of hallucinated strings. Meditation platforms layer daily horoscopes onto their morning routines. Any of these could be a customer.

All of them hit the same wall: there is no credible, modern, commercially-licensed astronomy API that covers Western, Vedic, Hellenistic, and electional traditions with first-class SDKs.

The options available on 2026-04-15:

- **AstrologyAPI / Prokerala / FreeAstrologyAPI** — per-request pricing, inconsistent response shapes, rate-limit cliffs, per-tradition packaging that forces customers to subscribe to bundles they don't need. Two of the three are effectively 2010-era stacks with HTTP quirks.
- **The dominant commercial astronomy library** — the gold standard for accuracy. Dual-licensed GPL / commercial. The GPL side contaminates any derivative — including SDKs. The commercial side is negotiated per-deal with the upstream vendor, no published pricing, and most of us would need 30-minute sales calls before we knew what a license cost.
- **Build it yourself** — months of specialized astronomy work most teams don't have, even when the math is in a textbook.

A developer building a compatibility feature in a dating app shouldn't need a pricing call to ship. A wellness startup shouldn't have to choose between aging vendors and a legal grenade. That is the gap.

## Users & segments

The launch targets five segments with deliberately different pricing expectations.

| Segment | What they need | Price sensitivity | Acquisition channel |
|---|---|---|---|
| **Indie builders / solo devs** | Natal chart + daily transits · TS or Python SDK · free tier to explore | High | SEO, GitHub search, Show HN |
| **Wellness / spiritual apps (Series A/B)** | Reliable uptime · predictable flat rate · all tradition coverage · one invoice | Medium | Direct founder-to-founder · referrals · Discord communities |
| **AI product teams** | Structured JSON to ground LLM outputs · low latency · OpenAPI schema | Low | Dev-tool conferences · AI eng newsletters |
| **Dating / compatibility apps** | Synastry + composite at 10K+/day · custom quota · SLA | Low | Outbound · LinkedIn |
| **Academic / research** | Batch historical positions · full accuracy disclosure · citable | Medium | Citations · academic Twitter · research-tool directories |

The first two segments pay the bills in months 1–6. Segments three and four justify the Scale tier. Segment five is low-volume but lends credibility.

## Competitive positioning

The existing field, scored on the axes that matter to a buying developer:

| | Modern DX | Licensing freedom | All traditions | SDK quality | Accuracy disclosure |
|---|:-:|:-:|:-:|:-:|:-:|
| AstrologyAPI | 🔴 | 🟡 | 🟡 | 🔴 | 🔴 |
| Prokerala | 🔴 | 🟡 | 🟢 | 🔴 | 🔴 |
| FreeAstrologyAPI | 🟡 | 🟡 | 🟡 | 🔴 | 🔴 |
| the dominant astronomy library | 🟡 | 🔴 GPL / 💰 commercial | 🟢 | 🟢 | 🟢 |
| **Insights Astrology API** | 🟢 | 🟢 (proprietary server + MIT SDKs) | 🟢 | 🟢 | 🟢 |

The win condition is not "be more accurate than the dominant astronomy library." It's "be the only modern option that a team can adopt without a legal review and without a pricing call."

## Pricing hypothesis

Three tiers, designed so that the upgrade paths are obvious:

| Tier | Price | Quota | Target conversion event |
|---|---|---|---|
| **Developer** | Free | 60 req/min · 1,000/day | Ship a prototype with the SDK |
| **Studio** | $49/mo | 600 req/min · 100,000/day | Post-PMF app scaling past free tier |
| **Scale** | Custom | Custom | Dating/AI companies with volume + SLA needs |

Developer tier has to feel unlimited for solo builders but break at production scale — 60 req/min + 1,000/day hits exactly that shape. Studio tier is priced to be approved on a personal card (no procurement), matching the "founder signing up without a meeting" acquisition model. Scale is a conversation.

## Addressable revenue

Back-of-envelope, using conservative assumptions:

- 200 developer signups in first 90 days — plausible given organic GitHub + HN traffic on technical releases of this depth
- 5% convert to Studio tier (industry benchmark for dev-tool free-to-paid) = 10 paying customers
- 10 × $49 = **$490/mo MRR at 90 days**
- Two Scale customers by month 6 at ~$500/mo each = $1,490/mo

That's not venture-scale. It's bootstrap-viable with near-zero marginal cost (CPU only) and it feeds the parent business's ability to operate without outside capital. The better frame is: this API's existence is what makes "Omkar ships commercial products" credible to recruiters and future acquirers — it's evidence, not just revenue.

## What this document is not

- Not a TAM/SAM/SOM analysis with made-up numbers — the addressable revenue estimate above is intentionally conservative
- Not a pitch deck — no hockey sticks, no "$50B opportunity"
- Not a complete competitive teardown — the five-row table covers what matters to a buying developer; the rest is noise

## What this document is

The starting point for the rest of the case study. Every design decision downstream — build-from-scratch, MIT SDKs, stateless auth, tiered pricing — flows from the two facts established here: *there is a gap, and the incumbents can't close it without rebuilding.*

---

**Next:** [02 · Architecture](./02-architecture.md) — how the system is organized to serve the five segments above.
