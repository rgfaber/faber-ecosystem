# PLAN: Faber Foundation

**Status:** Proposed
**Created:** 2026-07-20
**Scope:** faber-tweann, faber-neuroevolution, faber-nn-nifs, faber-ecosystem

---

## The Premise

faber-{tweann,neuroevolution} were intended as a rewrite of Gene Sher's DXNN2.
An audit on 2026-07-20 established that the port is incomplete in a specific,
diagnosable way:

> **The nouns were ported. The verbs were not.**

Self-contained modules survived (`genotype`, `cortex`, `neuron`, `sensor`,
`actuator`, `morphology`, `population_monitor`, `exoself`, `plasticity`,
`selection_algorithm`, `fitness_postprocessor`, `signal_aggregator`).

Glue and infrastructure did not (`polis`, `scape`, `trainer`, `benchmarker`,
`substrate`, `substrate_cpp`, `substrate_cep`).

The inter-module message protocol was reinvented rather than transliterated,
and the fitness channel was never carried across at all.

**Consequence:** no end-to-end evolutionary run has ever completed in this
codebase. `grep -rn 'exoself_terminated'` across both repos returns exactly one
hit: the `receive` clause waiting for it. Every agent times out after 5s and is
assigned fitness `[0.0]`.

### Governing principle

> **DXNN2 is the specification. Sher's Handbook is the reference implementation.
> Nothing ships that has not run.**

The README therefore stands. It accurately describes DXNN2. The work is to
finish the port, not to lower the claim.

---

## Reference Material

| Source | Location | Role |
|---|---|---|
| Sher, *Handbook of Neuroevolution Through Erlang* | `/home/rl/MEGA/BOOKS/Handbook_of_Neuroevolution_Through_ERLANG_-_Gene_I_Sher.pdf` | Reference implementation, chapter by chapter |
| DXNN2 source | to be vendored at `reference/dxnn2/` (read-only, not a dependency) | Conformance target |

PDF page offset is not constant: printed + 14 around Ch 7, + 9/10 around
Ch 12-13, + 7 around Ch 16-17.

### Chapter dependency chain (this is the build order, not a preference)

```
Ch 7  scape / trainer
Ch 8  polis / population_monitor
Ch 9  XOR WORKS                      <-- first real gate
Ch 12 trace / stat
Ch 13 benchmarker
Ch 14 pole balancing + T-Maze        <-- Table 14.1
Ch 15 plasticity (Hebbian, Oja, neuromodulation)
Ch 16 substrate encoding (HyperNEAT)
Ch 17 substrate plasticity
Ch 18 ALife / Flatland
Ch 19 Forex
```

---

## Architecture Decision: Two Phenotypes, One Genotype

The audit found two parallel evaluation paths. Both are kept, with distinct roles.

| Phenotype | Modules | Role |
|---|---|---|
| **Reference** (process-per-neuron) | `exoself`, `cortex`, `neuron`, `sensor`, `actuator`, `scape` | Correctness, plasticity, substrate work. Sher's design. |
| **Compiled** (vectorized) | `network_evaluator`, `tweann_nif` | Fast fitness evaluation. Faber's addition, genuinely good. |

**Conformance requirement:** given the same genotype and the same inputs, both
phenotypes must produce numerically equal outputs within tolerance. This becomes
a permanent integration test and is the cheapest possible guard against the two
systems drifting apart again.

`domain_sdk` (`agent_environment` / `agent_sensor` / `agent_actuator` /
`agent_evaluator`) is retained as the public embedding API for third-party
environments. It is not the internal architecture.

---

## Phase 0: Ground Truth

**Duration:** 2-3 days

- Vendor DXNN2 at `reference/dxnn2/`, read-only, excluded from the build.
- Produce `docs/CONFORMANCE.md`: every DXNN2 module -> faber module -> status
  (`ported` / `partial` / `missing` / `invented`).
- Produce `docs/PROTOCOL.md`: every message tuple in DXNN2 vs faber, marked
  identical / divergent / absent.

**Exit:** both matrices exist, every row has a status. This is the artifact
that was never produced during the original port, and its absence is the root
cause of everything below.

---

## Phase 1: Stop The Bleeding

**Duration:** 3-5 days

### 1a. Absorb faber-nn-nifs into faber-tweann

