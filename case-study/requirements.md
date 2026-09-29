# ShopSphere — Requirements Catalogue

This catalogue translates the five ShopSphere business capabilities into a small set of testable requirements. The requirements are intentionally written at business/system level rather than as detailed UI specifications so that they remain stable when implementation details change.

Each requirement has a unique identifier (`REQ-xxx`) and is linked to one or more risks in `../risk/risk-to-test-traceability.md`.

## Requirement-writing principles

The catalogue uses four rules:

1. A requirement should describe an observable business or system outcome.
2. It should avoid prescribing a test level or automation tool.
3. It should be specific enough to support evidence and risk analysis.
4. Unknown implementation detail should remain an assumption or design decision rather than being silently added to the requirement.

---

## CAP-01 — Authentication & Account Access

| Requirement ID | Requirement | Evidence of satisfaction |
|---|---|---|
| **REQ-001** | A registered customer with valid credentials can establish an authenticated session. | Authentication succeeds and the customer can access authenticated account functions. |
| **REQ-002** | Invalid credentials must not establish an authenticated session. | Authentication is rejected without creating an authenticated session. |
| **REQ-003** | An authenticated customer must be able to access only account and order data they are authorised to view. | Requests for another customer's protected resources are denied. |
| **REQ-004** | Session expiry or sign-out must remove access that depends on the previous authenticated session. | Protected actions fail or require re-authentication after expiry/sign-out. |

### Primary risk links

- `RISK-001` — unauthorised access to another customer's data
- `RISK-002` — legitimate customer cannot establish or maintain a valid session

---

## CAP-02 — Product Discovery

| Requirement ID | Requirement | Evidence of satisfaction |
|---|---|---|
| **REQ-005** | Customers can search or browse the catalogue and receive products that match the requested criteria. | Search/filter/sort responses are correct for representative and boundary conditions. |
| **REQ-006** | Product price and availability presented for purchase must reflect the catalogue state accepted by downstream basket/checkout processing. | Catalogue, basket, and checkout do not silently disagree on the accepted commercial state. |
| **REQ-007** | Product discovery must remain responsive within the agreed service threshold under representative expected demand. | Measured latency and error rate remain within the defined threshold for the tested load profile. |

### Primary risk links

- `RISK-003` — stale or incorrect price/availability
- `RISK-004` — incorrect or incomplete search/filter/sort results
- `RISK-005` — degraded search latency under expected peak demand

---

## CAP-03 — Basket Management

| Requirement ID | Requirement | Evidence of satisfaction |
|---|---|---|
| **REQ-008** | Customers can add, update, and remove valid basket items and quantities. | Basket state changes match the requested operation and invalid state transitions are rejected. |
| **REQ-009** | Basket totals must be calculated from the accepted items, quantities, prices, and applicable commercial rules. | Component/API calculations match expected totals across representative and boundary cases. |
| **REQ-010** | Basket state must remain consistent through normal navigation and until checkout consumes or invalidates it. | The basket can be re-read without unexpected loss, duplication, or corruption. |

### Primary risk links

- `RISK-006` — incorrect basket total
- `RISK-007` — lost or corrupted basket state before checkout

---

## CAP-04 — Checkout & Payment

| Requirement ID | Requirement | Evidence of satisfaction |
|---|---|---|
| **REQ-011** | Checkout must validate that the submitted basket and required purchase data are in an acceptable state before attempting payment. | Invalid checkout requests are rejected before an invalid order/payment state is produced. |
| **REQ-012** | An approved payment must result in one durable ShopSphere order that references the accepted payment outcome. | Payment and order state can be correlated and reconciled for the same transaction. |
| **REQ-013** | A declined payment must not be represented as a successful purchase and must not produce a completed order. | Decline response, customer-visible outcome, and stored order state are consistent. |
| **REQ-014** | Retrying a checkout after an uncertain payment result must not create a duplicate charge or duplicate completed purchase. | Repeated requests with the same logical transaction remain idempotent across payment and order boundaries. |
| **REQ-015** | Checkout must remain available and responsive within agreed service thresholds under the defined peak-load profile. | Checkout latency, throughput, and error rate remain within the agreed thresholds for the tested profile. |

### Primary risk links

- `RISK-008` — payment succeeds but no durable order is created
- `RISK-009` — retry creates a duplicate charge
- `RISK-010` — checkout unavailable or too slow under peak demand
- `RISK-011` — declined/failed payment reported incorrectly

---

## CAP-05 — Order Fulfilment & Confirmation

| Requirement ID | Requirement | Evidence of satisfaction |
|---|---|---|
| **REQ-016** | One successful logical checkout must create no more than one durable order. | Duplicate requests/retries do not produce duplicate orders. |
| **REQ-017** | The owning customer can retrieve a successfully created order from order history within the defined consistency window. | Persisted order becomes visible to the correct authenticated customer. |
| **REQ-018** | Order-created events and downstream consumers must tolerate duplicate delivery without producing duplicate business effects. | Repeated event delivery does not duplicate notifications or other protected downstream actions. |
| **REQ-019** | Failure or delay of the asynchronous confirmation notification must not invalidate an otherwise successful order. | Order remains valid/retrievable even when notification processing is delayed or retried. |

### Primary risk links

- `RISK-012` — duplicate order creation
- `RISK-013` — stored order not visible to owning customer
- `RISK-014` — duplicate event causes repeated downstream processing
- `RISK-015` — delayed or missing confirmation notification

---

## Cross-capability requirements

Some outcomes require more than one capability to remain consistent.

| Requirement ID | Cross-capability requirement | Capabilities |
|---|---|---|
| **REQ-020** | The commercial state accepted at checkout must be traceable to the basket and order so that unexpected price/quantity changes are detectable. | CAP-02, CAP-03, CAP-04, CAP-05 |
| **REQ-021** | One customer transaction must be traceable across checkout, payment, order persistence, and asynchronous processing using a stable correlation mechanism. | CAP-04, CAP-05 |
| **REQ-022** | A session interruption during checkout must not allow an unauthorised continuation or cause an uncontrolled duplicate transaction when the customer retries safely. | CAP-01, CAP-04 |

## Requirement scope notes

These requirements are deliberately implementation-neutral. For example, `REQ-014` requires idempotent behaviour but does not prescribe the exact idempotency-key storage mechanism. Similarly, `REQ-007` and `REQ-015` require agreed performance thresholds, but the numeric thresholds are release/environment criteria and can evolve independently of the business requirement.

Detailed UI layout, copy, visual design, and provider-internal behaviour are outside this catalogue unless they materially affect one of the defined business outcomes.
