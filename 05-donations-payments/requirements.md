# Module 05: Quyên góp & Thanh toán

## 1. Phạm vi
Quản lý phiên quyên góp, xác thực webhook ngân hàng, đối soát giao dịch, định danh giao dịch theo người dùng và phát hiện rủi ro.

## 2. Core entities
- `donation_sessions(id, project_id, user_id, amount_minor, currency, session_code, expires_at, status, matched_transaction_id, ...)`
- `transactions(id, project_id, user_id, amount_minor, currency, bank_reference, transfer_content, received_at, status, risk_score, ...)`
- `webhook_events(id, provider, event_id, signature, payload_hash, received_at, processed_at, result, ...)`

## 3. Luồng nghiệp vụ
1. User đã xác thực tạo `donation_session` (TTL 15 phút).
2. Client tạo QR thanh toán có chứa `session_code`.
3. Ngân hàng gửi webhook về hệ thống.
4. Hệ thống xác thực webhook và kiểm tra idempotency.
5. Hệ thống đối soát giao dịch với `donation_session`.
6. Nếu match thành công, giao dịch được gán đúng `user_id` và `project_id`.
7. Giao dịch được chấm điểm rủi ro, sau đó cập nhật sao kê.

## 4. Định danh giao dịch theo người dùng
Nguyên tắc định danh:
- Một lần bấm quyên góp tạo một phiên riêng (`donation_session`) gắn với `user_id`.
- `session_code` là khóa đối soát duy nhất trong nội dung thanh toán.
- Khi webhook đến, hệ thống map `session_code -> donation_session -> user_id`.
- Nếu không map được thì ghi `UNMATCHED`, đưa vào luồng xử lý thủ công, không tự gán sai người dùng.

Ràng buộc dữ liệu:
- `session_code` unique.
- `bank_reference` unique.
- Một transaction chỉ match tối đa một donation session.

## 5. Kỹ thuật webhook ngân hàng
Yêu cầu kỹ thuật bắt buộc:
- **Xác thực nguồn gửi:** kiểm tra chữ ký HMAC theo secret dùng chung.
- **Chống replay:** dùng timestamp + nonce, từ chối payload quá hạn thời gian cho phép.
- **Idempotency:** xử lý lặp an toàn bằng `event_id` và `bank_reference`.
- **ACK nhanh:** trả phản hồi nhanh cho ngân hàng; xử lý nặng chuyển sang queue worker.
- **Audit đầy đủ:** lưu payload hash, kết quả xác thực, kết quả xử lý để truy vết.

Mẫu phản hồi webhook:
- `ok`: xử lý mới thành công.
- `duplicate`: webhook trùng, không ghi thêm dữ liệu.
- `invalid_signature`: từ chối do chữ ký không hợp lệ.

## 6. Quy tắc nghiệp vụ bổ sung
- Dự án đã đạt ngưỡng huy động hoặc hết hạn kêu gọi thì không cho tạo session mới.
- Lệch số tiền so với session kỳ vọng -> `UNMATCHED`.
- Callback trùng phải an toàn và không được tạo bản ghi transaction mới.

## 7. API
- `POST /api/donations/sessions`
- `GET /api/donations/sessions/{id}`
- `POST /api/payments/sepay/webhook`
