# DXNN2 to Faber Conformance Matrix

**Generated:** 2026-07-20
**Reference:** `reference/dxnn2/` @ `d57d43a91f125f3930e06611f5fce15a0462b32f` (2017-06-15)
**Subject:** faber-tweann @ v1.2.0, faber-neuroevolution @ v1.2.3

---

## Headline

| Measure | Count |
|---|---|
| DXNN2 modules | 33 |
| faber-tweann modules | 47 |
| DXNN2 modules ported | 14 |
| DXNN2 modules renamed | 1 |
| **DXNN2 modules missing** | **18** |
| **faber-tweann modules with no DXNN2 counterpart** | **32** |

> **faber-tweann contains more invented modules (32) than ported ones (15).**

This is the single most important fact in this document. faber-tweann is not a
port of DXNN2 with gaps. It is a substantially different codebase that carried
across 15 of DXNN2's 33 modules and added 32 of its own, while retaining
DXNN2's naming, record shapes, and README claims.

---

## Module Matrix

### Ported (14)

| DXNN2 | faber-tweann |
|---|---|
| `actuator` | `actuator.erl` |
| `cortex` | `cortex.erl` |
| `exoself` | `exoself.erl` |
| `fitness_postprocessor` | `fitness_postprocessor.erl` |
| `functions` | `functions.erl` |
| `genome_mutator` | `genome_mutator.erl` |
| `genotype` | `genotype.erl` |
| `morphology` | `morphology.erl` |
| `neuron` | `neuron.erl` |
| `plasticity` | `plasticity.erl` |
| `population_monitor` | `population_monitor.erl` |
| `selection_algorithm` | `selection_algorithm.erl` |
| `sensor` | `sensor.erl` |
| `signal_aggregator` | `signal_aggregator.erl` |

Ported means the module name exists. It does **not** mean behaviour is
equivalent. See "Ported but divergent" below.

### Renamed (1)

| DXNN2 | faber-tweann |
|---|---|
| `specie_identifier` | `species_identifier.erl` |

### Missing (18)

Grouped by consequence.

#### Blocks any evolutionary run

| Module | Role in DXNN2 | Consequence of absence |
|---|---|---|
| `scape` | The environment. Process spawned per NN, holds problem state, returns fitness. | **No fitness signal exists.** Sensors return zeros, actuators pass through. |
| `polis` | Universe/process registry, starts mnesia, owns the run. | No lifecycle owner. |

#### Silently removes DXNN's core algorithm

| Module | Role in DXNN2 | Called from |
|---|---|---|
| `tuning_selection` | Chooses **which** neurons get weight-perturbed each attempt | `exoself.erl:219`, `genome_mutator.erl:218,262` |
| `tuning_duration` | Computes **max_attempts** per agent per generation | `exoself.erl:137` |
| `tot_topological_mutations` | Computes **how many** topological mutations to apply | `genome_mutator.erl:71` |

**This is DXNN's memetic algorithm** (Handbook Ch 10.1, "The Reason for the
Memetic Approach to Synaptic Weight Optimization"), the property that
distinguishes DXNN from NEAT: interleaving a stochastic hill-climber over
synaptic weights with topological evolution.

faber carries all three as genotype/state fields:
- `genotype.erl:212` `tuning_selection_f = random_element(SpecCon#constraint.tuning_selection_fs)`
- `genotype.erl:214` `tuning_duration_f = SpecCon#constraint.tuning_duration_f`
- `genotype.erl:217` `tot_topological_mutations_f = random_element(...)`
- `exoself.erl:71-72,147-163` stores both in its state record

and **never invokes any of them**. `grep -rnE "tuning_selection:|tuning_duration:|tot_topological_mutations:"`
across both faber repos returns one doc comment and zero calls.

`exoself.erl:69` hardcodes `max_attempts = 15` where DXNN2 computes it per
agent per generation.

#### Benchmarking and instrumentation

| Module | Role |
|---|---|
| `benchmarker` | Handbook Ch 13. Drives N evolutionary runs, averages traces, emits gnuplot data. Required for any Table 14.1 comparison. |
| `visor` | Visualisation. |
| `data_extractor` | Dataset ingestion. |
| `map2rec` | Map to record conversion. |

#### Substrate encoding (HyperNEAT)

| Module | Role |
|---|---|
| `substrate` | Handbook Ch 16. The substrate phenotype. |
| `substrate_cpp` | Coordinate pre-processor. |
| `substrate_cep` | Connectivity expression producer. |

Claimed in faber-ecosystem README as an existing feature. Does not exist.
`genotype.erl:271` raises `substrate_not_implemented`.

#### Environments and applications

