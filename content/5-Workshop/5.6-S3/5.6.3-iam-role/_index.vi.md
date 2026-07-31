---
title : "IAM Role cho việc tải ảnh lên"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---

1. **IAM Console → Roles → Create role**, trusted entity **AWS service**,
   use case **EC2**. Đặt tên **`caerus-ec2-s3-role`** - permission boundary
   của tài khoản sẽ từ chối bất kỳ tên role nào không có tiền tố `caerus-`,
   nên đây không phải là một lựa chọn về phong cách, mà là một yêu cầu bắt
   buộc.

2. **Gắn một inline policy** giới hạn đúng ba bucket mà API cần, không hơn
   không kém:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ReadDeployZip",
         "Effect": "Allow",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::caerus-backend/*"
       },
       {
         "Sid": "ReadWriteImages",
         "Effect": "Allow",
         "Action": ["s3:GetObject", "s3:PutObject"],
         "Resource": "arn:aws:s3:::caerus-images-dev/*"
       },
       {
         "Sid": "ReadWriteTickets",
         "Effect": "Allow",
         "Action": ["s3:GetObject", "s3:PutObject"],
         "Resource": "arn:aws:s3:::caerus-tickets-dev/*"
       }
     ]
   }
   ```

3. **Gắn role này vào EC2 instance** làm instance profile khi khởi tạo ở mục
   5.7.2 (hoặc sau đó, thông qua Actions → Security → Modify IAM role, trên
   một instance đang chạy sẵn).

<!-- ![Inline policy attached to caerus-ec2-s3-role](/images/5-Workshop/5.6-S3/5.6.3-iam-role/example.png) -->
