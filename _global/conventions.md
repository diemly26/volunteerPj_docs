# Global Conventions

## 1) Naming Conventions

### 1.1 Java
| Item | Convention | Example |
|---|---|---|
| Package | lower-case dot-separated, by layer | `com.volunteerpj.module05.donation.service` |
| Class | PascalCase + role suffix | `DonationSessionService`, `ProjectController` |
| Interface | PascalCase (no `I` prefix) | `FraudScoringService` |
| Method | camelCase verb-first | `createDonationSession()` |
| Variable/field | camelCase | `riskScore`, `createdAt` |
| Constant | SCREAMING_SNAKE_CASE | `MAX_OTP_ATTEMPTS` |

### 1.2 Database
| Item | Convention | Example |
|---|---|---|
| Table | plural snake_case | `donation_sessions`, `project_members` |
| Column | snake_case | `project_id`, `expires_at` |
| PK | `id` | `id BIGINT` |
| FK column | `<ref_table_singular>_id` | `user_id`, `project_id` |
| Unique index | `uk_<table>_<columns>` | `uk_users_email` |
| Normal index | `idx_<table>_<columns>` | `idx_transactions_project_id_received_at` |
| FK name | `fk_<table>_<ref_table>` | `fk_transactions_users` |

### 1.3 API
| Item | Convention | Example |
|---|---|---|
| Version prefix | `/api/v1` | `/api/v1/projects` |
| Resource path | plural kebab-case noun | `/api/v1/join-requests` |
| Nested resource | parent then child | `/api/v1/projects/{id}/updates` |
| Admin scope | `/api/v1/admin/...` | `/api/v1/admin/disbursements/{id}/approve` |
| HTTP verbs | `GET` read, `POST` create/action, `PUT` replace/update, `PATCH` partial update, `DELETE` remove | - |

### 1.4 DTO suffix rules
| Purpose | Suffix | Example |
|---|---|---|
| Create request | `CreateRequest` | `OrganizationCreateRequest` |
| Update request | `UpdateRequest` | `ProjectUpdateRequest` |
| Action request | `ActionRequest` | `DisbursementApproveActionRequest` |
| Response | `Response` | `DonationSessionResponse` |
| Summary item | `SummaryResponse` | `ProjectSummaryResponse` |
| Internal mapper dto | `Dto` | `TransactionFeatureDto` |

## 2) Standard Field Set (all entities)
| Field | Type | Constraint |
|---|---|---|
| `id` | `BIGINT` | `PRIMARY KEY AUTO_INCREMENT` |
| `created_at` | `DATETIME` | `NOT NULL DEFAULT CURRENT_TIMESTAMP` |
| `updated_at` | `DATETIME` | `NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP` |
| `created_by` | `BIGINT` | `NULL`, `FK -> users.id` |

## 3) Standard API Response Envelope
```json
{
  "success": true,
  "data": {},
  "error": {
    "code": "",
    "message": ""
  }
}
```

## 4) Standard Enum Format
- Format: `SCREAMING_SNAKE_CASE`
- DB storage: `VARCHAR(32)`

## 5) Standard Error Codes
| Code | Meaning |
|---|---|
| `AUTH_REQUIRED` | Missing/invalid authentication |
| `TOKEN_EXPIRED` | Access/refresh token expired |
| `FORBIDDEN` | Authenticated but no permission |
| `NOT_FOUND` | Resource does not exist |
| `VALIDATION_ERROR` | Request data invalid |
| `CONFLICT` | Unique/state conflict |
| `DUPLICATE_REQUEST` | Idempotency duplicate |
| `RATE_LIMITED` | Too many requests |
| `INVALID_STATE` | Action not allowed in current state |
| `EXTERNAL_SERVICE_ERROR` | Upstream dependency failed |
| `WEBHOOK_INVALID_SIGNATURE` | Webhook signature invalid |
| `WEBHOOK_REPLAY_DETECTED` | Replay attack detected |
| `AI_MODEL_NOT_READY` | Model unavailable/not active |
| `PAGINATION_INVALID` | Invalid page/size/sort |
| `INTERNAL_ERROR` | Unhandled server error |

## 6) Standard Pagination
- Request: `?page=0&size=20&sort=createdAt,desc`

```json
{
  "content": [],
  "totalElements": 0,
  "totalPages": 0,
  "page": 0,
  "size": 20
}
```

## 7) Timestamp Handling
- DB timestamps stored in UTC.
- API timestamps serialized as ISO-8601 UTC (`yyyy-MM-dd'T'HH:mm:ss'Z'`).
- TTL fields use `expires_at DATETIME` (UTC).

## 8) FK Convention
- All FK columns are nullable by default unless business rule marks required.
- Every FK column must have an index.
- Join/query-heavy relations should add composite indexes by access pattern.
