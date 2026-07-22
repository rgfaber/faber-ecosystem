# Programme 3 (Meta-learning) — Synthesis of EXP-031 to EXP-045

A programme-level account of what the meta-learning track established, why, and where
it stops being true. Charter: [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md).
Roadmap: [`PLAN_P3_ROADMAP.md`](PLAN_P3_ROADMAP.md). Every claim below traces to a
signed insight (031-044) with persisted raw data; the interactive dashboard shows the
evidence visually.

## Abstract

Programme 3 asked a single question in two regimes: when a neural network must keep
information across time, is it better to HOLD it (memory-by-storage: a continuous-time
CfC neuron's internal state) or to WRITE it (memory-by-learning: an evolved local
plasticity rule that changes the weights)? Across fourteen experiments the answer
inverts between the two regimes, and the reason why is the programme's main result:

- **Memory (hold a cue within an episode, 031-041):** memory-by-learning wins
  decisively under stress. A re-imprinted weight survives noise and long delays that a
  decaying state cannot, and the advantage is largest for the most EXPRESSIVE rule
  (per-connection), whose value is an EVOLVABILITY property (it makes the robust region
  reachable), not a static one.
- **Learning (discover a reward mapping across an episode, 042-045):** the mechanisms
  CONVERGE. Reward-gated plasticity learns and re-learns near-optimally, but so does a
  recurrent state given the reward as input, and expressiveness buys nothing. Three
  independent stressors fail to separate them, because the task integrates reward over
  many trials and integration filters out the per-step noise that would expose a
  mechanism's substrate.

The unifying principle: **mechanism choice matters when the stressor reaches an exposed
substrate, and not otherwise.** Memory HOLDS a signal instantaneously, exposing the
substrate; lifetime learning ESTIMATES by integration, which filters the substrate away.
That is why memory separates the mechanisms and lifetime learning does not.

## The instrument

All results run on one reproducible substrate in `faber-tweann` (tested, elvis-clean):

- **Mechanisms.** CfC/LTC neurons (memory-by-storage) and three plasticity rule
  shapes (memory-by-learning): a global ABCD-Hebbian rule `dW = Eta*(A*pre*post +
  B*pre + C*post + D)`, Oja's self-normalising rule, and a per-connection ABCD rule
  where each synapse carries its own `{A,B,C,D}` (`evaluate_with_plasticity/3`).
- **The third factor.** `evaluate_with_neuromod/4` gates every weight change by a
  neuromodulator M (reward): `dW = M*Eta*(...)`, the piece deferred until the capstone.
- **Optimiser.** `sep_cma_es` (separable CMA-ES), process-free, with an optional
  per-generation trace for evolvability studies.
- **Benchmarks.** `tmaze_sim` (cue memory, duration), `multi_cue_tmaze_sim` (capacity,
  two held bits), `reversal_bandit_sim` and `prob_reversal_bandit_sim` (lifetime
  learning: a hidden, swapping reward mapping).

## Regime 1 — Memory (031-041)

The T-maze forces a network to carry a cue across a corridor. A memoryless network
caps at exactly 50 (chance); storage and learning both reach it.

- **031 (tie).** On the easy task both mechanisms solve 10/10 in ~1 generation. The
  plasticity RULE does the memory (a zero rule is memoryless); evolution finds a
  non-latch dynamic that still reads out correctly.
- **032/033 (cost gap).** As the corridor lengthens (delay 2 to 64) both still solve,
  but the CfC's evolutionary cost grows ~8x (130 to 1038 evals) while plasticity grows
  ~2.6x (120 to 312). Memory-by-learning scales more gracefully with the horizon.
- **034 (capability gap).** Add sensor noise and CfC BREAKS: at sigma=0.5 it solves
  0/8, plasticity 8/8. The first regime where the mechanisms diverge on capability.
- **035 (mechanism, measured).** The CfC internal state is LEAKY: its cue signal
  decays ~25x across an 8-step corridor to a ~4% margin, even noiseless. The plastic
  imprint is STABLE (~100%). This is why noise breaks CfC (it destroys the thin decayed
  margin) and plasticity holds (there is no thin margin to destroy).