Follow the macula precedent (`macula/rebar.config`, `priv/build-nifs.sh`,
"absorbed from macula-nifs package in v1.0").

- Move Rust crate to `faber-tweann/native/faber_nn_nif/`.
- `pre_hooks` build from source. No prebuilt artifact fetching (see the
  glibc trap that bit macula).
- **Kill the silent fallback.** `tweann_nif:detect_impl_module/0` currently
  auto-detects and drops to pure Erlang without notice. Replace with explicit
  configuration plus a startup log line naming the active path. A benchmark
  number that does not name its execution path is not a number.
- Retire the "enterprise-only" framing. Apache-2.0, same as the rest.
- Archive faber-nn-nifs with a pointer.

### 1b. Fix defects on live paths

| Defect | Location |
|---|---|
| `is_map(Network)` clause returns constant `0.5` for all outputs, sitting **above** the real clauses on the production dispatch path | `agent_bridge.erl:477-480` |
| Hardcoded 9 zeros on `error:undef`, unrelated to declared output count | `agent_bridge.erl:497-501` |
| Fitness = sum of network outputs | `exoself.erl:412-416` |
| `exoself_terminated` never sent | `exoself.erl:501-528` |
| Tuning loop resets counter and re-loops forever; `spawn_link` leak | `exoself.erl:448-451` |
| `mock_network_factory:mutate/2` discards parent weights and regenerates at random | test fixtures |
| `mock_evaluator:evaluate/2` returns `rand:uniform() * 100` | test fixtures |
| `rebar.lock` pins `faber_tweann` 0.1.0 against a `~> 1.0` constraint | faber-neuroevolution |
| `erlang:error(substrate_not_implemented)` | `genotype.erl:271` |
| "actual macula integration would go here" | `macula_mesh.erl:293` |

The two mock-fixture entries matter more than they appear. **Every strategy
test (generational, steady-state, island, novelty, MAP-Elites) currently
validates bookkeeping against a system with provably zero selection pressure.**
The island model is entirely unverified.

### 1c. Resolve duplicate wiring

`constructor.erl` correctly threads `scape_pid` and has zero callers.
`exoself`'s hand-written `spawn_sensors`/`spawn_actuators` do not. Pick one.
Recommendation: adopt `constructor.erl`, delete the duplicates.

### 1d. Claims hygiene

Move every unimplemented README claim to `ROADMAP.md`:
HyperNEAT/CPPN, substrate networks, SIMD, Mnesia storage, all unbacked speedup
figures (10-15x in nn-nifs vs 30-200x in tweann, mutually inconsistent, no
committed measurement).

Each claim returns to `README.md` when it lands, accompanied by committed
benchmark output. The README states what is; the roadmap states what will be;
everything in the roadmap gets built.

Restore both publish scripts to hard-fail on test failure.

**Exit:** `rebar3 eunit` green, publish scripts hard-fail, no false claims on
hex.pm, one NIF story, one wiring implementation.

---

## Phase 2: First Run (Fast Path)

**Duration:** 3 days

Run XOR through `domain_sdk`, the path the audit found already complete.

Write four callback modules:

| Module | Behaviour | Size |
|---|---|---|
| `xor_environment` | `agent_environment` | ~120 lines |
| `xor_sensor` | `agent_sensor`, `input_count/0` -> 2 | ~30 lines |
| `xor_actuator` | `agent_actuator`, `output_count/0` -> 1 | ~30 lines |
| `xor_evaluator` | `agent_evaluator` | ~20 lines |

Then run each strategy (generational, steady_state, island, novelty,
map_elites) against **real** fitness for the first time.

**Exit:**
- 18/20 independent runs solve XOR.
- All five strategies complete a run and show fitness improving over generations.
- `test/integration/` is no longer empty.

**Value:** converts "nothing has ever run" into "something has run" for roughly
one day of work, and produces the first evidence that any strategy functions.

---

## Phase 3: The Protocol (THE GATE)

**Duration:** 15 working days, hard cap

Restore Sher's architecture. Book chapters 7 and 8.

1. `scape.erl`: `gen/2`, `prep/1`, name dispatch. Process spawned by exoself,
   terminated before the cortex.
