---
title : "Kiểm tra kết nối"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---

1. **Xác nhận dữ liệu seed thực sự đã nằm ở đó**, truy vấn trực tiếp vào cơ sở dữ
   liệu:

   ```sql
   SELECT count(*) FROM events;
   -- expect 3, matching seed.sql
   ```

2. **Trỏ API Express vẫn đang chạy cục bộ sang RDS** bằng cách chỉ đổi
   `DATABASE_URL` trong `backend/.env` thành connection string của RDS, rồi khởi
   động lại API - không thay đổi code, chỉ thay đổi cấu hình.

3. **Gọi thử một endpoint thật và xác nhận response mang đúng dữ liệu seed:**

   ```bash
   curl http://localhost:3000/api/v1/events
   ```

   Response phải liệt kê đúng ba suất chiếu mà câu `SELECT count(*)` ở bước 1 đã xác
   nhận là tồn tại, qua đó chứng minh ứng dụng đang chạy - chứ không chỉ một phiên
   `psql` - có thể chạm tới cơ sở dữ liệu được quản lý một cách trọn vẹn, ngay cả
   khi EC2 còn chưa có mặt trong bức tranh.

{{% notice note %}}
Bước này cố ý giữ API chạy ngay trên máy của lập trình viên trong khi trỏ nó vào cơ
sở dữ liệu thật. Việc tách bạch "code API có chạy được với RDS không" khỏi "EC2 đã
cấu hình đúng chưa" đồng nghĩa là một sự cố ở mục 5.7 có thể được khoanh vùng chắc
chắn về phía EC2/mạng, thay vì phải bàn lại từ đầu xem liệu chính kết nối cơ sở dữ
liệu mới là vấn đề.
{{% /notice %}}

<!-- ![curl response showing seeded events served from RDS](/images/5-Workshop/5.5-RDS/5.5.3-verify/example.png) -->
