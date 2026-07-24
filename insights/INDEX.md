# Faber Insights

Numbered, signed findings from the faber neuroevolution programme.

Recorded **as we go**, not reconstructed afterwards. An insight is written when
something is *learned*, not when something is merely done: a bug fixed is a
commit, a bug fixed that reveals a class of defect is an insight.

## Rules

- **Evidence or it didn't happen.** Cite `file:line` or a measurement.
- **Negatives are first-class.** A falsified expectation is as valuable as a
  confirmed one, often more.
- **Never overclaim.** If a result is single-run, unseeded, or unmeasured, the
  insight says so in its own text.
- **Monotonic numbering.** Never renumber, never delete. Supersede by writing a
  new insight that references the old.
- **The runner is part of the record.** From insight 047, the experiment runner is
  archived beside its raw feed and its pinned engine version. A raw feed is produced
  BY a runner: if the runner is wrong the feed is wrong and internally consistent, and
  no analysis of the feed alone can find it. Both retractions below (038, 040) were
  confounds of that kind.

## Provenance of the record

| Insights | Raw feed | Runner | Engine version | Auditable at code level |
|---|---|---|---|---|
| 001-046 | yes | no (never committed; sole survivor is EXP-023's, at `faber-tweann/test/integration/exp023_tests.erl`) | not recorded | no |
| 047 | yes (+ a preserved run-1-flawed feed) | yes, in `faber-programmes` | pinned `9bb43e6` | yes |
| 048-050 | yes (050 also keeps a run-1-artifact feed) | yes, in `faber-programmes` | pinned `9bb43e6` | yes |
| 051+ | yes | yes, archived | pinned | yes |

Stated openly rather than left implicit. The pre-047 results are not thereby wrong,
and two of them were already corrected by their own data (038, 040). They simply
cannot be re-checked against the code that produced them. None of them has yet been
re-run under the new standard: the one attempt (EXP-049, hardening 046's reliability
claim) was shelved when its controlled redesign showed no signal.

## Insights

One line per insight below (a true ToC): number, short finding, verdict, date. The
**full finding** is each insight file's own title and body; the **per-programme arc**
is in the `SYNTHESIS_P*` docs (see Programmes). Append new rows AT THE END (numbering
is chronological); verify order before committing.

| # | Finding | Verdict | Date |
|---|---|---|---|
| [001](001-faber-is-not-a-dxnn2-port.md) | faber-tweann is not a DXNN2 port | confirmed | 2026-07-20 |
| [002](002-silent-fallbacks-hide-correctness-not-just-speed.md) | Silent fallbacks hide correctness divergence, not just speed | confirmed | 2026-07-20 |
| [003](003-an-inverted-objective-looks-like-working-evolution.md) | An inverted objective is indistinguishable from working evolution without a control | confirmed | 2026-07-20 |
| [004](004-domain-sdk-path-is-not-tweann.md) | The domain_sdk path evolves weights only, not topology | confirmed | 2026-07-20 |
| [005](005-seeding-the-server-process-suffices-for-reproducibility.md) | Seeding one process makes an evolutionary run replayable | confirmed | 2026-07-20 |
| [006](006-evaluations-to-solve-is-now-measurable.md) | Evaluations-to-solve is measurable; the first number was inflated 10x | confirmed | 2026-07-20 |
| [007](007-deterministic-environments-declare-themselves.md) | Deterministic environments declare themselves; corrected pole baseline 550 | confirmed | 2026-07-20 |
| [008](008-the-scape-and-fitness-channel.md) | Restoring the scape: the fitness channel | closed by 009 | 2026-07-20 |
| [009](009-xor-solved-through-shers-path.md) | XOR evaluation completes through DXNN2's path (K1 passes structurally) | confirmed | 2026-07-20 |
| [010](010-population-loop-works-multigeneration-blocked.md) | Population loop drives evaluation; multi-generation evolution blocked | confirmed | 2026-07-20 |
| [011](011-lifecycle-fixed-evolution-improves-topology-search-is-the-wall.md) | Lifecycle + tuning fixed; topology search is the next wall | confirmed | 2026-07-20 |
| [012](012-xor-solved-tuning-attempts-was-the-lever.md) | Evolution solves XOR; tuning depth was the lever | confirmed | 2026-07-20 |
| [013](013-topology-evolution-costs-37x-on-a-known-topology-problem.md) | On XOR, topology evolution costs ~37x weight-only (as expected) | confirmed | 2026-07-20 |
| [014](014-shallow-tuning-fails-xor.md) | The shallow default tuner does not solve XOR at a matched budget | confirmed | 2026-07-20 |
| [015](015-tuner-fitness-depends-on-problem-structure.md) | The right tuner depends on problem structure (shallow=control, deep=precise-weight) | confirmed | 2026-07-20 |
| [016](016-recurrence-works-but-single-pole-is-a-weak-memory-test.md) | Recurrent evaluation works, but single-pole is too weak a memory test | confirmed + inconclusive | 2026-07-20 |
| [017](017-ltc-wired-memory-is-dead-weight-at-this-budget.md) | LTC/CfC memory is dead weight at this budget (unexploitable capability is a cost) | confirmed + signed negative | 2026-07-20 |
| [018](018-three-way-memory-none-beats-both-wiring-beats-neuron.md) | Three-way memory: none beats both, wiring beats neuron | confirmed + signed negative + characterisation | 2026-07-21 |
| [019](019-memory-pays-when-the-task-needs-it-and-fits-budget.md) | Memory pays when the task needs it and fits budget (T-maze) | confirmed + discovery | 2026-07-21 |
| [020](020-delay-sweep-refutes-horizon-story-evolvability-is-the-axis.md) | Delay sweep refutes the horizon story; the real axis is evolvability | signed negative + finding | 2026-07-21 |
| [021](021-feedforward-tmaze-topology-inconclusive.md) | Feedforward T-maze topology inconclusive (bloat confound) | inconclusive + method | 2026-07-21 |
| [022](022-feedforward-tmaze-solver-is-compact-not-a-delay-line.md) | The T-maze memory solver is compact (title corrected by 023) | confirmed + refutes 021 | 2026-07-21 |
| [023](023-delay-line-confirmed-memory-is-a-chain-computation-is-a-dag.md) | Delay-line confirmed: memory is a chain, XOR is a convergent DAG | confirmed | 2026-07-21 |
| [024](024-ga-hits-optimizer-wall-on-dpnv.md) | The truncation-GA hits an optimizer wall on DPNV; P1 closes | signed negative + inflection | 2026-07-21 |
| [025](025-mu-lambda-es-cracks-markov-not-dpnv.md) | (mu,lambda)-ES cracks the Markov pole but not DPNV | confirmed + signed negative | 2026-07-21 |
| [026](026-coevolving-tau-solves-dpnv-5of10-vs-0-representation-is-real-but-partial.md) | Co-evolving CfC tau solves DPNV 5/10 vs 0 (representation is real, partial) | confirmed + signed correction + partial | 2026-07-21 |
| [028](028-sep-cma-es-cracks-dpnv-diagonal-suffices.md) | sep-CMA-ES cracks DPNV; diagonal covariance suffices (optimizer leg confirmed) | confirmed + inflection | 2026-07-21 |
| [029](029-memory-does-not-pay-on-dpnv-optimizer-was-the-lever.md) | Memory does not demonstrably pay on DPNV once the optimizer is strong | signed negative + correction | 2026-07-21 |
| [030](030-generalisation-dpnv-overshoots-memory-still-no-advantage.md) | Generalisation DPNV overshoots; memory still shows no advantage | signed negative + inconclusive | 2026-07-21 |
| [031](031-evolved-plasticity-solves-tmaze-matching-cfc.md) | Evolved plasticity solves the T-maze, matching CfC (P3 opens positive) | confirmed + positive | 2026-07-22 |
| [032](032-delay-sweep-plasticity-horizon-invariant-cfc-cost-grows.md) | Delay sweep: plasticity horizon-invariant, CfC's cost grows | confirmed + finding | 2026-07-22 |
| [033](033-extended-delay-sweep-plasticity-scales-far-more-gracefully.md) | Extended delay sweep: plasticity scales far more gracefully | confirmed + strengthened | 2026-07-22 |
| [034](034-sensor-noise-breaks-cfc-plasticity-robust.md) | Sensor noise breaks CfC; plasticity is robust (a capability gap) | confirmed + finding | 2026-07-22 |
| [035](035-mechanism-cfc-state-is-leaky-plastic-imprint-is-stable.md) | Mechanism measured: the CfC state is leaky, the plastic imprint is stable | confirmed | 2026-07-22 |
| [036](036-oja-less-robust-than-abcd-expressiveness-buys-robustness.md) | Oja less robust than ABCD; expressiveness (not stability) buys robustness | confirmed + finding | 2026-07-22 |
| [037](037-combined-stressors-compound-for-cfc-plasticity-robust.md) | Combined stressors compound for CfC; plasticity solves the whole grid | confirmed + finding | 2026-07-22 |
| [038](038-capacity-does-not-separate-storage-from-learning-reset-protocol-confound.md) | Capacity does not separate storage from learning (a reset-protocol confound) | signed correction + method + finding | 2026-07-22 |
| [039](039-expressiveness-is-the-decisive-axis-under-capacity-plus-noise.md) | Per-connection expressiveness is the decisive axis under capacity+noise | confirmed + inflection | 2026-07-22 |
| [040](040-interference-hypothesis-refuted-crosstalk-does-not-explain-robustness.md) | The interference hypothesis is refuted; cross-talk doesn't track robustness | signed negative + method | 2026-07-22 |
| [041](041-evolvability-confirmed-only-per-connection-escapes-the-shared-basin.md) | Evolvability confirmed: only per-connection escapes the shared basin | confirmed + method | 2026-07-22 |
| [042](042-capstone-existence-reward-gated-plasticity-learns-and-relearns.md) | Existence: reward-gated plasticity genuinely learns and re-learns | confirmed + positive | 2026-07-22 |
| [043](043-capstone-thesis-does-not-transfer-mechanisms-converge-on-lifetime-learning.md) | Capstone: the memory-arc thesis does not transfer to lifetime learning | signed negative + convergence | 2026-07-22 |
| [044](044-reversal-frequency-degrades-all-mechanisms-together-null-holds.md) | Reversal frequency degrades all mechanisms together (the null holds) | signed negative + interpretation | 2026-07-22 |
| [045](045-feedback-noise-null-integration-filters-it-the-deeper-reason.md) | Feedback-noise null; integration filters it (the deeper reason) | signed negative + sharpened synthesis | 2026-07-22 |
| [046](046-post-deployment-adaptation-plasticity-recovers-a-motor-reversal-in-continuous-control.md) | Post-deployment adaptation: plasticity recovers a motor reversal (the bridge) | confirmed (bridge) + nuanced | 2026-07-22 |
| [047](047-lifetime-substrates-converge-by-tracking-not-by-low-pass.md) | Lifetime substrates converge by tracking, not low-pass (045 mechanism refuted) | signed negative (045 mechanism) + positive + method | 2026-07-23 |
| [048](048-lifetime-tracking-is-a-shallow-interaction-window-not-deep-integration.md) | Lifetime tracking is a shallow interaction window, not deep integration | signed negative (own draft) + characterisation + method | 2026-07-23 |
| [050](050-map-elites-illuminates-but-no-peak-advantage-on-an-easy-task.md) | MAP-Elites illuminates but shows no peak advantage on an easy task (P4 opener) | confirmed (illumination) + signed negative + retraction + method | 2026-07-23 |
| [051](051-novelty-search-solves-a-deceptive-maze-where-objective-and-cma-are-trapped.md) | Novelty search solves a deceptive maze (34/40) where objective-EA and strong CMA are both trapped (Lehman & Stanley replicated) | confirmed (deception) + characterisation (descriptor) + method (false-negative retracted) | 2026-07-23 |
| [052](052-novelty-buys-coverage-at-no-detectable-speed-cost-where-objective-wins.md) | Where objective already wins, novelty buys large coverage (112/~112 cells vs 79) at no detectable speed cost -- the predicted efficiency tax did not appear | confirmed (coverage) + signed negative (no tax) + method (false-INVALID corrected) | 2026-07-23 |
| [053](053-red-queen-demonstrated-cofitness-blind-to-progress-in-a-1d-coevolution-game.md) | P7 opener: the Red Queen -- in a 1D coevolution game the trait escalates (x 50->86) while co-fitness stays ~0.5 with no trend (blind to progress); co-fitness misleads, a fixed benchmark is needed | confirmed (Red Queen, minimal) + method (design-gated + estimator fix) | 2026-07-23 |
| [054](054-a-fixed-benchmark-goes-blind-if-too-weak.md) | P7 rung 2: even a FIXED benchmark goes blind if too weak -- a weak reference set saturates (blind to ~31 of ~35 units of progress) while a graded one tracks; "fixed" is not enough, it must be graded | confirmed (benchmark strength) + method (design-gated redesign + metric fix) | 2026-07-23 |
| [055](055-a-misaligned-selection-proxy-decouples-from-true-quality-only-under-asymmetric-costs.md) | P7 rung 3: a misaligned (compensatory sum) SELECTION proxy is benign under symmetric costs but decouples from conjunctive true quality under ASYMMETRIC costs (pumps the cheap dim, abandons the bottleneck); only a graded aligned benchmark reveals it | confirmed (conditional decoupling) + method (design-gated redesign from tautology + direction-aware fix) | 2026-07-23 |
| [056](056-under-cycling-a-fixed-benchmark-is-honest-but-blind.md) | P7 rung 4: under structural CYCLING (intransitive game, population rotates ~8 turns, 1055 intransitive triples) a fixed benchmark is HONEST (no false progress, delta ~0) but BLIND to the cyclic structure -- only a master-tournament sees it; completes the 053-056 toolkit | confirmed (honest-but-blind) + method (design-gated + Spearman-artifact fix) | 2026-07-23 |
| [057](057-embodied-pursuit-evasion-coevolves-monotonically-no-cycling-pursuer-sustains-evader-marginal.md) | P7 EMBODIED rung: real [2,6,4] nets coevolving on 9x9-torus pursuit-evasion at FOUR crossover brackets (m=18/14/13/12, both catch-rate plateaus, n=60) make MONOTONE progress with NO cycling (master-tournament triples=0 at bands 0.05-0.15); the PURSUER sustains net frozen-benchmark progress everywhere (~0.14-0.23) and the EVADER only WEAKLY (~0.06-0.10), both roughly flat across the balance; the evader balance-contingency is REFUTED as unresolvable at this scale (both the evader "step" and a pursuer speed-edge trend from smaller runs were low-n noise; widest extreme pair unresolved); a DECOUPLED CONTROL (two static baselines, reproduced to n=80) refuses coevolutionary coupling over a DIVERSE opponent (coevo ties a frozen-random opponent; a n=60 pursuer hint did not reproduce at n=80; it beats a NARROW strong opponent but that is curriculum-diversity, not coupling) -- so two-sided PROGRESS, NOT an arms race | confirmed (monotone, no cycling; pursuer sustains) + refuted (no coupling over a diverse static opponent; not an arms race) + method (benchmark-grading fix + 2 CLAIM gates + decoupled control x2 baselines, reproduced) | 2026-07-23 |
| [058](058-flatland-instrument-scape-works-net-forages-and-hunts-benchmark-decomposes-057-refusal-reproduces.md) | Flatland front rung 1 (INSTRUMENT, no dynamics claim): built flatland_sim (pure 2D ALife world engine, geometry/sensing identical to 057 + energy economy). Scape works (greedy forages/survives, random starves); a [5,6,4] net EVOLVES foraging (=greedy) AND hunting (=greedy); the benchmark DECOMPOSES foraging/fleeing/capture orthogonally (pure-forager 27.6/0.31 vs pure-fleer 3.5/0.69, each graded); and 057's no-coupling refusal REPRODUCES on the graded instrument (pure pursuit-evasion, plants off, n=40: predator/prey coevo NET not exceeding static) -- so not a saturation artifact, and rung 2's ecology head-to-head is clean | confirmed (instrument validated; benchmark decomposes; 057 refusal robust) + method (DESIGN-gated instrument-before-claim) | 2026-07-23 |
| [059](059-flatland-ecological-survival-fitness-never-learns-reactive-fleeing-and-lowers-foraging.md) | Flatland front rung 2 (prey side): under ecological survival fitness (forage-to-not-starve + flee-to-not-be-caught), REACTIVE FLEEING is essentially never learned -- the 058 flee axis stays at ~0.29 (barely above the gen-0 random-net floor 0.254, vs a dedicated fleer 0.39-0.69) in EVERY predator condition including no-predator, so no forage/reactive-flee frontier can be OBSERVED (fleeing not learned, not a declined trade); predator PRESENCE lowers realized foraging (24.5->3.4-4.5, resolved; mechanism unidentified, confounded with EA budget); COEVO-vs-static an UNDERPOWERED NULL (n=20 single-run). The 058 reactive-flee axis is the wrong second axis; the missing instrument is an encounter-conditional AVOIDANCE metric | confirmed (flee never learned; presence lowers foraging) + refuted (no observable frontier) + underpowered (coupling null) + method (2 gates: design REDESIGN + claim SIGN-WITH-CHANGES) | 2026-07-24 |
| [060](060-flatland-pressured-prey-shows-near-predator-repulsion-above-chance.md) | Flatland front rung 2b: on an INTERVENTIONAL single-step probe (predator at ASSIGNED distance, no plant -> plant-independent), the PRESSURED prey steps AWAY from a near (d=2) predator above chance (avoid_near 0.500 vs the analytic chance 0.250 for both the naive forager AND the capture-disabled proximity-control; PRESSURED-minus-PROXIMITY +0.250 RESOLVED and REPRODUCED in 2 runs, raw values a genuine upward shift) -- a real anti-predator behaviour requiring the CAPTURE gradient (not sensor exposure, since the proximity-control stays at chance) that the 058 reactive-flee axis could not see. NARROW: single-step static-probe REPULSION not whole-episode fleeing (reconciles with 059); the pre-registered forage sanity gate TRIPPED so forage/plant-abandonment readouts dropped; and whether this repulsion DRIVES the 059 foraging collapse is UNTESTED (repulsion + collapse measured separately, no mediation) | confirmed (near-predator repulsion, reproduced, capture-gradient-driven) + open (link to the 059 collapse untested) + method (design REDESIGN obs->interventional; claim DO-NOT-SIGN -> reproduce+descope) | 2026-07-24 |

## Programmes

Full map and per-programme charters: [`plans/PLAN_PROGRAMMES_ROOT.md`](../plans/PLAN_PROGRAMMES_ROOT.md).

**P3 synthesis (031-048, memory + lifetime learning):** [`plans/SYNTHESIS_P3.md`](../plans/SYNTHESIS_P3.md).
One engine (P1-P7, the seven design axes), two couplings (P8 language, P9 body).

- **P1 — Capabilities** (insights 013-024, CLOSED): which network capability (tuner depth, substrate, memory mechanism, topology evolution) helps which problem. Arc: capability value = task-match x budget x optimizer; closed at the optimizer wall (024). [`CHARTER_P1_CAPABILITIES.md`](../plans/CHARTER_P1_CAPABILITIES.md) / [`PLAN_EXPERIMENT_PROGRAMME.md`](../plans/PLAN_EXPERIMENT_PROGRAMME.md).
- **P2 — Search Strategies** (insights 025+, ACTIVE): which search algorithm (GA / (mu,lambda)-ES / CMA-ES / CoSyNE / natural-ES), for which landscape; does a stronger optimizer overturn P1. [`CHARTER_P2_SEARCH_STRATEGIES.md`](../plans/CHARTER_P2_SEARCH_STRATEGIES.md) / [`PLAN_SEARCH_STRATEGIES.md`](../plans/PLAN_SEARCH_STRATEGIES.md).
- **P3 — Meta-learning** (CLOSED, insights 031-048): evolved plasticity; memory-by-learning vs storage; lifetime learning is a shallow interaction window, not deep integration. [`CHARTER_P3_META_LEARNING.md`](../plans/CHARTER_P3_META_LEARNING.md) / [`SYNTHESIS_P3.md`](../plans/SYNTHESIS_P3.md).
- **P4 — Objectives** (insights 050-052, characterised 2026-07-23): Quality-Diversity / selection pressure. 050 = first QD machinery + a modest illumination advantage, no peak advantage on an easy task. 051 = deception replicated on a CONSTRUCTED deceptive maze: final-position novelty solves 34/40 where objective-EA and strong sep-CMA-ES are both trapped. 052 = where objective already wins, novelty buys large coverage (112/~112 cells vs 79) at no detectable speed cost (the "efficiency tax" did not appear; formally inconclusive for strict equivalence). Arc: novelty reliably buys coverage, is the answer to deception, and carries no measurable penalty on non-deceptive tasks -> coverage becomes the objective in open-ended worlds (P7). [`CHARTER_P4_OBJECTIVES.md`](../plans/CHARTER_P4_OBJECTIVES.md).
- **P5 — Scale / Substrate** (OPEN): island / distributed / federated evolution across the mesh. [`CHARTER_P5_SCALE_SUBSTRATE.md`](../plans/CHARTER_P5_SCALE_SUBSTRATE.md).
- **P6 — Encoding / Development** (OPEN): direct vs indirect / generative encoding. [`CHARTER_P6_ENCODING_DEVELOPMENT.md`](../plans/CHARTER_P6_ENCODING_DEVELOPMENT.md).
- **P7 — Coevolution / Self-play** (OPENED 2026-07-23, insights 053-057): interaction, arms races, open-endedness, toward Flatland/ALife. A GRADUAL ladder (smallest steps first). Numbers-game toolkit (053-056), each validated where truth is knowable: 053 = the Red Queen (co-fitness blind to progress, use a fixed benchmark); 054 = a fixed benchmark must be GRADED (a too-weak one saturates); 055 = a misaligned SELECTION proxy decouples from true quality under asymmetric costs; 056 = under CYCLING a fixed benchmark is HONEST but BLIND (use a master-tournament). **057 = the EMBODIED rung**: the toolkit applied to real [2,6,4] nets on 9x9-torus pursuit-evasion at both crossover brackets -> MONOTONE progress, NO cycling (master-tournament triples=0); the PURSUER sustains net frozen-benchmark progress at both, the EVADER is MARGINAL (clears the criterion at the evader-favoured bracket, touches 0 at the pursuer-favoured, between-bracket diff unresolved). A DECOUPLED CONTROL (coevolution vs a frozen gen-0 static opponent, same benchmark, n=40) REFUSES coevolutionary coupling: coevo NET does not exceed static on any side at either bracket (all four diff CIs include 0) -- so this is two-sided PROGRESS, NOT a demonstrated arms race (each side adapts to any opponent gradient, not reciprocal escalation). Two CLAIM-gate passes deflated the headline from "arms race/fragile/replicates N&F"; the control then settled it. **NEXT: stronger static baseline + third bracket (m=12) + larger n**, then toward Flatland (P5/P6 structure). [`CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
- **P8 — LLM-augmented Evolution** (HORIZON, coupling): LLM operators; the Spartan bridge. [`CHARTER_P8_LLM_AUGMENTED.md`](../plans/CHARTER_P8_LLM_AUGMENTED.md).
- **P9 — DARS / Physical AI** (HORIZON, coupling): distributed autonomous robotic systems; embodied evolution. [`CHARTER_P9_DARS.md`](../plans/CHARTER_P9_DARS.md).

## Prior art

- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md) — every insight 013-020 independently re-derives an established result (NFL, NEAT complexification, memetic local-search balance, DPNV benchmark, LTC/CfC, T-maze memory, reservoir/TDNN). Calibration: reproducible confirmation on the DXNN2/Erlang substrate, NOT discovery.

## Related

- [`plans/PLAN_FABER_FOUNDATION.md`](../plans/PLAN_FABER_FOUNDATION.md) — the work programme
- [`docs/CONFORMANCE.md`](../docs/CONFORMANCE.md) — DXNN2 module gap analysis
- [`docs/PROTOCOL.md`](../docs/PROTOCOL.md) — message protocol comparison
- [`docs/CLAIMS_AUDIT.md`](../docs/CLAIMS_AUDIT.md) — documentation claims audit
| [061](061-flatland-open-population-fixed-greedy-behaviours-collapse-no-coexistence.md) | Flatland front rung 3a (INSTRUMENT; DESIGN gate split the open-population arms-race question -- foregone + demography-confounded -- into the coexistence/dynamics instrument first): with FIXED-GREEDY behaviours the open many-agent population (endogenous reproduction/energy/death) has NO coexistence regime -- it boom-busts to mutual extinction (median time-to-extinction 39 steps << 300-step burn-in) across the plant-density x initial-predator grid at DEFAULT stabilisers (0/72) AND at the STRONG-stabiliser corner LV theory prefers (satiate=12, percept=2 = large refuge; 0/72), despite functional-response satiation, a predator speed disadvantage, and limited perception. Greedy-hunt cannot self-limit predation. SCOPE: a negative about fixed-greedy behaviours in THIS game, NOT that open populations can't coexist in general NOR that evolved behaviours can't. 3a REFRAMES 3b: not "does it arms-race" but "can EVOLVED behaviours discover self-limiting predation and coexist where fixed-greedy collapses?" | confirmed negative (no coexistence, fixed-greedy, both grids; median TTE 39) + method (design REDESIGN arms-race->instrument; claim SIGN-WITH-CHANGES strong-corner+TTE+scoping) | 2026-07-24 |
