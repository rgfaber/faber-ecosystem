# 048 — 047's good-arm tracking is a SHALLOW ~3-5 trial reactive window over the interaction evidence, NOT deep multi-trial integration, and it is UNIFORM across the three substrates. Decoding the current good arm from a k-step window of the last k [reward, action] observations: with RAW [reward, action] features a linear decoder saturates at ~0.93 by k=3 and each substrate's internal state appears to beat it (cfc 0.965 vs raw-w10 0.934), which naively reads as deep integration. But the per-step bandit evidence is the INTERACTION r*a (which arm the last trial implicates: action +1/-1 for arm0/arm1 times reward +1/-1), which a raw-linear decoder cannot form. Add r*a per step and a mere 3-5 step interaction window MATCHES OR BEATS every substrate's state: cfc state 0.965 vs interaction-w5 0.978; nm_global 0.895 vs 0.966; nm_pc 0.955 vs 0.974; the paired (state - interaction_w10) is negative for all three (positive in 2/10, 0/10, 1/10 champions). So all three substrates track with a short reactive memory of the last few interaction observations, not evidence accumulation over many trials, and the SAME short window fits all three. The initial "deep" reading was a decoder-class artifact caught at the CLAIM gate.

**Verdict:** signed negative (my own "deep integration" draft refuted by the prescribed control) + characterisation (shallow, uniform, interaction-window tracking) + method (decoder-class confound) · **Date:** 2026-07-23
**Programme:** 3 (Meta-learning), EXP-048 — 047 follow-up. **Uses:** the PERSISTED 047 champions
(`faber-programmes/.../exp047_champions.eterm`), `prob_reversal_bandit_sim`, a k-step
sensor-history-window logistic decoder. Replay-only, NO re-evolution. n=10 champions/arm.

## The question (047's one open flank)

047 showed the lifetime substrates decode the good arm from their internal state near-ceiling
and about equally, beat the one-step sensor, and are not clocks, but explicitly did NOT resolve
DEPTH: a state holding just the last few [reward, action] pairs would score the same. This
experiment, prescribed by 047's own CLAIM gate, decodes the good arm from a k-step window of
the last k observations (k in {1,3,5,10}) and asks at what k the window matches the state.

## The trap, and the control that sprang it

A first pass used RAW [reward, action] window features. There the state appeared to beat even a
10-step window (cfc 0.965 > raw-w10 0.934; nm_pc 0.956 > 0.925), reading as "deep integration".
The CLAIM gate refuted it: the per-step evidence in this bandit is the PRODUCT r*a (you played
arm a and got reward r, so arm a is good iff r>0; the sign of r*a names the implicated arm). A
linear decoder on raw [r,a] cannot represent that product, while a network's tanh units present
nonlinearly-recoded observations in linearly-decodable form. So "state beats raw-linear window"
proves only that the state is not a LINEAR function of the last 10 raw observations -- NOT that
it integrates beyond them. A state that nonlinearly recodes just the last 3-5 pairs produces the
same signature (and the raw window saturating by k=3, 0.936/0.936/0.934, is exactly that).

## Method (corrected)

Add the interaction r*a to each step's window feature ([r, a, r*a], 3k dims). Compare each
substrate's state AUC to its interaction-window AUC, PAIRED per champion (same replay), with the
logistic decoder trained to convergence (1200 iterations, up from 400, so the wider windows are
not undertrained). Same polarity-balanced split and transition-trial exclusion as 047. Sanity:
the raw k=1 window reproduces 047's per-arm one-step sensor AUC EXACTLY (fixed 0.798, cfc 0.908,
nm_global 0.855, nm_pc 0.885) and the state AUCs reproduce 047 (cfc 0.965, nm_pc 0.955,
nm_global 0.895, fixed 0.500). Raw: `048-raw-integration-depth.txt`.

## Results (n=10, mean AUC)

| arm | state | raw-w3 | raw-w10 | intr-w3 | intr-w5 | intr-w10 | state - intr_w10 (paired) |
|---|---|---|---|---|---|---|---|
| cfc | 0.965 | 0.936 | 0.934 | 0.974 | **0.978** | 0.974 | -0.009 (2/10 positive) |
| nm_global | 0.895 | 0.894 | 0.894 | 0.957 | **0.966** | 0.964 | -0.068 (0/10) |
| nm_pc | 0.955 | 0.920 | 0.925 | 0.969 | **0.974** | 0.969 | -0.014 (1/10) |
| fixed (sentinel) | 0.500 | 0.942 | 0.973 | 0.970 | 0.986 | 0.987 | -0.487 (0/10) |

## Finding — SHALLOW, uniform, interaction-window tracking

With the interaction feature, a 3-5 step window matches or beats every substrate's state, and
the paired (state - interaction_w10) is negative for all three adaptive arms with near-unanimous
sign (positive in only 0-2 of 10 champions). None of the substrates carries good-arm information
beyond a short reactive window of the last few interaction observations. The tracking 047
measured is real but SHALLOW (effective depth ~3-5 trials), and it is UNIFORM: the same explicit
short window fits cfc, nm_global, and nm_pc alike. If anything the states are slightly LOSSY (the
5-step interaction window edges out every state).

## What this refines, and what it leaves standing

- **Refines 047:** "the substrates carry multi-step history beyond the one-step sensor" is
  correct but the horizon is only ~3-5 trials of interaction evidence, not deep accumulation.
  And it SHARPENS 047's "same computation" reading: one short interaction window fits all three.
- **Untouched:** 045's low-pass mechanism stays refuted; 047's core stands (the substrates track
  the good arm, equally, and it is not a clock -- the shifted-reversal control is independent of
  depth). The depth result changes the CHARACTER of the shared computation (shallow, not deep),
  not whether it is shared.
- **Retracted (in-session):** the intermediate "deep integration" reading of the raw-window pass.
  It was a decoder-class artifact (linear model cannot form r*a); the interaction-augmented
  control, prescribed by 047's CLAIM gate, flipped it. This is the fourth time the adversarial
  gate changed a conclusion before signing (cf 038, 040, and 047-run-1).

## Pointers

- Raw: `048-raw-integration-depth.txt`. Runner + engine pin + reused 047 champions:
  `faber-programmes/programmes/p3_meta_learning/exp048_integration_depth/`.
- Parent: [047](047-lifetime-substrates-converge-by-tracking-not-by-low-pass.md). Also fixed a
  transcription typo in 047's table (fixed sensor 0.766 -> 0.798; the feed always said 0.798).
