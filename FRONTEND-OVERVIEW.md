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
    WORKSPACE --> PERSONALSETTINGS["Cài đặt cá nhân"]
    PERSONALSETTINGS --> PROFILE["Hồ sơ"]
    PERSONALSETTINGS --> SECURITY["Bảo mật"]
    PERSONALSETTINGS --> NOTIPREF["Tùy chọn thông báo"]

    PROJECTS --> PROJECT["Workspace dự án"]
    PROJECT --> OVERVIEW["Tổng quan"]
    PROJECT --> TASKS["Task board"]
    PROJECT --> REVIEWQUEUE["Hàng chờ duyệt"]
    PROJECT --> REASSIGN["Cần phân công lại"]
    PROJECT --> MEMBERS["Thành viên"]
    PROJECT --> STATS["Thống kê"]
    PROJECT --> ACTIVITY["Nhật ký dự án"]
    PROJECT --> EXPORT["Báo cáo & xuất file"]
    PROJECT --> PROJECTSETTINGS["Cài đặt dự án"]

    TASKS --> DETAIL["Chi tiết task"]
    DETAIL --> PROGRESS["Báo cáo & minh chứng"]
    DETAIL --> ASSIGNMENTS["Lịch sử phân công<br/>Ai làm bao nhiêu %"]
    DETAIL --> REVIEW["Duyệt task"]
    DETAIL --> TRANSFER["Chuyển task"]

    MEMBERS --> MEMBERINVITE["Mời / mời lại thành viên"]

    PROJECTSETTINGS --> OWNERMODE["Chế độ Owner<br/>Contributor / Chỉ quản lý"]
    PROJECTSETTINGS --> OWNERSHIP["Chuyển quyền Owner"]
    PROJECTSETTINGS --> LIFECYCLE["Hoàn thành · Mở lại<br/>Hủy / xóa dự án"]

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
│ ├─ Reviews     │                                                     │
│ ├─ Reassign    │                                                     │
│ ├─ Members     │                                                     │
│ ├─ Statistics  │                                                     │
│ └─ Settings    │                                                     │
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
│ Báo cáo và phản hồi                │ Gửi duyệt / Review / Transfer   │
│ Ai làm: 0→40% · 40→75% · 75→100%   │ Đóng góp được công nhận         │
└────────────────────────────────────┴─────────────────────────────────┘
```

Lịch sử phân công là dữ liệu cốt lõi của task chuyển giao: mỗi giai đoạn phải lưu người thực hiện, khoảng tiến độ, thời gian, lý do chuyển và tỷ lệ đóng góp được công nhận.

## 5. Các màn hình cần thiết kế trước

| Thứ tự | Màn hình | Route | Kết quả cần nhìn thấy |
|---:|---|---|---|
| 1 | App shell | Toàn workspace | Header, sidebar, responsive navigation |
| 2 | Auth | `/auth/*` | Login, register, recovery, validation |
| 3 | Dashboard | `/dashboard` | Việc cần làm, dự án, deadline, review |
| 4 | Project overview | `/projects/:projectId` | Tiến độ, cảnh báo, thành viên |
| 5 | Task board | `.../tasks` | Trạng thái task và bộ lọc |
| 6 | Task detail | `.../tasks/:taskId` | Tiến độ, báo cáo, minh chứng, action |
| 7 | Assignment history | `.../assignments` | Các giai đoạn thực hiện và % đóng góp |
| 8 | Review queue | `.../reviews` | Tất cả task đang chờ người dùng duyệt |
| 9 | Review detail | `.../review` | Submission, feedback, quyết định |
| 10 | Reassignments | `.../reassignments` | Task cần giao lại hoặc bị chặn duyệt |
| 11 | Members | `.../members` | Role, status, contribution |
| 12 | Invite member | `.../members/invite` | Mời mới hoặc mời lại người đã rời |
| 13 | Statistics | `.../statistics` | Tiến độ trọng số và đóng góp |
| 14 | Project activity | `.../activity` | Dòng thời gian của riêng dự án |
| 15 | Project reports | `.../reports` | Báo cáo tổng hợp và xuất file |
| 16 | Project settings | `.../settings/*` | Chế độ Owner, ownership, vòng đời dự án |
| 17 | Personal settings | `/settings/*` | Hồ sơ, bảo mật, thông báo |
| 18 | Admin | `/admin/*` | Users, projects, system logs |

## 6. Các khu vực quản lý quan trọng

### Hàng chờ duyệt

Màn hình tổng hợp task `PENDING_REVIEW` mà Owner/Manager hiện tại được phép duyệt. Có bộ lọc theo dự án, assignee, deadline và thời gian chờ; không đưa task của chính reviewer vào danh sách.

### Cần phân công lại

Gồm task `NEEDS_REASSIGNMENT` và `REVIEW_BLOCKED`. Manager có thể chọn người nhận, reviewer mới, cách xử lý tiến độ cũ và tỷ lệ đóng góp được công nhận.

### Cài đặt dự án

```mermaid
flowchart LR
    SETTINGS["Cài đặt dự án"] --> GENERAL["Thông tin chung"]
    SETTINGS --> MODE["Chế độ Owner"]
    MODE --> CONTRIBUTOR["Contributor<br/>Được nhận task<br/>Bắt buộc có Manager"]
    MODE --> MANAGEMENT["Chỉ quản lý<br/>Không nhận task"]
    SETTINGS --> OWNERTRANSFER["Chuyển quyền Owner"]
    SETTINGS --> LIFECYCLE2["Hoàn thành · Mở lại · Hủy · Xóa"]
```

### Thành viên và lời mời

Trang “Mời thành viên” phải xử lý cả người mới và người từng `LEFT`/`REMOVED`. Khi mời lại, hệ thống kích hoạt bản ghi cũ, giữ lịch sử đóng góp và không tự động trả lại task đã chuyển.

## 7. Quy tắc frontend không được bỏ qua

- Không ai được tự duyệt task của chính mình.
- Owner ở chế độ `MANAGEMENT_ONLY` không được nhận task.
- Dự án `COMPLETED` hoặc `CANCELLED` phải hiển thị ở chế độ chỉ đọc.
- Rời dự án, loại thành viên hoặc hủy task không được xóa lịch sử đóng góp.
- Mọi màn hình phải có trạng thái loading, empty, error và forbidden.
- Action bị cấm phải được ẩn; backend vẫn kiểm tra quyền lại.

