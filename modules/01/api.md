# api.md

## Endpoint List
| method | path | purpose |
|---|---|---|
| POST | /api/v1/auth/register | Create unverified user |
| POST | /api/v1/auth/verify-email | Verify OTP |
| POST | /api/v1/auth/login | Issue access/refresh tokens |
| POST | /api/v1/auth/refresh-token | Rotate refresh token |
| POST | /api/v1/auth/logout | Revoke current refresh token |
| GET | /api/v1/auth/me | Read current profile |

## Payload Source
- Request/response fields MUST reference `schema.md` tables.
- Auth status checks MUST reference `schema.md -> users.status`.

## Minimal JSON
### POST /api/v1/auth/register
```json
{"email":"user@example.com","password":"Aa1!aaaaaa","fullName":"User A"}
```

### POST /api/v1/auth/login
```json
{"email":"user@example.com","password":"Aa1!aaaaaa"}
```
