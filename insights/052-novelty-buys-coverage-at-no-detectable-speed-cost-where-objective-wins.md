# 052 — [P4] Where objective search already wins (a non-deceptive maze), novelty search buys large behavioural COVERAGE (saturates 112 of ~112 reachable cells vs objective's 79 and random's 68, disjoint 95% CIs at n=120) at NO detectable SPEED cost: the predicted efficiency "tax" did not appear. Novelty reaches the goal about as fast as objective (EFG medians 268 vs 305, both ~2.3x faster than random 690), and the median-difference CI [-36, +132] straddles zero, so this signs the ABSENCE of a tax, not a proven equivalence (the strict +/-25% margin is not met even at n=120). A first-run false INVALID from a mis-specified floor gate was caught at the CLAIM gate, the rule was corrected and pre-committed, and the study rerun.

**Verdict:** confirmed (coverage) + signed negative (no speed tax appeared) + method (mis-specified gate caught and corrected) · **Date:** 2026-07-23
**Programme:** 4 (Objectives / selection pressure) — the closing efficiency question 051 named.
**Uses:** the EXP-051 engine unchanged, on a new non-deceptive maze E (D's wall+gap, goal at {9,9}).
Two searches vs random, matched operator+budget. Scoped to this maze family and representation.

## The question

051 showed the up-side of abandoning the objective: under deception, novelty solves where
goal-chasing is trapped. Most tasks are not deceptive. The complement (Lehman & Stanley's own
caveat, the "no free lunch" intuition): where the objective gradient DOES lead to the goal, does
rewarding novelty instead of proximity cost something? Framed as a TRADE, both sides separately
falsifiable: goal-chasing should buy speed-to-goal, novelty should buy coverage.

## The task — maze E, matched to the deceptive maze D

The EXP-051 engine unchanged (11x11 grid, position-only Markov sensors, `[6,10,4]` net, one
(mu+lambda) EA, mu=lambda=20, sigma=0.15). **Maze E** shares D's wall (y=5) and gap (x=9-10) but
moves the goal to G=(9,9), so the gap now sits ON the greedy right-then-up route: closeness
descends monotonically to the goal (non-deceptive), objective solves it reliably, but a constant
policy cannot (it needs a turn). D and E are a matched pair differing only in goal placement.

**The originally-planned twin was degenerate and retired.** A pre-run probe killed the first
candidate maze N (gap at x=1): an always-north constant policy solves it and 20% of RANDOM genomes
solve it, so the shared init population floored the speed metric in 98.8% of runs. Maze E has a
1.2% init-floor and objective is ~2x faster than random on it. (This catch is why the design gate
and a frozen validity probe run before the main study.)

## Method

Two metrics per run from ONE evaluation stream: **EFG** = the 1-based evaluation-order index of the
first genome to reach the goal (censored = +infinity); **coverage** = distinct VISITED cells (union
of all trajectories) at 2k / 6k / 15k evaluations. Coverage is visited-cells, not final-cells,
deliberately: final-position novelty optimises final-cell dispersion, so distinct-final-cells is a
manipulation check, while distinct-visited-cells is a good it does not directly optimise. Searches:
objective-EA (closeness), novelty-EA[final-pos] (k-NN novelty), random. All-runs medians with
bootstrap 95% CIs; TWO-SAMPLE tests (bootstrap CI on the median DIFFERENCE) for speed. n=120.

## The run-1 false INVALID, caught at the CLAIM gate (method)

Run 1 (n=40) fired RESULT=INVALID. The CLAIM gate found the cause was procedural, not substantive:
the floor gate had been operationalised as "objective and random EFG CIs must be fully disjoint" --
a textbook error (overlapping one-sample CIs are compatible with a clearly significant two-sample
difference), which failed on a 40-eval tail overlap despite objective being 3.9x faster than random
by median. A second defect: the verdict ladder collapsed two independent axes, discarding a passing
coverage finding because the speed gate tripped. The gate refused a post-hoc rescue (p-hacking),
required the rule be corrected and PRE-COMMITTED, and the study rerun at n=120. Both fixes (two-
sample median-difference floor/speed gates; coverage adjudicated independently) were committed
before the confirmatory run. (Cf 038, 040, 047-run1, 048, 050-run1, 051-run1 -- the gate's job.)

## Results (confirmatory, n=120, E=15000)

| search on maze E | solved | EFG median (95% CI) | coverage @15k (cells) |
|---|---|---|---|
| objective-EA | 118/120 | 305.5 [262, 378] | 79 [74, 85] |
| novelty-EA[final-pos] | 120/120 | 268.0 [203, 321] | **112 [112, 112]** |
| random | 120/120 | 689.5 [543, 872] | 68 [66, 71] |

Floor gate (two-sample): median(objective - random) EFG CI = [-589, -232], entirely below 0 --
objective is ~2.3x faster than random, E is valid and non-refloored. Coverage curve: at 2k all
arms sit on a ~66-cell shared random floor; novelty pulls away by 6k (103 vs 67 vs 66).

## Finding 1 — COVERAGE: novelty saturates the space, objective and random do not

Novelty reaches 112 of ~112 reachable cells; objective 79; random 68; all CIs disjoint. Over the
~66-cell shared random-evaluation floor, the attributable gains are novelty +46, objective +13,
random +2. Rewarding "reach new places" illuminates the whole maze; rewarding proximity converges
onto a narrow goal-seeking band; no-selection barely moves. **Hedge:** the gain's SIZE is partly a
ceiling statement about a small 11x11 maze (novelty has saturated it), and ~66 cells are the shared
floor. The direction and separation are robust; the magnitude is bounded by the world's size.

## Finding 2 — SPEED: the predicted efficiency tax did NOT appear

Novelty reaches the goal about as fast as objective (medians 268 vs 305), both ~2.3x faster than
random. The two-sample median-difference CI [-36, +132] straddles zero and the point estimate
mildly favours NOVELTY, not a tax. Across run 1 (obj 282 vs nov 292) and run 2 (obj 305 vs nov 268)
the sign of the tiny difference flips -- consistent with no real speed difference. **What is signed
is the ABSENCE of a tax, not equivalence:** the strict +/-25% equivalence margin is not met even at
n=120 (the CI's +132 upper end exceeds it), so the speed axis is formally INCONCLUSIVE for a
positive "equivalent" claim. The efficiency-tax intuition is unsupported here; "free lunch" would
overreach the evidence.

## What this closes for P4

With 050 (QD illuminates but shows no peak edge on an easy task), 051 (novelty solves a deceptive
maze where objective and a strong optimiser are trapped), and 052 (novelty buys large coverage at
no detectable speed cost where objective already wins), P4's selection-pressure axis is
characterised: rewarding behavioural novelty reliably buys COVERAGE, it is the answer to DECEPTION,
and it carries no measurable efficiency penalty on the non-deceptive tasks tested. The "tax"
intuition did not materialise. This motivates the next axis (P7 / open-endedness): once a world is
open-ended and unbounded, there is no single goal to be slower to, and coverage IS the objective --
the exact regime a bounded maze cannot express. Scoped to this maze family, single seed, sigma=0.15.

## Pointers

- Raw: `052-raw-efficiency-tradeoff.txt` (confirmatory n=120) + `052-raw-run1-n40-exploratory.txt`
  (the retracted false INVALID, kept on the record).
- Runner + engine pin + manifest: `faber-programmes/programmes/p4_objectives/exp052_novelty_efficiency_tradeoff/`.
- Charter: [`../plans/CHARTER_P4_OBJECTIVES.md`](../plans/CHARTER_P4_OBJECTIVES.md).
