# Message Protocol: DXNN2 vs Faber

**Generated:** 2026-07-20
**Reference:** `reference/dxnn2/` @ `d57d43a9`, cross-checked against Sher,
*Handbook of Neuroevolution Through Erlang*, Ch 7.

This is the specification for Phase 3 of PLAN_FABER_FOUNDATION. The protocol,
not the module list, is what makes DXNN2 a system. It was reinvented rather
than ported, and the fitness path was dropped entirely.

---

## DXNN2 Sense-Think-Act cycle

Verbatim from `reference/dxnn2/src/`.

| # | From -> To | Message | Source |
|---|---|---|---|
| 1 | Cortex -> Sensor | `{Cx_PId, sync}` | `cortex.erl:45` |
| 2 | Sensor -> Scape | `{self(), sense}` or `{self(), sense, Parameters}` | `sensor.erl:72,99,112` |
| 3 | Scape -> Sensor | `{Scape, percept, SensoryVector}` | `sensor.erl:74,101,114` |
| 4 | Sensor -> Neurons | `{self(), forward, Vector}` fanout | |
| 5 | Neuron -> Neuron | `{Input_PId, forward, Input}` | |
| 6 | Neuron -> Actuator | `{Output_PId, forward, [Output]}` | |
| 7 | Actuator -> Scape | `{self(), action, Output}` or `{self(), push, Parameters, Output}` | `actuator.erl:68,93` |
| 8 | Scape -> Actuator | `{Scape, Fitness, HaltFlag}` | `actuator.erl:70` |
| 9 | Actuator -> Cortex | `{self(), sync, Fitness, EndFlag}` | `actuator.erl:51` |
| 10 | Cortex -> Exoself | `{self(), evaluation_completed, FitnessAcc, CycleAcc, TimeDif, get(goal_reached)}` | `cortex.erl:72` |
| 11 | Exoself -> Cortex | `{ExoSelf_PId, reactivate}` | |
| 12 | Exoself -> Scape | `{ExoSelf_PId, terminate}` | |

### Scape lifecycle

```erlang
gen(ExoSelf_PId, Node) -> spawn(Node, ?MODULE, prep, [ExoSelf_PId]).
prep(ExoSelf_PId) -> receive {ExoSelf_PId, Name} -> scape:Name(ExoSelf_PId) end.
```

Spawned by exoself in `spawn_Scapes/4` before sensors, neurons and actuators.
Scape name is read from `#sensor.scape` / `#actuator.scape`. Only
`{private, Name}` entries are spawned; `{public, Name}` scapes are expected to
already be running. A sensor and actuator naming the same scape share one
process (deduplicated via `Unique_Scapes`). Terminated before the cortex.

### goal_reached (Handbook Ch 14)

`cortex.erl:54` checks `EndFlag == goal_reached`; when true it sets
`put(goal_reached, true)`. This propagates via element 6 of message 10 to the
exoself and on to the population_monitor, which freezes `tot_evaluations`.
Without it, "average evaluations to solve" is not measurable, because the
counter keeps incrementing after the goal is reached.

---

## Faber's protocol

From `faber-tweann/src/`.

| From -> To | Message | Source |
|---|---|---|
| Sensor -> Scape | `{SensorPid, sense, SensorName, Parameters}` | `sensor.erl:160-174` |
| Scape -> Sensor | `{ScapePid, sensory_signal, SensorySignal}` | `sensor.erl:160-174` |
| Scape -> Sensor (push) | `{scape, Signal}` | `sensor.erl:122-125` |
| Actuator -> Scape | `{ActuatorPid, actuate, Input, Parameters}` | `actuator.erl:235-248` |
| Scape -> Actuator | `{ScapePid, result, Result}` | `actuator.erl:235-248` |
| Cortex -> Exoself | `{cortex, Id, evaluation_complete, Outputs}` | `cortex.erl:248-256` |
| PopMonitor waits for | `{exoself_terminated, Fitness}` | `population_monitor.erl:315-332` |

---

## Divergence table

| Concern | DXNN2 | Faber | Verdict |
|---|---|---|---|
| Sensor request | `{PId, sense}` | `{PId, sense, SensorName, Parameters}` | Divergent |
| Sensor reply tag | `percept` | `sensory_signal` | Divergent |
| Actuator request tag | `action` / `push` | `actuate` | Divergent |
| Actuator reply | `{Scape, Fitness, HaltFlag}` | `{ScapePid, result, Result}` | **Divergent: no fitness, no halt** |
| Actuator -> Cortex | `{PId, sync, Fitness, EndFlag}` | not present | **Absent** |
| Cortex accumulates fitness | yes, `FitnessAcc` | no, `cortex.erl` contains no `fitness` | **Absent** |
| Cortex -> Exoself | 6-tuple with fitness, cycles, time, goal_reached | `{cortex, Id, evaluation_complete, Outputs}` | **Divergent: reports outputs, not fitness** |
| Fitness definition | supplied by scape | `lists:sum(Outputs)` at `exoself.erl:412-416` | **Wrong** |
| Agent termination | exoself notifies | nothing sends `exoself_terminated` | **Absent: 5s timeout, fitness `[0.0]`** |
| goal_reached | present | absent | **Absent: evaluations-to-solve unmeasurable** |
| Scape spawning | `spawn_Scapes/4` | `exoself` omits `scape_pid`, branches dead | **Absent** |

---

## Consequences

1. There is **no path by which a fitness value can reach the evolutionary
   loop**. Fitness originates in the scape in DXNN2; faber has no scape and no
   fitness message, so `exoself` invents a value from the network's own output.

2. Every agent times out. `population_monitor.erl:315-332` waits 5000ms for
   `{exoself_terminated, Fitness}`, which is sent by nothing, then assigns
   `[0.0]`. Selection operates on a uniformly zero landscape.

3. `goal_reached` being absent means that even after Phase 3, Table 14.1
   comparison is impossible until it is added, because "evaluations to solve"
   depends on freezing the counter at the moment of solution.

---

## Phase 3 decision

**Adopt DXNN2's protocol verbatim.** Reasons:

- The benchmarker (Ch 13) and the Table 14.1 methodology assume it.
- Substrate encoding (Ch 16) plugs into `exoself` and `genotype` as DXNN2
  shapes them.
- The Handbook is a working reference implementation for every message.
- Faber's variant has no fitness path, so there is nothing to preserve.

`constructor.erl` already threads `scape_pid` correctly and has zero callers.
Prefer adopting it over patching `exoself`'s hand-written
`spawn_sensors`/`spawn_actuators`.

Kill criterion K1 fails if this requires changing the public API of
`genotype`, `population_monitor` or `morphology`.
