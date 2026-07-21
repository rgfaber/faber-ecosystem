# 026 — Co-evolving CfC tau solves DPNV 5/10 where an honest random-tau baseline solves 0/10 (Fisher p~0.03): representation is a real, significant factor in the wall, but co-evolving tau alone is only a partial fix.

**Verdict:** confirmed (representation effect, significant) + signed correction
(control is 0/10, not "never" and not 025's implied 0; the smoke's effect size was
wildly overstated) + partial (5/10, the optimizer leg stays live) · **Date:** 2026-07-21
**Programme:** 2 (Search Strategies), EXP-026. The representation leg of the DPNV fork.
**Uses:** `mu_lambda_es`, `network_evaluator` (process-free bridge), `pb_sim` (DPNV).
Follows 025.

## The question

025 left DPNV's wall as a compound of representation (each CfC hidden neuron's
time constant `tau` is fixed to a RANDOM value in [0.1, 2.0]; the ES evolves only
weights) and optimizer (a plain (mu,lambda)-ES is the weakest ES). EXP-026 tests
the representation leg directly: extend the ES genome to co-evolve tau alongside
the weights. Does DPNV then solve?

## Method

Process-free bridge (025): a genome is written into a fixed-topology CfC net
(`create_cfc_feedforward(3,[12],1,tanh,tanh)`) and evaluated by running `pb_sim`'s
pure DPNV physics (sensor variant 3, double-pole fall check, solve = balance 1000
steps) against `evaluate_with_state`. `mu_lambda_es`, mu15/lambda100, 400k-eval
budget, n=10, seeded. Genome = weights (61) for the controls, weights ++ per-cfc
tau (73) for the treatment; tau maps through `tau = 0.05 + softplus(x)`. Three arms:

- **control_fixed** — weights only; ONE random tau vector, fixed across all 10 runs.
- **control_redraw** — weights only; a FRESH random tau drawn per run (the honest
  baseline: samples the fixed-random-tau distribution instead of one draw).
- **treatment** — co-evolve tau.

Raw: `026-raw-tau-coevolution.txt`.

## Results

| Arm | solved | evals-to-solve | non-solve fitness /1000 |
|---|---|---|---|
| control_fixed (one tau) | 3/10 | 3.3k, 4.5k, 47.4k | 120-288 |
| control_redraw (fresh tau/run) | **0/10** | — | 72-803 (mostly <230) |
| treatment (co-evolve tau) | **5/10** | 3.0k, 4.6k, 4.9k, 5.1k, 8.1k | 124-768 |

## Finding 1 — the honest baseline is 0/10, and it corrects the smoke and 025

An n=1 smoke had control not-solving and treatment solving, implying a near-total
effect. It did not survive n=10. Two corrections:

- **Weights-only with a fixed tau is not "never":** one lucky tau vector solved
  3/10 (control_fixed). 025's "0/4" and the n=1 "never" were undersampling.
- **But most random taus are unsolvable:** redrawing tau each run gives **0/10**
  at 400k. So control_fixed's 3/10 was a property of one lucky draw, not of
  random tau in general. control_redraw is the correct baseline.

## Finding 2 — co-evolving tau is a real, statistically significant effect

Against the honest baseline, **treatment 5/10 vs control_redraw 0/10** is
significant at n=10: Fisher exact two-tailed **p ~ 0.033**. Every treatment solve
is fast (3.0-8.1k evals, 30-80 generations), well inside the budget at which both
controls stall. Representation (an evolvable memory timescale) is therefore a
genuine factor in the DPNV wall, not a spurious one.

## Finding 3 — the mechanism: solvers reach for longer timescales than random init offers

The best individual's tau range was logged. Every one of the 5 solvers has at
least one neuron with **tau ~ 4-8**, above the default [0.1, 2.0] init band, while
keeping the mean moderate (~0.7-1.9). DPNV wants longer memory timescales than
random init supplies, which is partly why control_redraw (tau capped in [0.1,2.0])
never solves: the needed timescale is often outside its range.

## Finding 4 — but it is a PARTIAL fix; the optimizer leg stays live

Treatment is 5/10, not 10/10. The 5 failures ran the full 400k and let tau drift
large (up to 12.75) without solving: a large timescale is necessary-ish but far
from sufficient, and a plain (mu,lambda)-ES cannot reliably find the joint
(weights, tau) configuration. So representation is confirmed AND the optimizer
remains a real limiter, exactly the compound 025 diagnosed. This keeps EXP-028
(CMA-ES / CoSyNE, the literature's DPNV solvers) fully motivated: the target is
turning 5/10 into reliable.

## EXP-026c — is it evolving tau, or just access to longer timescales? (partially resolved)

026's caveat: control_redraw fixes tau in [0.1, 2.0] AND cannot move it, so the
5/10-vs-0/10 effect confounds "evolvable" with "wider range". EXP-026c adds a
**control_wide** arm: weights-only, a fresh random tau per run in the wider
**[0.1, 8.0]** band (the range the 026 solvers reached), still fixed within a run.
n=10, same budget. Result: **2/10** solved.

| Arm | tau | solved |
|---|---|---|
| control_redraw | fixed random [0.1, 2.0] | 0/10 |
| control_wide | fixed random [0.1, 8.0] | 2/10 |
| treatment | co-evolved | 5/10 |

The ladder 0 -> 2 -> 5 says **both mechanisms plausibly contribute**: widening the
fixed range lifts 0 to 2 (access to longer timescales helps), and evolving reaches
5. But the honest statistics stop short of separating them at n=10:

- treatment 5/10 vs control_redraw 0/10: **significant, Fisher two-tailed p ~ 0.033**.
- treatment 5/10 vs control_wide 2/10: **NOT significant** (one-tailed p ~ 0.18).
- control_wide 2/10 vs control_redraw 0/10: not significant.

So control_wide is statistically indistinguishable from BOTH ends: n=10 cannot
cleanly attribute the effect to range vs evolvability. The tau logs reinforce the
mechanism either way: control_wide solvers and stallers both carry long taus (means
3.5-5.0), so a long timescale is necessary-ish but not sufficient — it must be
MATCHED to the weights, which evolving does and a fixed draw (even from the good
band) mostly misses. **Verdict on the caveat: partially resolved.** Access to
longer timescales gives a modest, non-significant lift; the significant effect
needs evolvable tau; a clean separation would need larger n (30-50/arm).

## Prior art

Replication, not discovery. In LTC/CfC the time constant IS the memory timescale
(Hasani et al. 2021/2022; see `../docs/RESEARCH_PRIOR_ART.md`), so "fixing tau
randomly cripples memory and evolving it helps on a non-Markov task" is the
expected consequence of the model, measured here on DPNV with the ES bridge. The
partial-fix framing echoes NEAT/complexification: the right representation
parameter matters, but the search still has to find it.

## Caveat / next

- **n=10 per arm; the significant comparison (5/10 vs 0/10) rests on n=10.** Solid
  for a rate, but a wider replication would tighten the tau-range story (n=5 solvers).
- **Evolvable-tau vs merely wider-range: partially disentangled (EXP-026c, above).**
  A wider [0.1,8.0] fixed-tau control solves 2/10 — intermediate, and statistically
  indistinguishable from both 0/10 and 5/10 at n=10. Both mechanisms plausibly
  contribute; a clean split needs larger n (30-50/arm), deferred as low-value.
- **Next:** EXP-028 (CMA-ES / CoSyNE) to attack the remaining optimizer leg — the
  live path to turning 5/10 into reliable.
