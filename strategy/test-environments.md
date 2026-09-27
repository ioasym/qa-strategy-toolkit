# Test Environment Strategy

## Principles

1. Each environment has a defined purpose.
2. Environment-specific configuration is versioned where possible.
3. Tests fail clearly when required dependencies are unavailable.
4. Test data is synthetic and disposable.
5. Production secrets are never reused in test environments.

## Example environment matrix

| Capability | Integration | QA | Pre-prod |
|---|---:|---:|---:|
| Real database engine | Yes | Yes | Yes |
| Payment sandbox | Stub/sandbox | Sandbox | Sandbox |
| Email delivery | Captured sink | Captured sink | Controlled sandbox |
| Browser E2E | Limited | Full | Release pack |
| Load testing | No | Limited | Controlled window |

## Drift controls

Record:

- deployed version/commit
- database schema version
- feature flags
- provider sandbox version/state
- browser versions
- test framework version
