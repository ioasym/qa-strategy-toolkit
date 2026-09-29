# Test Strategy — ShopSphere

## 1. Purpose

This strategy defines how quality risk will be evaluated and how testing will provide fast, trustworthy information about release readiness.

## 2. Quality objectives

The strategy prioritises:

1. Preventing financial and order-integrity defects.
2. Protecting authentication and authorization boundaries.
3. Maintaining a reliable checkout path.
4. Detecting regressions early through fast automated feedback.
5. Keeping browser-level automation focused, deterministic, and maintainable.
6. Producing evidence that supports explicit release decisions.

## 3. Scope

### In scope

- Web storefront
- Authentication
- Catalogue/search
- Basket
- Checkout
- Payment-provider sandbox integration
- Order creation
- Notification handoff
- Supported browsers and representative mobile viewport coverage

### Out of scope for this example

- Real payment-card processing
- Production customer data
- Third-party provider internal implementation
- Full penetration testing

## 4. Test approach by level

| Level | Primary purpose | Typical ownership | Feedback speed |
|---|---|---|---|
| Unit/component | Business rules and local edge cases | Developers | Seconds |
| API/service | Contract and service behaviour | Dev + QA/SDET | Seconds/minutes |
| Integration | Dependencies, persistence, messaging | Dev + QA/SDET | Minutes |
| UI E2E | Critical customer journeys | QA/SDET | Minutes |
| Exploratory | New/changed behaviour and unknown risk | Whole team | Variable |
| Performance | Capacity, latency, stability | QE/SDET + platform | Scheduled |
| Security | AuthN/AuthZ and security controls | Security + engineering | Continuous/scheduled |

## 5. Risk-based prioritisation

ShopSphere uses the explicit scoring definitions in [`../risk/risk-assessment-model.md`](../risk/risk-assessment-model.md):

```text
Risk score = Business impact × Failure likelihood
```

The applied scores and rationale are recorded in [`../risk/product-risk-analysis.md`](../risk/product-risk-analysis.md). Scores are reviewed alongside:

- release/change scope
- recent architecture or dependency changes
- incident/defect history when available
- observability and recovery gaps
- security, contractual, or regulatory obligations
- evidence freshness and environment relevance

### Risk bands

| Score | Band | Default treatment |
|---:|---|---|
| 16–25 | Critical | Multi-layer evidence, resilience/negative coverage, explicit release-risk disposition |
| 10–15 | High | Strong automated evidence at the lowest useful layer; release relevance reviewed explicitly |
| 5–9 | Medium | Targeted automated/manual/exploratory evidence based on change scope |
| 1–4 | Low | Selective verification based on change and customer relevance |

**Risk band is not the same as a release gate.** A Medium risk directly changed by the release can require fresh blocking evidence, while a High risk outside the change scope may rely on stable regression evidence. Release-critical decisions are tracked separately in `risk-to-test-traceability.md`.

## 6. Critical regression pack

The release-critical automated pack is derived from the requirement/risk traceability model rather than from UI pages alone. It includes evidence for:

- valid/invalid authentication and session control (`REQ-001`–`REQ-004`, `RISK-001`/`RISK-002`)
- basket state and total correctness (`REQ-008`–`REQ-010`, `RISK-006`/`RISK-007`)
- successful checkout producing one durable order (`REQ-012`, `REQ-016`, `RISK-008`/`RISK-012`)
- declined payment producing the correct negative outcome (`REQ-013`, `RISK-011`)
- retry after an uncertain result remaining idempotent (`REQ-014`, `RISK-009`)
- order retrieval by the correct customer (`REQ-017`, `RISK-013`)

Product search remains strongly automated, but whether it is a release-blocking gate depends on the release/change scope. Performance and asynchronous-processing evidence are similarly conditional where the relevant components or capacity assumptions have changed.

The blocking pack should remain small enough to be reliable and fast, while deeper component/API/integration suites provide broader evidence below the browser layer. See `../risk/risk-to-test-traceability.md` and [`../decisions/ADR-001-ui-automation-scope.md`](../decisions/ADR-001-ui-automation-scope.md).

## 7. Test data

- Prefer synthetic test users and products.
- Generate unique order/customer identifiers per run.
- Avoid shared mutable records across parallel tests.
- Reset or recreate data through APIs where possible; see [`../decisions/ADR-002-api-first-test-data.md`](../decisions/ADR-002-api-first-test-data.md).
- Never commit secrets or production-like customer data.

## 8. Environments

| Environment | Purpose |
|---|---|
| Local/dev | Fast developer verification |
| Integration | Service/API/integration checks |
| QA | Functional and exploratory testing |
| Pre-production | Release-candidate and production-like validation |

Environment drift must be visible. Configuration, versions, and provider sandbox dependencies should be recorded with execution evidence.

## 9. Defect management

Defect severity is based on impact. Priority reflects scheduling urgency. Triage considers customer impact, frequency, workaround, release timing, and operational detectability.

## 10. Automation quality bar

Automated tests should be:

- deterministic
- independent
- maintainable
- appropriately layered
- observable when they fail
- fast enough for their pipeline stage

Repeated flaky tests are treated as defects in the test system rather than accepted as normal noise. The investigation, quarantine, retry, and restoration policy is documented in [`../decisions/ADR-003-flaky-test-policy.md`](../decisions/ADR-003-flaky-test-policy.md).

## 11. Exit decision

A release decision is supported by:

- critical-path pass status
- unresolved critical/high defects
- risk acceptance decisions
- regression results
- performance/security evidence where applicable
- change-specific exploratory results
- known limitations

Testing informs the release decision; it does not replace business ownership of accepted risk. Blocking evidence is selected using the risk- and change-aware approach in [`../decisions/ADR-004-release-quality-gates.md`](../decisions/ADR-004-release-quality-gates.md).
