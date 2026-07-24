# 061 — [Flatland rung 3a, INSTRUMENT] The open many-agent population with FIXED-GREEDY behaviours has NO coexistence regime: it boom-busts to mutual extinction (median 39 steps) across the density x predator grid AND the strong-stabiliser corner

Flatland rung 3a (P7 x P5). The DESIGN gate split rung 3: the open-population ARMS-RACE question is
foregone (four rungs of optimizer under-convergence) and demography-confounded, so rung 3a builds and
tests the coexistence/dynamics INSTRUMENT first -- the prerequisite the arms-race rung (3b) needs. It
asks the smallest non-foregone open-population question: does the open many-agent population (prey +
predators living and REPRODUCING in one shared world, endogenous energy/reproduction/death) sustain
predator-prey COEXISTENCE and produce Lotka-Volterra population cycles?

Answer, with FIXED competent hand-coded behaviours (to isolate ecology from evolution): **NO. It boom-
busts to mutual extinction across every configuration tried** -- so this open-population world is not (yet)
a viable substrate for the arms-race rung, and evolved self-limiting behaviour becomes the crux.

## The finding

Prey = forage-and-flee; predator = greedy-hunt with three classic Lotka-Volterra stabilisers built in
(functional-response SATIATION / handling time; a predator SPEED DISADVANTAGE; LIMITED PERCEPTION giving a
spatial refuge). Reproduction endogenous (energy>=threshold -> clone, split energy); death at energy 0;
starvation-order culling over a cap. World 40x40, horizon H=1500, calibration seeds 1-3 disjoint from
measurement seeds 101-106.

- **GRID A (default stabilisers satiate=3, percept=6):** coexistence fraction = **0.00** in all 12 cells
  (plant-density {80,150,250} x initial-predators {8,15,25,40}), all seeds.
- **GRID B (the strong-stabiliser corner LV theory prefers: satiate=12, percept=2 = large refuge):**
  also **0.00** in all 12 cells. Even long handling time + a big spatial refuge does not rescue coexistence.
- **Hand-exploration (8 configs before the grids):** predators strong->weak, prey weak->strong, satiate
  {off,3,5,8}, pred-skip {off,3,4,5,6}, perception {global,6}, world 20x20 & 40x40, various ratios --
  every one collapsed.
- **Time-to-first-extinction: median 39 steps** across 144 grid runs -- collapse precedes the 300-step
  burn-in. So the LV cross-correlation / cycle detector was never exercised (nothing alive post-burn), and
  the coexistence criterion effectively reduces to "survived the burn-in", which nothing did.

The collapse mode is the classic unstable predator-prey overshoot: predators over-consume prey to
extinction, then starve. Greedy-hunt is a maximally efficient (near-optimal) hardcoded predator; with
FIXED behaviours the population has no way to self-limit predation.

**Verdict:** confirmed negative (no coexistence with fixed-greedy behaviours, across density x predators
AND the strong-stabiliser corner; median time-to-extinction 39) + method (design gate REDESIGN arms-race->
instrument; claim gate SIGN-WITH-CHANGES: strong-corner probe + time-to-extinction required, scoping
de-generalised) · **Date:** 2026-07-24
**Programme:** 7 x P5 — Flatland front, rung 3a (dynamics instrument). Report as an ecological-dynamics
(Lotka-Volterra) negative for this game + these fixed behaviours.

## Scope (the CLAIM gate's required de-generalisation)

This is a negative about FIXED-GREEDY behaviours in THIS game. It is NOT:
- a claim that open populations cannot coexist IN GENERAL (a huge literature says they can, with the right
  functional responses / spatial structure -- this simple substrate just does not realise it);
- a claim that EVOLVED behaviours cannot coexist on this same world. Greedy-hunt cannot self-limit
  predation; whether EVOLVED predators discover self-limiting (prudent) predation and coexist is exactly
  the open rung-3b question, which 3a does NOT foreclose. Indeed 3a REFRAMES 3b: the interesting question
  is no longer "does the open population arms-race" but "can evolution discover a self-limiting ecology at
  all, where a hand-coded optimal predator drives collapse?"
- The strong-stabiliser corner (satiate=12, percept=2) was probed BECAUSE LV theory places coexistence
  there; it too collapsed, which is what makes the negative robust rather than a weak-stabiliser artifact.

## Why this rung is instrument-only (and how it was gated twice)

The DESIGN gate refused the open-population arms-race question: the favoured null (barrier survives) is
foregone from optimizer under-convergence, and mean benchmark competence over live samples confounds
adaptation with DEMOGRAPHY. It prescribed building the coexistence/dynamics instrument first. The CLAIM
gate then caught that the initial grid pinned the stabilisers at their weakest tested settings (so "no
coexistence" was asserted over the region LV theory implicates least) and that the arms-race-cannot-
proceed wording contradicted itself; it required the strong-stabiliser corner probe, the time-to-
extinction distribution, and the scoping above -- all done, all strengthening the negative.

## What this buys (and the next rung)

3a re-shapes the whole Flatland endgame. The bare-grid arms-race (057), the ecological pairwise rungs
(059-060), and now the open-population dynamics all say the same thing from different angles: this simple
substrate does not spontaneously produce the escalating/co-sustaining dynamics ALife theory associates
with open-endedness. Rung 3b's honest question becomes: **can EVOLVED behaviours (mutation on the avatar
policies, which 3a switched off) discover self-limiting predation and sustain a coexisting ecology where
fixed-greedy behaviour collapses?** -- with a learnability positive control and a within-lineage
adaptation read (the gate's earlier requirements). That is a genuinely open, non-foregone question the
fixed-behaviour negative sets up cleanly.

## Reproduce

`exp061_flatland_open_population_tests`: `calibrate/0` (single-seed trajectory), `run/0` (both grids +
time-to-extinction). Raw feed `061-raw-open-population.txt`. Engine `flatland_sim` (faber-tweann `eaf1081`).
