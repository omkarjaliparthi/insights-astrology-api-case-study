# 08 · Outcomes & Lessons

## What shipped on 2026-04-15

- 43 public v1 endpoints across 7 domains
- ~40K LOC of primary-source astronomy engine
- Stateless HS256 JWT auth + dual-backend rate limiter
- TypeScript, Python, and Go SDKs — zero runtime deps each
- GitHub Actions for CI + automated npm / PyPI / Go publishing
- `/pricing`, `/docs`, `/docs/api` (Scalar interactive explorer), `/dashboard` (live demo)
- Freemium demo endpoint guard (origin + IP + kill switch)
- Proprietary-source server LICENSE + MIT SDK LICENSES + NOTICE attribution file
- OpenAPI 3.1 spec at `/openapi.json`
- 230+ Meeus golden-file tests, all passing
- README positioned as a technical product brief
- Brand rebuild under the "Insights by Omkar" umbrella

## Where it is now &nbsp; <sub><i>(as of 2026-04-27 — 6 weeks after launch)</i></sub>

- **109+ v1 endpoints** across 10+ domains — Western, Vedic, Jaimini, KP, Hellenistic, electional, points & geometry, bodies, LLM-grounded readings, developer/admin
- **Engine refits**: ELP/MPP02 lunar theory port (v9.1) brought the Moon to 0.87″ vs JPL Horizons; DOPRI8 8(7) integrator with 9-perturber force model now drives Pluto + asteroid trajectories (v9.0)
- **9 semver versions** of disciplined iteration (v1 → v9.8.x) — 250+ commits, 1,900+ test assertions, accuracy disclosure published at `/v1/accuracy`
- **Triple-fallback rate limiting**: Upstash Redis → Supabase RPC → in-process buckets
- **Brand transition**: the product publicly trades as **Kriya** as of v9.7 — `kriya.insightsbyomkar.com`. The Go SDK was renamed from `tuffys-astrology-go` to `kriya-go` (module: `github.com/omkarjaliparthi/kriya-go`, package: `kriya`). The internal API repo retains its original name (`tuffys-ai-astrology`) for now — a non-public detail.
- **TS + Python SDKs regenerated from the OpenAPI spec on every release** — endpoint coverage now tracks the server automatically, so the SDK-vs-server drift problem doesn't exist.

The point of this section: the v1 sprint was the launch, not the product. The product is the cadence after.

## What this proves

Not "one person can ship in a day" — that would be misleading. The actual lesson:

**Research and scoping done ahead of the sprint is what makes a sprint possible.** Every decision that got written down in a commit message on 2026-04-15 — build vs buy, proprietary-source + MIT, stateless JWT, three SDKs, Meeus golden-files — was a decision made and rejected alternatives weeks before. The execution day was translation work.

If someone hands me a fresh repo and a blank day, I don't ship this. I ship some version of this only because every subsystem was a known quantity before I touched it: I knew which Meeus chapters I'd need, which VSOP87 files to pull from IMCCE, which license text to adapt, which auth pattern to port from the sister SaaS.

That's the TPM lesson, not an engineering one. The skill is *pre-execution decomposition*, not typing speed.

## What the 40K-LOC number hides

Of the ~40K lines in `lib/ephemeris/`, ~30K are coefficient data — fixed-width tables from IMCCE's VSOP87 FTP mirror, converted by `scripts/generate-vsop87.mjs` into tree-shakeable TypeScript modules. The active logic is closer to 10K lines. The evaluator itself is a ~200-line polynomial-in-τ summation; the rest is bookkeeping, coordinate transforms, and the tradition-specific layers (Vedic, Hellenistic, electional).

The data-generation script is the unglamorous load-bearer. Parsing IMCCE's fixed-width format is boring and tedious and it's the only reason the whole project is tree-shakeable — without it, every planet would pull in every other planet's coefficients and the serverless bundle would balloon past the Vercel size cap.

## What worked

**The architectural discipline of "engine has no HTTP, API has no astronomy."** Tested under pressure: adding endpoints late in the day was a mechanical exercise because the engine never had to be touched. If the two had been mixed, every handler addition would've risked introducing a math bug in an unrelated calculation.

**Reusing patterns from the sister SaaS.** The rate-limit module's "Upstash with in-process fallback" was a port, not an invention. The observability fire-and-forget pattern, the structured-error shape, the CI pipeline — all adapted from insights-by-omkar. Cross-project pattern reuse is the single highest leverage tool a solo builder has.

