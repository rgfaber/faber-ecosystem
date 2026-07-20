# 011 — Lifecycle and tuning fixed; evolution improves monotonically; topology search is the next wall

**Verdict:** confirmed (positive + precise next blocker) · **Date:** 2026-07-20
**Advances:** roadmap 2b (insight 010)

## Claim

The multi-generation blocker of insight 010 was not one bug but five coupled
ones across the genotype lifecycle and the memetic tuner. With them fixed,
evolution runs stably for 150+ generations and best fitness climbs
monotonically. It does not yet solve XOR outright, and the reason is now
precisely located: topology search, not the lifecycle.

## The five bugs, all found by bisecting from a running population

1. **Spliced neurons shared layer 0.** `genotype:generate_id(neuron)` hardcodes
   layer 0, so `add_neuron` never gave a spliced neuron a distinct layer.
   Same-layer connections became self/feedback loops the single-pass evaluator
   waits on forever. Fixed: splice at the midpoint layer between endpoints.

2. **Link mutations ignored feedforward.** `add_inlink`/`add_outlink` connected
   any neurons regardless of direction, creating cycles under a feedforward
   constraint. Fixed: forward-only by layer, which also blocks self-loops.

3. **Bias crashed linking.** `add_bias` records `{bias, Weight}` in
   `input_idps`; `link_neurons` tried to resolve `bias` as a pid and hit
   `badmatch []`. Fixed: extract the bias weight, route it to the neuron's bias
   field.

4. **A crashing agent killed the whole run** via `spawn_link`. Fixed: the
   population's agent closure traps exits; a bad genotype scores [0.0] and is
   selected out.

5. **The memetic tuner was dead.** The exoself sent `{perturb, _}`, `backup`
   and `restore` to neurons with no clauses for them ("unexpected message"), so
   NO weight optimisation happened. This is DXNN's defining mechanism (roadmap
   2, Handbook Ch 10) and it had never run. Fixed: the neuron perturbs,
   snapshots and reverts its weights.

Bug 5 is the one that matters most for capability. Before it, evolution had
selection and topology mutation but no weight learning at all; fitness sat at
~1.0 (a network outputting roughly zero). After it, fitness improves.

## The evidence of function

XOR, feedforward, population 40, best fitness by generation:

| generation | best fitness | approx RMSE |
|---|---|---|
| 50 | 1.6 | 0.62 |
| 150 | 2.8 | 0.36 |

Monotonic. `fitness = 1/(RMSE + epsilon)`, so rising fitness is falling error.
974 tests pass; no cascade across 150 generations.

## Why it does not yet solve, stated precisely

Fitness plateaus around RMSE 0.36 because the error is uneven: three XOR cases
are easy, the fourth needs the network's nonlinearity. `add_neuron` splices a
connection, producing a CHAIN (sensor -> n1 -> n2 -> out), which is a squashed
monotonic function of the input sum and cannot compute XOR. XOR needs the
output to combine TWO parallel hidden units with different weights. The
mutation operators can build that (add_neuron plus cross-links) but random
search reaches it slowly.

So the remaining gap is topology-search quality, not a lifecycle defect. This
is a genuinely different kind of problem: correctness is done, effectiveness is
next.

## What this does NOT claim

Evolution does not solve XOR through this path yet. No comparison to insight
004's control (median 550 evaluations, domain_sdk weight-only) is warranted
until it does. The 0.99 -> 2.8 trajectory shows the engine works; it does not
show it is competitive.

## Next

- Topology-search quality: bias mutation toward parallel hidden units, or adopt
  NEAT-style structural innovation, so XOR (and harder problems) solve in a
  reasonable number of evaluations.
- Then, and only then, the Table 14.1 / control comparison.
- Recurrent networks remain deferred (a recurrent neuron still has no first-
  cycle seed); the feedforward filter avoids the problem rather than solving it.

## The pattern, six deep

Record destructuring, actuator misrouting, cortex report-per-cycle, exoself
never terminating (009), the multi-generation lifecycle (010), and now five
more within it (011). Every wall fell only to running the whole system one more
step. The tuner being dead for the entire life of the codebase, undetected by
1400 tests, is the sharpest example yet of insight 001: components pass, verbs
fail, and only end-to-end execution finds it.
