# api.md

## Endpoint List
| method | path | purpose |
|---|---|---|
| POST | /api/v1/projects/{id}/join-requests | Create join request |
| GET | /api/v1/projects/{id}/join-requests?status= | List requests by status |
| POST | /api/v1/projects/{id}/join-requests/{requestId}/approve | Approve request |
| POST | /api/v1/projects/{id}/join-requests/{requestId}/reject | Reject request |
| POST | /api/v1/projects/{id}/join-requests/{requestId}/withdraw | Withdraw own request |
| PUT | /api/v1/projects/{id}/members/{memberId}/role | Change member role |

## Payload Source
- Fields MUST reference `schema.md -> project_join_requests`.
- Role transitions MUST reference `schema.md -> project_members.role`.

## Minimal JSON
### POST /api/v1/projects/{id}/join-requests
```json
{"message":"I can support logistics"}
```

### PUT /api/v1/projects/{id}/members/{memberId}/role
```json
{"role":"SUB_MANAGER","reason":"OPERATION_NEED"}
```
