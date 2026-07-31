---
title : "Xây dựng cục bộ"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Tổng quan

Trước khi bất kỳ tài nguyên AWS nào tồn tại, Caerus chạy hoàn toàn trên
localhost: một container PostgreSQL chạy Docker cho backend, các file JSON
giả cho frontend, và một module client duy nhất khiến việc chuyển từ dữ
liệu giả sang API thật sau này chỉ là một thay đổi một dòng. Mục này có
chủ đích là mục ngắn nhất và ít mang màu sắc AWS nhất trong workshop - nó
tồn tại để chứng minh logic ứng dụng là đúng trước khi bất kỳ biến số nào
của cloud được đưa vào, để nếu có gì đó hỏng sau khi triển khai, lỗi nằm ở
việc triển khai, không phải ở code.

#### Nội dung

- [Backend: Express và PostgreSQL trên Docker](5.4.1-backend/)
- [Frontend: React với dữ liệu giả](5.4.2-frontend/)
- [Tích hợp](5.4.3-integration/)