- **036 (expressiveness, not elegance).** The self-normalising Oja rule (1 param) is
  LESS robust than ABCD (5 params): 2/8 at sigma=0.5 where ABCD holds 8/8. Robustness
  comes from parameter count (expressiveness), not from the elegant stability property.
  The genuine richer-rule test needs MORE parameters than ABCD, not fewer.
- **037 (stressors compound).** Delay and noise together compound for CfC (0/8 at
  sigma=0.5 at both delays, deepening with delay); plasticity solves the whole grid.
- **038 (capacity alone does NOT separate; a corrected confound).** On a two-bit task,
  storage and per-connection plasticity TIE at 7/8. An initial "storage is weakest"
  reading was a signed CORRECTION: naive CfC's 2/8 was a reset-protocol confound
  (cross-trial state interference), not a capacity limit; with a per-trial reset CfC
  solves 7/8. Capacity, unlike noise, is not a separator.
- **039 (the decisive separation).** Capacity AND noise together: per-connection
  plasticity solves 8/10 where global ABCD manages 1/10 and CfC 0/10 (Fisher p~0.0007).
  The decisive axis under the hardest stress is per-SYNAPSE expressiveness, and,
  notably, global plasticity is as fragile as storage here. Expressiveness, not the
  learning-vs-storage dichotomy, is what survives.
- **040 (a killed hypothesis).** The natural explanation, that per-connection reduces
  INTERFERENCE between the two bits, is REFUTED: per-connection solvers have the
  HIGHEST readout cross-talk (0.82), not the lowest. Cross-talk (magnitude leak) does
  not track robustness because decisions are sign-based. A signed negative that
  redirected the inquiry to evolvability.
- **041 (evolvability, confirmed).** Instrumenting the SEARCH: under capacity+noise all
  three mechanisms converge to a shared ~76 (one-and-a-half-bit) plateau by generation
  50 (CfC even leads early), then only per-connection climbs out toward the two-bit
  solution (76.9 to 89.3 over gen 50-200); global reaches ~85, CfC stalls ~82. The
  advantage is the EVOLVABILITY of the noise-robust region: the richer parameterisation
  makes it reachable by evolution, not a static property of the final weights.

**Regime 1 verdict:** memory-by-learning beats memory-by-storage on robustness
(gracefully with the horizon, decisively under noise), the winning axis under combined
stress is per-synapse expressiveness, and its advantage is an evolvability property.
The mechanism is measured (leaky state vs stable imprint), one plausible story was
falsified (interference), and one metric-choice self-correction was recorded.

## Regime 2 — Lifetime learning (042-045)

Everything above is memory: HOLDING information you were given. The capstone tests
LEARNING: discovering, from reward alone, a mapping you were never shown, and
re-discovering it when it swaps. The mechanism is the third factor (reward gating the
plasticity), deferred since 036.

- **042 (existence, positive).** On the reversal bandit (hidden good arm, swapped
  mid-life, reward the only signal) reward-gated plasticity LEARNS and RE-LEARNS: both
  global and per-connection solve 8/8 at ~90-91 (near-optimal) vs a fixed net at exactly
  50. The traces show textbook reversal learning (explore, lock, reverse, re-lock).
  Lifetime learning is tractable, not a wall. On this easy 1-bit task the simpler global
  rule is CHEAPER than per-connection (expressiveness unneeded is a cost, as everywhere).
- **043 (the fair contest, null).** Probabilistic reward (0.8/0.2) fed as INPUT so a
  recurrent state competes on equal footing (RL^2-style). All four mechanisms (storage,
  global, per-layer, per-connection) TIE at ~92-93. "Learning beats storage" does NOT
  transfer: given reward as input, a recurrent state implements the learning as well as
  reward-gated plasticity. Expressiveness is a cost again.
- **044 (a stressor, still null).** Reversal FREQUENCY (1 to 7 reversals per life) is a
  real difficulty axis but degrades all three mechanisms together (~4 points), with only
  a weak, non-significant hint that per-connection is the most robust.
