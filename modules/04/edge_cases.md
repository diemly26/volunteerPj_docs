# edge_cases.md

## Cases
| id | case | rule | error_code |
|---|---|---|---|
| EC-01 | Duplicate pending request | IF same user has PENDING request THEN reject create | CONFLICT |
| EC-02 | User already active member | IF active membership exists THEN reject create | CONFLICT |
| EC-03 | Approve by unauthorized actor | IF actor is not manager or sub_manager THEN reject approve | FORBIDDEN |
| EC-04 | Promote second manager | IF manager exists THEN reject role change to MANAGER | CONFLICT |
| EC-05 | Withdraw non-pending request | IF status is not PENDING THEN reject withdraw | INVALID_STATE |

## References
- See `schema.md -> project_join_requests.status`.
- See `schema.md -> project_members.role`.
