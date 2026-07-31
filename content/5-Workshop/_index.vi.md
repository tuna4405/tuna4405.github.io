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
ghế trên sơ đồ ghế 6x10 theo thời gian thực, đặt tối đa sáu ghế trong một
transaction, hủy vé trước giờ chiếu, và tải vé PDF; quản trị viên tạo suất
chiếu và tải lên hình ảnh poster. Yêu cầu duy nhất chi phối mọi quyết định
khác trong workshop này là **một ghế không bao giờ được bán hai lần**, ngay
cả khi hai khách hàng cùng click vào một ghế trong cùng một thời điểm - đây
là lý do vì sao booking transaction, chiến lược locking, và bài kiểm thử
concurrency đều được dành riêng một mục chứ không chỉ nhắc qua loa.

Việc triển khai chạy hoàn toàn trong **ap-southeast-1** và phát triển theo
từng giai đoạn xuyên suốt workshop này thay vì xuất hiện hoàn chỉnh ngay từ
đầu: một trang web React tĩnh trên Amazon S3, một API Express trên Amazon
EC2 phía sau một Application Load Balancer với hai instance trải trên hai
Availability Zone, PostgreSQL trên Amazon RDS chạy Multi-AZ bên trong một
private subnet, Amazon CloudFront (kèm AWS WAF) đứng trước cả trang web lẫn
API để phục vụ HTTPS trên một domain duy nhất, và Amazon CloudWatch cùng SNS
theo dõi toàn bộ hệ thống. Bản thân lớp compute cuối cùng hoàn toàn private:
cả hai EC2 instance đều nằm sau một NAT gateway, không có public IP và
không mở bất kỳ cổng SSH nào, thay vào đó được quản trị thông qua AWS Systems
Manager Session Manager - cùng tư thế vận hành mà database đã có ở mục
5.5.1, đạt được vì cùng một lý do.

Mỗi mục bên dưới xây dựng dựa trên trạng thái để lại từ mục trước đó. Hãy
làm theo đúng thứ tự trong lần đầu tiên thực hiện.

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
12. [Repository, trang web, và video demo](5.12-Links-and-Demo/)
