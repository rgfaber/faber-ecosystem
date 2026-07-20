# 009 — An evolutionary evaluation completes through DXNN2's path; K1 passes

**Verdict:** confirmed · **Date:** 2026-07-20
**Closes:** [008](008-the-scape-and-fitness-channel.md)

## Claim

An agent now runs to completion through faber-tweann's process-per-neuron
path, produces a fitness derived from the scape rather than from the sum of its
outputs, and reports that fitness back to its caller. This is the first time in
the codebase's history. K1's structural gate is passed.

## Evidence

`test/integration/xor_sher_path_tests.erl`, two tests, both green within the
973-test suite:

- a single xor_mimic agent runs to completion and reports a positive fitness
- that fitness is below the goal_reached ceiling (an untrained random network
  cannot score perfectly), confirming it comes from the scape and not from
  output magnitude

## The four bugs found by running it

Every one was latent because this path had never executed (insight 001). The
1400 unit tests all passed while none of these worked.

1. **Record destructuring throughout exoself.** Every
   `{agent, A} = genotype:dirty_read({agent, Id})` cannot match: dirty_read
   returns the record directly, and an `#agent{}` is a 20-field tuple, not a
   2-tuple. Fifteen such sites. The whole spawn/link path was dead code.

2. **The actuator reported to the wrong process.** Components are spawned
   before the cortex exists, so each was given the exoself as a placeholder
   cortex_pid. Nothing re-pointed them. actuator_output went to the exoself,
   which dropped it, and the cortex waited forever. Fixed with a
   {link, cortex_pid, _} message in the existing linking phase.

3. **The cortex reported after one cycle, not one evaluation.** A scape
   presents an evaluation as several sense-think-act cycles (XOR is four, one
   per case) and halts only on the last. faber's cortex reported to the exoself
   after a single cycle, so each "evaluation" saw only the first case, and the
   scape, reset every attempt, never advanced. Fixed by making the cortex loop
   until the scape's halt flag, accumulating fitness, then report once. This
   uses the halt flag added with the fitness channel; no message changed shape.

4. **The exoself looped forever and never terminated.** On exhausting its
   tuning attempts it reset the counter and re-looped, sending nothing, which
   is why population_monitor timed out every agent at 5s with fitness [0.0]. It
   now reports {exoself_terminated, Fitness} and tears the phenotype down.

## K1 status

**Passed on the structural criterion.** Reaching a green run did NOT require
redesigning the sense-think-act message protocol beyond the fitness channel,
nor changing the public API of genotype, population_monitor or morphology. The
one added message, {link, cortex_pid, _}, is a wiring link in the same category
as the existing {link, fanin_pids, _}, not a protocol change.

## What this does NOT yet claim

- **Solving, not merely running.** These tests prove an evaluation completes
  and fitness is real. They do not yet show evolution SOLVES XOR through this
  path. That needs population_monitor driving a generational loop to a
  goal_reached, which is the next step.
- **Recurrent networks.** The test uses feedforward. A recurrent neuron expects
  a feedback input on its first cycle that nothing has produced; seeding that
  is a separate neuron-evaluation concern, deferred.
- **Comparison to the domain_sdk baseline.** Insight 004's control (median 550
  evaluations) cannot be compared until this path solves and reports
  evaluations-to-solve through population_monitor.

## The wider lesson

Four independent bugs, each fatal, each invisible to a large passing test
suite, all on one never-executed path. This is insight 001 made concrete: unit
tests over components prove the nouns; only running the whole thing proves the
verbs. The integration directory that had been empty since 2025-11-20 is where
the truth was.
