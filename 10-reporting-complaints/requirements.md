# Module 10: Reporting & Complaints — Báo cáo vi phạm

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | User | Tôi muốn báo cáo dự án có dấu hiệu gian lận. |
| US-02 | User | Tôi muốn báo cáo giao dịch đáng ngờ trên sao kê. |
| US-03 | Admin | Tôi muốn xử lý báo cáo và phản hồi kết quả. |

## 2. Business Logic

### 2.1 Tạo báo cáo
- User chọn loại báo cáo: `PROJECT`, `TRANSACTION`, `UPDATE`.
- Lưu `report` với mô tả và reference id.

### 2.2 Xử lý báo cáo
- Admin review: confirm hoặc dismiss.
- Kết quả gửi thông báo cho user đã báo cáo.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| description | 20–2000 ký tự |
| oneActive | 1 user chỉ có 1 report open cho cùng reference |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/reports` | `ReportService.create()` | Tạo báo cáo | Yes | `{ type, referenceId, description }` | `ReportDto` |
| GET | `/api/admin/reports` | `ReportService.getAll()` | DS báo cáo | Yes (Admin) | Query: `status, page, limit` | `PaginatedReportDto` |
| POST | `/api/admin/reports/:id/resolve` | `ReportService.resolve()` | Xử lý báo cáo | Yes (Admin) | `{ action, note }` | `ReportDto` |

### Response DTO Definitions

**ReportDto**
```json
{
  "id": "Long",
  "type": "PROJECT | TRANSACTION | UPDATE",
  "referenceId": "Long",
  "description": "string",
  "status": "OPEN | RESOLVED",
  "createdAt": "DateTime"
}
```

**PaginatedReportDto**
```json
{
  "reports": "ReportDto[]",
  "total": "number"
}
```
