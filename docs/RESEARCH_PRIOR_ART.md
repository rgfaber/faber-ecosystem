# Prior Art — where the faber insights sit in the literature

A grounding pass (2026-07-21, WebSearch). Blunt calibration first: **none of the
insights 013-020 are novel results.** Each independently re-derives a
well-established principle, on the DXNN2 / Erlang process-per-neuron substrate.
That is a strength (the harness measures real phenomena) but must never be
reported as discovery. The value is the *substrate-specific, reproducible,
signed corpus* — priors for THIS toolchain — not new science.

This document maps each finding to its prior art so the corpus stays honest and
so a future Neurolab-as-a-Service knows which priors are ours vs the field's.

## The unifying principle — capability value = task-match x budget (013/015/017/018/019)

Two established pillars, combined:

- **No Free Lunch** (Wolpert & Macready 1997): averaged over all problems no
  algorithm/capability beats another; value is problem-dependent. Our "a
  capability only pays if the task needs it" is the NFL corollary. (Caveat, and
  the field's own: "When and Why Metaheuristics Researchers Can Ignore No Free
  Lunch Theorems", arXiv 1906.03280 — NFL's practical bite is limited.)
- **NEAT complexification** (Stanley & Miikkulainen 2002, *Evolutionary
  Computation* 10(2); stanley.cec02, stanley.phd04): start minimal, add structure
  only when fitness rewards it. Their headline number is our capability-cost
  principle verbatim: **random-starting NEAT was ~7x slower than minimal-start**,
  because unneeded dimensions force search through a higher-dimensional space and
  waste time. Our 013 (topology ~37x on a known-topology problem), 017/018
  (memory a tax when the budget can't exploit it) are the same phenomenon on our
  substrate.

The "budget" half is classical bias-variance / sample complexity: a richer
hypothesis class needs more data/evaluations to be worth its capacity.

## Tuner depth (014/015) — memetic local-search intensity

Textbook memetic-algorithm theory (Moscato 1989; Neri, Cotta & Moscato eds.
2012; "Memetic Algorithms: Parametrization and Balancing Local and Global
Search", arXiv 1109.6441). The field's named parameters are exactly ours:
**local search depth** (= tuning_duration), **frequency/probability** (=
tuning_selection). The established warning — "if local search is applied to all
individuals every generation, almost all computation is spent on local search and
global search is starved" — is our insight 015 (deep all-neuron tuning wastes
evaluations on smooth control) almost word for word. DXNN2's adaptive
tuning_selection/duration exist because Sher was implementing this known balance.

## Pole balancing (015/017/018) — the DPNV benchmark

Double-pole-balancing WITHOUT velocity (DPNV) is THE canonical non-Markovian
neuroevolution benchmark: Wieland 1991 (introduced it); Gomez & Miikkulainen,
"Solving Non-Markovian Control Tasks with Neuroevolution", IJCAI-99 (ESP); Gruau
Cellular Encoding; Stanley & Miikkulainen NEAT (EC02) benchmarked on it. Our
pb_sim (with/without velocity) is a faithful re-use of this standard, and the
literature's framing — DPNV *requires* recurrent memory, the with-velocity
version is Markov and feedforward-solvable — is exactly what 015-019 rest on.
Also standard: XOR as the canonical deceptive/non-separable hard case (Minsky &
Papert 1969), so 015's "pole easier than XOR" is landscape-deception folklore
(Goldberg; Kauffman NK), not a new result.

## LTC / CfC (017/018/019/020) — continuous-time / liquid networks

Liquid Time-Constant networks (Hasani, Lechner, Amini, Rus, Grosu — LTC, AAAI
2021) and Closed-form Continuous-time networks (Hasani et al., *Nature Machine
Intelligence* 4, 2022; arXiv 2106.13898). The CfC gate we use is theirs. Crucial
for us: in LTC/CfC the **time constant sets the memory timescale** — how fast a
neuron's state responds and forgets. So our 020 finding "CfC memory is bounded by
tau and decays with delay" is not a discovery; it is the defining property of the
model. Note the tension with their headline results: LTC/CfC are shown to EXCEL
at control (autonomous driving, drone flight). Our 018 "CfC hurt on double-pole"
is a DIFFERENT regime — tiny evolutionary budget, seeded-untuned CfC, discovering
weights by mutation rather than gradient descent — not a contradiction, and worth
stating as such so we don't look like we're refuting them.

## T-maze memory (019) — a standard working-memory paradigm

The cue-then-delay-then-decision T-maze is a standard neuroevolution / RNN memory
benchmark. Ziemke & Thieme, "Evolving internal memory for T-maze tasks in noisy
environments" (*Connection Science*, 2002); more recently GECCO 2021,
"Neuroevolution of a Recurrent Neural Network for Spatial and Working Memory".
The literature's description — "a corridor with sensory cues, a delay interval
between the cues and the decision period, reward if the chosen direction matches
the cue" — is our tmaze_sim exactly. So 019 is a faithful re-implementation of a
known paradigm; the result (memory pays here, reactive caps at chance) is the
expected, established outcome.

## Delay-line / feedforward memory (019/020) — reservoir computing & TDNN

The richest match, and the correct frame for 020:

- **Time-Delay Neural Networks** (Waibel, Hanazawa, Hinton, Shikano, Lang 1989):
  feedforward nets given memory by tapped input delays.
- **Reservoir computing / Echo State Networks** (Jaeger 2001; Maass Liquid State
  Machines 2002) and **memory capacity** (Jaeger 2002; fading memory, Boyd & Chua
  1985): a reservoir's linear short-term **memory capacity is BOUNDED by its
  dimension**, and past-input influence **fades** over time. This is precisely the
  "bounded horizon" intuition behind 019's delay-line hypothesis.
- Directly on point: "Exploiting Signal Propagation Delays to Match Task Memory
  Requirements in Reservoir Computing" (2024) — using inter-neuron propagation
  delays as the memory mechanism. That is our emergent "FIFO buffering across
  cycles" idea, already a named technique in the field.

Caveat this literature forces on us: MC bounds are for reconstructing the FULL
input history. Our T-maze needs only ONE bit held across the delay, which a small
nonlinear structure (a latch/attractor, or a short delay line) can do without
full MC. That is exactly why 020's delay sweep did not show a clean dimensional
bound, and why EXP_024 (topology inspection of a solved feedforward agent) is the
right next step: distinguish a depth-D delay line from a compact latch. Whichever
it is, it is a known mechanism from this literature, not a new one.

## The one thing that is at most a small original note

An actor-model (Erlang process-per-neuron, async message-passing) neuroevolution
substrate spontaneously produces reservoir-style propagation-delay memory in
nominally feedforward networks, for free, as a side effect of the FIFO input
accumulator. The MECHANISM is old (propagation-delay memory, above); the specific
observation that this substrate hands it to you unbidden is a substrate-quirk
note, publishable at most as a short experience report — not a scientific result.

## Standing rule for the corpus

Every insight that states a principle must, before it hardens into an external
claim, carry a one-line "Prior art:" pointer to this document. We report our
findings as *independent, reproducible confirmation on the DXNN2/Erlang
substrate*, never as discovery. Independent replication of established results is
genuinely valuable — it validates the instrument — and that is the honest and
sufficient framing.

## Sources

- Wolpert & Macready 1997, "No Free Lunch Theorems for Optimization."
- Stanley & Miikkulainen 2002, "Evolving Neural Networks through Augmenting Topologies," *Evolutionary Computation* 10(2). https://nn.cs.utexas.edu/downloads/papers/stanley.ec02.pdf
- "Memetic Algorithms: Parametrization and Balancing Local and Global Search," arXiv 1109.6441.
- Gomez & Miikkulainen 1999, "Solving Non-Markovian Control Tasks with Neuroevolution," IJCAI-99. https://www.ijcai.org/Proceedings/99-2/Papers/097.pdf
- Hasani et al. 2021 (LTC, AAAI). https://ojs.aaai.org/index.php/AAAI/article/view/16936 ; Hasani et al. 2022 (CfC, Nature MI), arXiv 2106.13898. https://arxiv.org/abs/2106.13898
- Ziemke & Thieme 2002, "Evolving internal memory for T-maze tasks in noisy environments," *Connection Science*. https://www.tandfonline.com/doi/pdf/10.1080/09540090412331314812
- "Neuroevolution of a Recurrent Neural Network for Spatial and Working Memory," GECCO 2021. https://arxiv.org/abs/2102.12638
- "Exploiting Signal Propagation Delays to Match Task Memory Requirements in Reservoir Computing," 2024. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11201534/
- Waibel et al. 1989 (TDNN); Jaeger 2001 (ESN) / 2002 (memory capacity); Maass 2002 (LSM).
