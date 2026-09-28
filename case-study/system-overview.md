# ShopSphere — System Overview

## Product purpose

ShopSphere is a fictional distributed e-commerce platform used to demonstrate a complete quality-engineering strategy. Customers can sign in, discover products, manage a basket, complete checkout, and receive a durable order confirmation.

The case study is intentionally realistic enough to create meaningful testing trade-offs while remaining independent of any real employer, client, or production system.

## Business model in scope

For this repository, the customer purchase lifecycle is represented by five top-level capabilities:

| ID | Capability | Primary outcome |
|---|---|---|
| **CAP-01** | Authentication & Account Access | A legitimate customer can securely access their account |
| **CAP-02** | Product Discovery | A customer can find products using accurate catalogue information |
| **CAP-03** | Basket Management | A customer can prepare a correct and stable basket |
| **CAP-04** | Checkout & Payment | A customer can complete payment once and receive an unambiguous result |
| **CAP-05** | Order Fulfilment & Confirmation | A successful purchase becomes one durable, visible order |

See [`business-capabilities.md`](business-capabilities.md) for the detailed capability definitions and quality concerns.

## Primary actors

| Actor | Goal |
|---|---|
| Shopper | Discover products and decide what to buy |
| Registered customer | Access an account, purchase products, and review orders |
| Support agent | Investigate customer, payment, and order issues |
| Merchandising administrator | Maintain product information and availability |
| Payment provider | Authorise, decline, or return an uncertain payment result |
| Operations / support team | Detect, diagnose, and recover from service failures |

## Critical customer journey

The highest-value cross-system journey is the purchase flow:

```text
Sign in
  ↓
Discover product
  ↓
Add/update basket
  ↓
Submit checkout
  ↓
Authorise payment
  ↓
Create one durable order
  ↓
Show order confirmation/history
  ↓
Send confirmation notification
```

This flow crosses every top-level business capability and multiple technical boundaries. It therefore receives broad coverage, but not exclusively through browser automation.

## Additional customer journeys

- invalid sign-in and account recovery handling
- product search, filter, and sort
- basket quantity update and item removal
- payment decline
- payment timeout and safe retry
- order-history retrieval
- delayed notification handling

## Quality attributes

The strategy considers more than functional correctness:

- **Reliability:** orders must not be lost or duplicated.
- **Data integrity:** basket, payment reference, and order state must remain consistent across service boundaries.
- **Performance:** product discovery and checkout must remain responsive under agreed load.
- **Security:** authentication, authorization, session handling, and payment-adjacent data require strong controls.
- **Compatibility:** critical journeys must work on supported browsers and representative mobile viewports.
- **Observability:** failures should be diagnosable through logs, traces, correlation IDs, metrics, and test artifacts.
- **Recoverability:** transient dependency failures must not create ambiguous or duplicate business state.
- **Testability:** important business behaviours should be verifiable below the UI where practical.

## Example business invariants

These invariants are especially important because several risks derive from them:

1. A completed payment must correspond to one durable order.
2. Retrying an ambiguous checkout must not create a duplicate charge or duplicate order.
3. A customer must not access another customer's order information.
4. Basket totals used at checkout must be consistent with the accepted order.
5. An asynchronous notification failure must not invalidate an otherwise successful order.

## System assumptions

The strategy assumes that:

- payment-card details are handled by an external payment provider; ShopSphere stores only safe references/tokens
- the storefront communicates with backend services through REST APIs
- order data is persisted in a relational database
- order-created events are distributed through an asynchronous message/event mechanism
- confirmation email is asynchronous and is not part of the payment transaction
- provider sandbox behaviour can represent approval, decline, timeout, and retry scenarios
- test environments use synthetic customers and non-production credentials
- services expose sufficient logs/correlation identifiers to trace one checkout across system boundaries

## Scope boundary

The case study evaluates ShopSphere's responsibility for orchestrating and validating customer journeys. It does not attempt to test the internal implementation of the external payment provider, email provider, browser, or database engine.
