---
title : "Thiết kế cơ sở dữ liệu"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

Năm bảng, tất cả trong một database PostgreSQL:

- **`users`** - id, name, email, `password_hash` dùng bcrypt, `role`
  (`customer` hoặc `admin`).
- **`events`** - một dòng cho mỗi suất chiếu (không phải mỗi bộ phim):
  title, description, `starts_at` (timestamptz), duration, auditorium,
  price, `banner_url`.
- **`seats`** - một dòng cho mỗi ghế của mỗi suất chiếu, `seat_row`
  (`A`-`F`), `seat_number` (`1`-`10`), và một `status` là `available` hoặc
  `booked`.
- **`bookings`** - user, event, `total_price`, `status`
  (`confirmed`/`cancelled`), `cancelled_at`.
- **`booking_seats`** - bảng nối (join table) giữa một booking và các ghế
  cụ thể mà nó giữ.

**Điểm gài bẫy trong cách đặt tên `seat_row`.** Cột này có tên `seat_row`,
không phải `row` - `ROW` là một từ khóa dành riêng (reserved word) trong
PostgreSQL (và cũng là một hàm dựng sẵn), nên một cột được đặt tên đúng
nghĩa là `row` sẽ thất bại hoàn toàn hoặc buộc mọi query chạm vào nó phải
dùng một identifier có dấu ngoặc kép rất vướng víu. API vẫn gọi nó là `row`
trong JSON, vì đó là tên mà một frontend developer muốn dùng; việc chuyển
đổi từ `seat_row`/`seat_number` sang `row`/`number` chỉ diễn ra một lần,
trong hàm row-mapping ở backend, chứ không rải rác khắp mọi query.

**Bốn quyết định thiết kế đáng được giải thích, không chỉ nêu ra suông:**

1. **Ghế thuộc về một suất chiếu, không thuộc về một phòng vật lý.** Một sơ
   đồ 6x10 được sinh mới hoàn toàn cho mỗi `event`, vì vậy câu hỏi "ghế A1
   có còn trống không" luôn là câu hỏi về một suất chiếu cụ thể duy nhất,
   không bao giờ là câu hỏi cần join thêm một lần nữa về lịch của một
   phòng để làm rõ.
2. **`totalSeats` và `availableSeats` được tính toán tại thời điểm query,
   không bao giờ được lưu như bộ đếm (counter).** Một bộ đếm được lưu trữ
   có thể trôi lệch khỏi thực tế ngay khi một booking và một lần cập nhật
   counter không hoàn toàn atomic; một `COUNT(*)` trên bảng `seats` không
   thể bị trôi lệch, vì không có gì cần phải đồng bộ.
3. **Tiền là một cột kiểu số nguyên tính bằng đồng Việt Nam, và
   `total_price` của một booking được chụp nhanh (snapshot) tại thời điểm
   tạo.** Nếu giá của suất chiếu thay đổi sau đó, các booking trong quá khứ
   không được âm thầm ghi đè lại - snapshot đó chính là số tiền mà khách
   hàng thực sự đã trả.
4. **Hủy một booking là lật trạng thái (status flip), không bao giờ xóa
   dòng.** `status` chuyển sang `cancelled` và `cancelled_at` được đóng
   dấu thời gian; dòng dữ liệu vẫn tồn tại, để lịch sử booking mà một báo
   cáo hoặc một cuộc trao đổi hỗ trợ khách hàng có thể cần vẫn còn đó.

![Entity-relationship diagram](/images/5-Workshop/5.3-Design/5.3.2-database-schema/erd.png)
