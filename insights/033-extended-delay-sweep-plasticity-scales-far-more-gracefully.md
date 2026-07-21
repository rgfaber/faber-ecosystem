# 033 — Extended T-maze delay sweep (32/64) confirms and widens 032: both plasticity and CfC solve to delay 64 (neither breaks), but CfC's evolution cost grows ~8x across delay 2->64 while plasticity's grows only ~2.6x. At delay 64 CfC needs 3.3x more evals. Memory-by-learning scales far more gracefully than memory-by-storage.

**Verdict:** confirmed + strengthened finding · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-033a. Extends 032. **Uses:**
`evaluate_with_plasticity/3`, `sep_cma_es`, `tmaze_sim`. Run in parallel with the
noise sweep (034) via a git worktree.

## The question

032 (delay 4/8/16) found plasticity horizon-invariant and CfC's cost growing, but
both still solved at delay 16 -- the divergence was in evals, not solve rate. Does
CfC's cost go vertical (or CfC fail) at longer horizons while plasticity stays flat?

## Method

Same two arms (plastic / cfc), net `2 -> [12] -> 1`, sep-CMA-ES (lambda 100, 300
gens cap, solve 90/100), n=8, delays 32 and 64. Raw: `033-raw-extended-delay.txt`.
Combined with 031 (delay 2) and 032 (4/8/16) for the full curve.

## Results — evals-to-solve vs delay (mean; both arms solve every run)

| delay | 2 | 4 | 8 | 16 | 32 | 64 |
|---|---|---|---|---|---|---|
| plastic | 120 | 120 | 100 | 160 | 200 | 312 |
| cfc | 130 | 170 | 390 | 460 | 750 | 1038 |
| plastic fitness | 100 | 100 | 100 | 100 | 100.0 | 100.0 |
| cfc fitness | ~99 | 98.8 | 99.2 | 98.2 | 99.0 | 99.5 |

## Finding 1 — neither breaks, even at delay 64

Both mechanisms solve 8/8 at delay 32 and delay 64. Holding a cue across 64
corridor steps is still within reach of both a plastic rule and a CfC state. So the
answer to "does CfC go vertical" is no -- it keeps solving, just at rising cost.

## Finding 2 — the cost gap widens: plastic ~2.6x, CfC ~8x across the sweep

Plastic's evals-to-solve grow gently (120 -> 312, ~2.6x over delay 2 -> 64); CfC's
grow steeply and monotonically (130 -> 1038, ~8x, and accelerating: 460 -> 750 ->
1038 over the last three points). At delay 64 CfC needs **3.3x** the evals plastic
does, and the ratio has grown from ~1 at delay 2 to ~3.3 at delay 64. Plasticity is
not perfectly flat at extreme delay, but it scales far more gracefully.

## Finding 3 — plastic stays perfect; CfC sits just under

Plastic reaches fitness 100.0 at every delay; CfC hovers at ~98-99.5. The plastic
solutions are exact; the CfC ones are near-exact. Consistent across the whole sweep.

## Prior art

Replication + a comparative note (see 032). That an evolved local rule scales more
gracefully with the memory horizon than a fixed continuous-time state is a
substrate-specific measurement on this benchmark, robust across a wide sweep
(delay 2-64) but still modest in absolute terms (both solve in <11 generations).

## Caveat / next

- **n=8; both solve.** The ranking is a cost gap, not a capability gap. A net-size
  or budget change could shift the absolute numbers; the SHAPE (CfC steeper than
  plastic) is the robust claim.
- **Mechanism still tentative** (032): CfC state must be tuned to persist across more
  steps (narrower target as delay grows); the plastic rule re-imprints each trial.
  Not proven.
- **Next:** the noise sweep (034, run in parallel) tests the OTHER stressor -- does
  sensor noise hurt the decaying CfC state more than the re-imprinted plastic weight?
- Extended-delay curve folded into the P3 delay-sweep dashboard.
