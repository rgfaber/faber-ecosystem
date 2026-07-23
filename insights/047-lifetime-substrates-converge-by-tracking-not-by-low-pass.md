# 047 — 045's low-pass MECHANISM is refuted by direct measurement, and in the OPPOSITE direction: the lifetime-learning substrates do not converge because integration averages away per-step differences, they converge because each INDEPENDENTLY learns genuine evidence-based good-arm tracking that is EQUALLY accurate. Decoding the current good arm from each substrate's own internal state (recurrent hidden state / drifting plastic weights) gives near-ceiling, near-identical AUC (cfc 0.965, nm_pc 0.956, nm_global 0.908; spread 0.057) with clean sentinels (static-net 0.500, time-only control 0.500); the state beats the instantaneous sensor by 0.05-0.07 (multi-step history, not one-step reflex); per-step estimates AGREE (disagreement 0.101 < 0.15, the pre-registered "nothing to average" bar); and a shifted-reversal control (replay at reversal 20/30/40) shows FLAT fitness, no more schedule-sensitive than the reactive-only static arm, ruling out an internal clock. 045's fitness convergence (043-045) STANDS; its causal story ("integration is a low-pass filter that averages per-step substrate differences") does NOT: there are no per-step differences to average.

**Verdict:** signed negative (045 mechanism refuted) + positive mechanism (evidence-based tracking) + method lesson (CLAIM gate caught a fatal decoder confound) · **Date:** 2026-07-23
**Programme:** 3 (Meta-learning), EXP-047 — the P3 closer. **Uses:** `prob_reversal_bandit_sim`,
`evaluate_with_state/2`, `evaluate_with_neuromod/4`, `get_internal_state/1`, `get_weights/1`,
`sep_cma_es`. n=10 independent evolutionary runs per arm. First experiment through the
`faber-programmes` repo (runner + pinned engine `9bb43e6` archived; champions persisted).

## The question

043-045 nulled three lifetime stressors (SNR, reversal frequency, feedback noise): the four
arms (fixed / cfc storage / nm_global / nm_pc plasticity) tie on fitness at ~92-93. 045
EXPLAINED this with a low-pass principle: the mechanisms converge because integration averages
away per-step differences between the substrates. A prior DESIGN gate killed a fitness-race
follow-up as confounded (on this bandit non-integrable = reactively-solvable) and forced a
MECHANISTIC probe: three fitness nulls cannot tell "the substrates compute the same thing"
from "they compute different things that wash out at the action". Only measuring the internal
state can. This is the lifetime-learning analogue of insight 035 (which measured the memory-arc
mechanism directly).

## Method

Re-evolve the 043 fair-contest cell (prob reversal bandit, good arm 0.8, single reversal at 30,
life 60, reward fed as sensor input so storage competes fairly), n=10 per arm, `sep_cma_es`.
Genome dims reproduce the 043 feed exactly (fixed 51, nm_global 56, cfc 61, nm_pc 212; two arms
independently imply the [3,10,1] topology). Persist champions. Then REPLAY each champion on 16
held-out instances (flip_seeds disjoint from the evolver), decode the current good arm from the
substrate's own internal state (cfc: `get_internal_state/1`; nm: the drifting plastic weight
vector) with a logistic-regression decoder (standardise, GD, Mann-Whitney AUC). Two sentinels:
the static (fixed) arm's constant state must decode at chance, and a time-only control (decode
the label from trial index) must sit at chance or the split is time-confounded. Phase 3: replay
with the reversal shifted to 20/30/40 to separate genuine tracking (flat fitness) from an
internal clock (peaked at the trained time). Raw: `047-raw-mechanism-probe.txt`.

## A fatal decoder confound, caught at the CLAIM gate (recorded in the open)

