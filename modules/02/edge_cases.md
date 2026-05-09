# edge_cases.md

## Cases
| id | case | rule | error_code |
|---|---|---|---|
| EC-01 | Owner not active | IF `users.status != ACTIVE` THEN reject create | FORBIDDEN |
| EC-02 | Duplicate tax code | IF `organizations.tax_code` exists THEN reject create | CONFLICT |
| EC-03 | Reject without reason | IF `reason` empty THEN reject action request | VALIDATION_ERROR |
| EC-04 | Approve suspended org | IF `organizations.status = SUSPENDED` THEN reject approve | INVALID_STATE |
| EC-05 | Missing required document | IF mandatory document missing THEN reject submit | VALIDATION_ERROR |

## References
- See `schema.md -> organizations.status`.
- See `schema.md -> organization_documents.status`.