**The accuracy table as a marketing asset.** Publishing `Sun: 36″ | Mercury–Neptune: 1″ | Moon: 0.02° | Pluto: 1°` with sources is the opposite of what most API marketing does (vague claims, no numbers). It signals technical seriousness and it gives the sales conversation a concrete anchor.

**OIDC trusted publishing for PyPI.** No token to rotate, no secret to leak. This is a small thing that adds up over time — every secret you don't store is one fewer thing to leak and one fewer expiry date to forget.

## What broke

**The live demo dashboard is thinner than it should be.** The `/dashboard` page exists; it accepts a birth input and returns a chart. But the chart-wheel visual is a v1-stub — no aspect lines, no degree ticks, no retrograde markers in the rendered art. The commit message claimed "chart wheel v2 with aspect lines"; the implementation shipped the schema changes but not the visual polish. That's an honest scope overrun to correct in the next sprint.

**Onboarding documentation is minimal.** A CONTRIBUTING.md or ARCHITECTURE.md that walks a new collaborator through the codebase is still missing. Flagged for a future documentation pass.

**No performance benchmarks.** I assume sub-150ms latency because the math is O(coefficient count) and the coefficient counts are small, but "assume" is not "measured." Should add a micro-benchmark suite and publish the numbers.

**Vedic golden-file coverage is lighter than Western.** The literature is less centralized, so the test coverage is correspondingly weaker. I know where the gaps are; filling them means cross-referencing multiple panchangas by hand. Scoped for the first 30-day retrospective.

**No customer portal for self-serve key minting.** Working around this with an email workflow is fine at N=0 customers and breaks at N=20. The cost of building a portal (Clerk + admin panel) is ~2 days; the cost of doing 20 email-based key mints is ~1 hour. Portal ships after 20 paying customers.

## What I'd do differently

- **Draft the case study alongside the sprint**, not after. Writing this document after shipping meant I had to reconstruct a few decisions that were intuitive in the moment. If I'd sketched these pages as I went, the reasoning would have stayed sharper.
- **Pre-generate mock screenshots of the Scalar API explorer** so the README's live links don't depend on Vercel deploy timing.
- **Ship the `/dashboard` chart-wheel visual before the HTTP layer**, not after. Visual polish was the only thing I cut, and it's the thing most visible to a first-time visitor.
- **Write the NOTICE file first.** It's the least-fun file to write, gets skipped, and then requires a backfill archaeology session over git log.

## What's next — priorities post-launch

1. **Close the `/dashboard` v2 gap** — complete the chart wheel visual
2. **Write `ARCHITECTURE.md`** — replace the stub
3. **Performance benchmarks + public latency page**
4. **First customer email blast** — solo builders in Next.js + AI-wellness Discord communities
5. **Expand Vedic test coverage** — cross-referenced panchanga dates
6. **Decide on developer portal after first 20 paid customers**

## Metrics to check at 30 days

- Developer signups (JWT keys minted) — target 200
- Paid Studio conversions — target 10
- Daily API calls at peak — target 50K
- Median response latency — target <150ms
- SDK installs combined (TS + Python) — target 1,000
- Uptime — target 99.9%
- Refund / dispute rate — target <1%

If any of these miss by 2×, the case study needs a v2 with what changed. If any of these beats by 2×, that's a different kind of learning — probably about which channel converted.

## Portfolio value

For the job search specifically: this project pairs with the live consumer SaaS (Insights by Omkar) to cover both halves of the product-engineering spectrum.

- **Insights by Omkar** — evidence of shipping a full consumer product with real revenue, real users, real support load, real chargeback defense
- **Insights Astrology API** — evidence of deep technical work (astronomy math from primary sources), dev-tool product thinking (three SDKs, zero deps, MIT licensing), and commercial API design (tiered pricing, stateless auth, SLA-grade rate limiting)

Very few Senior PM / TPM / Founding PM candidates have both artifacts. That's the whole point.

---

**Back to:** [case study index](../README.md)

**Also:** [PRD · public launch](https://github.com/omkarjaliparthi/tpm-portfolio/blob/main/prds/astrology-api-launch.md) · [RFC · engine build vs buy](https://github.com/omkarjaliparthi/tpm-portfolio/blob/main/rfcs/home-grown-ephemeris-engine.md) · [Parent SaaS case study](https://github.com/omkarjaliparthi/insights-by-omkar-case-study) · [Live API · Kriya](https://kriya.insightsbyomkar.com)
