# 021 — Topology inspection of feedforward T-maze agents: inconclusive (a method flaw), but they evolve deep near-linear chains, delay-independently.

**Verdict:** inconclusive + method lesson · **Date:** 2026-07-21
**Experiment:** EXP_024 (dump the best feedforward T-maze agent's structure)
**Prior art:** see `docs/RESEARCH_PRIOR_ART.md` (reservoir memory capacity, TDNN,
propagation-delay memory).

## What it was meant to answer

019/020 left open what the feedforward T-maze memory mechanism IS. Plan: evolve a
feedforward agent to solve, dump the best agent's neuron count and longest
sensor->actuator path. A delay line needs a path at least as long as the delay,
growing with delay; a compact latch would not.

## Why it did not answer it

`best_agent()` selected by the `#agent.fitness` field, but that field is never
written back to the genotype — the population_monitor holds fitness in its own
state and does not persist it to the ETS record. So every agent read back had
fitness 0, and "best" was an arbitrary agent, not a confirmed solver.

| Delay | best_fitness (unreliable) | neurons | longest path |
|---|---|---|---|
| 2 | 0 | 28 | 29 |
| 8 | 0 | 27 | 28 |

Raw: `021-raw-tmaze-topology.txt`.

## What can still be read (weakly)

- Evolved feedforward T-maze agents are **deep near-linear chains**: longest path
  (28-29) is almost the full neuron count (27-28), i.e. nearly every neuron sits
  in one sequence. DXNN/NEAT neuron-splicing into a connection produces this.
- The depth is **delay-independent** (28-29 at both delay 2 and 8). If this held
  for solvers, it would explain 020's delay-independence: a ~28-deep chain is
  ample delay-line capacity for any tested delay (2..16), so raising the delay
  does not require a deeper structure.

Both readings are CONFOUNDED: (a) these are arbitrary agents, not isolated
solvers; (b) the depth may be ordinary complexification bloat unrelated to memory
— evolved XOR (no-memory) agents might be just as deep. Neither the delay-line
mechanism nor a latch is confirmed.

## Method lessons

1. **Agent fitness is not persisted to the genotype.** Any post-hoc "best agent"
   analysis must re-evaluate agents against the scape, or the population_monitor
   must write fitness back (and expose the best agent id in its completion Info).
2. **A structural claim needs a no-memory control.** To attribute depth to memory,
   compare feedforward T-maze (memory) vs feedforward XOR (no memory) depth. If
   T-maze is not deeper, the depth is bloat, not a delay line.

## Status of the mechanism question

Still OPEN, as 020 said. The feedforward T-maze memory is real (a reactive net
caps at 50) but uncharacterised. Whatever it is, it is a known mechanism from the
reservoir/TDNN literature (`RESEARCH_PRIOR_ART.md`), not a new one.

## Next (a fixed EXP_024)

- Persist agent fitness (or re-evaluate) to isolate the actual solver.
- Add the XOR-depth control.
- Then inspect the solver: chain depth vs delay, and ideally trace one trial's
  signal flow to see the cue propagate.
