---
title : "Amazon S3"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Tổng quan

Bốn bucket, mỗi bucket một nhiệm vụ, và cả bốn đều hoàn toàn private ngay từ
lúc được tạo ra. `caerus-frontend-web` phục vụ trang React đã build, chỉ được
đọc bởi CloudFront distribution thiết lập ở phần này - không có giai đoạn nào
mà nó được phục vụ trực tiếp từ một S3 website endpoint trước. `caerus-images-dev`
chứa poster sự kiện được tải lên và `caerus-tickets-dev` chứa vé PDF được sinh
ra, cả hai chỉ được đọc thông qua các presigned URL có thời hạn ngắn mà API ký
khi có yêu cầu. `caerus-backend` không chứa gì ngoài gói triển khai đã nén
dùng để đưa code lên EC2 ở mục 5.7 - đây là hạ tầng phụ trợ, không phải một
phần của ứng dụng đang chạy, đó là lý do nó sẽ không xuất hiện trong luồng
request của sơ đồ kiến trúc. Chưa có bucket nào trong số này làm được gì có
thể quan sát cả - chưa có API nào đang chạy để ghi vào `caerus-images-dev`
hay đọc từ `caerus-backend` cho tới khi mục 5.7 tồn tại; object thật đầu
tiên thực sự nằm trong bất kỳ bucket nào, và luồng upload poster được kiểm
chứng end-to-end, sẽ được kiểm tra ở mục 5.7.7.

#### Nội dung

- [Tạo các bucket](5.6.1-create-buckets/)
- [CloudFront cho Frontend](5.6.2-cloudfront-for-frontend/)
- [IAM Role cho việc tải ảnh lên](5.6.3-iam-role/)
