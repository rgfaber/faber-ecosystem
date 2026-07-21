# 034 — Noisy T-maze: sensor noise BREAKS CfC (0/8 at sigma=0.5) while plasticity stays robust (8/8 at every level). A capability gap, not just a cost gap. With 033 (delay), plasticity beats CfC on both stressors: it scales more gracefully with the memory horizon AND survives noise.

**Verdict:** confirmed + finding (a capability separation) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-033b. **Uses:** `evaluate_with_plasticity/3`,
`sep_cma_es`, `tmaze_sim`. Run in parallel with 033 (extended delay) via a git
worktree.

## The question

033 (extended delay) showed plasticity scales more gracefully than CfC, but both
still SOLVE up to delay 64 -- a cost gap, not a capability gap. Does the OTHER
stressor, sensor noise, separate them harder? Hypothesis: Gaussian noise corrupts a
decaying CfC state (it integrates noise over the corridor) more than a plastic
weight re-imprinted each trial.

## Method

T-maze delay 8, Gaussian noise added to the sensor each step (sigma 0.1/0.3/0.5),
plastic vs cfc, sep-CMA-ES (lambda 100, 300 gens cap, solve 90/100), n=8. Cue-step
reset detection uses the CLEAN sensor so noise cannot misfire the reset; the net
sees the noisy input. Fitness is stochastic (one noisy episode per eval). Standard
noisy-T-maze paradigm (Ziemke and Thieme 2002). Raw: `034-raw-noise-sweep.txt`.

## Results (mean over 8 runs)

| sigma | plastic solved | plastic fitness | plastic evals | cfc solved | cfc fitness | cfc evals |
|---|---|---|---|---|---|---|
| 0.1 | 8/8 | 97.1 | 462 | 8/8 | 94.5 | 712 |
| 0.3 | 8/8 | 92.6 | 1675 | 8/8 | 91.1 | **10300** |
| 0.5 | **8/8** | 91.8 | 5938 | **0/8** | 80.5 | 30000 (capped) |

## Finding 1 — noise BREAKS CfC; plasticity survives

At sigma=0.5, CfC solves **0/8** (caps at fitness 80.5, full budget), while plastic
solves **8/8** (91.8). This is a capability gap: at that noise level the plastic
mechanism CAN hold the cue and the CfC mechanism CANNOT. Even at sigma=0.3, CfC
barely clears the bar (91.1, just above the 90 threshold) and only by spending
**~6x** the evals plastic needs (10300 vs 1675). Plasticity degrades gently across
the whole range and always solves.

## Finding 2 — the direction confirms the hypothesis (tentatively)

Noise hurts memory-by-storage more than memory-by-learning, as predicted. Plausible
mechanism: a CfC neuron integrates its input into a continuous state, so per-step
sensor noise accumulates in the held value and corrupts the cue over the corridor;
the plastic net re-imprints the cue from the strong (+/-1) cue signal each trial and
resets to a clean baseline, so per-step corridor noise perturbs the imprint less.
Consistent with the data, but the exact evolved encodings are not pinned (031), so
this is a hypothesis, not a proof. The empirical break at sigma=0.5 is the solid claim.

## Finding 3 — together with 033, plasticity wins on BOTH stressors

- **Memory horizon** (033): both solve to delay 64, but CfC costs ~3.3x more to
  evolve -> plasticity scales more gracefully (a cost gap).
- **Noise** (034): CfC fails at sigma=0.5, plasticity solves -> plasticity is more
  robust (a capability gap).

So across the two ways of stressing memory, evolved plasticity (memory-by-learning)
is the more robust mechanism than the CfC state (memory-by-storage) on this
benchmark. This is Programme 3's payoff: the two memory mechanisms are equivalent on
the easy task (031) but rank clearly once stressed.

## Prior art

Replication + a comparative note. The noisy T-maze is Ziemke and Thieme 2002 (which
motivated evolving INTERNAL memory precisely because reactive/fixed schemes fail
under noise). That an evolved local plasticity rule is more noise-robust than a
fixed continuous-time state is a substrate-specific measurement on this benchmark.

## Caveat / next

- **n=8; stochastic fitness.** One noisy episode per eval makes selection noisy;
  a candidate could clear 90 by luck. The 0/8-vs-8/8 gap at sigma=0.5 is large enough
  to be robust to that, but the exact evals numbers are noisy.
- **Global rule; delay 8.** A richer plasticity rule or a different delay could shift
  absolutes; the SHAPE (CfC breaks first) is the claim.
- **Next:** a neuromodulated / per-connection plastic rule (richer P3), or combine
  the stressors (long delay AND noise). Result rendered in the P3 dashboard.
