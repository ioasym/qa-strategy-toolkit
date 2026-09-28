# ShopSphere - Requirements Catalogue

The current document formalizes the five ShopSphere business capabilities into the concise catalogue of testable requirements. In contrast to detailed user interface specification, each requirement intentionally is written at business/system level in order to provide some stability against ever changing implementation details.

Each requirement has a unique identifier (`REQ-xxx`) and is associated with one or more risks in `../risk/risk-to-test-traceability.md`.

## Requirement-writing principles

Catalog follows four principles for each requirement:

1. Describes a visible business/system outcome.
2. Does not mention any particular test level or automation framework.
3. Has enough specificity to collect evidence and assess risks.
4. Does not contain any implementation details that could not be considered known at the time of requirement formulation.

---

## CAP-01 — Authentication and Account Access

| Requirement ID | Requirement | Evidence of Satisfaction |
|---|---|---|
| **REQ-001** | A registered customer with valid credentials can create an authenticated session. | Authenticated session has been created. |
| **REQ-002** | Incorrect credentials cannot create an authenticated session. | Creation of authenticated session was rejected. |
| **REQ-003** | An authenticated customer can access only authorized own account/order resources. | Any request for non-authorized resources of the other customer has been denied. |
| **REQ-004** |  Session expiration or sign out destroys all accesses which have been obtained using the previous authenticated session. | Any request for protected actions either fails or requires authentication after the session expiration/sign out. |

### Primary Risk Associations

- `RISK-001` - unauthorized access to another customer's data
- `RISK-002` - a legitimate customer cannot create or maintain authenticated session

---

## CAP-02 — Product Discovery

| Requirement ID | Requirement | Evidence of Satisfaction |
|---|---|---|
| **REQ-005** | A customer can search/browse the catalogue and receive products matching the selected criteria. | Correct search/filter/sort responses are received for representative and boundary cases. |
| **REQ-006** | Price and availability information which is shown for purchasing purposes should match the catalogue state which is acceptable for further basket/checkout processing. | There is no silent disagreement between catalogue, basket and checkout on the accepted commercial state. |
| **REQ-007** | Product discovery should continue to perform within the agreed service threshold under representative expected load. | Latency/error rate measured values are within the agreed service threshold for the given load. |

### Primary Risk Associations

- `RISK-003` - incorrect/stale price/availability
- `RISK-004` - incorrect / incomplete search/filter/sort results
- `RISK-005` - slow search latency under expected peak load

---

## CAP-03 — Basket Management

| Requirement ID | Requirement | Evidence of Satisfaction |
|---|---|---|
| **REQ-008** | A customer can add, update or remove valid basket items/quantities. |  Basket is updated in accordance with the request. Invalid basket state transitions are rejected. |
| **REQ-009** | Basket totals should be calculated from the accepted items/quantities/prices/commercial rules. | Calculations performed on component/API level match expected totals. |
| **REQ-010** | Basket state should be consistent throughout the normal navigation until checkout consumes or invalidates it. | Basket can be reread without unexpected losses, duplications or corruptions. |

### Primary Risk Associations

- `RISK-006` - incorrect basket total
- `RISK-007` - lost / corrupted basket state before checkout

---

## CAP-04 — Checkout and Payment

| Requirement ID | Requirement | Evidence of Satisfaction |
|---|---|---|
| **REQ-011** | Checkout should validate that the provided basket/purchase data are in an acceptable state before trying to process payment. | Invalid checkout requests are rejected before producing an invalid order/payment state. |
| **REQ-012** | An accepted payment results in one durable ShopSphere order referencing the accepted payment outcome. | Order and payment states can be reconciled for the same transaction. |
| **REQ-013** | A refused payment should not be accepted as a successful purchase and does not produce a completed order. | Decline response, customer visible state and order state are in agreement. |
| **REQ-014** | Repeating a checkout request after an unclear payment result does not produce a duplicated charge or completed purchase. | Repeated requests for the same transaction are idempotent on the payment and order level. |
| **REQ-015** | Checkout should be available and responsive within the agreed service thresholds under the given peak load profile. | Checkouts latency/throughput/error rate stays within the agreed thresholds for the given profile. |

### Primary Risk Associations

- `RISK-008` - payment succeeded but did not produce a durable order
- `RISK-009` - repeated actions produced a duplicated charge
- `RISK-010` - checkout is not available or too slow under peak load
- `RISK-011` - a declined/failing payments are incorrectly reported

---

## CAP-05 — Order Fulfillment and Confirmation

| Requirement ID | Requirement | Evidence of Satisfaction |
|---|---|---|
| **REQ-016** | A single checkout request does not produce duplicate durable orders. | Repeat of the same logical request does not produce additional orders. |
| **REQ-017** | Owning customer should be able to read a successfully created order from the order history within the specified consistency window. | Created order is visible to the correctly authenticated customer. |
| **REQ-018** | Order-created events and downstream processing should tolerate duplicates without producing duplicate downstream business effects.. | Redelivery of the event does not cause duplicate downstream processing actions. |
| **REQ-019** | The order remains valid and accessible even if asynchronous confirmation notification fails or delayed. | Order remains valid and accessible even if notification processing failed or repeated. |

### Primary Risk Associations

- `RISK-012` - duplicate order creation
- `RISK-013` - order is not visible to the owning customer
- `RISK-014` - event duplicates are processed downstream and produce duplicate actions
- `RISK-015` - delayed or failing notification process

---

## Cross-Capability Requirements

Some of outcomes require coordination across several capabilities in order to keep consistency.

| Requirement ID | Cross-capability requirement | Capabilities |
|---|---|---|
| **REQ-020** | Accepted commercial state at checkout should be traceable to both the basket and order so unexpected price/quantity changes can be detected. | CAP-02, CAP-03, CAP-04, CAP-05 |
| **REQ-021** | Single customer transaction should be traceable across checkout, payment, order persistence and asynchronous processing using stable correlation mechanism. | CAP-04, CAP-05 |
| **REQ-022** | Session interruption during checkout should not allow unauthorized continuation or duplication of transaction if the customer retries. | CAP-01, CAP-04 |

## Requirement Scope Notes

The requirements are intentionally implementation agnostic. For example, `REQ-014` requires idempotence, however does not define any particular implementation of idempotency key storage. `REQ-007` and `REQ-015` REQ-015 require agreed performance thresholds, however numeric thresholds are environment-specific and could evolve independently from business requirements.

Detailed user interface layout, copy, visual design and provider internal behaviors are out of scope of this catalog unless they have material impact on any of defined business outcomes.