- **045 (the substrate stressor, still null, and the reason found).** Feedback-sensor
  NOISE, the direct 034 analogue, was predicted to break storage (whose only reward
  channel is the sensor) while sparing clean-modulator plasticity. It did NOT: across
  sigma 0 to 1.0 all three arms hold at ~91-92, and clean-M vs noisy-M plasticity are
  indistinguishable. The reason is INTEGRATION: the task accumulates reward over ~30
  trials, and integration averages out per-step noise (~sigma/sqrt(N)) on every channel,
  so no substrate is exposed. The architectural hypothesis (a clean neuromodulatory
  channel buys robustness) is disconfirmed.

**Regime 2 verdict:** reward-gated plasticity is a working, near-optimal lifetime-
learning mechanism, but the storage-vs-learning-vs-expressiveness distinctions that
dominated memory VANISH here, across three independent stressors. Where the task is
solved by integrating reward over trials, integration filters the per-step noise that
would expose a mechanism's substrate, so the mechanisms converge.

## The synthesis

Two through-lines run the whole programme.

1. **Separation is a property of the stressor, not the mechanism, and the mechanism is
   instantaneous-hold vs integrated-estimate.** Memory HOLDS a signal instantaneously
   across a corridor: there is no averaging, so the substrate's noise-response is exposed
   directly. A decaying CfC trace loses a noisy cue where a re-imprinted weight does not
   (035), so the mechanisms diverge, and the more expressive rule diverges furthest
   (039). Lifetime learning ESTIMATES a reward mapping by integrating over many trials:
   integration is a low-pass filter that averages out per-step noise on any channel
   (observation or modulator, 045), so the substrate is hidden and the mechanisms
   converge (043, 044, 045). The right question is never "which mechanism is better" in
   the abstract, but "does the stressor reach an exposed substrate." Instantaneous
   holding exposes it; integrated estimation filters it away.

2. **Expressiveness is a cost when unexploited and decisive when needed.** Per-connection
   plasticity (240 rule parameters) is slower to evolve and no better on every easy or
   task-attacking regime (013/015/017 memory capability; 036 Oja; 042/043 lifetime), and
   is the sole survivor exactly once, under capacity+noise (039), where its evolvability
   opens the robust region (041). Capability that cannot be exploited is pure search
   cost; the programme sees this pattern seven times.

## What is established, and what is open

Established (with signed evidence): the leaky-state vs stable-imprint mechanism (035);
that expressiveness, not elegance, buys robustness (036); the decisive per-connection
separation under capacity+noise and its evolvability basis (039, 041); that the
interference explanation is false (040); that reward-gated plasticity learns and
re-learns (042); that the memory thesis does not transfer to lifetime learning across
THREE independent stressors (043 reward-SNR, 044 frequency, 045 feedback noise); and the
reason (045): integration over trials filters per-step noise on any channel, so the
lifetime substrate is hidden. The lifetime-learning equivalence is now conclusive.

Open: task-distribution GENERALISATION (train on some reversal structures, test on
unseen), whether a stressor that DEFEATS integration (trial-correlated noise, or a very
short window) can re-expose the substrate, confirmation of any headline under a second
optimiser, and above all the SCALE/REAL-TASK bridge (see below). The lifetime-stressor
line is closed.

## Engineering value and limitations

These experiments are mechanism SCIENCE, but they produced real engineering, and the
findings are actionable. Three things are worth separating: what was built, what the
findings tell a builder, and what is still missing.

### Artifacts already in faber-tweann

The questions forced the construction of a tested, reusable neuroevolution toolkit that
did not exist before the arc. Each is permanent, elvis-clean, unit-tested, and pushed:

| Component | Module | What it is |
|---|---|---|
| Separable CMA-ES | `sep_cma_es` | Process-free, O(N), drop-in fitness interface, optional evolvability trace. A real search backend. |
| Plasticity engine | `evaluate_with_plasticity/3` | Three rule families (global ABCD, Oja, per-connection) behind one call. |
| Three-factor rule | `evaluate_with_neuromod/4` | Reward-gated plasticity, the core primitive for any meta-RL / lifetime-adaptation use. |
| State introspection | `get_internal_state/1` | Made the 035 mechanism measurable; useful for debugging any recurrent net. |
| Benchmark suite | `tmaze_sim`, `multi_cue_tmaze_sim`, `reversal_bandit_sim`, `prob_reversal_bandit_sim` | A capability-regression harness for memory and learning mechanisms. |

