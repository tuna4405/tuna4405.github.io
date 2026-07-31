---
title : "Thiết kế cơ sở dữ liệu"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

Năm bảng, tất cả nằm trong một cơ sở dữ liệu PostgreSQL duy nhất:

- **`users`** - id, tên, email, `password_hash` băm bằng bcrypt, `role`
  (`customer` hoặc `admin`).
- **`events`** - mỗi dòng là một suất chiếu (không phải mỗi bộ phim): tiêu đề, mô
  tả, `starts_at` (timestamptz), thời lượng, phòng chiếu, giá vé, `banner_url`.
- **`seats`** - mỗi dòng là một ghế của một suất chiếu, gồm `seat_row` (`A`-`F`),
  `seat_number` (`1`-`10`), và `status` là `available` hoặc `booked`.
- **`bookings`** - người dùng, sự kiện, `total_price`, `status`
  (`confirmed`/`cancelled`), `cancelled_at`.
- **`booking_seats`** - bảng nối giữa một booking và những ghế cụ thể mà nó giữ.

**Cái bẫy đặt tên ở `seat_row`.** Cột này tên là `seat_row` chứ không phải `row` -
`ROW` là từ khóa dành riêng trong PostgreSQL (và cũng là một hàm dựng sẵn), nên một
cột đặt tên đúng là `row` hoặc sẽ lỗi thẳng, hoặc buộc mọi truy vấn chạm tới nó phải
dùng định danh trong dấu nháy rất vướng víu. Phía API thì vẫn gọi nó là `row` trong
JSON, vì đó là cái tên mà một lập trình viên frontend muốn thấy; việc chuyển đổi từ
`seat_row`/`seat_number` sang `row`/`number` diễn ra đúng một lần, trong hàm ánh xạ
dòng dữ liệu ở backend, chứ không rải rác khắp mọi truy vấn.

**Bốn quyết định thiết kế đáng được giải thích, chứ không chỉ nêu ra:**

1. **Ghế thuộc về một suất chiếu, không thuộc về một phòng vật lý.** Bố cục 6x10
   được sinh mới cho từng `event`, nhờ đó "ghế A1 còn trống không" luôn là câu hỏi
   về đúng một suất chiếu cụ thể, chứ không bao giờ là câu hỏi cần thêm một phép
   join ngược về lịch chiếu của phòng để làm rõ nghĩa.
2. **`totalSeats` và `availableSeats` được tính ngay lúc truy vấn, không bao giờ
   lưu thành bộ đếm.** Một bộ đếm được lưu sẵn có thể trôi lệch khỏi thực tế ngay
   khi việc ghi booking và việc cập nhật bộ đếm không hoàn toàn nguyên tử; còn một
   `COUNT(*)` trên bảng `seats` thì không thể trôi lệch, bởi chẳng có gì phải giữ
   cho đồng bộ cả.
3. **Tiền là một cột số nguyên theo đơn vị đồng Việt Nam, và `total_price` của một
   booking được chụp lại (snapshot) ngay khi tạo.** Nếu giá của suất chiếu thay đổi
   về sau, những booking trong quá khứ không được phép bị viết lại một cách âm thầm
   - bản chụp ấy mới là số tiền khách hàng thực sự đã trả.
4. **Hủy một booking là một lần đổi trạng thái, không bao giờ là xóa dòng.**
   `status` chuyển sang `cancelled` và `cancelled_at` được đóng dấu thời gian; dòng
   dữ liệu vẫn còn đó, nên lịch sử đặt vé mà một báo cáo hay một cuộc trao đổi hỗ
   trợ khách hàng có thể cần đến vẫn tồn tại.

![Sơ đồ thực thể - quan hệ](/images/5-Workshop/5.3-Design/5.3.2-database-schema/erd.png)
