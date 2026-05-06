# Module 06: Minh bạch sao kê

## 1. Phạm vi
Công khai sao kê giao dịch theo thời gian gần thực, hỗ trợ truy vết nguồn tiền và công khai lịch sử thay đổi dự án liên quan.

## 2. Chính sách hiển thị giao dịch
- Hiển thị: `NORMAL`, `SUSPICIOUS` (kèm nhãn cảnh báo).
- Ẩn: `CONFIRMED_FRAUD`, `REVERSED`.
- Giao dịch chưa đối soát (`UNMATCHED`) không hiển thị công khai cho tới khi được xử lý.

## 3. Core entities
- `statement_views(...)`
- `statement_exports(...)`

## 4. Yêu cầu hiển thị công khai
- Sao kê phải thể hiện tối thiểu: thời gian, số tiền, trạng thái, nội dung chuyển khoản đã mask thông tin nhạy cảm.
- Cho phép lọc theo mốc thời gian và trạng thái.
- Dữ liệu phải nhất quán với trạng thái dự án (đang huy động / đã dừng nhận quyên góp / đã giải ngân).

## 5. API
- `GET /api/projects/{id}/statement`
- `GET /api/projects/{id}/statement/export?format=csv|xlsx`
