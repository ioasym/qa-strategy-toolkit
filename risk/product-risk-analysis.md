# Product Risk Analysis — ShopSphere

This file applies the scoring definitions in [`risk-assessment-model.md`](risk-assessment-model.md) to the 15 canonical ShopSphere product risks.

The values are **initial scenario-based assessments for the fictional case study**. They show how a quality engineer can make scoring rationale visible instead of presenting unexplained numbers.

## Applied assessment

| Risk | Capability | Impact | Impact rationale | Likelihood | Likelihood rationale | Score | Band |
|---|---|---:|---|---:|---|---:|---|
| **RISK-001** — unauthorised account/order access | CAP-01 | **5** | Protected customer data could be exposed to another user | **2** | Requires an authorization/control defect, but cross-account access is a credible failure mode | **10** | **High** |
| **RISK-002** — valid session cannot be established/maintained | CAP-01 | **4** | Legitimate customers can be blocked from account and purchase-related actions | **3** | Authentication/session transitions are common and involve expiry/state handling | **12** | **High** |
| **RISK-003** — stale/incorrect price or availability | CAP-02 | **4** | Incorrect commercial state can cause failed checkout, customer dispute, or conversion loss | **3** | Catalogue state crosses service boundaries and can change during a session | **12** | **High** |
| **RISK-004** — incorrect search/filter/sort results | CAP-02 | **3** | Product discovery is materially degraded but checkout integrity is not directly compromised | **3** | Search permutations and changing catalogue data create plausible correctness defects | **9** | **Medium** |
| **RISK-005** — search latency degrades under demand | CAP-02 | **3** | Slow discovery can reduce conversion and usability without directly corrupting transactions | **3** | Peak traffic and query complexity can expose degradation under normal operating conditions | **9** | **Medium** |
| **RISK-006** — incorrect basket total | CAP-03 | **5** | Customer may be shown or charged an incorrect amount | **2** | Calculation rules are deterministic but can fail at rule/boundary combinations | **10** | **High** |
| **RISK-007** — basket state lost/corrupted | CAP-03 | **4** | Customer cannot reliably continue a prepared purchase | **3** | Navigation, persistence, concurrent updates, and state transitions create plausible exposure | **12** | **High** |
| **RISK-008** — payment succeeds but order is missing | CAP-04 | **5** | Financial state and durable purchase state disagree, requiring recovery/reconciliation | **3** | Cross-system payment, persistence, and failure-handling boundaries make the scenario plausible | **15** | **High** |
| **RISK-009** — uncertain retry creates duplicate charge | CAP-04 | **5** | Customer can be charged more than once for one logical purchase | **3** | Provider timeout/uncertain responses and retries are credible distributed-system conditions | **15** | **High** |
| **RISK-010** — checkout unavailable/too slow at peak | CAP-04 | **5** | Revenue-critical purchases can be broadly blocked | **3** | Peak load, dependency saturation, and service contention are plausible operating conditions | **15** | **High** |
| **RISK-011** — declined payment reported as success | CAP-04 | **5** | Customer and stored order state can misrepresent an unsuccessful financial transaction | **2** | Requires incorrect provider-response/error mapping but is credible at an integration boundary | **10** | **High** |
| **RISK-012** — successful checkout creates duplicate order | CAP-05 | **5** | Duplicate durable orders can trigger duplicate fulfilment/reconciliation work | **3** | Retries/concurrency/eventual consistency can expose missing idempotency controls | **15** | **High** |
| **RISK-013** — stored order not visible to owner | CAP-05 | **4** | Customer trust/support burden is high and purchase status becomes unclear | **2** | Requires persistence/read-model/authorization inconsistency; credible but less frequent | **8** | **Medium** |
| **RISK-014** — duplicate event causes duplicate downstream effects | CAP-05 | **4** | Duplicate fulfilment/notification actions can create operational/customer impact | **2** | At-least-once style delivery makes duplicates plausible, but idempotent consumers should constrain exposure | **8** | **Medium** |
| **RISK-015** — confirmation delayed/missing | CAP-05 | **2** | Customer communication is degraded while the valid order remains durable/retrievable | **3** | Asynchronous delivery and retry paths make delay/failure plausible | **6** | **Medium** |

## Distribution

The initial catalogue contains:

- **0 Critical** risks
- **10 High** risks
- **5 Medium** risks
- **0 Low** risks

This is acceptable. A risk model does not need examples in every band. The catalogue intentionally focuses on meaningful customer/business failure modes rather than inventing low-value risks to populate the matrix.

## Why severe financial risks are not automatically scored Critical

`RISK-008`, `RISK-009`, `RISK-010`, and `RISK-012` have Impact 5 but Likelihood 3, producing a High score of 15. The case study assumes these failures are plausible distributed-system risks but not *likely* or *very likely* under normal design controls.

If evidence later showed frequent timeout-driven duplication, unstable order persistence, or recurring peak checkout outages, the Likelihood rating would be reassessed and could move the risk into the Critical band.

## Treatment examples

### RISK-001 — security-sensitive High risk

Although the baseline score is 10 (High), authorization is a security boundary. The treatment is therefore stronger than a generic High functional risk:

- automated authorization matrix at API/service level
- negative cross-account access checks
- focused security review/testing
- release-critical evidence for changes to authentication/authorization boundaries

This is a **treatment escalation**, not an artificial change to the numeric score.

### RISK-008 — financial consistency High risk

The score of 15 drives strong multi-layer evidence:

- order state-transition rules at component level
- payment/order integration checks
- provider approval + failure-path simulation
- correlation/reconciliation evidence
- focused successful-purchase E2E

The risk is release-critical because it sits directly on the payment/order consistency chain.

### RISK-015 — Medium risk with conditional release relevance

A delayed confirmation is Score 6 (Medium) because the successful order remains durable. It normally receives asynchronous integration and observability coverage without blocking every release.

If a release specifically replaces the notification subsystem or has an explicit communication requirement, the same baseline risk can become release-critical for that change without changing its underlying product-risk score.

## Review triggers

Re-score or re-treat a risk when:

- architecture/service boundaries change
- provider timeout/error semantics change
- incident data contradict the current likelihood assumption
- traffic profile or capacity assumptions increase materially
- an existing control is removed or proven ineffective
- a release directly changes the affected high-risk capability

The selected test seams and release-critical decisions are documented in [`risk-to-test-traceability.md`](risk-to-test-traceability.md).
