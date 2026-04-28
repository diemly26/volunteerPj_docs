# Module 07: Progress Updates — Cập nhật hoạt động

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Manager | Tôi muốn đăng cập nhật hoạt động kèm ảnh. |
| US-02 | User | Tôi muốn xem lịch sử chỉnh sửa công khai. |
| US-03 | Admin | Tôi muốn xử lý báo cáo bài viết sai lệch. |

## 2. Business Logic

### 2.1 Đăng bài cập nhật
- Manager tạo bài viết: tiêu đề, nội dung, ảnh.
- Hiển thị ngay, không cần duyệt.

### 2.2 Chỉnh sửa/xóa
- Manager có thể sửa hoặc xóa.
- Lưu lịch sử chỉnh sửa.

### 2.3 Bài giải ngân tự động
- Khi báo cáo giải ngân được duyệt, hệ thống tạo update `is_disbursement_result=true`.
- Manager chỉ thêm ảnh, không sửa phần tài chính.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| title | 5–200 ký tự |
| content | 20–5000 ký tự |
| images | Tối đa 10 ảnh, mỗi ảnh ≤ 5MB |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/projects/:id/updates` | `UpdateService.create()` | Tạo update | Yes (Manager) | `multipart/form-data` | `ProjectUpdateDto` |
| PUT | `/api/projects/:id/updates/:updateId` | `UpdateService.update()` | Cập nhật | Yes (Manager) | `{ title?, content? }` | `ProjectUpdateDto` |
| DELETE | `/api/projects/:id/updates/:updateId` | `UpdateService.delete()` | Xóa update | Yes (Manager) | — | `MessageResponseDto` |
| GET | `/api/projects/:id/updates` | `UpdateService.getList()` | DS updates công khai | No | Query: `page, limit` | `PaginatedUpdatesDto` |

### Response DTO Definitions

**ProjectUpdateDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "title": "string",
  "content": "string",
  "imageUrls": "string[]",
  "isDisbursementResult": "boolean",
  "createdAt": "DateTime"
}
```

**PaginatedUpdatesDto**
```json
{
  "updates": "ProjectUpdateDto[]",
  "total": "number"
}
```

**MessageResponseDto**
```json
{
  "message": "string"
}
```
