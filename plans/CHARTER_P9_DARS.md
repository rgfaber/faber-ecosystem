# Programme 9 — DARS / Physical AI (Distributed Autonomous Robotic Systems)

**Status: HORIZON (coupling).** The coupling of the evolutionary engine to the
physical world at collective scale: many autonomous embodied agents acting and
coordinating over a mesh. The sensorimotor bookend to P8's linguistic one.

> One engine, two couplings. P8 couples evolution to language and symbolic
> cognition; P9 couples it to the body, and specifically to *many* bodies.
> Between them sit the seven engine axes (P1-P7). See
> [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md).

## The axis

Evolution of, and for, **distributed autonomous robotic systems**: collectives of
embodied agents where behaviour is grounded in physical morphology and where the
collective, not the individual, is the unit of interest. This adds three
variables the engine axes hold fixed:

- **The body** — morphology is given up as a constant; it can be co-evolved with
  the controller, and it offloads control into physics (morphological computation).
- **The collective** — many robots coordinate. Swarm behaviours (aggregation,
  flocking, foraging, collective transport) emerge from local rules and local
  sensing, with no central controller.
- **The reality gap** — controllers evolved in simulation must transfer to
  hardware, at swarm scale, where per-unit variation and noise are the norm.

**Distinct from P5.** P5 distributes the *search* (evolutionary populations across
compute nodes). P9 distributes the *robots* (bodies acting in the world). They
compose into the most Macula-native experiment on the map: **embodied evolution**,
where the evolutionary process itself runs on the robot collective over the mesh,
with no central server (Watson, Ficici & Pollack 2002).

## Why this programme exists

Neuroevolution's most striking results are embodied, and its most Macula-aligned
future is a *federation* of autonomous robots rather than one clever machine. A
mesh of independently owned, cooperating robots is the commons vision applied to
physical systems: non-extractive, fault-tolerant, sovereign at the edge. Faber
already carries the seed (`pb_sim` exposes `morphology_pole_balancing` variants),
and the substrate story is uniquely ours: process-per-neuron controllers are tiny
and deploy to microcontrollers; OTP supervision makes a physical control loop
self-healing; the Macula mesh is the coordination fabric a robot collective needs.

## Central questions

1. Does **embodied evolution** (distributed, on-collective, server-free) over a
   Macula mesh converge, and how does it compare to centralised evolution then
   deploy? (The P5 x P9 crossover; the headline Macula-native result.)
2. Do **evolved swarm behaviours** (collective transport, foraging) beat
   hand-designed local rules, and are they robust to unit loss? (Fault tolerance
   as a fitness property, native to BEAM supervision.)
3. How wide is the **reality gap** at swarm scale, and does domain randomization
   close it? (Ties to Raf's parksim finding: the sim/real divide is a property of
   the SOURCE, not the domain.)

## Where to start (the good first experiment)

- **P9-a**: a multi-agent 2D physics scape (N bodies, local sensing) with a shared
  evolved controller; confirm the engine produces a coordinated collective
  behaviour (aggregation is the "XOR" of swarms: prove the pipeline first).
- **P9-b**: embodied-evolution variant, controllers evolving on the collective
  over simulated mesh links, versus centralised evolve-then-deploy on P9-a.
- **P9-c** (horizon): deploy to real edge robots under OTP supervision; measure
  the reality gap and unit-loss robustness.

## The BEAM-native angle

A robot collective is a supervised distributed system with intermittent links,
partial failure, and no central authority: exactly what BEAM and the Macula mesh
are built for. Embodied evolution with no central server is not a workaround here,
it is the natural shape of the substrate. Small evolved controllers deploy where
large models cannot; supervision makes control loops self-healing; the mesh
carries both coordination and the distributed evolutionary process.

## Prior art (report as replication, not discovery)

- Watson, Ficici & Pollack 2002, "Embodied Evolution" — distributed evolution on a robot population, no central controller. The direct ancestor of P5 x P9.
- Bredeche et al., environment-driven distributed evolution (mEDEA).
- Sims 1994, "Evolving Virtual Creatures"; Lipson & Pollack 2000, Golem (evolved + fabricated robots).
- Şahin 2005, swarm robotics taxonomy; Trianni & Dorigo, evolving collective behaviours; Rubenstein et al. 2014, Kilobots.
- Cully et al. 2015, "Robots that can adapt like animals" (Nature) — QD on real hardware. Bridges P4.
- Tobin et al. 2017, domain randomization for sim-to-real.
- DARS symposium series (Distributed Autonomous Robotic Systems, since 1992) — the field this programme takes its name from.

## Dependencies

Needs a multi-agent physics scape (none exists; `pb_sim` physics is a reusable
seed), a morphology genotype for the coevolution arm (P6), and P5's distributed
substrate for the embodied-evolution arm. A genuine horizon programme: chartered
now to complete the map, activated once the engine has a reliable optimizer (the
DPNV fork) and a multi-agent scape exists.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`CHARTER_P5_SCALE_SUBSTRATE.md`](CHARTER_P5_SCALE_SUBSTRATE.md) — distributes the search (composes with P9)
- [`CHARTER_P6_ENCODING_DEVELOPMENT.md`](CHARTER_P6_ENCODING_DEVELOPMENT.md) — morphology encoding
- [`CHARTER_P4_OBJECTIVES.md`](CHARTER_P4_OBJECTIVES.md) — QD for robotics (Cully)
- [`CHARTER_P8_LLM_AUGMENTED.md`](CHARTER_P8_LLM_AUGMENTED.md) — the other coupling
