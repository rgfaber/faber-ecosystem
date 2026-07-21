# Programme 4 — Objectives (Selection pressure)

**Status: OPEN.** Chartered, with a good first experiment that is also the third
leg of the DPNV fork. Corpus: to be assigned.

## The axis

**Selection pressure**: what fitness rewards, holding the network and the operator
family fixed. Every programme so far optimised a single fixed objective. P4 varies
the objective itself:

- **Novelty search** — reward behavioural novelty, ignore the objective entirely
  (Lehman & Stanley 2011). The canonical answer to deception.
- **Quality-Diversity** — MAP-Elites, novelty-with-local-competition: fill an
  archive of diverse, high-performing solutions rather than seek one optimum.
- **Multi-objective** — Pareto fronts over competing goals.
- **Objective shaping** — reward design, staged objectives.

## Why this programme exists

Programmes 1 and 2 both stalled on DPNV, and both assumed the failure was in the
network or the optimizer. There is a third possibility they cannot see from inside
a single-objective search: the DPNV fitness landscape may be **deceptive**, its
gradient leading away from the solution. The literature's answer to deception is
not a better optimizer but a different selection pressure: reward diversity or
novelty, and let stepping-stones accumulate. P4 tests that hypothesis, and more
broadly turns "find one solution" into "characterise the space of solutions,"
which is exactly the map a Neurolab-as-a-Service sells.

## Central question

Does a novelty / QD run solve DPNV where objective-driven GA (024) and ES (025)
could not? A yes localises the wall to **deception** (the objective), not the
representation (P3) or the optimizer (P2). This is the third leg of the DPNV
three-way fork.

## Where to start (the good first experiment)

- **P4-a: novelty search on DPNV.** Behaviour characterised by the pole-angle
  time series (or a coarse behavioural descriptor); select on novelty, log whether
  a balancer appears as a by-product. The cleanest deception test.
- **P4-b: MAP-Elites** over a behavioural grid on the pole and T-maze suites;
  produce an archive, not a champion. This is the QD infrastructure that P9
  (QD-for-robotics, Cully) and P8 (LLM-generated behaviour descriptors) both reuse.

## The BEAM-native angle

A QD archive is a large, concurrently updated population; the actor model and a
mesh-distributed archive (P5) fit the illumination pattern of MAP-Elites well.
Novelty archives are append-mostly stores, a natural read-model shape.

## Prior art (report as replication, not discovery)

- Lehman & Stanley 2011, "Abandoning Objectives: Evolution through the Search for
  Novelty Alone."
- Mouret & Clune 2015, "Illuminating search spaces" (MAP-Elites); Pugh, Soros &
  Stanley 2016, QD survey.
- Deb et al. 2002, NSGA-II (multi-objective).
- Cully et al. 2015 (QD on real robots, bridges P9); deception folklore (Goldberg;
  Kauffman NK), already cited for insight 015 in
  [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md).

## Dependencies

Needs a behavioural descriptor for each task (the one new concept versus
objective-only search) and an archive store. Reuses the existing scapes and
harness. Ready to start; no prerequisite beyond descriptor design.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map and the DPNV fork
- [`CHARTER_P2_SEARCH_STRATEGIES.md`](CHARTER_P2_SEARCH_STRATEGIES.md) — the optimizer leg
- [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md) — the representation leg
- [`CHARTER_P9_DARS.md`](CHARTER_P9_DARS.md) / [`CHARTER_P8_LLM_AUGMENTED.md`](CHARTER_P8_LLM_AUGMENTED.md) — QD consumers
