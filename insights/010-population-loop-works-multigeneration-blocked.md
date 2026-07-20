# 010 — The population loop drives Sher-path evaluation; multi-generation evolution is blocked

**Verdict:** confirmed (positive + signed blocker) · **Date:** 2026-07-20

## Claim

The population_monitor's generational machinery successfully drives a whole
population of agents through the process-per-neuron path, collecting real
fitness from each. Evolution does NOT yet solve XOR, because multi-generation
reproduction produces genotypes the evaluator cannot handle. The two facts are
separate and both are established.

## What works

`test/integration/xor_evolves_tests.erl`: 20 fresh xor_mimic agents, one
generation via the population_monitor. All 20 evaluate through the full Sher
path (scape, fitness channel, memetic tuning, exoself_terminated), fitness is
collected, best fitness [0.99] — a real, varied value, not the [0.0] the old
timeout produced. Green in the 974-test suite.

Fixing this required, in population_monitor:
- normalising scalar exoself fitness to the module's vector-fitness convention
  (lists:sum crashed on a bare float);
- raising the per-agent timeout from 5s to 30s (the memetic tuning loop runs up
  to 15 weight-perturbation attempts, and 5s was too tight, producing spurious
  [0.0]);
- handling the goal_reached signal to latch a solved flag;
- a completion notification so a caller can observe the run's outcome.

## What is blocked, and where the bug is NOT

Multi-generation evolution crashes: a badmatch in exoself:link_neurons (a
neuron references an id absent from the process map) and neuron input timeouts.

Bisected by pure-data integrity probes:

| Genotype | Integrity |
|---|---|
| 20 fresh constructions | clean, 1 neuron, no dangling refs |
| clone + single mutation | clean, grows to 2 neurons, all refs resolve |
| multi-generation (clone/mutate/delete repeated, with cleanup) | breaks |

So the bug is NOT in fresh construction, NOT in a single clone, NOT in a single
mutation. It emerges only under the repeated clone/mutate/delete lifecycle the
population runs between generations, most likely in the interaction of
reproduce_population (clone survivors) with cleanup_agents (delete
non-survivors), or in mutation operators that add connections the feedforward
evaluator cannot order.

This is the genotype/mutation lifecycle subsystem, entirely separate from the
scape and fitness channel that Phase 3 set out to restore.

## Why this is recorded rather than fixed now

Chasing it further would spend the K1 budget on a different subsystem. The
Phase 3 deliverable, an evaluation completing through Sher's path (insight
009), is done. The population loop working for one generation is a genuine
extension of that. The multi-generation lifecycle is a bounded, diagnosable
next task, on the roadmap.

## What this does NOT claim

Evolution does not solve XOR through this path yet. The comparison against
insight 004's control (median 550 evaluations, domain_sdk weight-only path)
cannot be made until it does. Any statement that faber's DXNN path is
competitive is unsupported.

## The pattern, again

Every layer of this path has hidden a bug that only running the whole thing
exposed: record destructuring (009), actuator misrouting (009), cortex
report-per-cycle (009), exoself never terminating (009), and now the
multi-generation lifecycle. Component tests proved every noun; only running the
verbs, generation after generation, finds the next wall. The empty
integration directory is where each one lived.
