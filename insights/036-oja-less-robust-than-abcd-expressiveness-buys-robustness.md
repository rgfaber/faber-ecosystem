# 036 — Oja's rule is NOT the plasticity workhorse: the constrained self-normalising Oja (1-param) is cheapest at low noise but LESS noise-robust than the expressive ABCD-Hebbian (5-param) -- Oja breaks 2/8 at sigma=0.5 where ABCD holds 8/8, sitting between ABCD and CfC. Expressiveness buys robustness; the "richer rule" win needs MORE parameters, not fewer.

**Verdict:** confirmed + finding (ABCD > Oja > CfC under noise) + a corrected framing
(Oja is simpler, not richer) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-036. **Uses:** `evaluate_with_plasticity/3`
(now with an Oja clause, faber-tweann 8b37d62), `sep_cma_es`, `tmaze_sim`.

## The question

The current plastic rule is a global ABCD-Hebbian ({A,B,C,D,Eta}, 5 params). The
roadmap asked whether a "richer" rule widens plasticity's lead. Oja's rule
(dw = Eta*post*(pre - post*w), 1 param, self-normalising) is the classic principled
alternative -- stable weights without a clamp. Does it beat ABCD under noise?

## Method

Delay-8 T-maze, sensor noise sigma 0.1/0.3/0.5, three arms: abcd (5-param), oja
(1-param), cfc (baseline). sep-CMA-ES, n=8. Raw: `036-raw-rule-comparison.txt`.

## Results (mean over 8 runs)

| rule | sigma=0.1 | sigma=0.3 | sigma=0.5 |
|---|---|---|---|
| abcd | 8/8 (96.4, 450 evals) | 8/8 (93.0, 2438) | **8/8** (91.2, 3912) |
| oja | 8/8 (95.5, **225 evals**) | 8/8 (91.5, 9050) | **2/8** (82.5, 23938) |
| cfc | 8/8 (93.2, 1200) | 8/8 (91.2, 8875) | **0/8** (80.6, capped) |

## Finding 1 — Oja is NOT more robust; it is LESS

The elegant, self-normalising Oja rule does not beat ABCD. At sigma=0.5 Oja solves
only 2/8 (fitness 82.5) where ABCD holds 8/8 (91.2). Oja sits BETWEEN ABCD (robust)
and CfC (fails), degrading toward CfC-like fragility under heavy noise. The
principled simpler rule loses to the expressive one.

## Finding 2 — expressiveness buys robustness

ABCD's extra terms (B*pre, C*post, D bias) give evolution the freedom to find
noise-robust imprints. Oja's single term (post*(pre - post*w)) is constrained to a
Hebbian-with-decay form; that self-normalisation, while it keeps weights bounded,
over-constrains the memory and leaves less room to shape a noise-tolerant solution.
So the rule's PARAMETER COUNT / expressiveness, not its stability property, is what
buys the robustness that 034 measured for ABCD.

## Finding 3 — Oja IS cheapest at LOW noise

At sigma=0.1 Oja solves in 225 evals -- half of ABCD's 450 and a fifth of CfC's
1200. So the constrained rule is efficient when the task is easy; it is only under
stress that its lack of expressiveness costs it. A clean speed-vs-robustness trade.

## Finding 4 — corrects the roadmap framing: Oja is SIMPLER, not richer

The roadmap listed Oja under "richer rules", but Oja has FEWER parameters than ABCD.
This experiment therefore tests the wrong direction: it shows a SIMPLER rule is less
robust. The genuine "does a richer rule widen the lead" question needs rules with
MORE expressiveness than ABCD -- per-connection coefficients, or a reward-modulated
(neuromodulated) term. Those remain the open P3 direction.

## Prior art

Replication + a comparative note. Oja 1982 (the self-normalising Hebbian rule) and
the ABCD/Soltoggio generalised plasticity rule are both standard. That the more
expressive rule is more robust under noise on this benchmark is a substrate-specific
measurement; the speed-vs-robustness trade is the expected shape.

## Caveat / next

- **n=8; one task, one delay.** Oja's fragility at sigma=0.5 (2/8) is clear, but a
  larger n would tighten the abcd-vs-oja gap at sigma=0.3 (both 8/8, abcd cheaper).
- **ABCD stays the workhorse.** Keep it; do NOT switch to Oja for robustness.
- **Next (the real "richer" test):** per-connection {A,B,C,D} (genome grows to
  N_w x 4) or a reward-modulated term (the T-maze returns reward at the junction) --
  MORE expressive than ABCD, to see if the robustness lead widens further.
- Feeds the P3 dashboard (rule-comparison panel).
