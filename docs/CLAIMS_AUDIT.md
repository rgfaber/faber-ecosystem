# Claims Audit

**Generated:** 2026-07-20, run `wf_18a439d7-3f9` (complete: 162/162 agents, 0 errors)

Every documentation claim across the four faber repos, checked against code.
Each non-backed verdict went to an independent verifier whose job was to REFUTE it
by finding implementing code, biased toward keeping the claim.

| | Count |
|---|---|
| assessed | 154 |
| confirmed_unbacked | 68 |
| rescued | 86 |
| unverified | 0 |

**This run is complete.** Two earlier runs died on session limits (42 then 20 agents).
See [CLAIMS_AUDIT_CRITIQUE.md](CLAIMS_AUDIT_CRITIQUE.md) for the completeness critic,
which identifies what this audit did NOT cover, chiefly EDoc comments in `src/`
and build-file metadata. Several of its findings are already fixed.

## Confirmed unbacked (68)

| Repo file | Line | Category | Action | Claim |
|---|---|---|---|---|
| `README.md` | 30 | api | **correct** | {ok, AgentId} = genotype:construct_agent(Constraint), |
| `README.md` | 83 | architecture | **correct** | **Mnesia Storage**: Persistent genotype storage |
| `guides/overview.md` | 24 | architecture | **correct** | \\| Mnesia persistence \\| Yes \\| File-based \\| None \\| Yes \\| |
| `guides/quickstart.md` | 7 | architecture | **correct** | First, initialize the Mnesia database: |
| `guides/quickstart.md` | 25 | api | **correct** | {ok, AgentId} = genotype:construct_agent(Constraint). |
| `guides/quickstart.md` | 28 | architecture | **correct** | This creates a minimal neural network topology stored in Mnesia. |
| `guides/quickstart.md` | 81 | api | **correct** | population_monitor:start(PMP). |
| `guides/quickstart.md` | 96 | api | **correct** | {ok, BestAgent} = genotype:read({agent, BestAgentId}). |
| `guides/tweann-basics.md` | 62 | architecture | **correct** | **Genotype**: The blueprint - stored in Mnesia database as records |
| `guides/tweann-basics.md` | 38 | feature | **roadmap** | - **Remove connection** - Prune unused links |
| `guides/architecture.md` | 15 | architecture | **correct** | Handles persistent storage of neural network blueprints (genotypes) using Mnesia. |
| `guides/architecture.md` | 88 | architecture | **correct** | Genotype (Mnesia) |
| `guides/c4-architecture.md` | 20 | architecture | **correct** | **Mnesia** - Distributed database that stores genotypes (network blueprints) persistently |
| `guides/c4-architecture.md` | 41 | architecture | **correct** | **Genotype Store** - Mnesia database storing neural network blueprints as Erlang records |
| `guides/c4-architecture.md` | 113 | architecture | **correct** | 5. `genome_mutator` updates genotypes in Mnesia |
| `guides/c4-architecture.md` | 175 | architecture | **correct** | - **Database**: Mnesia (distributed, ACID) |
| `guides/c4-architecture.md` | 85 | architecture | **correct** | - `cortex` - Network controller (gen_server) |
| `guides/c4-architecture.md` | 42 | architecture | **correct** | **Morphology System** - Problem-specific configurations defining sensors, actuators, and fitness functions |
| `guides/diagram-index.md` | 61 | architecture | **correct** | Each neuron becomes a `gen_server` with its own state and plasticity |
| `guides/diagram-index.md` | 143 | api | **correct** | - **Unbounded**: relu, softplus (deep networks, sparse activation) |
| `guides/diagram-index.md` | 130 | feature | **delete** | - **Self-Modulation**: Neuron controls its own learning parameters |
| `guides/diagram-index.md` | 202 | feature | **correct** | ### Military Swarm Coordination  Autonomous swarm systems. |
| `guides/installation.md` | 13 | version | **correct** | {faber_tweann, "~> 0.17.0"} |
| `guides/installation.md` | 30 | version | **correct** | {:faber_tweann, "~> 0.16.0"} |
| `guides/installation.md` | 3 | feature | **correct** | ## Community Edition (Hex.pm)  The Community Edition is available on hex.pm and uses pure Erlang fallbacks for |
| `guides/neuron-types.md` | 74 | performance | **correct** | The **Closed-form Continuous-time (CfC)** approximation provides equivalent expressivity with ~100x speedup |
| `guides/ltc-evolution.md` | 291 | api | **correct** | cortex:reset_ltc_states(CortexPid). |
| `CLAUDE.md` | 31 | architecture | **correct** | Uses **Mnesia** for persistent genotype storage |
| `CLAUDE.md` | 24 | architecture | **correct** | **genotype.erl** - Genotype construction and Mnesia persistence |
| `design_docs/investigation-khepri-vs-mnesia.md` | 5 | architecture | **correct** | **Purpose**: Evaluate Khepri as potential replacement for Mnesia in genotype storage |
| `design_docs/investigation-khepri-vs-mnesia.md` | 11 | architecture | **correct** | **Recommendation**: **Keep Mnesia for now (v0.8.x - v1.0)**, investigate Khepri for **v2.0+** |
| `design_docs/code-analysis-god-modules.md` | 57 | architecture | **correct** | **Responsibility**: CRUD operations for neural network genotypes (Mnesia persistence) |
| `design_docs/code-analysis-god-modules.md` | 24 | architecture | **correct** | \\| `morphology` \\| 278 \\| 10 \\| ⚠️ **DATA MODULE** \\| 6 morphologies (extensible) \\| |
| `design_docs/code-analysis-god-modules.md` | 188 | architecture | **correct** | - 6 morphologies = 278 LOC (46 LOC/morphology average) |
| `design_docs/README.md` | 312 | architecture | **correct** | 2. **Optimize Mnesia access**    - Consider async/event-sourced design    - Add caching where beneficial    -  |
| `design_docs/INDEX.md` | 246 | architecture | **correct** | - **Analysis Location**: `/home/rl/work/github.com/rgfaber/faber-tweann/design_docs/` |
| `README.md` | 44 | feature | **delete** | **Transfer of meta-knowledge** - Training strategies can transfer across domains |
| `guides/getting-started.md` | 60 | api | **correct** | Outputs = [network_evaluator:propagate(Network, I) \\|\\| I <- Inputs], |
| `guides/custom-evaluator.md` | 110 | api | **correct** | Output = network_evaluator:propagate(Network, Input), |
| `guides/domain-sdk.md` | 417 | api | **correct** | neuroevolution_server:evolve(Pid, 100), |
| `guides/domain-sdk.md` | 419 | api | **correct** | Best = neuroevolution_server:get_best(Pid), |
| `guides/domain-sdk.md` | 420 | api | **correct** | neuroevolution_server:stop(Pid). |
| `guides/behavioral-events.md` | 50 | api | **correct** | neuroevolution_behavioral_events:subscribe(temporal, self()), |
| `guides/behavioral-events.md` | 63 | api | **correct** | Events = neuroevolution_behavioral_events:get_events(temporal, #{ |
| `guides/behavioral-events.md` | 275 | api | **correct** | neuroevolution_behavioral_events:configure(#{ |
| `guides/silos/temporal-silo.md` | 119 | api | **correct** | case temporal_silo:should_terminate_early(TempPid, Fitness) of |
| `guides/silos/morphological-silo.md` | 95 | api | **correct** | Result = morphological_silo:trigger_pruning(MorphPid, NetworkId), |
| `guides/silos/economic-silo.md` | 111 | api | **correct** | ok = economic_silo:redistribute_wealth(EconPid), |
| `guides/silos/cultural-silo.md` | 186 | api | **correct** | Bonuses = cultural_silo:get_cultural_bonuses(CultPid, PopulationIds). |
| `guides/silos/ecological-silo.md` | 414 | api | **correct** | ecological_silo:introduce_disease(PopId, #{virulence => 0.6, transmissibility => 0.8}) |
| `guides/silos/communication-silo.md` | 359 | api | **correct** | Dialects = communication_silo:get_dialect_info(PopId) |
| `guides/silos/regulatory-silo.md` | 364 | api | **correct** | regulatory_silo:inherit_marks(Pid, ParentGeneId, ChildGeneId) |
| `guides/silos/cultural-silo.md` | 477 | architecture | **correct** | \\| `cultural_innovation.erl` \\| Innovation detection \\| Same \\| |
| `guides/lineage-tracking.md` | 292 | dependency | **correct** | **Event Store Backend**: Implements `neuroevolution_lineage_events` using erl-esdb-gater |
| `guides/lineage-tracking.md` | 371 | api | **roadmap** | {ok, Tree} = lineage_tree_projection:get_ancestors(<<"ind-001">>, 5). |
| `guides/interoperability.md` | 328 | dependency | **correct** | macula_rpc:register_method(<<"brain.infer">>, fun handle_inference/1). |
| `guides/continual-learning.md` | 275 | api | **roadmap** | Portfolio = portfolio_manager:init([ |
| `README.md` | 43 | feature | **delete** | **Substrate Networks** - Spatial neural patterns with geometric regularity |
| `README.md` | 51 | feature | **roadmap** | **HyperNEAT** - Indirect encoding via CPPNs for large-scale networks |
| `README.md` | 92 | api | **correct** | Genome = faber_tweann_genotype:construct(     faber_tweann_morphology:xor_mimic(),     <<"species_001">> ),  % |
| `README.md` | 61 | architecture | **correct** | <img src="assets/dependency-flow.svg" alt="Faber Dependency Flow" width="100%"> |
| `README.md` | 141 | feature | **roadmap** | **HyperNEAT** - Stanley, K.O., D'Ambrosio, D.B. & Gauci, J. (2009). *A Hypercube-Based Encoding for Evolving L |
| `README.md` | 7 | architecture | **correct** | This is an **enterprise-only** package that provides 10-15x performance improvements over pure Erlang fallback |
| `README.md` | 148 | performance | **delete** | Typical speedups over pure Erlang:  \\| Operation \\| Speedup \\| \\|-----------\\|---------\\| \\| Network ev |
| `README.md` | 111 | performance | **correct** | ### SIMD Batch Activations - `tanh_batch/1`, `sigmoid_batch/1`, `relu_batch/1`, `softmax_batch/1` - `activatio |
| `README.md` | 14 | dependency | **keep** | {deps, [     {faber_tweann, "~> 1.0"},     {faber_nn_nifs, {git, "git@codeberg.org:rgfaber/faber-nn-nifs.git", |
| `README.md` | 187 | architecture | **correct** | - [faber_tweann](https://hex.pm/packages/faber_tweann) - Community edition on hex.pm |
| `src/faber_nn_nifs.app.src` | 2 | architecture | **correct** | {description, "High-performance Rust NIFs for Faber Neuroevolution (Enterprise)"} |

## Rescued by verification (86)

Flagged as unbacked, but a verifier found implementing code.
**These claims are true. Do not remove them.**

| Repo file | Claim | Backed by |
|---|---|---|
| `README.md` | **First TWEANN library with LTC neurons** in Erlang/OTP | src/ltc_dynamics.erl (evaluate_cfc/4,/5, evaluate_ode/5,/6, compute_liquid_ |
| `README.md` | **CfC closed-form approximation** - ~100x faster than ODE-based LTC | ltc_dynamics:evaluate_cfc/4, evaluate_cfc/5, evaluate_cfc_internal/4 (src/l |
| `README.md` | **Rust NIF acceleration** - Optional 30-200x speedup for fitness statistics | faber-tweann/native/faber_nn_nifs/src/lib.rs: fitness_stats/1 (line 767), k |
| `README.md` | **Pure Erlang fallback** - Works on any system without Rust toolchain | src/tweann_nif_fallback.erl (full 592-line parity surface); src/tweann_nif. |
| `README.md` | **Production ready** - Comprehensive logging, error handling, and process s | src/tweann_logger.erl (debug/info/warning/error/log), src/faber_tweann_sup. |
| `README.md` | {deps, [{faber_tweann, "~> 1.0"}]}. | /home/rl/work/codeberg.org/rgfaber/faber-tweann/README.md:25 vs src/faber_t |
| `README.md` | **CfC Mode**: ~100x faster than ODE-based evaluation | faber-tweann/src/ltc_dynamics.erl: evaluate_cfc/4, evaluate_cfc/5, evaluate |
| `README.md` | **Comprehensive Logging**: Structured logging throughout | /home/rl/work/codeberg.org/rgfaber/faber-tweann/src/tweann_logger.erl (debu |
| `README.md` | **Rust NIF (optional)**: High-performance network evaluation | faber-tweann/src/tweann_nif.erl (compile_network/3, evaluate/2, evaluate_ba |
| `guides/overview.md` | **LTC/CfC Neurons**: First TWEANN library to implement Liquid Time-Constant | faber-tweann/src/ltc_dynamics.erl (evaluate_cfc/4, evaluate_cfc/5, evaluate |
| `guides/overview.md` | Based on DXNN2 by Gene Sher (from "Handbook of Neuroevolution Through Erlan | src/genotype.erl, src/constructor.erl, src/morphology.erl, src/functions.er |
| `guides/overview.md` | \\| Fault tolerance \\| Yes \\| No \\| No \\| Limited \\| | faber-tweann/src/brain_sup.erl (init/1, ?SUP_FLAGS one_for_all intensity 3  |
| `guides/overview.md` | \\| Hot code loading \\| Yes \\| No \\| No \\| Yes \\| | faber-neuroevolution/src/evolve/neuroevolution_server.erl:417 code_change/3 |
| `guides/quickstart.md` | Phenotype = constructor:construct_phenotype(AgentId). | faber-tweann/src/constructor.erl — constructor:construct/1 (and construct/2 |
| `guides/quickstart.md` | cortex:sync(Phenotype#phenotype.cortex_pid). | cortex:sync/1 at src/cortex.erl:110-111 (exported, used in exoself.erl:135, |
| `guides/quickstart.md` | - Create offspring through mutation/crossover | faber-tweann/src/genome_crossover.erl:61 crossover/3; faber-tweann/src/cros |
| `guides/quickstart.md` | Constraint = #constraint{ morphology = xor_mimic, connection_architecture = | include/records.hrl:513-515 (#constraint record, both fields with exactly t |
| `guides/tweann-basics.md` | - Persistent and can be saved/loaded | faber-neuroevolution: src/persist/checkpoint_manager.erl (save_checkpoint/2 |
| `guides/tweann-basics.md` | - Supervision ensures clean restarts | brain_sup.erl (init/1, start_link/1,2) — reached via brain_system:start_lin |
| `guides/tweann-basics.md` | - Pruning unnecessary structure naturally through selection | faber-tweann/src/fitness_postprocessor.erl:81 size_proportional/2 (+ pareto |
| `guides/architecture.md` | Implements genetic operators for topology and weight evolution. Supports bo | faber-neuroevolution/src/strategies/generational_strategy.erl:breed_offspri |
| `guides/architecture.md` | \\| Fault tolerance \\| Full crash \\| Graceful degradation \\| | neuron:handle_input_timeout/1 (src/neuron.erl:237-252) and cortex loop `{ne |
| `guides/architecture.md` | - **Distributed Potential**: Network components can span multiple nodes | faber-neuroevolution/src/evaluate/neuroevolution_evaluator.erl:evaluate_bat |
| `guides/architecture.md` | - **Hot Code Loading**: Update logic without stopping evolution | faber-tweann/src/morphology_registry.erl:158 code_change/3 (+ register/2, u |
| `guides/c4-architecture.md` | 3. Constructor spawns Network Runtime processes from genotypes | /home/rl/work/codeberg.org/rgfaber/faber-neuroevolution/src/constructor.erl |
| `guides/c4-architecture.md` | - **Language**: Erlang/OTP 24+ | src/ (48 .erl modules); OTP-floor-relevant API use in src/innovation.erl (p |
| `guides/diagram-index.md` | - **Binary**: step, sgn (threshold logic) | functions.erl — bin/1 (lines 224-232), sgn/1 (lines 219-222), trinary/1 (li |
| `guides/diagram-index.md` | ### Distributed Evolution Model  Multi-node evolution architecture. | faber-neuroevolution: src/evaluate/neuroevolution_evaluator.erl -> evaluate |
| `guides/diagram-index.md` | - **Innovation numbers**: Historical markings for meaningful crossover | faber-neuroevolution/src/strategies/generational_strategy.erl:385,591-616 ( |
| `guides/installation.md` | - Mnesia (included with Erlang) | src/faber_tweann.app.src:9 (applications list); storage itself is src/genot |
| `guides/neuron-types.md` | **faber-tweann is the first TWEANN library to implement LTC neurons in Erla | faber-tweann/src/ltc_dynamics.erl (377 lines), faber-tweann/src/neuron_ltc. |
| `guides/neuron-types.md` | The `constructor.erl` module automatically spawns the appropriate neuron ty | faber-tweann/src/constructor.erl:186 spawn_neuron_by_type/2, :200 spawn_sta |
| `guides/neuron-types.md` | For the Snake Duel game, LTC neurons enable: ... | src/ltc_dynamics.erl:compute_liquid_tau/4 (adaptive response); src/neuron_l |
| `guides/ltc-usage-guide.md` | {ok, ChildId} = crossover:crossover(ParentA_Id, ParentB_Id). | crossover:crossover/2 (faber-tweann/src/crossover.erl:79-95), with LTC inhe |
| `guides/ltc-usage-guide.md` | %% Perturbs tau by a small random amount within bounds [0.01, 100.0] | ltc_mutations:mutate_time_constant/1 (src/ltc_mutations.erl:91-106), re-exp |
| `guides/ltc-evolution.md` | get_tau_distribution(AgentId) -> Agent = genotype:dirty_read({agent, AgentI | genotype:dirty_read/1 (src/genotype.erl:167-173); fields in include/records |
| `guides/CUSTOM_MORPHOLOGIES.md` | See `examples/` directory for complete working examples: examples/xor/, exa | examples/xor/src/morphology_xor.erl, examples/pole_balancing/src/morphology |
| `guides/CUSTOM_MORPHOLOGIES.md` | Sensors and actuators connect to "scapes" - the environments they interact  | faber-tweann/src/sensor.erl (sense path, scape_pid branch ~:160-172), faber |
| `guides/CUSTOM_MORPHOLOGIES.md` | This guide explains how to create custom morphologies for faber-tweann v0.9 | src/morphology_behaviour.erl (get_sensors/1, get_actuators/1 callbacks); sr |
| `CLAUDE.md` | Tests require Mnesia initialization: ```erlang genotype:init_db(). genotype | faber-tweann/src/genotype.erl: init_db/0 (line 88), reset_db/0 (line 109);  |
| `CLAUDE.md` | **morphology.erl** - Sensor/actuator specifications per problem domain | /home/rl/work/codeberg.org/rgfaber/faber-tweann/src/morphology.erl (get_Sen |
| `CLAUDE.md` | Each morphology defines sensors and actuators for a problem domain: - xor_m | examples/xor/src/morphology_xor.erl:get_sensors/1,get_actuators/1 (xor_mimi |
| `CLAUDE.md` | Uses **Erlang records** (not maps) for all data structures | include/records.hrl (23 record definitions); genotype.erl:96,147,151 (ETS k |
| `CLAUDE.md` | rebar3 compile         # Compile all modules | rebar.config pre_hooks -> priv/build-nifs.sh (build_nif/1); src/ 48 modules |
| `design_docs/code-analysis-god-modules.md` | \\| `genome_mutator` \\| 791 \\| 21 \\| ✅ **ACCEPTABLE** \\| Cohesive mutat | /home/rl/work/codeberg.org/rgfaber/faber-tweann/src/genome_mutator.erl (mut |
| `design_docs/README.md` | ### DXNN2_CODEBASE_ANALYSIS.md (37KB, 970 lines) | /home/rl/work/codeberg.org/rgfaber/faber-tweann/design_docs/DXNN2_CODEBASE_ |
| `README.md` | {faber_neuroevolution, "~> 0.12.0"} | /home/rl/work/codeberg.org/rgfaber/faber-neuroevolution/README.md:56 and sr |
| `README.md` | This library implements the first level of a hierarchical meta-learning sys | faber-neuroevolution: src/meta/meta_controller.erl (gen_server, update/2, g |
| `README.md` | The Liquid Conglomerate is a novel architecture that uses hierarchical **Li | faber-neuroevolution/src/silos/lc_chain.erl — ltc_forward/3 (:369), propaga |
| `README.md` | **LTC Meta-Controller** - Adaptive hyperparameter optimization using Liquid | faber-neuroevolution/src/meta/meta_controller.erl (process_generation/2, fo |
| `README.md` | **Level 0 (fast tau)**: Task networks react to immediate domain state | faber-neuroevolution/src/silos/lc_silo_chain.erl:213-256 (handle_call({get_ |
| `README.md` | **Level 2+ (slow tau)**: Higher-order controllers learn meta-strategies | faber-neuroevolution: src/silos/lc_l2_controller.erl (tau_l2 slow timescale |
| `README.md` | **Automatic stagnation recovery** - Detects and escapes local optima | faber-neuroevolution/src/strategies/generational_strategy.erl:380,799-822 a |
| `README.md` | **Phase-appropriate strategies** - Different strategies for exploration vs  | faber-neuroevolution/src/silos/task_silo/task_silo.erl — compute_l1_boosts_ |
| `README.md` | [Cooperative Silos](guides/cooperative-silos.md) - Cross-silo communication | faber-neuroevolution/src/silos/lc_cross_silo.erl (emit/4, emit_batch/3, get |
| `README.md` | hierarchical meta-learning system called the **Liquid Conglomerate** | faber-neuroevolution/src/silos/lc_chain.erl (init_level/2, level_to_morphol |
| `guides/getting-started.md` | {faber_neuroevolution, "~> 0.12.0"} | /home/rl/work/codeberg.org/rgfaber/faber-neuroevolution/guides/getting-star |
| `guides/cooperative-silos.md` | EffectiveEvals = lc_cross_silo:compute_effective_evals(ResourceSignals, Tas | faber-neuroevolution/src/silos/lc_cross_silo.erl:148-150 (get_effective_eva |
| `guides/silos/social-silo.md` | \\| `social_reputation.erl` \\| Reputation management \\| Same \\| | social_silo.erl — update_reputation/3, get_reputation/2, update_reputation_ |
| `guides/silos/temporal-silo.md` | \\| `temporal_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | faber-neuroevolution/src/silos/temporal_silo/temporal_silo.erl:196 collect_ |
| `guides/silos/morphological-silo.md` | \\| `morphological_silo_sensors.erl` \\| Sensor collection (10) \\| Same \\ | morphological_silo:collect_sensors/1 (/home/rl/work/codeberg.org/rgfaber/fa |
| `guides/silos/competitive-silo.md` | \\| `competitive_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | faber-neuroevolution/src/silos/competitive_silo/competitive_silo.erl:202 co |
| `guides/silos/economic-silo.md` | \\| `economic_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | faber-neuroevolution/src/silos/economic_silo/economic_silo.erl — economic_s |
| `guides/silos/communication-silo.md` | \\| `src/lc_silos/communication_silo.erl` \\| Main silo gen_server \\| | faber-neuroevolution/src/silos/communication_silo/communication_silo.erl (- |
| `guides/silos/ecological-silo.md` | \\| `src/lc_silos/ecological_silo.erl` \\| Main silo gen_server \\| | /home/rl/work/codeberg.org/rgfaber/faber-neuroevolution/src/silos/ecologica |
| `guides/silos/regulatory-silo.md` | \\| `src/lc_silos/regulatory_silo.erl` \\| Main silo gen_server \\| | regulatory_silo (src/silos/regulatory_silo/regulatory_silo.erl) — -behaviou |
| `guides/silos/developmental-silo.md` | \\| `src/lc_silos/developmental_silo.erl` \\| Main silo gen_server \\| | faber-neuroevolution: src/silos/developmental_silo/developmental_silo.erl ( |
| `guides/lineage-tracking.md` | {faber_neuroevolution_esdb, "~> 0.1.0"} | faber-neuroevolution/src/silos/lc_event_emitter.erl (emit/3, emit/4, emit_b |
| `guides/interoperability.md` | NetworkJson = faber_tweann:to_json(Network), | faber-tweann/src/network_evaluator.erl:775 — network_evaluator:to_json/1 (e |
| `guides/interoperability.md` | OnnxBinary = faber_tweann:to_onnx(Network), | faber-tweann/src/network_onnx.erl — network_onnx:to_onnx/1 and to_onnx/2 |
| `guides/liquid-conglomerate.md` | \\| **Temporal** \\| **+++** (2-4x faster) \\| Minimal \\| Eliminates waste | faber-neuroevolution/src/silos/temporal_silo/temporal_silo.erl (record_time |
| `guides/topology-evolution.md` | \\| `add_neuron/1` \\| Insert neuron into existing connection \\| | topological_mutations:add_neuron/1 (faber-tweann/src/topological_mutations. |
| `guides/silos/resource-silo.md` | \\| `resource_silo.erl` \\| Main gen_server \\| `src/silos/resource_silo/`  | faber-neuroevolution/src/silos/resource_silo/resource_silo.erl (-module(res |
| `rebar.config` | {faber_tweann, "~> 1.0"} | rebar.lock (pkg faber_tweann 1.2.0) + _build/default/lib/faber_tweann/ebin/ |
| `rebar.config` | {macula, {git, "https://github.com/rgfaber/macula.git", {tag, "v0.14.2"}}} | faber-neuroevolution/src/distribute/macula_mesh.erl (start_peer/4, advertis |
| `README.md` | {faber_tweann, "~> 0.1.0"},     {faber_neuroevolution, "~> 0.1.0"} | faber-ecosystem/README.md lines 84-86 and 94-96; versions declared in faber |
| `README.md` | {:faber_tweann, "~> 0.1.0"},     {:faber_neuroevolution, "~> 0.1.0"} | faber-ecosystem/README.md:90-98 (current text) backed by faber-tweann/src/f |
| `README.md` | [![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE) | faber-ecosystem/LICENSE (MIT License, Copyright (c) 2026 R.G. Lefever) |
| `README.md` | MIT - See [LICENSE](LICENSE) for details. | /home/rl/work/codeberg.org/rgfaber/faber-ecosystem/LICENSE (MIT License, Co |
| `README.md` | It provides a **complete stack** for evolving adaptive AI controllers using | faber-tweann/src/topological_mutations.erl (add_neuron/1, add_outlink/1, ad |
| `README.md` | - `evaluate_cfc_parallel/4` - Parallel CfC neurons | faber-nn-nifs/native/src/lib.rs:1506 `evaluate_cfc_parallel` (rustler NIF,  |
| `README.md` | The NIFs are automatically detected and used by `faber_tweann`. No code cha | faber-tweann/src/tweann_nif.erl — detect_impl_module/0 + require_nif/0 (v2. |
| `README.md` | The `tweann_nif` module in `faber_tweann` automatically detects this packag | faber-tweann/src/tweann_nif.erl:131 detect_impl_module/0 and :142 require_n |
| `CHANGELOG.md` | ### Stable Release  First stable release. All APIs are considered stable. | src/faber_nn_nifs.erl (all APIs listed under the 1.0.0 entry, e.g. weight_d |
| `CHANGELOG.md` | - **SIMD Batch Activations**: `tanh_batch/1`, `sigmoid_batch/1`, `relu_batc | faber-tweann/native/faber_nn_nifs/src/lib.rs:1369-1405 (tanh_batch, sigmoid |
| `CHANGELOG.md` | [1.0.0]: https://github.com/rgfaber/faber-nn-nifs/compare/v0.1.0...v1.0.0 [ | /home/rl/work/codeberg.org/rgfaber/faber-nn-nifs/.git/config (remote "githu |
