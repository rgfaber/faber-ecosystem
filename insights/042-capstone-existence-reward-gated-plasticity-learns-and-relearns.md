# 042 — Capstone I (existence) CONFIRMED: reward-gated (three-factor) plasticity genuinely LEARNS and RE-LEARNS within a lifetime. On the reversal bandit (hidden good arm, swapped mid-lifetime), both reward-modulated global ABCD and per-connection solve 8/8 at ~90-91 (near-optimal) vs a fixed net at exactly 50 (chance). The per-trial traces show textbook reversal learning: explore -> lock -> [reversal] -> re-lock (global re-adapts in ~3 trials, per-connection in ~1). Lifetime learning is NOT a wall. On this EASY 1-bit task global is CHEAPER than per-connection (dim 30, ~1k evals vs dim 90, ~2.2k) — expressiveness unneeded here is a cost, as everywhere in the programme.

**Verdict:** confirmed + positive (capstone existence) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-042. **Builds:** `evaluate_with_neuromod/4`
(the deferred third factor, faber-tweann b0440e6), `reversal_bandit_sim` (new scape).
`sep_cma_es`. n=8.

## The turn from memory to learning

Everything in 031-041 was memory WITHIN an episode (hold a cue you were shown). This
is the first LEARNING-across-episodes result: discover, from reward alone, a mapping
you were never shown, and re-discover it when it changes. The mechanism is the third
factor of the plasticity rule -- the neuromodulator M (reward) gating the weight
change, dW = M*Eta*(A*pre*post + B*pre + C*post + D) -- deferred since 036 because
plain memory did not need it. The capstone is where it becomes necessary.

## Method

Reversal bandit: two arms, the good one hidden and swapped at a reversal trial. The
sensor is a CONSTANT [1.0] -- the only signal is the reward stream, so this isolates
reward-GATED plasticity as the learning mechanism (a fixed net or a state with no
reward input cannot adapt). Fitness = mean lifetime reward over a fixed 8-instance
distribution (both good arms x reversal at {8,16,24,32}, lifetime 40), normalised so
chance = 50, perfect = 100. Arms: none (fixed), nm_global (reward-mod global ABCD),
nm_pc (reward-mod per-connection). Raw: `042-raw-reversal-existence.txt`.

## Results

| arm | solve | fitness | evals-to-solve | dim |
|---|---|---|---|---|
| none | 0/8 | 50.0 (chance) | never | 25 |
| nm_global | **8/8** | 90.0-91.3 | 800-1500 | 30 |
| nm_pc | **8/8** | 90.0-91.3 | 1600-3000 | 90 |

## Finding 1 — existence CONFIRMED; lifetime learning is tractable

Both reward-modulated arms solve 8/8 at ~90-91 (near the ~95 optimum after
unavoidable exploration losses), where the fixed net sits at exactly 50. Reward-gated
plasticity is a working lifetime-learning mechanism on this substrate. The pessimistic
possibility (that lifetime learning is a wall at our budget, cf the optimizer wall at
024) is REFUTED: it solves in ~1000 evaluations.

## Finding 2 — the traces show textbook reversal learning

Best-genome replay on the (good=0, reversal=20) instance, one char per trial
(+ correct, - wrong):

    none       --------------------++++++++++++++++++++
    nm_global  -+++++++++++++++++++---+++++++++++++++++
    nm_pc      ++++++++++++++++++++-+++++++++++++++++++

The fixed net picks one arm forever (wrong for 20, then right for 20 after the swap
happens to align -- exactly chance). Both plastic agents LOCK onto the good arm and,
at the reversal, DETECT the drop in reward and RE-LOCK: global loses ~3 trials
re-adapting, per-connection just 1. This is the explore -> exploit -> reverse ->
re-exploit signature of associative reversal learning, evolved, not designed.

## Finding 3 — on an EASY task, expressiveness is a COST (again)

Global (5 rule params, dim 30) solves in ~1000 evals; per-connection (240+1 params,
dim 90) needs ~2200. A 1-bit reversal does not need per-synapse specialisation, so
per-connection's extra parameters only enlarge the search. This is the SAME pattern
seen throughout: unexploited capability is a cost (013/015/017 memory; 036 Oja-vs-
ABCD; 041 the search). It sets the prediction for capstone II: per-connection should
pull ahead only when the lifetime task gets HARD (probabilistic / multi-arm / noisy),
mirroring the memory arc where per-connection separated only under capacity+noise
(039).

## What this establishes for the programme

The storage-vs-learning dichotomy now has BOTH regimes instrumented:
- memory (031-041): hold a cue; learning (plasticity) beats storage on robustness.
- learning (042+): adapt from reward; reward-gated plasticity works, near-optimally.

The next question (capstone II) is the fair three-way contest -- storage (a recurrent
state given reward as INPUT, RL^2-style) vs global vs per-connection reward-modulated
plasticity -- on a PROBABILISTIC reversal that excludes reactive win-stay-lose-shift.

## Caveat / next

- **n=8, deterministic reward, 1-bit, one lifetime length.** Deterministic reversal is
  also reactively solvable IF reward is an input; here it is not (constant sensor), so
  the result is clean for PLASTICITY, but it does not yet compare against storage.
- **EXP-043 (capstone II):** probabilistic reward (e.g. 0.8/0.2), reward+last-action as
  input to ALL arms, add a storage (cfc) arm and a per-LAYER arm (absorbing the old
  EXP-042 dimensions-vs-representation question). Does the memory-arc thesis (learning
  > storage on robustness; per-connection decisive when hard; via evolvability)
  TRANSFER to lifetime learning?
- Feeds the P3 dashboard (a capstone / reversal-learning panel).
