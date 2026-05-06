# Module 10: Báo cáo & Khiếu nại

## 1. Phạm vi
Cho phép người dùng phản ánh vi phạm hoặc bất thường liên quan đến dự án, giao dịch, nội dung cập nhật; cung cấp quy trình xử lý khiếu nại có SLA và kết quả minh bạch.

Mục tiêu nghiệp vụ:
- Tạo kênh giám sát cộng đồng hiệu quả.
- Chuẩn hóa phân loại mức độ ưu tiên để xử lý đúng trọng tâm.
- Lưu toàn bộ vòng đời xử lý phục vụ audit và cải tiến vận hành.

## 2. Core entities
- `reports(id, reporter_user_id, target_type, target_id, category, description, evidence_urls, priority, status, created_at, updated_at)`
- `report_actions(id, report_id, actor_id, action, note, before_status, after_status, created_at)`
- `report_sla(id, report_id, due_at, breached_at, escalation_level)`

## 3. Luồng nghiệp vụ
1. User gửi báo cáo vi phạm, hệ thống tạo trạng thái `OPEN`.
2. Worker triage phân loại mức ưu tiên `LOW | MEDIUM | HIGH` theo loại vi phạm và bằng chứng đính kèm.
3. Admin nhận xử lý, chuyển trạng thái `IN_REVIEW`.
4. Admin kết luận:
   - `RESOLVED`: xác nhận báo cáo đúng, đã xử lý.
   - `DISMISSED`: báo cáo không đủ căn cứ hoặc không đúng.
5. Hệ thống gửi thông báo kết quả cho người báo cáo.

## 4. Quy tắc và ràng buộc
- Người gửi báo cáo phải là user `ACTIVE`.
- Báo cáo bắt buộc có `target_type`, `target_id`, `description` tối thiểu.
- Không cho tạo liên tiếp nhiều báo cáo trùng mục tiêu trong thời gian quá ngắn từ cùng một user.
- Kết luận `DISMISSED` bắt buộc ghi rõ lý do.

## 5. Yêu cầu kỹ thuật triển khai
- Hỗ trợ gắn SLA theo mức ưu tiên để theo dõi tồn đọng.
- Mọi thay đổi trạng thái phải ghi `report_actions`.
- Cần dashboard admin gồm: số lượng theo trạng thái, theo ưu tiên, theo loại vi phạm.
- Có cơ chế escalation tự động khi gần/đã quá SLA.

## 6. Edge cases
- Báo cáo liên quan transaction đã bị `REVERSED`: vẫn phải cho phép gửi báo cáo bổ sung nếu có dấu hiệu gian lận khác.
- Nhiều báo cáo cùng target trong thời gian ngắn: gợi ý gộp vụ việc để xử lý tập trung.
- Reporter bị `BLOCKED` sau khi đã gửi báo cáo: báo cáo cũ vẫn phải được xử lý đến kết luận.

## 7. API
- `POST /api/reports`
- `GET /api/reports/my`
- `GET /api/admin/reports?status=&priority=&category=`
- `POST /api/admin/reports/{id}/start-review`
- `POST /api/admin/reports/{id}/resolve`
- `POST /api/admin/reports/{id}/dismiss`
- `POST /api/admin/reports/{id}/escalate`
