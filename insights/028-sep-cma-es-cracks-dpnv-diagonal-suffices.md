# 028 — sep-CMA-ES cracks DPNV: 6/10 weights-only (vs 0/10 plain ES) and 10/10 with co-evolved tau. The optimizer leg is confirmed, DPNV is now reliably solved, and DIAGONAL covariance suffices (no full CMA-ES, no eigendecomposition NIF).

**Verdict:** confirmed (optimizer leg) + inflection (first reliable DPNV solve;
the DPNV fork resolves to optimizer + representation, NOT deception) · **Date:** 2026-07-21
**Programme:** 2 (Search Strategies), EXP-028. The optimizer leg of the DPNV fork.
**Uses:** new `sep_cma_es` (validated on benchmarks), `network_evaluator` bridge,
`pb_sim` (DPNV). Follows 024/025/026.

## The question

026 left DPNV's wall a compound of representation (fixed-random tau) and optimizer
(a plain (mu,lambda)-ES, single global sigma). This tests the optimizer leg: does
sep-CMA-ES (the diagonal restriction of CMA-ES, adapting a PER-COORDINATE step
size, no correlations) raise the solve rate at matched budget?

## Method

Same process-free bridge, net, budget (lambda 100, 400k evals), seeds, and fitness
as 026. The only change is the optimizer (`mu_lambda_es` -> `sep_cma_es`), in two
arms matched to 026's. `sep_cma_es` was validated first on benchmark functions:
sphere, and a condition-1e6 ellipsoid that only converges if the per-coordinate
variance adaptation is correct (a covariance-update bug fails it while the sphere
still passes). Raw: `028-raw-sep-cma-es-dpnv.txt`.

## Results

| Arm | optimizer | tau | solved | evals-to-solve |
|---|---|---|---|---|
| control_redraw (026) | plain (mu,lambda)-ES | random | 0/10 | — |
| **sepcma_weights** | **sep-CMA-ES** | random | **6/10** | 17k-128k |
| treatment (026) | plain (mu,lambda)-ES | co-evolved | 5/10 | 3-8k |
| **sepcma_tau** | **sep-CMA-ES** | co-evolved | **10/10** | 7.6k-24k |

## Finding 1 — the optimizer was a real wall

Swapping the plain ES for sep-CMA-ES, holding everything else fixed, lifts
weights-only DPNV from **0/10 to 6/10** (Fisher exact p ~ 0.01) and tau-co-evolve
from **5/10 to 10/10** (p ~ 0.03). Per-coordinate step-size adaptation is a large
part of what the plain single-sigma ES lacked. The optimizer leg of the DPNV fork
is confirmed, alongside the representation leg (026).

## Finding 2 — representation and optimizer compound to a reliable solve

**sep-CMA-ES + co-evolved tau solves DPNV 10/10**, every seed, in 7.6k-24k
evaluations (a fraction of the 400k budget). Against the plain-ES + random-tau
baseline (026 control_redraw, 0/10) this is p ~ 1e-5. The two legs of the fork are
both real and combine cleanly: fix the memory timescale (tau) AND the search
(covariance-aware), and DPNV goes from unsolvable to routine. This is the first
reliable solve of DPNV in the programme, and the payoff of the 024->025->026->028
arc.

## Finding 3 — diagonal suffices; full CMA-ES (and the eig NIF) are not needed

sep-CMA-ES keeps its covariance DIAGONAL: no correlations, no eigendecomposition.
That it reaches 10/10 means the missing ingredient was anisotropic per-coordinate
SCALING, not correlated/rotated steps. So full CMA-ES is not required for DPNV, and
the symmetric-eigendecomposition NIF it would need (the identified gap: the native
crate is rustler-only, no nalgebra) is NOT built. The decision gate resolves
against the Rust work, on the data.

## Finding 4 — the DPNV fork resolves to optimizer + representation, not deception

The 024/025 wall had three candidate causes: representation (P3/tau), optimizer
(P2), deception (P4/novelty). 026 confirmed representation (partial); 028 confirms
optimizer (major) and shows the two together reach the objective directly, in a few
thousand evaluations. So DPNV was NOT deceptive: no novelty/quality-diversity was
needed to escape a misleading gradient. The P4 deception hypothesis is answered
negative FOR DPNV (P4 remains a valid programme for genuinely deceptive problems).

## Prior art

Replication, not discovery. CMA-ES and its variants are the established
neuroevolution solvers for pole balancing (Igel 2003, "Neuroevolution for
Reinforcement Learning Using Evolution Strategies"); sep-CMA-ES is Ros and Hansen
2008 ("A Simple Modification in CMA-ES Achieving Linear Time and Space
Complexity"). Our contribution is the substrate-specific, signed measurement: on
the DXNN2/Erlang process-free bridge, diagonal covariance adaptation plus evolvable
tau is sufficient for reliable DPNV. See `../docs/RESEARCH_PRIOR_ART.md`.

## Caveat / next

- **n=10 per arm.** The headline comparisons are significant (p ~ 0.01 / 0.03 /
  1e-5). sepcma_tau vs sepcma_weights (10/10 vs 6/10) is only borderline (p ~ 0.09):
  co-evolving tau on top of sep-CMA helps but n=10 cannot prove the last increment.
- **The `sep_cma_es` module is permanent** (committed, benchmark-validated); the
  DPNV runner was throwaway.
- **Next:** EXP-029 — the bridge back to Programme 1. Now that DPNV is reliably
  solvable, re-run the memory comparison (018/019/024) under sep-CMA-ES: does memory
  pay once solves are reachable? That is the payoff Programme 1 could not measure.