2. `xor_sim/1` and `xor_sim/3` per the Ch 7 listing.
3. `exoself:spawn_Scapes/4`: read `#sensor.scape` / `#actuator.scape`,
   deduplicate, spawn `{private, X}`, populate the currently-permanently-empty
   `private_scape_pids` / `public_scape_pids` fields.
4. **Add the fitness channel.** This is the real work. Adopt Sher's protocol:
   `{ScapePId, Fitness, HaltFlag}` -> actuator -> `{APId, sync, Fitness, HaltFlag}`
   -> cortex accumulates -> `{CxPId, evaluation_completed, FitnessAcc, CycleAcc, TimeAcc}`
   -> exoself.
5. Replace `lists:sum(Outputs)` with scape-supplied fitness.
6. Send `{exoself_terminated, Fitness}` from `terminate_network/1` (requires
   capturing `CallerPid`, currently discarded at `exoself.erl:102`).
7. Fix the tuning loop exit path.
8. `polis.erl` (Ch 8.3), `trainer.erl` (Ch 7.2).
9. Register `xor_mimic` in `morphology_registry`; move `examples/xor` out of the
   examples-only rebar profile.

**Exit:**
- XOR solves 18/20 through the reference phenotype.
- Result agrees with Phase 2 (which now serves as the control; disagreement
  localizes a bug).
- Conformance test passes: reference and compiled phenotypes agree numerically.

**KILL CRITERION K1.**
Fail if 15 days elapse without a green run, or if reaching one requires
redesigning the cortex/exoself message protocol beyond adding the fitness
channel, or if it requires changing the public API of `genotype`,
`population_monitor`, or `morphology`.

On fail: keep the `domain_sdk` path, abandon the Sher architecture, write a
signed negative against "faber-tweann implements DXNN2", and re-plumb the
Table 14.1 comparison through `agent_environment` instead.

---

## Phase 4: Instrumentation

**Duration:** 1 week

- Ch 12: `#trace{}` and `#stat{}` records, stat emission every 500 evaluations.
- Ch 13: `benchmarker.erl`, `#pmp{}`, `#experiment{}`, `population_monitor:prep_PopState/2`,
  `report/2`, gnuplot output to `benchmarks/`.
  - Note: the book's `report/2` has a latent bug, `io:format/2` given a 2-element
    args list against a single `~p`. Fix on transcription.
  - Note: `benchmarker:start/1` overwrites any existing experiment of the same
    id. Keep `continue/1` for crash recovery.
- ETS -> Mnesia migration (Ch 8.4.1), **single-node `disc_copies` only**.
  Explicitly not Mnesia clustering across beam nodes, which would compete with
  the genomes-over-mesh model.
  - This also resolves a live split-brain: `lc_chain.erl:398-450` reads
    genotypes expecting Mnesia while `genotype.erl:96` is ETS.

**Exit:** `benchmarker:start(xor)` produces a report with average evaluations
over 50 runs, plus gnuplot series.

---

## Phase 5: Literature Parity

**Duration:** 1-2 weeks

Implement `pb_sim` using the book's `sm_DoublePole/3` verbatim:
dt 0.01, 0.1s granularity with force applied only in the first sub-step,
G -9.81, cart mass 1, pole1 0.5m / 0.1kg, pole2 0.05m / 0.01kg,
cart-track friction 0.0005, pole-hinge friction 0.000002,
track edges +/- 2.4, failure angle 36 degrees, fitness goal 90000
(= 30 simulated minutes).

Three variants via morphology parameters, one scape:

| Variant | sensor `vl` / params | actuator params |
|---|---|---|
| Single pole, partial info, no damping | 2 / `[2]` | `[without_damping, 0]` |
| Double pole, partial info, no damping | 3 / `[3]` | `[without_damping, 1]` |
| Double pole, with damping | 3 / `[3]` | `[with_damping, 1]` |

### Pre-registered statistics

- n = 50 runs per variant, evaluation cap 100,000.
- Report **median and IQR**. Mann-Whitney U for comparisons.
- **Not** means and t-tests. Sher's own std is 50-80% of his means; the
  distributions are heavy-tailed.

### Pre-registered pass criterion: band placement, not number matching

> Double-pole / no-damping median under 20,000 evaluations, with at least
> 80% solve rate.

