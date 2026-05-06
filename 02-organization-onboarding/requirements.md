# Module 02: Onboarding tổ chức

## 1. Phạm vi
Tiếp nhận, thẩm định và phê duyệt hồ sơ tổ chức tham gia nền tảng; cấp quyền quản lý dự án và kiểm soát trạng thái pháp lý của tổ chức.

Mục tiêu nghiệp vụ:
- Đảm bảo chỉ tổ chức hợp lệ mới được kêu gọi quyên góp.
- Chuẩn hóa quy trình duyệt hồ sơ để minh bạch và có thể audit.
- Tách rõ quyết định "được hoạt động" và quyết định "trusted".

## 2. Core entities
- `organizations(id, owner_user_id, legal_name, tax_code, representative_name, contact_email, status, is_trusted, trusted_note, approved_at, rejected_at, created_at, updated_at)`
- `organization_documents(id, organization_id, doc_type, file_url, file_hash, status, uploaded_at, verified_at)`
- `organization_reviews(id, organization_id, admin_id, action, reason, created_at)`

## 3. Luồng nghiệp vụ
1. Người dùng `ACTIVE` nộp hồ sơ tổ chức và tài liệu bắt buộc.
2. Hệ thống kiểm tra dữ liệu tối thiểu, chuyển hồ sơ sang `PENDING_REVIEW`.
3. Admin kiểm tra pháp lý, tính đầy đủ hồ sơ và tính nhất quán thông tin.
4. Admin quyết định:
   - `APPROVED`: tổ chức được phép hoạt động.
   - `REJECTED`: từ chối, bắt buộc nêu lý do rõ ràng.
5. Sau khi `APPROVED`, Admin có thể cấp `MANAGER` cho chủ hồ sơ.
6. Cờ `is_trusted` chỉ được bật/tắt qua luồng governance riêng và bắt buộc ghi audit.

## 4. Quy tắc và ràng buộc
- `tax_code` là duy nhất đối với tổ chức còn hiệu lực.
- Tổ chức `REJECTED` có thể nộp lại hồ sơ mới sau khi cập nhật đầy đủ giấy tờ.
- Không hard-delete hồ sơ đã nộp; nếu hết hiệu lực thì chuyển `EXPIRED` để lưu dấu vết pháp lý.
- Tổ chức `SUSPENDED` không được tạo dự án mới và không được chỉnh sửa dự án đang vận hành.

## 5. Yêu cầu kỹ thuật triển khai
- Tài liệu tải lên cần lưu `file_hash` để phát hiện tài liệu trùng hoặc thay đổi nội dung.
- Trạng thái tài liệu (`organization_documents.status`) tách riêng với trạng thái tổ chức để theo dõi chi tiết.
- Mọi quyết định duyệt/từ chối/suspend/trusted đều phải tạo bản ghi `organization_reviews` hoặc `audit_logs`.
- Danh sách duyệt cần hỗ trợ lọc theo trạng thái, ngày nộp, độ ưu tiên xử lý.

## 6. Edge cases
- Hồ sơ thiếu giấy tờ: không cho chuyển `PENDING_REVIEW`.
- Tax code trùng với tổ chức đang hoạt động: từ chối nộp mới.
- Admin từ chối nhưng không nhập lý do: không cho submit quyết định.
- Tổ chức đang `APPROVED` bị chuyển `SUSPENDED`: cần phát thông báo cho owner và admin queue.

## 7. API
- `POST /api/organizations`
- `PUT /api/organizations/{id}`
- `GET /api/organizations/{id}`
- `GET /api/admin/organizations?status=&keyword=&from=&to=`
- `POST /api/admin/organizations/{id}/approve`
- `POST /api/admin/organizations/{id}/reject`
- `POST /api/admin/organizations/{id}/suspend`
- `PUT /api/admin/organizations/{id}/trusted`
