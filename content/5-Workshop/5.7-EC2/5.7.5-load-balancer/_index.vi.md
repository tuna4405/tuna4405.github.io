---
title : "Load Balancer và Instance Thứ Hai"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.7.5 </b> "
---

Một EC2 instance duy nhất là một điểm lỗi đơn (single point of failure) - ổn
cho lần triển khai hoạt động đầu tiên, nhưng đáng để cải thiện một khi nó đã
ổn định. Phần này thêm một instance thứ hai ở một Availability Zone khác và
một Application Load Balancer đặt trước cả hai. Đây cũng là điểm đầu tiên
trong toàn bộ dự án mà tầng compute có được một đường vào thực sự từ internet
công khai - mọi thứ cho tới lúc này chỉ có thể truy cập được từ bên trong một
phiên Session Manager hoặc qua đường hầm SSM ở mục 5.7.4.

1. **Khởi tạo một instance thứ hai**, `caerus-server-2`, giống hệt
   `caerus-server-1` về AMI, instance type, `caerus-ec2-sg`, và IAM instance
   profile - khác biệt duy nhất là subnet, `caerus-app-private-1b` (mục
   5.7.2), ở Availability Zone còn lại, để load balancer thực sự thu được lợi
   ích từ việc có hai target và mỗi AZ giữ được NAT gateway độc lập của riêng
   mình. Triển khai cùng gói backend lên đó (mục 5.7.2) và xác nhận nó phản
   hồi `/api/v1/health` từ bên trong phiên Session Manager của riêng nó trước
   khi động tới bất cứ thứ gì dùng chung.

2. **Tạo một security group cho load balancer**, `caerus-alb-sg`, cho phép
   HTTP trên port 80 từ bất kỳ đâu - đây là điểm vào công khai thực sự của hệ
   thống, và là điểm duy nhất từng tồn tại.

3. **Tạo một target group**, `caerus-tg`, protocol HTTP, port 3000, health
   check path `/api/v1/health`, và đăng ký cả hai instance.

4. **Tạo Application Load Balancer**, `caerus-alb`, Internet-facing, trải
   trên các subnet **public** ở cả hai Availability Zone (bản thân load
   balancer nằm trong các subnet public cùng với các NAT gateway - đây là
   thành phần duy nhất trong chuỗi này vốn được thiết kế để truy cập trực
   tiếp được từ internet), security group `caerus-alb-sg`, với một listener
   HTTP:80 forward tới `caerus-tg`.

5. **Thêm đúng rule mà `caerus-ec2-sg` đã chờ sẵn** (mục 5.7.3 cố tình để nó
   trống): Custom TCP, port 3000, source là **security group
   `caerus-alb-sg`**. Đây không phải siết chặt một rule có sẵn - đây là rule
   inbound đầu tiên và duy nhất mà security group này từng mang, và trước
   thời điểm này không có gì có thể chạm tới port 3000 trên bất kỳ instance
   nào mà không đã ở sẵn bên trong một phiên Session Manager.

6. **Kiểm tra qua load balancer**:

   ```bash
   curl http://<alb-dns-name>/api/v1/health
   ```

   Gọi lặp lại nhiều lần, lưu lượng sẽ luân phiên giữa hai instance mà không
   có khác biệt nào đáng chú ý trong response - đó chính xác là tính chất
   đang được kiểm chứng. Đây cũng là lệnh `curl` đầu tiên trong toàn bộ dự án
   chạm tới được tầng compute từ một terminal hoàn toàn bình thường, không
   cần AWS CLI, không cần đường hầm SSM, không cần một session đang hoạt
   động.

7. **Trỏ frontend tới DNS name của load balancer** thay vì đường hầm SSM cục
   bộ ở mục 5.7.4 (`VITE_API_BASE_URL=http://<alb-dns-name>/api/v1`), build
   lại, upload lên `caerus-frontend-web`, và invalidate CloudFront cache (mục
   5.6.2) để thay đổi thực sự được phục vụ. Danh sách `allowedOrigins` trong
   `app.js` không cần thay đổi gì - domain của distribution đã được thêm vào
   đó từ mục 5.7.4, trước cả khi load balancer tồn tại để phục vụ nó. Tải lại
   site tại domain CloudFront: vấn đề CORS đã được chẩn đoán và sửa ở local
   trong mục 5.7.4 không quay lại, vì nó chưa bao giờ thực sự đặc thù cho
   đường hầm đó - nó là cùng một phép kiểm tra origin, chỉ được thực hiện sớm
   hơn trên hạ tầng rẻ hơn.

<!-- ![Target group showing both instances Healthy, and the CloudFront-hosted frontend successfully calling the API through the load balancer](/images/5-Workshop/5.7-EC2/5.7.5-load-balancer/example.png) -->
