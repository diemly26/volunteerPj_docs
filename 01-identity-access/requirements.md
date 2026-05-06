# Module 01: Định danh & Phân quyền

## 1. Phạm vi
Quản lý toàn bộ vòng đời tài khoản người dùng gồm đăng ký, xác minh email, đăng nhập, cấp và làm mới token, đăng xuất và kiểm soát quyền truy cập theo vai trò.

Mục tiêu nghiệp vụ:
- Chỉ cho phép người dùng đã xác minh và đang hoạt động truy cập các nghiệp vụ chính.
- Giảm rủi ro chiếm quyền tài khoản qua cơ chế OTP, token rotation và phát hiện reuse token.
- Tạo nền tảng phân quyền rõ ràng cho các module còn lại.

## 2. Core entities
- `users(id, email, password_hash, full_name, role, status, email_verified_at, failed_login_count, last_login_at, created_at, updated_at)`
- `otp_challenges(id, email, purpose, code_hash, expires_at, attempt_count, consumed_at, created_at)`
- `refresh_tokens(id, user_id, token_hash, rotated_from_id, expires_at, revoked_at, ip_address, user_agent, created_at)`
- `auth_audit_logs(id, user_id, action, result, ip_address, user_agent, reason, created_at)`

## 3. Luồng nghiệp vụ
### 3.1 Đăng ký và xác minh email
1. Người dùng gửi thông tin đăng ký hợp lệ.
2. Hệ thống tạo user trạng thái `UNVERIFIED`.
3. Hệ thống tạo OTP mới (TTL 10 phút) và gửi email xác minh.
4. Người dùng nhập OTP để xác minh.
5. Nếu OTP hợp lệ, user chuyển `ACTIVE`; nếu sai quá số lần cho phép, OTP bị khóa.

### 3.2 Đăng nhập
1. Người dùng nhập email/mật khẩu.
2. Hệ thống kiểm tra thông tin đăng nhập và trạng thái tài khoản.
3. Nếu hợp lệ, cấp `access_token` ngắn hạn và `refresh_token` dài hạn.
4. Ghi nhật ký đăng nhập phục vụ truy vết bảo mật.

### 3.3 Làm mới token (refresh)
1. Client gửi `refresh_token` còn hiệu lực.
2. Hệ thống kiểm tra token hash, trạng thái revoke và quan hệ token family.
3. Nếu hợp lệ, hệ thống revoke token cũ và phát token mới (rotation bắt buộc).
4. Nếu phát hiện reuse token đã revoke, hệ thống revoke toàn bộ token family và yêu cầu đăng nhập lại.

### 3.4 Đăng xuất
- Đăng xuất thiết bị hiện tại: revoke refresh token hiện tại.
- Đăng xuất toàn bộ thiết bị: revoke toàn bộ refresh token đang còn hiệu lực của user.

## 4. Quy tắc và ràng buộc
- `email` là duy nhất toàn hệ thống.
- Mật khẩu tối thiểu 10 ký tự, bao gồm chữ hoa, chữ thường, số và ký tự đặc biệt.
- OTP chỉ dùng một lần, lưu hash, không lưu mã thô.
- Giới hạn thử OTP: tối đa 5 lần/phiên OTP.
- Giới hạn đăng nhập thất bại theo IP và theo tài khoản để giảm brute-force.
- User `BLOCKED` không được đăng nhập và không được tạo session cho các nghiệp vụ tài chính.

## 5. Yêu cầu kỹ thuật triển khai
- JWT phải chứa tối thiểu: `sub`, `role`, `iat`, `exp`, `jti`.
- Tất cả timestamp dùng UTC.
- Token và OTP bắt buộc lưu dạng hash (không lưu plain text).
- Cấu hình thời hạn token/OTP đặt trong `system_config` để có thể thay đổi không cần sửa code.
- Bắt buộc ghi `auth_audit_logs` cho các sự kiện nhạy cảm: đăng nhập thất bại, đổi mật khẩu, refresh thất bại, phát hiện token reuse.

## 6. Edge cases
- OTP hết hạn nhưng user tiếp tục nhập: trả lỗi rõ ràng, cho phép yêu cầu OTP mới.
- Người dùng yêu cầu OTP mới nhiều lần: OTP cũ tự vô hiệu.
- Tài khoản chưa xác minh nhưng cố đăng nhập: từ chối và gợi ý xác minh lại email.
- Token refresh gửi lặp do mạng chập chờn: xử lý idempotent ở tầng API để tránh cấp chồng token.

## 7. API
- `POST /api/auth/register`
- `POST /api/auth/verify-email`
- `POST /api/auth/resend-otp`
- `POST /api/auth/login`
- `POST /api/auth/refresh-token`
- `POST /api/auth/logout`
- `POST /api/auth/logout-all`
- `GET /api/auth/me`
