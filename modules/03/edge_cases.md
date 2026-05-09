# edge_cases.md

## Cases
| id | case | rule | error_code |
|---|---|---|---|
| EC-01 | Organization not approved | IF `organizations.status != APPROVED` THEN reject create | FORBIDDEN |
| EC-02 | End before start | IF `end_at <= start_at` THEN reject create/update | VALIDATION_ERROR |
| EC-03 | Duplicate active bank account | IF active account duplicate per project THEN reject update | CONFLICT |
| EC-04 | Edit after completed | IF `projects.status = COMPLETED` THEN reject update | INVALID_STATE |
| EC-05 | Auto-stop trigger race | IF concurrent stop triggers THEN apply idempotent transition once | DUPLICATE_REQUEST |

## References
- See `schema.md -> projects.status`.
- See `schema.md -> project_edit_history`.
