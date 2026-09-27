# ShopSphere - System Overview

## Purpose of the Product

ShopSphere is a hypothetical distributed e-commerce platform used as a demonstration of a holistic quality-engineering approach. The system allows a customer to sign in, find a product, maintain a shopping basket, complete a checkout process, and receive a durable order confirmation.

This case study is designed to be realistically detailed in order to provide tangible testing trade-offs, while not being affiliated with any employer, client, or production environment.

## Scope of the Business Model

In this repository, customer purchase process is defined through five main capabilities:

| ID | Capability | Primary Outcome |
|---|---|---|
| **CAP-01** | Authentication and Account Access | A genuine customer is able to access their account |
| **CAP-02** | Product Discovery | A customer is able to find products based on accurate catalogue information |
| **CAP-03** | Basket Management | A customer is able to create a correct and stable basket |
| **CAP-04** | Checkout and Payment | A customer is able to make a payment once and receive a definite outcome |
| **CAP-05** | Order Creation and Confirmation | A successfully completed purchase turns into a durable and visible order |


Definitions of each capability along with the quality-related concerns are outlined in the document [`business-capabilities.md`](business-capabilities.md).

## Primary Actors

| Actor | Purpose  |
|---|---|
| Shopper | Find products and make purchase decisions |
| Registered customer | Access the account, purchase products and review orders |
| Support agent | Diagnose customer, payment and order-related problems |
| Merchandising administrator | Update the product information and availability |
| Payment provider | Approve, reject or decline to make a decision about the uncertainty of a payment |
| Operations / support team | Diagnose and resolve service failures |

## Important Customer Journey

The most valuable cross-system journey is the purchase process:

```text
Sign in
  ↓
Find product
  ↓
Update the basket
  ↓
Submit checkout
  ↓
Authorize payment
  ↓
Create a single durable order
  ↓
Display order confirmation/history
  ↓
Notify the order completion
```

This journey spans all top-level business capabilities and many technical boundaries. Thus, wide coverage is needed. Coverage shall be comprehensive and not limited to browser automation.

## Additional Customer Journeys

- Sign in validation
- Invalid sign in recovery
- Product search, filtration and sorting
- Basket quantity update and items removal
- Payment rejection
- Payment timeout and safe retry
- Display of order history
- Notification timeout handling

## Quality Attributes

Along with functional correctness, the following quality attributes are considered in the strategy:

- **Reliability:** the orders should not be lost and/or duplicated
- **Data integrity:** the basket, payment reference and order status should remain consistent across service boundaries
- **Performance:** product discovery and checkout process should demonstrate responsiveness under the predefined load
- **Security:** the authentication, authorization, session management and payments-related data should be strictly controlled
- **Compatibility:** important user journeys should work with the supported web browsers and mobile viewports
- **Observability:** failures should be diagnosable through logs, traces, correlation identifiers, metrics and test artifacts
- **Recoverability:** temporary failures in external dependencies should not lead to ambiguous or duplicate business state
- **Testability:** important business behaviors should be testable beneath the user interface when possible

## Important business invariants

The invariants are particularly important since several risks are associated with them:

1. A completed payment should lead to creation of a single durable order.
2. Retry of an ambiguous checkout should not cause a duplicate payment or order.
3. A customer should not have access to another customer's order data.
4. The basket totals used in the checkout process should match the accepted order.
5. A notification delivery failure should not affect the order.

## System assumptions

This strategy assumes that:

- credit card information is managed by the external payment provider; ShopSphere holds only safe references or tokens
- storefront communicates with backend services via REST APIs
- order data are stored in a relational database
- order-created events are published through asynchronous messaging/eventing mechanism
- the confirmation email is asynchronous and not a part of the payment transaction
- provider sandbox behavior is capable of simulating approval, rejection, timeout and retry cases
- test environments use synthetic customers and non-production credentials
- services have enough logs and correlation identifiers to trace a single checkout across service boundaries

## Scope boundary

This case study evaluates ShopSphere's responsibility for the orchestration and validation of customer journeys. It does not attempt to test the implementation of external payment providers, email providers, browsers and databases.
