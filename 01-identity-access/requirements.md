# Module 01: Identity & Access — Xác thực & Phân quyền

## 1. User Stories

| ID | Role | Story |
|----|------|-------|
| US-01 | Volunteer | Tôi muốn đăng ký tài khoản và xác thực email bằng OTP. |
| US-02 | Manager | Tôi muốn đăng nhập để quản lý dự án của tổ chức. |
| US-03 | Admin | Tôi muốn phân quyền theo vai trò để bảo vệ tài nguyên quản trị. |
| US-04 | User | Tôi muốn đăng xuất và refresh token an toàn. |

## 2. Business Logic

### 2.1 Đăng ký (Volunteer)
1. User nhập `email`, `password`, `name`.
2. Kiểm tra email chưa tồn tại.
3. Hash password (bcrypt 12).
4. Tạo user với `role=VOLUNTEER`, `status=UNVERIFIED`.
5. Gửi OTP xác thực email (TTL 10 phút).

### 2.2 Xác thực email
1. User nhập OTP.
2. Hệ thống kiểm tra OTP hợp lệ và chưa hết hạn.
3. Chuyển `status=ACTIVE`.

### 2.3 Đăng nhập
1. Kiểm tra email/password.
2. Từ chối nếu `status=UNVERIFIED` hoặc `status=BLOCKED`.
3. Phát hành Access Token (15m) + Refresh Token (7d).

### 2.4 Refresh token
- Token rotation, xóa token cũ, tạo token mới.

### 2.5 Đăng xuất
- Xóa refresh token khỏi DB.

### 2.6 RBAC
- Vai trò hệ thống: `VOLUNTEER`, `MANAGER`, `ADMIN`.
- Middleware `authorize(roles[])` kiểm tra JWT trước mỗi request.

## 3. Validations & Constraints

| Field | Ràng buộc |
|-------|-----------|
| email | Bắt buộc, đúng format, unique |
| password | Tối thiểu 8 ký tự, có chữ hoa + số + ký tự đặc biệt |
| OTP | 6 số, TTL 10 phút |
| Access Token | JWT, hết hạn 15 phút |
| Refresh Token | JWT, hết hạn 7 ngày, lưu DB |
| Rate limit | Login 5/min/IP, OTP 3/min/email |

## 4. API Endpoints

| Method | Endpoint | Service Function | Mô tả | Auth | Input | Response DTO |
|--------|----------|-----------------|-------|------|-------|-------------|
| POST | `/api/auth/register` | `AuthService.register()` | Đăng ký volunteer | No | `{ email, password, name }` | `MessageResponseDto` |
| POST | `/api/auth/verify-otp` | `AuthService.verifyOtp()` | Xác thực email bằng OTP | No | `{ email, otp }` | `MessageResponseDto` |
| POST | `/api/auth/login` | `AuthService.login()` | Đăng nhập | No | `{ email, password }` | `LoginResponseDto` |
| POST | `/api/auth/refresh-token` | `AuthService.refreshToken()` | Refresh token | No | `{ refreshToken }` | `TokenResponseDto` |
| POST | `/api/auth/logout` | `AuthService.logout()` | Đăng xuất | Yes | `{ refreshToken }` | `MessageResponseDto` |
| GET | `/api/auth/me` | `AuthService.getMe()` | Lấy user hiện tại | Yes | — | `UserDto` |

### Response DTO Definitions

**UserDto**
```json
{
  "id": "Long",
  "email": "string",
  "name": "string",
  "role": "VOLUNTEER | MANAGER | ADMIN",
  "status": "UNVERIFIED | ACTIVE | BLOCKED",
  "createdAt": "DateTime"
}
```

**LoginResponseDto**
```json
{
  "accessToken": "string",
  "refreshToken": "string",
  "user": "UserDto"
}
```

**TokenResponseDto**
```json
{
  "accessToken": "string",
  "refreshToken": "string"
}
```

**MessageResponseDto**
```json
{
  "message": "string"
}
```
