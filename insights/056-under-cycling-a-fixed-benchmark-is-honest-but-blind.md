# 056 — [P7 rung 4] Under structural CYCLING, a fixed benchmark is HONEST-BUT-BLIND: on an intransitive game where the population rotates ~8 full turns (structural cycling confirmed by 1055 intransitive triples A>B>C>A among saved centroids, vs the transitive control's 0), the fixed benchmark does NOT report false progress (end-minus-start delta -0.017, 95% CI [-0.035, 0.009], includes zero) even though it visibly oscillates (range 0.557); so it is not FOOLED by cycling, but it is blind to the cyclic structure -- only a champion-vs-champion master-tournament (CIAO / dominance tournament) reveals it. This completes the P7 measurement toolkit (053-056).

**Verdict:** confirmed (honest-but-blind, the expected outcome) + method (design-gated redesign + metric fix) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution / self-play) — rung 4, the last numbers-game methodology rung. Pure-Erlang
numbers game, no net/NIF. Report as replication of Watson & Pollack 2001 (intransitive coevolution) and
Cliff & Miller 1995 / Stanley (CIAO / dominance tournaments). Scoped to this cyclic game.

## Why this rung exists

053-055 built the fixed-benchmark instrument (not co-fitness; graded; aligned) -- all assuming
progress CLIMBS. Coevolution's third failure mode is CYCLING: on an intransitive game the population
chases its own tail, no real progress. "A master-tournament detects cycling" is a textbook result
(Cliff-Miller) and is our INSTRUMENT, not a finding. The one genuinely open question: under confirmed
structural cycling, is the fixed benchmark FOOLED (does it report a false monotone RISE, as if there
were progress) or merely HONEST-BUT-BLIND (it oscillates / stays flat, correctly showing no progress,
but cannot see the cyclic structure)?

## Method

Two self-play coevolutions (mu=lambda=30, sigma=0.3, tau=0.3, R=200, n=20):
- **Intransitive (cyclic dominance):** strategy = angle theta; P(A beats B) = logistic(sin(theta_A -
  theta_B)/tau). Selection favours being "just ahead", so the population ROTATES around the circle
  (structural cycling by construction; the pilot confirms ~8 full turns).
- **Transitive control:** P(A beats B) = logistic((x_A - x_B)/tau) -- the 053 "bigger wins" game; the
  population escalates, does not cycle. The SAME tools on it must show the opposite readings.
Measurements use the population CENTROID (there is no dominant individual under intransitivity):
(1) a fixed graded benchmark (a half-circle arc, so a rotating centroid's win-rate OSCILLATES);
(2) co-fitness; (3) a master-tournament -- save the centroid every K generations and count
intransitive TRIPLES (A beats B beats C beats A, each beyond a [0.45,0.55] band) using the game's rule.

## Method note (two bugs caught in validation, both pre-signing)

The DESIGN gate first demolished a draft (incoherent FOOLED/ROBUST rule; "CIAO detects cycling" is
textbook instrument not a finding; champion ill-defined under intransitivity; the Watson-Pollack rule
risked diagonal collapse) and prescribed this cyclic-dominance-game redesign (reliable rotation,
centroid measurement, the false-rise question). Then VALIDATION caught two bugs before any signed run:
(1) a Spearman(gen, benchmark) trend reads a spurious ~0.36 on a benchmark that is CONSTANT up to
float noise (the antipodally-symmetric refs cancel to exactly 0.5, and rank correlation then ranks the
1e-16 float noise) -- replaced with the end-minus-start magnitude DELTA (the 054 lesson: magnitude, not
rank) and a half-circle arc so the benchmark genuinely oscillates; (2) a double-reversal of the saved
centroids corrupted the later-loses diagnostic (triples, being order-independent, were unaffected).
Corrected and pre-committed. Run 1 kept: `056-raw-run1-spearman-artifact-exploratory.txt`.

## Results (n=20, R=200)

| game | benchmark end-minus-start delta (median, 95% CI) | benchmark oscillation range | intransitive triples (median) | later-loses fraction |
|---|---|---|---|---|
| intransitive (cyclic) | **-0.017 [-0.035, 0.009]** (includes 0: NO net rise) | 0.557 (it oscillates) | **1055** | 0.465 |
| transitive (control) | 0.375 [0.367, 0.379] (a clean rise) | 0.469 | 0 | 0.000 |

Co-fitness ~0.51 in both. Transitive control valid (rising benchmark, zero triples).

## Finding — the fixed benchmark is honest under cycling, but cannot see it

The intransitive game cycles structurally: the centroid rotates ~8 full turns and the master-tournament
finds 1055 intransitive triples (A>B>C>A), while the transitive control has exactly 0. Under that
cycling, the fixed benchmark **oscillates** (range 0.557 -- it clearly moves as the population rotates)
but shows **NO net rise** (delta -0.017, CI includes 0): it is NOT fooled into reporting false
progress. But it is BLIND to the cyclic structure -- a flat/oscillating benchmark cannot distinguish
"cycling" from "stuck". Only the master-tournament (the intransitive-triple count / the banded
later-loses pattern, ~0.47 vs the control's 0) reveals the cycling. The benchmark is a progress meter,
and it correctly reads "no progress" during cycling; it is not a cycling detector.

## What this completes for P7

The measurement toolkit is now four rules deep, each validated where truth is knowable:
- **053** co-fitness is BLIND to progress (it hovers while the trait escalates) -> use a fixed benchmark.
- **054** the fixed benchmark must be GRADED (a too-weak one saturates and goes blind).
- **055** a misaligned SELECTION proxy decouples from true quality under asymmetric costs.
- **056** under CYCLING the fixed benchmark is HONEST (no false progress) but BLIND -> use a
  master-tournament to detect cycling.
Together these let the embodied pursuit-evasion rung be measured honestly, since any of Red Queen,
disengagement, or cycling can occur there and no ground-truth oracle exists once the players are
networks. This was the last place to validate the cycling-detector against a known-answer game.

## Pointers

- Raw: `056-raw-cycling.txt` + `056-raw-run1-spearman-artifact-exploratory.txt`.
- Runner + engine pin + manifest: `faber-programmes/programmes/p7_coevolution/exp056_coevolution_cycling/`.
- Charter: [`../plans/CHARTER_P7_COEVOLUTION.md`](../plans/CHARTER_P7_COEVOLUTION.md).
