# Automation Strategy

## Objectives

Automation exists to provide **fast, repeatable evidence**, not to maximise the number of automated scripts.

## Principles

- Test behaviour at the lowest useful level.
- Keep E2E coverage intentionally small.
- Prefer APIs for test setup and cleanup.
- Make failures diagnosable.
- Run high-signal checks frequently.
- Track flaky tests as quality debt.
- Remove or refactor low-value tests.

## Pipeline model

```text
Commit
  -> unit/component
  -> API/service
  -> selected integration
  -> deploy to QA
  -> critical UI E2E
  -> exploratory / change-specific checks
  -> pre-prod release pack
```

## Automation quality criteria

A candidate automated test should have:

- stable preconditions
- clear expected outcome
- repeatable data
- suitable execution environment
- maintainable ownership
- a failure signal someone will act on

## Failure artifacts

For UI automation capture where useful:

- trace
- screenshot
- video (selectively)
- console/network diagnostics
- test input identifier
- environment/build metadata

## Flaky test policy

1. Confirm whether the product or test is faulty.
2. Quarantine only when necessary to preserve pipeline trust.
3. Create a visible defect/work item.
4. Fix promptly.
5. Remove quarantine after repeated stable runs.

Retry should not be used to hide unknown failures.
