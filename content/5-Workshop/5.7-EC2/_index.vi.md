---
title : "Amazon EC2 và triển khai"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Tổng quan

Vị trí mạng của tầng compute là một quyết định được đưa ra trước khi instance
đầu tiên từng khởi tạo, không phải một bài học rút ra sau đó: private subnet,
một NAT gateway cho mỗi Availability Zone, và Systems Manager để quản trị đều
đã sẵn sàng trước khi `caerus-server-1` tồn tại - giống hệt cách mục 5.5.1 đặt
RDS vào private subnet ngay từ lần khởi tạo đầu tiên thay vì như một lần di
chuyển sau này. Không instance nào trong dự án này từng có IP công khai hay
một port SSH đang mở. Câu chuyện được kể ở đây là một lần khởi tạo thực hành
trên một IAM user cá nhân, hạ tầng mạng private được xây cho tầng ứng dụng,
một instance duy nhất được triển khai vào đó và được quản trị hoàn toàn qua
Session Manager, một security group bắt đầu không có bất kỳ rule inbound nào
vì chưa có gì cần tới nó, frontend được trỏ tới instance đó qua một đường hầm
SSM để bắt lỗi CORS không thể tránh khỏi trước khi có bất kỳ traffic thật nào,
và sau đó một instance thứ hai cùng một Application Load Balancer - thành
phần đầu tiên trong toàn bộ chuỗi này thực sự mở một đường vào từ internet
công khai, có chủ đích, tại đúng một điểm duy nhất. Đây là phần mà ứng dụng
trở nên truy cập được từ bất kỳ đâu, trong khi các instance phục vụ nó vẫn
không thể truy cập được từ bất kỳ đâu ngoại trừ đúng load balancer đó.

#### Nội dung

- [Thực hành khởi tạo và huỷ instance](5.7.1-launch-practice/)
- [Hạ tầng mạng Private và Instance Đầu Tiên](5.7.2-deploy-api/)
- [Siết chặt Security Group](5.7.3-security-groups/)
- [Build frontend và xử lý CORS](5.7.4-frontend-and-cors/)
- [Load Balancer và Instance Thứ Hai](5.7.5-load-balancer/)
- [CloudFront: Một Domain HTTPS Duy Nhất cho Tất Cả](5.7.6-cloudfront/)
