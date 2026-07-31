---
title : "Kiểm tra tải lên và hiển thị poster"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.6.4 </b> "
---

Vì `caerus-images-dev` là bucket riêng tư (mục 5.6.1), luồng xử lý poster không phải
là "tải file lên, lưu URL của nó, xong" - mà là tải file lên, lưu **key** của
object, và ký một URL mới có giới hạn thời gian mỗi lần poster thực sự được hiển
thị.

1. **Đăng nhập với tư cách admin** rồi mở **Create screening**, điền thông tin suất
   chiếu và đính kèm một ảnh poster khổ dọc tỉ lệ 2:3 (`image/jpeg` hoặc
   `image/png`, tối đa 5 MB, được `multer` kiểm soát ngay ở đầu vào).

2. **Lần theo request**: `POST /events/:id/banner` nhận ảnh dưới dạng multipart form
   data, tải buffer lên `caerus-images-dev` với một key dạng
   `events/{id}/banner.jpg`, và lưu chính **key** đó - không phải một URL - vào cột
   `banner_url` của sự kiện.

3. **Xác nhận object đã nằm trong S3**: mở `caerus-images-dev` trong Console và tìm
   thấy object vừa tải lên ở đúng key mong đợi.

4. **Tải lại trang danh sách sự kiện hoặc trang chi tiết sự kiện** và xác nhận
   poster hiển thị được. Điều thực sự diễn ra ở mỗi request `GET /events` là API lấy
   key đã lưu rồi gọi `getSignedImageUrl()`, tạo ra một presigned URL có hiệu lực
   trong một giờ trước khi trao response cho trình duyệt - bản thân bucket không bao
   giờ trở thành công khai, và URL mà một trình duyệt nhìn thấy là dùng một lần theo
   nghĩa nó ngừng hoạt động sau khi hết hạn.

{{% notice note %}}
Hãy mở tab network của trình duyệt và soi trường `bannerUrl` trong response của
`GET /events`: đó là một URL đầy đủ dạng `https://caerus-images-dev.s3...` mang theo
`X-Amz-Signature`, `X-Amz-Expires`, và các tham số truy vấn liên quan - bằng chứng
nhìn thấy được rằng URL đã được ký và chỉ tồn tại tạm thời, chứ không phải một liên
kết công khai thông thường.
{{% /notice %}}

<!-- ![Event card rendering the uploaded poster, and the signed URL in the network tab](/images/5-Workshop/5.6-S3/5.6.4-verify-upload/example.png) -->
