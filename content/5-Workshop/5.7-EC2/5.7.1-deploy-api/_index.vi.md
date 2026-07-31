---
title : "Hạ tầng mạng Private và Instance Đầu Tiên"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.7.1 </b> "
---

1. **Tạo hai private subnet cho tầng ứng dụng**, `caerus-app-private-1a`
   (`ap-southeast-1a`) và `caerus-app-private-1b` (`ap-southeast-1b`), với các
   CIDR block không trùng với bất kỳ subnet hiện có nào, kể cả
   `caerus-private-1a`/`1b` của RDS.

![](/images/5-Workshop/5.7-EC2/ec2_private_subnet.png)

2. **Tạo một NAT gateway cho mỗi Availability Zone**, `caerus-nat-1a` và
   `caerus-nat-1b`, mỗi cái trong subnet **public** của đúng AZ đó (một NAT
   gateway phải nằm trong một subnet có route tới Internet Gateway, không bao
   giờ nằm trong một subnet private), mỗi cái với một Elastic IP mới cấp
   riêng.

![](/images/5-Workshop/5.7-EC2/ec2_nat.png)

3. **Tạo một route table cho mỗi AZ**, `caerus-app-private-rt-1a` (route
   `0.0.0.0/0` → `caerus-nat-1a`) và `caerus-app-private-rt-1b` (route
   `0.0.0.0/0` → `caerus-nat-1b`) - ngược lại với route table của RDS, và
   chính là toàn bộ lý do hai tầng dùng các subnet riêng biệt. Gắn mỗi route
   table với đúng subnet tương ứng ở bước 1.

4. **Cấp quyền Systems Manager** trên role của EC2 instance
   (`caerus-ec2-s3-role`, mục 5.6.3): gắn AWS-managed policy
   `AmazonSSMManagedInstanceCore`. Đây là thứ cho phép SSM Agent vốn đã chạy
   sẵn trên Amazon Linux tự đăng ký và chấp nhận lệnh - hoàn toàn qua một kết
   nối đi ra mà các NAT gateway giờ cung cấp, không cần rule inbound nào cả.
   Không key pair, không SSH, ở bất kỳ thời điểm nào trong dự án này.

#### Khởi tạo instance đầu tiên

5. **Khởi tạo `caerus-server-1`** - Amazon Linux 2023, `t3.micro`, **không
   key pair**, subnet `caerus-app-private-1a`, auto-assign public IP **tắt**,
   instance profile `caerus-ec2-s3-role`, gắn tag `Owner`. Nó không có IP
   công khai theo chủ đích - load balancer ở mục 5.7.5 sẽ là thứ duy nhất
   từng gọi trực tiếp tới nó.

![](/images/5-Workshop/5.7-EC2/ec2_launch.png)

6. **Kết nối qua Session Manager**, không phải EC2 Instance Connect - instance
   không có IP công khai để Instance Connect có thể chạm tới ngay từ đầu. EC2
   Console → chọn `caerus-server-1` → **Connect → Session Manager →
   Connect
   
![](/images/5-Workshop/5.7-EC2/ec2_ssm.png)

   quyền sở hữu file và process `pm2` khởi
   động ở dưới khớp với đúng account mà bất kỳ tự động hoá nào sau này mong
   đợi:

   ```bash
   sudo su - ec2-user
   sudo dnf install -y nodejs20 postgresql16 unzip
   mkdir -p ~/caerus-api && cd ~/caerus-api
   aws s3 cp s3://caerus-backend/backend.zip .
   unzip backend.zip
   npm ci --omit=dev
   ```

7. **Tạo `.env`** - file duy nhất không bao giờ nằm trong file zip và không
   bao giờ nằm trong version control:

   ```ini
   DATABASE_URL=postgresql://<user>:<password>@<rds-endpoint>:5432/caerus
   JWT_SECRET=<random, e.g. `openssl rand -hex 32`>
   PORT=3000
   CINEMA_TIMEZONE=Asia/Ho_Chi_Minh
   AWS_REGION=ap-southeast-1
   S3_BUCKET_IMAGES=caerus-images-dev
   S3_BUCKET_TICKETS=caerus-tickets-dev
   ```

8. **Chạy dưới `pm2`** để process sống sót qua việc mất kết nối và khởi động
   lại:

   ```bash
   sudo npm install -g pm2
   pm2 start src/server.js --name caerus-api
   pm2 startup   # run the sudo command it prints
   pm2 save
   ```

9. **Kiểm tra từ bên trong session trước**:

    ```bash
    curl http://localhost:3000/api/v1/health
    # {"ok":true}
    ```

    sau đó, từ máy của developer, mà không bao giờ phải phơi instance ra
    internet: mở một phiên port-forwarding cục bộ -

    ```bash
    aws ssm start-session --target <instance-id> \
      --document-name AWS-StartPortForwardingSession \
      --parameters '{"portNumber":["3000"],"localPortNumber":["3000"]}'
    ```

    rồi `curl http://localhost:3000/api/v1/health` qua đường hầm đó từ một
    terminal thứ hai. Đây là cách kiểm tra từ bên ngoài được dùng cho mọi lần
    xác minh một instance private cho tới khi mục 5.7.5 cấp cho tầng compute
    một điểm vào công khai thực sự.

<!-- ![Session Manager connected to caerus-server-1, and a curl response through an SSM port-forwarding tunnel](/images/5-Workshop/5.7-EC2/5.7.2-deploy-api/example.png) -->
