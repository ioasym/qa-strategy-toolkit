# ADR-002 — Use API-First Test-Data Setup and Teardown

- **Status:** Accepted
- **Decision owners:** Quality Engineering + Service owners
- **Related capabilities:** CAP-01, CAP-03, CAP-04, CAP-05
- **Related risks:** RISK-006, RISK-007, RISK-008, RISK-009, RISK-012, RISK-013

## Context

Reliable automated tests require predictable preconditions. Creating every user, basket, product state, and order through the UI would increase runtime and couple test setup to user-interface behaviour that is unrelated to the scenario being verified.

Direct database manipulation can be fast, but it bypasses service contracts and can create invalid or unrealistic state if schemas or invariants change.

## Decision

Where supported, automated tests will create and clean up their preconditions through **public or dedicated test APIs** rather than through the UI or direct database writes.

Preferred order for test-data setup is:

1. public/dedicated API or service boundary
2. approved test fixture or service-level builder
3. controlled integration fixture when the lower boundary itself is under test
4. UI setup only when UI setup is part of the behaviour being validated

Direct production-like database mutation is not the default test-data strategy.

Each parallel test should own unique or immutable data where practical. Shared mutable accounts, baskets, and orders should be avoided.

## Alternatives considered

### A. Create all state through the UI

**Advantages**

- represents real customer interaction
- avoids needing additional test interfaces

**Reasons not selected**

- slow
- duplicates UI steps unrelated to many test objectives
- makes failures harder to localise
- increases dependence on UI availability

### B. Create state directly in the database

**Advantages**

- fast
- precise control over records

**Reasons not selected as the default**

- bypasses domain validation and service invariants
- couples tests to persistence design
- can create states the application cannot create legitimately
- makes refactoring storage more expensive

## Consequences

### Positive

- faster test setup
- improved isolation
- better support for parallel execution
- clearer intent in browser tests
- reduced coupling between scenario setup and UI implementation

### Trade-offs

- may require test-support endpoints or builders
- test APIs require ownership and access control
- cleanup must be designed carefully for asynchronous flows
- API availability becomes a dependency for some UI tests

## Data management guardrails

- use synthetic identities and data
- generate unique identifiers for mutable entities
- avoid shared mutable state across tests
- do not commit secrets or production data
- make cleanup idempotent where possible
- preserve enough failed-test data to support diagnosis when required

## Example

Instead of creating a customer and populating a basket through ten browser interactions before testing checkout:

```text
Test API: create synthetic customer
        ↓
Test API: create basket with known product
        ↓
Browser: authenticate as customer
        ↓
Browser: execute checkout behaviour under test
```

The setup proves less through the browser, but the test becomes faster and more specific about the risk it is intended to cover.

## Evidence that would justify revisiting this decision

Reconsider this ADR if test APIs become less representative than the actual service contracts, if test-support interfaces create unacceptable security/maintenance cost, or if a feature's risk specifically depends on UI-driven creation of state.
