# Module 10: Báo cáo & Khiếu nại

## Phạm vi
Người dùng báo cáo vi phạm/sự cố trên dự án, giao dịch, bài cập nhật; admin tiếp nhận và xử lý.

## Core entities
- `reports(...)`
- `report_actions(...)`

## Business flow
1. User đã xác thực tạo report `OPEN`.
2. Worker triage gán ưu tiên `LOW/MEDIUM/HIGH`.
3. Admin chuyển `IN_REVIEW`.
4. Kết luận `RESOLVED` hoặc `DISMISSED`.
5. Gửi thông báo kết quả cho reporter.

## API
- `POST /api/reports`
- `GET /api/admin/reports?status=&priority=`
- `POST /api/admin/reports/{id}/start-review`
- `POST /api/admin/reports/{id}/resolve`
