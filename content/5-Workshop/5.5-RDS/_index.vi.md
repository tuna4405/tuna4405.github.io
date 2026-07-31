---
title : "Amazon RDS cho PostgreSQL"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Tổng quan

Phần này chỉ thuần về hạ tầng: dựng lên một instance PostgreSQL được quản lý
với những thuộc tính mà một triển khai thực tế cần - Multi-AZ để tự động
failover, và một private subnet hoàn toàn không có đường ra internet - thay
vì bắt đầu đơn giản rồi mới nâng cấp sau. Chưa có gì chạy trên database này
cả; không có compute nào trong VPC có thể chạm tới nó cho tới khi mục 5.7 tồn
tại, và việc chạy `001_init.sql` và `seed.sql` từ máy của developer sẽ có
nghĩa là phải mở `caerus-rds-sg` cho một IP cá nhân chỉ để rồi vứt bỏ rule đó
ngay sau đó. Bước đó - cùng với lần kiểm tra end-to-end thực sự đầu tiên xem
schema và seed data có hoạt động hay không - diễn ra đúng một lần, từ bên
trong chính instance ứng dụng, ở mục 5.7.7.

#### Nội dung

- [Khởi tạo DB Instance](5.5.1-launch-instance/)
