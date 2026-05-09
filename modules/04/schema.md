# schema.md

## Tables
| table | columns |
|---|---|
| project_join_requests | id BIGINT PK AI; project_id BIGINT NOT NULL; user_id BIGINT NOT NULL; message VARCHAR(500) NULL; status VARCHAR(32) NOT NULL; reviewed_by BIGINT NULL; reviewed_at DATETIME NULL; reject_reason VARCHAR(255) NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| project_members | id BIGINT PK AI; project_id BIGINT NOT NULL; user_id BIGINT NOT NULL; role VARCHAR(32) NOT NULL; status VARCHAR(32) NOT NULL; joined_at DATETIME NOT NULL; left_at DATETIME NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| project_member_role_history | id BIGINT PK AI; project_member_id BIGINT NOT NULL; before_role VARCHAR(32) NOT NULL; after_role VARCHAR(32) NOT NULL; changed_by BIGINT NOT NULL; reason VARCHAR(255) NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |

## Enums
| enum | values |
|---|---|
| JOIN_REQUEST_STATUS | PENDING, APPROVED, REJECTED, WITHDRAWN |
| PROJECT_MEMBER_ROLE | MANAGER, SUB_MANAGER, VOLUNTEER |
| PROJECT_MEMBER_STATUS | ACTIVE, INACTIVE |

## FK
| from | to | nullable |
|---|---|---|
| project_join_requests.project_id | projects.id | NO |
| project_join_requests.user_id | users.id | NO |
| project_join_requests.reviewed_by | users.id | YES |
| project_members.project_id | projects.id | NO |
| project_members.user_id | users.id | NO |
| project_member_role_history.project_member_id | project_members.id | NO |
| project_member_role_history.changed_by | users.id | NO |
| project_join_requests.created_by | users.id | YES |
| project_members.created_by | users.id | YES |
| project_member_role_history.created_by | users.id | YES |

## Indexes
| index | definition |
|---|---|
| idx_project_join_requests_project_id_status | project_join_requests(project_id, status) |
| uk_project_join_requests_project_id_user_id_pending | UNIQUE(project_id, user_id, status) filtered status='PENDING' |
| idx_project_members_project_id_status | project_members(project_id, status) |
| uk_project_members_project_id_user_id | UNIQUE(project_id, user_id) |
| idx_project_member_role_history_member_id_created_at | project_member_role_history(project_member_id, created_at) |
