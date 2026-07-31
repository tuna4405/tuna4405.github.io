---
title: "Nhật ký tuần 3"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3: Compute, Lưu trữ, và Cơ sở dữ liệu Quản lý (Managed Database)

* Hiểu về việc lựa chọn kích cỡ instance EC2, lưu trữ, và cách giữ một tiến trình web luôn chạy trên một instance.
* Hiểu S3 đủ sâu để dùng cho static hosting và cho việc tải lên riêng tư (private) thông qua presigned URL.
* Hiểu về cơ sở dữ liệu quan hệ được quản lý (managed relational database) và các đảm bảo về khóa (locking) mà một hệ thống đặt vé cần đến.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Kiến thức nền tảng về EC2: <br> &emsp; + Các dòng (family) instance và các kích cỡ thuộc Free Tier <br> &emsp; + Amazon Machine Images, key pair, và truy cập SSH <br> &emsp; + Các loại EBS volume, và điều gì xảy ra với một volume khi stop so với terminate <br> - **Thực hành:** khởi chạy một instance, kết nối qua SSH, cài Node.js, và giữ một HTTP server nhỏ luôn chạy dưới một process manager | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 3 | - Khái niệm về load balancing và scaling (chỉ mới học, chưa xây dựng): Elastic Load Balancing, target group, health check, và vì sao một instance đơn lẻ là điểm khởi đầu chấp nhận được cho một dự án nhỏ nhưng không phải điểm kết thúc <br> - Terminate instance thực hành ngay trong ngày và xác nhận không còn gì đang chạy | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 4 | - Kiến thức nền tảng về Amazon S3: <br> &emsp; + Bucket, object, key, và không gian tên phẳng (flat namespace) <br> &emsp; + Block Public Access và bucket policy <br> &emsp; + Static website hosting <br> &emsp; + Pre-signed URL cho quyền truy cập có giới hạn thời gian vào một object riêng tư, và cấu hình CORS trên bucket | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 5 | - Amazon RDS: <br> &emsp; + Các engine được hỗ trợ, instance class, và những gì dịch vụ quản lý (managed service) đảm nhận thay (patching, backup, failover) <br> &emsp; + Triển khai Multi-AZ, subnet group, và khả năng truy cập công khai (public accessibility) <br> &emsp; + VPC gateway endpoint cho S3, và vì sao việc giữ luồng traffic đó không đi qua internet công cộng lại quan trọng cho cả bảo mật lẫn chi phí | 02/07/2026 | 02/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 6 | - Các khái niệm cơ sở dữ liệu làm nền tảng cho một hệ thống đặt vé: <br> &emsp; + Thuộc tính ACID và các mức isolation của transaction <br> &emsp; + Pessimistic locking với `SELECT ... FOR UPDATE`, khóa theo thứ tự nhất quán để tránh deadlock, so với optimistic concurrency control <br> - **Thực hành:** khởi chạy một instance RDS PostgreSQL, kết nối từ một client cục bộ, và tạo một bảng | 03/07/2026 | 03/07/2026 | <https://www.postgresql.org/docs/16/transaction-iso.html> |
| 7 | - Tự học: đọc về AWS Config và Conformance Packs - ghi lại cấu hình của một tài nguyên theo thời gian và đánh giá nó theo các bộ quy tắc (rule set) - như một cách để bắt được đúng loại lỗi cấu hình mặc định trên console (một bucket bị để public, một security group bị để mở) rất dễ bị bỏ qua khi làm việc nhanh | 04/07/2026 | 04/07/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html> |

### Kết quả đạt được Tuần 3:

* Có thể khởi chạy, cấu hình, kết nối, và terminate một instance EC2, và có thể giữ một tiến trình Node.js luôn chạy dưới một process manager.
* Có thể cấu hình một bucket cho static hosting và giải thích pre-signed URL như một cách chia sẻ object riêng tư mà không cần biến bucket thành public.
* Có thể khởi chạy một instance RDS, kết nối tới nó, và giải thích những gì dịch vụ quản lý xử lý tự động.
* Có thể giải thích ACID, các mức isolation, và row-level locking, và xác định `SELECT ... FOR UPDATE` là cơ chế mà giao dịch đặt vé cần dùng.
* Nắm được những thành phần networking và compute nào phát sinh chi phí theo giờ bất kể lưu lượng, trước khi thiết kế kiến trúc triển khai (deployed architecture).
