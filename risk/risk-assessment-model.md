# Risk Assessment Model

This document explains the scoring system used in the ShopSphere case study. There are three decisions which are often confused:

1. **How bad could the failure be?** - Impact
2. **How probable is the failure assuming the stated assumptions?** - Likelihood
3. **What is the appropriate treatment and release evidence are appropriate?** - Risk treatment

The model has been kept simple for explanation and criticism purposes. It supports engineering judgement rather than replaces it.

## 1. Base formula

Each product risk has a base score:

```text
Risk score = Impact × Likelihood
```

Both elements are scored from 1 to 5, so the result is in the range 1 to 25.

The score reflects the **baseline product risk before considering the effectiveness of the chosen test and operational controls**. It is not a probabilistic measurement and must not be used as such.

## 2. Impact scale

Impact defines a credible consequence in case of the failure reaching a customer or production flow.

| Rating | Label | Definition | ShopSphere-style descriptors |
|---:|---|---|---|
| **1** | Negligible | Cosmetic or very limited inconveniences; no transaction, security, availability or data integrity impact. | Minor presentation problem that does not affect customer purchasing. |
| **2** | Minor | Limited  functionality impairment with a practical workaround; small impact on customer support or customer satisfaction. | Delayed confirmation, but the order can still be retrieved. |
| **3** | Moderate | Significant functionality impairment or partial journey; may require customer support intervention. | Search results are incomplete or wrong for typical criteria. |
| **4** | Major | Essential journey or important data becomes unavailable or inconsistent for the impacted customers; significant conversion, operations or customer satisfaction impact. | Customer basket becomes lost prior to checkout or the order becomes invisible after it has been created. |
| **5** | Severe | Financial inconsistency, data leakage, double charging/double order creation or inability to perform an essential revenue-generating transaction. | Payment was successful, but no persistent order is created; customer is able to access other customer's orders. |

### Impact scoring rule

The score should consider a **credible business consequence**, rather than a technical complexity of the change. For example, a one-line authorization bug may cause Impact 5, but even a major refactoring can have minor impact if its failure modes are well-contained.

## 3. Likelihood scale

Likelihood defines the probability of the failure under the current design, traffic assumptions, dependency behaviour, change profile and preventive controls.

Given the fictional nature, ShopSphere rating should be considered a **scenario-based estimate**, rather than a conclusion from real-life production incidents statistics.

| Rating | Label | Definition | Typical evidence/assumption |
|---:|---|---|---|
| **1** | Rare | Requires an exceptional set of circumstances and is heavily constrained by the design | Edge path with mature preventive controls and limited concurrency/dependency exposure |
| **2** | Unlikely | Possible, but requires a less common circumstance or a particular fault | Cross-account access bug, double event delivery or stale read under particular circumstances |
| **3** | Possible | Normal operations can uncover the failure and the path shows significant state/dependency complexity | Timeouts, retries, peak traffic, sessions transition, cross-service consistency |
| **4** | Likely |  Common triggering circumstances or highly dynamic/weakly controlled domain | Frequent changes in the integration contract with limited preventive checks |
| **5** | Very likely | The failure is expected to repeat without immediate corrective action | Known unstable behaviour or frequently reproducible defect pattern |

### Likelihood inputs

If real operational data is available, it should be used. Otherwise, the following considerations should be taken into account:

- Frequency of the triggering user/system circumstance
- Quantity and reliability of external/internal dependencies
- Concurrency and retry behavior
- State transitions complexity
- Change frequency in the affected area
- Maturity of preventive controls
- Incident/defect history, if available

Do not raise likelihood merely because a failure would be severe; impact and likelihood are deliberately separate dimensions.

## 4. Risk bands

| Score | Band | Default interpretation |
|---:|---|---|
| **16–25** | Critical | Expect the immediate risk treatment and good release evidence |
| **10–15** | High | Expect robust evidence; relevance of release should be checked explicitly |
| **5–9** | Medium | The evidence should be targeted according to the change scope and failure modes |
| **1–4** | Low | Generally enough to base the release evidence on a selective, change-specific approach |

The bands are used for prioritization and do not determine release decisions themselves. 

## 5. Default risk treatment by band

| Band | Test/evidence expectation | Release treatment |
|---|---|---|
| **Critical** | Multi-layer automated checks if possible; negative/resilience coverage; exploratory focus; observability/recovery evidence | Generally blocking the release until evidence is satisfactory or the residual risk is explicitly accepted by an accountable person |
| **High** | Robust coverage at the lowest layer possible; end-to-end/integration testing only if the boundary or customer journey requires it | Evaluate explicitly whether the risk is release-critical for the current change; unresolved failures need documented disposition |
| **Medium** | Appropriate targeting of automation and/or exploratory testing appropriate for the change | Usually depends on change scope; may become release-critical if the area modified by the change is directly exposed to the risk |
| **Low** | Selective evidence; avoid unnecessary expensive regression to satisfy scoring metric | Generally notblocking, but can be blocking if there is an explicit commitment or release goal related to it |

## 6. Score is not equal to release criticality

Risk may be rated High without becoming a release-blocking gate for each release. On the contrary, Medium risk can become release-critical if the release modifies the affected component or customer commitment.

Thus, release criticality includes:

- the baseline risk score or band
- change of the affected capability by the release
- critical procurement or security path of the risk
- evidence freshness and relevance to the relevant environment
- unresolved defects or known control weaknesses
- explicit contractual, security, regulatory, or business commitments

This explains why  **Release critical?** is recorded separately from the risk band in `risk-to-test-traceability.md`.

## 7. Overrides and escalation

The numeric model may be overridden if it understates or overstated the decision context.

Examples of the situation when it might happen are:

- security or privacy obligations
- contractual service level agreement
- recent production incident
- unproven architectural change
- degraded dependency
- strong compensating control that materially reduces exposure

Any override should record:

- original score or band
- new treatment (not necessarily a new numeric score)
- rationale
- accountable owner
- review or expiry condition

Prefer **treatment overrides** over manipulation of the score just to get a particular band.

## 8. Residual risk

After applying controls and gathering evidence, the residual risk can still be present. This repository does not compute the second pseudo-precise residual score.

The release record should contain:

- remaining baseline risks
- evidence that passed orfailed
- known defects or gaps
- available mitigation or workaround
- people who took over the remaining business risk

This makes the reasoning about releases auditable without implying test suite mathematically removes all uncertainty.

## 9. Re-assessment triggers

Risk should be re-assessed when any of the following factors changes significantly:

- architecture or service boundaries
- behavior of external providers
- traffic or capacity assumptions
- incident or defect history
- security or authorization model
- criticality of the capability for business 
- maturity of preventive controls
- change frequency in the affected domain

Applied ShopSphere assessment is described in the file [`product-risk-analysis.md`](product-risk-analysis.md).
