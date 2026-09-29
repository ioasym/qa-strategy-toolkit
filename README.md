# QA Strategy Toolkit

A practical Quality Engineering strategy and decision framework built around **ShopSphere**, a fictional distributed e-commerce platform. The model maps business capabilities and requirements to product risks, test levels, automation decisions, quality evidence, and release-readiness decisions.

> **Scope note:** ShopSphere and all related examples are synthetic. The repository contains no employer/client code, production credentials, production data, or proprietary test assets.

## Coverage

- Risk-based testing and prioritisation
- Test strategy design across test levels
- Automation candidate selection and ROI thinking
- Quality Engineering decision records and trade-off documentation
- Entry / exit criteria and release readiness
- Defect triage and root-cause analysis
- Quality metrics that support decisions
- Practical templates that can be reused in real projects
- Communication suitable for engineering and business stakeholders

## Case study

**ShopSphere** is a fictional distributed e-commerce platform organised around five business capabilities:

- **CAP-01 — Authentication & Account Access**
- **CAP-02 — Product Discovery**
- **CAP-03 — Basket Management**
- **CAP-04 — Checkout & Payment**
- **CAP-05 — Order Fulfilment & Confirmation**

The case study derives testable requirements and quality risks from these capabilities, then traces them through test levels, automation choices, resilience testing, observability, and release evidence.

## Repository map

```text
qa-strategy-toolkit/
├── case-study/
│   ├── system-overview.md
│   ├── business-capabilities.md
│   ├── requirements.md
│   ├── architecture.md
│   └── business-risks.md
├── strategy/
│   ├── test-strategy.md
│   ├── test-levels.md
│   ├── test-environments.md
│   └── entry-exit-criteria.md
├── risk/
│   ├── risk-assessment-model.md
│   ├── product-risk-analysis.md
│   ├── risk-matrix.md
│   ├── risk-based-prioritisation.md
│   └── risk-to-test-traceability.md
├── automation/
│   ├── automation-strategy.md
│   ├── automation-candidate-selection.md
│   └── test-pyramid.md
├── decisions/
│   ├── README.md
│   ├── ADR-001-ui-automation-scope.md
│   ├── ADR-002-api-first-test-data.md
│   ├── ADR-003-flaky-test-policy.md
│   └── ADR-004-release-quality-gates.md
├── defects/
│   ├── defect-triage-process.md
│   ├── severity-vs-priority.md
│   └── root-cause-analysis.md
├── metrics/
│   ├── qa-dashboard.md
│   ├── quality-metrics.md
│   └── release-readiness.md
├── templates/
│   ├── test-plan-template.md
│   ├── test-closure-template.md
│   ├── risk-register-template.md
│   └── release-checklist.md
├── examples/
│   ├── sample-risk-register.csv
│   └── sample-release-readiness.md
└── .github/workflows/markdown-check.yml
```

## Suggested reading order

1. `case-study/system-overview.md`
2. `case-study/business-capabilities.md`
3. `case-study/requirements.md`
4. `case-study/architecture.md`
5. `case-study/business-risks.md`
6. `risk/risk-assessment-model.md`
7. `risk/product-risk-analysis.md`
8. `risk/risk-to-test-traceability.md`
9. `strategy/test-strategy.md`
10. `automation/automation-strategy.md`
11. `decisions/README.md`
12. `decisions/ADR-001-ui-automation-scope.md`
13. `decisions/ADR-004-release-quality-gates.md`
14. `metrics/release-readiness.md`

## Core principle

Testing effort should not be distributed evenly. It should follow **risk, change frequency, user impact, technical complexity, and feedback speed**.

The baseline prioritisation model used in this repository is:

```text
Risk score = Business impact × Failure likelihood
```

Impact and likelihood use explicit 1–5 definitions in `risk/risk-assessment-model.md`. The score is a decision aid, not a release decision. Security obligations, change scope, architecture changes, incidents, control gaps, and evidence quality can justify stronger treatment without manipulating the numeric score.

## Design considerations

The strategy makes these trade-offs explicit:

- Product risk is identified before selecting test cases.
- Component/API checks are preferred when browser execution adds no useful confidence.
- Automation candidates are selected on repeatability, risk coverage, maintenance cost, and feedback value.
- Release recommendations are based on evidence and residual risk rather than test-count or pass-rate targets.
- Flaky automation is treated as quality-system debt because it weakens confidence in release evidence.
- Technical test decisions are recorded when the trade-off should remain reviewable over time.

## License

MIT. See `LICENSE`.
