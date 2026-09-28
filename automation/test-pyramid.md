# Test Pyramid / Test Portfolio

A useful portfolio has many fast checks and fewer expensive cross-system checks.

```text
             UI E2E
           /        \
        Integration
      /              \
          API/Service
    /                  \
        Unit/Component
```

The shape is guidance rather than a quota. Architecture and risk determine the actual distribution.

## Anti-patterns

- Repeating all API permutations through the UI
- Using E2E tests to diagnose low-level business logic
- Building automation that depends on one shared mutable account
- Keeping unreliable tests in the blocking pipeline indefinitely
