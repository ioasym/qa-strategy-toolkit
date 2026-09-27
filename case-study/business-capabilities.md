# ShopSphere – Business Capabilities

This case study models ShopSphere around five core capabilities. The capability model is intentionally brief in order to give the testing strategy a business-focused perspective which remains stable regardless of any possible changes in the underlying implementation.

## Capability map

| ID | Capability | Business outcome | Representative user value | Consequence of failure |
|---|---|---|---|---|
| **CAP-01** | Authentication and Account Access | Customers are able to access their account securely | Sign in, keep a session alive, see personal account information | Loss of access, unauthorized access, extra support load |
| **CAP-02** | Product Discovery | Customers are able to find and evaluate products | Search, browse, filter, look at product information | Poor conversion, wrong purchases |
| **CAP-03** | Basket Management | Customers are able to create a valid purchase basket | Add/remove/change the number of items, calculate the total cost | Wrong totals, abandoned checkout, data inconsistency |
| **CAP-04** | Checkout and Payment | Customers are able to make payment exactly once | Confirm purchase details, pay, handle rejections/declines | Revenue loss, duplicate payments, partial purchase |
| **CAP-05** | Order Fulfillment and Confirmation | A successful purchase creates an order | Order creation, history, order confirmation notification | Missing orders, double orders, unclear orders for customers |

## CAP-01 — Authentication and Account Access

### Outcome

A legitimate customer is able to sign in and access only data and actions he is entitled to access.

### Representative behaviors

- sign-in with a valid pair of credentials
- sign-in attempt rejection in case of invalid credentials
- creation and expiration of a secure session
- protection of order data of the individual customer
- consistency of the account state after logout

### Quality concerns

- account bypass
- broken authorization between customers
- session leak or stale session
- inability of legitimate users to access accounts

## CAP-02 — Product Discovery

### Outcome

A customer  is able to find  products and make a purchasing decision based on accurate product information.

### Representative behaviors

- keyword search
- category browsing
- sorting and filtering
- looking at product details
- displaying price and availability

### Quality concerns

- stale or inconsistent product data
- incorrect sorting/filtering
- slow search response under expected load
- price and availability mismatch between catalog and basket

## CAP-03 — Basket Management

### Outcome

A customer is able to create a basket where the contents, quantities and totals will be correct till the point of checkout.

### Representative behaviors

- add an item
- change quantity
- remove an item
- recalculate totals
- maintain basket state during regular navigation

### Quality concerns

- incorrect totals
- invalid quantities
- stale prices
- loss of basket state
- concurrency causing inconsistent state

## CAP-04 — Checkout and Payment

### Outcome

A customer is able to submit a valid purchase and receive clear feedback without being charged more than once.

### Representative behaviors

- validate checkout details
- create payment attempt
- handle payment approval
- handle payment decline
- handle payment timeout
- retry safely after an ambiguous result

### Quality concerns

- payment is successful but there is no order created
- duplicate payment after retry
- checkout outage under peak load
- misleading status after timeout
- invalid basket/order state allowed by checkout

## CAP-05 — Order Fulfillment and Confirmation

### Outcome

A successful checkout creates one durable order which is visible to the customer and can be processed downstream.

### Representative behaviors

- create order exactly once
- persist order and payment reference
- order exposure through order history
- publication of order-created event
- sending order confirmation notification

### Quality concerns

- order is created more than once
- no order after the payment
- order visible to the wrong customer
- event is published more than once
- delayed or missing order confirmation

## Supporting capabilities

The five capabilities above are the basis of the risk model for customers. ShopSphere also has some capabilities which are influencing quality, but are not treated separately as top-level capabilities in the current case study:

- merchandising administration
- observability and operational support
- notification delivery
- configuration and feature management
- test-data and environment management

## Why capabilities are used in the strategy

Features and service boundaries are subject to evolution. The capability such as **Checkout  Payment** retains its meaning even if the payment orchestration moves to a different service or if the user interface is redesigned.
For this reason, the quality strategy begins with business capabilities and the failure modes of these capabilities. Then the testing level and automation approach are selected in order to get most convincing evidence against the identified risks.
