---
title : "Chạy migration và seed"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

1. **Sao chép endpoint của RDS** từ tab Connectivity & Security của instance - một
   hostname dạng `caerus-db.xxxxxxxxxx.ap-southeast-1.rds.amazonaws.com`, không bao
   giờ là một địa chỉ IP, bởi RDS có thể đổi địa chỉ bên dưới khi failover.

2. **Chạy đúng hai file đã dùng ở cục bộ**, nhưng trỏ vào endpoint thay vì
   `localhost:5433`:

   ```bash
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -f db/migrations/001_init.sql
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -f db/seed.sql
   ```

3. **Xác nhận các bảng đã vào đúng chỗ:**

   ```bash
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -c "\dt"
   ```

Việc đây đúng là những file đã chạy trên Docker ở mục 5.4.1, giống nhau từng byte,
chính là toàn bộ lý lẽ cho việc giữ migration ở dạng SQL thuần thay vì giấu sau một
trình chạy migration riêng của framework: không có bản cài đặt thứ hai nào phải giữ
đồng bộ với bản thứ nhất, và không có rủi ro cơ sở dữ liệu được quản lý âm thầm
lệch schema so với những gì đã kiểm thử ở cục bộ.

<!-- ![psql session against the RDS endpoint, migration and seed output](/images/5-Workshop/5.5-RDS/5.5.2-migrate-and-seed/example.png) -->
