# 051 — [P4] Novelty search SOLVES a genuinely deceptive maze (final-position novelty 34/40, 0.850) where objective-EA (1/40) AND a strong sep-CMA-ES optimizer (0/40) are both trapped, while both solve/are-competent on the non-deceptive twin — the canonical Lehman & Stanley deception result, replicated on faber with disjoint 95% Wilson CIs. The DESCRIPTOR matters: final-position novelty (0.850) funnels toward the goal better than trajectory novelty (0.600), and a first run's "novelty fails on deception" (2/40, below random) was a FALSE NEGATIVE from a k-NN self-exclusion bug + a trajectory-descriptor strawman + a fluke budget — caught and reversed at the CLAIM gate before signing.

**Verdict:** confirmed (deception replicated) + characterisation (descriptor lesson) + method (false-negative retracted) · **Date:** 2026-07-23
**Programme:** 4 (Objectives / selection pressure). **Uses:** a pure-Erlang deterministic grid-maze
scape (no engine change), one (mu+lambda) EA reused across treatments, `sep_cma_es` (ceiling).
Single evolutionary seed, deterministic maze -> claims scoped to this maze pair. Report as
replication of Lehman & Stanley 2011 ("Abandoning Objectives"), not discovery.

## The question

P4 varies the SELECTION PRESSURE. Insight 050 built the QD machinery but on an un-deceptive,
too-easy pole (everything hit the cap) and concluded: the interesting QD claims need a task with a
real quality GRADIENT. This builds one. The Lehman-Stanley thesis: when the objective landscape is
DECEPTIVE (its gradient leads AWAY from the solution), rewarding behavioural NOVELTY (ignore the
objective, seek new behaviours) solves the task where objective-driven search stalls in the
deceptive local optimum. DPNV was the charter's candidate deceptive task, but insight 028 localised
its wall to optimizer+representation, NOT deception — so this constructs a task that is GENUINELY
deceptive and tests the hypothesis directly.

## Method

A deterministic 11x11 grid maze, matched PAIR. **D (deceptive):** a barrier forces the solution
path to first move AWAY from the goal (increasing closeness-distance) to round it; the
closeness-to-goal basin leads into a cul-de-sac. **N (non-deceptive twin):** identical
size/start/goal, barrier moved so the greedy-closeness route reaches G. Both solvable by
construction. Agent = a `[6,10,4]` feedforward net. **Sensors = normalised current position (x,y)
+ 4 wall-legality bits, NO goal sensor** — Markov and memoryless-solvable, so deception lives in
the LANDSCAPE, not leaked through a per-cell gradient pointer (the DESIGN gate's fix; a goal-sign
sensor would have made D trivial in policy space). Objective fitness = closeness = 1/(1+manhattan
to G); solved = path reaches G within T=60 steps.

ONE (mu+lambda)=20+20 EA, Gaussian sigma=0.15, budget E=15,000 evals, **n=40 independent runs per
cell**. Treatments differ in ONE line — the score: **objective-EA** (closeness) vs **novelty-EA**
(mean k-NN distance, k=15, to an append-only behaviour archive). Two novelty descriptors run:
**trajectory** (visited-cell set) and **final-position** (endpoint). Comparators at the same
operator+budget: **sep-CMA-ES on the objective** (labelled strong-optimizer ceiling), **random
search** (floor). MAP-Elites demoted to a reachability control only (it optimizes coverage ->
fills the goal cell tautologically). mutation-effectiveness reported (0.227 of single mutations
change the final cell -> stalling is a TRAP, not neutrality). Raw: `051-raw-deceptive-maze.txt`.

## The run-1 FALSE NEGATIVE, caught at the CLAIM gate (retracted in the open)

Run 1 fired "DECEPTIVE but NOVELTY NO HELP": the maze certified deceptive (obj-EA 40/40 on N,
obj-EA 1/40 + CMA 0/40 on D) but novelty scored 2/40, BELOW random's 3/40. The CLAIM gate refused
to sign it and found the run did not fairly test novelty: **(1) a k-NN self-exclusion BUG** —
`[dist(B,O) || O <- Others, O =/= B]` dropped neighbours BY VALUE, erasing the distance-0
duplicates that ARE the anti-convergence penalty (a converged population should score LOW novelty);
this handicaps novelty specifically. **(2) a descriptor strawman** — trajectory novelty has a huge
behaviour space CONFINED below the wall, so it farms novelty forever without pressure to cross;
Lehman-Stanley uses FINAL-POSITION novelty, whose endpoint coverage funnels toward the wall.
**(3) a sliding-window archive** (kept newest 300 -> forgot old behaviours -> re-farmed explored
regions). **(4) a budget set on an n=4 fluke.** Fixes: count duplicates and drop exactly one
self-distance; append-only archive; add the final-position arm; matched budget. All replay-cheap.
(Cf 038, 040, 047-run1, 048, 050-run1 — the gate's job. This is the most valuable kind: a false
negative flipped to the correct positive.)

