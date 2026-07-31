---
title : "Bản đồ màn hình và luồng người dùng"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3.3 </b> "
---

Hai lối đi xuyên qua cùng một tập màn hình, phân biệt bằng `role` trên JWT chứ
không phải bằng một ứng dụng riêng.

**Lối đi của khách hàng:**

| Màn hình | Gọi tới |
|---|---|
| Trang chủ (danh sách sự kiện) | `GET /events` |
| Chi tiết sự kiện | `GET /events/:id`, `GET /events/:id/seats` |
| Chọn ghế / xác nhận đặt vé | `POST /bookings` |
| Vé của tôi | `GET /bookings` |
| Chi tiết booking | `GET /bookings/:id`, `DELETE /bookings/:id`, `POST /bookings/:id/ticket` |
| Đăng nhập / Đăng ký | `POST /auth/login`, `POST /auth/register` |

**Lối đi của admin** (cùng màn hình đăng nhập, token mang `role: "admin"`):

| Màn hình | Gọi tới |
|---|---|
| Quản lý suất chiếu (danh sách) | `GET /events` |
| Tạo suất chiếu | `POST /events` |
| Tải poster lên | `POST /events/:id/banner` |


![Bản đồ màn hình: lối đi của khách hàng và lối đi của admin](/images/5-Workshop/5.3-Design/5.3.3-screen-map/caerus_screen_map.png)
