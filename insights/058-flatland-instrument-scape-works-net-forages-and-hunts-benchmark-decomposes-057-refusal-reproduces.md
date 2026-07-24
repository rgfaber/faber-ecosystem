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
   **[AMENDED 2026-07-24, FORAGING FIGURE ONLY: 33.7 is a BEST-OF-RUN maximum from a SINGLE execution,
   not a typical run; the typical run is 32.33 (93.9% of greedy). The kill gate and this finding's
   conclusion STAND and are strengthened (exp065 CONSTRUCTED greedy exactly in weight space, 243/243);
   only the typicality reading is withdrawn. See "Amendment 2026-07-24" at the end.]**
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
  **[AMENDED 2026-07-24: "the net matches greedy" is withdrawn as a statement about a TYPICAL run at
  that configuration. It holds for the BEST run only. The typical run reaches 32.33 of greedy's 34.42
  (93.9%), and half of runs fall below 0.95x greedy. See "Amendment 2026-07-24" below.]**
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

---

## Amendment 2026-07-24 — the foraging representability figure is a BEST-OF-RUN, not a typical run

**What is withdrawn.** Finding 2's "33.7 plants eaten ~= the near-optimal greedy 34.4" and the method
note's "at 80 gens / pop 20 over all layouts the net matches greedy", *read as statements about what a
typical evolved champion achieves*. They are true of the best run, not of the run distribution.

**Why.** `exp058_flatland_instrument_tests:evolve/3` calls `best/5`, which threads
`max(B, element(1, hd(Ranked)))` and returns the maximum fitness EVER SEEN during the run. It was
called once. So 33.7 is a single draw of a best-of-run statistic, and no spread was measured.

**The corrected number.** `exp065_greedy_ceiling_and_mixture_tests.erl` re-ran this EA at THIS
insight's exact configuration (80 generations, 12 `forage_starts` layouts, mu=lambda=20, sigma=0.2,
`N(0,1)` init), n=20 independent runs with common random numbers by run index:

| statistic | value | as % of greedy |
|-----------|-------|----------------|
| hand-coded greedy (12 layouts) | 34.4167 | 100 |
| **median run** | **32.3333** | **93.9** |
| IQR | [29.25, 33.67] | [85.0, 97.8] |
| min / max | 13.58 / 35.00 | 39.5 / 101.7 |
| runs >= 0.95x greedy | 10 of 20 | |
| runs exceeding greedy | 2 of 20 | |

The distribution is not smooth: 31.00 occurs four times and 33.6667 six times, with a starvation tail
(13.58, 16.25, 24.67). Two runs beat greedy outright. Per-run values, seeds and eval counts are in
`faber-programmes/experiments/exp065-raw-greedy-ceiling-and-mixture.txt`.

**The KILL GATE STANDS, and is strengthened.** Representability asks whether the net class can EXPRESS
the capability, and a best-of-run demonstration is the right evidence for that. It is now settled far
more strongly than by any run: exp065 CONSTRUCTED the greedy policy directly in weight space (no search
at all) and verified it on all 243 `(DX,DY,energy)` states, where it returns 34.4167, bit-identical to
the hand-coded greedy. `flatland_sim:sense/6` hands the net the nearest-plant delta and greedy is
`axis_move` on that delta, whose four branches are convex cones that an argmax over four linear
readouts represents exactly. So the `[5,6,4]` net PROVABLY expresses competent foraging, and finding
2's conclusion ("any later failure to produce dynamics is DYNAMICS, not a capacity/instrument limit")
is unaffected and better supported than when signed.

**What this changes downstream.** Champions used by rungs 059-062 were drawn from the distribution
above, not from its maximum, so a typical downstream champion was a ~94%-of-greedy forager rather than
a greedy-equivalent one. It also corrects the size of the competence gap the corpus has been
discussing: ~6% at THIS configuration, not the ~15% quoted from exp063, which was measured at 50
generations over 8 layouts. Budget and layout set are confounded between those two, so the honest
statement is that the gap is CONFIGURATION-SPECIFIC.

**Caveats on the corrected number.** Same deterministic layouts and deterministic `regrow/2`, so the
world is memorizable and these are in-sample figures; unique evaluations are 1620 (20 + 80x20), not
3200, because parents are re-scored each generation on a deterministic fitness; and this is one EA at
one sigma. Nothing here bears on the HUNTING figure in finding 2, on findings 1, 3 or 4, or on
insights 059-062.

**Provenance.** Required by the exp064 CLAIM gate (which identified the best-of-run reading and
prescribed a faithful 80-gen/12-layout re-run before any correction) and by the exp065 CLAIM gate
(which confirmed exp065's arm R IS that re-run and ruled the amendment owed). The exp065 rung itself
was NOT signed; see `faber-programmes/experiments/exp065_representation_vs_search.md` for its defect
list. This amendment rests only on the arm R distribution and the construction, both of which that gate
audited and passed.
