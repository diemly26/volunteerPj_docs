# Module 11: Thông báo

## Phạm vi
Thông báo in-app và email từ domain events, có điều khiển kênh gửi và retry.

## Core entities
- `notifications(...)`
- `notification_deliveries(...)`
- `notification_preferences(...)`

## Event-driven flow
Domain module phát event -> Notification service render payload + kiểm tra preferences -> tạo in-app và/hoặc enqueue email job -> retry bằng exponential backoff.

## API
- `GET /api/notifications`
- `PUT /api/notifications/{id}/read`
- `PUT /api/notifications/read-all`
- `GET /api/notifications/preferences`
- `PUT /api/notifications/preferences`
