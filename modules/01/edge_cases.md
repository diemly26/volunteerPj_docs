# edge_cases.md

## Cases
| id | case | rule | error_code |
|---|---|---|---|
| EC-01 | OTP expired | IF `otp_challenges.expires_at < now` THEN reject verify | TOKEN_EXPIRED |
| EC-02 | OTP already consumed | IF `otp_challenges.consumed_at IS NOT NULL` THEN reject verify | CONFLICT |
| EC-03 | Login blocked user | IF `users.status = BLOCKED` THEN reject login | FORBIDDEN |
| EC-04 | Refresh reuse | IF refresh token already revoked THEN revoke family | DUPLICATE_REQUEST |
| EC-05 | Re-register same email | IF `users.email` exists THEN reject create | CONFLICT |

## References
- See `schema.md -> users.status`.
- See `schema.md -> refresh_tokens.revoked_at`.
