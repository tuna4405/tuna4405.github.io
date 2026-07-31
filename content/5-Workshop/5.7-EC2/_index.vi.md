---
title : "Amazon EC2 và triển khai"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Tổng quan

Câu chuyện về tầng compute, kể theo đúng thứ tự nó đã thực sự diễn ra: một lần khởi
chạy thực hành trên IAM user cá nhân, một instance EC2 duy nhất được triển khai và
truy cập được, một security group được siết chặt quanh nó, frontend được trỏ vào nó
kèm việc xử lý lỗi CORS phát sinh - và chỉ khi mọi thứ đó đã vững, mới đến một
instance thứ hai cùng một Application Load Balancer đứng trước cả hai, rồi Amazon
CloudFront đứng trước cả load balancer lẫn bucket S3 chứa trang web, và cuối cùng
chính các instance được dời hẳn ra khỏi internet công cộng, vào các private subnet
nằm sau một NAT gateway và được quản trị qua Systems Manager thay cho SSH. Đây là
mục mà ứng dụng thôi chỉ truy cập được từ máy của lập trình viên và trở thành truy
cập được từ bất cứ đâu - trong khi chính các instance phục vụ nó lại trở thành chỉ
tiếp cận được từ đúng một nơi là load balancer.

#### Nội dung

- [Thực hành khởi tạo và huỷ instance](5.7.1-launch-practice/)
- [Triển khai API với pm2](5.7.2-deploy-api/)
- [Siết chặt Security Group](5.7.3-security-groups/)
- [Build frontend và xử lý CORS](5.7.4-frontend-and-cors/)
- [Load Balancer và Instance Thứ Hai](5.7.5-load-balancer/)
- [CloudFront: Một Domain HTTPS Duy Nhất](5.7.6-cloudfront/)
- [Private Subnets, NAT, và Systems Manager](5.7.7-private-subnet-nat/)
