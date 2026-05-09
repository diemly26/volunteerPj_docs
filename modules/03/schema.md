# schema.md

## Tables
| table | columns |
|---|---|
| projects | id BIGINT PK AI; organization_id BIGINT NOT NULL; type VARCHAR(32) NOT NULL; title VARCHAR(255) NOT NULL; description TEXT NOT NULL; fundraising_target_minor BIGINT NOT NULL; currency VARCHAR(8) NOT NULL; start_at DATETIME NOT NULL; end_at DATETIME NOT NULL; status VARCHAR(32) NOT NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| project_bank_accounts | id BIGINT PK AI; project_id BIGINT NOT NULL; bank_code VARCHAR(32) NOT NULL; account_no VARCHAR(64) NOT NULL; account_name VARCHAR(255) NOT NULL; is_active BOOLEAN NOT NULL DEFAULT TRUE; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| project_edit_history | id BIGINT PK AI; project_id BIGINT NOT NULL; editor_id BIGINT NOT NULL; before_json JSON NOT NULL; after_json JSON NOT NULL; reason VARCHAR(255) NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |

## Enums
| enum | values |
|---|---|
| PROJECT_STATUS | DRAFT, PENDING_REVIEW, APPROVED, ACTIVE, DISBURSEMENT_DUE, COMPLETED, REJECTED, SUSPENDED |
| PROJECT_TYPE | MONEY_DONATION, VOLUNTEER |

## FK
| from | to | nullable |
|---|---|---|
| projects.organization_id | organizations.id | NO |
| project_bank_accounts.project_id | projects.id | NO |
| project_edit_history.project_id | projects.id | NO |
| project_edit_history.editor_id | users.id | NO |
| projects.created_by | users.id | YES |
| project_bank_accounts.created_by | users.id | YES |
| project_edit_history.created_by | users.id | YES |

## Indexes
| index | definition |
|---|---|
| idx_projects_organization_id | projects(organization_id) |
| idx_projects_status_end_at | projects(status, end_at) |
| idx_project_bank_accounts_project_id | project_bank_accounts(project_id) |
| uk_project_bank_accounts_project_id_account_no | UNIQUE(project_bank_accounts.project_id, account_no) |
| idx_project_edit_history_project_id_created_at | project_edit_history(project_id, created_at) |
