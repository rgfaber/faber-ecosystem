# 024 — The truncation-GA hits an OPTIMIZER wall on double-pole-no-velocity: no solve in 165k evaluations, lottery-like variance, no memory benefit. Programme 1 closes; the search itself is the frontier.

**Verdict:** signed negative (budget-to-solution) + programme inflection · **Date:** 2026-07-21
**Programme:** 1 (Capabilities) — capstone / bridge to Programme 2 (Search Strategies)
**Experiment:** EXP_023 (high budget, constrained run on the dev workstation)
**Uses:** the memory variants (018/019/023). Motivates
[`PLAN_SEARCH_STRATEGIES.md`](../plans/PLAN_SEARCH_STRATEGIES.md).

## The hypothesis (018+019) and the test

018 found memory a TAX on double-pole-no-velocity at 60 generations (nothing
solved). 019 showed memory PAYS on a task solvable within budget. EXP_023 tested
the bridge: at HIGH budget (pop 30, **500 generations**, ~165k evaluations per
run, n=10 per arm), does a solve become reachable, and does memory pay when it
does?

Run on the dev workstation in a systemd cgroup unit capped at 8 of 32 cores
(after msi00, a laptop, hit 100C thermal-crit on a full-load attempt — noted:
sustained neuroevolution needs real cooling).

## Results (best_fitness per run; solve = balance 5000 steps = goal_reached)

| Arm | solved | median | distribution |
|---|---|---|---|
| A feedforward | 0/10 | ~3.1 | 0.18, 0.32, 0.39, 0.49, 0.79, 5.4, 6.3, 11.3, 53, **546** |
| B cfc (neuron) | 0/10 | ~0.24 | 0.15, 0.18, 0.22, 0.22, 0.24, 0.24, 0.40, 0.43, 0.44, **165** |
| C recurrent (wiring) | 0/n (running) | — | 0.23, **665**, ... (consistent: a rare huge outlier over a low floor) |

Raw: `024-raw-budget-to-solution.txt`. All runs ended at max_generations, none at
goal_reached.

## Three findings

### 1. No solve, at any budget tried

Nothing solves double-pole-no-velocity in 500 generations (~165k evaluations) —
across feedforward, cfc, and recurrent. For scale: the literature solves this
exact benchmark in a few thousand to ~tens of thousands of evaluations with the
right method. We spent 165k and did not. The solve is not merely far; it is out
of reach for this search.

### 2. Fitness is lottery-like, not convergent

Each arm's distribution is a low floor (~0.2-1) with a rare enormous outlier
(feedforward 546, cfc 165, recurrent 665). That is the signature of a search that
does not CONVERGE — it occasionally gets a lucky seed whose agent balances a
while, then most seeds plateau. Doubling generations would mostly buy more
lottery tickets, not convergence. So this is a METHOD wall, not a budget wall.

### 3. No reliable memory advantage — and cfc is the most STUCK

The n=2 pilot hinted "memory helps" (a recurrent 42 vs feedforward 0.26). At
n=10 that evaporates: feedforward's peak (546) is competitive with recurrent
(665) and beats cfc (165). And cfc has the LOWEST median (0.24) with the tightest
low cluster — it is the most consistently stuck, echoing 018's finding that CfC's
temporal smoothing caps performance on fast reactive control. So under this
optimizer, on this problem, memory does not pay — but that conclusion is itself
gated by the optimizer (see below).

## The real conclusion: the optimizer is the wall

The through-line of Programme 1 was "capability value = task-match x budget."
EXP_023 adds the missing term: **x optimizer**. On a hard landscape, a weak
optimizer cannot reach the solves that would let us even MEASURE whether a
capability pays. Our search is a truncation-selection GA (`selection_algorithm:
competition`, keep top-X) plus a shallow memetic tuner. The methods that solve
double-pole-no-velocity are EVOLUTION STRATEGIES — CMA-ES (Igel 2003), CoSyNE
(Gomez 2008), ESP. We have none of them.

So Programme 1 has characterised the capability space as far as its optimizer
allows, and in doing so discovered its own boundary. That is a clean inflection
point, not a failure: the frontier has moved from "which capability?" to "which
search?".

## Programme 1 closes here; Programme 2 opens

- **Programme 1 (Capabilities)** is at its natural end for the HARD problems.
  Remaining variations are either narrow mechanism confirmations (valid) or
  optimizer-confounded (blocked). Its arc — capability = task-match x budget x
  optimizer — is coherent and recorded (013-024).
- **Programme 2 (Search Strategies)** begins:
  [`PLAN_SEARCH_STRATEGIES.md`](../plans/PLAN_SEARCH_STRATEGIES.md). First target
  (EXP-025): a self-adaptive (mu,lambda)-ES on a fixed-topology weight problem —
  does it solve DPNV where this GA stalled? Then the bridge (EXP-027): re-run the
  memory comparison under a real optimizer, because "memory does not pay on DPNV"
  (finding 3) may be an artifact of the GA, not the truth.

## Caveat / honesty

This is n=10 on A and B (decisive: 20 runs, 0 solves). Arm C (recurrent) was cut
at 2/10: its runs are pathologically slow at 500 generations because of the
recurrent RESIDUAL STALLS noted in 018/020 (the two-phase reset reduced but did
not eliminate them) — a run can grind for many minutes on 10s input-timeouts.
That slowness is itself a data point (recurrent is expensive/unreliable at scale
here), and C's two runs (0.23, 665) show the same lottery pattern. No arm reaches
goal_reached; the between-arm differences are swamped by within-arm variance. The
finding does not depend on C's missing runs.