Before this arc faber-tweann was a DXNN2-derived TWEANN library; it now also carries a
modern optimiser, a plasticity/neuromodulation engine, and a benchmark suite. This is
the "one engine" that P1/P2 and the P8/P9 couplings are meant to consume.

### Design inputs the findings produce

Several results are directly actionable build heuristics:

- **Default to the simplest mechanism.** Expressiveness was a cost seven times
  (013/015/036/041/042/043) and decisive exactly once (039). Use storage or a global
  rule by default; reach for per-connection only under COMBINED stress
  (capacity + noise). Do not pay for knobs you cannot exploit.
- **For robust memory in noisy/edge settings, prefer imprint over state (035).** A
  decaying recurrent state loses its signal to noise where a re-imprinted weight does
  not.
- **Match the memory carrier's reset to the task's trial structure (038).** A
  leaky-state self-clear that works for one held item silently corrupts multi-item tasks.
- **Do NOT over-engineer the reward path for lifetime learning (EXP-045).** The earlier
  tentative recommendation, to architect a separate clean neuromodulatory channel for
  noisy-reward adaptation, was DISCONFIRMED: clean-M and noisy-M plasticity performed
  identically under feedback noise, because integration over trials already filters
  per-step noise on any channel. Rely on integration; reserve mechanism care for
  INSTANTANEOUS memory under noise, where the substrate is actually exposed.

And the conceptual lens for mechanism SELECTION: separation is a property of the
stressor, not the mechanism (attacks-substrate vs attacks-task), and its mechanism is
instantaneous-HOLD (substrate exposed) vs integrated-ESTIMATE (substrate filtered).

### What is missing (the honest gap)

These are mechanism studies on toy benchmarks (tiny nets, T-mazes and bandits, n=8).
They tell you which mechanism has which PROPERTY, cheaply and rigorously. They do NOT
yet establish behaviour at scale, on a real task, or under production constraints. The
artifacts need hardening/scaling; the findings are INPUTS to engineering, not finished
engineering. The bridge, and the highest-value step after P3, is to drive ONE real
faber-neuroevolution task end to end with a validated mechanism (per-connection
neuromodulated plasticity + sep-CMA-ES), turning "we understand the mechanism" into "we
shipped a capability".

## Method notes (why the results are trustworthy)

- **Signed insights, reported as replication.** Each experiment is a numbered, dated
  insight citing file:line or a measurement; negatives are first-class; n>=8 (often 10)
  for every rate claim; single-run results are labelled as such.
- **Two claims were retracted by their own data.** The "storage is weakest on capacity"
  reading (corrected in 038 by a reset control) and the "per-connection reduces
  interference" hypothesis (refuted in 040). The programme changes its mind on evidence.
- **Reproducible substrate.** Mechanisms, rules, scapes, and the traced optimiser are
  permanent, tested, elvis-clean modules in faber-tweann. Experiment runners are kept out
  of the committed test suite (wall-clock), but as of 2026-07-22 they are ARCHIVED into
  the corpus beside their raw feed as `insights/NNN-runner-*.erl`. The record is the
  signed insight, its persisted raw, and the runner that produced it.
- **Known gap in the record (2026-07-22).** Runners for insights 031-046 predate that
  decision and were never committed anywhere, so they are not recoverable. Those insights
  rest on their raw feeds alone and cannot be audited at the code level. Archiving starts
  at 047.

## Pointers

- Insights 031-044: [`../insights/INDEX.md`](../insights/INDEX.md).
- Dashboard (interactive, all evidence): the P3 Artifact (memory + learning panels).
- Programme map and charters: [`PLAN_PROGRAMMES_ROOT.md`](PLAN_PROGRAMMES_ROOT.md).
