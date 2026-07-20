# 007 — Deterministic environments should declare themselves; the corrected baseline

**Verdict:** confirmed · **Date:** 2026-07-20
**Supersedes the figure in:** [006](006-evaluations-to-solve-is-now-measurable.md)

## Claim

The 10x inflation identified in insight 006 was exactly a multiplier artifact,
not a difference in search behaviour. Correcting it requires a design decision
rather than a changed constant, and the decision belongs to the environment.

## The correction

`evaluations_per_individual` defaulted to 10, meaning each individual was
evaluated ten times and its fitness averaged. That is correct for a stochastic
environment, where averaging suppresses episode noise, and pure waste for a
deterministic one, where all ten runs recompute an identical result.

Rather than hardcode 1 for XOR, `agent_environment` gained an **optional
`is_deterministic/0` callback**. The engine derives the evaluation count from
it; an explicit caller setting always wins.

Default when the callback is absent is `false`, deliberately. Averaging a
deterministic environment merely wastes work; failing to average a stochastic
one produces noisy fitness and misleads selection. The safe direction is the
expensive one.

This has no analogue in DXNN2, which has no concept of a deterministic scape.

## Evidence the diagnosis was exact

Same five seeds, before and after:

| seed | before (epi=10) | after (epi=1) | ratio |
|---|---|---|---|
| 1 | 5000 | 500 | 10.0 |
| 2 | 6500 | 650 | 10.0 |
| 3 | 5500 | 550 | 10.0 |
| 4 | 5500 | 550 | 10.0 |
| 5 | 7000 | 700 | 10.0 |

Exactly 10.0 in every case. Identical seeds produced identical evolutionary
trajectories and identical champions; only the counter differed. Had the
environment been even slightly stochastic, the ratios would have scattered.

## The corrected control

**XOR, fixed topology `{2,[3],1}`, population 50, one evaluation per
individual: median 550 evaluations, range 500-700, n=5 seeds.**

This is the baseline Phase 3 gets measured against. Reproducible per seed.

## What changes

- The Phase 3 comparison (insight 004) holds seed, population and evaluation
  count constant, so any difference is attributable to topology evolution.
- Every future environment declares its determinism. Pole balancing: true.
  Elia battery dispatch against a recorded price trace: true. A foraging world
  with random placement: false.

## Caveat, unchanged from 006

Five seeds, one environment, one topology. XOR is a smoke test. Nothing about
faber's competitiveness against published systems follows from this number, and
it should not appear beside Table 14.1 figures, which are pole balancing.
