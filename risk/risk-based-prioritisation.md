# Risk-Based Prioritisation

## Decision sequence

1. Identify the business capability affected by the change.
2. Identify credible failure modes.
3. Estimate impact and likelihood.
4. Select the lowest test layer that can detect each failure reliably.
5. Add UI coverage only for risks that require browser-level confidence.
6. Select exploratory charters for uncertainty that scripted checks may miss.
7. Revisit prioritisation after incidents or significant architecture changes.

## Example

A change to checkout discount calculation should usually receive broad component/API permutations. A small number of E2E journeys should confirm the discount is represented correctly to the customer and carried through checkout. Repeating every discount permutation through a browser would add execution cost without equivalent confidence.
