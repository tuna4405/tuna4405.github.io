---
title : "Build frontend và xử lý CORS"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.7.4 </b> "
---

1. **Trỏ frontend sang API đã triển khai** bằng cách đặt `VITE_API_BASE_URL` trong
   `frontend/.env` thành `http://<ec2-public-ip>:3000/api/v1`, rồi build và tải
   lên:

   ```bash
   cd frontend
   npm run build
   ```

   Tải *nội dung bên trong* `dist/` lên `caerus-frontend-web` (lời cảnh báo ở mục
   5.6.2 về việc đừng kéo thả cả thư mục vẫn áp dụng ở đây).

2. **Mở website endpoint của trang và hãy chờ đợi nó lỗi.** Console của trình duyệt
   sẽ hiện ra đại ý như sau:

   > Access to fetch at `http://<ec2-ip>:3000/api/v1/events` from origin
   > `http://caerus-frontend-web.s3-website-ap-southeast-1.amazonaws.com`
   > has been blocked by CORS policy: No 'Access-Control-Allow-Origin'
   > header is present on the requested resource.

   Đây là điều đã lường trước, không phải một lỗi để phải bất ngờ - trang web và API
   nằm ở hai origin khác nhau (khác hẳn host), và trình duyệt cưỡng chế chính sách
   same-origin thay mặt cho *frontend*, bất kể phía backend có ý định cho phép những
   gì.

3. **Sửa ở phía backend**, chứ không phải bằng cách đấu với trình duyệt: `app.js`
   liệt kê chính xác các origin được phép gọi API.

   ```js
   const allowedOrigins = [
     'http://caerus-frontend-web.s3-website-ap-southeast-1.amazonaws.com',
     'http://localhost:5173',
   ];
   app.use(cors({ origin: allowedOrigins }));
   ```

   {{% notice warning %}}
   Hãy ghi đúng **region** trong chuỗi này. Một dòng whitelist sai region
   (`us-east-1` chép từ một ví dụ thay vì `ap-southeast-1` mà dự án thực sự dùng)
   nhìn thoáng qua thì thấy đúng nhưng lại lỗi với đúng thông báo CORS y hệt, khiến
   nó trở thành một sai sót rất dễ bị đẩy lên và hơi khó chịu để phát hiện - nếu gặp
   trường hợp này, hãy đối chiếu thanh địa chỉ của trình duyệt với chuỗi trong
   `app.js` từng ký tự một.
   {{% /notice %}}

4. **Triển khai lại backend** (lặp lại các bước liên quan ở mục 5.7.2) rồi tải lại
   trang - đúng cái request đã lỗi ở bước 2 giờ đã thành công.

<!-- ![Browser console showing the CORS error, and the same request succeeding after the fix](/images/5-Workshop/5.7-EC2/5.7.4-frontend-and-cors/example.png) -->
