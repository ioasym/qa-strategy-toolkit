# ADR-001 — Limit UI Automation to Critical Cross-System Journeys

- **Status:** Accepted
- **Decision owners:** Quality Engineering + Engineering
- **Related capabilities:** CAP-01, CAP-02, CAP-03, CAP-04, CAP-05
- **Related risks:** RISK-001, RISK-006, RISK-008, RISK-011, RISK-012, RISK-013

## Context

ShopSphere is a distributed e-commerce platform. A customer journey can cross the browser, authentication, catalogue, basket, payment, order, database, and notification boundaries.

It would be possible to validate a large percentage of business permutations through the browser. Doing so would, however, make the regression suite slower, more expensive to maintain, and harder to diagnose when a failure occurs.

Many business rules can be exercised more directly at component, API, or integration level. Browser automation is most valuable where the browser interaction itself or cross-system orchestration is part of the risk.

## Decision

ShopSphere will keep UI end-to-end automation focused on **critical customer journeys and browser-specific integration confidence**.

Detailed data permutations and business-rule combinations will be tested at the lowest useful layer:

- component tests for local business rules
- API/service tests for contracts, validation, and service behaviour
- integration tests for persistence, messaging, payment-provider behaviour, idempotency, and recovery
- UI E2E tests for a representative set of critical user journeys

The critical browser suite will include, at minimum where relevant to the release:

- valid authentication
- basket-to-checkout transition
- successful checkout and order confirmation
- declined payment behaviour
- order retrieval by the authenticated customer

## Alternatives considered

### A. Automate most functional coverage through the UI

**Advantages**

- resembles end-user interaction
- provides broad cross-system coverage

**Reasons not selected**

- slow feedback
- increased selector and environment sensitivity
- more difficult fault localisation
- repeated coverage of rules that can be tested faster below the UI
- greater execution and maintenance cost

### B. Avoid UI automation and rely on API/integration tests

**Advantages**

- very fast and stable feedback
- easier diagnostics

**Reasons not selected**

- would not verify browser interaction, routing, rendering, client-side state, or complete critical journeys
- would leave important customer-facing integration risks uncovered

## Consequences

### Positive

- faster regression feedback
- clearer fault localisation
- lower UI maintenance burden
- broader data coverage can be achieved at lower layers
- browser suite can remain a high-signal release gate

### Trade-offs

- requires strong API/component testability
- total quality evidence is distributed across multiple test layers
- teams must understand which layer owns each risk to avoid accidental coverage gaps

## Guardrails

A new UI test should have a clear reason for requiring browser-level execution. It should not be added merely because the scenario can be expressed as a user journey.

Before adding a UI test, ask:

1. What product risk does the test address?
2. Can the behaviour be verified more deterministically below the UI?
3. Is browser behaviour itself part of the risk?
4. Does the test duplicate existing lower-layer evidence?
5. Is the expected failure actionable?

## Evidence that would justify revisiting this decision

Reconsider this ADR if:

- critical browser-specific regressions escape despite lower-layer coverage
- application architecture moves substantial business logic into the client
- UI test reliability improves materially while execution cost remains acceptable
- product risk changes so that additional browser journeys become release-critical
