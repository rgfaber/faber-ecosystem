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

## Insights

| # | Title | Verdict | Date |
|---|---|---|---|
| [001](001-faber-is-not-a-dxnn2-port.md) | faber-tweann is not a DXNN2 port | confirmed | 2026-07-20 |
| [002](002-silent-fallbacks-hide-correctness-not-just-speed.md) | Silent fallbacks hide correctness divergence, not just performance | confirmed | 2026-07-20 |
| [003](003-an-inverted-objective-looks-like-working-evolution.md) | An inverted objective is indistinguishable from working evolution without a control | confirmed | 2026-07-20 |
| [004](004-domain-sdk-path-is-not-tweann.md) | The domain_sdk path evolves weights only, not topology | confirmed | 2026-07-20 |
| [005](005-seeding-the-server-process-suffices-for-reproducibility.md) | Seeding one process makes an evolutionary run replayable | confirmed | 2026-07-20 |
| [006](006-evaluations-to-solve-is-now-measurable.md) | Evaluations-to-solve is measurable; first number is inflated 10x | confirmed | 2026-07-20 |
| [007](007-deterministic-environments-declare-themselves.md) | Deterministic environments declare themselves; corrected baseline is 550 | confirmed | 2026-07-20 |
| [008](008-the-scape-and-fitness-channel.md) | Restoring the scape: the fitness channel (Phase 3, part 1) | closed by 009 | 2026-07-20 |
| [009](009-xor-solved-through-shers-path.md) | Evaluation completes through DXNN2's path; K1 passes structurally | confirmed | 2026-07-20 |
| [010](010-population-loop-works-multigeneration-blocked.md) | Population loop drives Sher-path evaluation; multi-generation evolution blocked | confirmed | 2026-07-20 |
| [011](011-lifecycle-fixed-evolution-improves-topology-search-is-the-wall.md) | Lifecycle + tuning fixed; evolution improves; topology search is the next wall | confirmed | 2026-07-20 |
| [012](012-xor-solved-tuning-attempts-was-the-lever.md) | Evolution SOLVES XOR through DXNN2's path; tuning depth was the lever | confirmed | 2026-07-20 |
| [013](013-topology-evolution-costs-37x-on-a-known-topology-problem.md) | On XOR, topology evolution costs ~37x weight-only (as expected); our tuner is also crude | confirmed | 2026-07-20 |
| [014](014-shallow-tuning-fails-xor.md) | DXNN2's shallow default tuner does NOT solve XOR at a matched budget; the crude tuner's depth was not waste | confirmed | 2026-07-20 |
| [015](015-tuner-fitness-depends-on-problem-structure.md) | The right tuner depends on problem structure: shallow wins control (pole), deep wins precise-weight (XOR); XOR was out of domain | confirmed | 2026-07-20 |
| [016](016-recurrence-works-but-single-pole-is-a-weak-memory-test.md) | Recurrent evaluation works (validated); but single-pole-no-velocity is too weak to prove recurrence beats feedforward — real test is double pole | confirmed + inconclusive | 2026-07-20 |
| [017](017-ltc-wired-memory-is-dead-weight-at-this-budget.md) | LTC/CfC now runs through evolution; adding memory HURT on double-pole at 30 gens — third instance of "unexploitable capability is a cost" (cf 013, 015) | confirmed + signed negative | 2026-07-20 |
| [018](018-three-way-memory-none-beats-both-wiring-beats-neuron.md) | Three-way memory (none/wiring/neuron) at 60 gens: none beats both, wiring beats neuron, CfC smoothing hurts fast control; recurrent fix (EXP_020) validated at scale | confirmed + signed negative + characterisation | 2026-07-21 |
| [019](019-memory-pays-when-the-task-needs-it-and-fits-budget.md) | Memory pays decisively on the T-maze (cfc solves in 1 gen); capability value = task-match x budget; feedforward+FIFO is a 4th variant (delay-line/TDNN) | confirmed + discovery | 2026-07-21 |
| [020](020-delay-sweep-refutes-horizon-story-evolvability-is-the-axis.md) | Delay sweep REFUTES the bounded-horizon story; real axis is evolvability (recurrent memory found gen 2-4 vs feedforward gen 14-23); n=2 too noisy; corrects 019's delay-line label | signed negative + finding | 2026-07-21 |
| [021](021-feedforward-tmaze-topology-inconclusive.md) | Topology inspection inconclusive (fitness not persisted to genotype); evolved feedforward T-maze agents are deep near-linear chains, delay-independent, but confounded by bloat | inconclusive + method | 2026-07-21 |
| [022](022-feedforward-tmaze-solver-is-compact-not-a-delay-line.md) | Fitness-persistence fix isolates the SOLVER: delay-2 feedforward memory solver is COMPACT (3 neurons); (title corrected by 023: it IS a short delay line) | confirmed + refutes 021 | 2026-07-21 |
| [023](023-delay-line-confirmed-memory-is-a-chain-computation-is-a-dag.md) | Delay-line CONFIRMED by wiring: memory solver is a LINEAR CHAIN, XOR solver is a convergent DAG; resolves the 019->020->022 flip-flop | confirmed | 2026-07-21 |
| [024](024-ga-hits-optimizer-wall-on-dpnv.md) | The truncation-GA hits an OPTIMIZER wall on DPNV: no solve in 165k evals, lottery-like variance, no memory benefit; capability value = task-match x budget x OPTIMIZER. Programme 1 closes | signed negative + inflection | 2026-07-21 |
| [025](025-mu-lambda-es-cracks-markov-not-dpnv.md) | [P2] (mu,lambda)-ES solves Markov pole process-free (~70x faster wall-clock) but NOT DPNV even at 400k evals; wall is optimizer AND representation (fixed-tau CfC) | confirmed + signed negative | 2026-07-21 |
| [026](026-coevolving-tau-solves-dpnv-5of10-vs-0-representation-is-real-but-partial.md) | [P2] Co-evolving CfC tau solves DPNV 5/10 vs 0/10 for the honest random-tau baseline (Fisher p~0.03): representation is a real, significant factor, but a partial fix (optimizer leg stays live); solvers reach tau~4-8, above the [0.1,2.0] init band. n=1 smoke's near-total effect did NOT survive n=10. 026c: wider-range [0.1,8.0] fixed tau 2/10 (intermediate, indistinguishable from both at n=10) | confirmed + signed correction + partial | 2026-07-21 |
| [028](028-sep-cma-es-cracks-dpnv-diagonal-suffices.md) | [P2] sep-CMA-ES cracks DPNV: 6/10 weights-only (vs plain-ES 0/10, p~0.01) and 10/10 with co-evolved tau (vs 5/10, p~0.03). Optimizer leg CONFIRMED; representation+optimizer compound to the first reliable DPNV solve; DIAGONAL covariance suffices (no full CMA-ES, no eig NIF); DPNV fork resolves to optimizer+representation, NOT deception | confirmed + inflection | 2026-07-21 |

