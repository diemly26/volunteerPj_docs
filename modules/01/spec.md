# spec.md

## Module
| field | value |
|---|---|
| module | 01-identity-access |
| tier | STANDARD |
| reason | MUST enforce auth rules and token lifecycle with deterministic states |
| async | NO |

## Dependencies
| type | name | source | status |
|---|---|---|---|
| table | users | module 01 | AVAILABLE |
| enum | USER_STATUS | module 01 | AVAILABLE |

## Business Rules
- Registration MUST create `users.status = UNVERIFIED`.
- OTP verification MUST set `users.status = ACTIVE` when OTP is valid.
- Login MUST fail with `FORBIDDEN` IF `users.status != ACTIVE`.
- Refresh MUST rotate token and revoke previous token in same token family.
- Refresh token reuse MUST revoke full token family.
- Logout MUST revoke current refresh token only.
- Logout-all MUST revoke all active refresh tokens for user.

## Constraints
| concern | rule |
|---|---|
| Layering | Controller -> Service -> Repository only, no skip |
| Entity exposure | Entity NEVER returned from Service, always map to DTO |
| DTO separation | RequestDTO != ResponseDTO, always separate classes |
| Transaction scope | @Transactional at Service method level only |
| Async boundary | @Async methods in dedicated AsyncService class |
| External HTTP in TX | FORBIDDEN: always call external HTTP outside @Transactional |
| Cross-module call | Service calls another module's Service only, never Repository |
