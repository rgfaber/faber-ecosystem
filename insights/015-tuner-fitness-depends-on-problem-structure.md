# 015 — The right tuner depends on problem structure. Shallow tuning wins control, deep tuning wins precise-weight. XOR was out of domain.

**Verdict:** confirmed (signed; resolves 014's open question) · **Date:** 2026-07-20
**Uses:** the cart-pole scape (`pb_sim`) built this session, the tuning modules
from 014, the XOR results in 012/013/014.

## The question 014 left open

Insight 014 found that DXNN2's own shallow default tuner (`dynamic_random` +
`wsize_proportional`, ~11 attempts) does NOT solve XOR at a matched budget,
while the crude deep tuner (`all` + `const 60`) does. It left two threads: is
the shallow tuner genuinely worse, or is XOR a pathological regime for it? The
stated way to settle it: run a bigger, differently-structured problem where
shallow tuning is *supposed* to shine. That problem is pole balancing.

We built the cart-pole scape (single pole, with velocity — full state, Markov,
feedforward-solvable) and ran the same two tuners.

## The result

Both tuners, `pb_1_with_velocity`, pop 20, up to 30 generations, 5 runs each.
"Solved" = an agent balanced the pole to the goal (goal_reached from the scape).

| Tuner | Solved | Generation of solve | Evaluations to solve |
|---|---|---|---|
| Default (shallow, ~11 attempts) | **5/5** | 1, 1, 2, 3, 7, 16 (varies) | 240, 469, 698, 1644, 3532 — median **698** |
| Crude (deep, 60 attempts) | **5/5** | 1, 1, 1, 1, 1 | 1200 every run (deterministic) |

Raw data: `015-raw-pole-tuner-comparison.txt`.

The shallow default that FAILED XOR (0/3, plateau ~1.2) **solves pole reliably
and cheaply** — median 698 evaluations, sometimes in a single generation. 014's
open thread is settled: **XOR was a pathological regime for shallow tuning, not
evidence the tuner is broken.** The ordering flips.

## The full picture

Put both problems in one table and the structure is unmistakable:

| Problem | Landscape structure | Shallow default (~11) | Deep crude (60) |
|---|---|---|---|
| XOR | precise-weight puzzle | **fails** (0/3, plateau ~1.2) | solves, ~20,125 evals |
| pole (1, with velocity) | smooth control / Markov | **solves**, median 698 evals | solves, 1200 evals, gen 1 |

**Neither tuner dominates.** Each wins the problem that matches it:

- **Precise-weight puzzles (XOR)** need deep per-agent hill-climbing. The
  solution is an exact weight combination in a tiny network; only sustained
  local search within one lifetime lands on it. Shallow nudges plus generational
  selection cannot assemble it — they plateau.

- **Control problems (pole with velocity)** have a smooth fitness landscape and
  a small linear controller suffices. Shallow tuning plus selection finds it
  cheaply; deep tuning *over-invests* — 60 attempts per agent when a handful
  would do — paying a fixed 1200-eval-per-generation tax whether or not it helps.

## Why this vindicates DXNN2's default

Sher's default is `dynamic_random` + `wsize_proportional` — the shallow one.
DXNN2's target domain is control and robotics: pole balancing, prey/predator
foraging, T-maze navigation, forex trading. Every one of those is a control
problem with the smooth-landscape structure where shallow adaptive tuning is
efficient. **His default is correct for his domain.** Insight 014 judged it on
XOR — a precise-weight puzzle that is not representative of that domain, and is
in fact the one shape of problem where shallow tuning is weakest. Out-of-domain
benchmarking made a good default look broken.

## A difficulty inversion worth stating

Pole balancing is the "serious" reinforcement-learning benchmark; XOR is the
textbook toy. Yet pole-with-velocity solves in ~700 evaluations and XOR needs
~20,000 — a ~30x inversion. **Evaluation cost tracks landscape structure, not
intuitive difficulty.** Pole-with-velocity is Markov (full state observed) with
a smooth gradient to a linear controller; XOR is a needle-in-a-haystack weight
target with no gradient until you are almost exactly right. The "hard" problem
is easier for evolution than the "easy" one.

## Reliability vs efficiency, within pole

A smaller second finding: on pole the two tuners trade different currencies.
Crude is *deterministic* — gen-1 solve, 1200 evals, zero variance across 5 runs.
Default is *adaptive* — median 698 evals (cheaper), but a wide range (240 to
3532) because it sometimes needs several generations. Deep tuning buys
consistency; shallow tuning buys average-case efficiency at the cost of variance.

## Consequences for the platform

- **The genotype default stays shallow (DXNN2-faithful).** It is right for the
  control problems that are the platform's real target. XOR keeps its explicit
  deep-config pin in `xor_evolves_tests`; that is documented as the exception,
  not the rule.

- **Tuner choice is a first-class experiment axis, not a constant.** The factor
  that matters is problem structure (precise-weight vs control), so the
  experiment matrix must pair each tuner against both kinds of problem. We now
  have one of each with signed numbers.

- **Next, the axis that actually needs new machinery: memory.** Pole *with*
  velocity is feedforward-solvable and says nothing about recurrence or LTC. The
  pole *without* velocity variant (built, `pb_1_without_velocity`) is non-Markov
  — the network must integrate velocity itself — and is the first problem that
  will discriminate feedforward from recurrent/LTC substrates. It requires
  working recurrent evaluation (first-cycle seeding), still the open
  prerequisite. That is where the LTC question from the user's point 2 gets a
  real test, and where step 3 (double pole, no velocity — the chaos crown) leads.
