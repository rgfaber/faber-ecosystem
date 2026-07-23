# 057 — [P7 embodied rung] Real neural pursuit-evasion coevolves MONOTONICALLY (no cycling), the PURSUER sustains frozen-benchmark progress at both crossover brackets, the EVADER only marginally

At the two speed brackets straddling the competent-play crossover, [2,6,4] faber-tweann nets coevolving
on 9x9-torus pursuit-evasion make MONOTONE directional progress with NO cycling: a population master-
tournament finds ZERO intransitive triples at noise bands 0.05/0.10/0.15 (with 25-38 of 45 cross-
generation edges decisive, so the band is not swallowing signal) and later-generations dominate earlier
25-29 to 0-1, at BOTH brackets. Measured against a FROZEN, verified-graded benchmark (pursuer on
capture-speed, evader on escape-rate -- incommensurable axes, only within-side end-vs-start comparisons
made), the PURSUER retains net progress at both brackets (NET +0.151 CI[0.100,0.204] at the evader-
favoured bracket, +0.189 CI[0.130,0.303] at the pursuer-favoured one; both disjoint from 0). The EVADER
is MARGINAL: it clears the end-vs-start sustainment criterion at the evader-favoured bracket (NET +0.153
CI[0.056,0.250]) but only touches it at the pursuer-favoured bracket (NET +0.042 CI[0.000,0.097], includes
0), and the between-bracket evader difference is NOT resolved at n=40 (0.111, CI[-0.000,0.194]). So there
is clear, non-cyclic, two-sided PROGRESS under coevolution, but a SUSTAINED two-sided arms race is not
cleanly established and reciprocal driving (versus independent improvement against a frozen ruler) is
NOT tested here.

**Verdict:** confirmed (monotone progress, no cycling) + partial (evader sustainment marginal, arms-race
coupling untested) + method (benchmark-grading fix + two adversarial CLAIM gates) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution / self-play) — the FIRST embodied rung, applying the 053-056 numbers-game
toolkit to real nets. Report as an embodied study of coevolutionary dynamics in the tradition of Nolfi &
Floreano 1998 and Cliff & Miller 1995 (CIAO / dominance tournaments); scoped to this game and these two
brackets. NOTE: this does NOT replicate the N&F cycling signature -- it finds the opposite (monotone
dominance, no cycling) in this setting.

## Why this rung exists

053-056 built and validated a coevolution measurement toolkit on numbers games where truth was knowable:
co-fitness is blind to progress (use a fixed benchmark); the benchmark must be GRADED, and ALIGNED;
under cycling it is honest-but-blind (use a master-tournament). This rung is the first application to
REAL faber-tweann networks coevolving on an embodied task, with those trusted rulers.

## The game

9x9 torus. Pursuer P and evader E, each a `[2,6,4]` feedforward net (sensors = opponent's shortest wrap-
around relative position; argmax of 4 outputs = move N/E/S/W; simultaneous moves). Capture = Chebyshev
<= 1, episode T=40. The evader skips 1 move every m steps -> pursuer speed factor s. Two coevolving
(mu+lambda) populations, mu=15, K=3 sampled opponents per fitness eval, R=30 generations, n=40 runs.

## Kill gates (both passed, pre-run)

- **Calibration.** Hand-coded greedy-vs-optimal-flee catch-rate across a fine speed grid is a COARSE
  STEP function; the two points bracketing 0.5 are m=14 (s=1.077, catch 0.389, evader-favoured) and m=13
  (s=1.083, catch 0.667, pursuer-favoured). The whole study runs at BOTH (see method note 3).
- **Representability.** A `[2,6,4]` net expresses competent pursuit (evolved catch-rate 0.857 > greedy
  0.667) and evasion, so any failure to progress is dynamics, not a capacity limit.

## The verified-graded benchmark (the crux; the 054 saturation demon, fixed)

FROZEN, seed-fixed hall-of-fame, identical across all runs: a difficulty-graded tunable greedy/flee
family (probability p of a random move) PLUS strong evolved-net opponents harvested once. Each role is
scored on the axis that has HEADROOM at the crossover, because the game is asymmetric there:
- **Pursuer: CAPTURE-SPEED** (1 - capture_step/T). Binary catch-rate saturates ~1.0 at s* (a strong
  pursuer almost always catches); capture-speed still separates fast from slow. Verified graded:
  0.288..0.867, aggregate 0.632 (off ceiling).
- **Evader: ESCAPE-RATE** (fraction NOT caught) vs STRONG pursuers only. Survival-TIME saturates (~0.85
  even for a random evader, since capture is late); escape-rate has headroom. Verified graded:
  0.083..0.333, aggregate 0.222 (off floor/ceiling).
These are INCOMMENSURABLE quantities; the study compares each side only to its OWN start, never the two
NET values to each other.

## Measurements (per run, per bracket)

- **Trajectory vs the frozen benchmark:** rise = peak-minus-start; gave-back = peak-minus-end;
  NET-sustained = end-minus-start. Bootstrap 95% CIs over the 40 runs. A side SUSTAINS iff its NET CI
  lower bound > 0.03 (this NET criterion is POST-HOC, added after an n=20 exploratory look -- see below).
