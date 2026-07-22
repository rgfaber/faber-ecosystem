# 040 — The interference hypothesis for per-connection's noise robustness is REFUTED: per-connection solvers have the HIGHEST readout cross-talk (0.82), not the lowest, while global ABCD has the LOWEST (0.26) — the opposite of the prediction. Cross-talk (magnitude leak between the two bits) does not track noise robustness at all, because decisions are SIGN-based and the sign survives large magnitude leak. And the probe was on the wrong population (sigma=0 solvers, not noise-evolved ones): CfC's noiseless solvers even saturate at +/-1 margins yet are the LEAST noise-robust (039). Mechanism stays OPEN; the advantage is likely EVOLVABILITY of the noise-robust region (cf 020), not a static representational property.

**Verdict:** signed negative (hypothesis refuted) + method correction · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-040. Instrumented replay (no evolution at
probe time), like the 035 probe. `multi_cue_tmaze_sim`, `sep_cma_es`. n=6 evolved / arm.

## The hypothesis (refuted)

039 found per-connection is the only mechanism that survives capacity+noise. Proposed
mechanism: a single global rule stores both cues in OVERLAPPING synapses, so the two
bits interfere; per-connection dedicates DISJOINT synapse groups, keeping them
independent. Prediction: cross-talk (how much flipping cue B perturbs the junction-1
readout of cue A) is HIGH for global ABCD, LOW for per-connection.

## Method

Evolve solvers at sigma=0 (n=6/arm), then drive the four (A,B) combos by hand and
read the junction outputs. Cross-talk_A = mean |m1(A,+B) - m1(A,-B)| over A,
normalised by mean |m1|; cross-talk_B symmetric on junction 2. Arms cfc_reset / abcd
/ pc. Raw: `040-raw-crosstalk-probe.txt`.

## Result — the prediction is inverted

| arm | mean cross-talk | solvers (of 6) | noise robustness (039) |
|---|---|---|---|
| abcd (global) | **0.26** (lowest) | 4 | 1/10 (fragile) |
| cfc_reset | 0.34 | 6 | 0/10 (fragile) |
| pc (per-connection) | **0.82** (highest) | 3 | **8/10 (robust)** |

Per-connection has the HIGHEST cross-talk and is the MOST robust; global ABCD the
LOWEST and among the least robust. The interference story predicted the exact
opposite. **Refuted.**

## Why cross-talk is the wrong quantity — decisions are SIGN-based

The pc solvers keep the decision SIGN correct while the magnitude swings wildly. Run 1:
junction-1 margin for A=+ is 0.93 at B=+ but only 0.20 at B=-, and for A=- it is -0.96
at B=- but -0.15 at B=+. Huge magnitude leak (cross-talk 1.38) yet every sign is
correct, so every decision is right. Cross-talk measures MAGNITUDE interference; the
task scores SIGN. A high-cross-talk readout with preserved signs solves perfectly.
The relevant robustness quantity is the DECISION MARGIN (distance from the sign
boundary), not the magnitude leak.

## Why even the decision margin here is uninformative — wrong population

The deeper flaw: these are sigma=0 solvers, evolved with NO pressure to build
noise-tolerant margins. Their margin structure does not reflect 039's noise-robust
population. Worse, it points the WRONG way: CfC's noiseless solvers saturate at +/-1
margins (run 1 and run 3: every |margin| = 1.0, the maximum possible headroom) yet
CfC is the LEAST noise-robust arm in 039 (0/10). If big noiseless margins meant
robustness, CfC would win; it loses. So a static probe of sigma=0 solutions cannot
explain the noise result. And the natural fix (probe noise-EVOLVED solvers) is
blocked: at sigma=0.3 only per-connection solves (039), so abcd/cfc have no
noise-evolved solvers to compare.

## What this reframes — evolvability, not a static property (cf 020)

Because only per-connection can FIND a noise-robust two-bit solution, the advantage
is most likely about the SEARCH, not the final weights: per-connection's parameter
richness makes the noise-robust region of solution space reachable, where global ABCD
and storage cannot get there at all. This echoes 020 (recurrent memory's advantage
was EVOLVABILITY — found in gen 2-4 vs 14-23 — not a bounded-horizon property). A
static representational probe is the wrong instrument for an evolvability claim.

## Caveat / next

- **n=3-6 solvers/arm, high within-arm variance** (cfc cross-talk ranges 0.00-0.94).
  The MEANS invert the prediction, which is enough to refute (pc predicted lowest, is
  highest); the exact values are noisy.
- **EXP-041 (the corrected probe):** instrument the SEARCH, not the solution. E.g. for
  each arm under sigma=0.3, track the best-fitness trajectory and the DECISION-margin
  distribution of the population over generations — does per-connection reach large
  sign-margins that global ABCD's search never populates? An evolvability metric
  (generations-to-first-robust-margin, or fraction of the population above a margin
  threshold) is the right lens.
- Not dashboarded: 040 refutes a mechanism, it adds no positive data panel. The
  031-039 dashboard stands.
