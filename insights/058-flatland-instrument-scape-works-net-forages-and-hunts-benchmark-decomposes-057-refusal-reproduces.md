# 058 — [Flatland rung 1, INSTRUMENT] The Flatland scape works, a [5,6,4] net FORAGES and HUNTS, the benchmark DECOMPOSES foraging from fleeing, and 057's no-coupling refusal REPRODUCES on it

The instrument rung that opens the Flatland / ALife convergence front (P7 x P4/P5/P6). Built
`faber-tweann/src/flatland_sim.erl` (a pure 2D world engine) and validated it as an INSTRUMENT --
making NO ecology or arms-race claim (that is exp059). Four instrument-level results, all clean:

1. **The scape works.** On a 9x9 torus with regrowing plants and an energy economy (E0=20, step cost
   Em=1, plant Ep=4, death at 0, T=40), a hand-coded greedy-forager eats 34.4 plants and survives 12/12
   layouts while a random agent eats ~2.4 and starves (~1/12 survive). Energy accounting, eating, death
   and plant regrowth all behave.
2. **Representability (kill gate).** A `[5,6,4]` network EVOLVES competent FORAGING (33.7 plants eaten
   ~= the near-optimal greedy 34.4; ~12x random) AND competent HUNTING (capture-rate 1.000 = greedy;
   ~24x random). So the net class expresses both capabilities -- any later failure to produce dynamics
   is DYNAMICS, not a capacity/instrument limit.
3. **A DECOMPOSED, graded benchmark.** Foraging-skill, fleeing-skill and capture-skill read
   ORTHOGONALLY (the DESIGN gate's core requirement, so a later coupling verdict cannot be foraging
   drift). Each sub-metric is difficulty-graded and un-saturated (predator capture-speed 0.29..0.88 agg
   0.65; prey escape-rate agg 0.31, off floor). Crucially, foraging and fleeing are DISTINCT AXES: an
   evolved pure-FORAGER scores forage 27.6 / flee 0.31, an evolved pure-FLEER scores forage 3.5 / flee
   0.69 -- the forager out-forages the fleer AND the fleer out-flees the forager.
4. **057's no-coupling refusal REPRODUCES on the new instrument.** Re-running 057's decoupled coupling
   control (coevolution vs a frozen static opponent) on PURE pursuit-evasion (plants OFF) in the Flatland
   engine, at 057's crossover speed, measured on the graded capture/flee sub-metrics (n=40, R=30):
   coevolution's NET does NOT exceed the static control on either side (predator -0.023 CI[-0.105,0.069];
   prey -0.069 CI[-0.139,0.056]). So 057's refusal was NOT a binary-capture-saturation artifact -- it
   holds with the continuous graded metric and the re-implemented instrument.

**Verdict:** confirmed (instrument validated; benchmark decomposes; 057 refusal robust) + method (DESIGN-
gated instrument-before-claim) · **Date:** 2026-07-23
**Programme:** 7 (Coevolution) x P5/P6 convergence — the Flatland/ALife front, rung 1 (instrument). No
dynamics claim. Report as DXNN2-ALife-heritage instrument replication. See
`faber-ecosystem/plans/PLAN_FLATLAND.md`.

## Why this rung is instrument-only

The 053-057 discipline: an instrument dressed as a finding is the recurring error. The DESIGN gate
(2026-07-23) split instrument from claim and refused the original rung-1 arms-race question as confounded
(reading coupling off composite energy fitness would let foraging drift wear an arms-race label) and
partly foregone (rung 1 adds only the energy economy, not many-agent population). So this rung builds and
validates the instrument; the energy-economy coupling question is exp059 (rung 2), and many-agent open
population is rung 3.

## The instrument (geometry/sensing IDENTICAL to 057)

`flatland_sim` holds geometry and sensing identical to the 057 pursuit-evasion grid (9x9 torus, 4-way
argmax movement, shortest-wrap relative-position sensing -- NOT DXNN2 range/ray sensors, deferred as a
confound). The ONLY thing added is the energy economy (plants + eating + death), which lives in the
caller. So a later coupling difference is attributable to the ecology, not to a change of sensing. The
sensor is 5 floats [OppDX, OppDY, PlantDX, PlantDY, EnergyFrac]; nets are `[5,6,4]`, evaluated process-
free via `network_evaluator` (the 057 path).

## Method notes

- **Foraging representability under-shot at first** (9.75 plants at 30 gens / pop 14) -- pure EA
  underpower, not capacity: the task (map the nearest-plant sensor to a toward-move) is what greedy does
  from the same input, and at 80 gens / pop 20 over all layouts the net matches greedy. A capacity
  verdict on a thin EA budget would have been a false INVALID.
- **The decoupled control is 057's**, ported verbatim to the Flatland engine (pure pursuit-evasion,
  plants off): each side evolved against a frozen gen-0 static opponent, NET progress on the frozen
  graded benchmark compared to coevolution's. Coupling licensed iff coevo NET exceeds static NET; it does
  not, reproducing 057.
- **Knobs pinned by argument, not outcome:** plant density lets a competent forager sustain but a blind
  one starve; Em/Ep run E0 down in ~E0/Em steps without eating. No outcome-tuned calibration (there is no
  coupling claim in this rung to place).

## What this buys (and the next rung)

The Flatland instrument is validated and 057-consistent: with the ecology OFF it gives 057's answer. So
rung 2 (exp059) has a clean head-to-head -- turn plants ON and ask, symmetrically (per the gate's de-
teleologising), whether the energy economy CHANGES the coupling verdict, in either direction, read ONLY
on the decomposed pursuit-evasion sub-metric (never composite energy fitness). Rung 3 then adds the
many-agent open population the 057 post-mortem flagged as the more likely locus of a real arms race.

## Reproduce

Engine `flatland_sim.erl` (faber-tweann ref `eaf1081`). `exp058_flatland_instrument_tests:run/0` (gates
0a/0b); `exp058_flatland_benchmark_tests:verify_decomposition/0` (grading + separation) and
`rebaseline_057/1` (the coupling re-baseline). Raw feed `058-raw-instrument.txt`.
