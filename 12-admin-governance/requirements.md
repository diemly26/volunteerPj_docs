# Module 12: Quản trị & Governance

## Phạm vi
Hàng đợi vận hành admin, chế tài người dùng, thay đổi cấu hình/chính sách và toàn vẹn audit.

## Core entities
- `admin_queue_snapshots(...)`
- `user_sanctions(...)`
- `system_config(...)`
- `audit_logs(...)`

## Governance flows
Dashboard admin tổng hợp hàng đợi xử lý.  
Block/unblock user phải tạo sanction record, không overwrite âm thầm.  
Đổi cấu hình nhạy cảm (fraud thresholds, trust policy) bắt buộc có lý do và audit.

## API
- `GET /api/admin/queues`
- `PUT /api/admin/users/{id}/block`
- `PUT /api/admin/users/{id}/unblock`
- `GET /api/admin/audit-logs`
- `PUT /api/admin/config/{key}`
