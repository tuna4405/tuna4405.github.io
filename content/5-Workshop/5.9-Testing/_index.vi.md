---
title : "Kiểm thử"
date : 2026-06-01
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Tổng quan

Mọi bài kiểm thử trong mục này đều chạy trên hệ thống đã triển khai - đi qua
CloudFront, băng qua load balancer, tới đúng instance RDS thật - chứ không chạy trên
localhost, bởi cam kết đang được kiểm thử chính là cam kết chỉ có ý nghĩa khi đặt
trên hạ tầng thật: hai client độc lập, đi tới bất kỳ instance nào trong hai instance
EC2 mà load balancer tình cờ định tuyến chúng đến, cùng tranh nhau một dòng dữ liệu
tại cùng một khoảnh khắc.



#### Nội dung

- [Kiểm thử đặt trùng ghế](5.9.1-concurrency/)
- [Các trường hợp biên](5.9.2-edge-cases/)
