---
title : "Amazon S3"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Tổng quan

Bốn bucket, mỗi cái một nhiệm vụ. `caerus-frontend-web` phục vụ trang React đã
build trực tiếp ra công chúng - đây là bucket duy nhất trong bốn cái có tính công
khai, và cũng chỉ công khai cho tới khi mục 5.7.6 giao nhiệm vụ đó cho CloudFront.
`caerus-images-dev` chứa các ảnh poster sự kiện được tải lên và `caerus-tickets-dev`
chứa các vé PDF được sinh ra, cả hai đều riêng tư, cả hai đều chỉ được đọc qua các
presigned URL có thời hạn ngắn do API ký khi có yêu cầu. `caerus-backend` không
chứa gì ngoài gói triển khai đã nén dùng để đưa code lên EC2 ở mục 5.7 - nó là phần
đường ống hạ tầng, không thuộc về ứng dụng đang chạy, và đó là lý do nó sẽ không
xuất hiện trong luồng request của sơ đồ kiến trúc.



#### Nội dung

- [Tạo các bucket](5.6.1-create-buckets/)
- [Cấu hình static website hosting](5.6.2-static-hosting/)
- [IAM Role cho việc tải ảnh lên](5.6.3-iam-role/)
- [Kiểm tra tải lên và hiển thị poster](5.6.4-verify-upload/)
