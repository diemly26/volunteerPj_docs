# Module 04: Tham gia dự án

## 1. Phạm vi
Quản lý quy trình tình nguyện viên đăng ký tham gia dự án, xét duyệt tham gia và quản lý vai trò thành viên trong đội dự án.

Mục tiêu nghiệp vụ:
- Đảm bảo tham gia đúng dự án, đúng vai trò, đúng thời điểm.
- Tránh trùng lặp yêu cầu và giảm thao tác thủ công cho manager.
- Theo dõi đầy đủ lịch sử thay đổi vai trò thành viên.

## 2. Core entities
- `project_join_requests(id, project_id, user_id, message, status, reviewed_by, reviewed_at, reject_reason, created_at, updated_at)`
- `project_members(id, project_id, user_id, role, status, joined_at, left_at, created_at, updated_at)`
- `project_member_role_history(id, project_member_id, before_role, after_role, changed_by, reason, created_at)`

## 3. Luồng nghiệp vụ
1. Tình nguyện viên gửi yêu cầu tham gia dự án (`PENDING`).
2. Manager/Sub-manager xem danh sách chờ duyệt.
3. Kết quả xử lý:
   - `APPROVED`: tạo bản ghi thành viên đang hoạt động.
   - `REJECTED`: lưu lý do từ chối.
   - `WITHDRAWN`: người dùng tự rút yêu cầu trước khi được duyệt.
4. Trong quá trình vận hành, manager có thể thay đổi vai trò thành viên theo quyền hạn.

## 4. Quy tắc và ràng buộc
- Một user chỉ có tối đa một `PENDING` request cho cùng một dự án.
- User `BLOCKED` không được tạo join request mới.
- Chỉ dự án đang `ACTIVE` mới nhận thành viên mới.
- Vai trò `MANAGER` của một dự án phải là duy nhất tại một thời điểm.
- Sub-manager không được tự nâng quyền lên manager.

## 5. Yêu cầu kỹ thuật triển khai
- Cần index `(project_id, status)` để tối ưu màn hình duyệt danh sách.
- Thay đổi vai trò phải ghi vào `project_member_role_history` để truy vết.
- Khi member rời dự án, chuyển trạng thái `INACTIVE`, không xóa cứng dữ liệu.
- Notification bắt buộc cho các sự kiện: yêu cầu được duyệt, bị từ chối, thay đổi vai trò.

## 6. Edge cases
- User gửi join request trùng khi đã là member active: trả lỗi nghiệp vụ rõ ràng.
- Yêu cầu bị duyệt đồng thời từ nhiều admin: dùng khóa giao dịch để tránh tạo trùng member.
- Dự án bị `SUSPENDED` trong lúc còn request `PENDING`: tự động đóng request chưa xử lý với lý do hệ thống.

## 7. API
- `POST /api/projects/{id}/join-requests`
- `GET /api/projects/{id}/join-requests?status=`
- `POST /api/projects/{id}/join-requests/{requestId}/approve`
- `POST /api/projects/{id}/join-requests/{requestId}/reject`
- `POST /api/projects/{id}/join-requests/{requestId}/withdraw`
- `PUT /api/projects/{id}/members/{memberId}/role`
- `DELETE /api/projects/{id}/members/{memberId}`
