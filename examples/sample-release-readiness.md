# Sample Release Readiness Record

## Release candidate
`shopsphere-2026.09.1-rc2`

## Evidence reviewed

- Critical customer journeys: passed
- Payment decline and retry scenarios: passed
- Order idempotency integration suite: passed
- Checkout performance p95: 420 ms against 500 ms threshold
- Exploratory checkout charter: complete

## Open defects

Two High-severity issues remain:

1. Order-history refresh can be delayed after confirmation. Workaround: refresh after several seconds.
2. Discount banner can display stale text after changing locale. Calculated price remains correct.

## Residual risk

The first issue can create temporary confusion but does not lose or duplicate an order. The second is presentation-only. Both should have accountable acceptance or a fix decision before release.

## Follow-up

- Verify order-history event latency monitoring after deployment.
- Add automated locale-switch coverage below UI where possible.
