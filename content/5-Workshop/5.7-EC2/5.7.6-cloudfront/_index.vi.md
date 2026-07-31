---
title : "CloudFront: Adding the API to the Same Domain"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.7.6 </b> "
---

Frontend đã được phục vụ qua CloudFront distribution tạo ở mục 5.6.2 từ
trước cả khi EC2 tồn tại. Phần này thêm nửa còn lại: định tuyến `/api/*` trên
*cùng* distribution đó tới load balancer, để toàn bộ ứng dụng - site và API -
đều truy cập được qua HTTPS trên một domain duy nhất. Không có gì ở origin S3
hiện tại, Origin Access Control, hay hành vi custom error response cho SPA
thay đổi cả; một **behavior** đơn giản chỉ ánh xạ một mẫu URL path tới một
origin cụ thể, và một distribution có thể chứa nhiều hơn một origin.

1. **Thêm một origin thứ hai** trỏ tới DNS name của load balancer, protocol
   **HTTP only** (bản thân load balancer không có listener HTTPS -
   CloudFront kết thúc TLS ở edge và nói chuyện HTTP thuần với origin, đây là
   hình dạng tiêu chuẩn của thiết lập này, không phải một đường tắt).

2. **Thêm một behavior cho `/api/*`** nhắm tới origin là load balancer:
   viewer protocol policy **Redirect HTTP to HTTPS**, allowed methods **GET,
   HEAD, OPTIONS, PUT, POST, PATCH, DELETE** (API dùng nhiều hơn chỉ GET),
   cache policy **CachingDisabled** (response của API không bao giờ được
   cache), origin request policy **AllViewerExceptHostHeader** (forward
   header `Authorization` mang JWT tới load balancer nguyên vẹn không thay
   đổi).

3. **Chờ distribution deploy xong**, sau đó trỏ frontend tới nó cho cả các
   lệnh gọi API:

   ```ini
   VITE_API_BASE_URL=https://<distribution-domain>/api/v1
   ```

   Build lại, upload lại `dist/`, và invalidate CloudFront cache (như mọi
   lần redeploy frontend kể từ mục 5.6.2 đều cần).

#### Hai điều đã gặp trục trặc ở đây, đáng biết trước

**Một `:3000` sót lại trong base URL của frontend thất bại theo cách trông
giống một vấn đề CORS hoặc kết nối, nhưng thực ra không phải cả hai.**
CloudFront chỉ chấp nhận các port chuẩn hướng tới viewer; một base URL được
copy tiếp từ thời kỳ EC2 đơn (`https://<distribution-domain>:3000/api/v1`)
đơn giản là không thể kết nối được - không phải lỗi quyền, không phải lỗi
CORS, chỉ là một kết nối không bao giờ hoàn tất. Tab network của trình duyệt
hiển thị một request không có bất kỳ response header nào cả, đó chính là dấu
hiệu nhận biết: so sánh với một lỗi CORS thực sự, vốn vẫn hoàn tất với một
response thật (bị từ chối). Cách sửa là bỏ hẳn port đi - các bước nhảy nội bộ
của CloudFront tới load balancer rồi tới port 3000 của instance là vô hình
đối với client theo thiết kế.

**Web ACL của WAF do CloudFront quản lý (từ mục 5.6.2) đã âm thầm chặn việc
upload poster ngay khi traffic API thật bắt đầu chạy qua nó, và custom error
response SPA hiện có đã che giấu hoàn toàn việc bị chặn đó.** Web ACL chạy
AWS Managed Rule Groups, bao gồm rule `SizeRestrictions_BODY` của
`AWSManagedRulesCommonRuleSet`, rule này mặc định chặn bất kỳ request body
nào lớn hơn khoảng 8 KB - ổn với payload JSON, nhưng một lần upload ảnh
poster thường lớn hơn thế rất nhiều. WAF trả về `403` cho request
`POST /api/v1/events/:id/banner` quá khổ, và custom error response được
thiết lập ở mục 5.6.2 (`403` → `/index.html` với override `200`, vốn chỉ nhằm
cho việc định tuyến SPA của origin S3) đã chặn ngang lỗi 403 đó và trả về
`index.html` của frontend kèm trạng thái `200` giả - nên trình duyệt thấy một
response tưởng như thành công mà không có object S3 nào được tạo và không có
lỗi nào tới được error handler của Express. Manh mối nằm ở response headers,
không phải mã trạng thái: `Server: AmazonS3` và `Content-Type: text/html`
trên một thứ lẽ ra phải là response JSON từ API. Cách sửa là một override
rule có mục tiêu cụ thể, không phải tắt WAF: mở Web ACL → rule group
`AWSManagedRulesCommonRuleSet` → override action của `SizeRestrictions_BODY`
thành **Count** thay vì **Block** kế thừa mặc định, để mọi rule khác trong
group đó (SQLi, XSS, các mẫu input xấu đã biết) vẫn được thực thi.

{{% notice note %}}
Đây cũng là một lời nhắc rằng các custom error response của CloudFront áp
dụng cho *toàn bộ distribution*, không chỉ origin mà chúng được viết cho -
một lỗi 403/404 từ origin API sẽ nhận cùng cách xử lý SPA-fallback như một
lỗi 403/404 từ origin S3. Không có override theo từng behavior cho việc này
trong luồng console tiêu chuẩn; đây là một hạn chế thực sự đáng để thiết kế
xung quanh hơn là một lỗi cấu hình cần sửa.
{{% /notice %}}

{{% notice note %}}
Sau phần này, việc gọi trực tiếp tới `<alb-dns-name>` vẫn còn tồn tại như một
URL, nhưng đó không phải cái nên được dùng nữa từ đây trở đi - domain của
distribution giờ là cửa ngõ duy nhất cho toàn bộ ứng dụng, bao gồm cả HTTPS.
{{% /notice %}}

<!-- ![CloudFront distribution behaviors: /api/* to the ALB origin, Default(*) to the S3 origin](/images/5-Workshop/5.7-EC2/5.7.6-cloudfront/example.png) -->
