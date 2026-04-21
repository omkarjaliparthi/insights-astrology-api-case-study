<p align="center">
  <a href="https://tuffys-ai-astrology.vercel.app"><img src="https://img.shields.io/badge/Live_API-insights_astrology_api-6E56CF?style=for-the-badge" /></a>
  <img src="https://img.shields.io/badge/Built_by-Omkar_Jaliparthi-6E56CF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Engine-first_principles-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Status-Public_v1-brightgreen?style=for-the-badge" />
</p>

<p align="center">
  <i>Public case study for the Insights Astrology API — a commercial astronomy API computed from first principles.</i>
</p>

---

## TL;DR

Built and shipped **[Insights Astrology API](https://tuffys-ai-astrology.vercel.app)** — a commercial astronomy API with **43 v1 endpoints** across Western, Vedic, Hellenistic, and electional traditions — on a home-grown VSOP87D + ELP2000 engine, in a **single focused day of execution** (2026-04-15) following weeks of research and scoping.

This is not a wrapper around the dominant astronomy library. It's not a proxy over a paid astrology API. It's ~40,000 lines of primary-source math (Meeus, IMCCE, Standish) with a stateless JWT auth layer, dual-backend rate limiting, three MIT-licensed SDKs, interactive API docs, and tiered pricing — all sitting alongside the parent consumer SaaS (Insights by Omkar) under the same LLC.

Most engineers would call this impossible in a day. It was possible because the research, the scoping, and the boundary decisions happened *before* the sprint started.

---

## At a glance

| | |
|---|---|
| **Product** | Commercial astronomy/astrology API · REST · OpenAPI 3.1 |
| **Coverage** | **43 endpoints** across 7 domains · 12 major subsystems |
| **Engine** | Home-grown · ~40K LOC · VSOP87D (IMCCE) planets · ELP2000 Moon · Standish Pluto · Meeus foundational |
| **Accuracy** | ~1″ Mercury–Neptune · 0.02° Moon · 36″ Sun · disclosed in README |
| **Auth** | Stateless HS256 JWT · per-key quotas encoded in payload · denylist revocation |
| **Rate limiting** | Dual-window (per-min + per-day) · Upstash Redis with in-process fallback |
| **SDKs** | TypeScript (npm) · Python (PyPI) · Go (pkg.go.dev) · all MIT · zero runtime deps |
| **Frontend** | Next.js App Router · live demo · `/pricing` · `/docs/api` with Scalar explorer |
| **Tests** | 230+ golden-file assertions validated against Meeus published tables |
| **CI/CD** | GitHub Actions · typecheck + lint + test + build on every PR · OIDC PyPI publishing |
| **Licensing** | Proprietary server · MIT SDKs (intentional split — see decision doc) |
| **Timeline** | v1.0 shipped 2026-04-15 · one focused execution day after weeks of research |
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
- **[Live API](https://tuffys-ai-astrology.vercel.app)** · [Interactive docs](https://tuffys-ai-astrology.vercel.app/docs/api) · [Pricing](https://tuffys-ai-astrology.vercel.app/pricing)
- **[Go SDK](https://github.com/omkarjaliparthi/tuffys-astrology-go)** · [TypeScript SDK (npm)](https://www.npmjs.com/package/tuffys-astrology) · [Python SDK (PyPI)](https://pypi.org/project/tuffys-astrology/)

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
