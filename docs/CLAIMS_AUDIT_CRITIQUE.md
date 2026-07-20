## What the audit missed

The audit covered README and guide markdown. It did not open source files, build files, changelogs, or roadmaps. That is where the largest remaining concentration of unbacked claims sits.

### 1. EDoc comments in `src/` — the biggest gap, entirely unaudited

`rebar.config` in both packages sets `{edoc_opts, [{source_path, ["src"]}]}` and ships `"src"` in `{hex, {files, ...}}`, so these comments render on hexdocs alongside the guides. They carry exactly the performance claims v2.0.0 withdrew from the READMEs.

**faber-tweann:**
- `src/signal_aggregator.erl:160` — "Performance: 40-100x faster than pure Erlang for N > 10 inputs." **Unbacked.** `ROADMAP.md` §6 states no committed benchmark output exists; `test/benchmark/bench_nif_vs_erlang.erl` has never had recorded numbers. This is a third mutually inconsistent figure alongside the withdrawn 30-200x and 10-15x.
- `src/faber_nn_nifs.erl:85`, `:396`, and the `@doc` lines at `:399`, `:404`, `:409` — "SIMD Batch Activations", "Apply tanh activation to a batch of values using SIMD" (likewise sigmoid, ReLU). **Misleading.** `ROADMAP.md` §6 is explicit that `native/faber_nn_nifs/src/lib.rs` contains zero SIMD: no `std::arch`, no `target_feature`, no `rayon`. The `@doc` on lines 399/404/409 is worse than a section heading, because it renders as the function's own documentation on hexdocs.
- `src/signal_aggregator.erl:186` — "cache-friendly and suitable for SIMD vectorization". Hedged into the future tense; **backed** as written, but sits adjacent to the false ones and should probably go with them.
- `src/genotype.erl:11` — module-level `@doc`: "A genotype is a collection of interconnected elements stored in Mnesia". **Misleading**, same defect the audit caught in `CLAUDE.md` and the guides, but this one is the module doc header on hexdocs. Note `genotype.erl:85` and `:166` already contain the corrected ETS statements, so the file contradicts itself.
- `src/constructor.erl:11` and `:81` — "Read agent genotype from Mnesia database". **Misleading.** Compounded by the fact that `constructor.erl` has zero callers (per ROADMAP §1).
- `src/neuron_info.erl:158` and `src/ltc_dynamics.erl:34` — "~100x faster than LTC ODE". Same claim the audit rated `partial` in `guides/neuron-types.md`; the source copies were not flagged. Whatever verdict lands on the guide should be applied to these two.
- `include/records.hrl:10`, `:253-269`, `:290`, `:309` — describe the `#substrate{}` record as "Used in HyperNEAT for evolving larger-scale networks through compositional pattern producing networks (CPPNs)". **Misleading**: the record is scaffolding with no implementation (`genotype.erl:271` raises `substrate_not_implemented`). `include` is in the hex files list, so this ships.

**faber-neuroevolution — no source file in this repo was audited at all:**
- `src/evaluate/nif_network.erl:5`, `:20`, `:150` — "50-100x faster than pure Erlang" (three times, including inside a usage example). **Unbacked.**
- `src/evaluate/neuroevolution_evaluator.erl:99`, `:109` — "50-100x faster network evaluation". **Unbacked.**
- `src/evolve/network_factory.erl:44` and `src/evolve/genome_factory.erl:107` — "50-100x faster evaluation". **Unbacked.**
- `src/evolve/neuroevolution_speciation.erl:229` — "30-50x faster". **Unbacked.**

That is a fifth and sixth distinct speedup range in a codebase whose sibling package just withdrew all such figures. Several also say "Uses NIF acceleration **when available**" — an architectural claim describing the silent auto-detect fallback that faber_tweann v2.0.0 removed by design.

### 2. Build-file and package-metadata claims — a category with zero coverage

