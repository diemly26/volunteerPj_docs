# Module 12: Quản trị & Governance

## 1. Phạm vi
Quản trị vận hành hệ thống ở cấp toàn cục: điều phối hàng đợi xử lý, áp dụng chế tài tài khoản, quản lý cấu hình nhạy cảm, bảo đảm toàn vẹn audit log và khả năng rollback.

Mục tiêu nghiệp vụ:
- Giữ vận hành ổn định khi khối lượng xử lý tăng.
- Kiểm soát thay đổi chính sách để tránh rủi ro ngoài ý muốn.
- Đảm bảo mọi quyết định quản trị có thể truy vết và kiểm chứng.

## 2. Core entities
- `admin_queue_snapshots(id, queue_type, pending_count, high_priority_count, captured_at)`
- `user_sanctions(id, user_id, action_type, reason, start_at, end_at, created_by, created_at, revoked_at, revoked_by)`
- `system_config(key, value_json, version, updated_by, updated_at)`
- `audit_logs(id, actor_id, actor_role, action, target_type, target_id, metadata_json, created_at)`

## 3. Luồng nghiệp vụ
### 3.1 Quản lý hàng đợi vận hành
1. Hệ thống tổng hợp các hàng đợi chính: duyệt tổ chức, duyệt dự án, giao dịch rủi ro, báo cáo vi phạm, giải ngân quá hạn.
2. Admin theo dõi dashboard và xử lý theo mức ưu tiên.
3. Snapshot hàng đợi được lưu định kỳ để theo dõi năng lực vận hành theo thời gian.

### 3.2 Áp dụng chế tài người dùng
1. Admin xác định vi phạm và chọn hình thức chế tài (`BLOCK`, `UNBLOCK`, giới hạn chức năng...).
2. Bắt buộc nhập lý do và phạm vi áp dụng.
3. Hệ thống tạo bản ghi chế tài mới, không ghi đè lịch sử.
4. Khi gỡ chế tài, cập nhật `revoked_at`, `revoked_by` để bảo toàn lịch sử.

### 3.3 Quản lý cấu hình nhạy cảm
1. Admin cập nhật cấu hình (ví dụ threshold AI, policy trusted).
2. Hệ thống ghi version mới vào `system_config`.
3. Mọi thay đổi đều ghi audit và hỗ trợ rollback về version trước đó.

## 4. Quy tắc và ràng buộc
- Không cho phép chỉnh sửa trực tiếp bản ghi audit log.
- Cấu hình nhạy cảm bắt buộc có lý do thay đổi.
- Chỉ admin có quyền phù hợp mới được thao tác config hoặc sanction.
- Thao tác sanction không được làm mất dữ liệu lịch sử giao dịch/nghiệp vụ của user.

## 5. Yêu cầu kỹ thuật triển khai
- `audit_logs` cần hỗ trợ truy vấn theo `actor`, `action`, `target`, `time_range`.
- Các thay đổi config cần cơ chế optimistic locking theo `version` để tránh ghi đè đồng thời.
- Dashboard vận hành cần chỉ số tối thiểu: backlog, thời gian chờ trung bình, tỷ lệ xử lý đúng SLA.
- API quản trị phải có rate limit riêng, tách khỏi API người dùng phổ thông.

## 6. Edge cases
- Hai admin cập nhật cùng một config key gần như đồng thời: request đến sau phải bị từ chối do lệch version.
- Admin chặn nhầm tài khoản: phải có luồng un-block nhanh, có log đầy đủ.
- Lỗi hệ thống trong lúc ghi audit: không cho commit thao tác quản trị nếu chưa ghi được audit tương ứng.

## 7. API
- `GET /api/admin/queues`
- `GET /api/admin/queues/history`
- `PUT /api/admin/users/{id}/block`
- `PUT /api/admin/users/{id}/unblock`
- `GET /api/admin/users/{id}/sanctions`
- `GET /api/admin/audit-logs`
- `PUT /api/admin/config/{key}`
- `POST /api/admin/config/{key}/rollback`
