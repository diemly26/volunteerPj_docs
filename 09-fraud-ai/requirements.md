# Module 09: Fraud AI — Phát hiện giao dịch bất thường

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Admin | Tôi muốn xem danh sách giao dịch bị AI flag. |
| US-02 | Admin | Tôi muốn quản lý lifecycle model (staging/active/previous). |
| US-03 | System | Tôi muốn đảm bảo feature snapshot bất biến. |

## 2. Business Logic

### 2.1 Feature snapshot
- Tính 7 feature tại thời điểm giao dịch.
- Lưu vào `transaction_features` và không update sau insert.

### 2.2 Hybrid 2 tầng
- Tầng 1: Isolation Forest (unsupervised).
- Tầng 2: Fraud Classifier (supervised) dùng nhãn Admin.

### 2.3 Labeling
- Admin review quyết định `is_fraud`.
- Label lưu vào `transaction_labels`, UNIQUE theo `transaction_id`.

### 2.4 Model lifecycle
- STAGING → ACTIVE → PREVIOUS/REJECTED.
- Rollback 1 thao tác.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| labels | 1 giao dịch chỉ có 1 label |
| retrain | Offline, không block realtime |
| thresholds | Lấy từ `system_config` |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| GET | `/api/admin/ai/flagged-transactions` | `AiService.getFlagged()` | DS flagged queue | Yes (Admin) | Query: `page, limit` | `PaginatedTransactionDto` |
| POST | `/api/admin/ai/transactions/:id/label` | `AiService.label()` | Gắn nhãn giao dịch | Yes (Admin) | `{ isFraud, source }` | `TransactionLabelDto` |
| GET | `/api/admin/ai/models` | `ModelRegistryService.getAll()` | DS model | Yes (Admin) | — | `ModelRegistryDto[]` |
| POST | `/api/admin/ai/models/:id/promote` | `ModelRegistryService.promote()` | Promote model | Yes (Admin) | — | `ModelRegistryDto` |
| POST | `/api/admin/ai/models/:id/rollback` | `ModelRegistryService.rollback()` | Rollback | Yes (Admin) | `{ reason }` | `ModelRegistryDto` |

### Response DTO Definitions

**TransactionLabelDto**
```json
{
  "id": "Long",
  "transactionId": "Long",
  "isFraud": "boolean",
  "source": "AI_FLAGGED | ADMIN_INITIATED | USER_REPORT",
  "createdAt": "DateTime"
}
```

**ModelRegistryDto**
```json
{
  "id": "Long",
  "status": "STAGING | ACTIVE | PREVIOUS | REJECTED",
  "precision": "number",
  "recall": "number",
  "f1": "number",
  "createdAt": "DateTime"
}
```
