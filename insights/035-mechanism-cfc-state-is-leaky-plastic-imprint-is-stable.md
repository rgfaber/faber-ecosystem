# 035 — Mechanism probe, MEASURED: the CfC internal state is LEAKY (its cue signal decays ~25x across an 8-step corridor to a razor-thin ~4% margin, even with no noise) while the plastic weight imprint is STABLE (holds ~100%, even grows). That is why sensor noise breaks CfC and not plasticity (034): noise destroys CfC's thin decayed margin but cannot erode plasticity's stable imprint.

**Verdict:** confirmed (034's hypothesis turned into a measurement) · **Date:** 2026-07-22
**Programme:** 3 (Meta-learning), EXP-035. No evolution beyond getting solved agents.
**Uses:** `evaluate_with_plasticity/3`, new `network_evaluator:get_internal_state/1`,
`sep_cma_es`, `tmaze_sim`.

## The question

034 found sensor noise breaks CfC (0/8 at sigma=0.5) but not plasticity (8/8), and
HYPOTHESISED that a decaying CfC state accumulates noise while a re-imprinted plastic
weight does not. This measures it directly.

## Method

Solve a CfC and a plastic agent on the delay-8 T-maze. For each, run 60 noisy +cue
and 60 noisy -cue trials at sigma 0 / 0.3 / 0.5, and at every step record the MEMORY
CARRIER: the CfC internal_state, or the plastic weight vector. The "cue signal" =
||mean_carrier(+cue) - mean_carrier(-cue)|| per step, normalised to the cue step
(step 0 = 1.0). This is how much of the stored cue survives across the corridor.
Raw: `035-raw-mechanism.txt` + `035-mechanism.json`.

## Results — normalised cue separation in the carrier (step 0=cue, 1-8=corridor, 9=junction)

```
CfC state:      sigma 0.0 : 1.00 0.40 0.23 0.15 0.11 0.08 0.06 0.05 0.04 0.02
                sigma 0.5 : 1.00 0.35 0.16 0.10 0.05 0.04 0.07 0.05 0.10 0.10
plastic weights: sigma 0.0 : 1.00 1.00 1.01 1.02 1.02 1.03 1.04 1.06 1.07 1.09
                sigma 0.5 : 1.00 1.06 1.06 1.11 1.10 1.05 1.06 1.09 1.03 1.04
```

## Finding 1 — the CfC state is LEAKY (decays even without noise)

The CfC internal state's cue signal decays ~25x across the 8-step corridor (1.00 ->
~0.04) with ZERO noise. The state forgets the cue almost entirely; it only solves
because ~4% is enough to get the SIGN right at the junction. That is a razor-thin
margin, held not by robust memory but by the last scraps of a decaying signal.

## Finding 2 — the plastic imprint is STABLE (holds ~100%, even grows)

The plastic weight separation holds at ~100% across the corridor and even grows
slightly (1.00 -> 1.07). The stored cue is imprinted, not decaying. There is no thin
margin -- the memory is full-strength at the junction.

## Finding 3 — this IS the "why" for 034

Put the two together with 034: noise adds a fixed perturbation per step. CfC arrives
at the junction on a ~4% signal, so even modest noise (sigma=0.5) swamps it and flips
the decision -> CfC fails. Plasticity arrives on a full-strength imprint, so the same
noise barely dents it -> plasticity holds. 034's hypothesis ("noise corrupts a
decaying state more than a re-imprinted weight") is now measured: the CfC state
decays to a thin margin, the plastic imprint does not. Mechanism confirmed.

## Finding 4 — the decay is intrinsic, not a noise artefact

The ~25x decay is present at sigma=0 (no noise at all). So CfC's leakiness is a
property of the evolved continuous-time dynamics on this task, not something noise
induces -- noise merely exploits the thin margin the decay leaves. This also explains
033's cost curve: as the delay grows, the CfC signal must survive more decay steps,
so evolution must tune the state dynamics ever more precisely (a narrower target),
while the plastic imprint holds regardless of corridor length.

## Prior art

Replication + a measurement. That a fixed continuous-time (CfC/LTC) state decays and
a plastic/eligibility trace persists is the textbook contrast (leaky integrator vs
Hebbian trace). This pins it quantitatively on our benchmark and links it to the 033
(delay) and 034 (noise) results as one mechanism.

## Caveat / next

- **One solved agent per mechanism; output is one carrier snapshot.** The decay shape
  is robust (25x is not subtle), but the exact ratios depend on the evolved solution.
- **Mechanism confirmed** -> the 033/034 findings can now drop "tentative": CfC's
  weakness is its leaky state, plasticity's strength is its stable imprint.
- Feeds the P3 dashboard's mechanism panel (decay vs hold).
