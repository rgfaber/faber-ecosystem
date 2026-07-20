# 017 — LTC/CfC is now wired into evolution; and adding memory HURT on double-pole at this budget — the third instance of "unexploitable capability is a cost".

**Verdict:** confirmed (LTC wiring) + signed negative (the comparison) · **Date:** 2026-07-20
**Experiment:** EXP_017 (pivoted)
**Uses:** the LTC-into-evolution wiring and the two-phase reset built this
session, the bounded double-pole benchmark, the capability-cost lesson from
013/015.

## The infrastructure win

LTC/CfC neurons now evaluate through the evolutionary path. The CfC/ODE engine
(neuron_ltc + ltc_dynamics) existed but exoself spawned standard neurons for
everything, silently ignoring neuron_type — a genotype full of cfc neurons ran
as standard. exoself now dispatches by neuron_type; neuron_ltc gained the tuner
and protocol handlers (perturb/restore, link-bias, ro-seed, reset). A cfc-seeded
population evaluates end to end, the memetic tuner works, no crash
(ltc_evolution_tests). This is a genuine new operational variant: memory in the
neuron (persistent internal_state) rather than in the wiring.

## The comparison, and the surprise

pb_2_without_velocity (double pole, no velocity, bounded 5000-step goal). Two
FEEDFORWARD arms, pop 20, max_gen 30, 5 runs each. The one variable is whether
neurons carry temporal memory.

| Arm | best_fitness by run | median |
|---|---|---|
| A: memoryless (standard) | 2.29, 0.43, 0.49, 0.45, 0.10 | **0.45** |
| B: cfc (memory in internal_state) | 0.14, 0.13, 0.19, 0.26, 0.21 | **0.19** |

Raw: `017-raw-ltc-vs-memoryless.txt`. Zero input-timeouts (both feedforward).

**Adding memory made it worse.** CfC's median fitness is ~2.4x BELOW the
memoryless baseline. This is the opposite of the naive expectation that a memory
task rewards memory.

## Why — the capability-cost principle, third instance

Neither arm solves: double-pole-without-velocity is a brutal benchmark that the
literature attacks over hundreds of generations, and 30 is far too few. So we
are comparing EARLY-evolution fitness, and there the CfC substrate is dead
weight:

- CfC adds evolvable state per neuron (time constant, bound, and a gated
  internal_state that must be learned to be useful). That enlarges the search
  space and injects dynamical noise.
- In a budget where evolution cannot yet exploit memory, the memoryless network
  has a simpler, more evolvable search space and finds better reactive
  approximations faster.

This is the SAME shape as two earlier findings:

- **013:** evolving topology costs ~37x when the topology is already known.
- **015:** deep per-agent tuning is wasteful on a smooth control problem.
- **017:** neuron-level memory is a tax when the budget cannot exploit it.

The principle: **an unneeded or unexploitable capability is a cost, not a
neutral.** Capability must match BOTH the problem structure AND the budget, or it
is a tax on search. This is a strong prior for the variant map (see the
programme's Neurolab-as-a-Service thesis): a service would warn a user off CfC
here, not for lack of memory-need, but for lack of budget to exploit it.

## What this does NOT claim

- **Not** "LTC/CfC memory is useless." It is a budget-limited, task-limited
  negative. The honest test needs (a) far more generations (hundreds, matching
  the benchmark), and/or (b) a memory task solvable in few generations, and/or
  (c) better CfC initialisation. Seeded cfc here starts with EMPTY backbone/head
  weights (default input-driven gate) and only ~30 generations to tune tau and
  bound — an under-powered CfC.
- **Not** a three-way memory comparison. The wiring-memory variant (recurrent)
  is DEFERRED: recurrent evaluation has a within-episode synchronous-cycle
  desync (a neuron receives both its [0.0] seed and the source's real cycle-0
  output, desyncing the per-cycle input count until it stalls on input_timeout).
  The two-phase reset built this session fixed the between-attempt seed loss but
  not this within-episode desync. See "Deferred" below.

## The variant menu after this session

Three ways to give a network memory, and their status on the process-per-neuron
substrate:

| Variant | Status | Character |
|---|---|---|
| none (feedforward) | works | cheapest; wins when memory is unexploitable |
| neuron (LTC/CfC internal_state) | **works** (new) | robust, cheap to evaluate; no advantage shown yet at low budget |
| wiring (recurrent topology) | **deferred** | fragile: within-episode cycle desync stalls evaluation |

Each working, characterised variant enriches the eventual NLaaS offering. The
recurrent deep-fix is product work: it promotes a broken variant to a menu-able
one.

## Deferred / next

- **Recurrent cycle-protocol deep-fix** (own EXP): make same-layer/mutual
  recurrence sync correctly (distinguish the cycle-0 seed from the source's real
  first output; enforce one-fire-per-neuron-per-cortex-cycle). This unblocks the
  three-way memory comparison and is the honest way to test wiring-memory.
- **Budget-scaled LTC test:** re-run EXP_017 at hundreds of generations, or on a
  memory task solvable in few generations, to see whether CfC memory ever pays
  off — separating "memory doesn't help here" from "the budget was too small".
