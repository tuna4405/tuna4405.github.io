---
title : "Triển khai API với pm2"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---

1. **Khởi chạy instance** - Amazon Linux 2023, `t3.micro`, không dùng key pair (chỉ
   EC2 Instance Connect), gắn instance profile `caerus-ec2-s3-role` từ mục 5.6.3, và
   gắn tag `Owner`.

2. **Đóng gói backend ở máy cục bộ** rồi trung chuyển qua bucket triển khai thay vì
   `git clone` thẳng lên instance, nhờ đó instance không bao giờ cần có thông tin
   đăng nhập GitHub của riêng nó:

   ```powershell
   robocopy backend "$env:TEMP\deploy" /E /XD node_modules .git /XF .env
   Compress-Archive -Path "$env:TEMP\deploy\*" -DestinationPath "$env:TEMP\backend.zip" -Force
   ```

   Tải `backend.zip` lên `caerus-backend`.

3. **Trên instance**, thông qua EC2 Instance Connect:

   ```bash
   sudo dnf install -y nodejs20 postgresql16 unzip
   mkdir -p ~/caerus-api && cd ~/caerus-api
   aws s3 cp s3://caerus-backend/backend.zip .
   unzip backend.zip
   npm ci --omit=dev
   ```

4. **Tạo file `.env`** - file duy nhất không bao giờ nằm trong gói zip và không bao
   giờ nằm trong version control:

   ```ini
   DATABASE_URL=postgresql://<user>:<password>@<rds-endpoint>:5432/caerus
   JWT_SECRET=<random, e.g. `openssl rand -hex 32`>
   PORT=3000
   CINEMA_TIMEZONE=Asia/Ho_Chi_Minh
   AWS_REGION=ap-southeast-1
   S3_BUCKET_IMAGES=caerus-images-dev
   S3_BUCKET_TICKETS=caerus-tickets-dev
   ```

5. **Chạy dưới `pm2`** để tiến trình sống sót qua một lần mất kết nối và một lần
   khởi động lại máy:

   ```bash
   sudo npm install -g pm2
   pm2 start src/server.js --name caerus-api
   pm2 startup   # run the sudo command it prints
   pm2 save
   ```

6. **Kiểm chứng**, trước tiên ngay trên instance, sau đó từ bên ngoài:

   ```bash
   curl http://localhost:3000/api/v1/health
   # {"ok":true}
   ```

   rồi từ trình duyệt hoặc `curl` trên máy của chính lập trình viên, gọi đúng đường
   dẫn đó tới public DNS name của instance.

<!-- ![pm2 status showing caerus-api online, and a curl response from outside the instance](/images/5-Workshop/5.7-EC2/5.7.2-deploy-api/example.png) -->
