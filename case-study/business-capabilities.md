# ShopSphere — Business Capabilities

ShopSphere is grouped around five business capabilities. Keeping the model to five top-level capabilities gives the test strategy a stable business view even when the underlying implementation changes.

## Capability map

| ID | Capability | Business outcome | Typical user value | Failure consequence |
|---|---|---|---|---|
| **CAP-01** | Authentication & Account Access | Customers can securely access their account | Sign in, maintain a session, view personal account data | Lost access, unauthorised access, support burden |
| **CAP-02** | Product Discovery | Customers can find and evaluate products | Search, browse, filter, view product details | Reduced conversion, incorrect purchasing decisions |
| **CAP-03** | Basket Management | Customers can prepare a valid purchase | Add, update, remove items; maintain totals | Incorrect totals, abandoned checkout, data inconsistency |
| **CAP-04** | Checkout & Payment | Customers can complete payment exactly once | Confirm order details, submit payment, handle decline/retry | Direct revenue loss, duplicate charge, incomplete purchase |
| **CAP-05** | Order Fulfilment & Confirmation | A successful purchase becomes a durable, visible order | Order creation, order history, confirmation notification | Lost/duplicate orders, customer uncertainty, reconciliation work |

## CAP-01 — Authentication & Account Access

### Outcome

A legitimate customer can sign in and access only the data and actions they are authorised to use.

### Representative behaviours

- successful sign-in with valid credentials
- rejection of invalid credentials
- safe session creation and expiry
- protection of customer-specific order data
- predictable account state after sign-out

### Quality concerns

- authentication bypass
- broken authorization between customers
- session leakage or stale sessions
- inability of legitimate users to access accounts

## CAP-02 — Product Discovery

### Outcome

A customer can discover products and make a purchasing decision using accurate product information.

### Representative behaviours

- keyword search
- category browsing
- filter and sort
- product details
- price and availability presentation

### Quality concerns

- stale or inconsistent product data
- incorrect sorting/filtering
- slow search response under expected traffic
- disagreement between catalogue and basket price

## CAP-03 — Basket Management

### Outcome

A customer can construct a basket whose contents, quantities, and totals remain correct until checkout.

### Representative behaviours

- add item
- change quantity
- remove item
- recalculate totals
- preserve basket state across normal navigation

### Quality concerns

- incorrect totals
- invalid quantities
- stale prices
- lost basket state
- concurrent updates causing inconsistent state

## CAP-04 — Checkout & Payment

### Outcome

A customer can submit a valid purchase and receive a clear result without being charged more than once.

### Representative behaviours

- validate checkout details
- create payment attempt
- handle payment approval
- handle payment decline
- handle provider timeout
- retry safely after an ambiguous result

### Quality concerns

- payment succeeds but order is not created
- duplicate payment after retry
- checkout outage under peak demand
- misleading status after timeout
- invalid basket/order state accepted by checkout

## CAP-05 — Order Fulfilment & Confirmation

### Outcome

A successful checkout produces one durable order that is visible to the customer and can be processed downstream.

### Representative behaviours

- create order exactly once
- persist order and payment reference
- expose order through order history
- publish order-created event
- send confirmation notification

### Quality concerns

- duplicate order creation
- order missing after payment
- order visible to the wrong customer
- event published more than once
- delayed or missing confirmation

## Supporting capabilities

The five capabilities above define the customer-facing risk model. ShopSphere also has supporting capabilities that influence quality but are not modelled as separate top-level capabilities in this case study:

- merchandising administration
- observability and operational support
- notification delivery
- configuration and feature management
- test-data and environment management

## Why capabilities are used in the strategy

Features and service boundaries change over time. A capability such as **Checkout & Payment** remains meaningful even if payment orchestration moves to a different service or the user interface is redesigned.

The quality strategy therefore starts from business capabilities and their failure modes, then selects the test level and automation technique that provide the strongest evidence for each risk.

## From capability to requirement

The capability model defines stable business outcomes; the detailed, testable outcomes are catalogued in [`requirements.md`](requirements.md). Those requirements are then linked to risks and test evidence in [`../risk/risk-to-test-traceability.md`](../risk/risk-to-test-traceability.md).
