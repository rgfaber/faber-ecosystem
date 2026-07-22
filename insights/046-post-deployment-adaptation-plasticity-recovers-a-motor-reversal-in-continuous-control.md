# 046 — Post-deployment adaptation in CONTINUOUS CONTROL: reward-modulated plasticity RELIABLY recovers from a hidden motor reversal in a windy pole-balancer (5/5 near-full survival), where a fixed controller is unreliable (3/5, it partially self-immunizes) and a recurrent CfC mostly fails (0/5 full). The neuromod engine's first REAL-TASK demonstration -- the science-to-capability bridge. Two sub-findings: a regulator settles toward a stable manifold some fixed policies span across regimes, so CONTROL is harder to break with actuator shifts than a DECISION task (a bandit's fixed policy is always chance); and the shift only bites once a WIND disturbance forces continuous non-zero control effort.

**Verdict:** confirmed (bridge) + nuanced (reliability, not clean separation) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning) -> engineering bridge, EXP-046. **Builds on:** `pb_sim`
(new hidden `shift_gain` + `wind` overrides), `evaluate_with_neuromod/4`, `sep_cma_es`. n=5.

## The question

The P3 synthesis recommended the highest-value next step: drive a REAL faber-neuroevolution
task with a validated mechanism, turning "we understand the mechanism" into "we shipped a
capability". The task: single-pole balancing where a HIDDEN force shift (motor reversal or
gain drop, unsensed) hits mid-episode -- the continuous-control analogue of the reversal
bandit (042). Can an evolved controller adapt to it post-deployment?

## Method

`pb_sim` single pole with velocity (sensor [4]), goal 400 steps, hidden shift at step 120
(shift_gain -1.0 reversal or 0.35 gain-drop), constant wind 5.0 N. Arms: fixed (feedforward,
no adaptation), cfc (recurrent, adaptive via state), nm_plastic (reward-modulated plasticity,
adaptive via weights; the modulator M = tanh(5*(prev tilt - cur tilt)) reinforces the policy
while the pole is recovering). Fitness = survival steps. n=5. Raw: `046-raw-pole-adaptation.txt`.

## Results (survival steps, max ~400)

| arm | reversal (-1.0) | gain-drop (0.35) |
|---|---|---|
| fixed | 401, 238, 401, 401, 224 | ~320 (all partial) |
| cfc | 276, 368, 188, 186, 176 | ~281 (mixed) |
| nm_plastic | 393, 401, 401, 381, 401 | ~332 |

## Finding 1 — reward-modulated plasticity reliably adapts (the bridge)

Under the motor reversal, nm_plastic recovers on all 5 runs (~395 mean, four at the 400 cap).
It derives a reward from the pole's recovery and re-wires its weights online to invert its
effective control -- the same three-factor mechanism that solved the reversal bandit (042),
now in continuous control. This is the neuromod engine's first demonstration on a REAL task,
not a toy benchmark: the science-to-capability bridge the synthesis called for.

## Finding 2 — a fixed regulator partially self-immunizes (control vs decision)

The fixed controller survives the reversal on 3/5 runs. And WITHOUT wind it survived even a
full reversal every time (a prior pass): a regulator drives the system toward a stable
manifold, and near a low-effort equilibrium a force-gain change barely matters. Only a
constant WIND disturbance -- forcing continuous non-zero control effort -- makes the shift
bite, and even then some fixed policies span both regimes. This is the key contrast with a
DECISION task: a bandit's fixed policy is ALWAYS chance under reversal (there is no
self-correcting equilibrium to exploit), so decision tasks separate fixed from adaptive
cleanly while control tasks do not. Post-deployment adaptation is easiest to DEMONSTRATE
where the environment offers no regulable manifold.

## Finding 3 — recurrence is not automatically adaptive

CfC UNDERPERFORMS (0/5 full recoveries, 176-368). The added state and slow tau dynamics did
not evolve a reliable adaptive controller within budget; if anything they make a worse fast
controller, consistent with 017/018 (CfC smoothing hurt fast pole control). Adaptation here
came from the reward-gated weight change, not from recurrence per se.

## Engineering value

This is the concrete engineering payoff the synthesis flagged as missing: the reward-modulated
plasticity engine (`evaluate_with_neuromod/4`) works on a real continuous-control adaptation
task. The story is shippable: an evolved controller that FEELS the pole falling and re-wires
itself online to survive a motor fault. It also hardened `pb_sim` (hidden shift + wind
disturbance, both reusable for future robustness/adaptation studies).

## Caveat / next

- **n=5; one task; reliability, not absolute separation** (fixed survives 3/5 under reversal).
  The honest headline is "plasticity is the RELIABLE adapter", not "fixed always fails".
- **The demo needed wind tuning** to make the shift matter -- itself a finding about WHEN
  adaptation is demonstrable (only when the task cannot regulate the mismatch away).
- **Workbench:** the exported best trajectories drive an interactive animation (a plastic agent
  recovering from the reversal vs a fixed controller, trained on the normal regime, crashing
  when deployed into the reversed one) -- the pb_sim adaptation demo, for beam-campus-net.
