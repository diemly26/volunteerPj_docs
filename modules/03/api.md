# api.md

## Endpoint List
| method | path | purpose |
|---|---|---|
| POST | /api/v1/projects | Create project draft |
| PUT | /api/v1/projects/{id} | Update project |
| GET | /api/v1/projects/{id} | Read project detail |
| GET | /api/v1/projects/{id}/history | Read edit history |
| POST | /api/v1/admin/projects/{id}/approve | Approve project |
| POST | /api/v1/admin/projects/{id}/suspend | Suspend project |

## Payload Source
- Field definitions MUST reference `schema.md -> projects`.
- History payload MUST reference `schema.md -> project_edit_history`.

## Minimal JSON
### POST /api/v1/projects
```json
{"organizationId":10,"type":"MONEY_DONATION","title":"Project A","description":"desc","fundraisingTargetMinor":1000000,"currency":"VND","startAt":"2026-01-01T00:00:00Z","endAt":"2026-02-01T00:00:00Z"}
```

### POST /api/v1/admin/projects/{id}/approve
```json
{"reason":"PASSED_REVIEW"}
```