- `faber-tweann/src/faber_tweann.app.src:9` declares `mnesia` in `{applications, [...]}`. **Misleading**, and it is a functional claim, not just prose: it forces the mnesia application to start for every consumer of a package that makes no `mnesia:` call. `ROADMAP.md` §3 lists removing it as pending cleanup, so this is known; the audit simply never looked at `.app.src`.
- `faber-neuroevolution/rebar.config:15` declares `{faber_tweann, "~> 1.0"}` while `faber-tweann/src/faber_tweann.app.src` is at `vsn 2.0.0`. **Misleading dependency claim.** `~> 1.0` cannot resolve to 2.0.0, so faber_neuroevolution 1.2.3 pins itself to the pre-correction faber_tweann — the one with the silent fallback and the four contract bugs the v2.0.0 changelog documents. Meanwhile `faber-ecosystem/README.md` tells users to install `{faber_tweann, "~> 2.0"}` and `{faber_neuroevolution, "~> 1.2"}` together, which is unsatisfiable as written. The same stale `~> 1.0` appears three more times in the `mesh` and `mesh_test` profiles.
- `faber-neuroevolution/rebar.config` mesh profiles pull `{macula, {git, "https://github.com/rgfaber/macula.git", {tag, "v0.14.2"}}}`. **Unbacked dependency claim** — macula lives under `macula-io` on Codeberg, not `rgfaber` on GitHub. `guides/interoperability.md`'s `macula_rpc:register_method/2` was already flagged unbacked; this is the build-file half of the same fiction.
- `.app.src` `{links, ...}` in both faber-tweann and faber-neuroevolution point at `https://github.com/rgfaber/...`. These are the links hex.pm renders on the package page. Codeberg is canonical and every other corrected document now says so. **Misleading**, low severity.
- `faber-nn-nifs/src/faber_nn_nifs.app.src` description is `"DEPRECATED - absorbed into faber_tweann v2.0.0"` — good, and it means the confirmed-list entry quoting `"High-performance Rust NIFs for Faber Neuroevolution (Enterprise)"` for that file is **stale**. See §5.

### 3. Changelogs

faber-tweann has **two** changelogs and only the second ships.

- `faber-tweann/design_docs/releases/CHANGELOG.md` is the one in `{ex_doc, {extras, ...}}` and `{hex, {files, ...}}`. Its `[2.0.0]` entry is exemplary. But the retained `[0.16.0]` section still reads "**Enterprise NIF Package Support** — Added automatic detection and integration with the separate `faber_nn_nifs` enterprise package, providing 10-15x performance improvements". The v2.0.0 entry twelve lines above states there was never an enterprise edition and that no figure was ever measured. Historical changelog entries are normally sacrosanct, so this is a **judgement call rather than a clear defect** — but a reader landing on the hexdocs Changelog page sees the withdrawn framing and the withdrawn number presented as fact.
- `faber-tweann/CHANGELOG.md` (repo root) is a *different file*, stops at `[1.2.0]`, and is in neither the extras list nor the hex files list. Its `[1.0.0]` section still claims "**CfC Mode**: ... (~100x faster than ODE)", "**NIF Acceleration**: Optional Rust NIF support via `faber_nn_nifs` (10-200x speedup)", and "**863 tests passing**". **Misleading**, and it is the file a Codeberg visitor opens by reflex. The 10-200x figure here is a *seventh* range and does not match the 30-200x the v2.0.0 entry says was withdrawn.
- `faber-nn-nifs/CHANGELOG.md` is in that package's `{ex_doc, {extras, ...}}`. Its `[1.0.0]` feature list still advertises "**SIMD Batch Activations**: `tanh_batch/1`, `sigmoid_batch/1`, `relu_batch/1`, `softmax_batch/1`". **Misleading**, same zero-SIMD finding. The audit flagged the identical wording in that repo's README but not the changelog three files away.

### 4. Docs that exist but are not shipped — a structural asymmetry

`faber-tweann/ROADMAP.md` is excellent and is correctly wired into both `{ex_doc, {extras, ...}}` and `{hex, {files, ...}}`.

`faber-neuroevolution/ROADMAP.md` is equally good and appears in **neither**. I grepped the whole of that `rebar.config` for "ROADMAP" and got nothing. So faber-neuroevolution's corrective document — the one stating that the meta-controller does not learn and that the silos are unwired — never reaches a hexdocs or hex.pm reader, while the overclaiming guides all do. `faber-ecosystem/README.md` links to it at `/src/branch/master/ROADMAP.md`, which resolves (both repos are on `master`), but only for someone who follows the link off-site.

### 5. Likely false positives in the confirmed list

