---
title : "Tạo các bucket"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---

Bốn bucket, được tạo trong `ap-southeast-1`, mỗi cái đều có quyết định về truy cập
công khai được đưa ra một cách có chủ đích chứ không để nguyên giá trị mặc định:

| Bucket | Truy cập công khai | Mục đích |
|---|---|---|
| `caerus-frontend-web` | Công khai (tạm thời) | Phục vụ trang React đã build |
| `caerus-images-dev` | Riêng tư | Ảnh poster sự kiện được tải lên |
| `caerus-tickets-dev` | Riêng tư | Vé PDF được sinh ra |
| `caerus-backend` | Riêng tư | Chỉ để tạm chứa gói zip triển khai |

1. **Tạo cả bốn** bằng **S3 Console → Create bucket**, cùng region với mọi thứ
   khác.

2. **`caerus-frontend-web`** là bucket duy nhất cần cho đọc công khai ở giai đoạn
   này - static website hosting (mục 5.6.2) đòi hỏi như vậy. Chỉ bỏ chọn **Block
   all public access** trên riêng bucket này.

3. **Ba bucket còn lại giữ nguyên Block all public access ở trạng thái BẬT** (giá
   trị mặc định). API chạm tới chúng qua một IAM role (mục 5.6.3), không bao giờ
   qua một bucket policy công khai - chẳng có lý do gì để một người bên ngoài ứng
   dụng đọc thẳng một poster hay một vé bằng URL, và đó chính là lý do vé được sinh
   ra cùng poster được tải lên đều đi qua presigned URL thay vì một bucket công
   khai, dù xét về mặt kỹ thuật cả hai đều là "ảnh mà ai có link cũng xem được".

{{% notice note %}}
Việc mở công khai `caerus-frontend-web` chỉ là tạm thời chứ không phải một quyết
định thiết kế lâu dài - mục 5.7.6 sẽ thay nó bằng một CloudFront distribution dùng
Origin Access Control, và tại thời điểm đó quyền truy cập công khai sẽ được tắt trở
lại, bucket chỉ còn tiếp cận được qua CloudFront.
{{% /notice %}}

<!-- ![Four buckets listed in the S3 console](/images/5-Workshop/5.6-S3/5.6.1-create-buckets/example.png) -->
