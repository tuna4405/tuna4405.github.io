---
title : "Bản đồ màn hình và luồng người dùng"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3.3 </b> "
---

Hai luồng đi qua cùng một tập hợp màn hình, được phân biệt bởi `role` trên
JWT chứ không phải bởi một ứng dụng riêng biệt.

**Luồng khách hàng (customer path):**

| Màn hình | Gọi |
|---|---|
| Trang chủ (danh sách sự kiện) | `GET /events` |
| Chi tiết sự kiện | `GET /events/:id`, `GET /events/:id/seats` |
| Chọn ghế / xác nhận đặt vé | `POST /bookings` |
| Lượt đặt của tôi | `GET /bookings` |
| Chi tiết lượt đặt | `GET /bookings/:id`, `DELETE /bookings/:id`, `POST /bookings/:id/ticket` |
| Đăng nhập / Đăng ký | `POST /auth/login`, `POST /auth/register` |

**Luồng admin (admin path)** (cùng màn hình đăng nhập, `role: "admin"`
trên token):

| Màn hình | Gọi |
|---|---|
| Quản lý suất chiếu (danh sách) | `GET /events` |
| Tạo suất chiếu | `POST /events` |
| Tải lên poster | `POST /events/:id/banner` |

{{% notice note %}}
Có chủ đích không có màn hình "chỉnh sửa suất chiếu" hay "xóa suất chiếu".
Một khi ghế đã tồn tại cho một sự kiện, việc thay đổi hình dạng của nó một
cách hồi tố là một vấn đề khó hơn nhiều so với việc tạo một sự kiện mới -
nằm ngoài phạm vi của dự án này, và đáng để nói rõ điều đó thay vì để lại
một khoảng trống âm thầm trong bản đồ màn hình.
{{% /notice %}}

![Screen map: customer path and admin path](/images/5-Workshop/5.3-Design/5.3.3-screen-map/caerus_screen_map.png)
