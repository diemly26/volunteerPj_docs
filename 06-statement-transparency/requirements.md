# Module 06: Statement Transparency — Sao kê công khai

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | User | Tôi muốn xem sao kê công khai theo thời gian thực. |
| US-02 | Manager | Tôi muốn xuất file sao kê để báo cáo. |
| US-03 | Admin | Tôi muốn ẩn giao dịch gian lận khỏi sao kê. |

## 2. Business Logic

### 2.1 Sao kê công khai
- Hiển thị: tên người gửi, số tiền, thời gian, nội dung chuyển khoản.
- Giao dịch `SUSPICIOUS` hiển thị cờ cảnh báo.

### 2.2 Xử lý gian lận
- Khi Admin xác nhận gian lận → `is_on_statement=false`.

### 2.3 Xuất file
- Manager xuất CSV/Excel theo dự án.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| export | Tối đa 20,000 dòng/lần |
| public | Không hiển thị giao dịch `ABNORMAL` |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| GET | `/api/projects/:id/statement` | `StatementService.getPublicStatement()` | Sao kê công khai | No | Query: `page, limit` | `PaginatedStatementDto` |
| GET | `/api/projects/:id/statement/export` | `StatementService.export()` | Xuất sao kê | Yes (Manager) | Query: `format=csv|xlsx` | Binary |

### Response DTO Definitions

**StatementItemDto**
```json
{
  "id": "Long",
  "senderName": "string",
  "amount": "number",
  "transferContent": "string",
  "createdAt": "DateTime",
  "status": "NORMAL | SUSPICIOUS"
}
```

**PaginatedStatementDto**
```json
{
  "items": "StatementItemDto[]",
  "total": "number"
}
```