## Programmes

Full map and per-programme charters: [`plans/PLAN_PROGRAMMES_ROOT.md`](../plans/PLAN_PROGRAMMES_ROOT.md).
One engine (P1-P7, the seven design axes), two couplings (P8 language, P9 body).

- **P1 — Capabilities** (insights 013-024, CLOSED): which network capability (tuner depth, substrate, memory mechanism, topology evolution) helps which problem. Arc: capability value = task-match x budget x optimizer; closed at the optimizer wall (024). [`CHARTER_P1_CAPABILITIES.md`](../plans/CHARTER_P1_CAPABILITIES.md) / [`PLAN_EXPERIMENT_PROGRAMME.md`](../plans/PLAN_EXPERIMENT_PROGRAMME.md).
- **P2 — Search Strategies** (insights 025+, ACTIVE): which search algorithm (GA / (mu,lambda)-ES / CMA-ES / CoSyNE / natural-ES), for which landscape; does a stronger optimizer overturn P1. [`CHARTER_P2_SEARCH_STRATEGIES.md`](../plans/CHARTER_P2_SEARCH_STRATEGIES.md) / [`PLAN_SEARCH_STRATEGIES.md`](../plans/PLAN_SEARCH_STRATEGIES.md).
- **P3 — Meta-learning** (NEXT): learn to learn; evolved plasticity; the representation leg of the DPNV fork (EXP-026). [`CHARTER_P3_META_LEARNING.md`](../plans/CHARTER_P3_META_LEARNING.md).
- **P4 — Objectives** (OPEN): QD / novelty; the deception leg of the DPNV fork. [`CHARTER_P4_OBJECTIVES.md`](../plans/CHARTER_P4_OBJECTIVES.md).
- **P5 — Scale / Substrate** (OPEN): island / distributed / federated evolution across the mesh. [`CHARTER_P5_SCALE_SUBSTRATE.md`](../plans/CHARTER_P5_SCALE_SUBSTRATE.md).
- **P6 — Encoding / Development** (OPEN): direct vs indirect / generative encoding. [`CHARTER_P6_ENCODING_DEVELOPMENT.md`](../plans/CHARTER_P6_ENCODING_DEVELOPMENT.md).
- **P7 — Coevolution / Self-play** (OPEN, horizon): interaction, arms races, open-endedness. [`CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
- **P8 — LLM-augmented Evolution** (HORIZON, coupling): LLM operators; the Spartan bridge. [`CHARTER_P8_LLM_AUGMENTED.md`](../plans/CHARTER_P8_LLM_AUGMENTED.md).
- **P9 — DARS / Physical AI** (HORIZON, coupling): distributed autonomous robotic systems; embodied evolution. [`CHARTER_P9_DARS.md`](../plans/CHARTER_P9_DARS.md).

## Prior art

- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md) — every insight 013-020 independently re-derives an established result (NFL, NEAT complexification, memetic local-search balance, DPNV benchmark, LTC/CfC, T-maze memory, reservoir/TDNN). Calibration: reproducible confirmation on the DXNN2/Erlang substrate, NOT discovery.

## Related

- [`plans/PLAN_FABER_FOUNDATION.md`](../plans/PLAN_FABER_FOUNDATION.md) — the work programme
- [`docs/CONFORMANCE.md`](../docs/CONFORMANCE.md) — DXNN2 module gap analysis
- [`docs/PROTOCOL.md`](../docs/PROTOCOL.md) — message protocol comparison
- [`docs/CLAIMS_AUDIT.md`](../docs/CLAIMS_AUDIT.md) — documentation claims audit
