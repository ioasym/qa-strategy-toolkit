# ADR-003 — Treat Flaky Automated Tests as Quality Defects

- **Status:** Accepted
- **Decision owners:** Quality Engineering + Owning engineering team
- **Related concerns:** release evidence, pipeline trust, diagnostics, automation maintainability

## Context

An automated test that sometimes passes and sometimes fails without a relevant product change creates ambiguous evidence. Frequent unexplained failures teach teams to ignore red pipelines and can hide genuine regressions.

Automatic retry can be useful as a diagnostic signal or when the product contract itself includes retryable behaviour, but retrying every failed test until it passes would convert uncertainty into a false sense of confidence.

## Decision

Flaky automated tests are treated as **defects in the quality system** and must be investigated, owned, and resolved.

A flaky test is not considered healthy simply because a retry passes.

When a suspected flaky failure occurs:

1. capture evidence such as trace, logs, screenshot, network diagnostics, inputs, build, and environment metadata
2. determine whether the failure is caused by the product, test implementation, test data, dependency, or environment
3. create visible ownership for unresolved recurring flakiness
4. quarantine only when necessary to protect the signal of a blocking pipeline
5. define a repair or removal action
6. require repeated stable execution before restoring a quarantined test to a blocking gate

## Retry policy

Retry may be used to collect evidence or model an explicitly retryable product contract. It must not be the only response to an unknown intermittent failure.

Where retry is enabled, reporting should preserve the initial failure so the flakiness remains visible.

## Alternatives considered

### A. Retry failed tests automatically and report only the final result

**Advantages**

- fewer red pipelines
- reduced immediate interruption

**Reasons not selected**

- hides intermittent regressions
- makes reliability trends invisible
- encourages teams to accept unstable tests

### B. Delete every flaky test immediately

**Advantages**

- removes noise quickly

**Reasons not selected**

- can remove useful risk coverage before a replacement exists
- avoids investigating whether the product itself is intermittent

## Consequences

### Positive

- higher trust in automated evidence
- visible reliability debt
- stronger root-cause discipline
- fewer false positives and ignored failures over time

### Trade-offs

- investigation consumes engineering time
- temporary quarantine can reduce coverage
- reliability metrics and ownership need maintenance

## Suggested reliability signals

Track at least:

- flaky executions / automated executions
- top recurring flaky tests
- mean time to resolution for quarantined tests
- number of blocking tests currently quarantined

A target such as "<1% flaky execution rate" can be useful only if the counting method is stable and the underlying failures remain visible.

## Exit from quarantine

A quarantined test returns to the blocking suite when:

- root cause is understood or the unstable dependency has been removed
- the fix has been reviewed
- repeated executions across representative conditions are stable
- failure artifacts remain available if recurrence occurs
