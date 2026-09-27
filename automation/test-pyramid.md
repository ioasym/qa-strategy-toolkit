# Test Pyramid / Layered Test Strategy

An effective test portfolio consists of more frequent low-cost test runs and fewer high-cost, across-the-system test runs.

```text
             UI E2E
           /        \
          Integration
        /              \
          API/Service
      /                  \
         Unit/Component
```

The described shape should be taken as a recommendation rather than an absolute guideline, because the true proportions depend on the architecture and the corresponding risks involved.

## Anti-patterns

- Using the UI to cover all possible combinations of API calls
- Using end-to-end tests to debug business logic details
- Implementing automation tests using a single mutable account
- Keeping failing tests in the blocking pipeline forever
