---
title : "Thực hành khởi tạo và huỷ instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.7.1 </b> "
---

Trước khi động tới các instance thực của dự án, mỗi thành viên trong nhóm đã
tự thực hiện bài tập này độc lập trên IAM user của riêng mình, chỉ nhằm xây
dựng phản xạ khởi tạo, kết nối và dọn dẹp.

1. **Khởi tạo** một instance Amazon Linux thuộc diện Free Tier, kết nối qua
   EC2 Instance Connect, và cài đặt Node.js.
2. **Chạy một thứ gì đó thực sự trên đó** - một HTTP server nhỏ có thể truy
   cập được từ trình duyệt tại địa chỉ IP công khai của instance, xác nhận
   rằng rule inbound của security group thực sự cho phép loại lưu lượng mà
   nó tuyên bố cho phép.
3. **Huỷ instance ngay trong ngày**, sau đó quay lại Console và xác nhận
   không còn gì sót lại - không instance, không Elastic IP mồ côi.

{{% notice note %}}
Một Elastic IP không được gắn và một instance đang chạy đều bị tính phí theo
giờ bất kể có ai đang sử dụng hay không. "Huỷ tài nguyên thực hành ngay trong
ngày" không phải là một gợi ý trong dự án này, mà là thói quen duy nhất giữ
cho mức sử dụng Free Tier của một nhóm hai người nằm trong hạn mức 750 giờ mỗi
tháng, vì hạn mức đó được chia sẻ chung cho mọi instance chạy đồng thời, không
được cấp riêng cho từng instance.
{{% /notice %}}

<!-- ![Instance running, then the same instance's terminated state in the Console](/images/5-Workshop/5.7-EC2/5.7.1-launch-practice/example.png) -->
