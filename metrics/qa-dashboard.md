# QA Dashboard — Example

A dashboard should present a small set of actionable signals.

## Release candidate RC-24.9

| Signal | Current | Threshold / expectation | Status |
|---|---:|---:|---|
| Critical-path pass rate | 100% | 100% | Green |
| Open Critical defects | 0 | 0 | Green |
| Open High defects | 2 | Reviewed | Amber |
| Flaky test rate | 0.8% | < 1% | Green |
| Checkout p95 latency | 420 ms | < 500 ms | Green |
| Known untested High risks | 0 | 0 | Green |

## Commentary

The status colour is not the decision itself. Release notes should explain the two open High defects, their customer impact, workaround, and any accepted residual risk.
