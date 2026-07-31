---
title: "Nhật ký tuần 3"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3: Compute, Lưu trữ, và Các Dịch vụ Cơ sở dữ liệu Quản lý (Managed Database)

* Nắm được cách chọn kích cỡ instance EC2, cách lưu trữ, và cách giữ cho một tiến trình web luôn sống trên một instance.
* Học S3 đủ sâu để dùng cho static hosting và cho việc tải lên riêng tư (private) phục vụ qua presigned URL.
* Hiểu về cơ sở dữ liệu quan hệ được quản lý (managed relational database) và những đảm bảo về khóa (locking) mà một hệ thống đặt vé dựa vào.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Kiến thức nền tảng về EC2: <br> &emsp; + Các dòng (family) instance và những kích cỡ nằm trong Free Tier <br> &emsp; + Amazon Machine Images, key pair, và truy cập SSH <br> &emsp; + Các loại EBS volume, và số phận của một volume khi stop so với khi terminate <br> - **Thực hành:** khởi chạy một instance, kết nối vào qua SSH, cài Node.js, và giữ một HTTP server nhỏ luôn sống dưới một process manager | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 3 | - Khái niệm về load balancing và scaling (mới dừng ở mức tìm hiểu, chưa dựng): Elastic Load Balancing, target group, health check, và vì sao một instance đơn lẻ là điểm xuất phát hợp lý cho một dự án nhỏ nhưng không phải điểm dừng <br> - Terminate instance thực hành ngay trong ngày hôm đó và kiểm tra lại rằng không còn gì sót lại đang chạy | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 4 | - Kiến thức nền tảng về Amazon S3: <br> &emsp; + Bucket, object, key, và không gian tên phẳng (flat namespace) <br> &emsp; + Block Public Access và bucket policy <br> &emsp; + Static website hosting <br> &emsp; + Pre-signed URL cấp quyền truy cập có giới hạn thời gian vào một object riêng tư, và cấu hình CORS trên bucket | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 5 | - Amazon RDS: <br> &emsp; + Các engine sẵn có, các instance class, và phần việc mà dịch vụ quản lý (managed service) gánh thay cho mình (patching, backup, failover) <br> &emsp; + Triển khai Multi-AZ, subnet group, và khả năng truy cập công khai (public accessibility) <br> &emsp; + VPC gateway endpoint cho S3, và việc giữ luồng traffic đó tránh xa internet công cộng mang lại lợi ích gì cho cả bảo mật lẫn chi phí | 02/07/2026 | 02/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 6 | - Những khái niệm cơ sở dữ liệu mà một hệ thống đặt vé dựa lên: <br> &emsp; + Thuộc tính ACID và các mức isolation của transaction <br> &emsp; + Pessimistic locking bằng `SELECT ... FOR UPDATE`, lấy khóa theo một thứ tự nhất quán để deadlock không xảy ra, đặt cạnh optimistic concurrency control <br> - **Thực hành:** khởi chạy một instance RDS PostgreSQL, kết nối vào từ một client cục bộ, và tạo một bảng | 03/07/2026 | 03/07/2026 | <https://www.postgresql.org/docs/16/transaction-iso.html> |
| 7 | - Tự học: tìm hiểu AWS Config và Conformance Packs - ghi lại cấu hình của một tài nguyên theo thời gian rồi đối chiếu với các bộ quy tắc (rule set) - như một cách bắt được đúng loại cấu hình mặc định trên console (một bucket bị bỏ ở chế độ public, một security group bị bỏ mở) mà người ta rất dễ bấm lướt qua khi làm nhanh | 04/07/2026 | 04/07/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html> |

### Kết quả đạt được Tuần 3:

* Khởi chạy, cấu hình, kết nối và terminate được một instance EC2, đồng thời giữ được một tiến trình Node.js luôn sống dưới một process manager.
* Cấu hình được một bucket cho static hosting và giải thích được pre-signed URL như một cách chia sẻ object riêng tư mà không phải biến bucket thành public.
* Khởi chạy được một instance RDS, kết nối vào, và mô tả được những gì dịch vụ quản lý tự lo liệu.
* Giải thích được ACID, các mức isolation, và row-level locking, đồng thời xác định `SELECT ... FOR UPDATE` chính là cơ chế mà giao dịch đặt vé cần đến.
* Biết được những thành phần networking và compute nào tính phí theo giờ bất kể có lưu lượng hay không, trước khi bắt tay thiết kế kiến trúc triển khai (deployed architecture).
