# Test Levels

## Unit / component

Use for deterministic business rules, validation, transformations, pricing logic, and error mapping. These checks should form the largest and fastest feedback layer.

## API / service

Use for:

- request/response contracts
- authentication/authorization behaviour
- positive/negative flows
- boundary values
- error semantics
- idempotency

## Integration

Use when correctness depends on real boundaries such as databases, message brokers, provider sandboxes, or service-to-service communication.

## UI end-to-end

Reserve for a compact set of journeys where browser behaviour itself matters. Avoid reproducing every data permutation at UI level.

## Exploratory testing

Apply especially to new features, complex interactions, usability concerns, ambiguous requirements, and areas with a history of escaped defects.

## Performance testing

Focus on customer-visible latency, throughput, saturation, degradation behaviour, and stability over time.
