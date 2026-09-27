# Automation Strategy

## Objectives

Automation is designed to provide quick and repeatable proof, rather than to produce large number of automated scripts.

## Principles

- Analyze behavior on the lowest level of its usefulness.
- Provide deliberately limited end-to-end (E2E) coverage.
- Prefer APIs for tests setup and teardown.
- Make failures diagnosable.
- Run high-signal checks frequently.
- Document flaky tests as quality debt.
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

## Quality criteria for automated tests

Candidate for automation should have:

- stable preconditions
- clear and understandable expected result
- repeatable data
- proper testing environment
- maintainable owner
- failure signal for taking action

## Artifacts to gather for failures

In case of UI automation collect where needed:

- trace
- screenshot
- video (optional)
- diagnostics from console/communication
- input id for the test
- environment/build info

## Policy for flaky tests

1. Determine the problem is with the product itself or with the test.
2. Quarantine only when it is required to keep the pipeline integrity.
3. Report defect/work item.
4. Fix problem quickly.
5. Unquarantine after several successes.

Do not use retries to hide failures.