Rationale: with std 3596 on a mean of 5184, SEM at n=50 is ~509. Distinguishing
5184 from 4792 is meaningless. Distinguishing the ESP/NEAT/DXNN band (thousands)
from the SANE/RWG band (hundreds of thousands) is easy, and is the only question
the data can answer.

**Table 14.1 (Sher, evaluations to solve, lower better; `*` = weight-only, does
not evolve topology):**

| Method | Single-Pole | Double-Pole W/O Damping | Double-Pole W/ Damping |
|---|---|---|---|
| RWG | 8557 | 415209 | 1232296 |
| SANE | 1212 | 262700 | 451612 |
| CNE* | 724 | 76906* | 87623* |
| ESP | 589 | 7374 | 26342 |
| NEAT | - | - | 6929 |
| CMA-ES* | - | 3521* | 6061* |
| CoSyNE* | 127* | 1249* | 3416* |
| DXNN | not performed | 2359 | 2313 |
| Sher's book system | 647 | 5184 | 4792 |

---

## Phase 6: Faculties

**Duration:** 2-3 weeks

- T-Maze scape (Ch 14.2). Reward positions swap mid-run; requires plastic
  neurons to re-learn.
- `plasticity_oja` (Ch 15.2). `faber_nn_nifs` already exports
  `oja_update_batch/4`; no Erlang module consumes it.
- 2x2 ablation on T-Maze: {standard, LTC/CfC} x {static, plastic}.

**Exit:** a signed result on whether plasticity and LTC neurons help. Either
direction is a result. If plastic does not beat static on post-swap recovery,
the plasticity behaviour is decorative and that is worth knowing.

---

## Phase 7: Performance, Measured

**Duration:** 1-2 weeks

Measure and commit:
- evals/sec: NIF vs pure-Erlang fallback.
- evals/sec: reference phenotype vs compiled phenotype.
- evals/sec: msi00 (i7-7700HQ, AVX2, 8 threads) vs one beam node (Celeron J4105).

Then implement real SIMD, on the dense/layered path only. The sparse
topological-order graph walk will not vectorize usefully.

**Open question this settles:** the NIF currently accelerates only speciation
distance. All fitness evaluation runs through pure Erlang. If evolution is
dominated by the reference phenotype's message passing, the NIFs are largely
irrelevant to throughput and routing fitness evaluation through the compiled
phenotype is a bigger win than any SIMD work.

**Exit:** every performance claim in the README backed by committed benchmark
output naming its execution path.

---

## Phase 8: Substrate Encoding

**Duration:** 2-3 months

Chapter 16 (73 printed pages, ~42 of listings; `substrate.erl` alone is a
19-page listing) then Chapter 17 (18 pages).

New modules: `substrate`, `substrate_cpp`, `substrate_cep`.
Modified: `genotype`, `exoself`, `genome_mutator`, `morphology`, `records.hrl`.

New `#substrate{}` record: `id, agent_id, densities, linkform, plasticity=none,
cpp_ids=[], cep_ids=[]`. New `substrate_id` field on `#agent{}`.

Design note from the book: do **not** create separate `#substrate_cpp{}` /
`#substrate_cep{}` records. Sher explicitly rejects this and instead adds a
`type` field (`neural | substrate_cpp | substrate_cep`) to the existing
`#sensor{}` and `#actuator{}` records, to avoid duplicating every mutation
operator and linking function.

Ch 16 can be built with `plasticity=none`; it does not hard-depend on Ch 15.
Ch 17 requires Ch 16.

**Exit:** substrate-encoded XOR and pole balancing solve. HyperNEAT claim
promoted from ROADMAP.md back into README.md.

---

## Phase 9: Islands

**Duration:** 1 week

4 islands with ring migration vs 1 panmictic population, equal total
evaluations, double-pole-with-damping, n = 30, Mann-Whitney.

Run in a single BEAM on msi00. Islands are a **strategy**, not hardware; this
separates the migration question from the distribution question.

**Exit:** signed result on whether migration beats isolation at equal evaluations.

---

## Phase 10: Energy Environment

Battery dispatch against Elia open data (imbalance prices, load, solar and wind
forecasts; no licensing restrictions).

The property no market offers: **real data with a computable optimum.** Offline
optimal dispatch on a recorded price trace is a small linear program.

Baselines, in increasing order of difficulty:
1. Do nothing.
2. Threshold heuristic.
3. MPC using Elia's published forecasts.

