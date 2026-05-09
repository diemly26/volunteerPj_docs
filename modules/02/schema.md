# schema.md

## Tables
| table | columns |
|---|---|
| organizations | id BIGINT PK AI; owner_user_id BIGINT NOT NULL; legal_name VARCHAR(255) NOT NULL; tax_code VARCHAR(64) NOT NULL; representative_name VARCHAR(255) NOT NULL; contact_email VARCHAR(255) NOT NULL; status VARCHAR(32) NOT NULL; is_trusted BOOLEAN NOT NULL DEFAULT FALSE; trusted_note VARCHAR(255) NULL; approved_at DATETIME NULL; rejected_at DATETIME NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| organization_documents | id BIGINT PK AI; organization_id BIGINT NOT NULL; doc_type VARCHAR(32) NOT NULL; file_url VARCHAR(500) NOT NULL; file_hash VARCHAR(128) NOT NULL; status VARCHAR(32) NOT NULL; uploaded_at DATETIME NOT NULL; verified_at DATETIME NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| organization_reviews | id BIGINT PK AI; organization_id BIGINT NOT NULL; admin_id BIGINT NOT NULL; action VARCHAR(32) NOT NULL; reason VARCHAR(255) NOT NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |

## Enums
| enum | values |
|---|---|
| ORG_STATUS | PENDING_REVIEW, APPROVED, REJECTED, SUSPENDED, EXPIRED |
| ORG_DOC_STATUS | PENDING_REVIEW, APPROVED, REJECTED |
| ORG_REVIEW_ACTION | APPROVE, REJECT, SUSPEND, TRUST, UNTRUST |

## FK
| from | to | nullable |
|---|---|---|
| organizations.owner_user_id | users.id | NO |
| organization_documents.organization_id | organizations.id | NO |
| organization_reviews.organization_id | organizations.id | NO |
| organization_reviews.admin_id | users.id | NO |
| organizations.created_by | users.id | YES |
| organization_documents.created_by | users.id | YES |
| organization_reviews.created_by | users.id | YES |

## Indexes
| index | definition |
|---|---|
| uk_organizations_tax_code | UNIQUE(organizations.tax_code) |
| idx_organizations_owner_user_id | organizations(owner_user_id) |
| idx_organizations_status | organizations(status) |
| idx_organization_documents_organization_id | organization_documents(organization_id) |
| idx_organization_documents_status | organization_documents(status) |
| idx_organization_reviews_organization_id_created_at | organization_reviews(organization_id, created_at) |
