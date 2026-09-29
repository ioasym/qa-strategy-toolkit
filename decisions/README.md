# Quality Engineering Decision Records

This directory records important **Quality Engineering decisions and trade-offs** for the ShopSphere case study.

The files use an Architecture Decision Record (ADR) style because the decisions affect the design and operation of the test system, even when the subject is not application architecture itself.

## Why record quality decisions?

A test strategy describes the overall approach. A decision record captures **one consequential choice**, the context that led to it, the alternatives considered, and the consequences that follow.

This makes the reasoning reviewable and prevents practices such as "all tests must be E2E" or "retry every flaky test" from becoming undocumented defaults.

## Decision status

Each record uses one of these states:

- **Proposed** — under review; not yet adopted.
- **Accepted** — the current decision for the case study.
- **Superseded** — replaced by a newer decision record.
- **Deprecated** — no longer recommended, but retained for history.

## Current decisions

| ID | Decision | Status | Main concern |
|---|---|---|---|
| ADR-001 | Limit UI automation to critical cross-system journeys | Accepted | Feedback speed and maintainability |
| ADR-002 | Use API-first test-data setup and teardown | Accepted | Isolation, speed, and parallel execution |
| ADR-003 | Treat flaky automated tests as quality defects | Accepted | Trust in test evidence |
| ADR-004 | Use risk- and change-aware release quality gates | Accepted | Evidence-based release decisions |

## How these records relate to the toolkit

```text
Business capability / requirement
            ↓
          Risk
            ↓
      Test strategy
            ↓
  Quality decision record
            ↓
Implementation / evidence
            ↓
     Release decision
```

Decision records do not replace the strategy or risk register. They explain why specific engineering policies exist and what trade-offs they introduce.
