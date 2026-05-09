# spec.md

## Module
| field | value |
|---|---|
| module | 03-project-lifecycle |
| tier | STANDARD |
| reason | MUST enforce project lifecycle and funding-stop transitions |
| async | YES |

## Dependencies
| type | name | source | status |
|---|---|---|---|
| table | users | module 01 | AVAILABLE |
| table | organizations | module 02 | AVAILABLE |
| enum | ORG_STATUS | module 02 | AVAILABLE |
| table | projects | module 03 | AVAILABLE |

## Business Rules
- Project create MUST require `organizations.status = APPROVED`.
- Project submit MUST set `projects.status = PENDING_REVIEW`.
- Project approve MUST set `projects.status = APPROVED`.
- Project activate MUST set `projects.status = ACTIVE`.
- Funding stop MUST set `projects.status = DISBURSEMENT_DUE` IF target reached.
- Funding stop MUST set `projects.status = DISBURSEMENT_DUE` IF `end_at < now`.
- Edit of sensitive fields MUST append `project_edit_history` and require re-review.

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
