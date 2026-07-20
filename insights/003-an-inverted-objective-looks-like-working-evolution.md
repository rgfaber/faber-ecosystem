# 003 — An inverted objective is indistinguishable from working evolution

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

An evolutionary run optimising the wrong objective looks, from the outside,
exactly like an evolutionary run optimising the right one. Fitness rises,
generations advance, a champion is returned. Without a control you cannot tell
the difference.

## Evidence

The first XOR run reported healthy-looking training:

```
best_fitness_ever = 21.0
last_gen_best     = 21.0
last_gen_avg      = 20.999999999999996
```

Re-evaluating the returned champion gave fitness **0.577**, `correct = 1`,
`solved = false`.

Comparing stored fitness against recomputed error across the population:

```
stored=20.645036279654626   sse=11.645036279654624   → diff exactly 9.0
stored=20.576386007609074   sse=11.576386007609072   → diff exactly 9.0
stored=20.55550280635803    sse=11.555502806358032   → diff exactly 9.0
```

`9 = correct(1) + presented(4) + cases(4)`. The engine was computing
**fitness = sum of every numeric value in the metrics map**, which for XOR is
`sse + 9`. The objective *increased with squared error*.

Chain: `bridge_evaluator:evaluate/2` computed the correct fitness from the
domain evaluator, then `neuroevolution_server:handle_evaluation_complete/2`
recomputed it by calling `EvaluatorModule:calculate_fitness/1` on the metrics
alone, reaching `bridge_evaluator:default_fitness/1`, which sums numbers. That
overwrote the domain fitness.

`calculate_fitness/1` receives only `Metrics` and so structurally cannot reach
the bridge or the domain evaluator.

## What caught it

Not the passing tests. Two things:

1. `last_gen_avg ≈ last_gen_best` — the whole population scoring identically is
   the signature of a degenerate landscape, and it is visible in the stats
   without any special instrumentation.
2. A **control**: `oracle_solves_test` and `constant_output_does_not_solve_test`
   in `test/xor_tests.erl`. A hand-written perfect network must score as solved
   and a constant network must not. Without those two tests, a broken
   environment that scored everything perfectly would have made the evolution
   test pass for the wrong reason.

## What changes

- Every environment gets an oracle test and a degenerate-baseline test before
  any evolution test is trusted. Cheap, and it is the only thing standing
  between "it works" and "it appears to work".
- Watch `avg ≈ best` as a first-order alarm for a flat or inverted landscape.
- Fitness stashed under a reserved metrics key so it survives the engine's
  recomputation step.
