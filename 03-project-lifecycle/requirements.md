# Module 03: Vòng đời dự án

## 1. Phạm vi
Quản lý toàn bộ vòng đời dự án: tạo, duyệt, vận hành, chỉnh sửa công khai, dừng nhận quyên góp và chuyển sang giải ngân.

## 2. Core entities
- `projects(id, organization_id, type, title, description, fundraising_target_minor, currency, start_at, end_at, status, ...)`
- `project_bank_accounts(id, project_id, bank_code, account_no, account_name, is_active, ...)`
- `project_edit_history(id, project_id, editor_id, before_json, after_json, reason, created_at)`

## 3. Lifecycle
`DRAFT -> PENDING_REVIEW -> APPROVED -> ACTIVE -> DISBURSEMENT_DUE -> COMPLETED`

Nhánh kiểm soát:
- `PENDING_REVIEW -> REJECTED`
- `ACTIVE -> SUSPENDED` (Admin)

## 4. Quy tắc nghiệp vụ
- Chỉ tổ chức `APPROVED` được nộp dự án.
- Dự án `MONEY_DONATION` bắt buộc có tài khoản nhận tiền active.
- Trusted org có thể auto-approve theo policy flag, nhưng vẫn phải ghi audit.
- Trường nhạy cảm (`bank_account`, `fundraising_target_minor`, `end_at`) khi chỉnh sửa phải bật cờ re-review.

## 5. Cơ chế tự động ngừng quyên góp
Dự án dừng nhận quyên góp ngay khi đạt một trong hai điều kiện:
1. Tổng tiền hợp lệ đã đạt `fundraising_target_minor`.
2. Đã qua `end_at` (hết thời hạn kêu gọi).

Khi dừng:
- Không tạo donation session mới.
- Không nhận match mới cho dự án (trừ xử lý ngoại lệ hậu kiểm).
- Trạng thái chuyển sang `DISBURSEMENT_DUE` để bắt đầu giai đoạn báo cáo giải ngân.

## 6. Công khai lịch sử chỉnh sửa dự án
Lịch sử chỉnh sửa dự án phải public cho mọi người dùng (kể cả chưa đăng nhập), bao gồm:
- thời điểm chỉnh sửa,
- nội dung trước/sau,
- người chỉnh sửa,
- lý do chỉnh sửa (nếu có).

Yêu cầu hiển thị:
- Sắp xếp theo thời gian mới nhất.
- Không cho xóa bản ghi lịch sử đã phát hành.
- Có phân trang để hỗ trợ dự án chỉnh sửa nhiều lần.

## 7. API
- `POST /api/projects`
- `PUT /api/projects/{id}`
- `GET /api/projects/{id}`
- `GET /api/projects/{id}/history`
- `POST /api/admin/projects/{id}/approve`
- `POST /api/admin/projects/{id}/reject`
- `POST /api/admin/projects/{id}/suspend`
