# Module 04: Tham gia dự án

## Phạm vi
Quản lý join request, xét duyệt thành viên và phân vai trò nhóm dự án.

## Core entities
- `project_join_requests(...)`
- `project_members(...)`

## Business flow
1. Tình nguyện viên gửi join request cho dự án `VOLUNTEER`.
2. Trạng thái request: `PENDING | APPROVED | REJECTED | WITHDRAWN`.
3. Manager/Sub-manager xét duyệt.
4. Approve -> tạo member active.
5. Manager có thể promote/demote `SUB_MANAGER`.

## Ràng buộc
- Mỗi `(project_id,user_id)` chỉ có một request pending.
- User bị block không được tham gia.
- Vai trò manager của dự án là duy nhất.
- Sub-manager không được đổi manager.

## API
- `POST /api/projects/{id}/join-requests`
- `POST /api/projects/{id}/join-requests/{requestId}/approve`
- `POST /api/projects/{id}/join-requests/{requestId}/reject`
- `POST /api/projects/{id}/join-requests/{requestId}/withdraw`
- `PUT /api/projects/{id}/members/{memberId}/role`
