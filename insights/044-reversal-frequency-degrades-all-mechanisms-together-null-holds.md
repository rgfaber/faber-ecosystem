# 044 — Reversal FREQUENCY is a real stressor but does NOT separate the mechanisms: from 1 to 7 reversals per lifetime all three (storage, global, per-connection) degrade together (~4 points, to ~86-88), with only a weak, non-significant directional hint that per-connection is the most robust (88.3, tightest) and global the least (86.2, one run at 77.8). Nowhere near the memory arc's decisive 8/10-vs-1/10 separation (039). Two independent lifetime stressors now (043 probabilistic reward, 044 reversal frequency) fail to reproduce the memory-arc separations. The storage-vs-learning-vs-expressiveness distinctions are a MEMORY-under-stress phenomenon, not a lifetime-learning one.

**Verdict:** signed negative (no clean separation) + weak directional finding · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-044. **Uses:** `prob_reversal_bandit_sim`
(periodic mode, new), `evaluate_with_neuromod/4`, `sep_cma_es`. n=8.

## The question

043 tied all mechanisms on a single easy reversal. 042's traces hinted per-connection
re-locks in ~1 trial vs global's ~3, so when reversals are FREQUENT the re-adaptation
cost should dominate and faster re-adapters should separate -- the lifetime echo of how
capacity+noise separated the memory mechanisms (039). Sweep reversal frequency and see.

## Method

Probabilistic reversal bandit (good arm 0.8, reward+action fed as input), reversal
PERIOD swept {30, 15, 8} = {1, 3, 7} reversals over a 60-trial life. Arms cfc (storage),
nm_global, nm_pc (reward-mod plasticity). Fitness normalised (chance 50, optimal 100),
n=8. Raw: `044-raw-reversal-frequency.txt`.

## Results (mean fitness)

| period (reversals/life) | cfc | nm_global | nm_pc |
|---|---|---|---|
| 30 (1) | 91.6 | 90.4 | 91.7 |
| 15 (3) | 90.7 | 91.0 | 90.6 |
| 8 (7) | 87.4 | 86.2 | 88.3 |
| drop 30->8 | 4.2 | 4.2 | 3.5 |

Per-run at period 8: cfc tight (86.6-89.8), nm_pc moderate (86.1-90.7), nm_global
WIDE (77.8-90.3, one run cratering).

## Finding 1 — frequency degrades everyone, roughly equally

More reversals mean more re-adaptation, and every mechanism pays ~4 points from 1 to 7
reversals. Reversal frequency is a genuine difficulty axis (unlike the reward SNR at
0.8, which all mechanisms shrugged off in 043). But the degradation is mechanism-
NEUTRAL in magnitude: no arm falls off a cliff while another holds.

## Finding 2 — a weak directional hint, not a separation

At the stressed end (period 8) the ordering is nm_pc (88.3) > cfc (87.4) > nm_global
(86.2), and per-connection is also the most RELIABLE (tightest spread) while global is
the least (77.8-90.3). This is the same DIRECTION as the memory arc (per-connection
most robust, the simple rule least). But the gaps are ~2 points inside a per-run spread
several times larger, and would not survive a significance test at n=8. It is a hint,
not a result -- a far cry from 039's 8/10-vs-1/10.

## Finding 3 — the lifetime null is now robust (two stressors)

043 (fair contest, reward SNR) and 044 (reversal frequency) are two independent
attempts to separate the mechanisms in the lifetime-learning regime; both fail to
produce a decisive gap. The memory arc separated dramatically under its analogous
stressors (noise 034, capacity+noise 039). The contrast is the finding: the storage-
vs-learning-vs-expressiveness distinctions are a property of HOLDING information under
stress (memory), not of LEARNING from reward. Where reward is available to all
mechanisms, they converge -- because a recurrent state, a global rule, and a
per-connection rule can all implement adequate reversal learning, and the task does not
punish the differences between them.

## Why the asymmetry (the interpretation)

In memory, the stressor attacks the STORED signal directly: noise corrupts a decaying
CfC trace but not a re-imprinted weight (035), so the mechanisms diverge. In lifetime
learning, the stressor (frequency) attacks the TASK, not a mechanism-specific
substrate: every mechanism must simply re-adapt more often, and they all can. There is
no mechanism-specific fragility for the stressor to exploit. That is why memory
separates and learning does not.

## Caveat / next

- **n=8; the period-8 hint (pc most robust) is real in direction but n.s.** A larger n
  (>=24) at period 8, or a harsher frequency (period 4), could sharpen or dissolve it;
  the disciplined read now is "no separation".
- **One untested lever remains -- the direct 034 analogue:** Gaussian NOISE on the
  [last_reward, last_action] feedback SENSOR, which corrupts the RL^2 state's evidence
  specifically. That is the most likely place storage would finally crack before
  reward-gated plasticity (whose M channel is separate). If even that nulls, the
  lifetime-learning equivalence is conclusive. Recorded as the open EXP-045.
- **The programme is ripe for synthesis.** 031-044 is a complete two-regime arc;
  writing it up (SYNTHESIS_P3) is the natural next deliverable.
- Feeds the P3 dashboard (a frequency-sweep line added to the contest section).
