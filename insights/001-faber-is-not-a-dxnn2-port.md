# 001 — faber-tweann is not a DXNN2 port

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

faber-tweann was understood to be a rewrite of Gene Sher's DXNN2. It is not.
It carried across a minority of DXNN2's modules, added twice as many of its
own, and reinvented the message protocol that makes the modules a system.

## Evidence

Mechanical comparison against `reference/dxnn2` @ `d57d43a9`:

| Measure | Count |
|---|---|
| DXNN2 modules | 33 |
| Ported | 14 |
| Renamed | 1 |
| Missing | 18 |
| faber-tweann modules with no DXNN2 counterpart | 32 |

**More invented modules (32) than ported ones (15).**

The split is not random. Self-contained nouns survived (`genotype`, `cortex`,
`neuron`, `sensor`, `actuator`, `morphology`, `population_monitor`, `exoself`).
Glue and infrastructure did not (`polis`, `scape`, `trainer`, `benchmarker`,
`substrate`, `tuning_selection`, `tuning_duration`,
`tot_topological_mutations`).

The protocol was reinvented rather than transliterated. DXNN2's sensor asks
`{SPId, sense}` and receives `{Scape, percept, Vector}`; faber's asks
`{Pid, sense, SensorName, Parameters}` and receives
`{ScapePid, sensory_signal, Signal}`. DXNN2's scape returns
`{ScPId, Fitness, HaltFlag}`; **faber has no fitness message at all.**

Three tells that this was a file-by-file port that never ran:

1. `constructor.erl` threads `scape_pid` correctly and has **zero callers**.
   Someone ported the wiring, then hand-wrote `exoself`'s spawn functions
   separately and never connected them.
2. Fitness had to be invented (`lists:sum(Outputs)` at `exoself.erl:412-416`)
   precisely because the scape that supplies it was never ported.
3. 1400 unit tests passed because each module is internally consistent.
   Nothing tested a seam. `test/integration/` had been empty since 2025-11-20.

## What changes

"Make the code comply with the README" is the correct instruction, and it means
**finish the port**, not lower the claim. DXNN2's own README describes DXNN2
accurately; it just never described faber.

Sher's chapter order is the dependency order and therefore the work order. It
is not a matter of taste: substrate encoding (Ch 16) plugs into a working
system from Ch 9.

The missing memetic tuning layer matters more than the missing scape. The
scape's absence stops everything loudly. `tuning_selection`/`tuning_duration`/
`tot_topological_mutations` are assigned into the genotype
(`genotype.erl:212,214,217`) and carried in exoself state, and **never
invoked** — so the system would run and quietly not be DXNN.
