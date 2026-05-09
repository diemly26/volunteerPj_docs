# spec.md

## Module
| field | value |
|---|---|
| module | 02-organization-onboarding |
| tier | STANDARD |
| reason | MUST enforce approval workflow and legal traceability |
| async | NO |

## Dependencies
| type | name | source | status |
|---|---|---|---|
| table | users | module 01 | AVAILABLE |
| enum | USER_STATUS | module 01 | AVAILABLE |
| table | organizations | module 02 | AVAILABLE |

## Business Rules
- Organization create MUST require owner with `users.status = ACTIVE`.
- Organization create MUST set `organizations.status = PENDING_REVIEW`.
- Approve action MUST set `organizations.status = APPROVED`.
- Reject action MUST set `organizations.status = REJECTED` and store reason.
- Suspend action MUST set `organizations.status = SUSPENDED`.
- Trusted flag change MUST create `organization_reviews` record.

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
