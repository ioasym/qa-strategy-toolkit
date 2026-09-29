# ADR-004 — Use Risk- and Change-Aware Release Quality Gates

- **Status:** Accepted
- **Decision owners:** Product + Engineering + Quality Engineering
- **Related artifacts:** risk assessment model, traceability matrix, entry/exit criteria, release-readiness record

## Context

A fixed rule such as "all tests must pass" sounds objective but does not describe which evidence matters, whether all tests are equally relevant, how known defects are handled, or whether the current release changed the affected capability.

Likewise, a numeric product-risk score is useful for prioritisation but should not mechanically determine a release decision. Release readiness depends on current change scope, evidence freshness, unresolved defects, environment relevance, and accepted residual risk.

## Decision

ShopSphere will use **risk- and change-aware quality gates**.

A gate is blocking when its evidence is necessary to control a release-relevant risk. The set of blocking evidence may therefore vary by release while a small core of critical controls remains stable.

### Stable core gates

The following remain blocking for normal production releases unless an explicit exception is recorded:

- authentication/session critical path
- basket and price integrity critical path
- one durable order from successful checkout
- correct declined-payment outcome
- idempotent retry after ambiguous payment outcome
- authenticated customer can retrieve only the correct order data
- no unresolved Critical defect without explicit accountable acceptance

### Conditional gates

Additional blocking evidence is selected when change scope or risk requires it, for example:

- catalogue/search regression when search logic changed
- performance evidence when checkout implementation, traffic assumptions, infrastructure, or dependencies changed
- resilience evidence when retry, timeout, provider, or messaging behaviour changed
- security-focused evidence when authentication/authorization controls changed

## Gate decision inputs

Release readiness considers:

1. changed capabilities and requirements
2. baseline product risks and risk bands
3. whether controls for those risks were exercised on the release candidate
4. result quality and evidence freshness
5. open defects and known limitations
6. environment/dependency relevance
7. residual risk and accountable acceptance

## Alternatives considered

### A. Fixed global pass-rate threshold

Example: release when 98% of tests pass.

**Reasons not selected**

- one failing financial-integrity control can matter more than dozens of passing low-risk tests
- encourages focus on volume rather than evidence relevance
- flaky or obsolete tests distort the percentage

### B. Block release whenever any High product risk exists

**Reasons not selected**

- product risk describes potential failure exposure, not whether the current release introduced unacceptable residual risk
- mature products will often have High baseline risks that are controlled by architecture, automated evidence, monitoring, or operational safeguards

## Consequences

### Positive

- release evidence aligns with actual product risk
- change-specific testing becomes explicit
- pass-rate vanity metrics have less influence
- residual risk can be discussed transparently

### Trade-offs

- requires discipline to map changes to capabilities/risks
- quality gates require periodic review
- release decisions involve informed judgment rather than a single universal number

## Exception handling

If a blocking gate cannot be satisfied:

1. document the missing or failed evidence
2. state the affected requirement/risk
3. identify customer/business impact
4. document available mitigations, monitoring, rollback, or feature-flag controls
5. record the accountable decision owner and expiry/follow-up action

An exception is visible risk acceptance, not a silent bypass.

## Evidence that would justify revisiting this decision

Reconsider this ADR if the system moves to a release model where independently deployable components can use more local gates, if regulatory requirements impose fixed evidence, or if operational controls materially change the cost of specific failure modes.
