---
title : "Private Subnets, NAT, và Systems Manager"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7.7 </b> "
---

Load balancer đã thu hẹp `caerus-ec2-sg` để chỉ nhận traffic từ đúng
`caerus-alb-sg` (mục 5.7.5), nghĩa là các instance không còn cần public IP để phục
vụ traffic nữa - load balancer là thứ duy nhất từng gọi thẳng tới chúng. Mục này gỡ
bỏ hẳn public IP và cổng SSH đang mở, mang lại cho tầng compute đúng tính chất
"không có đường ra internet" mà cơ sở dữ liệu đã có từ mục 5.5.4.

#### Vì sao phải là một cặp subnet riêng chứ không dùng của cơ sở dữ liệu

Các private subnet của RDS (mục 5.5.4) được dựng với một route table **không** có
route `0.0.0.0/0` nào cả - đúng đắn với RDS vốn không bao giờ khởi tạo traffic
outbound, nhưng sai với EC2 vốn cần truy cập ra ngoài để chạy `npm ci` và vá lỗi hệ
điều hành. Dùng lại subnet của cơ sở dữ liệu đồng nghĩa với việc thêm một route ra
internet vào một route table đã cố ý được dựng lên mà không có nó, làm vẩn đục một
quyết định thiết kế vốn đúng như đã viết. Một cặp private subnet thứ hai, tách biệt -
mỗi Availability Zone một cái, khớp với hai instance - giữ cho chính sách mạng của
hai tầng độc lập với nhau, dù rốt cuộc cả hai đều "private" theo nghĩa thông thường
của từ này.

1. **Tạo hai private subnet cho tầng ứng dụng**, `caerus-private-app-a` (cùng AZ với
   `caerus-api-1`) và `caerus-private-app-b` (cùng AZ với `caerus-api-2`), với các
   khối CIDR không chồng lấn bất kỳ subnet nào sẵn có, kể cả subnet của RDS.

2. **Tạo một NAT Gateway**, `caerus-nat`, đặt trong một trong các **public** subnet
   (nơi ALB đã nằm sẵn) - một NAT gateway bắt buộc phải nằm trong một subnet có
   route tới Internet Gateway, không bao giờ nằm trong subnet private. Cấp phát một
   Elastic IP mới cho nó ngay lúc tạo.

3. **Tạo một route table cho các private subnet của tầng ứng dụng**, với một route
   `0.0.0.0/0` trỏ tới `caerus-nat` - đây là điều ngược lại với route table của RDS,
   và cũng là toàn bộ lý do hai tầng dùng subnet riêng. Gắn cả hai subnet mới vào
   route table này.

{{% notice note %}}
Việc dùng một NAT gateway duy nhất chứ không phải mỗi Availability Zone một cái là
một đánh đổi chi phí có chủ đích ở đây: hai NAT gateway sẽ nhân đôi chi phí theo giờ
để đổi lấy dự phòng chỉ bảo vệ traffic *outbound* (cài package, vá lỗi) - nó không
ảnh hưởng gì tới khả năng sẵn sàng của các request từ phía client, vốn vẫn đi trọn
vẹn qua load balancer bất kể NAT gateway của AZ nào đang sống. Nếu AZ chứa NAT
gateway gặp sự cố, instance ở AZ còn lại tạm thời mất truy cập internet chiều
outbound nhưng vẫn phục vụ traffic bình thường.
{{% /notice %}}

4. **Cấp quyền Systems Manager** trên instance role của EC2
   (`caerus-ec2-s3-role`): gắn policy do AWS quản lý
   `AmazonSSMManagedInstanceCore`. Đây là thứ cho phép SSM Agent vốn đã chạy sẵn
   trên Amazon Linux tự đăng ký và nhận lệnh - hoàn toàn qua một kết nối outbound mà
   NAT gateway giờ đã cung cấp, không cần bất kỳ rule inbound nào.

5. **Tái sử dụng nguyên trạng thiết lập của các instance đang chạy thay vì triển
   khai lại từ đầu**: EC2 Console → chọn `caerus-api-1` → **Actions → Image and
   templates → Create image**. Việc này chụp lại đĩa của instance - Node.js, code đã
   triển khai, danh sách tiến trình của `pm2` - thành một AMI có thể khởi chạy thẳng
   vào các private subnet mới.

6. **Khởi chạy một instance thay thế từ AMI đó** vào `caerus-private-app-a`, cùng
   loại instance, cùng `caerus-ec2-sg`, cùng IAM instance profile (giờ đã mang theo
   quyền SSM từ bước 4). Lặp lại để có một instance thay thế thứ hai trong
   `caerus-private-app-b`, từ một AMI của `caerus-api-2`.

7. **Kiểm chứng quyền truy cập trước khi động vào các instance cũ**: EC2 Console →
   chọn một instance mới → **Connect → Session Manager → Connect**, hoặc
   `aws ssm start-session --target <instance-id>` từ một terminal đã cấu hình AWS
   CLI. Xác nhận `pm2 list` cho thấy API đang chạy, và
   `curl localhost:3000/api/v1/health` trả lời được từ bên trong phiên làm việc đó.

8. **Đăng ký cả hai instance mới vào `caerus-tg`** và chờ cả hai báo **Healthy**
   trước khi gỡ đăng ký hai cái cũ - đúng mẫu không-gián-đoạn đã dùng khi instance
   thứ hai lần đầu được thêm vào ở mục 5.7.5.

9. **Gỡ bỏ hoàn toàn rule SSH khỏi `caerus-ec2-sg`** (không có rule thay thế -
   Session Manager không cần bất kỳ cổng inbound nào), rồi terminate hai instance
   gốc trong public subnet khi các bản thay thế đã gánh traffic thật mà không xảy ra
   sự cố gì.

{{% notice warning %}}
Hãy làm bước này sau cùng, một cách có chủ đích. Việc gỡ bỏ quyền truy cập SSH là
không thể đảo ngược với các instance *cũ* ngay khi bạn terminate chúng - nếu có gì
đó trục trặc với quyền Session Manager trên các instance mới, bạn sẽ muốn những cái
cũ vẫn còn tiếp cận được như một phương án dự phòng, cho tới khi bạn thực sự xác nhận
các bản thay thế chạy được chứ không chỉ mới khởi chạy xong.
{{% /notice %}}

10. **Gỡ đăng ký rồi xóa các AMI** khi cả hai instance thay thế đã chạy ổn định một
    thời gian - một AMI cùng EBS snapshot đứng sau nó đều tiếp tục tốn phí lưu trữ
    dù instance mà nó được nhân bản từ đó không còn tồn tại, nên đây là một khoản chi
    có thật và rất dễ quên (xem [Quản lý chi phí và tài
    nguyên](/5-Workshop/5.10-Cost/)).

Sau mục này, `<instance-public-ip>:3000` không còn phân giải tới đâu cả - đúng như
thiết kế. Hai con đường duy nhất đi vào tầng compute là load balancer (cho traffic
ứng dụng) và Session Manager (cho việc quản trị), và không đường nào phụ thuộc vào
việc instance có public IP hay không.

<!-- ![Session Manager connected to a private-subnet instance, alongside the target group showing both replacement instances Healthy](/images/5-Workshop/5.7-EC2/5.7.7-private-subnet-nat/example.png) -->
