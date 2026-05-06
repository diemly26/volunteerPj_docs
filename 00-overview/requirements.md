# Module 00: Tổng quan hệ thống

## 1. Phạm vi sản phẩm
Nền tảng kết nối tổ chức cộng đồng với người tham gia và người ủng hộ, bảo đảm minh bạch tài chính và kiểm soát rủi ro giao dịch.

Năng lực cốt lõi:
- Quản lý tài khoản và phân quyền.
- Onboarding tổ chức và quản trị dự án.
- Quyên góp trực tuyến qua tích hợp ngân hàng bằng webhook.
- Sao kê công khai và lịch sử chỉnh sửa dự án công khai.
- Báo cáo giải ngân công khai khi dự án kết thúc.
- Phát hiện giao dịch bất thường bằng AI hai tầng.

## 2. Vai trò và quyền hạn
- `VOLUNTEER`: xem dự án, quyên góp, tham gia hoạt động, theo dõi sao kê, gửi báo cáo.
- `MANAGER`: tạo và cập nhật dự án, quản lý hoạt động, nộp báo cáo giải ngân.
- `ADMIN`: duyệt tổ chức/dự án/giải ngân, giám sát rủi ro, quản trị cấu hình hệ thống.

## 3. Kiến trúc tổng thể
- Backend: REST API + worker bất đồng bộ cho webhook, AI scoring, thông báo.
- Database: relational DB (MySQL/Postgres) với FK, unique constraints, audit log.
- Eventing: outbox pattern cho các sự kiện nghiệp vụ quan trọng.
- AI: pipeline hai tầng gồm Isolation Forest + Fraud Classifier, có vòng phản hồi từ quyết định Admin.

## 4. Bộ trạng thái chuẩn
- `UserStatus`: `UNVERIFIED | ACTIVE | BLOCKED`
- `OrgStatus`: `PENDING_REVIEW | APPROVED | REJECTED | SUSPENDED | EXPIRED`
- `ProjectStatus`: `DRAFT | PENDING_REVIEW | APPROVED | ACTIVE | DISBURSEMENT_DUE | COMPLETED | REJECTED | SUSPENDED`
- `DonationSessionStatus`: `PENDING | MATCHED | EXPIRED | UNMATCHED | CANCELLED`
- `TransactionStatus`: `RECEIVED | NORMAL | SUSPICIOUS | CONFIRMED_FRAUD | REVERSED`
- `DisbursementStatus`: `DUE | DRAFT | SUBMITTED | APPROVED | REJECTED | OVERDUE | VIOLATED`

## 5. Quy tắc kỹ thuật áp dụng cho toàn hệ thống
- Tất cả thời gian lưu UTC.
- Tiền tệ lưu bằng đơn vị nhỏ nhất (`amount_minor`) + `currency`.
- Webhook bắt buộc: xác thực chữ ký (HMAC), chống replay, idempotency.
- Hành động đặc quyền bắt buộc ghi `audit_log`.
- Dữ liệu feature AI là snapshot bất biến tại thời điểm giao dịch.
