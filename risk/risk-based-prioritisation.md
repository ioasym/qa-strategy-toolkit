# Risk-Based Prioritisation

Risk-based testing means spending the strongest evidence effort where failure would matter most and where the failure is plausibly exposed by the system or current change.

The process uses the scoring definitions in [`risk-assessment-model.md`](risk-assessment-model.md), but the score is only one input to the testing decision.

## Decision sequence

### 1. Identify the affected business capability

Start from the user/business outcome rather than the page or service name.

Example:

```text
Checkout service change
        ↓
CAP-04 — Checkout & Payment
```

### 2. Identify credible failure modes

Use the canonical `RISK-xxx` catalogue where possible. Add a new risk only when the change introduces a materially different business failure mode.

### 3. Assess impact and likelihood

Use the explicit 1–5 definitions. Record the rationale, not only the number.

```text
RISK-009 — duplicate charge after uncertain retry
Impact: 5 — duplicate financial charge
Likelihood: 3 — timeout + retry is a plausible distributed-system condition
Score: 15 — High
```

### 4. Determine change exposure

Ask whether the release actually touches the risk path:

- changed component/service
- changed dependency or contract
- changed retry/state logic
- changed capacity assumptions
- changed authorization boundary
- changed feature flag/configuration

A High risk outside the change scope may rely on stable regression evidence; a Medium risk directly modified by the release may need fresh blocking evidence.

### 5. Select the lowest useful test seam

Choose the cheapest layer that can prove the failure mode reliably.

| Failure characteristic | Preferred evidence |
|---|---|
| Pure calculation/business rule | Unit/component |
| Request/response rule or authorization matrix | API/service |
| Database, provider, message, or service boundary | Integration |
| Retry/timeout/concurrency behaviour | Integration/resilience |
| Customer-facing cross-system journey | Focused UI E2E |
| Capacity/latency | Performance |
| Unknown/novel interaction risk | Exploratory |

### 6. Add complementary controls only where they add confidence

High risk does **not** mean “write more UI tests.” For example, `RISK-008` needs payment/order integration, persistence, correlation, and a focused E2E confirmation because the failure spans several boundaries.

### 7. Decide release criticality explicitly

Use the risk band plus change context.

A check is a strong release-gate candidate when:

- the risk is Critical/High **and** the changed release exposes it
- the failure affects authorization, financial integrity, or the critical purchase path
- the release removes/changes a key preventive control
- the evidence is required by an explicit business/security commitment

A check may remain conditional when:

- the risk is Medium and the area is unchanged
- a stable lower-level suite already gives current evidence
- the failure is recoverable and does not compromise transaction/data integrity

### 8. Record residual risk

If evidence is incomplete or a known defect remains, record:

- affected `RISK-xxx`
- failed/missing evidence
- customer/business consequence
- mitigation/workaround
- accountable acceptance decision
- follow-up/review trigger

## Worked example — discount calculation

Suppose a release changes checkout discount rules.

```text
Change: discount calculation
        ↓
CAP-03 Basket + CAP-04 Checkout
        ↓
RISK-006 incorrect basket total (High)
RISK-003 stale/incorrect commercial state (High)
        ↓
Primary evidence:
component/API permutations
        ↓
Supporting evidence:
selected basket-to-checkout integration
one representative E2E journey
```

Repeating every discount permutation through a browser would add execution and maintenance cost without equivalent confidence.

## Worked example — notification template only

Suppose a release changes only the confirmation email template and not order creation/event delivery.

`RISK-015` remains Medium. Fresh evidence should focus on notification rendering/handoff. There is no reason to rerun expensive payment resilience suites *because of the score alone* if the relevant financial components are unchanged and their regression evidence remains valid.

## Prioritisation anti-patterns

Avoid:

- scoring every risk High to guarantee attention
- changing scores to justify a preselected test approach
- equating risk band with number of test cases
- using UI automation as the default treatment for High risks
- treating historical pass rate as proof that impact is low
- accepting residual risk without naming the affected business outcome
