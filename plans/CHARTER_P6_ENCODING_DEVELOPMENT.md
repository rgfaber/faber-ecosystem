# Programme 6 — Encoding / Development (Genotype→phenotype map)

**Status: OPEN.** Chartered. Corpus: to be assigned.

## The axis

**The genotype→phenotype map**: how a genome becomes a network, holding what the
network can express (P1) and how it is searched (P2) fixed. The same phenotype can
be reached by different encodings; the encoding does not change what is
expressible, it changes *scalability* and *regularity exploitation*.

- **Direct encoding** — one gene per parameter (the current faber default; genome
  size grows with the network).
- **Indirect / generative encoding** — a compact genome that develops into a
  larger network; regularities (symmetry, repetition) are expressed once and
  reused (HyperNEAT / CPPN, Stanley 2007).
- **Developmental encoding** — a growth process (cellular encoding, Gruau 1994)
  builds the phenotype over developmental steps.

## Why this programme exists (and why it is separate from P1)

P1 characterised what capabilities help which problems, always under a direct
encoding. But the encoding is an independent lever: an indirect encoding can
express a large regular network from a tiny genome, which changes the scalability
ceiling and lets evolution exploit geometric structure that direct encoding must
rediscover parameter by parameter. This matters precisely where the phenotype gets
large or regular, which is where P9 (many-jointed bodies, swarms) and larger
control problems live. Encoding is therefore the axis that gates scale-up.

## Central questions

1. On a problem with **geometric regularity** (a symmetric body, a tiled sensor
   array), does an indirect encoding find solutions with fewer evaluations than
   direct encoding, and does it scale to larger versions of the problem the direct
   encoding cannot reach?
2. Does an indirect encoding **generalise** across problem sizes (evolve small,
   deploy large) the way HyperNEAT's geometric substrate is meant to?

## Where to start (the good first experiment)

- **P6-a: a CPPN-style indirect encoder** for a fixed-substrate weight-only
  network (the domain_sdk arena), compared to direct encoding on a regular task
  (a symmetric pole array, or a tiled version of an existing scape). Report
  evaluations-to-solve and genome size.
- **P6-b: scale test** — evolve on the small problem, evaluate the developed
  phenotype on a larger instance; measure the transfer.

## The BEAM-native angle

Development is a process; a growth process that spawns neuron processes as it
unfolds is a natural fit for the actor substrate, and construction is already how
faber builds phenotypes (`constructor.erl`). An indirect encoder is a small change
to the construction step, not a new runtime.

## Prior art (report as replication, not discovery)

- Stanley 2007, "Compositional Pattern Producing Networks" (CPPN); Stanley,
  D'Ambrosio & Gauci 2009, HyperNEAT.
- Gruau 1994, cellular encoding (developmental).
- Clune, Stanley et al., on the regularity / evolvability benefits of generative
  encodings; the encoding-scalability argument (direct encoding's genome-size wall).

## Dependencies

Needs an indirect-encoder construction path alongside the direct one; reuses the
existing search and scapes. Prerequisite for the morphology-coevolution arm of P9
(a body is naturally an indirect/developmental genome). Ready to start.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`CHARTER_P1_CAPABILITIES.md`](CHARTER_P1_CAPABILITIES.md) — what encoding is held constant against
- [`CHARTER_P9_DARS.md`](CHARTER_P9_DARS.md) — morphology as a developmental genome
- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md)
