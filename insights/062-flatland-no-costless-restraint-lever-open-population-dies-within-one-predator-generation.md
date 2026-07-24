# 062 — [Flatland rung 3b precondition] No costless restraint lever exists in the open population: greedy over-consumes, restraint self-starves, and coexistence dies within ~1 predator generation -- so evolution cannot be selected here

Flatland rung 3b, Part A (the PRECONDITION the DESIGN gate split out). 061 showed fixed-greedy predators
collapse the open population. The natural rung-3b question -- can EVOLVED behaviours discover self-limiting
predation and coexist? -- was ruled DEAD ON ARRIVAL by the design gate: 061 collapses in ~39 steps =
zero-to-one predator generations, so selection cannot act (no time). This rung asks the precondition:
does ANY regime persist for MANY predator GENERATIONS (a costless, non-self-starving prudent lever;
persistence measured in generations, not steps)? Answer: NO -- and the reason is a SQUEEZE.

## The finding (a squeeze, not just a null)

The only restraint available to a predator in this substrate is "decline to hunt", which means eat
nothing -- inseparable from starvation. So there is NO costless restraint lever, and that coupling IS the
result:
- **theta=1 (greedy, the non-starving extreme):** collapses by OVER-CONSUMPTION -- predators eat all
  local prey then starve, ~1 predator generation.
- **theta>1 (decline to hunt when local prey < theta = restraint):** ACCELERATES extinction via
  STARVATION -- prudent predators eat too little and die faster.
Neither end persists. Persistence fraction = 0.00 (coexist AND >= 20 predator generations) across ALL of:
- theta {1,2,3,5,8} x plant-density {100,150,220}: 0/90, median 0.6-1.0 generations.
- a predator ENERGETICS sweep (reproduction-threshold {34,55,80} x energy-per-catch {8,14,22} at theta=3):
  0/54, median 0.0-1.4 (slower breeding -> FEWER generations; predators die before reproducing).
- the 061 STRONG-STABILISER corner (satiate=12, tiny perception=2 = large refuge, greedy theta=1) -- the
  most likely known counterexample: 0/6, median 0.7 generations.
MAX predator-generations anywhere ~1.4, an ORDER OF MAGNITUDE below the GMIN=20 needed for selection.

**Verdict:** confirmed negative (no persistent regime in the searched region; the restraint-starvation
squeeze) + method (design gate REDESIGN dead-on-arrival -> precondition; claim gate SIGN-WITH-CHANGES ->
strong-corner check + squeeze framing + region scoping) · **Date:** 2026-07-24
**Programme:** 7 x P5 — Flatland front, rung 3b precondition (Part A). Report as an eco-evolutionary
TIMESCALE negative for this substrate + these behaviours.

## Why this blocks rung 3b (the DESIGN gate's fatality, confirmed)

Selection on a heritable trait needs many generations of differential reproduction. Here coexistence dies
within ~1 predator generation everywhere searched, so NO selection can act: "evolution can't rescue
coexistence" would be true because there is NO EVOLUTION (no time), not because restraint is unselectable.
Rung 3b (evolved restraint / viscosity, exp062's original plan; now deferred as rung 063) is therefore
BLOCKED in this region -- there is no eco-evolutionary timescale to measure it on. Part B (open-population
learnability) was not run: it is moot without a persistent regime to run it on.

## Method

Extended the 061 open-population sim (prey forage-and-flee; endogenous energy/reproduction/death;
starvation-order cull) with a fixed PRUDENT predator (prey-density THRESHOLD theta: hunt the nearest prey
only if the LOCAL prey count within perception >= theta, else wander -- sparing depleted patches). World
40x40, horizon H=4000; calibration seeds 1-3 disjoint from measurement seeds 101-106.
**Generations metric:** predator generation-equivalents = cumulative predator births / mean standing
predator count (a birth-turnover proxy for overlapping generations). The result is ~1 vs GMIN=20 -- an
order of magnitude short, so it is insensitive to a 2-3x error in the turnover proxy.

## Scope (the CLAIM gate's required de-generalisation)

This is a negative about the SEARCHED REGION of this substrate + the decline-to-hunt restraint family +
these energetics. World size (40x40), perception radius, prey reproduction rate, and plant energy content
were held FIXED. Whether a persistent regime exists ELSEWHERE in this substrate (a much larger world with
stronger spatial refugia, higher prey productivity, or a different restraint mechanism) is UNTESTED -- a
substrate change is offered as a HYPOTHESIS for why coexistence fails, NOT a demonstrated necessity. This
is NOT a claim that open ALife populations cannot persist or evolve in general (a large literature says
they can, with the right functional responses / spatial structure -- this simple substrate does not
realise it).

## What this buys (and where the front stands)

The Flatland front's whole arc converges: no coupling (057-058), no reactive fleeing / no frontier (059),
near-predator repulsion but unlinked (060), no fixed-behaviour coexistence (061), and now no eco-evo
TIMESCALE (062). The honest bottom line: this simple neuro-ALife substrate does not spontaneously produce
the persistent, escalating, co-sustaining dynamics ALife theory associates with open-endedness -- not for
lack of any single ingredient, but because the pairwise/open dynamics collapse or refuse to couple at
every angle tested. Progress on the arms-race / open-endedness questions needs a materially different
substrate (larger spatially-structured world enabling refugia and persistence; or a restraint mechanism
that is not decline-to-hunt), which is the honest next design problem -- NOT another rung on this world.

## Reproduce

`exp062_flatland_persistence_tests`: `calibrate/0`, `run/0` (theta x density grid + energetics sweep +
061 strong-corner check, all in predator generations). Raw feed `062-raw-persistence.txt`. Engine
`flatland_sim` (faber-tweann `eaf1081`).
