# Module 11: Thông báo

## 1. Phạm vi
Thông báo in-app và email theo sự kiện nghiệp vụ, bao gồm các mốc quyên góp, dừng huy động, cảnh báo rủi ro và kết quả giải ngân.

## 2. Core entities
- `notifications(...)`
- `notification_deliveries(...)`
- `notification_preferences(...)`

## 3. Event-driven flow
Domain module phát event -> Notification service dựng nội dung -> kiểm tra preferences -> gửi in-app/email -> retry có kiểm soát.

## 4. Sự kiện thông báo quan trọng
- Dự án được duyệt/từ chối.
- Dự án đạt ngưỡng huy động hoặc hết hạn kêu gọi (dừng nhận donate).
- Giao dịch đóng góp thành công.
- Giao dịch bị đưa vào diện cần rà soát.
- Báo cáo giải ngân được duyệt và công khai.

## 5. API
- `GET /api/notifications`
- `PUT /api/notifications/{id}/read`
- `PUT /api/notifications/read-all`
- `GET /api/notifications/preferences`
- `PUT /api/notifications/preferences`
