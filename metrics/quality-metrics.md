# Quality Metrics

Metrics should answer a decision question. Avoid collecting numbers only because tools make them available.

## Useful examples

### Escaped defect rate

```text
Escaped defects / total confirmed defects
```

Interpret by severity and area; a raw percentage alone can hide important context.

### Flaky automation rate

```text
Non-product intermittent failures / automated executions
```

Track trend and top offending tests.

### Critical-risk coverage

Percentage of identified Critical/High risks with explicit test controls.

### Mean defect age

Useful for understanding whether serious defects remain unresolved for long periods.

### Pipeline feedback time

How quickly engineers receive trustworthy test feedback after a change.

## Metrics to use carefully

- raw test-case count
- raw automation percentage
- defects found per tester
- pass rate without risk context

These can encourage the wrong behaviour when treated as targets.
