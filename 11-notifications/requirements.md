# Module 11: Notifications — Thông báo hệ thống

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | User | Tôi muốn nhận thông báo trong web cho các sự kiện quan trọng. |
| US-02 | User | Tôi muốn nhận email với cảnh báo quá hạn. |
| US-03 | Admin | Tôi muốn nhận thông báo khi có việc cần duyệt. |

## 2. Business Logic

### 2.1 Loại thông báo chính
- Volunteer: approve/reject join, donate success, report result.
- Manager: project approval, disbursement deadlines, report decision.
- Admin: new project, edit log, new disbursement, flagged transactions.

### 2.2 Pipeline
- Module nguồn emit event.
- Notification Service tạo in-app + email (nếu cần).

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| retention | Lưu 90 ngày |
| email | Rate limit 10 email/user/giờ |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| GET | `/api/notifications` | `NotificationService.getAll()` | DS thông báo | Yes | Query: `page, limit` | `PaginatedNotificationsDto` |
| PUT | `/api/notifications/:id/read` | `NotificationService.markRead()` | Đánh dấu đã đọc | Yes | — | `NotificationDto` |

### Response DTO Definitions

**NotificationDto**
```json
{
  "id": "Long",
  "type": "string",
  "title": "string",
  "body": "string",
  "isRead": "boolean",
  "createdAt": "DateTime"
}
```

**PaginatedNotificationsDto**
```json
{
  "notifications": "NotificationDto[]",
  "total": "number"
}
```
