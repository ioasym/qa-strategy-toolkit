# Entry and Exit Criteria

## Entry criteria for system/regression testing

- Acceptance criteria are testable and major ambiguities are resolved.
- Target build is deployed and identified.
- Required test data and accounts exist.
- Critical dependencies are reachable or validly simulated.
- Blocking environment incidents are resolved.
- Change scope and known risks are documented.

## Exit criteria for release recommendation

- Critical-path automation passes or exceptions are explicitly understood.
- No open Critical defects unless accepted by accountable stakeholders.
- High-severity defects have documented impact and decision.
- Required exploratory testing is complete.
- Relevant performance/security checks meet agreed thresholds.
- Known limitations and residual risks are documented.
- Test evidence is attached to the release record.

## Release-gate decision model

The criteria above are interpreted through the risk- and change-aware gate model in [`../decisions/ADR-004-release-quality-gates.md`](../decisions/ADR-004-release-quality-gates.md). A global pass percentage does not override the relevance of a failed high-impact control, and an exception to a blocking gate requires explicit, visible risk disposition.
