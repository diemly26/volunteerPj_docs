# api.md

## Endpoint List
| method | path | purpose |
|---|---|---|
| POST | /api/v1/organizations | Submit organization profile |
| GET | /api/v1/organizations/{id} | Read organization detail |
| POST | /api/v1/admin/organizations/{id}/approve | Approve profile |
| POST | /api/v1/admin/organizations/{id}/reject | Reject profile |
| POST | /api/v1/admin/organizations/{id}/suspend | Suspend organization |
| PUT | /api/v1/admin/organizations/{id}/trusted | Toggle trusted flag |

## Payload Source
- Request and response fields MUST reference `schema.md`.
- Status transitions MUST reference `schema.md -> organizations.status`.

## Minimal JSON
### POST /api/v1/organizations
```json
{"legalName":"Org A","taxCode":"0123456789","representativeName":"Rep A","contactEmail":"org@example.com"}
```

### POST /api/v1/admin/organizations/{id}/reject
```json
{"reason":"INVALID_DOCUMENT"}
```
