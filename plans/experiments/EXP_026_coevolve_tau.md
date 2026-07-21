# EXP_026 — Co-evolve CfC tau with weights on DPNV

The representation leg of the DPNV three-way fork (root map: the wall of insights
024/025 is representation, optimizer, or deception). Programme 2.

## Hypothesis

DPNV stalls (024 GA, 025 ES) partly because `create_cfc_feedforward` fixes each
hidden neuron's time constant `tau` to a RANDOM value in [0.1, 2.0] and the ES
evolves only weights. Tau IS the CfC memory timescale. **Prediction (directional):**
if the ES co-evolves tau alongside the weights, DPNV solves (balances ~1000 steps)
within the same budget at which weights-only stalls (025: 179/436/224 of 1000 at
400k evals). What would surprise us: co-evolved tau also fails at 400k, which
would move the wall off representation and onto the optimizer (EXP-028).

## Why now / what it unblocks

The cheapest, most decisive test on the P2 critical path. A solve localises the
wall to representation and the fix is nearly free (widen the evolved parameter
set). A non-solve sends us to CMA-ES / CoSyNE (EXP-028) with representation ruled
out. Either outcome is a signed result.

## Method

- **Problem:** DPNV — double pole, no velocity. `pb_sim`, sensor variant `3`
  (`[CPos, PAngle1, PAngle2]`), actuator params `[without_damping, 1, 1000]`
  (double-pole fall check on, 1000-step solve goal). Genuine non-Markov benchmark.
- **Method path:** process-free ES bridge (EXP-025): `network_evaluator:set_weights`
  + `evaluate_with_state` (carries CfC internal_state) against `pb_sim`'s pure
  physics. No process-per-neuron, no scape process.
- **Substrate:** CfC feedforward, `create_cfc_feedforward(3, [12], 1, tanh, tanh)`.
- **Optimizer:** `mu_lambda_es` (self-adaptive (mu,lambda)-ES), unchanged.
- **Held constant:** net shape, optimizer, budget, seeds, the pole physics, the
  fitness (steps balanced, without_damping).
- **The one variable:** genome = weights only (control, tau fixed-random = EXP-025)
  vs weights ++ per-cfc-neuron tau (treatment). Tau params map to tau via a
  positive transform `tau = 0.05 + softplus(x)` (keeps tau > 0, no overflow,
  starts near the original [0.1,2.0] band, can grow to longer timescales).

## Config

- Net: 3 -> 12 (cfc) -> 1 (standard). Weights `Nw = 61`; cfc neurons `Ntau = 12`.
- Genome dim: control `61`, treatment `73`.
- ES: `mu=15, lambda=100, max_generations=4000` (~400k evals, matched to 025's
  DPNV budget), `fitness_goal=995.0`, `init_sigma=1.0`.
- Episode bounded at 1000 control steps (cheap evaluations; EXP_016 harness rule).
- Seeded per replicate (`{26,1,N}`) for replay (insight 005).
- Runner: `test/integration/exp026_tests.erl`, throwaway, invoked
  `rebar3 eunit --module=exp026_tests`. Feed lines prefixed `EXP026`. NOT
  committed to the suite.

## Kill criterion

Pre-registered: run both arms at 400k evals. If the treatment (co-evolved tau)
reaches a solve (>=995 steps) in >=1 of the first seeds while the control does not,
representation is confirmed as (a) cause; scale to n>=5 for a rate. If the
treatment reaches 0 solves at 400k across seeds AND its best-fitness ceiling is not
materially above the control's ~180-436 band, tau alone is insufficient: the wall
is the optimizer, proceed to EXP-028. Do not extend budget past 400k in this
experiment (that is a different question).

## Feed

n=10 per arm, 400k evals, solve = 1000 steps. Raw:
`insights/026-raw-tau-coevolution.txt`.

```
control_fixed  (one random tau, all runs)   3/10 solved  (3.3k/4.5k/47.4k evals)
control_redraw (fresh random tau [0.1,2.0]) 0/10 solved  (ceiling 72-803)
control_wide   (fresh random tau [0.1,8.0]) 2/10 solved  (EXP-026c, ceiling 56-184)
treatment      (co-evolve tau)              5/10 solved  (all 3.0-8.1k evals)
```

Stats: treatment vs control_redraw significant (Fisher p~0.03); treatment vs
control_wide NOT significant (p~0.18); control_wide sits between, indistinguishable
from either at n=10. EXP-026c partially resolves 026's evolvable-vs-range caveat.

Method note: the first pass ran all 20 runs in one eunit generator and hit the
3600s timeout (truncated treatment at run 5). Fixed by splitting one generator
per arm, arm-selected via `EXP026_ARM`, each writing its own raw file. The
single-generator smoke (n=1) showed a near-total effect that did NOT survive n=10.

## Insight

**5/10 vs 0/10 (honest baseline), Fisher p~0.03 — representation is a real,
significant factor, but a partial fix.** The corrected control (redraw tau/run)
is 0/10, so the effect is genuine; but treatment is 5/10 not 10/10, so the
optimizer leg (EXP-028) stays live. Solvers reach tau~4-8, above the [0.1,2.0]
init band. Full write-up:
[`insights/026-coevolving-tau-solves-dpnv-5of10-vs-0-representation-is-real-but-partial.md`](../../insights/026-coevolving-tau-solves-dpnv-5of10-vs-0-representation-is-real-but-partial.md).
