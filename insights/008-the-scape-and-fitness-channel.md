# 008 — Restoring the scape: the fitness channel Phase 3 needed

**Verdict:** open (Phase 3 in progress) · **Date:** 2026-07-20

## Claim

The reason no evolutionary run had ever completed through faber-tweann's
process-per-neuron path was a single missing link: there was no channel by
which a fitness value could travel from the environment back to the exoself.
Adding it is mechanical and does not require redesigning the protocol.

## What was missing

faber-tweann had the nouns (sensor, neuron, actuator, cortex, exoself) but not
the scape, and not the fitness message. Concretely:

- `#sensor.scape` and `#actuator.scape` were declared and read by nobody.
- Sensors returned zeros for unrecognised sensor names.
- Actuators passed their input straight through.
- `cortex.erl` contained no occurrence of the word "fitness".
- `exoself` computed fitness as `lists:sum` over the network's own outputs, an
  objective that rewards emitting large numbers.

## What was added

A generic `scape` process (DXNN2 wire protocol verbatim), `xor_sim` (the module
the XOR morphology has referenced since day one), and the fitness channel:
actuator asks the scape and receives `{Fitness, HaltFlag}`, cortex accumulates
both, exoself uses the scape's fitness.

`goal_reached` propagates through a `merge_halt/2` so that one actuator
reporting the task solved is never masked by another reporting an ordinary
stop.

## One departure from DXNN2, deliberate

Sher puts every scape as a function inside one shared `scape` module. Here
`scape` is a generic process dispatching to a callback module. The morphologies
already declare scapes as atoms (`{private, xor_sim}`), so a module of that
name is more faithful to the declaration AND extensible without editing a
shared file. The wire protocol is unchanged, so the benchmarker and substrate
encoding that assume it are unaffected.

## Silent-failure removal

Sensor and actuator scape timeouts now RAISE rather than returning zeros or
passing input through. This is the same principle as insight 002 and 003: a
silent degenerate value corrupts fitness with no signal. A timeout on the scape
is a bug, and the system should say so.

## K1 status

Intact. Nothing so far required redesigning the cortex/exoself message protocol
beyond adding the fitness channel, nor touching the public API of `genotype`,
`population_monitor` or `morphology`. Roughly 1.5 of the 15 allotted days used.

## What remains before this insight can be closed

- Scape spawning in `exoself` (or adopt `constructor.erl`, which already
  threads `scape_pid` correctly and has zero callers).
- `exoself_terminated`: still sent by nothing, so `population_monitor` times
  out every agent at 5s. This is the last structural gap.
- The end-to-end run through Sher's path, which is the actual K1 gate.

Until that run completes, the claim that faber-tweann implements DXNN2's
evaluation loop is unproven, and this insight stays open.

971 tests pass, but they test the pieces. No test yet exercises the whole
Sher-path seam, which is exactly the gap that let the missing fitness channel
survive in the first place.
