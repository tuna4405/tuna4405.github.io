---
title : "Thực hành khởi tạo và huỷ instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.7.1 </b> "
---

Trước khi động tới các instance của chính dự án, mỗi thành viên trong nhóm tự chạy
bài tập này một cách độc lập trên IAM user của riêng mình, thuần túy để tạo phản xạ
cho ba việc: khởi chạy, kết nối, và dọn dẹp.

1. **Khởi chạy** một instance Amazon Linux thuộc diện Free Tier, kết nối qua EC2
   Instance Connect, và cài Node.js.
2. **Cho nó chạy một thứ gì đó thật** - một HTTP server nhỏ truy cập được từ trình
   duyệt tại public IP của instance, qua đó xác nhận rule inbound của security group
   thực sự cho qua đúng loại traffic mà nó tuyên bố.
3. **Terminate ngay trong ngày**, rồi quay lại Console và xác nhận không còn gì đang
   chạy - không instance, không Elastic IP mồ côi.

{{% notice note %}}
Một Elastic IP không gắn vào đâu và một instance đang chạy đều tính tiền theo giờ,
bất kể có ai đang dùng hay không. "Terminate tài nguyên thực hành ngay trong ngày"
không phải là một lời khuyên trong dự án này, mà là thói quen duy nhất giữ cho mức
sử dụng Free Tier của một nhóm hai người nằm trong hạn mức 750 giờ mỗi tháng, bởi
hạn mức đó được chia chung cho mọi instance chạy đồng thời chứ không cấp riêng cho
từng instance.
{{% /notice %}}

<!-- ![Instance running, then the same instance's terminated state in the Console](/images/5-Workshop/5.7-EC2/5.7.1-launch-practice/example.png) -->
