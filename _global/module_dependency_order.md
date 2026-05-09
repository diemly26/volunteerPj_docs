# Module Dependency Order

12)
| Order | Module | Depends On | Justification |
|---:|---|---|---|
| 1 | `01-identity-access` | - | Defines user identity/auth base |
| 2 | `02-organization-onboarding` | 01 | Organization owner and reviewer are users |
| 3 | `03-project-lifecycle` | 02, 01 | Project belongs to organization; actor is user |
| 4 | `04-participation` | 03, 01 | Join/member records need project + user |
| 5 | `05-donations-payments` | 03, 01 | Donation session/transaction bind project + user |
| 6 | `09-fraud-ai` | 05 | Scores/labels derived from transactions |
| 7 | `06-statement-transparency` | 05, 09, 03 | Statement reads transactions + risk status + project context |
| 8 | `07-progress-updates` | 03, 04 | Update owner/permission depends on project member roles |
| 9 | `08-disbursement` | 03, 05, 07 | Disbursement cycle/report tied to project, donation totals, outcomes |
| 10 | `10-reporting-complaints` | 01, 03, 05, 07 | Reporter is user; targets are project/transaction/update |
| 11 | `11-notifications` | 01..10 | Consumes events from business modules |
| 12 | `12-admin-governance` | 01..11 | Global audit, sanctions, config over all modules |

## Shared Entity Ownership
| Shared Entity | Defining Module | Consumed By |
|---|---|---|
| `users` | 01 | 02,03,04,05,10,11,12 |
| `organizations` | 02 | 03,12 |
| `projects` | 03 | 04,05,06,07,08,10,11,12 |
| `project_members` | 04 | 07,11,12 |
| `transactions` | 05 | 06,08,09,10,11,12 |
| `donation_sessions` | 05 | 11,12 |
| `fraud_scores` / `transaction_labels` | 09 | 06,11,12 |
| `disbursement_reports` | 08 | 07,11,12 |
| `reports` | 10 | 11,12 |
| `notifications` | 11 | 12 |
| `system_config` / `audit_logs` / `user_sanctions` | 12 | all modules |

## Cross-Module Shared Reference Types
- Actor references: `user_id`, `created_by`, `admin_id`, `reviewed_by`
- Domain references: `organization_id`, `project_id`, `transaction_id`, `report_id`
- State references: all module statuses stored as `VARCHAR(32)` enums
