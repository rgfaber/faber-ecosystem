# 059 — [Flatland rung 2] Ecological survival fitness never learns REACTIVE FLEEING (the 058 flee axis stays at the do-nothing floor), and predator presence lowers realized foraging

Flatland rung 2 (P7 x P4/P5/P6 convergence). Asked, via the DESIGN gate's reframe from a confounded
coupling-verdict question, whether the energy economy produces a forage/flee FRONTIER (a prey trading
foraging for fleeing under predator pressure). The CLAIM gate then required the honest reading below: it
is NOT a frontier result -- it is a double negative about what the ecological fitness selects and what the
058 instrument can see.

Three findings (n=20 prey champions per condition, ~50 generations each, at a calibrated ecological
capture probability ~0.44):

1. **Reactive fleeing is essentially NEVER LEARNED under the ecological (survival/foraging) fitness.** On
   the 058 fleeing axis (escape-rate vs a frozen strong-predator HoF, plants OFF), every condition sits
   at ~0.28-0.31 -- barely above the gen-0 RANDOM-net floor (0.254) and far below a dedicated fleer
   (0.389 here; 0.69 in 058). The no-predator forager (0.292) and the strong-predator-trained prey
   (0.292) are indistinguishable. So the flee axis is never engaged -- there is no second axis being
   climbed, hence no forage/reactive-flee trade-off can be OBSERVED. This is a negative about
   learnability + instrument, NOT a demonstrated non-trade.

2. **Predator PRESENCE lowers realized foraging** -- forage-skill ~24.5 with no predator collapses to
   ~3.4-4.5 with ANY predator (P0 CI[11.88,27.69] cleanly separated from WEAK CI[3.38,8.06] and below).
   The PRESENCE effect is resolved; the GRADED response to predator TYPE is NOT (WEAK 4.50 / COEVO 3.63 /
   STRONG 3.44 mutually overlap). The MECHANISM is NOT identified -- it could be weaker selection on
   foraging (a predator truncates episodes, capping the plants-eaten fitness), adaptive AVOIDANCE
   (keeping distance, unmeasured here), or EA underpower on a harder landscape. The word "disrupts" is
   deliberately avoided.

3. **Coevolution vs static is an UNDERPOWERED NULL, not a confirmed negative.** COEVO ~= WEAK ~= STRONG:
   the COEVO-minus-WEAK differences are unresolved at n=20 single-run (forage -0.88 CI[-4.81,1.56]; flee
   -0.028 CI[-0.139,0.056]). This is CONSISTENT with the 057/058 no-coupling refusal but does NOT
   independently establish it -- 057 needed n=80 + reproduction, and an n=60 coupling hint there
   evaporated at n=80. Not read as "no coupling in the ecology"; read as "not resolved".

**Verdict:** confirmed (predator presence lowers foraging; flee axis never engaged) + refuted (no
observable forage/flee frontier -- because fleeing is not learned, not because a trade was declined) +
underpowered (coevo-vs-static null) + method (2 gates: design REDESIGN + claim SIGN-WITH-CHANGES) ·
**Date:** 2026-07-24
**Programme:** 7 (Coevolution) x P5/P6 convergence — the Flatland/ALife front, rung 2. Prey side only
(the predator's ecology is vacuous pairwise). Scoped to this game, this fitness, m=13.

## Why this rung exists + how it was gated twice

The DESIGN gate REDESIGNED a first draft (a coupling-verdict "does the energy economy change coupling"
question) that conflated coupling with a train-test move-budget shift + an M confound and mostly re-
confirmed 058; it prescribed the forage/flee TRADE-OFF question instead (what the bare game cannot pose,
read on the orthogonal 058 axes). The CLAIM gate then REFUSED the "no frontier" headline: the flee axis
is DEAD (P0 = STRONG = 0.29, a dedicated fleer 0.69), so "no frontier" wrongly implies a declined trade
when the truth is fleeing is never learned. It also stripped a smuggled mechanism ("disrupts"), scoped
the coupling null to underpowered, and fixed a false monotone-dose-response claim. The signed wording is
the gate's.

## Method

A prey lives in an ecological episode (9x9 torus, plants + energy + one predator; geometry/sensing
identical to 057/058, only the energy economy added). Prey fitness = plants eaten before death (death =
captured OR starved) -- rewards foraging AND staying alive to keep foraging. A LADDER of predator
pressure: P0 (no predator), WEAK-static (frozen gen-0 net), COEVO (co-adapting predator population),
STRONG-static (hand-coded greedy-hunt). Per condition, n=20 champion prey are FROZEN and read as a 2D
point (forage-skill, flee-skill) on the 058 graded benchmark. Nets `[5,6,4]`, process-free
(network_evaluator); engine `flatland_sim` (faber-tweann `eaf1081`).

| condition | forage-skill | flee-skill |
|---|---|---|
| P0 no-predator  | 24.50 CI[11.88,27.69] | 0.292 CI[0.250,0.333] |
| WEAK-static     |  4.50 CI[ 3.38, 8.06] | 0.306 CI[0.250,0.347] |
| COEVO           |  3.63 CI[ 2.56, 5.81] | 0.278 CI[0.194,0.333] |
| STRONG-static   |  3.44 CI[ 1.88, 6.25] | 0.292 CI[0.250,0.361] |
| (flee floor: gen-0 random net 0.254 · dedicated fleer 0.389-0.69) | | |

## Caveats (pre-committed honest scope)

- **P0 under-convergence:** P0 forage 24.5 < 058 greedy 34, wide CI -- the EA budget (50 gen / pop 14)
  under-converges foraging, so realized-forage magnitudes are LOWER BOUNDS and the collapse magnitude is
  confounded with EA power on the harder predator-present landscape.
- **Avoidance is unmeasured:** the 058 flee axis reads REACTIVE escape, not AVOIDANCE (keeping distance).
  If the pressured prey does avoidance, the real trade is forage-vs-avoidance and this benchmark has no
  axis for it. So "flee never learned" is precise; "no anti-predator behaviour" is NOT claimed.
- **Single operating point:** m=13, capture ~0.44 -- one calibrated balance.

## What this buys (and the next rung)

The clean instrument lesson: the 058 REACTIVE-flee axis is the WRONG second axis for whatever ecological
survival pressure selects -- the prey does not learn to flee reactively, so the forage/flee frontier is
not measurable on it. The genuinely missing instrument (the gate's would-strengthen, now the rung-2b/3
prerequisite) is an ENCOUNTER-CONDITIONAL AVOIDANCE metric (mean predator-prey minimum distance, or
forage-rate with-vs-without predator per episode) -- it adjudicates avoidance vs degradation and is
required before any mechanistic claim about the foraging collapse. Rung 3 (many-agent open population)
remains the 057-post-mortem locus for a real arms race. Toward Flatland proper: an ecology where a
sustained two-sided gradient can live likely needs both the avoidance instrument AND population structure.

## Reproduce

`exp059_flatland_forage_flee_frontier_tests`: `calibrate/0`, `verify_ranges/0`, `floor_flee/0`,
`run_frontier(#{n=>20, g=>50})`. Raw feed `059-raw-frontier.txt`. Engine `flatland_sim` (faber-tweann `eaf1081`).
