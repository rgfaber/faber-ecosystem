# 006 — Evaluations-to-solve is now measurable, and the first number is inflated 10x

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

The metric every neuroevolution paper reports — evaluations required to solve —
was previously unmeasurable in this codebase. It is now recorded. The first
measurement is valid but must not be compared to literature as-is, because the
default configuration evaluates each individual ten times on a deterministic
task.

## What was added

- Optional `is_solved/1` callback on the `agent_evaluator` behaviour. Fitness
  is a continuous search signal and a poor success criterion; this is the
  discrete one.
- `bridge_evaluator` stashes the verdict; `neuroevolution_server` latches
  `total_evaluations` at the **first** solving individual and exposes it as
  `evaluations_to_solve` in stats.
- `stop_on_solved` config, default false so existing behaviour is unchanged.

## First measurement

XOR, fixed topology `{2,[3],1}`, population 50, `stop_on_solved`:

| seed | evaluations_to_solve |
|---|---|
| 1 | 5000 |
| 2 | 6500 |
| 3 | 5500 |
| 4 | 5500 |
| 5 | 7000 |

Median 5500, range 5000-7000. Reproducible: the same seed yields the same
count across runs (`xor_solve_metric_tests`).

## The inflation, stated before anyone quotes the number

`evaluations_per_individual` defaults to **10**. XOR is fully deterministic:
four fixed cases, no stochasticity anywhere in the episode. So nine of every
ten evaluations recompute an identical result.

500 evaluations per generation for a population of 50 means the median 5500
represents about **11 generations**. The honest comparable figure is closer to
**550** distinct evaluations.

This does not invalidate the measurement, which is internally consistent and
reproducible. It means the number is only comparable to other runs under the
same setting, and **must not be placed beside published figures** until
`evaluations_per_individual` is set to 1 for deterministic environments.

## What changes

- Set `evaluations_per_individual => 1` for deterministic environments. Repeat
  evaluation exists for stochastic ones, where averaging over episodes is the
  point.
- Consider deriving the default from the environment rather than fixing it at
  10, since a deterministic environment can declare itself as such.
- The Phase 3 control comparison (insight 004) must hold this setting constant
  along with the seed, or the difference measured will be configuration rather
  than topology evolution.

## Caveat on scope

Single-environment, single-topology, five seeds. XOR is a smoke test, not a
benchmark: everything in Sher's Table 14.1 solves problems considerably harder.
No claim about faber's competitiveness follows from this number.
