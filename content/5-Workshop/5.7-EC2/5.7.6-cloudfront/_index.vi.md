---
title : "CloudFront: Một Domain HTTPS Duy Nhất"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.7.6 </b> "
---

Đến thời điểm này, trang web được phục vụ qua HTTP thuần từ một website endpoint của
S3, còn API được phục vụ qua HTTP thuần từ load balancer, trên hai tên miền khác
nhau. Mục này đặt một Amazon CloudFront distribution duy nhất đứng trước cả hai, để
toàn bộ ứng dụng truy cập được qua HTTPS trên một tên miền - và làm điều đó bằng
định tuyến theo đường dẫn của CloudFront chứ không phải bằng hai distribution riêng
biệt.

#### Ý tưởng gói trong một câu

Một CloudFront distribution có thể chứa nhiều hơn một origin. Một **behavior** ánh
xạ một mẫu đường dẫn URL tới một origin cụ thể: request tới `/api/*` đi về load
balancer, còn mọi request khác (behavior mặc định) đi về bucket S3 chứa trang web.
Cả hai đều được phục vụ từ *cùng* một tên miền của distribution
(`dxxxxxxxxxxxxx.cloudfront.net`) - CloudFront không cần, và cũng không dùng, một
tên miền riêng cho mỗi origin.

1. **Tạo một Origin Access Control** (CloudFront Console → Origin access → Create
   control setting), hành vi ký chọn **Sign requests (recommended)**, loại origin
   chọn **S3**. Đây là thứ sẽ cho phép origin S3 quay về trạng thái hoàn toàn riêng
   tư.

2. **Tạo distribution** với bucket S3 làm origin đầu tiên (mặc định), chọn **REST
   endpoint** của bucket (`caerus-frontend-web.s3.ap-southeast-1.amazonaws.com`),
   *chứ không phải* website endpoint mà console gợi ý cho một bucket đã bật static
   hosting - Origin Access Control chỉ hoạt động với REST endpoint. Gắn OAC từ bước
   1 vào. Đặt **Default root object: `index.html`**.

3. **Thay bucket policy cho đọc công khai** bằng policy mà CloudFront đề nghị sinh
   ra, thu hẹp theo service principal của CloudFront và ARN của đúng distribution
   này:

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

   Sau đó **tắt hẳn Static website hosting** trên bucket và **bật lại Block all
   public access** - bucket trở lại riêng tư, và thứ duy nhất đọc được nó là đúng
   distribution này.

4. **Thêm một origin thứ hai** trỏ tới DNS name của load balancer, giao thức **HTTP
   only** (bản thân load balancer không có listener HTTPS - CloudFront kết thúc TLS
   ngay tại biên rồi nói chuyện HTTP thuần với origin, đây là hình hài chuẩn của
   thiết lập này chứ không phải một lối đi tắt).

5. **Thêm một behavior cho `/api/*`** trỏ tới origin load balancer: viewer protocol
   policy chọn **Redirect HTTP to HTTPS**, các method được phép là **GET, HEAD,
   OPTIONS, PUT, POST, PATCH, DELETE** (API dùng nhiều hơn mỗi GET), cache policy
   chọn **CachingDisabled** (response của API tuyệt đối không được cache), origin
   request policy chọn **AllViewerExceptHostHeader** (chuyển tiếp nguyên vẹn header
   `Authorization` mang JWT xuống load balancer).

6. **Thêm hai custom error response** trên distribution, ánh xạ cả `403` lẫn `404`
   về `/index.html` với mã HTTP trả về là `200` - việc này thay cho vai trò SPA
   fallback mà thiết lập error document của chính S3 từng đảm nhiệm, giờ khi static
   website hosting đã tắt.

7. **Chờ distribution triển khai xong**, rồi trỏ frontend vào nó:

   ```ini
   VITE_API_BASE_URL=https://<distribution-domain>/api/v1
   ```

   Build lại và tải `dist/` lên lại.

#### Hai thứ đã hỏng ở đây, nên biết trước

**Các lớp bảo vệ WAF đi kèm CloudFront cần những quyền IAM mà tài khoản có thể chưa
cấp.** Bật các lớp bảo vệ bảo mật thuộc gói miễn phí trong lúc tạo distribution sẽ
gọi `wafv2:CreateWebACL` thay mặt người dùng, nhắm vào một tài nguyên WAF nằm cụ thể
ở **`us-east-1`** - WAF dành cho CloudFront luôn nằm ở đó bất kể phần còn lại của
kiến trúc dùng region nào. Nếu IAM user thiếu quyền này, việc tạo distribution sẽ
thất bại với một lỗi `AccessDenied` nêu đích danh action và resource. Cách sửa là
một inline policy cấp `wafv2:CreateWebACL` cùng các quyền đi kèm thường thấy
(`UpdateWebACL`, `DeleteWebACL`, `GetWebACL`, `TagResource`) - và, ít hiển nhiên
hơn, một câu lệnh *thứ hai* cho loại tài nguyên `managedruleset`, bởi các lớp bảo vệ
mặc định của CloudFront tham chiếu tới AWS Managed Rule Groups và WAF kiểm tra quyền
trên cả Web ACL đang được tạo lẫn mọi managed rule group mà nó tham chiếu.

