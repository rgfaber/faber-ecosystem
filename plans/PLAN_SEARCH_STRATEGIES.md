# Programme 2 — Search Strategies (Optimizers)

The second faber research programme. Where Programme 1 (Capabilities,
`PLAN_EXPERIMENT_PROGRAMME.md`) asked *which network capability helps which
problem*, holding the SEARCH fixed (a truncation-selection GA + memetic tuner),
Programme 2 asks *which search algorithm is best, for which landscape* — and
whether a stronger optimizer overturns Programme 1's conclusions.

## Why this programme exists

Programme 1 characterised the capability space as far as its optimizer allowed,
and in doing so hit that optimizer's wall. Insight 024: at 500 generations
(~165k evaluations per run, n=10 per arm), the truncation-GA + shallow tuner does
NOT solve double-pole-without-velocity with ANY memory mechanism; fitness is
lottery-like (rare high outliers over a low median), with no reliable memory
benefit. That is not a budget wall — it is an OPTIMIZER wall. The methods that
solve this exact benchmark in the literature are evolution strategies:

- **CMA-ES** — Igel 2003, "Neuroevolution for Reinforcement Learning Using
  Evolution Strategies" (top performer on pole balancing).
- **CoSyNE** — Gomez, Schmidhuber & Miikkulainen 2008 (Cooperative Synapse
  Neuroevolution), among the most sample-efficient on DPNV.
- **ESP** — Gomez & Miikkulainen (Enforced Subpopulations).

We have none of these. faber-tweann's DXNN path is a memetic GA; the domain_sdk
path has a pluggable strategy FRAMEWORK but no ES ALGORITHM; a CMA-ES covariance
NIF exists only as scaffolding. This programme fills that gap.

## The axis

Search method, holding the network fixed:

| Method | Family | Status |
|---|---|---|
| truncation-GA + memetic tuner | GA | Programme 1 baseline (`population_monitor`) |
| **(mu, lambda)-ES** (self-adaptive sigma) | ES | **first target (EXP-025)** |
| (mu + lambda)-ES | ES | queued |
| CMA-ES | ES | queued (covariance NIF is scaffolding) |
| CoSyNE | cooperative coevolution | queued |
| natural / OpenAI-ES | ES (gradient estimate) | queued |

## Design discipline (the one that matters)

**Control for topology.** ES does not evolve topology — CMA-ES needs a fixed
parameter dimensionality. So all Programme-2 comparisons run on a FIXED-TOPOLOGY,
weight-only network. The GA baseline for these experiments must ALSO be
fixed-topology weight-only (not the DXNN topology-evolving path), or we confound
optimizer with topology-search. domain_sdk (weight-only, fixed topology) is the
natural arena; the DXNN path stays as Programme 1's home.

Second discipline (from Programme 1): report evaluations-to-solve, not just
generations — ES and GA have different population/offspring counts per generation,
so evaluations is the honest axis.

## Experiments

| EXP | Question | Status |
|---|---|---|
| 025 | Does (mu,lambda)-ES solve DPNV where the GA stalled (024)? | next |
| 026 | ES vs GA, matched evaluations, across a problem set (XOR, pole variants, DPNV) | queued |
| 027 | **Bridge back:** re-run the memory comparison (018/019/024) under ES — does memory pay once the optimizer can reach solves? | queued (the payoff) |
| 028 | (mu,lambda) vs (mu+lambda) vs CMA-ES on DPNV — which ES? | queued |

## The bridge to Programme 1

Programme 2 is not independent — it shares all infrastructure (scapes,
morphologies, harness, corpus) and its first real payoff (EXP-027) is to REVISIT
Programme 1's confounded results with an optimizer that can reach solves. Many
Programme-1 findings are "inconclusive at this budget"; several are really
"inconclusive with this optimizer." Programme 2 sharpens them.

## Corpus

One unified insight corpus, continuous numbering (025+), each insight tagged with
its programme. Insights cross-reference across programmes (Programme 2 cites
018/023/024). Do not fragment the corpus.

## Related

- [`PLAN_EXPERIMENT_PROGRAMME.md`](PLAN_EXPERIMENT_PROGRAMME.md) — Programme 1 (Capabilities)
- [`../insights/024-*.md`](../insights/) — the GA/optimizer wall that motivates this
- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md) — ES prior art (Igel, Gomez/CoSyNE)
