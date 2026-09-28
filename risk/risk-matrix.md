# Risk Matrix

The ShopSphere model uses a 5×5 matrix based on the definitions in [`risk-assessment-model.md`](risk-assessment-model.md).

| Likelihood \ Impact | **1** | **2** | **3** | **4** | **5** |
|---|---:|---:|---:|---:|---:|
| **5 — Very likely** | 5 M | 10 H | 15 H | 20 C | 25 C |
| **4 — Likely** | 4 L | 8 M | 12 H | 16 C | 20 C |
| **3 — Possible** | 3 L | 6 M | 9 M | 12 H | 15 H |
| **2 — Unlikely** | 2 L | 4 L | 6 M | 8 M | 10 H |
| **1 — Rare** | 1 L | 2 L | 3 L | 4 L | 5 M |

Legend:

- **C** — Critical (16–25)
- **H** — High (10–15)
- **M** — Medium (5–9)
- **L** — Low (1–4)

## Interpretation

| Band | Score | Default treatment |
|---|---:|---|
| **Critical** | 16–25 | Strong multi-layer controls and explicit release-risk disposition |
| **High** | 10–15 | Strong automated evidence; release relevance assessed explicitly |
| **Medium** | 5–9 | Targeted evidence based on change scope and risk exposure |
| **Low** | 1–4 | Selective/change-focused verification |

## Example placements

- `RISK-008` — payment succeeds but order missing: **5 × 3 = 15 (High)**
- `RISK-001` — unauthorised account/order access: **5 × 2 = 10 (High)**
- `RISK-004` — incorrect search/filter/sort: **3 × 3 = 9 (Medium)**
- `RISK-015` — delayed/missing confirmation: **2 × 3 = 6 (Medium)**

## Important limitation

The matrix prioritises **baseline product risk**. It does not decide by itself whether a particular release should be blocked. Release criticality is tracked separately using change scope, evidence, security/financial relevance, known defects, and explicit commitments.
