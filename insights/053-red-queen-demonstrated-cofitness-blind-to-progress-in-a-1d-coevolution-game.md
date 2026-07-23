# 053 — [P7 opener] The smallest coevolution step: in a stochastic 1D transitive numbers game, the trait escalates unboundedly (real, DIRECTLY OBSERVABLE progress: champion x rises 50.6 -> 85.7 over 150 generations, Spearman(gen, x) = 1.000, disjoint CIs, n=30) while co-fitness (population A's mean win-probability against population B) stays ~0.5 with no generational trend (mean 0.494, trend Spearman CI [-0.59, +0.47] spanning 0) -- the Red Queen: co-fitness is blind to progress the trait makes plain. The minimal, undeniable demonstration that co-fitness misleads, on the one case where ground truth needs no instrument.

**Verdict:** confirmed (Red Queen, minimal/degenerate case) + method (design-gated redesign + estimator fix) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution / self-play) — the OPENER, rung 1 of a gradual ladder.
**Uses:** a pure-Erlang stochastic numbers game (no net, no NIF). Report as replication of
Van Valen 1973 (Red Queen), Watson & Pollack 2001 / De Jong 2004 (numbers games). Scoped to
this degenerate 1D case; richer coevolution arrives at later rungs.

## Why this rung exists

P1-P6 optimise against a FIXED task; P7 makes the task move (fitness comes from a co-evolving
opponent). Coevolution's central measurement trap (Van Valen's Red Queen): performance against the
CURRENT opponent (co-fitness) can stay flat while both sides genuinely improve, because the
yardstick moves. The standard fix is a FIXED, non-coevolving benchmark. Before betting that
instrument on hard games (pursuit-evasion, eventually Flatland), the ladder validates it on
known-answer numbers games. This is the smallest rung: 1D, one known dynamic (arms race), ground
truth a single number we can read directly, so co-fitness's failure is undeniable.

## The game

A player is a single real x. Two coevolving (mu+lambda) populations, mu=lambda=30, mutation
x' = x + N(0, sigma=0.3), start x0=50. **Stochastic win rule** P(A beats B) = logistic((x_A - x_B)/tau),
tau=0.3, so near-equal players meet at ~0.5 and the win probability is smooth, not a step. Fitness =
mean win-probability against ALL opponents in the current other population (full cross-eval, no
sampling noise). R=150 generations, n=30 runs. Known ground truth: a monotone unbounded ARMS RACE.

## Method notes (two fixes, both design-anticipated)

The DESIGN gate (Fable) forced a redesign of a first draft that used a DETERMINISTIC win rule
(x_A > x_B): there champion-vs-champion co-fitness is 0 or 1 (whoever leads always wins), never
~0.5, so the Red Queen signal was false by construction; and benchmark-fidelity is a TAUTOLOGY in
1D (any benchmark is a monotone function of a scalar truth), so it validates nothing. Fixes: the
stochastic win rule above, and benchmark-fidelity DEMOTED to a runner smoke test (deferred to the
2D rung where truth stops being one number). A first run under the stochastic rule then measured
co-fitness as champion-vs-champion and it read 0.576 (outside the [0.45,0.55] band): with tau small
relative to the gap between two escalating champions that estimator is BIMODAL (snaps to 0/1 by
which champion leads), a high-variance estimate of a quantity whose true mean is provably 0.5 by
symmetry. Corrected to the POPULATION-level mean win-probability (smooth, low-variance), pre-committed,
rerun. Both precursors kept: `053-raw-run1-champ-champ-exploratory.txt`.

## Results (n=30, R=150)

| quantity | value |
|---|---|
| champion x: first -> last (median, disjoint CIs) | 50.6 [50.5, 50.7] -> 85.7 [85.4, 85.8] |
| escalation Spearman(generation, x), median | 1.000 (min 1.000 across runs) |
| co-fitness (population A vs B), across-run mean | 0.494 |
| co-fitness trend Spearman(generation, co-fitness), median [CI] | -0.058 [-0.59, +0.47] (spans 0) |
| benchmark smoke test (monotone in x; unsaturated) | all runs monotone; 100% unsaturated |

## Finding — co-fitness is blind to progress the trait proves is real

The trait escalates monotonically and unboundedly (a textbook arms race, Spearman 1.0), so progress
is undeniable and directly visible. Co-fitness averages 0.5 with no generational trend: it does NOT
rise as both populations improve, because the opponent improves in lockstep. If you watched only
co-fitness you would conclude nothing is happening. That is the Red Queen, and it is exactly why a
fixed benchmark (not co-fitness) is the correct progress measure in coevolution.

**Honest nuance:** within a single run co-fitness has wide variance (per-run mean ranged
[0.05, 0.91]): it reflects the momentary BALANCE OF POWER (which population is ahead, which drifts
run to run and can separate), not the absolute progress. It is symmetric across runs (mean 0.5) and
carries no trend with generation. So co-fitness measures relative standing, never absolute progress
-- the sharper statement of why it misleads.

## Scope and what this licenses

This is the DEGENERATE Red Queen: both populations climb the same single axis, with no strategic
interaction, non-transitivity, or disengagement. It establishes only that co-fitness misleads and
that the harness produces a clean arms race -- the cheapest possible place to confirm both.
Benchmark-FIDELITY (does a fixed benchmark aggregate progress correctly when truth is NOT one
number?) is untestable here and is the deliverable of **rung 054 (>=2D transitive)**. The ladder
then adds intransitivity/cycling (055), disengagement (056), and finally neural embodied
pursuit-evasion toward Flatland.

## Pointers

- Raw: `053-raw-1d-transitive.txt` (population-level co-fitness) +
  `053-raw-run1-champ-champ-exploratory.txt` (the bimodal-estimator precursor).
- Runner + engine pin + manifest: `faber-programmes/programmes/p7_coevolution/exp053_coevolution_1d_transitive/`.
- Charter: [`../plans/CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