- **Master-tournament (pre-registered):** save the population every 3 generations (10 saved/run); build
  the cross-generation capture matrix Score(i,j) = pursuer_i vs evader_j; generation i "beats" j iff
  Score(i,j)-Score(j,i) exceeds a noise band. Count intransitive triples A>B>C>A (cycling) and later-vs-
  earlier dominance (monotone progress). Swept over bands 0.05/0.10/0.15.
- **Cross-bracket:** two-sample bootstrap CI of each side's between-bracket NET difference.

## Result (n=40 per bracket, R=30)

| bracket | pursuer NET | evader NET | triples @0.05/0.10/0.15 | later vs earlier |
|---|---|---|---|---|
| m=14 (s=1.077, evader-fav) | +0.151 [0.100,0.204] | +0.153 [0.056,0.250] | 0 / 0 / 0 | 25 vs 1 |
| m=13 (s=1.083, pursuer-fav) | +0.189 [0.130,0.303] | +0.042 [0.000,0.097] | 0 / 0 / 0 | 29 vs 0 |

Cross-bracket EVADER NET difference (m=14 - m=13): 0.111, CI[-0.000, 0.194] -> **NOT resolved**.
Decisive edges: 25-38 of 45 pairs per run (the band is not swallowing signal).

What is RESOLVED: (1) NO cycling at either bracket, robust to the band; monotone directional progress.
(2) The pursuer sustains net progress at both. (3) The evader sustains at the evader-favoured bracket and
not at the pursuer-favoured one -- BUT (4) the between-bracket evader difference is not resolved, so this
is a threshold crossing at the boundary, NOT a demonstrated "contingent on balance" effect.

What is NOT established: a SUSTAINED two-sided arms race (the evader's sustainment is marginal and
bracket-dependent within noise); and RECIPROCAL DRIVING -- both sides improving on a frozen benchmark is
consistent with, but does not prove, coevolutionary coupling (two independent hill-climbs give the same
signature). Earning "arms race" needs a decoupled solo control (evolve each side vs the frozen benchmark
only; coevolved must out-progress solo). Not run here.

## Method note (fixes; two DESIGN-gate and two CLAIM-gate interventions)

1. **DESIGN gate** redesigned a 4-point speed sweep (under-sampling; saturated extremes; circular pilot-
   HoF; no overfit-winner cell; unreliable argmax champion) into ONE balance done deeply with fixed
   instruments -- which then became BOTH brackets.
2. **Exploratory n=20** (unsigned) exposed the 054 saturation demon: binary catch-rate saturated the
   pursuer benchmark, flipping the verdict run-to-run. FIX: continuous capture-speed (pursuer) + escape-
   rate (evader) + a frozen graded HoF + NET = end-vs-start (a transient peak that collapses is not
   sustained).
3. **CLAIM gate #1** REFUTED an early "disengagement toward pursuer": the pre-registered master-
   tournament was never scored (populations were discarded); both sides' peak-progress was disjoint from
   0 (the pre-registered brief-two-sided cell), making the NET criterion post-hoc; and s* was misplaced
   onto the pursuer-favoured side (the argmin of a coarse step function). FIXES: scored the master-
   tournament; ran BOTH brackets; declared NET post-hoc; dropped "even at the 50/50 balance".
4. **CLAIM gate #2** downgraded the headline from "sustained two-sided arms race, fragile, replicating
   Nolfi-Floreano" to the present wording: the between-bracket difference is unresolved (so no "contingent
   /fragile"), frozen-benchmark gains are progress not proven arms-race (so no "arms race" without the
   decoupled control), triples=0 is robust to the band (so "no cycling" stands), and the N&F reference was
   backwards (they found cycling; this finds none). Two free computations were required and done: the
   cross-bracket difference CI and the band sweep.

## What this buys (and the next rungs)

The 053-056 toolkit, applied to real nets, gives a trustworthy embodied reading: this game coevolves
MONOTONICALLY (not cyclically), with the pursuer robustly improving and the evader marginal. A clean
two-sided arms race is NOT demonstrated at these brackets. Next, to strengthen: (a) a DECOUPLED solo
control to test reciprocal driving (earn or refuse "arms race"); (b) a third, more pursuer-favoured
bracket (m=12) to turn two points into a trend and test a real balance-contingency; (c) larger n to move
the evader's m=13 CI off the 0 boundary. Toward Flatland: an embodied arms race, if it needs a two-sided
sustained gradient, likely requires added ecological structure (space, resources, many agents) -- P5/P6.

## Reproduce

`experiments/exp057_embodied_pursuit_evasion_tests.erl` (engine pinned `9bb43e6b...`); raw feed
`057-raw-embodied.txt`. `calibrate/0`, `representability/0`, `verify_benchmark/0`, `coevo_pilot/0`,
`run(#{n=>40, r=>30})` runs both brackets with the master-tournament and cross-bracket difference.
