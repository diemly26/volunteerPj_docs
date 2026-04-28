# Module 05: Donations & Payments — Quyên góp & Thanh toán

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Volunteer | Tôi muốn donate qua QR nhanh chóng và có tracking rõ ràng. |
| US-02 | Admin | Tôi muốn webhook SePay an toàn và idempotent. |
| US-03 | Manager | Tôi muốn mọi giao dịch được ghi nhận real-time. |

## 2. Business Logic

### 2.1 Tạo donation session
1. User nhập số tiền, hệ thống tạo `donation_session` TTL 15 phút.
2. Session chứa `session_code` dùng trong nội dung chuyển khoản.

### 2.2 VietQR
- Frontend gọi VietQR API để tạo ảnh QR với `session_code`.

### 2.3 SePay webhook
1. Verify HMAC.
2. Idempotency check qua `bank_reference` UNIQUE.
3. Parse `session_code`, map `user_id` nếu có.
4. Persist `transaction`.
5. Gọi Fraud AI để phân loại `NORMAL`/`SUSPICIOUS`.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| donation_session | TTL 15 phút |
| bank_reference | UNIQUE |
| HMAC | Bắt buộc verify trước mọi xử lý |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/donations/sessions` | `DonationService.createSession()` | Tạo session donate | Yes | `{ projectId, amount }` | `DonationSessionDto` |
| POST | `/api/payments/sepay/webhook` | `PaymentWebhook.handle()` | Nhận callback SePay | No | Raw payload | `WebhookResponseDto` |

### Response DTO Definitions

**DonationSessionDto**
```json
{
  "id": "Long",
  "projectId": "Long",
  "userId": "Long",
  "amount": "number",
  "sessionCode": "string",
  "expiresAt": "DateTime",
  "status": "PENDING | MATCHED | EXPIRED | UNMATCHED"
}
```

**WebhookResponseDto**
```json
{
  "status": "ok | duplicate",
  "transactionId": "Long | null"
}
```
