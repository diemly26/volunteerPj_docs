# Module 09: Phát hiện gian lận & AI Ops

## Phạm vi
Feature snapshot cho transaction, risk scoring, admin labeling và model lifecycle.

## Core entities
- `transaction_features(...)` (append-only)
- `fraud_scores(...)`
- `transaction_labels(...)`
- `model_registry(...)`

## Decision pipeline
Ingest transaction -> feature snapshot bất biến tại event-time -> Isolation Forest + Fraud Classifier -> ensemble quyết định `NORMAL/SUSPICIOUS` -> hàng đợi admin review.

## Guardrails
- Không rò rỉ nhãn ở tầng 1.
- Train/validation tách theo thời gian.
- Chỉnh nhãn bằng version mới, không overwrite.
- Threshold nằm trong `system_config` và có audit + rollback.

## API
- `GET /api/admin/ai/flagged-transactions`
- `POST /api/admin/ai/transactions/{id}/label`
- `GET /api/admin/ai/models`
- `POST /api/admin/ai/models/{id}/promote`
- `POST /api/admin/ai/models/{id}/rollback`
