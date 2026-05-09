# spec.md

## Module
| field | value |
|---|---|
| module | 04-participation |
| tier | STANDARD |
| reason | MUST enforce join request states and role constraints |
| async | NO |

## Dependencies
| type | name | source | status |
|---|---|---|---|
| table | users | module 01 | AVAILABLE |
| table | projects | module 03 | AVAILABLE |
| enum | PROJECT_STATUS | module 03 | AVAILABLE |
| table | project_members | module 04 | AVAILABLE |

## Business Rules
- Join request create MUST require `projects.status = ACTIVE`.
- Join request create MUST require `users.status = ACTIVE`.
- Approve request MUST create `project_members` with role `VOLUNTEER`.
- Reject request MUST set reason and `project_join_requests.status = REJECTED`.
- Withdraw request MUST be allowed only when status is `PENDING`.
- Role change MUST append `project_member_role_history`.
- Manager role MUST be unique per project.

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
