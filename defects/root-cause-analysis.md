# Root-Cause Analysis

The goal is not to assign blame. It is to reduce recurrence.

## Suggested structure

### Problem statement
What happened, where, and who/what was affected?

### Detection
How was the issue detected? Why was it not detected earlier?

### Technical cause
What condition directly produced the failure?

### Contributing factors
Examples:

- ambiguous requirement
- missing test seam
- unsafe retry behaviour
- weak observability
- environment drift
- absent negative-path coverage

### Corrective action
Fix the immediate defect.

### Preventive action
Improve architecture, tests, monitoring, review, or process so similar failures are less likely.

### Verification
Define evidence that verifies the preventive action works.
