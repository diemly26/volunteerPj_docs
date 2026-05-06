# Module 08: Giải ngân

## 1. Phạm vi
Quản lý báo cáo sử dụng quỹ khi dự án kết thúc huy động, quy trình kiểm duyệt và công khai kết quả hoạt động.

## 2. Core entities
- `disbursement_cycles(id, project_id, due_at, status, extended_until, violation_level, ...)`
- `disbursement_reports(id, cycle_id, total_received_minor, total_spent_minor, remaining_minor, status, submitted_at, approved_at, reject_reason, ...)`
- `disbursement_items(id, report_id, category, amount_minor, description, proof_url, outcome_note, ...)`

## 3. Luồng nghiệp vụ
1. Dự án dừng nhận quyên góp -> tạo `disbursement_cycle` trạng thái `DUE`.
2. Manager tạo báo cáo nháp (`DRAFT`) và nộp (`SUBMITTED`).
3. Admin kiểm tra chứng từ/khoản chi/kết quả hoạt động.
4. Admin duyệt (`APPROVED`) hoặc từ chối (`REJECTED`, bắt buộc lý do).
5. Khi duyệt, dự án chuyển `COMPLETED` và phát hành thông tin giải ngân công khai.

## 4. Nội dung công khai bắt buộc khi dự án kết thúc
Báo cáo công khai tối thiểu phải có:
- Tổng số tiền đã huy động.
- Danh sách khoản chi (hạng mục, số tiền, mô tả).
- Chứng từ cho từng khoản chi.
- Số tiền còn lại (nếu có) và phương án xử lý.
- Kết quả hoạt động của dự án sau giải ngân (đầu ra/tác động thực tế).

## 5. Quy tắc tuân thủ
- Nhắc hạn T-7/T-3/T-1.
- Quá hạn -> `OVERDUE`.
- Gia hạn tối đa 1 lần, 3 ngày.
- Hết hạn sau gia hạn -> `VIOLATED`.

## 6. Ràng buộc dữ liệu
- `sum(disbursement_items.amount_minor) + remaining_minor = total_received_minor`.
- Mỗi khoản chi bắt buộc có chứng từ hợp lệ.
- Không cho duyệt báo cáo nếu thiếu khoản chi bắt buộc hoặc thiếu bằng chứng.

## 7. API
- `POST /api/projects/{id}/disbursement/report`
- `POST /api/projects/{id}/disbursement/report/submit`
- `POST /api/admin/disbursements/{id}/approve`
- `POST /api/admin/disbursements/{id}/reject`
- `POST /api/admin/disbursements/{id}/extend`
