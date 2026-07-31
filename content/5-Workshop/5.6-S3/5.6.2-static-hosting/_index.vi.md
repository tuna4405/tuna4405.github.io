---
title : "Cấu hình static website hosting"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---

1. **`caerus-frontend-web` → Properties → Static website hosting → Enable**, với
   **Index document: `index.html`** và **Error document: `index.html`** - cùng một
   file cho cả hai, bởi đây là một ứng dụng single-page: React Router xử lý
   `/events/3` ở phía client, nên một lần tải lại cứng trên URL đó vẫn phải trả về
   `index.html` chứ không phải một lỗi 404 thẳng thừng từ S3.

2. **Gắn một bucket policy cho phép đọc công khai:**

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Sid": "PublicReadGetObject",
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:GetObject",
       "Resource": "arn:aws:s3:::caerus-frontend-web/*"
     }]
   }
   ```

3. **Build và tải trang web lên**:

   ```bash
   cd frontend && npm run build
   ```

   Sau đó tải *nội dung bên trong* `frontend/dist/` lên thư mục gốc của bucket -
   không phải bản thân thư mục `dist`. Kéo thả cả thư mục vào sẽ tạo ra
   `caerus-frontend-web/dist/index.html` thay vì
   `caerus-frontend-web/index.html`, và website endpoint sẽ hiện ra một danh sách
   trống thay vì ứng dụng.

4. **Mở website endpoint** (tab Properties, ở cuối khung Static website hosting) và
   xác nhận ứng dụng tải lên được.

<!-- ![The built React site served from the S3 website endpoint](/images/5-Workshop/5.6-S3/5.6.2-static-hosting/example.png) -->
