# Module 04: Participation — Tham gia dự án

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Volunteer | Tôi muốn gửi yêu cầu tham gia dự án tình nguyện. |
| US-02 | Manager | Tôi muốn duyệt/từ chối yêu cầu tham gia. |
| US-03 | Manager | Tôi muốn gán Sub-manager cho thành viên. |

## 2. Business Logic

### 2.1 Gửi yêu cầu tham gia
1. Volunteer bấm "Tham gia".
2. Tạo `project_request` với trạng thái `PENDING`.

### 2.2 Duyệt yêu cầu
- Manager/Sub-manager Approve → tạo `project_member` role `MEMBER`.
- Reject → lưu lý do (optional).

### 2.3 Quản lý vai trò
- Manager có thể gán/thu hồi `SUB_MANAGER` cho thành viên.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| request | 1 user chỉ có 1 request pending / project |
| role | `MANAGER` duy nhất / project |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/projects/:id/join` | `ParticipationService.requestJoin()` | Gửi yêu cầu tham gia | Yes (Volunteer) | — | `ProjectRequestDto` |
| POST | `/api/projects/:id/requests/:requestId/approve` | `ParticipationService.approve()` | Duyệt yêu cầu | Yes (Manager/Sub) | — | `ProjectMemberDto` |
| POST | `/api/projects/:id/requests/:requestId/reject` | `ParticipationService.reject()` | Từ chối | Yes (Manager/Sub) | `{ reason? }` | `ProjectRequestDto` |
| PUT | `/api/projects/:id/members/:memberId/role` | `ParticipationService.assignRole()` | Gán role | Yes (Manager) | `{ role }` | `ProjectMemberDto` |

### Response DTO Definitions

**ProjectRequestDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "userId": "Long",
  "status": "PENDING | APPROVED | REJECTED",
  "reason": "string | null",
  "createdAt": "DateTime"
}
```

**ProjectMemberDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "userId": "Long",
  "role": "MANAGER | SUB_MANAGER | MEMBER",
  "status": "ACTIVE | LEFT",
  "joinedAt": "DateTime"
}
```
