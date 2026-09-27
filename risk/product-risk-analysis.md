# Product Risk Analysis

## Method

Rate each risk from 1–5 for business impact and failure likelihood.

```text
Risk score = Impact × Likelihood
```

The number starts a conversation; it does not make the decision automatically.

## Example analysis

| Feature / risk | Impact | Likelihood | Score | Primary controls |
|---|---:|---:|---:|---|
| Payment succeeds but order missing | 5 | 3 | 15 | Integration, reconciliation, observability |
| Duplicate order on retry | 5 | 3 | 15 | Idempotency tests, concurrency checks |
| User sees another user's order | 5 | 2 | 10 | Authorization tests |
| Checkout latency under peak | 4 | 3 | 12 | Load tests, SLO thresholds |
| Wrong basket total | 5 | 2 | 10 | Component/API permutations |
| Search typo tolerance issue | 2 | 3 | 6 | Functional/exploratory |
| Minor layout issue | 1 | 3 | 3 | Targeted UI check |

## Review triggers

Reassess risks when:

- architecture changes
- a production incident occurs
- a critical dependency changes
- traffic or usage patterns change
- a new market/regulatory requirement appears
- a previously stable test area becomes failure-prone
