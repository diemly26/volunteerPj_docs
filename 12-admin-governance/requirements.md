# Module 12: Admin Governance — Quản trị & Kiểm soát

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Admin | Tôi muốn quản lý queue dự án và báo cáo giải ngân. |
| US-02 | Admin | Tôi muốn quản lý tổ chức tin cậy và block user. |
| US-03 | Admin | Tôi muốn xem audit log của cấu hình AI. |

## 2. Business Logic

### 2.1 Hàng đợi quản trị
- Project approval queue.
- Disbursement review queue.
- Flagged transactions queue.

### 2.2 Block user
- Block → user không đăng nhập được, hiển thị lý do.

### 2.3 AI config audit
- Thay đổi config ghi log đầy đủ.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| auditLog | Immutable, retention 2 năm |
| access | Chỉ Admin |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| GET | `/api/admin/queues` | `AdminQueueService.getAll()` | Tổng hợp queue | Yes (Admin) | — | `AdminQueueDto` |
| PUT | `/api/admin/users/:id/block` | `AdminService.blockUser()` | Block user | Yes (Admin) | `{ reason }` | `UserDto` |
| PUT | `/api/admin/users/:id/unblock` | `AdminService.unblockUser()` | Unblock user | Yes (Admin) | — | `UserDto` |
| GET | `/api/admin/audit-logs` | `AuditLogService.getAll()` | Audit log | Yes (Admin) | Query: `page, limit, action?` | `PaginatedAuditLogDto` |

### Response DTO Definitions

**AdminQueueDto**
```json
{
  "pendingProjects": "number",
  "pendingDisbursements": "number",
  "flaggedTransactions": "number",
  "openReports": "number"
}
```

**PaginatedAuditLogDto**
```json
{
  "logs": "AuditLogDto[]",
  "total": "number"
}
```
