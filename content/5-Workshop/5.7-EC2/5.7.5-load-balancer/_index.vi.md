---
title : "Load Balancer và Instance Thứ Hai"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.7.5 </b> "
---

Một instance EC2 duy nhất là một điểm lỗi đơn - chấp nhận được cho lần triển khai
chạy được đầu tiên, và đáng để cải thiện khi mọi thứ đã ổn định. Mục này bổ sung một
instance thứ hai ở một Availability Zone khác cùng một Application Load Balancer
đứng trước cả hai, mà không hề phải đưa instance đầu tiên offline trong lúc làm.

1. **Khởi chạy instance thứ hai**, `caerus-api-2`, giống hệt cái đầu tiên về AMI,
   loại instance, `caerus-ec2-sg`, và IAM instance profile - khác biệt duy nhất là
   subnet, được chọn ở một Availability Zone khác với subnet của instance đầu tiên,
   để load balancer thực sự thu được lợi ích từ việc có hai target. Hãy triển khai
   đúng gói backend đó lên nó (mục 5.7.2) và xác nhận nó trả lời `/api/v1/health`
   trên public IP của chính nó trước khi động vào bất cứ thứ gì dùng chung.

2. **Tạo một security group cho load balancer**, `caerus-alb-sg`, cho phép HTTP
   cổng 80 từ mọi nơi - đây, chứ không phải security group của EC2, giờ mới là điểm
   vào công khai thật sự của hệ thống.

3. **Tạo một target group**, `caerus-tg`, giao thức HTTP, cổng 3000, đường dẫn
   health check là `/api/v1/health`, và đăng ký cả hai instance vào đó.

4. **Tạo Application Load Balancer**, `caerus-alb`, loại Internet-facing, trải trên
   public subnet của cả hai instance, security group là `caerus-alb-sg`, với một
   listener HTTP:80 chuyển tiếp tới `caerus-tg`. Bản thân load balancer sẽ nằm lại
   trong các public subnet này vĩnh viễn - thứ chuyển sang private subnet ở mục
   5.7.7 về sau là các instance, không phải load balancer.

5. **Kiểm thử qua load balancer trước khi thay đổi bất cứ thứ gì khác** - ở thời
   điểm này cả hai instance vẫn còn truy cập trực tiếp được, nên một cấu hình sai ở
   load balancer không thể làm sập ứng dụng trong lúc đang được debug:

   ```bash
   curl http://<alb-dns-name>/api/v1/health
   ```

   Gọi lặp đi lặp lại, traffic luân phiên qua hai instance mà response không có khác
   biệt nào nhìn thấy được - và đó đúng là tính chất đang cần kiểm chứng.

6. **Chỉ khi bước 5 đã được xác nhận là chạy tốt**, mới siết `caerus-ec2-sg`: gỡ bỏ
   rule cho phép cổng 3000 từ "My IP" và thay bằng cổng 3000 từ **security group
   `caerus-alb-sg`**. Từ thời điểm này trở đi, gọi thẳng `http://<instance-ip>:3000`
   sẽ không còn hoạt động - đúng như thiết kế, bởi load balancer giờ là con đường
   duy nhất được thừa nhận để đi vào.

{{% notice note %}}
Bước 5 tồn tại chính là để nếu load balancer bị cấu hình sai thì hệ thống chỉ suy
giảm xuống mức "vẫn truy cập được theo cách cũ" chứ không phải "sập hoàn toàn" trong
lúc chờ sửa. Khóa security group trước khi xác nhận load balancer hoạt động là gỡ bỏ
tấm lưới an toàn ấy mà chẳng đổi lại được lợi ích gì.
{{% /notice %}}

7. **Trỏ frontend sang DNS name của load balancer** thay vì IP của một instance nào
   đó (`VITE_API_BASE_URL=http://<alb-dns-name>/api/v1`), build lại, và triển khai
   lại trang web. Frontend giờ sống sót được khi một trong hai instance bị dừng,
   khởi động lại, hay bị thay thế, bởi ngay từ đầu nó đã không phụ thuộc vào địa chỉ
   IP của một instance cụ thể nào.

<!-- ![Target group showing both instances Healthy](/images/5-Workshop/5.7-EC2/5.7.5-load-balancer/example.png) -->
