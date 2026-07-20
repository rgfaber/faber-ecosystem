# 020 — The delay sweep refutes the "bounded horizon" story. The real axis is evolvability: recurrent memory is found in 2-4 generations, delay-line memory in 14-23.

**Verdict:** signed negative (hypothesis refuted) + one robust finding (evolvability) · **Date:** 2026-07-21
**Experiment:** EXP_022 (T-maze corridor-delay sweep 2/8/16)
**Uses:** the tmaze scape and the four-variant framing of 019. Corrects 019's
"delay-line / TDNN" label.

> **Corrected by insight 023:** this insight's retraction of the delay-line label
> was premature. It rested on n=2 noise (feedforward solved delay 16 by luck).
> Isolating the real solver (023) shows its wiring IS a linear chain (a short
> delay line), vs a convergent DAG for XOR. The delay-line mechanism is confirmed.
> The durable finding below — evolvability differs (recurrent gen 2-4 vs
> feedforward 14-23) — still stands.

## The hypothesis (from 019) and why it is wrong

019 proposed that "feedforward" solves the T-maze via emergent DELAY-LINE memory
(FIFO buffering), which is BOUNDED by its evolvable depth, so raising the corridor
delay should make the feedforward arm fall off while explicit memory (cfc,
recurrent) stays flat. Prediction: feedforward degrades monotonically with delay.

It does not. Feedforward solved delay 16 BEST.

## Data (pop 20, max_gen 25, 2 runs per cell)

| Delay | feedforward | cfc | recurrent |
|---|---|---|---|
| 2 | 2/2, solve gen 15-17 | 2/2, gen 1-3 | 2/2, gen 2-3 |
| 8 | 1/2, gen 14 | 0/2 (cap) | 2/2, gen 2-4 |
| 16 | 2/2, gen 16-23 | 1/2, gen 25 | 1/2, gen 3 |

Raw: `020-raw-tmaze-delay-sweep.txt`. Zero input-timeouts.

Solve rates are non-monotonic for every variant (2/2 -> 1/2 -> 2/2 etc.). With
n=2 that is noise, not signal — the first and most important lesson.

## What the sweep actually shows

### Refuted: no bounded horizon at these delays

Every variant solved delay 16 at least once, feedforward at a perfect 2/2. There
is no clean delay ceiling in 2..16 for any of them. The tidy "each variant has
its own memory horizon that the sweep reveals" story from 019 is not supported.

### Robust: evolvability, not capacity, is the differentiator

The generation-to-solve is consistent and sharply separated, unlike the noisy
solve rates:

- **recurrent (wiring): gen 2-4**, across ALL delays. Fastest and most reliable.
- **cfc (neuron): gen 1-3 at short delay**, but unreliable and slow as delay
  grows (gen 25 when it solved at delay 16; failed at delay 8). Its time constant
  must co-evolve to hold longer, and 25 generations is often too few.
- **feedforward: gen 14-23**, across all delays. It CAN reach long delays but is
  5-10x SLOWER to evolve than recurrent.

So the mechanisms differ mainly in HOW EASILY EVOLUTION FINDS THE MEMORY, not in
how far it reaches. Recurrent memory is by far the most evolvable — a single
feedback edge holding a value is a short mutational path; whatever the feedforward
mechanism is, evolution takes an order of magnitude longer to assemble it.

## Correction to insight 019

019 labelled the feedforward memory "delay-line / TDNN" and called it bounded.
EXP_022 does not support that: a bounded delay line should fail at delay 16, and
it did not (a 16-stage tapped delay is also implausible to evolve in ~16
generations). The honest status: feedforward networks in this substrate DO
acquire real memory (confirmed again here — they solve a task a minimal reactive
net caps at 50 on), but the MECHANISM is uncharacterised. It is not established
to be a simple delay line. Determining what it is needs topology inspection of a
solved feedforward agent, not just black-box sweeps.

## Method lesson (the loudest one)

n=2 per cell is far too few: run-to-run variance (0/2, 1/2, 2/2) swamps every
trend except the gen-to-solve separation. Any solve-RATE or horizon claim needs
n>=10 per cell. A proper version of this experiment is 3 variants x ~5 delays x
>=10 runs x more generations = hundreds of long runs. That is the concrete case
for moving high-throughput experiments to msi00.lab (8 cores / 31 GiB, vs the dev
box) — see the programme's high-budget track.

## For the variant map

- Recurrent memory: most evolvable (gen 2-4), robust. The default recommendation
  when a task needs memory.
- CfC memory: fast to a point, but its horizon is gated by an evolvable time
  constant that may not co-evolve in a small budget.
- Feedforward "memory": real but uncharacterised; slow to evolve; do not yet
  label it a delay line.

## Next

- **Topology inspection** of a solved feedforward T-maze agent — the only way to
  learn what the feedforward memory mechanism actually is.
- **High-n delay sweep on msi00.lab** (n>=10, more generations, delays to 32) to
  turn the noisy solve rates into real curves.
