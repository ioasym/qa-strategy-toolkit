# ShopSphere – Business Risks

The business risk arises from the interaction between the five capabilities of ShopSphere and not from individual test cases. Each of the risks described below defines a possible failure mode that prevents the accomplishment of a particular capability in terms of the desired business outcome.

## Risk Themes

### Revenue Loss

Problems in checkout, payment or order creation may prevent successful completion of purchases or require manual resolution.

### Incorrect Financial or Order State

Duplication of payments, duplication of order creation or inconsistency between order and payment state might generate immediate impact for the customer or the process of reconciliation.

### Unauthorized Access

Security problems such as defects in the implementation of authentication or authorization may expose customer specific data about accounts and orders.

### Data Consistency

The prices presented in the catalogue, the basket total, payment results and the order data stored need to be consistent between services.

### Customer Trust

False checkout status, missing orders and wrong confirmation messages might reduce the customer's trust, despite the correct processing of the transaction in the backend.

### Availability & Performance

Latency or unavailability of search or checkout services may negatively impact the conversion rates and the ability to make purchases during the peak traffic period.

### Operational Support Costs

Lack of observability and cross-service system failures may increase the time needed for diagnostics and transform an easily diagnosable technical problem in a prolonged outage for the customers.

## Risk Catalog Based on Capabilities

### CAP-01 – Authentication and Account Access

| Risk ID | Failure Mode | Impact | Likelihood | Initial Quality Response |
|---:|---:|---:|---:|---|
| **RISK-001** | Customer has access to other customers' order or account data | 5 | 2 | Tests for API focused on authorization, negative access checks and security review|
| **RISK-002** | Valid customer cannot establish or maintain session | 4 | 3 | Auth API test coverage, session management and targeted UI end-to-end tests|

### CAP-02 – Product Discovery

| Risk ID | Failure Mode | Impact | Likelihood | Initial Quality Response |
|---:|---:|---:|---:|---|
| **RISK-003** | Catalogue shows outdated or wrong price or availability information | 4 | 3 | Contract/integration checks, basket data validation|
| **RISK-004** | Search, filtering, sorting operations return inaccurate or incomplete results | 3 | 3 | API permutations with UI tests coverage|
| **RISK-005** | Search latency is increased during the peak load period | 3 | 3 | API performance tests with compliance to operational SLA|


### CAP-03 – Basket Management

| Risk ID | Failure Mode | Impact | Likelihood | Initial Quality Response |
|---|---|---:|---:|---|
| **RISK-006** | Basket total is calculated incorrectly | 5 | 2 | Component/API boundary tests, price/quantity permutations |
| **RISK-007** | Basket state is lost or corrupted before checkout | 4 | 3 | API state-transition checks, integration coverage, targeted E2E |

### CAP-04 – Checkout and Payment

| Risk ID | Failure Mode | Impact | Likelihood | Initial Quality Response |
|---:|---:|---:|---:|---|
| **RISK-008** | Payment succeeds but ShopSphere is unable to create the order | 5 | 3 | Payment/order integration tests, reconciliation evidence and observability |
| **RISK-009** | Repeat after uncertain payment result causes duplicate payment | 5 | 3 | Idempotency, timeout simulation, repeat-request tests |
| **RISK-010** | Checkout is inaccessible or slow under peak load conditions | 5 | 3 | Load/performance tests, capacity thresholds and resilience checks |
| **RISK-011** | Declined or failed payment is confirmed as successful or ambiguous | 5 | 2 | Provider result mapping tests, API plus targeted end-to-end negative paths |


### CAP-05 – Order Fulfillment and Confirmation

| Risk ID | Failure Mode | Impact | Likelihood | Initial Quality Response |
|---:|---:|---:|---:|---|
| **RISK-012** | Successful checkout results in creation of several orders | 5 | 3 | Idempotent order creation, concurrency/retry integration tests |
| **RISK-013** | Order is not persisted and becomes not visible to the owning customer | 4 | 2 | Integration of order read models and history end-to-end | 
| **RISK-014** | Duplicate order-created event causes redundant downstream processing | 4 | 2 | Consumer idempotency, duplicate message tests | 
| **RISK-015** | Confirmation notification is late or absent | 2 | 3 | Asynchronous integration tests, retry/dead-letter observability |

## Highest-priority risk chain

The main quality scenario for ShopSphere is not the response of an individual page or an API call; the problem of integrity of the financial workflow:

```text
Customer performs checkout
        ↓
Payment provider processes the request
        ↓
ShopSphere receives approved / declined / uncertain result
        ↓
A valid order state is created exactly once
        ↓
The order stays available to be accessed by the correct customer
```

The most significant risks occur if there is inconsistency between the result of the payment and the durable order state created by ShopSphere.

### Example: RISK-008 - payment succeeds but order creation fails

```text
CAP-04 Checkout and Payment
        ↓
RISK-008
Payment succeeds but order creation fails
        ↓
Impact: 5
Likelihood: 3
Initial score: 15
        ↓
Controls
- Component: order-state transition rules
- API: checkout response/error semantics
- Integration: payment approval plus persistence
- Resilience: provider timeout or uncertain result
- Observability: payment/order correlation and reconciliation signal
- E2E: successful purchase and visible order confirmation
```

In this case we have an example of the primary principle used in this repository: **a high business risk needs multiple complementary controls rather than one comprehensive end-to-end test.**

## Cross-capability risks

There are some failures affecting more than one capability:

| Cross-capability concern | Capabilities affected | Reason |
|---|---|---|
| Differences in prices between catalog and checkout | CAP-02, CAP-03, CAP-04 | The customer sees one price while paying another amount |
| Session expires during checkout | CAP-01, CAP-04 | A valid purchase may fail or retry incorrectly |
| Payment approved but no event publication | CAP-04, CAP-05 | Order/notification state may be inconsistent |
| Slowdown of downstream dependencies under peak load | CAP-02, CAP-03, CAP-04 | Degradation spreads through the purchase process |

## Risk Ownership

While the numeric score is useful for prioritization of evidence gathering, it doesn't delegate ownership for the problem solving to quality assurance alone. Risk identification and mitigation require contributions from multiple areas: product, engineering, security, operations and quality assurance. Explicit understanding of residual business risk should be maintained by release owners.