- **`src/faber_nn_nifs.app.src` "(Enterprise)" description.** The current file at `faber-nn-nifs/src/faber_nn_nifs.app.src` reads `{description, "DEPRECATED - absorbed into faber_tweann v2.0.0"}`. The quoted string is not there. Either the audit read a stale copy or it read `faber-tweann/src/`'s absorbed version — I checked and it is not in either. Recommend re-verifying before acting.
- **The eight silo API entries and the three `neuroevolution_server` entries, marked `unbacked`.** The verdict is right at the function level but wrong at the module level, and the distinction matters for how you fix them. Every one of these modules exists — they are just nested one level deeper than a flat `src/*.erl` scan finds them (`src/silos/temporal_silo/temporal_silo.erl`, `src/evolve/neuroevolution_server.erl`, and so on). Each has a real, substantial export list that simply does not contain the documented function:
  - `neuroevolution_server` exports `start_link/1,2`, `start_training/1`, `stop_training/1`, `get_stats/1`, `get_population/1`, `get_last_evaluated_population/1`, `get_population_snapshot/1`, `update_config/2` — not `evolve/2`, `get_best/1` or `stop/1`. Note the README's own Quick Start correctly uses `start_link/1` + `start_training/1`, so `guides/domain-sdk.md` contradicts the README rather than the code.
  - `temporal_silo` has `record_early_termination/1`, not `should_terminate_early/2`. `morphological_silo` has `record_network_size/4` and `get_complexity_stats/1`, not `trigger_pruning/2`. `economic_silo` has `get_wealth_distribution/1`, not `redistribute_wealth/1`. `cultural_silo` has `record_innovation/4` and `promote_to_tradition/2`, not `get_cultural_bonuses/2`. `ecological_silo` has `register_niche/3` and `update_resource_pool/3`, not `introduce_disease/2`. `communication_silo` has `get_vocabulary/1` and `get_communication_stats/1`, not `get_dialect_info/1`. `regulatory_silo` has `add_epigenetic_mark/3`, not `inherit_marks/3`.
  - `neuroevolution_behavioral_events` exports 30+ event constructors (`offspring_born/1`, `champion_crowned/1`, …) plus `make_meta/1,2`, `event_to_map/1`, `stream_id/2` — but no `subscribe/2`, `get_events/2` or `configure/1`. The guide documents a pub/sub API over what is actually a plain event-struct constructor module.
  
  These are **misleading**, not unbacked: real modules with invented APIs bolted onto them. That is a documentation-rewrite job against a live export list, not a delete-the-section job. `lineage_tree_projection`, `portfolio_manager` and `network_evaluator` (in the neuroevolution repo) genuinely do not exist anywhere, so those entries stand as `unbacked`.

### 6. Claims the audit found one of, but not its siblings

`faber-neuroevolution/README.md` — the audit caught "**Transfer of meta-knowledge** - Training strategies can transfer across domains" but left the four claims surrounding it in the same block. That repo's own `ROADMAP.md` §1 says in terms: "the README must not describe the meta-controller as learning or adapting." Still present:
- Features list: "**LTC Meta-Controller** - Adaptive hyperparameter optimization using Liquid Time-Constant networks"
- "the system *learns how to learn* at multiple timescales"
- "**Self-tuning hyperparameters** - Mutation rate, selection ratio adapt automatically"
- "**Automatic stagnation recovery** - Detects and escapes local optima"
- "**Phase-appropriate strategies** - Different strategies for exploration vs exploitation"

All **misleading** on the same evidence: `meta_controller`'s `ltc_weights` are set in `init` and `reset` and nowhere else, and `meta_trainer`'s four gradient functions have zero callers. The forward pass is real, so this is a fixed random projection, not adaptation.

### 7. Categories with genuinely nothing to report

Worth stating so nobody re-runs them:

- **Licenses.** All three code packages declare `{licenses, ["Apache-2.0"]}` and carry an Apache-2.0 `LICENSE`; faber-tweann's README badge matches. Consistent. `faber-ecosystem` is MIT and its badge says MIT — also consistent, though the divergence between the umbrella docs repo (MIT) and every package it documents (Apache-2.0) is worth a deliberate decision rather than an accident.
- **Version snippets, mostly.** `faber-neuroevolution/README.md`'s `{faber_neuroevolution, "~> 1.2"}` matches `vsn 1.2.3`; `faber-ecosystem/README.md`'s `~> 2.0` / `~> 1.2` pair matches both `.app.src` files. The failures are the ones in §2 (the internal `~> 1.0` dep) and the already-confirmed stale `0.16.0`/`0.17.0` snippets in `faber-tweann/guides/installation.md`.
- **`guides/native-nifs.md`** is clean and is the model the other guides should be rewritten against — it names the fallback module correctly, documents `{nif_impl, nif | fallback}`, and explicitly refuses to quote a speedup. I verified `tweann_nif:impl/0` and `is_loaded/0` both exist at `src/tweann_nif.erl:56-57`.

### 8. One further defect, in already-corrected text

`faber-ecosystem/README.md`'s Basic Usage snippet — the one carrying a footnote explaining that it replaced an earlier fabricated snippet — is still wrong on the last two lines:

```erlang
Network = network_evaluator:from_genotype(AgentId),
Outputs = network_evaluator:evaluate(Network, [0.5, 0.8]).
```

`network_evaluator:from_genotype/1` is specced `-spec from_genotype(term()) -> {ok, network()} | {error, term()}` at `faber-tweann/src/network_evaluator.erl:235`. It returns a tagged tuple, so `Network` binds `{ok, Net}` and the `evaluate/2` call on the next line gets a tuple where it wants a network. `genotype:construct_Agent/3` and `network_evaluator:evaluate/2` and `from_genotype/1` all exist with the stated arities, so this is narrower than the fabrications it replaced — but the snippet as printed does not run. **Misleading.** Worth checking whether the same return-shape slip propagated into `faber-tweann/guides/quickstart.md`, whose `genotype:read/1` line the audit already flagged.
