# 005 — Seeding one process makes an evolutionary run replayable

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

Reproducible evolutionary runs were expected to require threading a seed
through every RNG consumer. They do not. Seeding the server process alone is
sufficient, because every operation that draws randomness during evolution
executes in that process.

## Evidence

Randomness is widespread: 45 `rand:uniform` and 10 `rand:normal` calls across
20 modules, covering genome creation, mutation, crossover, selection,
speciation and all five strategies. Before this change, `grep -rn "rand:seed"
src/` returned exactly one hit, inside a doc comment.

The relevant fact is not how many call sites there are but **where they
execute**. Strategy ticks and the genetic operators are invoked from the
server's `gen_server` callbacks, so they share one process dictionary and one
RNG state.

Added `rng_seed` to `#neuro_config{}` and a single `rand:seed(exsss, ...)` in
`neuroevolution_server:init/1`, before any draw.

Measured (`test/xor_seed_tests.erl`, 15 generations, population 20):

| Comparison | Result |
|---|---|
| seed 42 vs seed 42 | identical champion fitness and sse |
| seed 42 vs seed 99 | divergent |

Both assertions pass. Total suite 953, up from 951.

## Boundary — stated so it is not overclaimed

This covers evolutionary operations **only**. Fitness evaluation runs in
processes spawned by `evaluate_population_parallel/5`, each with independent
RNG state, and is not covered.

That is harmless for deterministic environments (XOR now, pole balancing next)
and **will not hold** for stochastic ones. A stochastic environment needs
per-episode seeding derived from the run seed, and any claim of reproducibility
there is unsupported until that exists.

## What changes

- Every benchmark run records its seed. A result without one is an anecdote.
- Bisecting a regression across evolution changes is now possible.
- The Phase 3 comparison against the fixed-topology control (insight 004) can
  hold the seed constant, so the difference measured is topology evolution and
  not RNG luck.
