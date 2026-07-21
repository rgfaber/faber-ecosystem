# Programme 5 — Scale / Substrate (Distribution of the search)

**Status: OPEN.** Chartered; Faber's structural differentiator. Corpus: to be
assigned.

## The axis

**Distribution of the search**: where and how the evolutionary process runs,
holding the algorithm and the network fixed. This is the axis where the BEAM and
the Macula mesh stop being an implementation detail and become the research
subject:

- **Island models** — semi-isolated subpopulations with periodic migration; the
  classic parallel-EA topology.
- **Distributed populations** — evolution spread across the beam cluster
  (beam00-03) and beyond.
- **Federated evolution** — populations on independently owned nodes across the
  Macula mesh, sharing genomes or facts without a central server.
- **Substrate efficiency** — the batched-population evaluator NIF (one Rust call
  per generation) as the throughput lever under all of the above.

## Why this programme exists

Faber is a BEAM-native engine; distribution is its reason to exist rather than a
scaling afterthought. GPU/Python neuroevolution centralises on one big machine;
faber's process-per-neuron model and the mesh make *federated* evolution across
many small, independently owned nodes natural. That is one of Macula's four
workload classes (federated AI) made concrete, and it is a research direction the
extractive-ML world structurally will not take. This programme is where the
research agenda and the product thesis are the same thing.

## Central questions

1. Does an **island model** across the beam cluster reach solutions faster (in
   wall-clock and in evaluations) than a single panmictic population, and does
   migration topology matter for the pole / T-maze suite?
2. Can evolution run **federated** across mesh nodes (genome or elite exchange as
   integration facts) with no central coordinator, and how does partial failure or
   node churn affect convergence? (Fault tolerance as a first-class result.)
3. What speedup does the **batched-population NIF** deliver, and how far does it
   shift which sweeps are affordable?

## Where to start (the good first experiment)

- **P5-a: two-island run on the dev box**, migration every K generations, versus a
  single population at matched total evaluations. Smallest real distribution result.
- **P5-b: batched-population evaluator NIF** (one Rust call per ES generation),
  then re-measure P5-a. High leverage; also unblocks large sweeps in P2 and P4.
- **P5-c** (horizon): federated run across beam00-03 over the mesh, elites shared
  as integration facts; measure convergence under induced node churn.

## The BEAM-native angle

The whole programme. Island migration is message passing; a federated population is
a mesh of nodes exchanging facts; node failure is a supervised restart, not a lost
run. Distribution, fault tolerance, and non-central coordination are what the
substrate gives for free. Composes with P9 (federated evolution of robot
collectives is P5 x P9) and P4 (a mesh-distributed QD archive).

## Prior art (report as replication, not discovery)

- Island / distributed EA models (Tanese 1989; Whitley et al., island GAs); Cantu-Paz,
  "A survey of parallel genetic algorithms."
- OpenAI-ES (Salimans et al. 2017) — evolution strategies as a scalable RL
  alternative, notable for near-linear parallel scaling (the throughput argument).
- Watson, Ficici & Pollack 2002, embodied evolution (server-free distributed
  evolution; the P5 x P9 bridge).

## Dependencies

The island arm needs only migration plumbing over the existing engine. The
federated arm needs the mesh integration-fact path (Macula), aligning with the
rest of Raf's stack. Ready to start on the island arm; the NIF is defined infra.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`CHARTER_P9_DARS.md`](CHARTER_P9_DARS.md) — distributes the robots (composes with P5)
- [`CHARTER_P2_SEARCH_STRATEGIES.md`](CHARTER_P2_SEARCH_STRATEGIES.md) — the batched NIF serves both
- [`CHARTER_P4_OBJECTIVES.md`](CHARTER_P4_OBJECTIVES.md) — distributed QD archive
