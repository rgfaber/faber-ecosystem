# 002 — Silent fallbacks hide correctness divergence, not just performance

**Verdict:** confirmed · **Date:** 2026-07-20

## Claim

A silent fallback between two implementations of the same interface is not a
performance concern. It is a correctness hazard, because the unused path stops
being exercised and the two implementations drift apart undetected.

## Evidence

faber-tweann dispatched between Rust NIFs (`faber_nn_nifs`) and a pure Erlang
fallback (`tweann_nif_fallback`) via `code:which/1` auto-detection. faber-tweann
never declared faber-nn-nifs as a build dependency, so **every hex install
silently ran the fallback** and the native path was never exercised by its own
test suite.

Absorbing the package into faber-tweann made the NIF path run for the first
time. Test count went 869 → 964 with **5 immediate failures**, exposing four
contract divergences:

| Function | Rust computed | Erlang computed |
|---|---|---|
| `weight_distance_l1/2` | sum ÷ length (mean absolute deviation) | sum (Manhattan) |
| `weight_distance_l2/2` | sqrt(sum) ÷ sqrt(length) (RMS deviation) | sqrt(sum) (Euclidean) |
| `weight_distance_batch/3` | boolean arg, sorted `{Index, Distance}` pairs | atom `l1`/`l2`, plain list in input order |
| `random_weights_batch/1` | bare size list, uniform weights | `{Count, Mean, StdDev}`, gaussian |

Two of the four return **wrong numbers rather than crashing**.
`weight_distance_l1` claimed Manhattan distance and computed something else,
disagreeing with its counterpart by a factor of the vector length.

The structural cause: each repository tested only its own side. The nn-nifs
test even documented the wrong behaviour as correct, with the comment
`%% L1 = |1-0| + |1-0| + |1-0| = 3, normalized by length = 1.0`. Both suites
were green while disagreeing.

## What changes

- Silent fallback removed. Implementation is selected explicitly via
  `{faber_tweann, [{nif_impl, nif | fallback}]}`, defaulting to `nif`, and a
  missing library raises with an explanation instead of degrading.
- `tweann_nif:impl/0` added so any benchmark can name its execution path. A
  performance number that does not name its path is not a number.
- `test/unit/nif_fallback_conformance_tests.erl` runs both implementations over
  the same inputs and asserts agreement. **Add a case there for every function
  that has two implementations.**

## Generalisation

This is the same shape as the earlier macula incident where a silent crypto
fallback produced `invalid_private_key` in CI. Second appearance of the class.
Wherever two implementations of one interface exist, a differential test is not
optional.
