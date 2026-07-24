# Programme 7 — Coevolution / Self-play (Interaction)

**Status: CLOSED @062 (2026-07-24).** Corpus: insights **053-057** (the coevolution toolkit on
numbers games and the first embodied rung) and **058-062** (the Flatland/ALife front, run as
P7 x P5/P6 convergence; see [`PLAN_FLATLAND.md`](PLAN_FLATLAND.md)).

> **The arc, in one line:** across six embodied rungs this simple neuro-ALife substrate refused to
> produce persistent or escalating coevolutionary dynamics, from six independent angles. 057: no
> coupling on the bare grid (a co-adapting opponent buys no more progress than a diverse static one).
> 059: reactive fleeing is never learned under ecological survival fitness. 060: a real near-predator
> repulsion exists, but whether it drives the 059 collapse is untested. 061: fixed-greedy open
> populations have no coexistence regime, they boom-bust to mutual extinction. 062: no eco-
> evolutionary timescale, because the only restraint available (decline-to-hunt) is
> self-starvation, so selection cannot act. The honest next step is a MATERIALLY DIFFERENT substrate
> (spatial structure with real refugia, or a restraint lever that is not decline-to-hunt), not
> another rung on this world.

## The axis

**Interaction**: fitness is not a fixed function but emerges from agents
interacting, holding the network and the operators fixed. Where P4 changes the
selection pressure on a fixed problem, P7 makes the **problem itself move**.

- **Competitive coevolution** — predator/prey, pursuit/evasion, arms races where
  each side's fitness is defined against the other.
- **Self-play** — an agent evolves against copies or a history of itself.
- **Cooperative coevolution** — subcomponents coevolve to compose a whole. Note:
  CoSyNE (cooperative synapse coevolution) lives in P2 as an *optimizer*; P7 is
  cooperation as *task structure*, not as a weight-search decomposition.
- **Open-endedness** — environments and agents coevolve so that novelty keeps
  being generated (POET), with no fixed target at all.

## Why this programme exists (and why it is separate from P4)

P4 still optimises against a fixed (if diversity-preserving) objective. P7 removes
the fixed objective: the fitness gradient is a moving target created by other
agents. This is where arms races, the Red Queen dynamic, and open-ended innovation
come from, and it is the regime in which evolution produces its most surprising
results. It is also the natural home for the swarm-vs-environment and
multi-collective dynamics that P9 will eventually need.

## Central questions

1. Does **competitive coevolution** on a pursuit/evasion scape produce an arms
   race (measurable via a dominance/CIAO tournament across generations) rather
   than cycling or collapse?
2. Can a minimal **open-ended** setup (agents plus a parameterised, coevolving
   environment) keep generating new solved challenges, or does it stagnate?

## Where to start (the good first experiment)

- **P7-a: a two-population pursuit/evasion scape**; track fitness against a fixed
  historical opponent set (to defeat the "fitness is relative" measurement trap),
  and plot the dominance tournament. The smallest honest coevolution result.
- **P7-b** (horizon): a POET-lite loop where environment parameters coevolve with
  agents; measure whether the frontier of solved environments keeps advancing.

## The BEAM-native angle

A coevolving ecosystem of long-lived, interacting agents under supervision is a
natural BEAM shape: always-on, fault-tolerant, message-passing. Open-ended runs are
meant to run indefinitely, which suits OTP far better than a Python loop that must
not crash for a week. Composes with P9 (coevolving robot collectives) and P5
(distributed ecosystems across the mesh).

## Prior art (report as replication, not discovery)

- Hillis 1990, coevolving sorting networks with parasites; Sims 1994, coevolved
  competing virtual creatures.
- Nolfi & Floreano, competitive coevolution and the Red Queen; Ficici & Pollack,
  coevolutionary dynamics and measurement (dominance / CIAO).
- Wang, Lehman, Clune, Stanley 2019, POET (open-ended coevolution of environments
  and agents).

## Dependencies

Needs a two-population / interactive scape and coevolutionary fitness bookkeeping
(the measurement discipline is the hard part, not the plumbing). A horizon
programme: chartered to complete the map, most valuable once the engine reliably
solves fixed tasks so that moving-target dynamics are the object of study, not a
confound.

## Related

- [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md) — the map
- [`CHARTER_P4_OBJECTIVES.md`](CHARTER_P4_OBJECTIVES.md) — fixed vs moving objective
- [`CHARTER_P9_DARS.md`](CHARTER_P9_DARS.md) — coevolving collectives
- [`../docs/RESEARCH_PRIOR_ART.md`](../docs/RESEARCH_PRIOR_ART.md)
