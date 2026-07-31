---
title : "Khởi tạo DB Instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

**RDS Console → Create database.**

1. **Standard create**, sau đó chọn engine. Ở đây có hai nút lớn nằm cạnh nhau -
   **Amazon Aurora** và **PostgreSQL** - và thật sự rất dễ bấm nhầm, kể cả lựa chọn
   ghi là "Aurora PostgreSQL Compatible", vốn vẫn là Aurora. Hãy chọn **PostgreSQL**
   thuần, phiên bản 16.x, để khớp với image `postgres:16` đã dùng ở cục bộ.

   {{% notice warning %}}
   Aurora và RDS PostgreSQL được tính phí và quản lý theo những cách khác nhau. Nếu
   một bước sau này hoạt động khác với mong đợi và có thành phần nào mang tên
   `Aurora` ở chỗ lẽ ra phải là `PostgreSQL`, thì đây chính là bước cần quay lại
   xem.
   {{% /notice %}}

2. **Templates: Free Tier** cho lần khởi tạo đầu tiên này (mục 5.5.4 về sau sẽ
   chuyển sang Production, đó là một bước riêng và có chủ đích - Free Tier giới hạn
   instance class và vô hiệu hóa hẳn Multi-AZ).

3. **DB instance identifier**: `caerus-db`. **Credentials management: Self
   managed** - không dùng AWS Secrets Manager, để giữ thiết lập nằm trong Free Tier
   và tránh một dịch vụ mà dự án không có nhu cầu nào khác. Hãy ghi lại ngay master
   username và mật khẩu; sau đó mật khẩu không thể lấy lại được từ Console.

4. **Connectivity**: cùng VPC với ứng dụng, **Public access: No**, và một security
   group riêng (`caerus-rds-sg`, tạo ra nhưng chưa có rule nào - rule inbound cho
   phép security group của ứng dụng sẽ được thêm vào khi security group đó tồn tại,
   ở mục 5.7.3).

5. **Additional configuration → Initial database name: `caerus`.** Điền sẵn ngay
   bây giờ sẽ tránh phải chạy `CREATE DATABASE` thủ công sau khi instance sẵn sàng.

6. **Gắn tag `Owner`** mang tên lập trình viên tạo ra nó, rồi tạo cơ sở dữ liệu và
   chờ trạng thái chuyển sang **Available** (mất vài phút với Single-AZ).

<!-- ![RDS create-database wizard: engine choice, instance class, connectivity panel](/images/5-Workshop/5.5-RDS/5.5.1-launch-instance/example.png) -->