The first n=10 decode fired FALSIFIED with cfc AUC 0.29, spread 0.50, and read as "the
substrates do NOT track equally". The CLAIM gate (adversarial review) refuted it from the code:
the 16 holdout instances alternate initial good arm by index, and the train/test split was on
index parity, so the decoder trained ONLY on GoodArm-0 episodes and tested ONLY on GoodArm-1.
Under the label rule that makes the training label identical to "trial >= 30" and INVERTS it on
test, so any time-drifting state decodes below chance (cfc's 0.29 is the inversion signature of
a STRONG decoder, not a weak one). Fix: a polarity-balanced split (instances 1-8 vs 9-16, both
carrying both polarities) plus the time-only control sentinel, transition trials excluded, and
the endogenous "beat the sensor" criterion dropped (`last_action` is the arm's own output). The
flawed run is preserved as `047-raw-run1-flawed-split.txt`. This is exactly the failure class of
the 038 and 040 retractions, caught BEFORE signing. It is why runners are now archived with
their engine pin (`faber-programmes`): the confound lived in the runner, not the feed.

## Results (n=10, corrected split; both sentinels pass)

Phase 1 replicates the 043 convergence: fixed 77.06 (sd 4.93; reactive win-stay-lose-shift is
available because reward is a sensor input), cfc 92.22 (0.67), nm_global 88.26 (1.22), nm_pc
91.13 (0.33).

Decode of the current good arm from internal state:

| arm | state AUC | sensor AUC (endogenous) | time-control |
|---|---|---|---|
| fixed (sentinel) | 0.500 | 0.798 | 0.500 |
| cfc | 0.965 (sd 0.013) | 0.908 | 0.500 |
| nm_global | 0.908 (sd 0.041) | 0.855 | 0.500 |
| nm_pc | 0.956 (sd 0.021) | 0.885 | 0.500 |

Adaptive spread 0.057 (< the pre-registered 0.10 equality bar); per-step disagreement 0.101
(< 0.15). Both sentinels at 0.500 certify the split is unconfounded.

Phase 3, shifted-reversal control (fitness at reversal 20/30/40, `clock_drop` = fit@30 minus
mean(fit@20,fit@40)): fixed -0.50, cfc +0.64, nm_global -0.70, nm_pc -0.12. Every adaptive arm
is as flat as (or flatter than) the reactive-only fixed arm.

## Finding 1 — 045's low-pass mechanism is REFUTED (pre-registered FALSIFIED branch)

The substrates do not carry equal-but-different per-step signals that integration averages away.
They decode the good arm EQUALLY (spread 0.057) and their per-step estimates AGREE (0.101 < the
0.15 "nothing to average" bar). There is essentially nothing for a low-pass filter to remove.
045's fitness facts stand; the mechanism it proposed does not.

## Finding 2 — the substrates genuinely TRACK, and it is not a clock

State decodes the good arm at 0.91-0.97 and beats the instantaneous endogenous sensor by
0.05-0.07 in all three arms, so each carries multi-step reward/action history, not a one-step
reflex. The shifted-reversal control rules out the clock alternative the DESIGN gate raised: with
the reversal fixed at 30 in training, a substrate could have evolved an internal timer, but
fitness is FLAT across reversal 20/30/40 and no more schedule-sensitive than the static arm that
has no state to time with. The substrates adapt to the reversal wherever it lands: evidence-based
tracking.

## What this replaces, and the scope

The corrected P3 lifetime picture: recurrent state, global plasticity, and per-synapse plasticity
independently converge on genuine, evidence-based, EQUALLY accurate good-arm tracking, and THAT
shared competence (not the averaging-away of hidden differences) is why they tie on fitness. The
045 "low-pass" metaphor is retired; 043-045's convergence results are unchanged.

NOT claimed (held back at the CLAIM gate): that the substrates compute the same thing by the same
internal geometry (equal decodability is not equal computation); and the DEPTH of the integration
window (a short reactive history vs long evidence accumulation) is not resolved here, only that it
exceeds one step and is not a clock. A k-step sensor-history-window decode would settle the depth
(cheap, replay-only) if a later insight needs it.

## Pointers

- Raw: `047-raw-mechanism-probe.txt` (corrected) and `047-raw-run1-flawed-split.txt` (the confound).
- Runner + engine pin + champions: `faber-programmes/.../exp047_lifetime_mechanism_probe*`.
- Builds on 035 (memory-arc mechanism), 042-045 (lifetime nulls + the low-pass claim now scoped).
