---
title : "CloudFront cho Frontend"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---

`caerus-frontend-web` giữ nguyên trạng thái private trong suốt vòng đời của
nó. Amazon CloudFront, được thiết lập ở đây, là thứ duy nhất từng đọc được
bucket này - ngay từ bản build đầu tiên được deploy trở đi. Không có giai
đoạn trung gian nào mà site được phục vụ trực tiếp từ một S3 website endpoint
rồi sau đó mới bị khóa lại: tại thời điểm này origin duy nhất phía sau
distribution chính là bucket này; một khi API được deploy, mục 5.7.6 sẽ thêm
một origin thứ hai và một routing rule vào *cùng* distribution đó, nhưng
không có gì trong thiết lập bên dưới thay đổi khi điều đó xảy ra.

1. **Tạo một Origin Access Control** (CloudFront Console → Origin access →
   Create control setting), signing behaviour chọn **Sign requests
   (recommended)**, origin type **S3**. Đây là thứ cho phép bucket giữ được
   trạng thái hoàn toàn private trong khi vẫn có thể đọc được bởi đúng
   distribution này.

2. **Tạo distribution**, với **REST endpoint** của `caerus-frontend-web`
   (`caerus-frontend-web.s3.ap-southeast-1.amazonaws.com`) làm origin -
   không phải website endpoint, vì static website hosting không bao giờ được
   bật cho bucket này, và Origin Access Control dù sao cũng chỉ hoạt động với
   REST endpoint. Gắn OAC đã tạo ở bước 1. Đặt **Default root object:
   `index.html`**.

3. **Gắn bucket policy mà CloudFront tự tạo ra**, giới hạn phạm vi ở
   CloudFront service principal và ARN của đúng distribution này:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Sid": "AllowCloudFrontServicePrincipal",
       "Effect": "Allow",
       "Principal": { "Service": "cloudfront.amazonaws.com" },
       "Action": "s3:GetObject",
       "Resource": "arn:aws:s3:::caerus-frontend-web/*",
       "Condition": {
         "StringEquals": {
           "AWS:SourceArn": "arn:aws:cloudfront::<account-id>:distribution/<distribution-id>"
         }
       }
     }]
   }
   ```

4. **Thêm hai custom error response**, ánh xạ cả `403` và `404` về
   `/index.html` với HTTP response code `200`. Đây là cơ chế fallback cho
   single-page-application: React Router xử lý một route như `/events/3`
   hoàn toàn ở phía client, nên khi hard refresh trên URL đó vẫn phải trả về
   `index.html` thay vì một lỗi thực sự từ S3 - vốn không hề biết route đó
   tồn tại.

5. **Build và upload site**:

   ```bash
   cd frontend && npm run build
   ```

   Upload *nội dung bên trong* của `frontend/dist/` lên gốc bucket - không
   phải bản thân thư mục `dist`. Kéo thả cả thư mục vào sẽ tạo ra
   `caerus-frontend-web/dist/index.html` thay vì
   `caerus-frontend-web/index.html`, và distribution sẽ chỉ phục vụ một
   trang trắng.

6. **Chờ distribution deploy xong**, sau đó mở domain của nó
   (`dxxxxxxxxxxxxx.cloudfront.net`) và xác nhận application load được. Các
   lệnh gọi API sẽ chưa hoạt động - vì chưa có backend nào được deploy ở
   thời điểm này trong workshop - nhưng bản thân static application phải
   render được.

![CloudFront distribution with the S3 origin, OAC attached, and the site loading at the distribution domain](/images/5-Workshop/5.6-S3/caerus_cloudfront.png)
