# Module 06: Minh bạch sao kê

## Phạm vi
Sao kê công khai, quy tắc hiển thị theo trạng thái gian lận và export.

## Chính sách hiển thị
- Hiển thị: `NORMAL`, `SUSPICIOUS`.
- Ẩn: `CONFIRMED_FRAUD`, `REVERSED`.

## Core entities
- `statement_views`
- `statement_exports(...)`

## Quy tắc
- Sao kê near-real-time.
- Tên người gửi theo alias/mask.
- Export chỉ cho manager/admin.
- Giới hạn 20.000 dòng/lần.

## API
- `GET /api/projects/{id}/statement`
- `GET /api/projects/{id}/statement/export?format=csv|xlsx`
