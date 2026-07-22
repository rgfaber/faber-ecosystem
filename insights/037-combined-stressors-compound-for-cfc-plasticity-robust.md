# 037 — Combined stressors COMPOUND for CfC: a longer memory AND sensor noise together deepen its failure (0/8 at sigma=0.5 for both delays; fitness FALLS from 82.4 at delay-8 to 78.0 at delay-16). Plasticity (ABCD) solves the ENTIRE grid 8/8, including delay-16 + sigma=0.5. The two stressors attack the same thin decaying CfC margin (035); the stable plastic imprint survives both.

**Verdict:** confirmed + finding · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-037. **Uses:** `evaluate_with_plasticity/3`,
`sep_cma_es`, `tmaze_sim`. Run in parallel with 036 via a git worktree.

## The question

033 (delay) and 034 (noise) each ranked plastic > CfC on one stressor. Do they
COMPOUND when applied together? Prediction (from the 035 mechanism): both stressors
attack the same thin, decaying CfC margin, so together they should hurt CfC more
than either alone; plasticity's stable imprint should survive the combination.

## Method

Grid of delay {8,16} x sensor noise {0.3,0.5}, arms plastic (ABCD) / cfc,
sep-CMA-ES, n=8, solve 90/100. Raw: `037-raw-combined.txt`.

## Results (mean over 8 runs)

| arm | delay 8, sigma 0.3 | delay 8, sigma 0.5 | delay 16, sigma 0.3 | delay 16, sigma 0.5 |
|---|---|---|---|---|
| plastic | 8/8 (93.8, 1.4k) | 8/8 (91.2, 4.1k) | 8/8 (93.5, 1.6k) | **8/8** (90.6, 4.4k) |
| cfc | 8/8 (90.9, 9.1k) | **0/8** (82.4) | 8/8 (90.4, 15.3k) | **0/8** (78.0) |

## Finding 1 — plasticity survives the full combination

Plastic solves all four cells 8/8, including the hardest (delay 16 AND sigma=0.5,
fitness 90.6). Its evals-to-solve barely move with delay (1.4k->1.6k at sigma=0.3,
4.1k->4.4k at sigma=0.5). The combination does not threaten the plastic imprint.

## Finding 2 — the stressors compound for CfC

CfC fails at sigma=0.5 at BOTH delays (0/8), and the longer corridor makes the
failure DEEPER: fitness falls from 82.4 (delay 8) to 78.0 (delay 16). Where CfC still
survives (sigma=0.3), the longer delay drives its cost up (9.1k -> 15.3k evals). So
delay and noise are not independent for CfC -- a longer corridor lowers the ceiling
the noise then has to beat.

## Finding 3 — 035 explains the compounding exactly

The mechanism (035): the CfC state decays across the corridor to a thin margin, and
noise destroys thin margins. A LONGER corridor means MORE decay -> an even thinner
margin -> even less noise tolerance. So the two stressors attack the SAME quantity
(the decayed cue signal at the junction): delay shrinks it, noise corrupts it, and
together they shrink-then-corrupt it past recovery. Plasticity has no decaying margin
(035: the imprint holds ~100% regardless of corridor length), so neither stressor,
alone or combined, finds anything to attack.

## The P3 picture (031-037)

- 031: plastic = CfC on the easy task.
- 032/033: plastic scales more gracefully with the memory HORIZON (cost gap).
- 034: plastic survives NOISE where CfC fails (capability gap).
- 035: WHY -- CfC state is leaky (decays to a thin margin), plastic imprint is stable.
- 036: ABCD is the workhorse; the simpler Oja rule is LESS robust (expressiveness helps).
- 037: the two stressors COMPOUND for CfC; plastic is robust to the combination.

**Thesis, now well-supported:** an evolved local plasticity rule is a more robust
memory mechanism than a fixed continuous-time state -- on horizon, on noise, and on
both together -- because it holds a stable imprint rather than a decaying trace.

## Caveat / next

- **n=8.** The 0/8-vs-8/8 gaps are large and robust; the exact fitnesses are noisy.
- **Next (the real "richer rule" test, from 036):** per-connection {A,B,C,D} or a
  reward-modulated term -- MORE expressive than ABCD -- to see if the lead widens.
  Then the capstone: learning ACROSS a task distribution (true learning-to-learn).
- Feeds the P3 dashboard.
