---
title : "Hạ tầng mạng Private và Instance Đầu Tiên"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---

#### Vì sao dùng một cặp subnet riêng, không dùng chung với database

Các private subnet của RDS (mục 5.5.1) nằm sau một route table **không** có
bất kỳ route `0.0.0.0/0` nào cả - đúng đắn đối với RDS, vốn không bao giờ tự
khởi tạo lưu lượng đi ra, nhưng sai đối với EC2, vốn cần truy cập đi ra ngoài
cho `npm ci` và vá lỗi hệ điều hành. Dùng chung subnet của database sẽ có
nghĩa là phải thêm một route internet vào một route table vốn được xây dựng
có chủ đích là không có route đó, làm vẩn đục một quyết định thiết kế vốn đã
đúng như đã viết. Một cặp private subnet thứ hai, riêng biệt - mỗi
Availability Zone một subnet - giữ cho chính sách mạng của hai tầng độc lập
với nhau ngay cả khi cả hai đều kết thúc ở trạng thái "private" theo nghĩa
thông thường của từ này.

1. **Tạo hai private subnet cho tầng ứng dụng**, `caerus-app-private-1a`
   (`ap-southeast-1a`) và `caerus-app-private-1b` (`ap-southeast-1b`), với các
   CIDR block không trùng với bất kỳ subnet hiện có nào, kể cả
   `caerus-private-1a`/`1b` của RDS.

2. **Tạo một NAT gateway cho mỗi Availability Zone**, `caerus-nat-1a` và
   `caerus-nat-1b`, mỗi cái trong subnet **public** của đúng AZ đó (một NAT
   gateway phải nằm trong một subnet có route tới Internet Gateway, không bao
   giờ nằm trong một subnet private), mỗi cái với một Elastic IP mới cấp
   riêng.

   {{% notice note %}}
   Một NAT gateway cho mỗi AZ, thay vì dùng chung một cái, là một lựa chọn có
   chủ đích để giữ cho câu chuyện tính khả dụng của tầng compute nhất quán
   với thiết kế Multi-AZ của database (mục 5.5.1): nếu AZ chứa
   `caerus-nat-1a` gặp sự cố, `caerus-app-private-1b` vẫn giữ được đường ra
   độc lập của riêng nó qua `caerus-nat-1b`, thay vì mất luôn cả egress theo
   AZ kia. Đánh đổi là chi phí NAT theo giờ tăng gần gấp đôi so với dùng
   chung một gateway - chấp nhận được ở đây vì phía RDS của kiến trúc này đã
   trả một khoản phí tương đương cho Multi-AZ, và một tầng compute mất truy
   cập đi ra ngay khi một AZ trục trặc sẽ làm suy yếu chính cam kết Multi-AZ
   đó.
   {{% /notice %}}

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

6. **Đóng gói backend ở local** và đưa qua deploy bucket thay vì `git clone`
   trực tiếp lên instance, để instance không bao giờ cần đến GitHub
   credentials của riêng nó:

   ```powershell
   robocopy backend "$env:TEMP\deploy" /E /XD node_modules .git /XF .env
   Compress-Archive -Path "$env:TEMP\deploy\*" -DestinationPath "$env:TEMP\backend.zip" -Force
   ```

   Upload `backend.zip` lên `caerus-backend`.

7. **Kết nối qua Session Manager**, không phải EC2 Instance Connect - instance
   không có IP công khai để Instance Connect có thể chạm tới ngay từ đầu. EC2
   Console → chọn `caerus-server-1` → **Connect → Session Manager →
   Connect**, hoặc `aws ssm start-session --target <instance-id>` từ một
   terminal đã cấu hình AWS CLI. Phiên làm việc mở ra với user `ssm-user`;
   chuyển sang `ec2-user` trước để quyền sở hữu file và process `pm2` khởi
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

8. **Tạo `.env`** - file duy nhất không bao giờ nằm trong file zip và không
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

9. **Chạy dưới `pm2`** để process sống sót qua việc mất kết nối và khởi động
   lại:

   ```bash
   sudo npm install -g pm2
   pm2 start src/server.js --name caerus-api
   pm2 startup   # run the sudo command it prints
   pm2 save
   ```

10. **Kiểm tra từ bên trong session trước**:

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
