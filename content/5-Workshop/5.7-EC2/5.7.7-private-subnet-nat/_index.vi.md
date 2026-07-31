---
title : "Private Subnets, NAT, và Systems Manager"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7.7 </b> "
---

<!-- Dịch từ bản tiếng Anh sau khi hoàn thành nội dung. -->

Bước một: tạo 2 private subnet riêng cho tầng ứng dụng (tách khỏi private
subnet của RDS) và 1 NAT Gateway.

Bước hai: tạo AMI từ 2 EC2 đang chạy, launch instance mới trong private
subnet từ AMI đó, cấp quyền Systems Manager, gỡ SSH khỏi security group.
