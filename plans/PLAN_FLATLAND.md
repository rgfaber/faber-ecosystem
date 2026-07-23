# PLAN — Flatland: the ecological / ALife convergence front

**Status: OPENING (2026-07-23).** The front where P7 (coevolution), P4 (novelty),
P5 (many-agent distributed substrate) and P6 (body encoding) converge on a single
2D artificial-life world. Opened as the direct consequence of insight 057.

## Why now — what 057 established

057 ran the coevolution toolkit (053-056) on a real neural pursuit-evasion game on a
bare 9x9 torus grid, exhaustively:

- MONOTONE progress, NO cycling (master-tournament triples=0 across four brackets).
- The pursuer sustains benchmark progress, the evader only weakly; both roughly flat
  across the balance.
- A DECOUPLED control (reproduced to n=80, two static baselines) REFUSED coevolutionary
  coupling: a co-adapting opponent buys no more progress than a diverse static one.
- Every finer probe (balance-contingency, speed-edge trend) DISSOLVED under larger n.

**Conclusion: the bare pairwise grid is exhausted for arms-race dynamics.** This may well be
the CORRECT answer for pairwise pursuit-evasion (a game with a stable fixed point and no
escalation ceiling), not a deficiency to be fixed -- the front tests that symmetrically, it
does not presume a rescue. What is certain is that the bare grid holds several axes FIXED that
ALife theory says matter: resources/energy, death and reproduction, and many agents in a shared
world. The untested variables are ecological, so the ecology is where the next question lives --
asked either-way, one axis at a time.

## What Flatland is

The DXNN2-heritage 2D artificial-life scape (Sher, *Handbook of Neuroevolution Through
Erlang*, the ALife chapters): a continuous 2D world holding many avatars at once, each
an evolved network sensing through range sensors and acting through movement/consumption.
The ingredients the bare grid lacked, each a lever a sustained arms race could use:

- **Resources + energy.** Plants (food) regrow; moving costs energy, eating gains it,
  death at zero. Now a prey has a reason to exist independent of the predator (forage
  well), and "getting better" has a substrate beyond a single binary contest.
- **Reproduction / open population.** Fitness is survival + offspring, not a fixed-length
  tournament. The population turns over IN the world; selection is frequency-dependent
  and endogenous, not an external generational loop.
- **Many agents in one shared world.** Interaction is many-to-many and spatial, not a
  pairwise match. Local density, patchy resources, and spatial refuges are all in play.

This is the instrument the open-endedness questions actually require. Bounded scapes
(11x11 maze, single pole, 9x9 grid) cannot measure open-endedness because there is no
room for it; 057 proved that for the arms-race case specifically.

## The convergence (why this is one front, not four)

| Programme | What it contributes to Flatland |
|-----------|--------------------------------|
| **P7 Coevolution** | the engine — predator/prey/plant co-adaptation IS the coevolution |
| **P4 Objectives/QD** | novelty/behavioural diversity pressure in an unbounded behaviour space |
| **P5 Scale/Substrate** | many agents = the many-small-nodes substrate; the world is distributable |
| **P6 Encoding/Development** | avatar bodies (sensor/actuator layouts) as developmental genomes |

Flatland is opened conservatively (smallest steps, every rung gated), NOT as a jump to
full open-ended ALife. The bare-grid discipline carries over: build the instrument,
verify representability as a kill gate, ask one non-foregone question per rung, gate the
design before building and the claim before signing.

## The substrate (already present)

`faber-tweann/src/scape.erl` is a generic scape process dispatching to a callback module
implementing the `scape` behaviour (the verbatim DXNN2 protocol: sense -> percept,
action -> {fitness, halt}). Existing callbacks: `pb_sim`, `tmaze_sim`, the bandit sims,
`xor_sim`. Flatland is a new `flatland_sim` scape callback plus forager/predator
morphologies (sensors/actuators) in `morphology.erl`. No new runtime — the exoself,
cortex, sensors, actuators, and the batched-population NIF all already exist.

