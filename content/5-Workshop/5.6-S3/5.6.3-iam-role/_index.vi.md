---
title : "IAM Role cho việc tải ảnh lên"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---

1. **IAM Console → Roles → Create role**, trusted entity chọn **AWS service**, use
   case chọn **EC2**. Đặt tên là **`caerus-ec2-s3-role`** - permission boundary của
   tài khoản sẽ từ chối mọi tên role không mang tiền tố `caerus-`, nên đây không
   phải là chuyện tùy chọn về mặt hình thức, mà là quy định được cưỡng chế.

2. **Gắn một inline policy** thu hẹp đúng vào ba bucket mà API cần, và không gì
   khác:

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

3. **Gắn role vào instance EC2** dưới dạng instance profile của nó khi khởi chạy ở
   mục 5.7.2 (hoặc gắn sau, qua Actions → Security → Modify IAM role, trên một
   instance đang chạy).

**Vì sao dùng instance role thay vì một access key trong `.env`.** Một access key
dán vào file môi trường là một bí mật tồn tại lâu dài, phải được sinh ra, phân phát
cho từng lập trình viên, xoay vòng theo lịch, và giữ cho không lọt vào version
control chỉ bằng kỷ luật. Một instance role thì không dính thứ nào trong số đó: AWS
SDK trên instance gọi vào instance metadata service, nhận về credentials tạm thời
có thời hạn ngắn, giới hạn đúng theo policy của role này, và tự làm mới chúng trước
khi hết hạn - không có bí mật nào để rò rỉ, bởi ngay từ đầu đã không có bí mật tồn
tại lâu dài nào. Lý do duy nhất khiến `getSignedImageUrl()` (dùng để phát ra một
liên kết tải xuống có giới hạn thời gian cho một object riêng tư) hoạt động được mà
người gọi không bao giờ nhìn thấy credentials AWS thô là vì nó ký URL bằng chính
credentials của instance role ấy, ngay trên instance, và chỉ có URL đã ký là rời
khỏi máy chủ.

<!-- ![Inline policy attached to caerus-ec2-s3-role](/images/5-Workshop/5.6-S3/5.6.3-iam-role/example.png) -->
