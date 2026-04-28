# Module 02: Organization Onboarding — Đăng ký tổ chức

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Manager | Tôi muốn đăng ký tổ chức với giấy chứng nhận để được duyệt. |
| US-02 | Admin | Tôi muốn duyệt hoặc từ chối yêu cầu đăng ký tổ chức. |
| US-03 | Admin | Tôi muốn gán nhãn "Tổ chức tin cậy" thủ công. |

## 2. Business Logic

### 2.1 Đăng ký tổ chức
1. Manager nhập thông tin tổ chức và upload giấy chứng nhận.
2. Hệ thống tạo `organization_profile` với `status=PENDING`.
3. Nếu sau 5 ngày chưa duyệt, hồ sơ tự động bị xóa.

### 2.2 Duyệt tổ chức
1. Admin xem danh sách hồ sơ chờ duyệt.
2. Approve → tạo user role `MANAGER`, kích hoạt tài khoản.
3. Reject → lưu lý do, hồ sơ kết thúc.

### 2.3 Tổ chức tin cậy
- Admin cấp/thu hồi nhãn `is_trusted_org` thủ công.
- Dự án mới của tổ chức tin cậy auto `APPROVED`.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| orgName | 3–200 ký tự |
| certificate | Bắt buộc, pdf/jpg/png, tối đa 10MB |
| bankAccount | Bắt buộc, định dạng hợp lệ |
| pendingTTL | 5 ngày |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/organizations/register` | `OrgService.register()` | Gửi hồ sơ tổ chức | No | `multipart/form-data` | `MessageResponseDto` |
| GET | `/api/admin/organizations/pending` | `OrgService.getPending()` | DS hồ sơ chờ duyệt | Yes (Admin) | Query: `page, limit` | `PaginatedOrgDto` |
| POST | `/api/admin/organizations/:id/approve` | `OrgService.approve()` | Duyệt hồ sơ | Yes (Admin) | — | `OrganizationDto` |
| POST | `/api/admin/organizations/:id/reject` | `OrgService.reject()` | Từ chối hồ sơ | Yes (Admin) | `{ reason }` | `OrganizationDto` |
| PUT | `/api/admin/organizations/:id/trusted` | `OrgService.setTrusted()` | Gán/thu hồi trusted | Yes (Admin) | `{ isTrusted }` | `OrganizationDto` |

### Response DTO Definitions

**OrganizationDto**
```json
{
  "id": "Long",
  "orgName": "string",
  "representative": "string",
  "phone": "string",
  "certificateUrl": "string",
  "bankAccount": "string",
  "status": "PENDING | APPROVED | REJECTED",
  "isTrusted": "boolean",
  "createdAt": "DateTime"
}
```

**PaginatedOrgDto**
```json
{
  "organizations": "OrganizationDto[]",
  "total": "number"
}
```

**MessageResponseDto**
```json
{
  "message": "string"
}
```
