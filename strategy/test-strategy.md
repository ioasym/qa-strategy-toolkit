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

Risk is estimated using:

```text
Risk score = Business impact × Failure likelihood
```

Scores are reviewed alongside:

- recent code changes
- incident history
- architectural complexity
- dependency changes
- observability gaps
- regulatory/security obligations

### Suggested bands

| Score | Band | Expected treatment |
|---:|---|---|
| 16–25 | Critical | Multi-layer coverage, negative paths, release-blocking evidence |
| 10–15 | High | Strong automated coverage + exploratory focus |
| 5–9 | Medium | Targeted automated/manual coverage |
| 1–4 | Low | Selective testing based on change |

## 6. Critical regression pack

The release-critical automated pack covers:

- valid/invalid login
- product search
- add to basket
- price/basket recalculation
- successful checkout
- declined payment
- retry-safe order creation
- order persisted and visible in order history

The pack should be small enough to remain reliable and fast enough to execute on every deployment candidate.

## 7. Test data

- Prefer synthetic test users and products.
- Generate unique order/customer identifiers per run.
- Avoid shared mutable records across parallel tests.
- Reset or recreate data through APIs where possible.
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

Repeated flaky tests are treated as defects in the test system rather than accepted as normal noise.

## 11. Exit decision

A release decision is supported by:

- critical-path pass status
- unresolved critical/high defects
- risk acceptance decisions
- regression results
- performance/security evidence where applicable
- change-specific exploratory results
- known limitations

Testing informs the release decision; it does not replace business ownership of accepted risk.
