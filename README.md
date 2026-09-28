# QA Strategy Toolkit

A practical Quality Engineering strategy and decision framework built around **ShopSphere**, a fictional distributed e-commerce platform. The repository demonstrates how business capabilities and requirements are translated into product risks, test levels, automation decisions, quality evidence, and release-readiness decisions.

> This repository contains original example material for demonstration and learning. It does not contain employer-confidential information, production credentials, or proprietary test assets.

## What this repository demonstrates

- Risk-based testing and prioritisation
- Test strategy design across test levels
- Automation candidate selection and ROI thinking
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
11. `metrics/release-readiness.md`

## Core principle

Testing effort should not be distributed evenly. It should follow **risk, change frequency, user impact, technical complexity, and feedback speed**.

The baseline prioritisation model used in this repository is:

```text
Risk score = Business impact × Failure likelihood
```

Impact and likelihood use explicit 1–5 definitions in `risk/risk-assessment-model.md`. The score is a decision aid, not a release decision. Security obligations, change scope, architecture changes, incidents, control gaps, and evidence quality can justify stronger treatment without manipulating the numeric score.

## How to make this repository your own

- Replace ShopSphere with another fictional domain such as travel, banking sandbox, logistics, or streaming.
- Add diagrams created from your own architecture assumptions.
- Add a small automated test repository and cross-link it from the automation strategy.
- Add anonymised examples only when you own the material and have permission to publish it.
- Add short decision records that explain *why* you selected particular test levels and metrics.

## Engineering discussion points

The case study is designed to make the following decisions explicit:

- How you identify product risk before choosing test cases
- Why some checks belong at API or component level instead of UI
- How you decide what to automate
- What signals you use before recommending a release
- How you handle flaky automation and escaped defects
- How quality information should influence engineering decisions

## License

MIT. See `LICENSE`.
