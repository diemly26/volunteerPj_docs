# Module 07: Cập nhật tiến độ

## Phạm vi
Quản lý bài cập nhật hoạt động, lịch sử chỉnh sửa và bài tổng kết giải ngân.

## Core entities
- `project_updates(...)`
- `project_update_assets(...)`
- `project_update_edits(...)`

## Quy tắc nghiệp vụ
- Manager đăng bài cho dự án active.
- Chỉnh sửa có lịch sử immutable.
- Xóa theo soft-delete `REMOVED`.
- Khi giải ngân được duyệt, hệ thống tự tạo bài tổng kết tài chính.
- Nếu `is_disbursement_result=true` thì trường tài chính immutable.

## API
- `POST /api/projects/{id}/updates`
- `PUT /api/projects/{id}/updates/{updateId}`
- `DELETE /api/projects/{id}/updates/{updateId}`
- `GET /api/projects/{id}/updates`
