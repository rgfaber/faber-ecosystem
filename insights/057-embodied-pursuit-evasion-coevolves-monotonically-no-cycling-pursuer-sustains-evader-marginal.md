# 057 — [P7 embodied rung] Real neural pursuit-evasion coevolves MONOTONICALLY (no cycling), the PURSUER sustains frozen-benchmark progress at both crossover brackets, the EVADER only marginally

At three speed brackets around the competent-play crossover (m=14/13/12), [2,6,4] faber-tweann nets
coevolving on 9x9-torus pursuit-evasion make MONOTONE directional progress with NO cycling: a population
master-tournament finds ZERO intransitive triples at noise bands 0.05/0.10/0.15 (with 25-38 of 45 cross-
generation edges decisive, so the band is not swallowing signal) and later-generations dominate earlier
25-30 to 0-1, at ALL THREE brackets. Measured against a FROZEN, verified-graded benchmark (pursuer on
capture-speed, evader on escape-rate -- incommensurable axes, only within-side end-vs-start comparisons
made), the PURSUER retains net progress at both brackets (NET +0.151 CI[0.100,0.204] at the evader-
favoured bracket, +0.189 CI[0.130,0.303] at the pursuer-favoured one; both disjoint from 0). The EVADER
sustains only WEAKLY -- ~0.06-0.10 NET, roughly flat across FOUR brackets at n=60, much less than the
pursuer. An apparent balance-contingency (sustain-when-favoured, marginal-when-not) seen in a smaller
two-bracket run did NOT survive larger n and a wider bracket span: the widest cross-crossover evader
difference (m=18 minus m=12) is +0.028, CI[-0.042,0.111], unresolved. So the evader is marginal relative
to the pursuer, but not sharply balance-dependent. And a
DECOUPLED CONTROL (each side evolved against a FROZEN opponent, same benchmark) shows NO coevolutionary
coupling over a DIVERSE static opponent: against a frozen RANDOM opponent, coevolution's NET does not
reliably exceed the control on any side at either bracket (n=40 all four CIs include 0; a stronger n=60
run hinted at a pursuer effect that did NOT reproduce at n=80 -- the random baseline wobbles because it
redraws opponents each run). Coevolution DOES beat a frozen STRONG-but-NARROW opponent for the pursuer
(reproduced across n=60 and n=80, both brackets, diffs +0.07..+0.11, disjoint), but that is a curriculum-
DIVERSITY effect -- a narrow strong target overfits, and a diverse random static opponent avoids it just
as coevolution does -- NOT reciprocal coupling. So there is clear, non-cyclic, two-sided PROGRESS under
coevolution, but it is NOT a demonstrated arms race: co-adaptation buys no benchmark progress over a
diverse fixed opponent; the progress is each side adapting to an opponent gradient, not reciprocal
escalation.

**Verdict:** confirmed (monotone progress, no cycling; pursuer sustains) + refuted (no coevolutionary
coupling over a static control; not an arms race) + method (benchmark-grading fix + two adversarial CLAIM
gates + decoupled control) · **Date:** 2026-07-23
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

**Four brackets across BOTH catch-rate plateaus, n=60** (`057-raw-brackets.txt`; supersedes a smaller
n=40 three-bracket pass whose apparent trends were low-n noise). m=18 & m=14 on the 0.389 (evader-
favoured) plateau, m=13 & m=12 on the 0.667 (pursuer-favoured) one; m=18 is deep evader-favoured, giving
the widest extreme pair (m=18 vs m=12). m=18's benchmark verified graded (not saturated) before running.

| bracket | pursuer NET | evader NET | triples @0.05/0.10/0.15 |
|---|---|---|---|
| m=18 (s=1.059, evader-fav deep) | +0.172 | +0.083 [0.042,0.125] | 0 / 0 / 0 |
| m=14 (s=1.077, evader-fav)      | +0.151 | +0.097 [0.056,0.167] | 0 / 0 / 0 |
| m=13 (s=1.083, pursuer-fav)     | +0.139 | +0.056 [0.028,0.083] | 0 / 0 / 0 |
| m=12 (s=1.091, pursuer-fav deep)| +0.227 | +0.056 [0.000,0.111] | 0 / 0 / 0 |

- **No cycling at any of the four brackets** (triples=0 at every band, monotone dominance 24-29 vs 0-1).
  This is now the most robust finding: no cycling across the whole crossover.
- **The evader balance-contingency is NOT resolvable at this scale.** Evader NET is roughly FLAT and
  weakly positive (~0.06-0.10) across all four brackets; the widest extreme pair (m=18 minus m=12) is
  +0.028, CI[-0.042,0.111] -- includes 0. The earlier "step" (a big m=14 vs m=13 drop) was low-n noise:
  at n=60 the m=14 evader NET is +0.097, not the +0.153 of the two-bracket run. The evader weakly
  sustains EVERYWHERE, not sharply "sustains when favoured, dies when not".
- **The pursuer speed-edge trend did NOT reproduce either.** Pursuer NET is 0.172/0.151/0.139/0.227 --
  non-monotone; extreme pair m=18 minus m=12 = -0.056, CI[-0.138,0.032], unresolved. The n=40 "monotone
  increase with speed edge" was also noise. The pursuer sustains more than the evader everywhere
  (~0.14-0.23 vs ~0.06-0.10), with no resolved balance trend.
- **Caveat (a real artifact):** at deep evader-favoured brackets the evader benchmark START is high
  (~0.5 of a ~0.58 ceiling), so measurable evader NET is capped by headroom there -- part of why the
  evader NET looks flat. A balance-contingency, if real, is smaller than this measurement can separate.

