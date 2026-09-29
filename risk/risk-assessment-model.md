# Risk Assessment Model

This document defines the scoring model used throughout the ShopSphere case study. It separates three related decisions that are easy to confuse:

1. **How serious could the failure be?** — Impact
2. **How plausible is the failure under the stated assumptions?** — Likelihood
3. **What quality treatment and release evidence are appropriate?** — Risk treatment

The model is intentionally simple enough to explain and review. It supports engineering judgment; it does not replace it.

## 1. Baseline formula

Each product risk receives an initial score:

```text
Risk score = Impact × Likelihood
```

Both values use a 1–5 scale. The result ranges from 1 to 25.

The score represents **baseline product risk before considering the strength of the selected test/operational controls**. It is not a probability calculation and should not be presented as actuarial precision.

## 2. Impact scale

Impact describes the credible consequence if the failure reaches a customer or production workflow.

| Rating | Label | Definition | ShopSphere-style example |
|---:|---|---|---|
| **1** | Negligible | Cosmetic or very limited inconvenience; no material transaction, security, availability, or data-integrity effect | Minor presentation defect with no effect on purchase behaviour |
| **2** | Minor | Limited feature degradation with a practical workaround; small support/customer-trust effect | Delayed confirmation while the valid order remains retrievable |
| **3** | Moderate | Material feature degradation or incomplete behaviour affecting a meaningful subset of users; may require support intervention | Search results are incomplete or incorrect for some common criteria |
| **4** | Major | Core journey or important data is unavailable/inconsistent for affected users; material conversion, operational, or customer-trust impact | Basket state is lost before checkout or a created order is temporarily not visible |
| **5** | Severe | Financial inconsistency, unauthorised data exposure, duplicate charging/order creation, or broad inability to complete a revenue-critical transaction | Payment succeeds but no durable order is created; customer accesses another customer's protected order data |

### Impact scoring rule

Score the **credible business consequence**, not the technical size of the code change. A one-line authorization bug can have Impact 5; a large internal refactor can have low impact if its failure modes are well contained.

## 3. Likelihood scale

Likelihood describes how plausible the failure is under the current design, traffic assumptions, dependency behaviour, change profile, and preventive controls.

Because ShopSphere is fictional, these ratings are **scenario-based estimates**, not claims based on real production incident statistics.

| Rating | Label | Definition | Typical evidence/assumption |
|---:|---|---|---|
| **1** | Rare | Requires an exceptional combination of conditions and is strongly constrained by the design | Narrow edge path with mature preventive controls and little concurrency/dependency exposure |
| **2** | Unlikely | Plausible but requires a less common condition or a specific fault | Cross-account access bug, duplicate event delivery, or stale read under limited conditions |
| **3** | Possible | Normal operating conditions can expose the failure and the path has meaningful state/dependency complexity | Timeouts, retries, peak load, session transitions, cross-service consistency |
| **4** | Likely | Triggering conditions are frequent or the area is highly change-prone/weakly controlled | Repeatedly changing integration contract with limited preventive checks |
| **5** | Very likely | Failure is expected to recur without immediate mitigation | Known unstable behaviour or a consistently reproducible defect pattern |

### Likelihood inputs

When real operational data exists, use it. Otherwise consider:

- frequency of the triggering user/system condition
- number and reliability of external/internal dependencies
- concurrency and retry behaviour
- complexity of state transitions
- frequency of change in the affected area
- maturity of preventive controls
- incident/defect history, when available

Do not raise likelihood merely because a failure would be severe; impact and likelihood are deliberately separate dimensions.

## 4. Risk bands

| Score | Band | Default interpretation |
|---:|---|---|
| **16–25** | Critical | Immediate, explicit risk treatment and strong release evidence expected |
| **10–15** | High | Strong automated/multi-layer evidence expected; release relevance reviewed explicitly |
| **5–9** | Medium | Targeted evidence based on change scope and failure mode |
| **1–4** | Low | Selective/change-focused evidence is normally sufficient |

The bands are prioritisation aids. They are not release decisions by themselves.

## 5. Default treatment by band

| Band | Test/evidence expectation | Release treatment |
|---|---|---|
| **Critical** | Multi-layer automated checks where feasible; negative/resilience coverage; exploratory focus; observability/recovery evidence | Normally release-blocking until evidence is satisfactory or residual risk is explicitly accepted by an accountable stakeholder |
| **High** | Strong coverage at the lowest useful layer; integration/E2E only where the boundary or customer journey requires it | Explicitly assess whether the risk is release-critical for the current change; unresolved failures require documented disposition |
| **Medium** | Targeted automation and/or exploratory testing appropriate to the change | Usually conditional on change scope; may become release-critical when the changed area directly exposes the risk |
| **Low** | Selective verification; avoid expensive regression merely to satisfy a score | Normally non-blocking unless a specific commitment or release objective makes it relevant |

## 6. Score is not the same as release criticality

A risk can be High without being a blocking gate for every release. Conversely, a Medium risk can be release-critical when a release directly changes the affected component or customer commitment.

Release criticality therefore considers:

- baseline risk score/band
- whether the release changes the affected capability
- whether the risk is on the critical purchase/security path
- evidence freshness and environment relevance
- unresolved defects or known control gaps
- explicit contractual, security, regulatory, or business commitments

This is why `risk-to-test-traceability.md` records **Release critical?** separately from the risk band.

## 7. Overrides and escalation

The numerical model may be overridden when it would understate or overstate the decision context.

Examples include:

- security or privacy obligations
- contractual service thresholds
- a recent production incident
- an unproven architecture change
- a temporarily degraded dependency
- a strong compensating control that materially reduces exposure

Any override should record:

- original score/band
- adjusted treatment (not necessarily a new fabricated number)
- rationale
- accountable owner
- review/expiry condition

Prefer **treatment overrides** over manipulating the score solely to obtain a desired band.

## 8. Residual risk

After controls are implemented and evidence is collected, the team may still have residual risk. This repository does not calculate a second pseudo-precise residual score. Instead, the release record states:

- which baseline risks remain relevant
- what evidence passed/failed
- known defects or gaps
- available mitigation/workaround
- who accepted any remaining business risk

This keeps release reasoning auditable without pretending the test suite mathematically eliminates uncertainty.

## 9. Review triggers

Reassess a risk when any of the following changes materially:

- architecture or service boundaries
- external provider behaviour
- traffic/capacity assumptions
- incident or defect history
- security/authorization model
- business criticality of the capability
- preventive control maturity
- frequency of change in the affected area

The applied ShopSphere assessment is documented in [`product-risk-analysis.md`](product-risk-analysis.md).