**Một cái đuôi `:3000` còn sót lại trong base URL của frontend gây ra lỗi trông
giống vấn đề CORS hoặc kết nối nhưng thực chất không phải cả hai.** CloudFront chỉ
chấp nhận các cổng chuẩn hướng về phía người xem; một base URL bê nguyên từ thời còn
một EC2 duy nhất (`https://<distribution-domain>:3000/api/v1`) đơn giản là không thể
kết nối - không phải lỗi quyền, không phải lỗi CORS, chỉ là một kết nối không bao giờ
hoàn tất. Tab network của trình duyệt hiện ra một request hoàn toàn không có response
header nào, và đó chính là dấu hiệu nhận biết: hãy so với một lỗi CORS thật sự, vốn
vẫn hoàn tất với một response (bị từ chối) có thật. Cách sửa là bỏ hẳn cổng đi - các
chặng nội bộ của CloudFront tới load balancer rồi tới cổng 3000 của instance vốn được
thiết kế để client không nhìn thấy.

**Web ACL do CloudFront quản lý đã âm thầm chặn việc tải poster, và custom error
response ở bước 6 che giấu lần chặn ấy hoàn toàn.** Bật các lớp bảo vệ đi kèm của
CloudFront sẽ tạo ra một Web ACL chạy các AWS Managed Rule Group, trong đó có rule
`SizeRestrictions_BODY` thuộc `AWSManagedRulesCommonRuleSet`, vốn mặc định chặn mọi
request body lớn hơn khoảng 8 KB - ổn với payload JSON, nhưng một lần tải ảnh poster
lên thì thường lớn hơn thế rất nhiều. WAF trả về `403` cho request quá khổ
`POST /api/v1/events/:id/banner`, và chính custom error response của distribution
(bước 6, `403` → `/index.html` kèm ghi đè mã `200`, vốn chỉ dành cho việc định tuyến
SPA từ origin S3) đã bắt lấy mã 403 đó rồi trả về `index.html` của frontend với một
trạng thái `200` giả - thành ra trình duyệt nhìn thấy một response có vẻ thành công
trong khi không có object S3 nào được tạo và không có lỗi nào tới được error handler
của Express. Dấu hiệu nhận biết nằm ở response header chứ không phải mã trạng thái:
`Server: AmazonS3` và `Content-Type: text/html` trên thứ lẽ ra phải là một response
JSON từ API. Cách sửa là ghi đè có trọng tâm một rule, chứ không phải tắt WAF: mở Web
ACL → nhóm rule `AWSManagedRulesCommonRuleSet` → ghi đè action của
`SizeRestrictions_BODY` thành **Count** thay cho **Block** được kế thừa, để mọi rule
còn lại trong nhóm (SQLi, XSS, các mẫu đầu vào độc hại đã biết) vẫn tiếp tục được
cưỡng chế.

{{% notice note %}}
Đây cũng là một lời nhắc rằng custom error response của CloudFront áp dụng cho *toàn
bộ distribution*, chứ không riêng origin mà nó được viết ra để phục vụ - một mã
403/404 từ origin API cũng bị đối xử theo kiểu SPA fallback y hệt một mã 403/404 từ
origin S3. Trong luồng console tiêu chuẩn không có cách ghi đè theo từng behavior cho
chuyện này; đây là một hạn chế có thật, đáng để thiết kế vòng qua chứ không phải một
lỗi cấu hình để đi sửa.
{{% /notice %}}

{{% notice note %}}
Sau mục này, website endpoint cũ của `caerus-frontend-web` và một lời gọi trực tiếp
tới `<alb-dns-name>` đều vẫn tồn tại dưới dạng URL, nhưng không cái nào là thứ nên
được dùng về sau nữa - tên miền của distribution giờ là cánh cửa chính duy nhất cho
cả ứng dụng, HTTPS bao gồm luôn trong đó.
{{% /notice %}}

<!-- ![CloudFront distribution behaviors: /api/* to the ALB origin, Default(*) to the S3 origin](/images/5-Workshop/5.7-EC2/5.7.6-cloudfront/example.png) -->