Net: the four-bracket n=60 test CONFIRMS no-cycling and the pursuer > evader asymmetry, and REFUTES the
apparent balance-contingency (both the evader "step" and the pursuer speed-edge trend were low-n noise).
Neither side shows a resolved trend across the balance.

**Decoupled control (coevolution vs FROZEN static opponents, same benchmark). Two static baselines --
a DIVERSE frozen-random opponent and a NARROW frozen-strong opponent (evolved, seed-disjoint from the
benchmark) -- at n=40, then n=60 and a n=80 reproduction:**

| baseline | side | coevo-minus-static @n=60 | @n=80 (reproduction) | coupling? |
|---|---|---|---|---|
| RANDOM (diverse) | pursuer | +0.10..+0.12 (1 cell disjoint) | +0.004 / +0.061 (both include 0) | did NOT reproduce |
| RANDOM (diverse) | evader | negative/0 | negative/0 | no |
| STRONG (narrow) | pursuer | +0.105 / +0.083 (disjoint) | +0.071 / +0.081 (disjoint) | robust, BUT diversity not coupling |
| STRONG (narrow) | evader | ~0 | ~0 | no |

(The n=40 first pass had all four RANDOM cells unresolved: pursuer +0.046/+0.047, evader +0.097/-0.028.)

What is RESOLVED: (1) NO cycling at either bracket, robust to the band; monotone directional progress.
(2) The pursuer sustains net progress at both. (3) The evader sustains at the evader-favoured bracket and
not at the pursuer-favoured one -- BUT (4) the between-bracket evader difference is not resolved, so this
is a threshold crossing at the boundary, NOT a demonstrated "contingent on balance" effect. (5) The
decoupled control (two static baselines, reproduced to n=80) shows NO coevolutionary coupling over a
DIVERSE fixed opponent: coevolution does not reliably beat a frozen-random static opponent (a single
n=60 pursuer hint did not reproduce at n=80). It DOES beat a NARROW frozen-strong opponent (pursuer,
reproduced), but that is curriculum diversity avoiding overfitting to a narrow target, which the random
baseline shares -- not reciprocal coupling.

What is REFUTED / NOT established: a SUSTAINED two-sided ARMS RACE, and coevolutionary COUPLING over a
diverse opponent. The controls settle what CLAIM gate #2 left open -- a co-adapting opponent buys no more
benchmark progress than a DIVERSE static one, so the progress is each side adapting to whatever opponent
gradient exists, not reciprocal escalation. The only reproduced coevolution advantage is over a NARROW
strong target (pursuer), and it is a diversity effect, not an arms race. (Caveats: a coupling effect
below ~0.06 NET could still hide at these n; the strong baseline is a fixed seed-harvested set.)

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
   Nolfi-Floreano" to progress-not-arms-race: the between-bracket difference is unresolved (so no
   "contingent/fragile"), frozen-benchmark gains are progress not proven arms-race, triples=0 is robust to
   the band (so "no cycling" stands), and the N&F reference was backwards (they found cycling; this finds
   none). It named the decoupled control as the way to settle "arms race". Two free computations were done: the
   cross-bracket difference CI and the band sweep.
5. **Decoupled control, two baselines, reproduced** (post gate #2, this insight): coevolution vs a frozen
   RANDOM static opponent AND a frozen STRONG-narrow one, same benchmark, at n=40, n=60, and n=80. No
   coupling over the diverse random baseline (a n=60 pursuer hint did not reproduce at n=80; the random
   baseline wobbles). A reproduced pursuer win over the narrow strong baseline is diversity, not coupling.
   "Arms race" refused, not merely unproven. A conservative tightening, so no fresh gate; the reproduction
   is exactly the guard against signing the n=60 single-run "coupling" fluke.

## What this buys (and the next rungs)

The 053-056 toolkit, applied to real nets, gives a trustworthy embodied reading: this game coevolves
MONOTONICALLY (not cyclically), the pursuer robustly improves, the evader is marginal, and a co-adapting
opponent buys no more benchmark progress than a static one -- so it is two-sided PROGRESS, not a
demonstrated arms race. The stronger-baseline + reproduction, the third bracket, and now
a four-bracket n=60 span across both catch-rate plateaus (m=18/14/13/12) are all done: coupling refused
over a diverse opponent; no cycling at four brackets; and the evader balance-contingency REFUTED as
unresolvable at this scale (both the evader "step" and the pursuer speed-edge trend were low-n noise; the
evader weakly sustains ~flat everywhere). The robust, reproduced core is now: MONOTONE progress, NO
cycling, pursuer sustains more than evader, both across the whole crossover, no arms race / coupling.
Toward Flatland: a sustained, coupled embodied arms race needs a two-sided gradient this bare-grid game
does not supply; it likely requires added ecological structure (space, resources, many agents) -- P5/P6.
A residual measurement limit worth removing first: the evader benchmark saturates high at deep evader-
favoured speeds (start ~0.5 of a ~0.58 ceiling), so any small balance-contingency is below the floor of
what end-vs-start on this benchmark can separate.

## Reproduce

`experiments/exp057_embodied_pursuit_evasion_tests.erl` (engine pinned `9bb43e6b...`); raw feed
`057-raw-embodied.txt`. `calibrate/0`, `representability/0`, `verify_benchmark/0`, `coevo_pilot/0`,
`run(#{n=>40, r=>30})` runs both brackets with the master-tournament, cross-bracket difference, and the
random-baseline coupling control. `run_coupling(#{n=>80, r=>30})` runs the two-baseline coupling study
(random + strong, `build_strong_opponents` / `control_vs`); raw feeds `057-raw-embodied.txt` and
`057-raw-coupling.txt`.
