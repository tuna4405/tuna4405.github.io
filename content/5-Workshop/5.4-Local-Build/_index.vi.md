---
title : "Xây dựng cục bộ"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Tổng quan

Trước khi có bất kỳ tài nguyên AWS nào, Caerus chạy hoàn toàn trên localhost: một
container PostgreSQL trên Docker cho backend, các file JSON giả lập cho frontend,
và một module client duy nhất khiến việc chuyển từ dữ liệu giả sang API thật về sau
chỉ còn là một thay đổi một dòng. Mục này cố ý là mục ngắn nhất và ít mùi AWS nhất
trong cả workshop - nó tồn tại để chứng minh logic ứng dụng đã đúng trước khi bất
kỳ biến số đám mây nào được đưa vào, để nếu có gì đó hỏng sau khi triển khai thì lỗi
nằm ở khâu triển khai, không phải ở code.

#### Nội dung

- [Backend: Express và PostgreSQL trên Docker](5.4.1-backend/)
- [Frontend: React với dữ liệu giả](5.4.2-frontend/)
- [Tích hợp](5.4.3-integration/)
