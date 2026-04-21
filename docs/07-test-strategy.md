# 07 · Test Strategy

## The question

How do you prove that a home-grown astronomy engine is *actually correct*? The whole licensing + margin + moat argument in [03 · Engine from scratch](./03-decision-engine-from-scratch.md) assumes we can publish an accuracy table and defend it. If the math is wrong, the entire product is wrong.

Worse: the errors won't show up as exceptions. They'll show up as subtle drifts — a chart that's off by a degree, an aspect that shouldn't trigger, a dasha period that's a month late. By the time a customer files a ticket, we've already published the chart in their app and stamped it as authoritative.

This is why every other commercial option is either "wrap the dominant astronomy library" (trust theirs) or "proxy a paid API" (pay for theirs).

## What I did instead

**Meeus golden-file validation.** The astronomy literature's gift to anyone building an ephemeris engine: Jean Meeus's *Astronomical Algorithms* publishes worked examples — specific dates, specific bodies, specific expected values — for every major calculation. They are the industry reference. Validating against them is the same as validating against the dominant astronomy library, because that library validated against them first.

- **16 test files** under `lib/ephemeris/__tests__/`
- **230+ assertions**
- Every major subsystem: time conversions, Sun, Moon, planets, houses, aspects, nodes, Vedic ayanamsas, dashas, sunrise/sunset, eclipse finding
- Each assertion quotes the Meeus chapter and page it validates against

## Example test

```ts
// lib/ephemeris/__tests__/bodies.test.ts
it('Sun position on 1992-10-13 matches Meeus ch. 25 worked example', () => {
  const jd = julianDayFromUTC(1992, 10, 13, 0, 0, 0)
  const sun = sunPosition(jd)

  // Meeus ch. 25 Appendix: apparent longitude 199.90895°, distance 0.99766 AU
  expect(sun.longitude).toBeCloseTo(199.90895, 2)  // within 0.01°
  expect(sun.distance).toBeCloseTo(0.99766, 4)     // within 0.0005 AU
})
```

The `toBeCloseTo` precision is deliberate — tight enough to catch algorithmic errors, loose enough that floating-point ulps and coefficient-truncation differences don't flake the test.

## Where the golden data comes from

| Subsystem | Reference |
|---|---|
| Time (JD, ΔT, obliquity, nutation, sidereal) | Meeus ch. 7, 9, 10, 12, 22 |
| Sun | Meeus ch. 25, Appendix worked example |
| Moon (ELP2000 truncated) | Meeus ch. 47 |
| Planets (VSOP87D) | Meeus ch. 32 + IMCCE table comparisons |
| Pluto (Keplerian) | Meeus ch. 37 |
| Houses (Placidus, Koch) | Meeus ch. 13 |
| Sunrise/sunset | Meeus ch. 15 |
| Eclipses | Meeus ch. 54 |
| Aspects | geometrically trivial once positions pass |
| Vedic ayanamsas, dashas, nakshatras | Cross-checked against published Lahiri / KP panchangas for known dates |

Vedic coverage is the weakest rung — the ayanamsa and dasha literature is less centralized than Meeus, and validation means cross-referencing multiple published panchangas for agreement. The test suite uses a shorter set of trusted reference dates. This is a known gap, flagged for expansion as traffic grows.

## CI gate

`.github/workflows/ci.yml` runs on every PR and every push to main:

```yaml
- run: npm ci
- run: npm run typecheck
- run: npm run lint
- run: npm test
- run: npm run build
```

No merge without all five passing. The test step is non-negotiable; the typecheck and lint steps catch the easy stuff so the test failures are always real ones.

## Test pyramid — intentionally inverted

Traditional pyramid: lots of unit tests, some integration, few e2e.

Mine: lots of *validation* tests (golden-file), very few unit tests, zero e2e.

The reasoning: the API handlers are ~50 lines each and delegate all real logic to the engine. Testing them unit-style would test the delegation, which is trivial. Testing them integration-style would hit a running server, which adds CI complexity for little signal. The failure mode that matters is "the engine gave the wrong number" — and that's exactly what golden-file tests catch.

If a handler breaks, the customer-facing error is obvious (500 or wrong response shape). If the engine silently drifts by 0.2°, no one notices for months. The tests weight proportionally to the *non-obviousness* of the failure mode.

## What golden-file tests don't catch

- **Integration bugs between subsystems** — e.g., the Vedic chart uses tropical positions from the core engine and applies an ayanamsa offset. A bug in either one separately is caught; a bug in the composition is not. Mitigation: a handful of composition tests that trace specific known charts end-to-end.
- **Performance regressions** — no benchmark suite yet. Flagged.
- **Concurrency issues** — all compute is synchronous and stateless; N/A.
- **HTTP-layer bugs** — auth mis-wiring, rate-limit off-by-one, header issues. Mitigation: a smaller set of HTTP-level tests under `lib/api/__tests__/`.

## The "what if Meeus is wrong?" question

Meeus is the output of decades of peer review; individual errata are cataloged in the book's own appendices. Where we implement an algorithm with a known Meeus erratum, the code carries the correction inline and cites it. Nothing more rigorous is feasible without a JPL DE-based reference implementation, which was scoped out for v1.

If a customer reports a discrepancy, the resolution protocol is:
1. Check against the Meeus-cited value for the exact date
2. Check against the dominant astronomy library (local, dev-only, not shipped)
3. If both agree and we disagree, we have a bug — fix with a new golden-file test
4. If SE disagrees with Meeus, check the Meeus errata and the SE release notes; pick the newer reference

So far (v1 day 0), all 230+ tests pass. No customer-reported discrepancies — but also no customers yet. The first production-traffic week is the real test.

## Retrospective questions for 30-day review

- How many customer-reported discrepancies, and what was the resolution?
- Did the CI gate catch a drift before release?
- Which subsystem had the weakest test coverage relative to its traffic?
- Any integration bugs between Vedic and core engine?

## Decision

**Meeus golden-file tests as the CI gate.** The cheapest path to defensible accuracy claims, grounded in the same reference every other commercial ephemeris uses. Gaps in Vedic validation acknowledged, scoped, and roadmapped.

---

**Next:** [08 · Outcomes & lessons](./08-outcomes-and-lessons.md)
