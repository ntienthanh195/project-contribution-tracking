# Sitemap — Project Contribution Tracking System

> Phạm vi: MVP web application. Mỗi trang cần có đường quay về dashboard hoặc luồng công việc liên quan.

```text
/
├── /login
├── /register
├── /forgot-password
├── /dashboard                         # Điều hướng theo vai trò hệ thống
│   ├── /dashboard/admin               # Admin
│   ├── /dashboard/projects            # Owner / Manager / Member
│   └── /dashboard/my-tasks            # Member / Contributor
├── /projects
│   ├── /projects/new                  # Tạo dự án
│   └── /projects/[projectId]
│       ├── /overview                  # Tổng quan và tiến độ dự án
│       ├── /tasks
│       │   ├── /new                   # Owner / Manager tạo task
│       │   └── /[taskId]
│       │       ├── /edit              # Chỉnh sửa task
│       │       ├── /reports           # Lịch sử báo cáo & minh chứng
│       │       ├── /review            # Duyệt hoặc yêu cầu chỉnh sửa
│       │       └── /transfer          # Chuyển task
│       ├── /members                    # Mời, phân quyền, trạng thái thành viên
│       ├── /analytics                  # Thống kê dự án và đóng góp cá nhân
│       ├── /activity                   # Nhật ký hoạt động
│       └── /settings                   # Owner quản lý dự án
├── /notifications
├── /profile
└── /admin                             # Chỉ Admin
    ├── /users
    ├── /projects
    └── /activity-logs
```

## Luồng điều hướng chính

| Điểm vào | Đích chính | Mục đích |
| --- | --- | --- |
| Đăng nhập / đăng ký | Dashboard | Nhận đúng dashboard theo quyền. |
| Dashboard | Danh sách dự án, task của tôi, thông báo | Theo dõi công việc cần xử lý. |
| Danh sách dự án | Tổng quan dự án | Xem tiến độ, thành viên và task. |
| Tổng quan dự án | Task, thành viên, analytics | Thao tác trong phạm vi dự án. |
| Chi tiết task | Báo cáo, review, transfer | Cập nhật, duyệt hoặc chuyển giao task. |
| Mọi màn hình | Notifications, Profile | Truy cập tiện ích cá nhân nhất quán. |

## Điều hướng theo vai trò

| Vai trò | Các khu vực chính |
| --- | --- |
| Admin | Admin dashboard, tài khoản, dự án toàn hệ thống, nhật ký hoạt động. |
| Owner | Dashboard dự án, tổng quan, task, thành viên, analytics, cài đặt dự án. |
| Manager | Dashboard dự án, task, thành viên, analytics và review task hợp lệ. |
| Member | Dashboard cá nhân, task được giao, báo cáo, minh chứng, yêu cầu chuyển task. |

## Nguyên tắc UI

- Header/sidebar nhất quán trong khu vực đã đăng nhập.
- Trang chi tiết task là điểm tập trung cho tiến độ, báo cáo, minh chứng và duyệt task.
- Các hành động không có quyền phải ẩn hoặc hiển thị trạng thái bị vô hiệu hóa kèm lý do.
- Các task cần xử lý ưu tiên hiển thị từ dashboard qua liên kết sâu đến đúng task.
