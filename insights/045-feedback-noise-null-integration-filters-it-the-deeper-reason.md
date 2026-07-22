# 045 — Feedback-sensor noise does NOT separate the lifetime mechanisms (a THIRD null), the prediction is REFUTED, and the reason is INTEGRATION. Across sensor noise sigma 0->1.0 all three arms hold at ~91-92: storage (cfc) drops only ~2 points (93.5->91.4, merely converging DOWN to the others), and clean-M vs noisy-M plasticity are indistinguishable. The lifetime task integrates reward over ~30 trials, and integration is a low-pass filter that averages out per-step noise on BOTH the observation and the modulator channels. This SHARPENS the synthesis: memory separates because it holds a signal INSTANTANEOUSLY (the substrate's noise-response is exposed); lifetime learning converges because it ESTIMATES BY INTEGRATION (averaging filters substrate differences away). The clean-neuromodulatory-channel engineering hypothesis is disconfirmed.

**Verdict:** signed negative (prediction refuted) + sharpened interpretation · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-045. **Uses:** `prob_reversal_bandit_sim`,
`evaluate_with_neuromod/4`, `sep_cma_es`. n=8.

## The question and the prediction (refuted)

043/044 nulled, but those stressors attacked the TASK. 045 attacks a mechanism-specific
SUBSTRATE: Gaussian noise on the [last_reward, last_action] feedback sensor corrupts a
recurrent state's ONLY reward channel, while reward-gated plasticity also receives reward
through a separate neuromodulator M. **Prediction:** storage (cfc) and noisy-M plasticity
degrade with sensor noise, clean-M plasticity holds -- locating plasticity's lifetime
advantage in the neuromodulatory channel. This is the direct 034 analogue.

## Method

Single-reversal probabilistic bandit (good arm 0.8, reversal at 30, life 60), sensor
noise sigma {0, 0.5, 1.0} on the two feedback channels (not the bias). Three arms: cfc
(storage, reward via noisy sensor only); nm_pc (per-connection plasticity, noisy
observation but CLEAN M); nm_pc_nm (same, but M noised by the same sigma). Fitness uses
the TRUE reward earned (noise affects observation/learning, not the accounting). n=8.
Raw: `045-raw-feedback-noise.txt`.

## Results (mean fitness)

| sigma | cfc | nm_pc (clean M) | nm_pc_nm (noisy M) |
|---|---|---|---|
| 0.0 | 93.5 | 92.1 | 91.7 |
| 0.5 | 91.6 | 91.4 | 90.8 |
| 1.0 | 91.4 | 91.4 | 91.6 |

At sigma=1.0 all three are ~91.4-91.6. cfc's "drop" is only it converging DOWN to the
plastic arms from a slightly higher noiseless start; clean-M and noisy-M are equal.

## Finding 1 — the prediction is refuted: no channel separates

Storage does NOT crack under feedback noise, and the clean-M advantage does NOT appear
(clean-M 91.4 = noisy-M 91.6 at sigma=1.0). The architectural hypothesis from the P3
synthesis (a dedicated clean neuromodulatory channel buys robustness) is DISCONFIRMED
in this regime. Both the observation channel and the modulator channel tolerate heavy
per-step noise equally.

## Finding 2 — the reason is INTEGRATION (the deeper mechanism)

The lifetime task requires accumulating reward evidence over ~30 trials before each
reversal. Integration is a low-pass filter: per-step noise of magnitude sigma on the
reward observation averages, over an N-trial window, to ~sigma/sqrt(N) on the integrated
estimate (for N~30, a >5x attenuation). The SAME averaging applies to the plastic weight,
which accumulates M-gated updates over trials, so noise on M is filtered too. Every
mechanism that integrates -- a recurrent state OR an accumulating weight -- inherits this
noise immunity. That is why neither the channel nor the mechanism separates.

## Finding 3 — the sharpened synthesis: instantaneous HOLD vs integrated ESTIMATE

This resolves WHY memory separates and lifetime learning does not:

- **Memory** holds a signal INSTANTANEOUSLY across a corridor. There is no averaging;
  the single cue must survive step by step, so the SUBSTRATE's noise-response is exposed
  directly. A decaying CfC state loses a noisy cue where a re-imprinted weight does not
  (035) -> the mechanisms separate, and the more expressive rule separates most (039).
- **Lifetime learning** ESTIMATES a reward mapping by integrating over many trials.
  Integration averages out per-step noise on any channel, so the substrate's
  noise-response is filtered away and the mechanisms converge (043/044/045).

The memory-vs-learning distinction is really INSTANTANEOUS-HOLD vs INTEGRATED-ESTIMATE.
The earlier synthesis principle (a stressor separates iff it attacks a mechanism-specific
substrate) is correct, and this is its mechanism: instantaneous holding exposes the
substrate; integrated estimation hides it behind an averaging filter.

## Consequence for the engineering guidance

The P3 synthesis's tentative recommendation -- architect a separate clean neuromodulatory
reward channel for noisy-reward lifetime learning -- is WITHDRAWN. 045 shows integration
already provides the noise immunity, so a clean channel adds nothing here. The correct
guidance: for reward-driven adaptation, rely on integration over trials (any mechanism
does this); do NOT over-engineer the reward path. Reserve mechanism care for
INSTANTANEOUS memory under noise, where the substrate is actually exposed.

## Caveat / next

- **n=8; per-step i.i.d. noise, one integration window (~30 trials).** A stressor that
  DEFEATS integration -- e.g. noise CORRELATED across trials, or a very short window
  (reversal every ~3 trials, so little to average) -- could in principle re-expose the
  substrate. 044's period-8 (7 reversals) already shortened the window and only produced
  a weak n.s. hint; a period-3 run is the last plausible place separation could hide. But
  the disciplined read after THREE nulls is that lifetime learning does not separate the
  mechanisms.
- **The lifetime sub-programme is conclusively a null:** three independent stressors,
  no separation, with the mechanism (integration) now understood. Time to close it.
- Feeds the P3 dashboard (a note on the frequency panel) and the synthesis.
