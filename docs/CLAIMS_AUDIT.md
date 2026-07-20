# Claims Audit

**Generated:** 2026-07-20 by multi-agent audit (run wf_18a439d7-3f9)

Every documentation claim across the four faber repos, checked against code.
Each non-backed verdict was then given to an independent verifier whose job was to
REFUTE it by finding implementing code, biased toward keeping the claim.

## Coverage warning

**This audit is incomplete.** 42 of 162 agents failed on a session limit, including the
completeness critic and most verifiers for the faber-tweann guides
(architecture, c4-architecture, tweann-basics, quickstart, diagram-index, installation,
neuron-types, ltc-usage-guide, ltc-evolution, CUSTOM_MORPHOLOGIES). Claims from those
files that were not verified are EXCLUDED from the confirmed list below, so this
undercounts. Re-run before treating it as exhaustive.

## Confirmed unbacked (58)

| Repo file | Line | Category | Action | Claim |
|---|---|---|---|---|
| `README.md` | 25 | version | **correct** | {deps, [{faber_tweann, "~> 1.0"}]}. |
| `README.md` | 83 | architecture | **correct** | **Mnesia Storage**: Persistent genotype storage |
| `guides/overview.md` | 24 | architecture | **correct** | \\| Mnesia persistence \\| Yes \\| File-based \\| None \\| Yes \\| |
| `guides/quickstart.md` | 7 | architecture | **correct** | First, initialize the Mnesia database: |
| `guides/quickstart.md` | 45 | api | **correct** | cortex:sync(Phenotype#phenotype.cortex_pid). |
| `guides/quickstart.md` | 28 | architecture | **correct** | This creates a minimal neural network topology stored in Mnesia. |
| `guides/quickstart.md` | 81 | api | **correct** | population_monitor:start(PMP). |
| `guides/quickstart.md` | 96 | api | **correct** | {ok, BestAgent} = genotype:read({agent, BestAgentId}). |
| `guides/quickstart.md` | 19 | api | **correct** | Constraint = #constraint{ morphology = xor_mimic, connection_architecture = recurrent }. |
| `guides/tweann-basics.md` | 62 | architecture | **correct** | **Genotype**: The blueprint - stored in Mnesia database as records |
| `CLAUDE.md` | 31 | architecture | **correct** | Uses **Mnesia** for persistent genotype storage |
| `CLAUDE.md` | 24 | architecture | **correct** | **genotype.erl** - Genotype construction and Mnesia persistence |
| `CLAUDE.md` | 65 | api | **correct** | Tests require Mnesia initialization: ```erlang genotype:init_db(). genotype:reset_db().   % Clear between tests ``` |
| `design_docs/investigation-khepri-vs-mnesia.md` | 5 | architecture | **correct** | **Purpose**: Evaluate Khepri as potential replacement for Mnesia in genotype storage |
| `design_docs/investigation-khepri-vs-mnesia.md` | 11 | architecture | **correct** | **Recommendation**: **Keep Mnesia for now (v0.8.x - v1.0)**, investigate Khepri for **v2.0+** |
| `design_docs/code-analysis-god-modules.md` | 57 | architecture | **correct** | **Responsibility**: CRUD operations for neural network genotypes (Mnesia persistence) |
| `design_docs/code-analysis-god-modules.md` | 24 | architecture | **correct** | \\| `morphology` \\| 278 \\| 10 \\| ⚠️ **DATA MODULE** \\| 6 morphologies (extensible) \\| |
| `design_docs/code-analysis-god-modules.md` | 188 | architecture | **correct** | - 6 morphologies = 278 LOC (46 LOC/morphology average) |
| `design_docs/README.md` | 312 | architecture | **correct** | 2. **Optimize Mnesia access**    - Consider async/event-sourced design    - Add caching where beneficial    - Test for c |
| `design_docs/INDEX.md` | 246 | architecture | **correct** | - **Analysis Location**: `/home/rl/work/github.com/rgfaber/faber-tweann/design_docs/` |
| `README.md` | 56 | version | **correct** | {faber_neuroevolution, "~> 0.12.0"} |
| `README.md` | 44 | feature | **roadmap** | **Transfer of meta-knowledge** - Training strategies can transfer across domains |
| `guides/getting-started.md` | 11 | version | **correct** | {faber_neuroevolution, "~> 0.12.0"} |
| `guides/getting-started.md` | 60 | api | **correct** | Outputs = [network_evaluator:propagate(Network, I) \\|\\| I <- Inputs], |
| `guides/custom-evaluator.md` | 110 | api | **correct** | Output = network_evaluator:propagate(Network, Input), |
| `guides/domain-sdk.md` | 417 | api | **correct** | neuroevolution_server:evolve(Pid, 100), |
| `guides/domain-sdk.md` | 419 | api | **correct** | Best = neuroevolution_server:get_best(Pid), |
| `guides/domain-sdk.md` | 420 | api | **correct** | neuroevolution_server:stop(Pid). |
| `guides/behavioral-events.md` | 50 | api | **correct** | neuroevolution_behavioral_events:subscribe(temporal, self()), |
| `guides/behavioral-events.md` | 63 | api | **delete** | Events = neuroevolution_behavioral_events:get_events(temporal, #{ |
| `guides/behavioral-events.md` | 275 | api | **delete** | neuroevolution_behavioral_events:configure(#{ |
| `guides/silos/temporal-silo.md` | 119 | api | **correct** | case temporal_silo:should_terminate_early(TempPid, Fitness) of |
| `guides/silos/morphological-silo.md` | 95 | api | **correct** | Result = morphological_silo:trigger_pruning(MorphPid, NetworkId), |
| `guides/silos/economic-silo.md` | 111 | api | **correct** | ok = economic_silo:redistribute_wealth(EconPid), |
| `guides/silos/cultural-silo.md` | 186 | api | **correct** | Bonuses = cultural_silo:get_cultural_bonuses(CultPid, PopulationIds). |
| `guides/silos/ecological-silo.md` | 414 | api | **delete** | ecological_silo:introduce_disease(PopId, #{virulence => 0.6, transmissibility => 0.8}) |
| `guides/silos/communication-silo.md` | 359 | api | **correct** | Dialects = communication_silo:get_dialect_info(PopId) |
| `guides/silos/regulatory-silo.md` | 364 | api | **correct** | regulatory_silo:inherit_marks(Pid, ParentGeneId, ChildGeneId) |
| `guides/lineage-tracking.md` | 294 | dependency | **correct** | {faber_neuroevolution_esdb, "~> 0.1.0"} |
| `guides/lineage-tracking.md` | 292 | dependency | **correct** | **Event Store Backend**: Implements `neuroevolution_lineage_events` using erl-esdb-gater |
| `guides/lineage-tracking.md` | 371 | api | **correct** | {ok, Tree} = lineage_tree_projection:get_ancestors(<<"ind-001">>, 5). |
| `guides/interoperability.md` | 43 | api | **correct** | NetworkJson = faber_tweann:to_json(Network), |
| `guides/interoperability.md` | 328 | dependency | **correct** | macula_rpc:register_method(<<"brain.infer">>, fun handle_inference/1). |
| `guides/liquid-conglomerate.md` | 179 | performance | **correct** | \\| **Temporal** \\| **+++** (2-4x faster) \\| Minimal \\| Eliminates wasted evaluation time \\| |
| `guides/continual-learning.md` | 275 | api | **roadmap** | Portfolio = portfolio_manager:init([ |
| `README.md` | 43 | feature | **roadmap** | **Substrate Networks** - Spatial neural patterns with geometric regularity |
| `README.md` | 51 | feature | **roadmap** | **HyperNEAT** - Indirect encoding via CPPNs for large-scale networks |
| `README.md` | 71 | version | **correct** | {faber_tweann, "~> 0.1.0"},     {faber_neuroevolution, "~> 0.1.0"} |
| `README.md` | 82 | version | **correct** | {:faber_tweann, "~> 0.1.0"},     {:faber_neuroevolution, "~> 0.1.0"} |
| `README.md` | 61 | architecture | **correct** | <img src="assets/dependency-flow.svg" alt="Faber Dependency Flow" width="100%"> |
| `README.md` | 141 | feature | **correct** | **HyperNEAT** - Stanley, K.O., D'Ambrosio, D.B. & Gauci, J. (2009). *A Hypercube-Based Encoding for Evolving Large-Scale |
| `README.md` | 7 | architecture | **correct** | This is an **enterprise-only** package that provides 10-15x performance improvements over pure Erlang fallbacks for comp |
| `README.md` | 148 | performance | **correct** | Typical speedups over pure Erlang:  \\| Operation \\| Speedup \\| \\|-----------\\|---------\\| \\| Network evaluate \\| |
| `README.md` | 111 | performance | **correct** | ### SIMD Batch Activations - `tanh_batch/1`, `sigmoid_batch/1`, `relu_batch/1`, `softmax_batch/1` - `activation_batch/2` |
| `README.md` | 14 | dependency | **correct** | {deps, [     {faber_tweann, "~> 1.0"},     {faber_nn_nifs, {git, "git@codeberg.org:rgfaber/faber-nn-nifs.git", {tag, "v1 |
| `README.md` | 187 | architecture | **correct** | - [faber_tweann](https://hex.pm/packages/faber_tweann) - Community edition on hex.pm |
| `CHANGELOG.md` | 24 | performance | **correct** | - **SIMD Batch Activations**: `tanh_batch/1`, `sigmoid_batch/1`, `relu_batch/1`, `softmax_batch/1` |
| `src/faber_nn_nifs.app.src` | 2 | architecture | **correct** | {description, "High-performance Rust NIFs for Faber Neuroevolution (Enterprise)"} |

## Rescued by verification (55)

Flagged as unbacked by the auditor, but a verifier found implementing code.
**These claims are true. Do not remove them.**

| Repo file | Claim | Backed by |
|---|---|---|
| `README.md` | **First TWEANN library with LTC neurons** in Erlang/OTP | faber-tweann: src/ltc_dynamics.erl (evaluate_cfc/4,/5, evaluate_ode/5,/6, evalua |
| `README.md` | **CfC closed-form approximation** - ~100x faster than ODE-based LTC | faber-tweann/src/ltc_dynamics.erl — evaluate_cfc/4 (line 105), evaluate_cfc/5 (l |
| `README.md` | **Rust NIF acceleration** - Optional 30-200x speedup for fitness statistics, nov | native/faber_nn_nifs/src/lib.rs: fitness_stats/1 (line 767), knn_novelty/4 (line |
| `README.md` | **Pure Erlang fallback** - Works on any system without Rust toolchain | src/tweann_nif_fallback.erl (complete 592-line pure-Erlang implementation, 64 fu |
| `README.md` | **Production ready** - Comprehensive logging, error handling, and process safety | faber-tweann: src/tweann_logger.erl (debug/info/warning/error/log), src/faber_tw |
| `README.md` | {ok, AgentId} = genotype:construct_agent(Constraint), | genotype:construct_Agent/3 (/home/rl/work/codeberg.org/rgfaber/faber-tweann/src/ |
| `README.md` | **CfC Mode**: ~100x faster than ODE-based evaluation | faber-tweann/src/ltc_dynamics.erl — evaluate_cfc/4,5 (lines 105, 123) and evalua |
| `README.md` | **Comprehensive Logging**: Structured logging throughout | faber-tweann/src/tweann_logger.erl — debug/2, info/2, warning/2, error/2 delegat |
| `README.md` | **Rust NIF (optional)**: High-performance network evaluation | faber-tweann/src/tweann_nif.erl — compile_network/3, evaluate/2, evaluate_batch/ |
| `guides/overview.md` | **LTC/CfC Neurons**: First TWEANN library to implement Liquid Time-Constant neur | faber-tweann/src/ltc_dynamics.erl (evaluate_cfc/4, evaluate_cfc/5, evaluate_cfc_ |
| `guides/overview.md` | Based on DXNN2 by Gene Sher (from "Handbook of Neuroevolution Through Erlang"),  | faber-tweann/src/morphology.erl (get_InitSensors/1, get_InitActuators/1), src/mo |
| `guides/overview.md` | \\| Fault tolerance \\| Yes \\| No \\| No \\| Limited \\| | faber-tweann/src/brain_sup.erl (init/1, one_for_all, permanent brain + brain_lea |
| `guides/overview.md` | \\| Hot code loading \\| Yes \\| No \\| No \\| Yes \\| | faber-tweann/src/morphology_registry.erl:158 (code_change/3); gen_server callbac |
| `guides/quickstart.md` | {ok, AgentId} = genotype:construct_agent(Constraint). | faber-tweann/src/genotype.erl:194 construct_Agent/3 (exported at genotype.erl:66 |
| `guides/quickstart.md` | Phenotype = constructor:construct_phenotype(AgentId). | faber-tweann/src/constructor.erl:57 — constructor:construct/1 (and construct/2 a |
| `CLAUDE.md` | **morphology.erl** - Sensor/actuator specifications per problem domain | faber-tweann: src/morphology.erl (get_Sensors/1, get_Actuators/1, get_InitSensor |
| `CLAUDE.md` | Each morphology defines sensors and actuators for a problem domain: - xor_mimic  | faber-tweann/examples/xor/src/morphology_xor.erl, examples/pole_balancing/src/mo |
| `CLAUDE.md` | Uses **Erlang records** (not maps) for all data structures | faber-tweann/include/records.hrl (23 domain records); record-based process state |
| `CLAUDE.md` | rebar3 compile         # Compile all modules | rebar.config (project definition + pre_hooks lines 25-28); src/ module tree; pri |
| `design_docs/code-analysis-god-modules.md` | \\| `genome_mutator` \\| 791 \\| 21 \\| ✅ **ACCEPTABLE** \\| Cohesive mutation o | faber-tweann/src/genome_mutator.erl (mutate/1, mutate/2, calculate_mutation_coun |
| `design_docs/README.md` | ### DXNN2_CODEBASE_ANALYSIS.md (37KB, 970 lines) | /home/rl/work/codeberg.org/rgfaber/faber-tweann/design_docs/DXNN2_CODEBASE_ANALY |
| `README.md` | This library implements the first level of a hierarchical meta-learning system c | src/silos/lc_silo_chain.erl (perturb_layers/2 L474, accept/reject L449-465, do_r |
| `README.md` | The Liquid Conglomerate is a novel architecture that uses hierarchical **Liquid  | faber-neuroevolution/src/silos/lc_silo_chain.erl — do_report_reward/2 (:308), ma |
| `README.md` | **LTC Meta-Controller** - Adaptive hyperparameter optimization using Liquid Time | meta_controller.erl (process_generation/2, forward_pass/2, apply_ltc_dynamics, o |
| `README.md` | **Level 0 (fast tau)**: Task networks react to immediate domain state | faber-neuroevolution/src/silos/lc_silo_chain.erl (l0_network / tau_l0 / forward_ |
| `README.md` | **Level 2+ (slow tau)**: Higher-order controllers learn meta-strategies | src/meta/meta_controller.erl (L2->L1 guidance functions, ?L2_GUIDANCE_DEFAULTS,  |
| `README.md` | **Automatic stagnation recovery** - Detects and escapes local optima | faber-neuroevolution/src/strategies/generational_strategy.erl:801 apply_stagnati |
| `README.md` | **Phase-appropriate strategies** - Different strategies for exploration vs explo | faber-neuroevolution/src/silos/task_silo/task_silo.erl — compute_l1_boosts_from_ |
| `README.md` | [Cooperative Silos](guides/cooperative-silos.md) - Cross-silo communication in L | faber-neuroevolution/src/silos/lc_cross_silo.erl — subscribe_to_silo_topics/0 (: |
| `README.md` | hierarchical meta-learning system called the **Liquid Conglomerate** | faber-neuroevolution/src/silos/lc_chain.erl (do_forward/3, level_to_tau/2, forwa |
| `guides/cooperative-silos.md` | EffectiveEvals = lc_cross_silo:compute_effective_evals(ResourceSignals, TaskSign | lc_cross_silo:get_effective_evals_per_individual/0 (src/silos/lc_cross_silo.erl: |
| `guides/silos/social-silo.md` | \\| `social_reputation.erl` \\| Reputation management \\| Same \\| | faber-neuroevolution/src/silos/social_silo/social_silo.erl — update_reputation/3 |
| `guides/silos/cultural-silo.md` | \\| `cultural_innovation.erl` \\| Innovation detection \\| Same \\| | cultural_silo.erl: record_innovation/4 (line 147), handle_cast({record_innovatio |
| `guides/silos/temporal-silo.md` | \\| `temporal_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | faber-neuroevolution/src/silos/temporal_silo/temporal_silo.erl — collect_sensors |
| `guides/silos/morphological-silo.md` | \\| `morphological_silo_sensors.erl` \\| Sensor collection (10) \\| Same \\| | faber-neuroevolution/src/silos/morphological_silo/morphological_silo.erl → morph |
| `guides/silos/competitive-silo.md` | \\| `competitive_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | competitive_silo:collect_sensors/1 (/home/rl/work/codeberg.org/rgfaber/faber-neu |
| `guides/silos/economic-silo.md` | \\| `economic_silo_sensors.erl` \\| Sensor collection (12) \\| Same \\| | faber-neuroevolution/src/silos/economic_silo/economic_silo.erl — collect_sensors |
| `guides/silos/communication-silo.md` | \\| `src/lc_silos/communication_silo.erl` \\| Main silo gen_server \\| | communication_silo (/home/rl/work/codeberg.org/rgfaber/faber-neuroevolution/src/ |
| `guides/silos/ecological-silo.md` | \\| `src/lc_silos/ecological_silo.erl` \\| Main silo gen_server \\| | faber-neuroevolution/src/silos/ecological_silo/ecological_silo.erl — module ecol |
| `guides/silos/regulatory-silo.md` | \\| `src/lc_silos/regulatory_silo.erl` \\| Main silo gen_server \\| | faber-neuroevolution/src/silos/regulatory_silo/regulatory_silo.erl (-module(regu |
| `guides/silos/developmental-silo.md` | \\| `src/lc_silos/developmental_silo.erl` \\| Main silo gen_server \\| | developmental_silo (src/silos/developmental_silo/developmental_silo.erl) — -beha |
| `guides/interoperability.md` | OnnxBinary = faber_tweann:to_onnx(Network), | faber-tweann/src/network_onnx.erl — network_onnx:to_onnx/1 and to_onnx/2 |
| `guides/topology-evolution.md` | \\| `add_neuron/1` \\| Insert neuron into existing connection \\| | faber-tweann/src/topological_mutations.erl:147 add_neuron/1 (exported); re-expor |
| `guides/silos/resource-silo.md` | \\| `resource_silo.erl` \\| Main gen_server \\| `src/silos/resource_silo/` \\| | src/silos/resource_silo/resource_silo.erl (-module(resource_silo), -behaviour(ge |
| `rebar.config` | {faber_tweann, "~> 1.0"} | faber-neuroevolution/rebar.lock (pkg faber_tweann 1.2.0 + pkg_hash) and faber-ne |
| `rebar.config` | {macula, {git, "https://github.com/rgfaber/macula.git", {tag, "v0.14.2"}}} | src/distribute/macula_mesh.erl (-ifdef(MACULA_MESH_ENABLED) block: start_peer/4, |
| `README.md` | Genome = faber_tweann_genotype:construct(     faber_tweann_morphology:xor_mimic( | faber-tweann/src/network_evaluator.erl:235 from_genotype/1; network_evaluator.er |
| `README.md` | [![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE) | /home/rl/work/codeberg.org/rgfaber/faber-ecosystem/LICENSE (MIT License, Copyrig |
| `README.md` | MIT - See [LICENSE](LICENSE) for details. | faber-ecosystem/LICENSE (MIT License text, line 1) |
| `README.md` | It provides a **complete stack** for evolving adaptive AI controllers using TWEA | faber-tweann/src/topological_mutations.erl (add_neuron/1, outsplice/1, add_inlin |
| `README.md` | - `evaluate_cfc_parallel/4` - Parallel CfC neurons | faber-nn-nifs/native/src/lib.rs:1506 `evaluate_cfc_parallel` (rustler NIF, Dirty |
| `README.md` | The NIFs are automatically detected and used by `faber_tweann`. No code changes  | faber-tweann/src/tweann_nif.erl:131 detect_impl_module/0 and :142 require_nif/0  |
| `README.md` | The `tweann_nif` module in `faber_tweann` automatically detects this package:  ` | faber-tweann/src/tweann_nif.erl:131 detect_impl_module/0, :143 require_nif/0, :1 |
| `CHANGELOG.md` | ### Stable Release  First stable release. All APIs are considered stable. | faber-nn-nifs/src/faber_nn_nifs.erl (684 lines, exports all functions listed und |
| `CHANGELOG.md` | [1.0.0]: https://github.com/rgfaber/faber-nn-nifs/compare/v0.1.0...v1.0.0 [0.1.0 | git remote 'github' -> git@github.com:rgfaber/faber-nn-nifs.git (real, live mirr |
