# 025 — (mu,lambda)-ES cracks the Markov pole instantly and process-free (~70x faster wall-clock), but does NOT solve DPNV even at 400k evals. The wall is optimizer AND representation.

**Verdict:** confirmed (bridge + Markov) + signed negative (DPNV) · **Date:** 2026-07-21
**Programme:** 2 (Search Strategies), first experiment (EXP-025)
**Uses:** the `mu_lambda_es` engine, `network_evaluator` (process-free forward
pass), `pb_sim` physics. Follows the GA wall of insight 024.

## The bridge

The ES-to-network evaluation is deliberately process-free: a candidate weight
vector is written into a fixed-topology network via `network_evaluator:
set_weights`, and evaluated by running `pb_sim`'s PURE physics in a tight loop
against the network's `evaluate_with_state` forward pass (which carries CfC
internal_state for memory). No BEAM process-per-neuron, no exoself, no scape
processes — so no recurrent stalls, and it is fast.

## Results

| Phase | Task / net | budget | result |
|---|---|---|---|
| 1 | pole-1 WITH velocity, feedforward | mu15 l100 | **SOLVED, gen 5 / 500 evals / 145 ms** |
| 2 | DPNV, CfC (6 hidden) | 30k evals | not solved, 110/1000 steps |
| 3 | DPNV, CfC (12 hidden) | 400k evals x3 | not solved: **179, 436, 224** /1000 |

Raw: `025-raw-es-pole.txt`. Fitness = steps balanced (without_damping); solve =
balance 1000 steps.

## Finding 1 — the process-free bridge is ~70x faster wall-clock

(mu,lambda)-ES solves single-pole-with-velocity in ~500 evaluations and **145
milliseconds**. The GA on the same task (insight 015) took ~700 evaluations and
~10 SECONDS. Eval-counts are not directly comparable (the GA also evolves
topology and runs the memetic tuner; the ES is weight-only fixed-topology), but
the WALL-CLOCK per evaluation is dramatically lower because the forward pass runs
in `network_evaluator` (compiled/matrix, no BEAM process spawn) instead of
spawning a process-per-neuron network. ~70x wall-clock for the same task. This
process-free evaluation path is the right substrate for the whole ES programme,
and it makes large-n / high-budget experiments cheap (400k evals in ~4 min).

## Finding 2 — (mu,lambda)-ES is efficient on the Markov problem

It solves the with-velocity (Markov, feedforward-solvable) pole in a handful of
generations, confirming the ES converges cleanly when the task is representable
by the fixed network. The bridge and the optimizer are both sound.

## Finding 3 — but simple (mu,lambda)-ES does NOT solve DPNV, even at 400k evals

This is the signed negative, and it sharpens insight 024. The hope (024) was that
a stronger optimizer would crack double-pole-no-velocity where the GA stalled.
Simple (mu,lambda)-ES does NOT: 179 / 436 / 224 steps out of 1000 at 400k
evaluations (13x the 30k that gave ~110-191). More budget helps SOME (best run
436 vs 30k's ~190) with high variance, but no run reaches a solve. So the GA ->
simple-ES upgrade is not sufficient for DPNV.

The wall is therefore NOT purely "weak GA". Two factors, both live:

1. **Representation.** `create_cfc_feedforward` fixes each hidden neuron's time
   constant `tau` to a RANDOM value in [0.1, 2.0], and the ES evolves only
   weights, never tau. Tau IS the memory timescale of a CfC neuron. If the random
   tau does not match what DPNV needs, no weight setting can compensate — the
   network structurally cannot hold the right memory. This is the leading
   suspect, and it is directly testable.
2. **Optimizer.** A simple self-adaptive (mu,lambda)-ES is the weakest ES. The
   methods that actually solve DPNV in the literature are CMA-ES (Igel 2003) and
   CoSyNE (Gomez 2008) — covariance-aware and cooperative-coevolution
   respectively — not a plain (mu,lambda)-ES.

So DPNV's difficulty is a compound of substrate (fixed-tau CfC) and optimizer
(needs CMA-ES/CoSyNE), on top of the GA's inadequacy. "Just use a better
optimizer" was too simple.

## Next (Programme 2)

- **EXP-026 (the decisive representation test):** co-evolve `tau` alongside
  weights (extend the ES vector to include per-neuron tau). If DPNV then solves,
  the wall was representation (fixed tau), and the fix is cheap. If it still
  stalls, the wall is the optimizer.
- **EXP-028:** implement CMA-ES (the covariance NIF is scaffolding; the
  sampling/update core is the work) and CoSyNE — the literature's DPNV solvers.
- **Infrastructure:** the NIF batched-population evaluator (one Rust call for a
  whole generation) — with the process-free path already ~70x faster, this is the
  next accelerator for the large ES sweeps.

## Caveat

n=1 on Phases 1-2, n=3 on Phase 3. The DPNV negative is robust (0 solves across 4
DPNV runs, 30k-400k evals), but the exact fitness ceiling is variance-laden. The
Markov solve is clean.
