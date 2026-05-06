# Module 07: Cập nhật tiến độ

## 1. Phạm vi
Quản lý bài cập nhật hoạt động dự án, lịch sử chỉnh sửa bài viết và bài tổng kết tự động sau khi giải ngân được duyệt.

## 2. Core entities
- `project_updates(...)`
- `project_update_assets(...)`
- `project_update_edits(...)`

## 3. Quy tắc nghiệp vụ
- Manager chỉ đăng bài cho dự án đang hoạt động hoặc giai đoạn phù hợp.
- Mọi chỉnh sửa bài cập nhật phải ghi lịch sử immutable.
- Xóa bài dùng soft-delete (`REMOVED`) để giữ liên kết kiểm tra/audit.
- Khi báo cáo giải ngân được duyệt, hệ thống tự tạo bài tổng kết tài chính và kết quả hoạt động.
- Nếu `is_disbursement_result=true`, phần số liệu tài chính không cho chỉnh sửa tay.

## 4. Yêu cầu công khai
- Người dùng công khai xem được bài cập nhật theo dòng thời gian.
- Bài tổng kết giải ngân phải gắn liên kết tới báo cáo giải ngân công khai.

## 5. API
- `POST /api/projects/{id}/updates`
- `PUT /api/projects/{id}/updates/{updateId}`
- `DELETE /api/projects/{id}/updates/{updateId}`
- `GET /api/projects/{id}/updates`
