# Module 02: Onboarding tổ chức

## Phạm vi
Tiếp nhận và duyệt hồ sơ tổ chức, cấp quyền manager, gắn nhãn trusted.

## Core entities
- `organizations(...)`
- `organization_documents(...)`
- `organization_reviews(...)`

## Business flow
1. User `ACTIVE` nộp hồ sơ và giấy tờ bắt buộc.
2. Hệ thống đặt trạng thái `PENDING_REVIEW`.
3. Admin duyệt hoặc từ chối.
4. Duyệt thành công -> `APPROVED`; có thể cấp `MANAGER` cho người nộp.
5. `is_trusted` là thao tác governance riêng, bắt buộc có audit note.

## Ràng buộc
- Nộp hồ sơ bắt buộc đăng nhập.
- Không hard-delete hồ sơ quá hạn; chuyển `EXPIRED` và giữ dữ liệu phục vụ audit/pháp lý.
- `tax_code` unique giữa các tổ chức còn hiệu lực.

## API
- `POST /api/organizations`
- `GET /api/admin/organizations?status=`
- `POST /api/admin/organizations/{id}/approve`
- `POST /api/admin/organizations/{id}/reject`
- `PUT /api/admin/organizations/{id}/trusted`
