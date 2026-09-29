# Risk-to-Test Traceability

This matrix connects the ShopSphere business model to concrete quality evidence.

```text
Business capability
        ↓
Requirement
        ↓
Risk / failure mode
        ↓
Primary test seam
        ↓
Automation decision
        ↓
Release evidence
```

The purpose is not to create traceability for its own sake. The matrix makes it possible to explain **why a test exists, which risk it addresses, where it is most effective, and whether its evidence is required for a release decision**.

## Decision rules

- Use the **lowest test level that can detect the failure reliably**.
- Add integration coverage when correctness depends on a real technical boundary.
- Add UI E2E only when confidence depends on the customer-facing cross-system journey.
- Mark a check as release-critical only when its failure would materially change the release risk decision.
- “Automated” does not mean “UI automated”; most high-volume permutations should remain below the browser layer.

## Core traceability matrix

The baseline risk bands in this matrix come from [`product-risk-analysis.md`](product-risk-analysis.md). Band and release criticality are intentionally separate: a risk score prioritises the product failure mode, while a release gate also depends on change scope and evidence needs.

| Risk | Band | Capability | Requirement(s) | Primary test seam | Supporting evidence | Automation decision | Release critical? |
|---|---|---|---|---|---|---|---|
| **RISK-001** — unauthorised account/order access | **High** | CAP-01 | REQ-003 | API/service | negative authorization checks; security review | Automate service-level authorization matrix; add focused security testing | **Yes** |
| **RISK-002** — valid session cannot be established/maintained | **High** | CAP-01 | REQ-001, REQ-002, REQ-004 | API/service | focused login/session UI E2E | Automate auth API lifecycle + small UI smoke path | **Yes** |
| **RISK-003** — stale/incorrect price or availability | **High** | CAP-02 | REQ-006, REQ-020 | Integration/API | catalogue-to-basket consistency evidence | Automate contract/consistency checks; avoid large UI permutations | **Yes** when checkout-affecting |
| **RISK-004** — incorrect search/filter/sort results | **Medium** | CAP-02 | REQ-005 | API/service | representative browser scenario; exploratory search charters | Automate API permutations + limited UI coverage | No, unless change affects critical discovery path |
| **RISK-005** — search latency degrades under demand | **Medium** | CAP-02 | REQ-007 | Performance/API | latency/error-rate trend and load profile | Automate scheduled/load-pipeline performance scenario where stable | Conditional |
| **RISK-006** — incorrect basket total | **High** | CAP-03 | REQ-009, REQ-020 | Component/API | selected integration checks into checkout | Automate price/quantity/commercial-rule permutations below UI | **Yes** |
| **RISK-007** — basket state lost/corrupted | **High** | CAP-03 | REQ-008, REQ-010 | API/integration | targeted basket-to-checkout E2E | Automate state-transition and persistence checks; one representative UI path | **Yes** |
| **RISK-008** — payment succeeds but order is missing | **High** | CAP-04 | REQ-011, REQ-012, REQ-021 | Integration | reconciliation signal; successful-purchase E2E | Automate provider approval + persistence + correlation checks | **Yes** |
| **RISK-009** — uncertain retry creates duplicate charge | **High** | CAP-04 | REQ-014, REQ-021 | Integration/resilience | repeated-request evidence; provider sandbox logs | Automate timeout/retry/idempotency scenarios | **Yes** |
| **RISK-010** — checkout unavailable/too slow at peak | **High** | CAP-04 | REQ-015 | Performance/integration | capacity/error-rate/latency report | Automate controlled load scenario in suitable environment | **Yes** for releases affecting checkout/capacity |
| **RISK-011** — declined/failed payment reported as success | **High** | CAP-04 | REQ-013 | Integration/API | focused declined-payment E2E | Automate provider-response mapping + one browser negative path | **Yes** |
| **RISK-012** — successful checkout creates duplicate order | **High** | CAP-05 | REQ-016, REQ-014 | Integration/component | concurrency/retry evidence | Automate idempotent order-creation and duplicate-request cases | **Yes** |
| **RISK-013** — stored order not visible to owner | **Medium** | CAP-05 | REQ-017, REQ-003 | Integration/API | order-history E2E within consistency window | Automate persistence/read-model checks + focused UI confirmation | **Yes** |
| **RISK-014** — duplicate event causes duplicate downstream effects | **Medium** | CAP-05 | REQ-018 | Integration/component | consumer state and duplicate-message evidence | Automate duplicate-delivery/idempotent-consumer scenarios | Conditional |
| **RISK-015** — confirmation delayed/missing | **Medium** | CAP-05 | REQ-019 | Async integration | retry/dead-letter/queue observability | Automate notification handoff and failure-path checks | No, unless notification is a release-specific objective |

## Cross-capability traceability

### Price consistency — CAP-02 → CAP-03 → CAP-04 → CAP-05

```text
REQ-006 / REQ-009 / REQ-020
        ↓
RISK-003 + RISK-006
        ↓
Catalogue/API contract checks
        ↓
Basket calculation tests
        ↓
Checkout acceptance integration checks
        ↓
Persisted order commercial-state verification
```

A single UI test cannot economically cover every price/quantity combination. Most evidence therefore sits at component/API level, with selected integration/E2E evidence proving that the same accepted commercial state survives the customer journey.

### Payment/order consistency — CAP-04 → CAP-05

```text
REQ-012 + REQ-014 + REQ-016 + REQ-021
        ↓
RISK-008 + RISK-009 + RISK-012
        ↓
Provider approval / timeout simulation
        ↓
Idempotent checkout and order creation
        ↓
Durable order persistence
        ↓
Correlation / reconciliation evidence
        ↓
Focused successful-purchase UI E2E
```

This is the principal release-critical chain in the case study. No single test replaces the other controls because the failure modes occur at different boundaries.

### Session interruption during checkout — CAP-01 → CAP-04

```text
REQ-004 + REQ-022
        ↓
RISK-002 + RISK-009
        ↓
Session lifecycle checks
        ↓
Checkout authorization boundary
        ↓
Safe retry/idempotency evidence
```

The objective is to prove both security and transaction safety: an expired session must not silently continue an unauthorised purchase, while a legitimate retry must not duplicate the financial transaction.

## Release evidence view

A release candidate does not require every repository test to be a blocking gate. Instead, evidence is selected according to risk and change scope.

### Always expected for the critical purchase path

- authenticated customer access behaves correctly (`REQ-001` to `REQ-004` as relevant)
- basket total and state are valid (`REQ-008` to `REQ-010`)
- successful payment produces one durable order (`REQ-012`, `REQ-016`)
- decline is handled correctly (`REQ-013`)
- ambiguous retry does not duplicate payment/order state (`REQ-014`)
- order is retrievable by the correct customer (`REQ-017`)

### Conditional evidence

- search/load evidence when catalogue/search changes (`REQ-005`, `REQ-007`)
- checkout performance evidence for material performance/capacity changes (`REQ-015`)
- duplicate-event and notification evidence when asynchronous processing changes (`REQ-018`, `REQ-019`)

## Traceability maintenance rule

When a new requirement or risk is introduced, update the matrix only if it changes a quality decision. Avoid creating one-to-one paperwork links that do not influence test scope, automation, ownership, or release evidence.
