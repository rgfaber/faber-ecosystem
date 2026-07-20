# Faber Insights

Numbered, signed findings from the faber neuroevolution programme.

Recorded **as we go**, not reconstructed afterwards. An insight is written when
something is *learned*, not when something is merely done: a bug fixed is a
commit, a bug fixed that reveals a class of defect is an insight.

## Rules

- **Evidence or it didn't happen.** Cite `file:line` or a measurement.
- **Negatives are first-class.** A falsified expectation is as valuable as a
  confirmed one, often more.
- **Never overclaim.** If a result is single-run, unseeded, or unmeasured, the
  insight says so in its own text.
- **Monotonic numbering.** Never renumber, never delete. Supersede by writing a
  new insight that references the old.

## Insights

| # | Title | Verdict | Date |
|---|---|---|---|
| [001](001-faber-is-not-a-dxnn2-port.md) | faber-tweann is not a DXNN2 port | confirmed | 2026-07-20 |
| [002](002-silent-fallbacks-hide-correctness-not-just-speed.md) | Silent fallbacks hide correctness divergence, not just performance | confirmed | 2026-07-20 |
| [003](003-an-inverted-objective-looks-like-working-evolution.md) | An inverted objective is indistinguishable from working evolution without a control | confirmed | 2026-07-20 |
| [004](004-domain-sdk-path-is-not-tweann.md) | The domain_sdk path evolves weights only, not topology | confirmed | 2026-07-20 |
| [005](005-seeding-the-server-process-suffices-for-reproducibility.md) | Seeding one process makes an evolutionary run replayable | confirmed | 2026-07-20 |
| [006](006-evaluations-to-solve-is-now-measurable.md) | Evaluations-to-solve is measurable; first number is inflated 10x | confirmed | 2026-07-20 |
| [007](007-deterministic-environments-declare-themselves.md) | Deterministic environments declare themselves; corrected baseline is 550 | confirmed | 2026-07-20 |
| [008](008-the-scape-and-fitness-channel.md) | Restoring the scape: the fitness channel (Phase 3, part 1) | closed by 009 | 2026-07-20 |
| [009](009-xor-solved-through-shers-path.md) | Evaluation completes through DXNN2's path; K1 passes structurally | confirmed | 2026-07-20 |
| [010](010-population-loop-works-multigeneration-blocked.md) | Population loop drives Sher-path evaluation; multi-generation evolution blocked | confirmed | 2026-07-20 |
| [011](011-lifecycle-fixed-evolution-improves-topology-search-is-the-wall.md) | Lifecycle + tuning fixed; evolution improves; topology search is the next wall | confirmed | 2026-07-20 |
| [012](012-xor-solved-tuning-attempts-was-the-lever.md) | Evolution SOLVES XOR through DXNN2's path; tuning depth was the lever | confirmed | 2026-07-20 |
| [013](013-topology-evolution-costs-37x-on-a-known-topology-problem.md) | On XOR, topology evolution costs ~37x weight-only (as expected); our tuner is also crude | confirmed | 2026-07-20 |
| [014](014-shallow-tuning-fails-xor.md) | DXNN2's shallow default tuner does NOT solve XOR at a matched budget; the crude tuner's depth was not waste | confirmed | 2026-07-20 |
| [015](015-tuner-fitness-depends-on-problem-structure.md) | The right tuner depends on problem structure: shallow wins control (pole), deep wins precise-weight (XOR); XOR was out of domain | confirmed | 2026-07-20 |
| [016](016-recurrence-works-but-single-pole-is-a-weak-memory-test.md) | Recurrent evaluation works (validated); but single-pole-no-velocity is too weak to prove recurrence beats feedforward — real test is double pole | confirmed + inconclusive | 2026-07-20 |

## Related

- [`plans/PLAN_FABER_FOUNDATION.md`](../plans/PLAN_FABER_FOUNDATION.md) — the work programme
- [`docs/CONFORMANCE.md`](../docs/CONFORMANCE.md) — DXNN2 module gap analysis
- [`docs/PROTOCOL.md`](../docs/PROTOCOL.md) — message protocol comparison
- [`docs/CLAIMS_AUDIT.md`](../docs/CLAIMS_AUDIT.md) — documentation claims audit
