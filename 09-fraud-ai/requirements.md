# Module 09: Phát hiện gian lận & AI Ops

## 1. Phạm vi
Phát hiện giao dịch bất thường theo pipeline hai tầng, quản lý vòng đời mô hình và tái huấn luyện từ kết luận của Admin.

## 2. Core entities
- `transaction_features(...)` (append-only)
- `fraud_scores(...)`
- `transaction_labels(...)`
- `model_registry(...)`

## 3. Decision pipeline
1. Khi nhận transaction, hệ thống tạo feature snapshot bất biến tại event-time.
2. Tầng 1 (Isolation Forest) chấm điểm bất thường không dùng nhãn.
3. Tầng 2 (Fraud Classifier) dự đoán xác suất rủi ro từ dữ liệu có nhãn.
4. Hệ thống hợp nhất kết quả 2 tầng để ra quyết định `NORMAL`/`SUSPICIOUS`.
5. Giao dịch `SUSPICIOUS` vào hàng đợi Admin review.

## 4. Vòng phản hồi và tái huấn luyện
- Admin review từng giao dịch và gắn nhãn kết luận.
- Nhãn được lưu versioned, không overwrite.
- Dữ liệu nhãn mới được đưa vào pipeline train offline.
- Mô hình mới phải qua kiểm tra chất lượng trước khi promote sang ACTIVE.

## 5. Guardrails
- Không rò rỉ nhãn vào tầng unsupervised.
- Train/validation tách theo thời gian.
- Threshold rủi ro quản lý qua `system_config`, có audit và rollback.

## 6. API
- `GET /api/admin/ai/flagged-transactions`
- `POST /api/admin/ai/transactions/{id}/label`
- `GET /api/admin/ai/models`
- `POST /api/admin/ai/models/{id}/promote`
- `POST /api/admin/ai/models/{id}/rollback`
