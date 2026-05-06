# Module 03: Vòng đời dự án

## Phạm vi
Tạo dự án, duyệt dự án, cập nhật có kiểm soát và quản lý chuyển trạng thái.

## Core entities
- `projects(...)`
- `project_bank_accounts(...)`
- `project_edit_history(...)`

## Lifecycle
`DRAFT -> PENDING_REVIEW -> APPROVED -> ACTIVE -> DISBURSEMENT_DUE -> COMPLETED`

Nhánh phụ: `PENDING_REVIEW -> REJECTED`, `ACTIVE -> SUSPENDED` (chỉ admin).

## Quy tắc
- Chỉ tổ chức `APPROVED` được nộp dự án.
- Dự án `MONEY_DONATION` bắt buộc có bank account active.
- Trusted org có thể auto-approve theo policy flag nhưng vẫn phải audit.
- Chỉnh sửa trường trọng yếu (`bank_account`, `end_at`, fundraising target) phải bật cờ re-review.

## API
- `POST /api/projects`
- `PUT /api/projects/{id}`
- `GET /api/projects/{id}`
- `GET /api/projects/{id}/history`
- `POST /api/admin/projects/{id}/approve`
- `POST /api/admin/projects/{id}/reject`
- `POST /api/admin/projects/{id}/suspend`
