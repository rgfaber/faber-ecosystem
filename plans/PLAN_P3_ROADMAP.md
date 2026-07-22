# Programme 3 (Meta-learning) — roadmap

Where P3 stands and the next phase. Charter: [`CHARTER_P3_META_LEARNING.md`](CHARTER_P3_META_LEARNING.md).

## The thesis so far (insights 031-034)

Evolved plasticity (memory-by-LEARNING: a local rule writes the cue into the
weights within an episode) vs CfC (memory-by-STORAGE: the cue sits in a neuron's
continuous state). On the T-maze:

- **031** — equal on the easy task: both solve delay-2 10/10 in ~1 generation.
- **032/033** — plasticity scales more gracefully with the memory horizon: both
  solve to delay 64, but CfC's evals-to-solve grow ~8x (130->1038) vs plastic ~2.6x
  (120->312). A **cost** gap.
- **034** — plasticity is noise-robust: at sensor noise sigma=0.5 CfC FAILS (0/8),
  plastic holds (8/8). A **capability** gap.

**Emerging thesis:** an evolved local learning rule is a more robust memory
mechanism than a fixed continuous-time state -- cheaper to find as the horizon
grows, and it survives noise. Two things it does NOT yet establish: (a) WHY (the
mechanism is hypothesised, not measured), and (b) whether the advantage holds for
richer rules and harder memory tasks. That is the next phase.

## Infra already built

- `network_evaluator:evaluate_with_plasticity/3` — global ABCD-Hebbian, process-free.
- `sep_cma_es`, `tmaze_sim` (delay via params), the batched CfC-population NIF.
- The experiment-viz pattern (emit JSON -> Artifact dashboard).
- Parallel runs via git worktree (033+034 ran that way).

## Next phase — three experiments, prioritised

### EXP-035 — the mechanism probe (do FIRST; cheap; closes the honesty gap)

**Question:** WHY does noise break CfC and not plasticity? 034's mechanism is a
hypothesis. Measure it directly.
**Method:** take a SOLVED CfC agent and a SOLVED plastic agent (best genomes from
032/031), run them on the noisy T-maze, and instrument the MEMORY carrier over a
trial: for CfC the hidden internal_state, for plastic the weight displacement. Plot
the "held cue signal" (state / imprint magnitude, and its sign-consistency with the
cue) across the corridor at sigma 0 / 0.3 / 0.5. Prediction: the CfC state's cue
signal degrades / flips under noise across the corridor, the plastic imprint does
not. No evolution -- just instrumented replay, like the 031 latch trace.
**Decides:** turns 034's tentative mechanism into a measured finding (or refutes it).
**Infra:** a capture harness (reuse the 031 trace pattern); no new src.

### EXP-036 — richer plasticity rules (does the lead widen? does it unlock harder tasks?)

**Question:** the current rule is one GLOBAL {A,B,C,D,Eta}. Do richer rules do more?
**Arms (rule families):**
- **Oja's rule** — dW = Eta*(pre*post - post^2*w): self-normalising, stable, avoids
  the weight blow-up the ABCD clamp currently guards.
- **Per-layer rule** — separate {A,B,C,D,Eta} for input->hidden vs hidden->output
  (genome +5).
- **Reward-modulated (neuromodulated)** — gate the update by the trial reward, the
  classic lifetime-learning rule; the T-maze already returns reward at the junction.
**Method:** compare rule families on the T-maze under the 034 noise sweep and the
033 delay sweep (the regimes that already rank mechanisms). Solve rate + evals.
**Decides:** whether richer local rules widen plasticity's robustness lead, and
which family is the P3 workhorse.
**Infra:** `evaluate_with_plasticity` needs a rule-TYPE parameter (Oja / ABCD) and,
for per-layer, per-layer coefficients; for reward-modulated, the eval loop must
thread the reward into the update. Real src work, kept elvis-clean.

### EXP-037 — combined stressors + a harder memory task

**Question:** do the two stressors compound (delay AND noise), and does the plastic
advantage survive a task that needs MORE than one held bit?
**Arms/method:**
- **Combined** — the 033 delay sweep WITH the 034 noise, one grid. Prediction: CfC
  collapses faster than either stressor alone; plastic holds longer.
- **Harder task** — a multi-cue / sequential-recall T-maze variant (remember 2 cues,
  or a cue sequence), which stresses memory CAPACITY not just duration. A new scape.
**Decides:** whether the P3 thesis generalises beyond single-bit, single-stressor
memory.
**Infra:** combined reuses 033a/033b; the harder task needs a new scape module
(`seq_tmaze_sim` or similar), the biggest new piece.

## Phase status (035-037 DONE, 2026-07-22)

- **035** DONE — mechanism measured: CfC state is leaky (~25x decay to a ~4% margin
  even noiseless), plastic imprint stable (~100%). 033/034 mechanism now measured.
- **036** DONE — ran Oja (not per-layer/reward-mod): Oja is SIMPLER (1-param), and
  LESS robust than ABCD (breaks 2/8 at sigma=0.5 where ABCD holds 8/8). Correction:
  expressiveness (param count), not the self-normalising property, buys robustness;
  the genuine "richer rule" win needs MORE params than ABCD.
- **037** DONE — combined delay x noise: stressors COMPOUND for CfC (0/8 at
  sigma=0.5 both delays, fitness falls with delay); plastic solves the whole grid
  8/8. Thesis (learning > storage for robust memory) holds on horizon, noise, both.

## Open chapter — EXP-038: does expressiveness buy CAPACITY? (active)

036 said the real richer-rule test needs MORE params than ABCD; the roadmap's
"harder task" is a capacity task. EXP-038 unites them: a new **multi-cue T-maze**
(`multi_cue_tmaze_sim`, two held bits — recall cue A at junction 1, cue B at
junction 2) against four arms — none (ceiling), cfc (multi-dim state), abcd
(global 5-param), **pc** (per-connection ABCD, each synapse its own {A,B,C,D},
new `evaluate_with_plasticity` rule `{pc, CoeffLayers, Eta}`). Hypothesis: one
global rule cannot specialise synapses to two independent bits, so global ABCD
ceilings where CfC's state and the expressive per-connection rule break through.
A clean negative (global ABCD copes fine, or even pc cannot) is equally valuable.
Infra built: per-connection plasticity (network_evaluator, + unit tests),
multi_cue_tmaze_sim scape. `none` confirmed at exactly 50 (memoryless ceiling).

## The deep question (beyond this phase)

All of 031-037 is memory WITHIN an episode (hold a cue). The ultimate P3 test is
learning ACROSS episodes / a task distribution -- true learning-to-learn, where the
evolved plasticity discovers a rule that adapts to tasks it never saw in evolution
(the Baldwin/Chalmers programme). That needs a task DISTRIBUTION, not one T-maze, and
is the natural capstone once the rule-family and mechanism questions settle.

## Sequence and rationale

1. **035 first** — cheap, no evolution, closes the mechanism honesty gap; makes
   033/034 a measured story, not a suggestive one.
2. **036 next** — the active front: richer rules are the substantive P3 direction and
   the one most likely to widen the thesis.
3. **037 after** — compounding + capacity, once 036 says which rule family to carry.
4. **learning-to-learn** — the capstone, when the mechanism and rule questions settle.

One active experiment at a time (the programme discipline). Each is a signed insight,
positive or negative; the emerging thesis (learning > storage for robust memory) is
strong enough that a clean NEGATIVE (a richer CfC, or a task where storage wins)
would be as valuable as another confirmation.
