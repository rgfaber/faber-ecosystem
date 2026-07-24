# Programme 2 — Search Strategies (Operators)

**Status: PARKED (2026-07-24).** Execution plan and experiment ledger:
[`PLAN_SEARCH_STRATEGIES.md`](PLAN_SEARCH_STRATEGIES.md). Corpus: insights 025+.

> **Parked after the Flatland excursion (exp063/064/065): three rungs attempted, ZERO signed.**
> The charter's stated payoff ("revisit P1's confounded results with an optimizer that can reach
> solves ... several findings are really inconclusive with THIS OPTIMIZER") was aimed at the
> Flatland front and did not land. What the excursion did produce is on disk and is real:
>
> - **exp063** (unsigned, runner + feed retained): at a 2000-evaluation budget on Flatland foraging,
>   exp058's hand-rolled EA, `mu_lambda_es` and `sep_cma_es` are indistinguishable (medians 28.94 /
>   29.00 / 29.00). The drafted insight was REFUSED at the CLAIM gate: 2000 evals is ~31 per dimension
>   on a 64-dim problem, where sep-CMA's covariance has barely moved, so the null is near-definitional
>   rather than evidence. A prior n=8 read of the same comparison did not replicate (sign flipped).
> - **exp064** (held, NOT killed): the pre-registered optimizer-strength + does-it-overturn-059
>   experiment. DESIGN gate returned REDESIGN with 14 attacks; the premise audit it prescribed was run
>   at the one budget where a null is uninformative, so the experiment was retired prematurely. Its
>   2k-to-20k budget sweep remains the design that could actually separate the arms. Eight concrete
>   fixes are recorded in its file.
> - **exp065** (run, NOT signed): the greedy-ceiling / mixture rung. Its CLAIM gate found real errors
>   in the drafted insight (a selected range quoted as the range, an on-policy map called exhaustive
>   with no denominator recorded, a boundary threshold below chance, an arm whose floor is guaranteed
>   by elitism, and thresholds called "pre-registered" that lived only in a runner comment). Two arms
>   (degraded-seed control, planner bound) are specified and unbuilt.
>
> **The one durable result: insight 058 was AMENDED** (2026-07-24). Its foraging representability
> figure 33.7 is a best-of-run from a single execution; the typical run is 32.33 (93.9% of greedy) at
> 058's own configuration, n=20. The kill gate STANDS and is strengthened, because exp065 constructed
> the greedy policy exactly in weight space (243/243 states, no search). This also resizes the gap the
> whole excursion was arguing about: **~6% at the faithful configuration, not ~15%**.
>
> **Do not resume P2 on Flatland without first re-pre-registering thresholds in a dated document.**
> The recurring failure across all three rungs was claims outrunning the record, not compute.
> Nothing here bears on 061/062, which are ecological and which no optimizer result touches.

## The axis

**Operators**: the variation-and-selection algorithm, holding the network fixed.
Where P1 asked which capability helps which problem, P2 asks which search
algorithm is best for which landscape, and whether a stronger optimizer overturns
P1's conclusions.

| Method | Family | Status |
|--------|--------|--------|
| truncation-GA + memetic tuner | GA | P1 baseline |
| (mu,lambda)-ES, self-adaptive sigma | ES | done (EXP-025) |
| (mu+lambda)-ES | ES | queued |
| CMA-ES | ES | queued (covariance NIF is scaffolding) |
| CoSyNE | cooperative coevolution | queued |
| natural / OpenAI-ES | ES (gradient estimate) | queued |

## Design discipline

**Control for topology.** ES needs a fixed parameter dimensionality, so all P2
comparisons run on a fixed-topology, weight-only network; the GA baseline must
also be fixed-topology, or optimizer confounds topology-search. The
`faber-neuroevolution` domain_sdk path (weight-only) is the arena; the DXNN path
stays P1's home. Report evaluations-to-solve, not generations (different
population counts per generation).

## Where it stands, and the fork

Insight 025: the self-adaptive (mu,lambda)-ES solves the Markov pole task
process-free at roughly 70x the wall-clock speed of the process-per-neuron path,
but does NOT solve DPNV even at 400k evaluations. So the wall is optimizer AND
representation. P2 owns the **optimizer** leg of the DPNV three-way fork:

- **EXP-026 (next)** — co-evolve tau with weights. Tests the representation
  hypothesis; strictly this is P3 territory (adapting the substrate's own
  parameter), run here because the ES vector is the vehicle. If it solves, the
  wall was representation.
- **EXP-028** — CMA-ES and CoSyNE, the literature's actual DPNV solvers. If these
  solve where (mu,lambda)-ES did not, the wall was optimizer.

## The payoff: bridge back to P1

P2's real value (EXP-027/029) is to revisit P1's confounded results with an
optimizer that can reach solves. Many P1 findings are "inconclusive at this
budget"; several are really "inconclusive with this optimizer." Re-running the
memory comparison (018/019/024) under a working optimizer answers the question
P1 could not: does memory pay once solves are reachable?

## Where to start (for a contributor)

- Implement one queued ES variant (CMA-ES sampling via C^(1/2) plus rank-1/rank-mu
  update; the covariance NIF is scaffolding waiting for it) and run EXP-028.
- Build the **batched-population evaluator** NIF (one Rust call per generation);
  high leverage for every large sweep in this and later programmes.

## Prior art

CMA-ES for RL (Igel 2003), CoSyNE (Gomez, Schmidhuber & Miikkulainen 2008), ESP
(Gomez & Miikkulainen 1999). See
[`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md).

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map and the DPNV fork
- [`PLAN_SEARCH_STRATEGIES.md`](PLAN_SEARCH_STRATEGIES.md) — the execution ledger
- [`CHARTER_P1_CAPABILITIES.md`](CHARTER_P1_CAPABILITIES.md) — the wall that handed off
- [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md) — self-adaptation seam
- [`CHARTER_P4_OBJECTIVES.md`](CHARTER_P4_OBJECTIVES.md) — the deception leg of the fork
