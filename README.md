<p align="center">
  <a href="https://kriya.insightsbyomkar.com"><img src="https://img.shields.io/badge/Live_API-Kriya_(Insights_Astrology_API)-6E56CF?style=for-the-badge" /></a>
  <img src="https://img.shields.io/badge/Built_by-Omkar_Jaliparthi-6E56CF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Engine-first_principles-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Status-Live_·_v9.x_·_109%2B_endpoints-brightgreen?style=for-the-badge" />
</p>

<p align="center">
  <i>Public case study for <b>Kriya</b> (formerly <i>Tuffys</i>) — the Insights Astrology API by Insights by Omkar. A commercial astronomy API computed from first principles.</i>
</p>

---

## TL;DR

Built and shipped **[Kriya](https://kriya.insightsbyomkar.com)** (formerly Tuffys) — a commercial astronomy API now at **109+ v1 endpoints** across Western, Vedic, Hellenistic, Jaimini, KP, and electional traditions — on a home-grown VSOP87D + ELP2000 + DOPRI8 engine.

The v1 launch on **2026-04-15** shipped 43 endpoints in a single focused execution day. That day was possible because the research, scoping, and architecture decisions happened weeks before the sprint started — the day was translation work, not invention. Since launch, the engine has grown across **nine semver versions** (v1 → v9.8.x) and ~250+ commits of disciplined iteration, more than doubling endpoint coverage and refitting the lunar theory to 0.87″ vs JPL Horizons.

This is not a wrapper around the dominant commercial astronomy library. It's not a proxy over a paid astrology API. It's primary-source math (Meeus, IMCCE VSOP87, ELP/MPP02, Standish) with a stateless JWT auth layer, dual-backend rate limiting, three MIT-licensed SDKs, interactive API docs, and tiered pricing — sitting alongside the parent consumer SaaS (Insights by Omkar) under the same LLC.

The lesson isn't "you can ship a 43-endpoint API in a day." It's that **pre-execution decomposition is the skill**, and the iteration cadence after the sprint is what separates a launch demo from a commercial product.

---

## At a glance &nbsp; <sub><i>last verified 2026-04-27</i></sub>

| | |
|---|---|
| **Product** | Commercial astronomy/astrology API · REST · OpenAPI 3.1 |
| **Coverage** | **109+ endpoints** across 10+ domains · grew from 43 v1 endpoints over nine semver versions |
| **Engine** | Home-grown · VSOP87D (IMCCE) planets · ELP/MPP02 Moon (refit to 0.87″ vs Horizons in v9.1) · DOPRI8 8(7) integrator with 9-perturber force model · Meeus foundational |
| **Accuracy** | ~1″ Mercury–Neptune · 0.87″ Moon · 36″ Sun · documented analytical-theory ceiling at `/v1/accuracy` |
| **Auth** | Stateless HS256 JWT · per-key quotas encoded in payload · denylist revocation |
| **Rate limiting** | Triple-fallback: Upstash Redis → Supabase RPC → in-process buckets |
| **SDKs** | TypeScript: `npm install insightsbyomkar` · Python: `pip install insightsbyomkar` · Go: `go get github.com/omkarjaliparthi/kriya-go` · all MIT · zero runtime deps |
| **Frontend** | Next.js App Router · live demo · `/pricing` · `/docs/api` with Scalar explorer |
| **Tests** | 230+ golden-file assertions validated against Meeus published tables; 1,900+ total test assertions |
| **CI/CD** | GitHub Actions · typecheck + lint + test + build on every PR · OIDC PyPI publishing |
| **Licensing** | Proprietary server · MIT SDKs (intentional split — see decision doc) |
| **Timeline** | v1.0 shipped 2026-04-15 · v9.x running on sustained iteration cadence as of 2026-04-27 |
| **Parent business** | Omkar's Holistic Services LLC (DBA *Insights by Omkar*) · formed May 2023 |

---

## 📑 Read the case study

1. **[Problem & market](./docs/01-problem-and-market.md)** — why a new astrology API, who buys it, what the incumbents get wrong
2. **[Architecture](./docs/02-architecture.md)** — system, engine tree, API layer, auth/rate/SDK boundaries
3. **[Decision · engine from scratch](./docs/03-decision-engine-from-scratch.md)** — build vs wrap vs proxy · licensing + margin · moat
4. **[Decision · auth & rate limiting](./docs/04-decision-auth-and-rate-limiting.md)** — stateless JWT + dual-backend rate limiter · why no database
5. **[Decision · licensing split](./docs/05-decision-licensing-split.md)** — proprietary server + MIT SDKs · the integration-friction frame
6. **[SDK strategy](./docs/06-sdk-strategy.md)** — TypeScript + Python + Go · zero-deps principle · publish automation
7. **[Test strategy](./docs/07-test-strategy.md)** — Meeus golden files · CI gate · the astronomy "truth" question
8. **[Outcomes & lessons](./docs/08-outcomes-and-lessons.md)** — what the one-day sprint proved, what breaks, what's next

### Reference

- **[Accuracy disclosure](./docs/accuracy.md)** — claims, tolerances, test layers, and what's not yet certified
- **[SOURCES](./SOURCES.md)** — astronomy + interpretive provenance, public-domain line, explicitly excluded modern authors

---

## Related artifacts

- **[PRD · public launch](https://github.com/omkarjaliparthi/tpm-portfolio/blob/main/prds/astrology-api-launch.md)** — in the TPM portfolio · success metrics, scoping, risk matrix
- **[RFC · home-grown ephemeris engine (build vs buy)](https://github.com/omkarjaliparthi/tpm-portfolio/blob/main/rfcs/home-grown-ephemeris-engine.md)** — four-option weighted scoring · four-phase implementation plan
- **[Parent SaaS case study](https://github.com/omkarjaliparthi/insights-by-omkar-case-study)** — the consumer product from which this API was conceptually extracted
- **[Live API](https://kriya.insightsbyomkar.com)** · [Interactive docs](https://kriya.insightsbyomkar.com/docs/api) · [Pricing](https://kriya.insightsbyomkar.com/pricing)
- **[Go SDK](https://github.com/omkarjaliparthi/kriya-go)** · [TypeScript SDK (npm)](https://www.npmjs.com/package/insightsbyomkar) · [Python SDK (PyPI)](https://pypi.org/project/insightsbyomkar/)

---

## Skills this project evidences

<table>
<tr>
<th>Product</th>
<th>Program</th>
<th>Engineering</th>
<th>Business</th>
</tr>
<tr>
<td valign="top">

- API product design
- Developer experience
- Tiered pricing & packaging
- Positioning vs incumbents
- Accuracy-as-a-feature
- Docs that convert

</td>
<td valign="top">

- Scope discipline (one-day sprint)
- Research ahead of execution
- Risk matrix · go/no-go
- CI gates as release discipline
- Versioning & compatibility

</td>
<td valign="top">

- Astronomy math from papers
- Stateless auth architecture
- Dual-backend rate limiting
- Tree-shakeable TS modules
- Multi-language SDKs
- OpenAPI 3.1 authoring

</td>
<td valign="top">

- Build vs buy (existential)
- Licensing as strategy
- Moat design
- Gross margin modeling
- Integration-friction framing
- LLC + DBA structuring

</td>
</tr>
</table>

---

<p align="center">
<b>Hiring Senior PM · TPM · Founding PM?</b><br/>
<a href="mailto:Jaliparthiomkar03@gmail.com">Jaliparthiomkar03@gmail.com</a> · <a href="https://www.linkedin.com/in/jaliparthiomkar">LinkedIn</a> · <a href="https://github.com/omkarjaliparthi">GitHub</a>
</p>
