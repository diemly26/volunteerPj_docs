# Module 01: Định danh & Phân quyền

## Phạm vi
Quản lý đăng ký, xác minh email, đăng nhập, refresh token rotation và role-based authorization.

## Core entities
- `users(id,email,password_hash,name,role,status,email_verified_at,created_at)`
- `otp_challenges(id,email,code_hash,purpose,expires_at,attempt_count,consumed_at)`
- `refresh_tokens(id,user_id,token_hash,expires_at,revoked_at,rotated_from_id,ip,user_agent)`

## Business flow
1. Đăng ký: tạo user `UNVERIFIED`, tạo OTP TTL 10 phút và gửi email.
2. Xác minh email: dùng OTP mới nhất chưa dùng; tối đa 5 lần sai; thành công chuyển `ACTIVE`.
3. Đăng nhập: từ chối nếu `UNVERIFIED/BLOCKED`; cấp access token 15 phút + refresh token 7 ngày.
4. Refresh token: bắt buộc rotation; phát hiện reuse token đã revoke thì revoke toàn bộ token family.
5. Đăng xuất: revoke refresh token hiện tại; hỗ trợ global logout.

## Ràng buộc
- Password tối thiểu 10 ký tự, có chữ hoa/chữ thường/số/ký tự đặc biệt.
- Rate limit: login 5/phút/IP + 20/ngày/tài khoản; OTP 3/giờ/email.
- Chỉ lưu hash cho token/OTP.
- JWT phải có `sub`, `role`, `iat`, `exp`, `jti`.

## API
- `POST /api/auth/register`
- `POST /api/auth/verify-email`
- `POST /api/auth/login`
- `POST /api/auth/refresh-token`
- `POST /api/auth/logout`
- `GET /api/auth/me`
