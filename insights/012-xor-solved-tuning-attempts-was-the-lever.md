# 012 — Evolution solves XOR through DXNN2's path; tuning attempts was the lever

**Verdict:** confirmed · **Date:** 2026-07-20
**Closes:** roadmap 2c, and Phase 3

## Claim

Evolution now solves XOR through faber-tweann's process-per-neuron path,
reliably and reproducibly. The plateau of insight 011 (RMSE ~0.36) was a
tuning-depth limit, not a topology limit: the memetic hill-climber had too few
steps to fully tune the good topologies selection was already finding.

## Evidence

Raising the exoself's memetic tuning attempts from 15 to 60 solves XOR. Four
independent runs, population 30, feedforward:

| run | outcome | generation solved |
|---|---|---|
| 1 | solved | 13 |
| 2 | solved | 20 |
| 3 | solved | 22 |
| 4 | solved | 12 |

4/4 solved, at generations 12-22. "Solved" is the scape's own goal_reached
signal: all four XOR cases within tolerance. The run stops because the task was
solved, not at the generation cap.

`test/integration/xor_evolves_tests.erl` is the permanent proof, green in the
974-test suite.

## Why this was the lever, and what it says

Insight 011 showed selection reaching RMSE 0.36 with a partly-working tuner.
The question was whether the wall was topology (the net cannot express XOR) or
tuning (the net can, but its weights are not optimised enough). A direct probe
settled it: random 6-mutation topologies tuned to only ~1.06 at 15 attempts,
while the population reached 2.8 through selection. So selection was finding
expressive topologies; they just were not being tuned to completion. At 60
attempts they are, and XOR falls.

This is DXNN's thesis in miniature: topological evolution proposes structures,
and a memetic weight optimiser exploits each one. Starve the optimiser and even
good structures look mediocre. The two mechanisms are complementary, and both
must be adequately resourced.

## Honest boundaries

- **60 is a fixed default, not computed.** DXNN2 derives tuning depth per agent
  via `tuning_duration` (roadmap item 2, still missing). 60 works for XOR; it
  is not adaptive and is more than harder or easier problems need.
- **Feedforward only.** Recurrent networks still lack first-cycle input
  seeding; the feedforward mutation filter sidesteps this rather than solving
  it.
- **XOR is a smoke test.** Solving it proves the engine works end to end. It
  says nothing about competitiveness on the pole-balancing benchmarks of
  Table 14.1, which is the next real measurement.

## What this unblocks

The control comparison from insight 004 is now possible: the domain_sdk
weight-only path solved XOR at a median 550 evaluations (fixed topology). The
DXNN path can now be measured the same way (seeded, evaluations-to-solve) and
compared. That is the first genuinely interesting number the programme can
produce, and it is finally reachable.

## The arc, closed

Six sessions of walls, each fallen only by running the whole system one step
further: record destructuring, actuator misrouting, cortex report-per-cycle,
exoself never terminating (009); the multi-generation lifecycle (010); five
coupled lifecycle-and-tuner bugs (011); and finally tuning depth (012). The
codebase went from "no evolutionary run has ever completed" to "evolution
solves XOR" without ever changing the public API of genotype,
population_monitor or morphology. K1 held throughout.