| Module | Role |
|---|---|
| `flatland` | Ch 18 ALife simulation |
| `fx` | Ch 19 Forex simulator |
| `scape_GTSA` | Generalised trading scape |
| `epitopes` | Bioinformatics application |
| `derivatives` | Financial derivatives support |

Note `reference/dxnn2/fx_tables/` contains the Forex price data.

#### Not applicable

| Module | Note |
|---|---|
| `dxnn2_app` | faber has `faber_tweann_app` / `faber_tweann_sup` |

---

## Ported but divergent

Module name present, behaviour differs materially.

| Module | Divergence |
|---|---|
| `sensor` | Scape protocol reinvented. Expects `{Pid, sense, SensorName, Parameters}` / `{ScapePid, sensory_signal, Sig}` against DXNN2's `{SPId, sense}` / `{Scape, percept, Vector}`. See PROTOCOL.md. |
| `actuator` | Same. Also has no fitness return path. |
| `cortex` | Contains zero occurrences of `scape` or `fitness`. Reports raw actuator outputs, not accumulated fitness. |
| `exoself` | Never passes `scape_pid` to sensors/actuators, so their scape branches are dead. Computes fitness as `lists:sum(Outputs)`. Never sends `exoself_terminated`. Does not call the tuning modules. |
| `genotype` | ETS, not Mnesia. DXNN2 and the Handbook (Ch 8.4.1) use Mnesia. README and repo CLAUDE.md both still claim Mnesia. |
| `population_monitor` | Waits on `{exoself_terminated, Fitness}`, which nothing sends. Every agent times out at 5s with fitness `[0.0]`. |

---

## Invented (32)

No DXNN2 counterpart. Not defects; several are genuinely valuable. Listed so
that "port DXNN2" work does not accidentally treat them as reference material.

**Vectorized evaluation path (good, keep):**
`network_evaluator`, `network_compiler`, `network_onnx`, `tweann_nif`,
`tweann_nif_fallback`, `neuron_info`

**LTC / CfC neurons (faber's genuine addition beyond DXNN2):**
`ltc_dynamics`, `ltc_mutations`, `neuron_ltc`

**Plasticity implementations (DXNN2 has these inside `plasticity.erl`):**
`plasticity_hebbian`, `plasticity_modulated`, `plasticity_none`
Note: no `plasticity_oja`, though Handbook Ch 15.2 covers Oja's rule and
`faber_nn_nifs` already exports `oja_update_batch/4`.

**Mutation decomposition (DXNN2 keeps these in `genome_mutator.erl`):**
`topological_mutations`, `parametric_mutations`, `mutation_helpers`,
`perturbation_utils`, `crossover`, `genome_crossover`, `innovation`

**Morphology registry (DXNN2 dispatches by atom):**
`morphology_behaviour`, `morphology_registry`

**Brain subsystem (no DXNN2 analogue at all):**
`brain`, `brain_learner`, `brain_pubsub`, `brain_sup`, `brain_system`

**Other:**
`constructor` (correct scape wiring, zero callers), `selection_utils`,
`network_pubsub`, `tweann_logger`, `faber_tweann_app`, `faber_tweann_sup`

---

## faber-neuroevolution

DXNN2 has no counterpart for this repo at all. Its `strategies/`, `silos/`,
`meta/` and `domain_sdk/` trees are entirely original work with no reference
implementation to diff against, and must be assessed on their own terms.

Audited findings recorded here for completeness:

| Subsystem | State |
|---|---|
| `domain_sdk` -> `neuroevolution_server` -> `bridge_evaluator` -> `agent_bridge` -> `network_evaluator` | Complete. Runs today given a user environment module. |
| `strategies/` (generational, steady_state, island, novelty, map_elites) | Present, tested only against `rand:uniform()` fitness with a mutation operator that discards parent weights. Unverified. |
| `silos/` (13 silos) | `apply_actuators/2` and `compute_reward/1` implemented 13 times, zero callers. 26 mutators, zero callers. Sensors compute real arithmetic over state nothing writes. |
| `meta/` (`meta_controller`, `meta_trainer`) | Weights assigned at init and reset only. `meta_trainer` zero callers. `gradients = #{}` hardcoded. |
| `lc_chain` | Runs. Learning rule is `Delta = LR * Reward * sign(W)`: no credit assignment. |

---

## Method

```
find reference/dxnn2/src -name '*.erl' -printf '%f\n' | sed 's/\.erl$//' | sort
```
compared against `faber-tweann/src` and `faber-neuroevolution/src`.

Caveat: name-level comparison only. "Ported" means a module of that name
exists, not that it behaves equivalently. The "Ported but divergent" section
records behavioural differences found by reading; it is not exhaustive.

Regenerate with `scripts/conformance.sh` (to be written). Do not use `ls` in
that script: `ls` is aliased to `eza` in this environment and emits icon
glyphs that corrupt `basename` pipelines.
