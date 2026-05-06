# Module 00: Tổng quan hệ thống (Mốc triển khai)

## 1. Phạm vi sản phẩm
Nền tảng kết nối người quyên góp/tình nguyện viên với các tổ chức cộng đồng.

Năng lực cốt lõi:
- Quản lý tài khoản + phân quyền
- Onboarding tổ chức và quản trị
- Tạo dự án và quản lý vòng đời dự án
- Quản lý tham gia tình nguyện
- Tiếp nhận quyên góp (bank webhook) và sao kê minh bạch
- Phát hiện gian lận + duyệt bởi admin
- Báo cáo giải ngân
- Báo cáo khiếu nại và thông báo

## 2. Vai trò và quyền hạn
- `VOLUNTEER`: xem dự án, quyên góp, tham gia dự án tình nguyện, gửi báo cáo vi phạm.
- `MANAGER`: quản lý tổ chức đã duyệt, quản lý dự án/thành viên/cập nhật/giải ngân.
- `ADMIN`: duyệt tổ chức/dự án/giải ngân, xử lý hàng đợi gian lận, thực hiện governance.

## 3. Kiến trúc tổng thể
- Backend: REST API + async workers (xử lý webhook, suy luận gian lận, gửi thông báo).
- Database: relational DB (MySQL/Postgres) với FK + unique constraints.
- Eventing: outbox pattern phát domain events tin cậy (`DONATION_MATCHED`, `PROJECT_APPROVED`, ...).
- AI: model registry (`STAGING/ACTIVE/PREVIOUS/REJECTED`) và feature snapshot bất biến.

## 4. Bộ trạng thái chuẩn
- `UserStatus`: `UNVERIFIED | ACTIVE | BLOCKED`
- `OrgStatus`: `PENDING_REVIEW | APPROVED | REJECTED | SUSPENDED | EXPIRED`
- `ProjectStatus`: `DRAFT | PENDING_REVIEW | APPROVED | REJECTED | ACTIVE | DISBURSEMENT_DUE | COMPLETED | SUSPENDED`
- `DonationSessionStatus`: `PENDING | MATCHED | EXPIRED | UNMATCHED | CANCELLED`
- `TransactionStatus`: `RECEIVED | NORMAL | SUSPICIOUS | CONFIRMED_FRAUD | REVERSED`
- `DisbursementStatus`: `NOT_REQUIRED | DUE | DRAFT | SUBMITTED | APPROVED | REJECTED | OVERDUE | VIOLATED`
- `ReportStatus`: `OPEN | IN_REVIEW | RESOLVED | DISMISSED`

## 5. Quy tắc cross-cutting
- Lưu thời gian UTC trong DB; client tự convert timezone.
- Phân trang phải khai báo rõ offset/cursor; mặc định limit 20, tối đa 100.
- Mutation APIs trả error code xác định và field validation rõ ràng.
- Webhook bắt buộc replay protection: `signature + timestamp + nonce`.
- Idempotency bắt buộc cho callback bên ngoài và thao tác duyệt/từ chối của admin.
- Hành động đặc quyền bắt buộc ghi immutable `audit_log`.
- Tiền tệ lưu theo đơn vị nhỏ nhất (`amount_minor`) + `currency`.
