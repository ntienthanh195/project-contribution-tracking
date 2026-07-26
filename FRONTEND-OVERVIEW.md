# FRONTEND OVERVIEW

> Bản đồ giao diện rút gọn dành cho toàn team. Chi tiết route, quyền và trạng thái xem tại [SITEMAP.md](./SITEMAP.md).

## 1. Toàn cảnh sản phẩm

```mermaid
flowchart TB
    START["Người dùng"] --> PUBLIC["Public"]
    PUBLIC --> LANDING["Landing page"]
    PUBLIC --> AUTH["Đăng nhập · Đăng ký · Khôi phục"]
    PUBLIC --> INVITE["Chấp nhận lời mời"]

    AUTH --> WORKSPACE["Workspace cá nhân"]
    INVITE --> WORKSPACE

    WORKSPACE --> DASH["Dashboard"]
    WORKSPACE --> PROJECTS["Dự án của tôi"]
    WORKSPACE --> MYTASKS["Task của tôi"]
    WORKSPACE --> CONTRIB["Đóng góp của tôi"]
    WORKSPACE --> NOTI["Thông báo"]

    PROJECTS --> PROJECT["Workspace dự án"]
    PROJECT --> OVERVIEW["Tổng quan"]
    PROJECT --> TASKS["Task board"]
    PROJECT --> MEMBERS["Thành viên"]
    PROJECT --> STATS["Thống kê"]

    TASKS --> DETAIL["Chi tiết task"]
    DETAIL --> PROGRESS["Báo cáo & minh chứng"]
    DETAIL --> REVIEW["Duyệt task"]
    DETAIL --> TRANSFER["Chuyển task"]

    ADMIN["Admin"] --> ADMINUI["Admin workspace"]
    ADMINUI --> USERS["Tài khoản"]
    ADMINUI --> ALLPROJECTS["Toàn bộ dự án"]
    ADMINUI --> LOGS["Nhật ký hệ thống"]
```

## 2. Một giao diện, nội dung theo vai trò

```mermaid
flowchart LR
    PAGE["Cùng một trang dự án"] --> ROLE{"Vai trò hiện tại"}
    ROLE --> OWNER["Owner<br/>Quản lý dự án, thành viên, task, thống kê"]
    ROLE --> MANAGER["Manager<br/>Điều phối task, duyệt, phân công lại"]
    ROLE --> MEMBER["Member<br/>Làm task, báo cáo, gửi minh chứng"]

    OWNER --> GUARD["Kiểm tra quyền cho từng action"]
    MANAGER --> GUARD
    MEMBER --> GUARD

    GUARD --> SHOW["Hiển thị action hợp lệ"]
    GUARD --> HIDE["Ẩn action bị cấm"]
```

| Vai trò | Nhìn thấy đầu tiên | Hành động quan trọng |
|---|---|---|
| Owner | Tiến độ dự án, task chờ duyệt, thành viên | Quản lý dự án, giao task, chuyển quyền |
| Manager | Task chờ duyệt, quá hạn, cần phân công lại | Tạo/giao task, review, chuyển task |
| Member | Task của tôi, deadline, phản hồi reviewer | Báo cáo tiến độ, gửi minh chứng, gửi duyệt |
| Admin | Tài khoản, dự án hệ thống, hoạt động gần đây | Quản lý tài khoản và giám sát hệ thống |

## 3. Khung màn hình chính

```text
┌──────────────────────────────────────────────────────────────────────┐
│ Logo  / Breadcrumb             Search     🔔 Thông báo     Avatar   │
├────────────────┬─────────────────────────────────────────────────────┤
│ Dashboard      │ Tiêu đề trang                           [CTA chính] │
│ Projects       ├─────────────────────────────────────────────────────┤
│ My Tasks       │                                                     │
│ Contributions  │                 NỘI DUNG TRANG                      │
│ Notifications  │                                                     │
│                │ Dashboard · Board · Table · Form · Timeline         │
│ Project menu   │                                                     │
│ ├─ Overview    │                                                     │
│ ├─ Tasks       │                                                     │
│ ├─ Members     │                                                     │
│ └─ Statistics  │                                                     │
└────────────────┴─────────────────────────────────────────────────────┘
```

Trên mobile: sidebar thành menu drawer; điều hướng chính nằm ở thanh dưới; bảng task chuyển thành danh sách card.

## 4. Luồng task quan trọng nhất

```mermaid
stateDiagram-v2
    [*] --> TODO
    TODO --> IN_PROGRESS: Bắt đầu làm
    IN_PROGRESS --> PENDING_REVIEW: Gửi báo cáo + minh chứng
    PENDING_REVIEW --> COMPLETED: Reviewer xác nhận
    PENDING_REVIEW --> REVISION_REQUIRED: Yêu cầu chỉnh sửa
    REVISION_REQUIRED --> IN_PROGRESS: Tiếp tục xử lý
    TODO --> NEEDS_REASSIGNMENT: Người phụ trách rời
    IN_PROGRESS --> NEEDS_REASSIGNMENT: Cần chuyển task
    PENDING_REVIEW --> REVIEW_BLOCKED: Không còn reviewer hợp lệ
    TODO --> CANCELLED: Hủy task
    IN_PROGRESS --> OVERDUE: Quá deadline
```

```text
TASK DETAIL
┌────────────────────────────────────┬─────────────────────────────────┐
│ Mô tả & tiêu chí hoàn thành        │ Assignee · Reviewer · Deadline │
│ Tiến độ hiện tại                   │ Priority · Weight · Status      │
├────────────────────────────────────┼─────────────────────────────────┤
│ Reports · Evidence · Assignments   │ Cập nhật tiến độ                │
│ Timeline báo cáo và phản hồi       │ Gửi duyệt / Review / Transfer   │
└────────────────────────────────────┴─────────────────────────────────┘
```

## 5. Các màn hình cần thiết kế trước

| Thứ tự | Màn hình | Route | Kết quả cần nhìn thấy |
|---:|---|---|---|
| 1 | App shell | Toàn workspace | Header, sidebar, responsive navigation |
| 2 | Auth | `/auth/*` | Login, register, recovery, validation |
| 3 | Dashboard | `/dashboard` | Việc cần làm, dự án, deadline, review |
| 4 | Project overview | `/projects/:projectId` | Tiến độ, cảnh báo, thành viên |
| 5 | Task board | `.../tasks` | Trạng thái task và bộ lọc |
| 6 | Task detail | `.../tasks/:taskId` | Tiến độ, báo cáo, minh chứng, action |
| 7 | Review | `.../review` | Submission, feedback, quyết định |
| 8 | Members | `.../members` | Role, status, contribution |
| 9 | Statistics | `.../statistics` | Tiến độ trọng số và đóng góp |
| 10 | Admin | `/admin/*` | Users, projects, logs |

## 6. Quy tắc frontend không được bỏ qua

- Không ai được tự duyệt task của chính mình.
- Owner ở chế độ `MANAGEMENT_ONLY` không được nhận task.
- Dự án `COMPLETED` hoặc `CANCELLED` phải hiển thị ở chế độ chỉ đọc.
- Rời dự án, loại thành viên hoặc hủy task không được xóa lịch sử đóng góp.
- Mọi màn hình phải có trạng thái loading, empty, error và forbidden.
- Action bị cấm phải được ẩn; backend vẫn kiểm tra quyền lại.


