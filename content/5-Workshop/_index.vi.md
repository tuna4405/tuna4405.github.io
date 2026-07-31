---
title: "Workshop"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Caerus: Hệ thống đặt ghế xem phim trên AWS

#### Tổng quan

Caerus là một nền tảng đặt ghế xem phim: khách hàng duyệt các suất chiếu, chọn
ghế trên một sơ đồ ghế trực tiếp, đặt tối đa sáu ghế trong một giao dịch, hủy vé
trước giờ chiếu, và tải vé PDF; quản trị viên tạo suất chiếu và tải ảnh poster
lên. Yêu cầu duy nhất định hình mọi quyết định khác trong workshop này là **một
ghế không bao giờ được bán hai lần**, kể cả khi hai khách hàng bấm vào cùng một
ghế trong cùng một khoảnh khắc - đó cũng là lý do giao dịch đặt vé, chiến lược
khóa (locking), và bài kiểm thử concurrency đều được dành hẳn một mục riêng chứ
không chỉ được nhắc thoáng qua.

Bản triển khai chạy hoàn toàn trong **ap-southeast-1** và tiến hóa theo từng
chặng xuyên suốt workshop chứ không xuất hiện hoàn chỉnh ngay từ đầu: một trang
React tĩnh trên Amazon S3, một API Express trên Amazon EC2 đứng sau Application
Load Balancer với hai instance trải trên hai Availability Zone, PostgreSQL trên
Amazon RDS chạy Multi-AZ bên trong private subnet, Amazon CloudFront (kèm AWS
WAF) đặt trước cả trang web lẫn API để có HTTPS trên một tên miền duy nhất, và
Amazon CloudWatch cùng SNS theo dõi toàn bộ. Bản thân tầng compute cuối cùng
hoàn toàn riêng tư: cả hai instance EC2 nằm sau một NAT gateway, không có public
IP và không mở cổng SSH nào, được quản trị thay vào đó qua AWS Systems Manager
Session Manager - đúng thế trận vận hành mà cơ sở dữ liệu đã có từ mục 5.5.4, và
vì cùng một lý do.

Mỗi mục dưới đây xây tiếp lên trạng thái mà mục trước để lại. Hãy đi theo đúng
thứ tự trong lần đọc đầu tiên.

#### Nội dung

1. [Giới thiệu](5.1-Overview/)
2. [Các bước chuẩn bị](5.2-Prerequisites/)
3. [Thiết kế hệ thống](5.3-Design/)
4. [Xây dựng cục bộ](5.4-Local-Build/)
5. [Amazon RDS cho PostgreSQL](5.5-RDS/)
6. [Amazon S3](5.6-S3/)
7. [Amazon EC2 và triển khai](5.7-EC2/)
8. [CloudWatch và SNS](5.8-CloudWatch/)
9. [Kiểm thử](5.9-Testing/)
10. [Quản lý chi phí và tài nguyên](5.10-Cost/)
11. [Dọn dẹp tài nguyên](5.11-Cleanup/)
12. [Tài liệu tham khảo](5.12-References/)
