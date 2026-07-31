---
title : "Backend: Express và PostgreSQL trên Docker"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

1. **Khởi động PostgreSQL 16 trong Docker**, ánh xạ ra cổng 5433 của máy chủ để nó
   không bao giờ đụng độ với một PostgreSQL đã cài sẵn trên máy lập trình viên:

   ```yaml
   services:
     db:
       image: postgres:16
       environment:
         POSTGRES_USER: caerus
         POSTGRES_PASSWORD: caerus_dev
         POSTGRES_DB: caerus
       ports:
         - "5433:5432"
       volumes:
         - pgdata:/var/lib/postgresql/data
   volumes:
     pgdata:
   ```

2. **Nạp schema và dữ liệu seed** dưới dạng các file `.sql` thuần, chạy bằng `psql`
   - đúng hai file đó về sau sẽ chạy y nguyên trên RDS ở mục 5.5, và đó chính là lý
   do duy nhất để giữ migration ở dạng SQL thay vì giấu sau một công cụ migration
   của ORM với phụ thuộc runtime riêng của nó.

3. **Cài đặt giao dịch đặt vé** - đoạn code duy nhất mà cả dự án tồn tại để làm cho
   đúng:

   ```sql
   BEGIN;

   -- Lock the requested seat rows, ordered by id, before reading their status.
   SELECT id, status FROM seats
   WHERE id = ANY($1) AND event_id = $2
   ORDER BY id
   FOR UPDATE;

   -- If any locked seat is not 'available', roll back and return 409
   -- SEAT_ALREADY_BOOKED with the conflicting ids.

   INSERT INTO bookings (user_id, event_id, total_price) VALUES (...) RETURNING id;
   INSERT INTO booking_seats (booking_id, seat_id) SELECT $1, unnest($2::int[]);
   UPDATE seats SET status = 'booked' WHERE id = ANY($1) AND status = 'available';

   COMMIT;
   ```

**Vì sao `ORDER BY id` quan trọng không kém `FOR UPDATE`.** Bản thân `FOR UPDATE`
chỉ ngăn hai giao dịch cùng nghĩ rằng một ghế đang trống; nó không ngăn được
deadlock. Nếu giao dịch A yêu cầu ghế `[12, 13]` còn giao dịch B yêu cầu `[13, 12]`
tại cùng một khoảnh khắc, thì khi không có thứ tự khóa cố định, mỗi bên có thể rơi
vào cảnh đang giữ đúng cái khóa mà bên kia cần. Sắp xếp các id ghế trước khi khóa
nghĩa là mọi giao dịch đều lấy khóa theo cùng một trình tự bất kể client gửi lên
theo thứ tự nào, nhờ đó hai giao dịch không bao giờ tạo thành một vòng chờ lẫn
nhau.

Như một lớp bảo hiểm rẻ tiền phòng khi về sau có một nhánh code nào đó đi vòng qua
hẳn cơ chế khóa, câu `UPDATE` cuối cùng kiểm tra lại `status = 'available'` và tầng
service khẳng định số dòng bị ảnh hưởng phải khớp với số ghế được yêu cầu - dưới
khóa thì điều này không bao giờ có thể sai, nên nếu nó sai thật thì đó là một tín
hiệu rất lớn rằng có thứ gì đó ở phía trên đã bỏ qua giao dịch, chứ không phải một
trạng thái cần xử lý êm đẹp.

<!-- ![Local docker-compose and psql session loading the schema](/images/5-Workshop/5.4-Local-Build/5.4.1-backend/example.png) -->
