---
title : "Amazon RDS cho PostgreSQL"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Tổng quan

Đưa cơ sở dữ liệu ra khỏi một container cục bộ, trong trường hợp đơn giản, chỉ là
đổi một connection string: đúng hai file `001_init.sql` và `seed.sql` ấy chạy y
nguyên trên một instance RDS được quản lý. Mục này trình bày trường hợp đơn giản
đó trước (các mục 5.5.1-5.5.3), rồi đi xa hơn những gì kế hoạch ban đầu đòi hỏi:
bật Multi-AZ để tự động failover và dời instance vào một private subnet hoàn toàn
không có đường ra internet (mục 5.5.4) - đồng thời nói thẳng về những điểm kỳ quặc
của AWS đã khiến phần thứ hai khó hơn mức đáng lẽ phải có.

#### Nội dung

- [Khởi tạo DB Instance](5.5.1-launch-instance/)
- [Chạy migration và seed](5.5.2-migrate-and-seed/)
- [Kiểm tra kết nối](5.5.3-verify/)
- [Multi-AZ và Private Subnet](5.5.4-multi-az-and-private-subnet/)
