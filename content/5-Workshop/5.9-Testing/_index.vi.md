---
title : "Kiểm thử"
date : 2026-06-01
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Tổng quan

Mọi bài kiểm thử trong phần này đều chạy trên hệ thống đã được triển khai -
qua CloudFront, qua load balancer, tới instance RDS thực - chứ không phải
trên localhost, bởi vì tính đảm bảo đang được kiểm thử chính là điều chỉ
thực sự có ý nghĩa dưới hạ tầng thực: hai client độc lập, được định tuyến
đến bất kỳ instance nào trong hai EC2 instance mà load balancer quyết định,
cùng tranh chấp một dòng dữ liệu (row) trong database tại cùng một thời
điểm.


#### Nội dung

- [Kiểm thử đặt trùng ghế](5.9.1-concurrency/)
- [Các trường hợp biên](5.9.2-edge-cases/)