## Results (fair test, E=15,000, n=40)

| search on maze D (deceptive), equal operator + budget | solved | rate (95% Wilson CI) |
|---|---|---|
| objective-EA (closeness) | 1/40 | 0.025 [0.004, 0.129] — TRAPPED |
| sep-CMA-ES on objective (strong ceiling) | 0/40 | 0.000 [0.000, 0.088] — ALSO trapped |
| random search (floor) | 4/40 | 0.100 [0.040, 0.231] |
| novelty-EA [trajectory] | 24/40 | 0.600 [0.446, 0.737] |
| **novelty-EA [final-position]** | **34/40** | **0.850 [0.709, 0.929]** |

Control (twin N, non-deceptive): objective-EA 40/40 (1.000 [0.912, 1.000]) — competent.
mutation-effectiveness on D = 0.227 (trap, not neutrality).

## Finding 1 — DECEPTION CONFIRMED: novelty solves where objective and a strong optimizer cannot

All four pre-committed conditions hold with disjoint CIs: objective-EA is competent on the
non-deceptive twin N (LB 0.912 > 0.8); objective-EA stalls on D (UB 0.129 < 0.2); sep-CMA-ES, a
strong optimizer, ALSO stalls on D (UB 0.088 < 0.3 — so the stall is the LANDSCAPE, not a weak
search); and final-position novelty on D (LB 0.709) exceeds objective on D (UB 0.129) with
non-overlapping intervals. This is the canonical Lehman & Stanley result: under genuine landscape
deception, abandoning the objective for behavioural novelty is what solves the task. P4's
reason-for-being — deception is a real failure mode that a DIFFERENT selection pressure fixes, not
a better optimizer — is now demonstrated on faber (the thing DPNV could not show, per 028).

## Finding 2 — the DESCRIPTOR is the funnel: final-position (0.850) > trajectory (0.600)

WHICH behaviour you reward decides whether novelty pressure funnels toward the goal.
Final-position novelty rewards endpoint coverage, which drives the population to spread its
end-cells across the grid and so to cross the wall. Trajectory novelty has a much larger behaviour
space that can be filled WITHOUT crossing (novel wiggles below the barrier), so it solves less
often (0.600 vs 0.850). Both fixed arms nonetheless beat objective — so run-1's 2/40 was the BUG,
not the descriptor alone; the descriptor is a second-order (but real) effect on top of a correct
k-NN + append-only archive.

## Finding 3 — a strong optimizer does not rescue you from deception (the point of deception)

sep-CMA-ES, which reached every cap on 050's easy pole, solves D 0/40. Deception is not a search-
strength problem: a better objective optimizer climbs the deceptive gradient FASTER into the same
cul-de-sac. Only changing the SELECTION PRESSURE (reward novelty, not proximity) escapes it. This
is the clean separation P4 exists to draw.

## What this establishes for P4

Deception replicates on faber with a constructed, certified-deceptive task; novelty search is
vindicated as a distinct and sometimes-necessary selection pressure. The QD machinery from 050
(archive + behavioural descriptor + operator-fair baselines) now has a task with a real gradient.
Open follow-up: the "free lunch" efficiency question (does novelty cost more evals than objective
where objective CAN solve?) and a budget sweep {15k, 60k, 240k} for any efficiency claim; MAP-Elites
scored as a positive on a non-tautological descriptor. Scoped to this maze pair, single seed.

## Pointers

- Raw: `051-raw-deceptive-maze.txt` (fair test) + `051-raw-run1-buggy-knn-trajectory.txt` (the
  retracted false negative).
- Runner + engine pin + manifest: `faber-programmes/programmes/p4_objectives/exp051_deceptive_maze_novelty/`.
- Charter: [`../plans/CHARTER_P4_OBJECTIVES.md`](../plans/CHARTER_P4_OBJECTIVES.md).
