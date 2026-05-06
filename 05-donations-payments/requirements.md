# Module 05: Quyên góp & Thanh toán

## Phạm vi
Tạo donation session, đối soát thanh toán, tiếp nhận webhook và lưu giao dịch.

## Core entities
- `donation_sessions(...)`
- `transactions(...)`
- `webhook_events(...)`

## Business flow
1. User đã xác thực tạo session TTL 15 phút.
2. Client tạo QR từ session code.
3. Hệ thống nhận bank webhook.
4. Verify `signature + timestamp`, chặn replay.
5. Idempotency theo provider event id và `bank_reference`.
6. Match theo `session_code`, amount, currency.
7. Lưu transaction `RECEIVED` rồi fraud scoring bất đồng bộ.
8. Cập nhật session `MATCHED` hoặc `UNMATCHED`.

## Quy tắc
- Webhook ACK nhanh; xử lý nặng vào queue worker.
- Lệch số tiền -> `UNMATCHED` + manual review queue.
- Callback trùng trả 200 + `duplicate`.

## API
- `POST /api/donations/sessions`
- `POST /api/payments/sepay/webhook`
- `GET /api/donations/sessions/{id}`
