---
title : "Tạo các bucket"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---

Bốn bucket, được tạo ở `ap-southeast-1`, và cả bốn đều được giữ hoàn toàn
private - **Block all public access BẬT** cho mọi bucket, không ngoại lệ:

| Bucket | Mục đích |
|---|---|
| `caerus-frontend-web` | Phục vụ trang React đã build, chỉ thông qua CloudFront (mục 5.6.2) |
| `caerus-images-dev` | Poster sự kiện được tải lên |
| `caerus-tickets-dev` | Vé PDF được sinh ra |
| `caerus-backend` | Chỉ để lưu tạm gói triển khai (zip) |

1. **Tạo cả bốn bucket** bằng **S3 Console → Create bucket**, cùng region với
   mọi thứ khác, để nguyên **Block all public access** ở trạng thái được
   chọn (mặc định) trên mọi bucket.

2. **Không bucket nào trong bốn bucket từng được public, kể cả bucket
   frontend.** `caerus-frontend-web` chỉ được đọc bởi Amazon CloudFront,
   thông qua Origin Access Control thiết lập ở mục kế tiếp - không có giai
   đoạn trung gian nào mà site được phục vụ trực tiếp từ một S3 website
   endpoint. Ba bucket còn lại chỉ được truy cập thông qua một IAM role (mục
   5.6.3): không có lý do gì để ai đó bên ngoài ứng dụng đọc trực tiếp một
   poster hay một vé qua URL, đó chính xác là lý do vé được sinh ra và
   poster được tải lên đi qua presigned URL thay vì một bucket public, mặc
   dù cả hai về mặt kỹ thuật đều là "hình ảnh mà bất kỳ ai có link đều có
   thể xem".

<!-- ![Four buckets listed in the S3 console, all with Block all public access ON](/images/5-Workshop/5.6-S3/5.6.1-create-buckets/example.png) -->
