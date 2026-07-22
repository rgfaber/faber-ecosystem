# 043 — Capstone II: the memory-arc thesis does NOT transfer to lifetime learning. On the probabilistic reversal bandit (reward fed as input, so storage competes fairly), all four mechanisms tie: storage (CfC 92.4), global (92.9), per-layer (92.9) and per-connection (93.2) plasticity all solve 8/8 at ~92-93, gaps far below the within-arm spread. "Learning beats storage" was about HOLDING information under stress (memory), not reward-driven adaptation: when storage has reward as INPUT it implements the learning (RL^2-style) as well as reward-gated plasticity. Expressiveness is a COST again (per-connection needs ~2x the evals). Like 038, an easy task ties the mechanisms; discrimination needs STRESS.

**Verdict:** signed negative (thesis does not transfer as-is) + finding + convergence · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-043. **Uses:** `prob_reversal_bandit_sim` (new),
`evaluate_with_neuromod/4`, `sep_cma_es`. n=8.

## The question

042 showed reward-gated plasticity LEARNS a reversal (existence). The programme's
central thesis, built on the MEMORY arc (031-041), is: learning (plasticity) beats
storage (recurrent state) on robustness, and per-synapse expressiveness is decisive
under stress. Does that thesis TRANSFER to genuine lifetime learning? Set up the fair
contest and find out.

## Method

Probabilistic reversal bandit: good arm pays 0.8 / bad 0.2, swapped mid-life, with the
previous reward and action fed back as INPUTS to all arms (so a recurrent state can
compete, RL^2-style; the noise defeats reactive win-stay-lose-shift). Four arms: cfc
(storage), nm_global / nm_per_layer / nm_pc (reward-modulated plasticity, increasing
expressiveness). Fitness = mean reward over a fixed 12-instance distribution
(2 arms x 2 reversal points x 3 flip seeds), normalised so chance = 50, optimal = 100;
solve goal 92. n=8. Raw: `043-raw-fair-contest.txt`.

## Results

| arm | mean fitness | best | evals-to-solve (median) | dim |
|---|---|---|---|---|
| cfc (storage) | 92.39 | 93.3 | ~1250 | 61 |
| nm_global | 92.94 | 97.0 | ~1550 | 56 |
| nm_per_layer | 92.85 | 93.8 | ~1350 | 60 |
| nm_pc | 93.17 | 95.8 | ~2650 | 212 |

All arms solve 8/8. The between-arm mean gap is < 0.8; the within-arm spread is
~1-5 (nm_global runs 92.1 to 97.0). No mechanism separates on outcome.

## Finding 1 — the thesis does NOT transfer: storage competes fully

In the memory arc, storage (CfC) FAILED under noise (034: 0/8) and per-connection
plasticity won. Here storage TIES the best plasticity (92.4 vs 93.2), because the key
difference is access to reward: given the reward as an INPUT, a recurrent state can
implement the reversal-learning algorithm in its dynamics (this is exactly RL^2 /
meta-RL with an RNN). "Learning beats storage" was a statement about HOLDING given
information under stress, not about reward-driven adaptation. In the lifetime-learning
regime with reward feedback, storage and learning are equivalent on this task.

## Finding 2 — expressiveness is a COST, not a benefit (again)

Per-connection (dim 212) needs ~2x the evaluations of global/storage (dim ~60) to
reach the same solve, and its mean is not higher (93.2 vs 92.9). Per-LAYER sits WITH
global (92.85 vs 92.94), not between global and per-connection. So on this task the
old EXP-042 dimensions-vs-representation question is moot: there is no separation to
explain, and the extra parameters only enlarge the search. This is the programme's
recurring shape -- unexploited expressiveness is a cost (013/015/017/036/041/042).

## Finding 3 — an easy task ties the mechanisms (cf 038)

This exactly parallels 038: memory CAPACITY alone did not separate storage from
learning (all ~7/8); it took capacity + NOISE (039) to fan them out. Likewise a single
2-arm reversal with reward feedback is learnable by any mechanism with memory of the
reward stream, so it ties. The discriminating regime for lifetime learning must be
STRESSED -- more arms, faster/again reversals, partial observability, or sensor noise
on the feedback -- the analogue of adding noise to the memory task.

## What the programme now knows

- **Memory (031-041):** learning (plasticity) beats storage on robustness; per-synapse
  expressiveness is decisive under capacity+noise, via evolvability.
- **Lifetime learning (042-043):** reward-gated plasticity works (near-optimal, 042);
  but on an easy reversal, storage and all plasticity variants CONVERGE (043). The
  storage-vs-learning gap is a MEMORY-under-stress phenomenon, not a universal law.

The honest synthesis: the mechanisms differ where information must be HELD against a
stressor; they converge where it must be LEARNED from reward and the task is easy.

## Caveat / next

- **Solve goal 92 censors the ceiling:** every run stops at ~92, so the higher best
  values (nm_global 97) are overshoot, not a reliable ranking. A no-goal run would
  show ceilings; the MEANS (all tied ~92-93) are the reliable signal.
- **EXP-044 (the stressed lifetime task):** add the analogue of 039's noise -- e.g.
  sensor noise on the reward/action feedback, or more arms / repeated reversals -- to
  find the regime (if any) where a mechanism separates for lifetime learning, mirroring
  how capacity+noise separated the memory mechanisms.
- **Standing side-check:** confirm any capstone headline with plain (mu,lambda)-ES.
- Feeds the P3 dashboard (capstone contest panel).
