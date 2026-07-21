# EXP_028 — sep-CMA-ES on DPNV (the optimizer leg)

The optimizer leg of the DPNV fork (Programme 2). Pre-registered in insight 026's
"next": does a covariance-aware optimizer solve DPNV where the plain (mu,lambda)-ES
stalled?

## Hypothesis

025/026 left the DPNV wall as a compound of representation (tau) and optimizer (a
plain (mu,lambda)-ES is the weakest ES, single global sigma). **Prediction:** an
optimizer that adapts per-coordinate step sizes (sep-CMA-ES, the diagonal
restriction of CMA-ES) raises the DPNV solve rate over the plain ES at matched
budget, both weights-only and with co-evolved tau. What would surprise us: no
improvement (the wall is correlations, needing full CMA-ES), or degradation.

## Why now / what it unblocks

The last leg of the DPNV fork. A positive here confirms optimizer as a real
factor and, combined with 026's representation result, tells us whether DPNV is
reliably solvable at all. It also gates the eigendecomposition NIF: sep-CMA needs
none (diagonal C), so if diagonal suffices we never build it.

## Method

- **Problem:** DPNV. Same process-free bridge as 026 (`pb_sim` sensor 3, solve =
  1000 steps), CfC net `create_cfc_feedforward(3,[12],1,tanh,tanh)`.
- **Optimizer:** `sep_cma_es` (new, `src/`), validated first on benchmark functions
  (`sep_cma_es_tests`: sphere + a condition-1e6 ellipsoid that only passes if the
  per-coordinate variance adaptation is correct).
- **Held constant:** net, budget (lambda 100, 400k evals), seeds, physics, fitness.
- **The one variable:** optimizer (`mu_lambda_es` -> `sep_cma_es`), in two arms
  matched to 026:
  - `sepcma_weights` — weights only, fresh random tau/run (vs 026 control_redraw 0/10)
  - `sepcma_tau` — weights ++ co-evolved tau (vs 026 treatment 5/10)

## Config

lambda=100, max_generations=4000 (~400k evals), fitness_goal=995, init_sigma=1.0,
x0=zeros. n=10, seeded `{28, arm, N}`. Runner `test/integration/exp028_tests.erl`
(throwaway). `sep_cma_es` + its tests are PERMANENT (committed).

## Kill criterion

If sep-CMA does not beat the plain ES on either arm (i.e. sepcma_weights ~ 0/10
AND sepcma_tau ~ 5/10), the optimizer leg is not diagonal-adaptation and the next
step is full CMA-ES (needs the eig NIF) or CoSyNE. If it clearly beats, the
optimizer leg is confirmed and the NIF is not built.

## Feed

n=10 per arm. Raw: `insights/028-raw-sep-cma-es-dpnv.txt`.

```
sepcma_weights (random tau)   6/10 solved  (17-128k evals)   vs 026 control_redraw 0/10
sepcma_tau     (co-evolve)   10/10 solved  (7.6-24k evals)   vs 026 treatment      5/10
```

Stats: sepcma_weights 6/10 vs control_redraw 0/10 Fisher p~0.01; sepcma_tau 10/10
vs treatment 5/10 p~0.03; sep-CMA+tau 10/10 vs plain-ES+random-tau 0/10 p~1e-5.

## Insight

**sep-CMA-ES cracks DPNV; with co-evolved tau it is 10/10.** Optimizer leg
confirmed; DPNV reliably solved; diagonal covariance suffices (no eig NIF). Full
write-up:
[`insights/028-sep-cma-es-cracks-dpnv-diagonal-suffices.md`](../../insights/028-sep-cma-es-cracks-dpnv-diagonal-suffices.md).
