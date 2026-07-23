# 050 — [P4 opener] MAP-Elites ILLUMINATES the pole-balancer (104 balancer cells vs random search's 74 at equal budget, ~40% more) with a VALID orthogonal descriptor, but shows NO peak-quality advantage: MAP-Elites, pure random search, a multi-restart hill-climb, and sep-CMA-ES ALL reach the 501 survival cap. The task is too easy to demonstrate a QD-vs-objective search edge. A first-run "QD beats objective 23.9x" was a single-greedy-baseline ARTIFACT (random genomes already hit the cap; the only method that failed was one greedy hill-climb, stalled at 21 below the random median 22) -- caught and retracted at the CLAIM gate before signing. First Quality-Diversity machinery on faber (archive + behavioural descriptor + QD-score), reusable by P8/P9.

**Verdict:** confirmed (illumination) + signed negative (no peak advantage) + retraction (run-1 artifact) + method · **Date:** 2026-07-23
**Programme:** 4 (Objectives / Quality-Diversity) — the OPENER. **Uses:** `pb_sim` (single pole
with velocity), `sep_cma_es` (ceiling), a new pure-Erlang MAP-Elites (archive + Gaussian operator).
Single evolutionary seed {50,50,50}; single fixed 3.6-deg start -> claims scoped to the
single-start behavioural manifold. Report as replication of Mouret & Clune 2015, not discovery.

## The question

P1-P3 optimised a single objective. P4 varies the SELECTION PRESSURE. First QD test: does
MAP-Elites (a) illuminate a diverse archive of pole-balancers, and (b) find the objective
optimum as a by-product / beat objective search (the "QD free lunch")? (The charter's original
opener -- novelty-on-DPNV as a deception test -- is moot: insight 028 localised the DPNV wall to
optimizer+representation, not deception.)

## Method

Single pole WITH velocity (feedforward-solvable), deterministic 3.6-deg start, `[4,8,1]` net,
49-dim genome, quality = survival steps (cap 500). Behavioural descriptor (2D): BD1 = mean signed
scaled pole angle (lean-left/right style), BD2 = mean scaled cart position (where on the track it
stabilises). Grid 25x25. MAP-Elites: 100 random init + 40,000 evals of {sample a uniformly-random
elite, mutate Gaussian sigma=0.1, keep the best per cell}. Comparators at the SAME operator and
40k budget: pure random search, a multi-restart (40 x (1+15)) hill-climb, and a single-start
(1+15) greedy hill-climb; sep-CMA-ES as a labelled strong-optimizer ceiling. Descriptor-validity
gate: |Spearman(BD, quality)| < 0.3 over ALL filled cells. Raw: `050-raw-map-elites.txt`.

## The run-1 artifact, caught at the CLAIM gate (retracted in the open)

Run 1 fired "QD BEATS OBJECTIVE 23.9x": MAP-Elites peak 501 vs a single-greedy hill-climb's 21.
The CLAIM gate refuted it from the feed's own pre-run: **random genomes already reach 501**
(3.7% of random genomes balance, at least one hits the cap with zero optimisation). So the only
method that fails is the single greedy trajectory (a pathological baseline on a plateau fitness),
which loses to everything including plain random sampling. The "diversity/stepping-stones" story
was a portfolio/restart artifact. The gate prescribed the controls below; they were run; the
"QD beats objective" claim is retracted. (Cf 038, 040, 047-run1, 048 -- the gate's job.)

## Results (corrected, E=40,000)

| method (equal operator + budget) | peak survival | balancer cells |
|---|---|---|
| MAP-Elites | 501 | **104** |
| pure random search | 501 | 74 |
| multi-restart (1+15) hill-climb | 501 | -- |
| single-greedy (1+15) hill-climb | **21** | -- |
| sep-CMA-ES (ceiling) | 501 | -- |

Descriptor validity over ALL 129 filled cells: |Spearman(BD1,quality)| = 0.076,
|Spearman(BD2,quality)| = 0.007 (both << 0.3 -> VALID, orthogonal to quality). Balancer archive
spans BD1 [-0.36, 0.38] x BD2 [-0.61, 0.60]. QD-score 33,610.

## Finding 1 — ILLUMINATION: MAP-Elites beats random search on coverage at equal budget

At the SAME 40k-eval budget, MAP-Elites fills 104 balancer cells (survival >= 100) vs pure
random search's 74 -- ~40% more of the reachable behaviour space. Its structured exploration
(mutating archived elites toward empty cells) illuminates behaviours random sampling misses.
This is the core QD result (Mouret & Clune), and it SURVIVES the fair equal-budget control.

## Finding 2 — NO peak-quality advantage (the honest null)

MAP-Elites, random search, multi-restart, and sep-CMA-ES ALL reach the 501 cap. The task is
un-deceptive and feedforward-solvable, so there is no headroom for a QD-vs-objective peak claim:
diversity here buys COVERAGE (finding many kinds of balancer), not a higher PEAK. The "free
lunch" question is void on this task; testing it needs a task with a real quality gradient
(deceptive, or partially observable) -- a P4 follow-up.

## Finding 3 — a valid, orthogonal descriptor exists for the pole

Mean signed pole angle x mean cart position spread among survivors and are near-uncorrelated with
survival (Spearman 0.076, 0.007), so the archive holds behaviourally-diverse balancers, not a
quality gradient in disguise. (Force-based axes -- mean force, force-sign reversals -- FAILED the
validity gate as quality proxies; recorded so the next QD experiment does not repeat it.)

## What this establishes for P4

First QD machinery on faber (archive + behavioural descriptor + QD-score + operator-fair
baselines), reusable by P8 (LLM behaviour descriptors) and P9 (QD for robotics). Illumination
replicates on an easy task; the interesting QD claims (free lunch, stepping-stones, deception)
need a task with a quality gradient. Scoped to the single-start manifold, single seed.

## Pointers

- Raw: `050-raw-map-elites.txt` (corrected) + `050-raw-run1-single-greedy-artifact.txt`.
- Runner + engine pin + archive: `faber-programmes/programmes/p4_objectives/exp050_map_elites_illumination/`.
- Charter: [`../plans/CHARTER_P4_OBJECTIVES.md`](../plans/CHARTER_P4_OBJECTIVES.md).
