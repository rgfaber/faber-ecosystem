# 031 — Evolved plasticity solves the cue-memory T-maze 10/10, matching CfC weight-memory (memoryless 0/10). Memory-by-learning is as effective and as fast as memory-by-storage here; Programme 3 opens on a positive.

**Verdict:** confirmed + positive (first Programme 3 result) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-031. **Uses:** new
`network_evaluator:evaluate_with_plasticity/3` (ABCD-Hebbian), `sep_cma_es`,
`tmaze_sim`. Extends the memory arc (019/026-030).

## The question

The memory arc asked whether STORING memory in evolved recurrent weights (CfC)
pays. P3 asks a different mechanism: evolve a local LEARNING RULE and let the
network write memory into its own weights within the episode (memory-by-learning).
Does it work on the T-maze, where memory demonstrably pays (019)?

## Method

Three arms, same net (2 -> [12] -> 1, tanh), same T-maze (delay 2, 100 balanced
trials, solve = 90), sep-CMA-ES (lambda 100, 500 gens cap), n=10:
- **memoryless** — weights only (dim 49). No memory.
- **cfc** — weights + co-evolved tau (dim 61). Memory-by-storage, the 019 baseline.
- **plastic** — weights + a global ABCD-Hebbian rule (dim 54). The eval loop resets
  the weights to the evolved initial at each cue step; the rule then latches that
  trial's cue. Memory-by-learning, the new arm.

Raw: `031-raw-plasticity-tmaze.txt`. Solved-agent trace: `031-solved-latch-trace.json`.

## Results

| Arm | mechanism | solved | evals-to-solve | fitness |
|---|---|---|---|---|
| cfc | memory-by-storage | 10/10 | ~100-400 | 96-100 |
| plastic | memory-by-learning | 10/10 | ~100-200 | 100 |
| memoryless | none | 0/10 | — | **exactly 50.0** (all 10) |

## Finding 1 — evolved plasticity solves it, matching CfC

The plastic arm solves 10/10, every run reaching 100/100 correct in one or two
generations — the same speed and completeness as the CfC weight-memory baseline.
An evolved local Hebbian rule is a fully viable memory mechanism on this task.

## Finding 2 — memory is necessary, and it is the RULE doing the work

The memoryless control lands on **exactly 50.0** on all 10 runs (balanced cues, so
a fixed turn scores half with zero variance). So the task genuinely needs memory.
A zero plastic rule (Eta=0 or A=B=C=D=0) reduces `evaluate_with_plasticity` exactly
to the memoryless forward pass. So the plastic arm's 10/10, versus memoryless 0/10
on the same 49 weight parameters, is the evolved Hebbian rule providing the memory,
not the weights.

## Finding 3 — evolution found its OWN dynamic, not the textbook latch

The solved agent's weight trace (visualised) does not store-then-hold like a
hand-set Hebbian rule; the weight displacement keeps GROWING through the corridor
(cue +1: 0.29 -> 0.74; cue -1: 0.87 -> 1.66), yet the junction readout is correct
(+cue -> output +0.30 = left; -cue -> -0.19 = right). The two cues drive very
different weight trajectories, and the evolved rule reads either out correctly. The
mechanism is memory-by-learning, but the specific encoding is evolution's, not ours.

## Finding 4 — on the T-maze, the two memory mechanisms are equivalent

Both solve 10/10 in ~1-2 generations. At delay 2 the task is easy enough that
neither mechanism has an edge; they are interchangeable here. A regime that
separates them (a longer delay, noise, or a task where weight-memory struggles) is
the natural follow-up.

## Prior art

Replication, not discovery. Evolving the learning rule is Chalmers 1990 ("The
evolution of learning"); evolved/neuromodulated plasticity for memory tasks is
Soltoggio et al. and Risi & Stanley (adaptive HyperNEAT), and the T-maze is a
standard plasticity benchmark. Our contribution is the substrate-specific, signed
measurement on the process-free faber path, with the mechanism visualised. See
`../docs/RESEARCH_PRIOR_ART.md`.

## Caveat / next

- **n=10; delay 2.** The delay-2 T-maze is solved by both mechanisms on generation
  0-2, so it cannot rank them. Next: a delay sweep (4/8/16) and/or noise, to find
  where plasticity and CfC diverge — the interesting P3 question.
- **Global rule.** One {A,B,C,D,Eta} for the whole net. A per-connection or
  per-layer rule (or neuromodulation) is the richer P3 direction.
- **Visualisation.** The three-arm result + the solved-agent latch trace are
  rendered as a self-contained dashboard Artifact (the new experiment-viz pattern).
