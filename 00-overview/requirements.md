# Module 00: Overview — Tổng quan hệ thống

## 1. Mục tiêu
- Kết nối tổ chức cộng đồng với tình nguyện viên và người quyên góp.
- Đảm bảo minh bạch qua sao kê real-time, progress updates, và báo cáo giải ngân bắt buộc.
- Tích hợp AI phát hiện giao dịch bất thường theo kiến trúc Hybrid 2 tầng.

## 2. Vai trò hệ thống
| Role | Mô tả | Quyền chính |
|------|-------|------------|
| Volunteer | Người tham gia dự án và quyên góp | Tìm dự án, tham gia, donate, theo dõi tiến độ, báo cáo vi phạm |
| Manager | Đại diện tổ chức | Tạo dự án, quản lý thành viên, đăng progress, nộp giải ngân |
| Admin | Quản trị hệ thống | Duyệt dự án, duyệt giải ngân, xử lý gian lận, quản lý AI |

## 3. Ràng buộc cốt lõi
- Donate bắt buộc đăng nhập để map giao dịch về `user_id`.
- Dự án chỉ duyệt 1 lần khi tạo; các lần chỉnh sửa ghi log và notify Admin.
- Feature snapshot bất biến, không update sau insert.
- Isolation Forest không dùng nhãn Admin; Fraud Classifier mới dùng nhãn.
- Webhook SePay bắt buộc HMAC + idempotency.

## 4. Phân rã domain
- Identity & Access
- Organization Onboarding
- Project Lifecycle
- Participation
- Donations & Payments
- Statement Transparency
- Progress Updates
- Disbursement
- Fraud AI
- Reporting & Complaints
- Notifications
- Admin Governance
