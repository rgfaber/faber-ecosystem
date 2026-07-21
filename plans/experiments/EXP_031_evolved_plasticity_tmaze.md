# EXP_031 — evolved plasticity on the T-maze (opens Programme 3)

The first Programme 3 (Meta-learning) experiment. Where P1/P2 evolved WEIGHTS,
P3 evolves the LEARNING RULE: the network adapts its own weights within an
episode (memory-by-learning), vs CfC's memory-by-storage. Tested on the one task
where memory demonstrably pays (insight 019).

## Hypothesis

An evolved local Hebbian rule can solve the cue-memory T-maze by latching each
trial's cue into the weights during the cue step and reading it out at the
junction. **Prediction:** the plastic arm solves the T-maze (>=90/100), like the
CfC weight-memory arm (019: solves in ~1 generation) and unlike the memoryless
control (capped at exactly 50/100). What would surprise us: plasticity cannot
solve it (the rule can't create readable cue-dependent state in a feedforward net
at this size/budget), or it solves but generalises worse across cue permutations.

## Why now / what it unblocks

Opens P3 on its strongest near-term hook. Extends the memory arc (026-030): those
asked whether STORING memory in evolved weights pays; this asks whether a
different mechanism (a learned rule adapting weights online) works. The NIF
already ships the plasticity primitives (hebbian_update_batch / oja / stdp).

## Method

- **Problem:** cue-memory T-maze (`tmaze_sim`, delay 2, 100 balanced trials, solve
  = 90). Memoryless caps at exactly 50 (balanced cues), so 90 is genuine memory.
- **Net:** feedforward `2 -> [12] -> 1`, tanh. No recurrence, no CfC state — the
  memory comes from plasticity.
- **The plastic path (built):** `network_evaluator:evaluate_with_plasticity/3`.
  Forward pass, then every weight updated by a GLOBAL ABCD-Hebbian rule using the
  layer's pre/post activations: `dW = Eta*(A*pre*post + B*pre + C*post + D)`,
  clamped to [-10,10]. Biases fixed. The eval loop RESETS the weights to the
  evolved initial at each cue step (input `[cue,0]`, cue!=0), so each trial
  re-latches its own cue; the input layer then holds through the corridor (pre=0)
  and reads out at the junction. Validated: `plastic_demo_tests` (plasticity moves
  weights; cue-layer holds during corridor; +cue vs -cue diverge).
- **Optimizer:** `sep_cma_es`. Genome = initial weights (49) ++ rule {A,B,C,D,Eta}
  (5) = 54. Eta bounded via `0.1*tanh` in the runner to keep the learning rate sane.
- **The one variable:** memory mechanism. Three arms:
  - `plastic`  — evolved weights + evolved Hebbian rule (dim 54)
  - `cfc`      — evolved weights + co-evolved tau (dim 61), the 019/026 baseline
  - `memoryless` — evolved weights, no memory (dim 49), the 50-cap control

## Config

lambda 100, ~200k evals, fitness_goal 90.0, init_sigma 1.0, n=10, seeded.
Runner `test/integration/exp031_tests.erl` (throwaway). The plastic path
(`evaluate_with_plasticity`) is PERMANENT (`src/network_evaluator.erl`).

## Kill criterion

If plastic reaches >=90 in >=1 of the first seeds, plasticity solves the T-maze;
scale to n>=10 for a rate and compare evals-to-solve vs CfC. If plastic caps near
the 50 memoryless ceiling across seeds, a global feedforward Hebbian rule is
insufficient at this size/budget (next: per-connection rule, or a bigger net).

## Visualisation (the new capability)

Experiments emit structured JSON (learning curve per generation, solve rates, and
mechanism traces), rendered as a self-contained Artifact dashboard. For EXP-031
the mechanism plot is the memory-latching trace: weight-displacement-from-baseline
over the trial steps for +cue vs -cue, showing the cue stored at step 0, HELD
through the corridor, read out at the junction, and diverging by cue = the memory.
Demonstrated from `plastic_demo_tests` output (`plastic-demo.json`).

## Feed

```
(paste run results here as they land)
```

## Insight

*Link to `insights/031-*.md` once run.*
