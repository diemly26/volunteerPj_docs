# schema.md

## Tables
| table | columns |
|---|---|
| users | id BIGINT PK AI; email VARCHAR(255) NOT NULL UNIQUE; password_hash VARCHAR(255) NOT NULL; full_name VARCHAR(255) NOT NULL; role VARCHAR(32) NOT NULL; status VARCHAR(32) NOT NULL; email_verified_at DATETIME NULL; failed_login_count INT NOT NULL DEFAULT 0; last_login_at DATETIME NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| otp_challenges | id BIGINT PK AI; email VARCHAR(255) NOT NULL; purpose VARCHAR(32) NOT NULL; code_hash VARCHAR(255) NOT NULL; expires_at DATETIME NOT NULL; attempt_count INT NOT NULL DEFAULT 0; consumed_at DATETIME NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| refresh_tokens | id BIGINT PK AI; user_id BIGINT NOT NULL; token_hash VARCHAR(255) NOT NULL UNIQUE; rotated_from_id BIGINT NULL; expires_at DATETIME NOT NULL; revoked_at DATETIME NULL; ip_address VARCHAR(64) NULL; user_agent VARCHAR(255) NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |
| auth_audit_logs | id BIGINT PK AI; user_id BIGINT NULL; action VARCHAR(64) NOT NULL; result VARCHAR(32) NOT NULL; reason VARCHAR(255) NULL; ip_address VARCHAR(64) NULL; user_agent VARCHAR(255) NULL; created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP; updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP; created_by BIGINT NULL |

## Enums
| enum | values |
|---|---|
| USER_STATUS | UNVERIFIED, ACTIVE, BLOCKED |
| USER_ROLE | VOLUNTEER, MANAGER, ADMIN |
| OTP_PURPOSE | REGISTER_VERIFY, PASSWORD_RESET |

## FK
| from | to | nullable |
|---|---|---|
| refresh_tokens.user_id | users.id | NO |
| refresh_tokens.rotated_from_id | refresh_tokens.id | YES |
| users.created_by | users.id | YES |
| otp_challenges.created_by | users.id | YES |
| refresh_tokens.created_by | users.id | YES |
| auth_audit_logs.created_by | users.id | YES |
| auth_audit_logs.user_id | users.id | YES |

## Indexes
| index | definition |
|---|---|
| uk_users_email | UNIQUE(users.email) |
| idx_users_status | users(status) |
| idx_otp_challenges_email_purpose | otp_challenges(email, purpose) |
| idx_otp_challenges_expires_at | otp_challenges(expires_at) |
| idx_refresh_tokens_user_id | refresh_tokens(user_id) |
| idx_refresh_tokens_expires_at | refresh_tokens(expires_at) |
| idx_auth_audit_logs_user_id_created_at | auth_audit_logs(user_id, created_at) |
