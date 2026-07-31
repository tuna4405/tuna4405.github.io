---
title : "Build frontend và xử lý CORS"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.7.4 </b> "
---

`caerus-server-1` không có IP công khai, nên chưa gì trên internet - kể cả
frontend đã chạy trên CloudFront từ mục 5.6.2 - có thể chạm tới nó được. Load
balancer, thứ cuối cùng mở ra một đường vào thực sự, chưa tồn tại cho tới mục
5.7.5, nhưng vấn đề CORS mà nó rồi sẽ phơi bày ra thì rẻ hơn nhiều để tìm và
sửa ngay bây giờ, ở local, thay vì phát hiện ra lần đầu tiên trên traffic
production.

1. **Mở lại đường hầm SSM port-forwarding từ mục 5.7.2** ở một terminal, giữ
   nó chạy, và trỏ một dev server frontend *cục bộ* tới đường hầm đó thay vì
   build cho CloudFront:

   ```ini
   # frontend/.env
   VITE_API_BASE_URL=http://localhost:3000/api/v1
   ```

   ```bash
   cd frontend
   npm run dev
   ```

2. **Mở dev server trên trình duyệt (`http://localhost:5173`) và chờ các
   lệnh gọi API vẫn thất bại**, bất kể đã có đường hầm. Console của trình
   duyệt sẽ hiển thị một thông báo đại loại như:

   > Access to fetch at `http://localhost:3000/api/v1/events` from origin
   > `http://localhost:5173` has been blocked by CORS policy: No
   > 'Access-Control-Allow-Origin' header is present on the requested
   > resource.

   Đây là điều được dự đoán trước, không phải một lỗi đáng ngạc nhiên - dev
   server và API được tunnel vẫn là hai origin khác nhau (khác port cũng
   được tính là khác origin), và trình duyệt thực thi same-origin policy
   thay mặt cho *frontend*, bất kể backend có ý định cho phép điều gì, và bất
   kể đường hầm SSM bên dưới chỉ là một chi tiết hạ tầng mà trình duyệt không
   bao giờ nhìn thấy.

3. **Sửa ở phía backend**, không phải bằng cách chống lại trình duyệt:
   `app.js` whitelist chính xác (các) origin được phép gọi API.

   ```js
   const allowedOrigins = [
     'https://<distribution-domain>',
     'http://localhost:5173',
   ];
   app.use(cors({ origin: allowedOrigins }));
   ```

   Domain của CloudFront distribution được đưa vào danh sách này ngay từ bây
   giờ dù chưa có gì chạm tới được nó theo kiểu end-to-end cho tới mục 5.7.5 -
   origin mà trình duyệt cuối cùng sẽ gửi không bao giờ thay đổi, chỉ có địa
   chỉ của backend là thay đổi, nên sẽ không cần làm lại gì ở đây sau này.

   {{% notice warning %}}
   Ghi đúng **origin** trong danh sách này - scheme, host, và không có dấu
   gạch chéo ở cuối. Một mục gần đúng nhưng không giống hệt (sai scheme, một
   domain cũ được copy từ một giai đoạn trước đó) nhìn qua có vẻ đúng và vẫn
   thất bại với đúng lỗi CORS y hệt, điều này khiến nó thực sự là một lỗi dễ
   vô tình đưa lên production và hơi khó phát hiện ra - nếu gặp trường hợp
   này, hãy so sánh thanh địa chỉ của trình duyệt với chuỗi trong `app.js`
   từng ký tự một.
   {{% /notice %}}

4. **Triển khai lại backend** (lặp lại các bước liên quan ở mục 5.7.2, qua
   cùng phiên SSM) và tải lại `http://localhost:5173` - cùng request đã thất
   bại ở bước 2 giờ sẽ thành công, kể cả qua đường hầm.

<!-- ![Browser console showing the CORS error against the SSM-tunneled backend, and the same request succeeding after the fix](/images/5-Workshop/5.7-EC2/5.7.4-frontend-and-cors/example.png) -->
