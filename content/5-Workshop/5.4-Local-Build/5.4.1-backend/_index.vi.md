---
title : "Backend: Express và PostgreSQL trên Docker"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

1. **Khởi động PostgreSQL 16 trong Docker**, map ra cổng 5433 trên host để
   nó không bao giờ xung đột với một PostgreSQL đã cài sẵn trên máy của
   developer:

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

2. **Nạp schema và dữ liệu seed** dưới dạng các file `.sql` thuần túy, chạy
   bằng `psql` - cùng hai file này sẽ chạy không thay đổi trên RDS sau này
   ở mục 5.5, đó chính là toàn bộ lý do vì sao giữ migration ở dạng SQL
   thay vì đặt sau một công cụ migration của ORM với dependency runtime
   riêng của nó.

3. **Triển khai booking transaction** - đoạn code duy nhất mà cả dự án này
   tồn tại để làm cho đúng:

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

**Vì sao `ORDER BY id` quan trọng không kém `FOR UPDATE`.** Chỉ riêng
`FOR UPDATE` ngăn hai transaction cùng nghĩ rằng một ghế đang trống; nó
không ngăn được deadlock. Nếu transaction A yêu cầu các ghế `[12, 13]` và
transaction B yêu cầu `[13, 12]` cùng một thời điểm, nếu không có một thứ
tự lock cố định thì mỗi transaction có thể kết thúc bằng việc giữ đúng lock
mà transaction kia đang cần. Sắp xếp các seat id trước khi lock có nghĩa là
mọi transaction đều lấy lock theo cùng một trình tự bất kể thứ tự mà client
gửi lên, nên hai transaction không bao giờ có thể tạo thành một chu trình
chờ đợi lẫn nhau.

Như một lớp bảo hiểm rẻ tiền phòng khi có một đường code trong tương lai bỏ
qua hoàn toàn lock, `UPDATE` cuối cùng kiểm tra lại `status = 'available'`
và service layer khẳng định số dòng bị ảnh hưởng khớp với số ghế được yêu
cầu - dưới lock thì điều này không bao giờ có thể thất bại, nên nếu nó
từng xảy ra, đó là một tín hiệu rất lớn cho thấy có gì đó ở phía trên đã bỏ
qua transaction, chứ không phải một trạng thái cần xử lý một cách nhẹ
nhàng.

<!-- ![Phiên docker-compose và psql cục bộ đang nạp schema](/images/5-Workshop/5.4-Local-Build/5.4.1-backend/example.png) -->
