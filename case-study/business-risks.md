# ShopSphere — Business Risks

Business risk is derived from the five ShopSphere capabilities rather than from isolated test cases. Each risk describes a failure mode that could prevent a capability from achieving its business outcome.

## Risk themes

### Revenue loss

Checkout, payment, or order-creation failures can prevent completed purchases or require manual recovery.

### Incorrect financial or order state

Duplicate payment, duplicate order creation, or disagreement between payment and order state can create direct customer and reconciliation impact.

### Unauthorized access

Authentication or authorization defects can expose customer-specific account or order information.

### Data integrity

Catalogue price, basket totals, payment result, and stored order details must remain consistent across service boundaries.

### Customer trust

Misleading checkout status, missing orders, or incorrect confirmations can reduce trust even when backend processing eventually succeeds.

### Availability and performance

Slow or unavailable search/checkout paths can reduce conversion and block purchases during important traffic periods.

### Operational support cost

Poor observability and intermittent cross-system failures increase diagnosis time and can turn a recoverable technical issue into a prolonged customer-facing incident.

## Capability-based risk catalogue

### CAP-01 — Authentication & Account Access

| Risk ID | Failure mode | Impact | Likelihood | Score | Band | Initial quality response |
|---|---|---:|---:|---:|---|---|
| **RISK-001** | Customer can access another customer's order/account data | 5 | 2 | 10 | High | Authorization-focused API tests, negative access checks, security review |
| **RISK-002** | Legitimate customer cannot establish or maintain a valid session | 4 | 3 | 12 | High | Auth API coverage, session lifecycle tests, focused UI E2E |

### CAP-02 — Product Discovery

| Risk ID | Failure mode | Impact | Likelihood | Score | Band | Initial quality response |
|---|---|---:|---:|---:|---|---|
| **RISK-003** | Catalogue presents stale or incorrect price/availability | 4 | 3 | 12 | High | Contract/integration checks, consistency checks with basket |
| **RISK-004** | Search/filter/sort returns incorrect or incomplete results | 3 | 3 | 9 | Medium | API permutations plus representative UI coverage |
| **RISK-005** | Search latency degrades under expected peak demand | 3 | 3 | 9 | Medium | API performance testing and operational thresholds |

### CAP-03 — Basket Management

| Risk ID | Failure mode | Impact | Likelihood | Score | Band | Initial quality response |
|---|---|---:|---:|---:|---|---|
| **RISK-006** | Basket total is calculated incorrectly | 5 | 2 | 10 | High | Component/API boundary tests, price/quantity permutations |
| **RISK-007** | Basket state is lost or corrupted before checkout | 4 | 3 | 12 | High | API state-transition checks, integration coverage, targeted E2E |

### CAP-04 — Checkout & Payment

| Risk ID | Failure mode | Impact | Likelihood | Score | Band | Initial quality response |
|---|---|---:|---:|---:|---|---|
| **RISK-008** | Payment succeeds but ShopSphere does not create a durable order | 5 | 3 | 15 | High | Payment/order integration tests, reconciliation evidence, observability |
| **RISK-009** | Retry after an uncertain provider result creates a duplicate charge | 5 | 3 | 15 | High | Idempotency tests, timeout simulation, repeat-request tests |
| **RISK-010** | Checkout is unavailable or too slow under peak demand | 5 | 3 | 15 | High | Load/performance tests, capacity thresholds, resilience checks |
| **RISK-011** | Declined/failed payment is reported as successful or ambiguously | 5 | 2 | 10 | High | Provider-response mapping tests, API + focused E2E negative paths |

### CAP-05 — Order Fulfilment & Confirmation

| Risk ID | Failure mode | Impact | Likelihood | Score | Band | Initial quality response |
|---|---|---:|---:|---:|---|---|
| **RISK-012** | Same successful checkout creates more than one order | 5 | 3 | 15 | High | Idempotent order creation, concurrency/retry integration tests |
| **RISK-013** | Order is stored but is not visible to the owning customer | 4 | 2 | 8 | Medium | Persistence/read-model integration checks, order-history E2E |
| **RISK-014** | Duplicate order-created event causes repeated downstream processing | 4 | 2 | 8 | Medium | Consumer idempotency and duplicate-message tests |
| **RISK-015** | Confirmation notification is delayed or missing | 2 | 3 | 6 | Medium | Async integration tests, retry/dead-letter observability |

## Financial-consistency risk chain

One of the most consequential ShopSphere quality scenarios is not a single page or API response. It is the financial-consistency chain:

```text
Customer submits checkout
        ↓
Payment provider processes request
        ↓
ShopSphere receives approved / declined / uncertain result
        ↓
Exactly one valid order state is produced
        ↓
Order remains retrievable by the correct customer
```

The most severe risks appear when the payment result and ShopSphere's durable order state disagree.

### Example: RISK-008 — payment succeeds but order is not created

```text
CAP-04 Checkout & Payment
        ↓
RISK-008
Payment succeeds but order creation fails
        ↓
Impact: 5
Likelihood: 3
Initial score: 15
        ↓
Controls
- component: order-state transition rules
- API: checkout response/error semantics
- integration: payment approval + persistence
- resilience: provider timeout/uncertain result
- observability: payment/order correlation + reconciliation signal
- E2E: successful purchase and visible order confirmation
```

RISK-008 is protected by several complementary controls because no single end-to-end test can cover the relevant failure modes with the same speed and diagnostic value.

## Cross-capability risks

Some failures span more than one capability:

| Cross-capability concern | Capabilities affected | Why it matters |
|---|---|---|
| Price changes between catalogue and checkout | CAP-02, CAP-03, CAP-04 | customer sees one value but pays another |
| Session expires during checkout | CAP-01, CAP-04 | valid purchase may be interrupted or retried incorrectly |
| Payment approved but event publication fails | CAP-04, CAP-05 | order/notification state can diverge |
| Slow dependencies during peak traffic | CAP-02, CAP-03, CAP-04 | degradation compounds across the purchase journey |

## Risk scoring and ownership

Impact, likelihood, score, and treatment definitions are maintained in [`../risk/risk-assessment-model.md`](../risk/risk-assessment-model.md). The numerical score helps prioritise evidence, but it does not transfer accountability to QA. Product, engineering, security, operations, and quality roles contribute to risk identification and mitigation. Residual business risk should be explicitly understood by the accountable release stakeholders.

## Traceability

The risk catalogue is connected to explicit ShopSphere requirements and test evidence in [`../risk/risk-to-test-traceability.md`](../risk/risk-to-test-traceability.md). That matrix is the canonical place to see the selected test seam, automation decision, and release-critical status for each `RISK-xxx` item.