## The ladder (smallest steps first; each rung gated + signed)

The DESIGN gate (2026-07-23) restructured this: instrument BEFORE claim, and the variable
isolated one at a time (energy economy, THEN population structure). It also de-teleologised
the framing: the 057 refusal may be the CORRECT answer for pairwise pursuit-evasion, not a
deficiency to be "rescued" -- so each rung asks a symmetric either-way question.

- **Rung 1 — the INSTRUMENT (exp058, SIGNED insight 058, 2026-07-23).** Built `flatland_sim`
  (toroidal world, regrowing plants, energy/death), geometry + sensing IDENTICAL to 057. All
  four deliverables clean: scape works (0a); a `[5,6,4]` net evolves competent foraging AND
  hunting (0b, both = greedy); the DECOMPOSED graded benchmark reads foraging/fleeing/capture
  orthogonally (pure-forager 27.6/0.31 vs pure-fleer 3.5/0.69, distinct axes); and 057's no-
  coupling refusal REPRODUCES on the graded instrument (pure pursuit-evasion, plants off, n=40)
  -- not a saturation artifact. Instrument validated + 057-consistent; no dynamics claim.
- **Rung 2 — the forage/flee trade-off (exp059, SIGNED insight 059).** The DESIGN gate redesigned
  the coupling-verdict question (confounded + foregone) into the forage/flee frontier question. The
  CLAIM gate then required the honest double-negative reading: REACTIVE FLEEING is never learned
  under ecological survival fitness (the 058 flee axis stays at ~0.29 -- barely above the gen-0
  random-net floor 0.254, vs a dedicated fleer 0.39-0.69 -- in every condition), so no forage/reactive-
  flee frontier is observable; predator PRESENCE lowers realized foraging (24.5->3.4-4.5, mechanism
  unidentified); COEVO-vs-static an underpowered null. Lesson: the 058 reactive-flee axis is the WRONG
  second axis; the missing instrument is an encounter-conditional AVOIDANCE metric.
- **Rung 2b — the avoidance instrument (next).** Build an encounter-conditional avoidance metric
  (mean predator-prey minimum distance; forage-rate with-vs-without predator per episode) to adjudicate
  AVOIDANCE vs foraging DEGRADATION -- the prerequisite for any mechanistic claim about the 059 collapse.
- **Rung 3 — population structure (the ALife-plausible variable the post-mortem flagged).**
  Many-agent open population with endogenous reproduction and frequency-dependent selection
  in one shared world -- the ingredient rungs 1-2 do NOT touch, and the more likely locus of a
  real arms race.
- **Rung 4+ (horizon):** novelty / behavioural diversity (P4) in the unbounded space; body /
  sensor encoding as a developmental genome (P6); distribution across nodes (P5). Each gated,
  opened only after the one below is signed.

## First deliverable

`experiments/exp058_flatland_ecological_arms_race.md` — the rung-1 INSTRUMENT pre-registration,
DESIGN-gated (build-with-changes; instrument split from claim) before any scape is built.

## Prior art (report as replication, not discovery)

- Sher, *Handbook of Neuroevolution Through Erlang* (2013), the ALife / Flatland chapters
  (predator-prey-plant neuroevolution on the BEAM; the direct heritage).
- Nolfi & Floreano 1998 (embodied predator-prey coevolution and its fragility).
- Yaeger 1994 (PolyWorld); Ackley & Littman 1991 (AL for evolution) — ecological ALife.
- Lehman & Stanley 2011 (novelty), Watson & Pollack (coevolution) — carried from 053-057.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the programme map
- [`CHARTER_P7_COEVOLUTION.md`](CHARTER_P7_COEVOLUTION.md) — the engine (053-057)
- [`CHARTER_P5_SCALE_SUBSTRATE.md`](CHARTER_P5_SCALE_SUBSTRATE.md) · [`CHARTER_P6_ENCODING_DEVELOPMENT.md`](CHARTER_P6_ENCODING_DEVELOPMENT.md)
- insight 057 — the result that opened this front
