# Ephemeris Accuracy

This document describes the accuracy characteristics of the ephemeris
engine behind the Insights Astrology API. Every claim below is backed
by a test in CI against the primary-source references listed in
[SOURCES.md](../SOURCES.md).

## Summary

| Body     | Algorithm                     | Typical accuracy vs. JPL DE | CI tolerance |
|----------|-------------------------------|------------------------------|--------------|
| Sun      | VSOP87 truncated (Meeus §25)  | < 0.01° (36 arc-seconds)     | 0.01°        |
| Moon     | ELP2000 truncated (Meeus §47) | < 0.1° (6 arc-minutes)       | 0.10°        |
| Mercury–Neptune | VSOP87D full            | < 0.01° (36 arc-seconds)     | 0.01°–0.05°  |
| Pluto    | Standish Keplerian            | < 0.5°                       | 0.5°         |

Accuracy holds across the range **1900–2100** (validated by regression
anchors at multiple dates). Outside this range, VSOP87 polynomials begin to
drift — documented in Meeus §32 — but our tests don't currently certify it.

## Test suite

Three layers of validation run in CI on every PR:

### 1. Textbook-verified anchors (`accuracy-vsop87.test.ts`)

Reproduces published worked examples from Meeus (1998) exactly. These are
the ground-truth check:

- **Sun 1992-10-13 00:00 TT** — Meeus §25 example, expected 199.90889°.
- **Moon 1992-04-12 00:00 TT** — Meeus §47 example, expected 133.16722° longitude, −3.22917° latitude.
- **Venus 1992-12-20 00:00 TT** — Meeus §33 example, expected 313.0862°.

If any of these fail, either the algorithm is broken or the test fixture is
wrong. Both deserve immediate investigation.

### 2. Astronomical-relationship tests (`accuracy-astronomical-relationships.test.ts`)

Validate universal astronomical relationships that must hold regardless of
ephemeris choice:

- Sun advances ≈0.9856°/day (mean tropical motion).
- Sun returns to same longitude after one tropical year (365.2422 days).
- Moon averages 13.176°/day over one year (±0.05°/day).
- ≈12.37 synodic months per year (±0.1).
- Jupiter/Saturn long-baseline mean motion converges on heliocentric
  sidereal period when measured over 300 years.
- Every body returns a longitude in [0, 360) at every tested date.

These are "no-algorithm-can-be-wrong" checks.

### 3. Regression anchors (`accuracy-regression.test.ts`)

Captures THIS engine's specific output at fixed dates as frozen values with
tight tolerances (0.002° for Sun, 0.01° for planets, 0.02° for Pluto). Any
silent change in the algorithm — a refactor, coefficient update, or
dependency bump — will flip these tests red and force the committer to
either (a) fix the regression or (b) consciously update the anchor.

Anchors cover: J2000 (2000-01-01), 1900-01-01, 2025-01-01, 2050-01-01.

## What's not yet tested

- **Accuracy outside 1900–2100.** VSOP87's published validity is ±4000 years
  around J2000 at reduced precision; we should add regression anchors at
  1500 and 2300 to cover that range explicitly.
- **Moon latitude at extrema.** Our ELP truncation may drift more at ±5°
  latitude extremes; worth adding dedicated tests.
- **Pluto accuracy.** Standish Keplerian is known to be ~0.5° off from JPL
  DE at extreme dates; we tolerate this but could upgrade to a Chebyshev
  polynomial fit.

## References

- Meeus, Jean. *Astronomical Algorithms*, 2nd ed. (Willmann-Bell, 1998).
- IMCCE VSOP87 series — <https://www.imcce.fr/content/medias/publications/publications-recherche/notes-scientifiques-et-techniques/2020/vsop87.pdf>.
- Chapront-Touzé & Chapront. "ELP2000-85: a semi-analytical lunar ephemeris." *Astronomy & Astrophysics* 124 (1988).
- Standish, E. M. "Keplerian Elements for Approximate Positions of the Major Planets." JPL memo, 1992.
