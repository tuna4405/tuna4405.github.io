---
title : "CloudWatch và SNS"
date : 2026-06-01
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

#### Tổng quan

Khả năng quan sát trải khắp mọi dịch vụ trong kiến trúc cuối cùng - EC2, RDS, và
Application Load Balancer - được dựng hoàn toàn từ các metric mặc định, miễn phí,
cộng thêm một luồng log ứng dụng và những alarm đã được chứng minh là thực sự báo cho
ai đó chứ không nằm im chưa từng kiểm chứng ở trạng thái OK. Bản thân mục này không
làm tăng hóa đơn một đồng nào: EC2 Detailed Monitoring, S3 Request Metrics, và phần
CloudWatch Logs Insights vượt quá hạn mức miễn phí hằng tháng đều được cố ý bỏ qua -
chi phí thật của kiến trúc này đến từ load balancer, NAT gateway, và RDS Multi-AZ
(xem [Quản lý chi phí và tài nguyên](/5-Workshop/5.10-Cost/)), chứ không đến từ việc
giám sát nó.


#### Nội dung

- [Xây dựng dashboard](5.8.1-dashboard/)
- [Đẩy log ứng dụng](5.8.2-application-logs/)
- [Cảnh báo và thông báo](5.8.3-alarms/)
