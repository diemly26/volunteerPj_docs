# Module 03: Project Lifecycle — Vòng đời dự án

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Manager | Tôi muốn tạo dự án mới (volunteer hoặc donation). |
| US-02 | Admin | Tôi muốn duyệt dự án lần đầu. |
| US-03 | User | Tôi muốn xem lịch sử chỉnh sửa công khai của dự án. |
| US-04 | Manager | Tôi muốn chỉnh sửa dự án sau khi duyệt mà không phải xin duyệt lại. |

## 2. Business Logic

### 2.1 Tạo dự án
1. Manager chọn `projectType` (volunteer hoặc money_donation).
2. Nhập thông tin cơ bản và ngân hàng nhận donate (nếu money_donation).
3. Nếu tổ chức trusted → `status=APPROVED` ngay.
4. Nếu không → `status=PENDING` và gửi notify Admin.

### 2.2 Duyệt dự án
- Admin duyệt 1 lần: `APPROVED` hoặc `REJECTED` kèm lý do.

### 2.3 Chỉnh sửa dự án
- Manager có thể cập nhật bất kỳ lúc nào sau khi `APPROVED`.
- Mọi chỉnh sửa ghi `project_edit_history` (before/after snapshot).
- Admin nhận notification để theo dõi.

### 2.4 Thay đổi tài khoản ngân hàng
- Cho phép đổi sau khi dự án hoạt động.
- Ghi log riêng, hiển thị công khai với timestamp.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| projectType | `VOLUNTEER` hoặc `MONEY_DONATION` |
| title | 10–200 ký tự |
| description | 50–5000 ký tự |
| bankAccount | Bắt buộc nếu `MONEY_DONATION` |
| approval | Chỉ duyệt lần đầu |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/projects` | `ProjectService.create()` | Tạo dự án | Yes (Manager) | `{ ... }` | `ProjectDto` |
| PUT | `/api/projects/:id` | `ProjectService.update()` | Cập nhật dự án | Yes (Manager) | `{ ... }` | `ProjectDto` |
| GET | `/api/projects/:id` | `ProjectService.getDetail()` | Chi tiết dự án | No | — | `ProjectDetailDto` |
| GET | `/api/projects/:id/history` | `ProjectService.getEditHistory()` | Lịch sử chỉnh sửa | No | — | `ProjectEditHistoryDto[]` |
| POST | `/api/admin/projects/:id/approve` | `ProjectService.approve()` | Duyệt dự án | Yes (Admin) | — | `ProjectDto` |
| POST | `/api/admin/projects/:id/reject` | `ProjectService.reject()` | Từ chối dự án | Yes (Admin) | `{ reason }` | `ProjectDto` |

### Response DTO Definitions

**ProjectDto**
```json
{
  "id": "Long",
  "managerId": "Long",
  "projectType": "VOLUNTEER | MONEY_DONATION",
  "title": "string",
  "status": "PENDING | APPROVED | REJECTED | LOCKED",
  "bankAccount": "string | null",
  "createdAt": "DateTime"
}
```

**ProjectEditHistoryDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "editedBy": "Long",
  "beforeJson": "object",
  "afterJson": "object",
  "createdAt": "DateTime"
}
```
