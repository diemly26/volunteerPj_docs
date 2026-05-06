# Module 08: Giải ngân

## Phạm vi
Nộp báo cáo chi tiêu, admin duyệt và cưỡng chế tuân thủ hạn giải ngân.

## Core entities
- `disbursement_cycles(...)`
- `disbursement_reports(...)`
- `disbursement_items(...)`

## Business flow
1. Kết thúc nhận tiền -> tạo cycle `DUE` (14 ngày).
2. Manager nộp báo cáo `DRAFT -> SUBMITTED`.
3. Admin duyệt `APPROVED` hoặc từ chối `REJECTED` (bắt buộc lý do).
4. Duyệt thành công -> dự án `COMPLETED`, tự động đăng bài tổng kết.

## Chính sách tuân thủ
- Nhắc hạn T-7/T-3/T-1.
- Quá hạn -> `OVERDUE`.
- Gia hạn tối đa 1 lần, 3 ngày.
- Hết hạn sau gia hạn -> `VIOLATED`.

## API
- `POST /api/projects/{id}/disbursement/report`
- `POST /api/projects/{id}/disbursement/report/submit`
- `POST /api/admin/disbursements/{id}/approve`
- `POST /api/admin/disbursements/{id}/reject`
- `POST /api/admin/disbursements/{id}/extend`