The clairvoyant LP is the metric (regret), **not** the bar. The bar is beating
baseline 3. Evolved policies routinely lose to a well-tuned MPC, and
"evolution beats random" is the number to avoid reporting.

---

## Phase 11: Liquid Conglomerate

**Gated on everything above. Requires its own falsifiable hypothesis before
any code.**

Current state (audited, not inferred):
- 13 silos each implement `apply_actuators/2` and `compute_reward/1`. Both have
  **zero callers**. 26 silo mutator functions, zero callers each.
- Silo sensors compute real arithmetic over state nothing ever writes, so they
  return constants. `temporal_silo:compute_reward/1` would return 0.85 forever.
- `meta_controller` weights are assigned at init and reset and nowhere else. It
  is a frozen random projection with a reward counter. `gradients = #{}` is
  hardcoded.
- `meta_trainer` exports exactly the four functions that would close the loop
  (`update_weights/4`, `compute_gradients/3`, `apply_gradients/3`,
  `estimate_advantage/2`) and has zero callers.
- `lc_chain`'s learning rule is `Delta = LR * Reward * sign(W)`. No credit
  assignment. Every weight moves away from zero by the same magnitude on
  positive reward, saturating at the +/- 10 clamp. It is a global gain knob.
- `lc_controller` (16KB) and `lc_population` (24KB): zero references,
  not supervised.

This is not a gap to fill. It is unstarted original research. There is no book
chapter and no literature baseline, so there is no way to distinguish correct
from incorrect. It must not be worked on until the benchmarked foundation
exists to evaluate it against.

---

## Cross-Cutting Rules

1. No module ships without an integration test that exercises it in the real
   chain. `test/integration/` has been empty since 2025-11-20.
2. Every README claim traces to a passing test or lives in ROADMAP.md.
3. Publish scripts hard-fail on test failure. No exceptions.
4. Conformance test between reference and compiled phenotypes runs in CI.
5. Benchmark output is committed, and names its execution path.
6. Seeded RNG per evaluation. Evolutionary runs must be reproducible; this is
   rare in the literature and is a genuine differentiator.

---

## Hardware

| Box | Spec | Role |
|---|---|---|
| msi00.lab | i7-7700HQ, 4C/8T, AVX2, 31GB | **Primary.** All phases. |
| beam00-03 | Celeron J4105, 4C, no AVX-512, 16-32GB | Out of scope until Phase 7 measurement says otherwise. |

msi00 alone is likely worth the entire beam fleet for float work, and the NIFs
have no SIMD to exploit yet. The mesh carries genomes only, and only if
compute demand ever exceeds one machine. Distribution is a measurement-driven
decision.

---

## Organisational Homes

| Artifact | Home |
|---|---|
| faber-* libraries | `beam-campus/` (after Phase 1 claims hygiene) |
| Scapes and benchmarks | `beam-campus/` |
| Training lab / harness | `beam-campus/` |
| UI | `beam-campus/` |
| Genome-migration mesh service | `hecate-services/`, only if Phase 7/9 justify it |

`rgfaber/` keeps nothing. Personal-org residence was an accident of the
February 2026 sprint.

---

## Summary Timeline

| Phase | Duration | Gate |
|---|---|---|
| 0 Ground truth | 2-3 d | Conformance matrix exists |
| 1 Stop the bleeding | 3-5 d | Green tests, no false claims on hex |
| 2 First run, fast path | 3 d | XOR 18/20, all 5 strategies run |
| 3 The protocol | 15 d **hard cap** | **K1.** XOR via Sher path, phenotypes agree |
| 4 Instrumentation | 1 w | `benchmarker:start(xor)` reports |
| 5 Literature parity | 1-2 w | Double-pole median < 20k, >= 80% solve |
| 6 Faculties | 2-3 w | Signed result on plasticity + LTC |
| 7 Performance | 1-2 w | Committed numbers, SIMD landed |
| 8 Substrate | 2-3 mo | HyperNEAT claim promoted |
| 9 Islands | 1 w | Signed result on migration |
| 10 Energy | - | Beat MPC baseline |
| 11 Liquid Conglomerate | - | Hypothesis first |

Phases 0-2 total under two weeks and are worth doing regardless of every
decision downstream.
