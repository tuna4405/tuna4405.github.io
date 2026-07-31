---
title : "CloudWatch và SNS"
date : 2026-06-01
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

#### Tổng quan

Khả năng quan sát (observability) cho mọi dịch vụ trong kiến trúc cuối cùng -
EC2, RDS, và Application Load Balancer - được xây dựng hoàn toàn từ các
metric mặc định, miễn phí, cộng với một log stream ứng dụng và các alarm đã
được chứng minh là thực sự gửi thông báo đến ai đó, chứ không chỉ nằm im ở
trạng thái OK mà chưa từng được kiểm chứng. Bản thân phần này không làm phát
sinh thêm chi phí nào: EC2 Detailed Monitoring, S3 Request Metrics, và
CloudWatch Logs Insights vượt quá hạn mức miễn phí hàng tháng đều được chủ
động bỏ qua - chi phí thực sự của kiến trúc này đến từ load balancer, NAT
gateway, và RDS Multi-AZ (xem [Quản lý chi phí và tài nguyên](/5-Workshop/5.10-Cost/)),
chứ không phải từ việc giám sát nó.


#### Nội dung

- [Xây dựng dashboard](5.8.1-dashboard/)
- [Cảnh báo và thông báo](5.8.2-alarms/)
