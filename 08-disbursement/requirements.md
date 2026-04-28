# Module 08: Disbursement — Báo cáo giải ngân

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Manager | Tôi muốn nộp báo cáo giải ngân sau khi dự án kết thúc. |
| US-02 | Admin | Tôi muốn duyệt hoặc từ chối báo cáo giải ngân. |
| US-03 | User | Tôi muốn xem kết quả giải ngân công khai. |

## 2. Business Logic

### 2.1 Kích hoạt giải ngân
- Khi dự án hết hạn donate → `status=PENDING_DISBURSEMENT`, đặt `due_date` 14 ngày.
- Gửi thông báo + email cho Manager.

### 2.2 Nộp báo cáo
- Manager nhập từng khoản chi kèm minh chứng.
- Nộp báo cáo → `status=SUBMITTED`.

### 2.3 Duyệt báo cáo
- Admin approve → tạo update "Kết quả giải ngân" và `status=COMPLETED`.
- Admin reject → chọn `extend_3_days` hoặc `suspend`.

### 2.4 Quá hạn
- Nhắc nhở trước 3 ngày.
- Quá hạn: khóa quyền tạo dự án mới, gia hạn 3 ngày.
- Vẫn không nộp: `DISBURSEMENT_VIOLATED`.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| dueDate | 14 ngày mặc định |
| item.proof | Bắt buộc |
| category | food, transport, medical, equipment, relief, other |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/projects/:id/disbursement` | `DisbursementService.create()` | Tạo báo cáo nháp | Yes (Manager) | `{ items[], remaining }` | `DisbursementDto` |
| POST | `/api/projects/:id/disbursement/submit` | `DisbursementService.submit()` | Nộp báo cáo | Yes (Manager) | — | `DisbursementDto` |
| POST | `/api/admin/disbursement/:id/approve` | `DisbursementService.approve()` | Duyệt | Yes (Admin) | — | `DisbursementDto` |
| POST | `/api/admin/disbursement/:id/reject` | `DisbursementService.reject()` | Từ chối | Yes (Admin) | `{ action, reason }` | `DisbursementDto` |

### Response DTO Definitions

**DisbursementDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "totalReceived": "number",
  "totalSpent": "number",
  "remaining": "number",
  "status": "DRAFT | SUBMITTED | APPROVED | REJECTED",
  "dueDate": "DateTime"
}
```
