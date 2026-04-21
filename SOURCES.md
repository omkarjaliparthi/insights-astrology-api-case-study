# Source attribution — engine rubrics

The interpretive rubrics shipped by this engine (the `lib/readings/`
modules and the Critic rubric corpus) describe traditional
astrological doctrine in our own words, citing original public-domain
sources as the textual origin. This file is the audit trail of which
sources we draw on and which we deliberately do not.

US copyright rule applied: works are treated as in the public domain
if published before 1929 OR if the author has been deceased for at
least 95 years. Translations are evaluated independently of the
original — a 2nd-century Greek source may be PD while a 2010 English
translation of it is not.

## Public domain — drawn on

- Claudius Ptolemy, *Tetrabiblos* (2nd c. CE) — Greek original; English via J. M. Ashmand (1822)
- Vettius Valens, *Anthology* (2nd c. CE) — Greek original; English via Mark Riley (released to public domain by translator)
- Manilius, *Astronomica* (1st c. CE) — Latin original
- Firmicus Maternus, *Mathesis* (4th c. CE) — Latin original
- Guido Bonatti, *Liber Astronomiae* (13th c.) — Latin original
- William Lilly, *Christian Astrology* (1647)
- Jean-Baptiste Morin, *Astrologia Gallica* (17th c.)
- Placidus de Tito, *Tabulae Primi Mobilis* (17th c.)
- Alan Leo (d. 1917), collected works
- Sepharial / Walter Gorn Old (d. 1929), collected works
- Charles E. O. Carter, pre-1929 publications

## Not drawn on (in copyright; explicitly excluded)

- Robert Hand, all works
- Liz Greene, all works
- Stephen Forrest, Steven Arroyo, Demetra George, Bernadette Brady, Sue Tompkins
- Robert Schmidt — including all Project Hindsight translations (PD ~2088)
- Dane Rudhyar, all works (PD ~2055)
- Marc Edmund Jones — including the Sabian symbols (PD ~2050; estate active)
- Reinhold Ebertin — including the cosmobiology midpoint catalog (PD ~2058)
- All modern English translations of ancient sources published after ~1929

When a doctrine widely associated with these authors is referenced
(e.g., midpoint analysis, time-lord systems, symbolic-degree work),
the rubric describes the doctrine using public-domain antecedents and
in our own words, never reproducing the modern formulation's language.

## Why this exists

Publishing the source policy makes it auditable. A reviewer, a
customer's legal team, or a concerned author can check in one place
which texts we draw on, which we deliberately don't, and why. If you
believe a rubric in this engine reproduces in-copyright material,
email admin@insightsbyomkar.com with subject "Source policy" and
reference the relevant topic.

## Astronomy sources (separate from interpretation)

Distinct from the interpretive rubrics, the engine's astronomy is
implemented from primary technical references:

- VSOP87D — Bretagnon & Francou (1988), Mercury through Neptune
- ELP2000-82B — Chapront-Touzé & Chapront (1983), Moon
- IAU 1980 Theory of Nutation — Wahr (1981), top-15-term truncation
- Pluto — homegrown RK4 n-body integrator with Jupiter–Neptune perturbers, JPL Horizons J2000 seed
- Houses — Placidus, Porphyry, Equal, Whole-Sign, Koch standard formulations

These are scientific references, not copyrightable interpretive text;
the engine cites them in `lib/api/version.ts` and on the
`/api/v1/accuracy` route's metadata.
