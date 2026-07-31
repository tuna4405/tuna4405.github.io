---
title : "Khởi tạo DB Instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

Database được triển khai Multi-AZ, trong một private subnet không có route ra
internet, ngay từ lần khởi tạo đầu tiên này - chứ không phải như một bước nâng
cấp sau đó. Một instance Single-AZ nằm trong subnet mặc định mà console gợi ý
vẫn đủ dùng cho một bản demo, nhưng nó mang theo một single point of failure
và nằm trên một đường mạng vốn không có lý do gì để tồn tại, vì vậy không có
lý do gì để dựng phiên bản đó trước rồi mới thay thế nó.

#### Vì sao cần một private subnet

"Public access: No" (dùng ở bước dưới) chỉ kiểm soát việc RDS có gán public
IP và chấp nhận kết nối từ bên ngoài VPC hay không - nó không nói gì về
*subnet* mà instance nằm trong đó. Một instance được tạo trong các subnet mặc
định của VPC, xét cho cùng, vẫn nằm trong một public subnet suốt thời gian
tồn tại, chỉ được bảo vệ bởi một security group. Một private subnet đúng
nghĩa - một subnet mà route table của nó hoàn toàn không có route ra internet
- là một lớp phòng thủ thứ hai, độc lập, vẫn đứng vững ngay cả khi security
group lỡ bị cấu hình sai.

1. **Tạo hai private subnet, mỗi AZ một subnet** (RDS Multi-AZ yêu cầu DB
   subnet group trải trên ít nhất hai AZ): `caerus-private-1a` ở
   `ap-southeast-1a`, `caerus-private-1b` ở `ap-southeast-1b`, mỗi subnet có
   một CIDR block không trùng với các subnet khác của VPC.

![](/images/5-Workshop/5.5-RDS/2rds_private_subnet.png)

2. **Tạo một route table không có route `0.0.0.0/0`** và gắn cả hai subnet mới
   vào đó - chính việc *không có* route tới internet gateway mới khiến một
   subnet trở nên private, không phải cái tên được đặt cho nó. Không cần NAT
   gateway ở đây: RDS không bao giờ tự khởi tạo traffic outbound ra internet.

![](/images/5-Workshop/5.5-RDS/caerus-private-rt.png)

3. **Tạo một DB subnet group**, `caerus-private-subnet-group`, từ hai private
   subnet đó.

4. **RDS Console → Create database → Full configuration** (tùy chọn còn lại,
   "Easy create", sẽ ẩn đi các control về Multi-AZ và private subnet mà mục
   này cần tới, thay bằng các giá trị mặc định "recommended" được chọn sẵn),
   sau đó chọn engine. Có hai nút lớn nằm cạnh nhau ở đây - **Amazon Aurora**
   và **PostgreSQL** - và thực sự rất dễ bấm nhầm, kể cả tùy chọn có nhãn
   "Aurora PostgreSQL Compatible", vốn vẫn là Aurora. Chọn **PostgreSQL**
   thuần túy, phiên bản 16.x, để khớp với image `postgres:16` dùng ở local.

5. **Templates: Production.** Multi-AZ hoàn toàn không có sẵn ở template Free
   Tier - Production mới là thứ mở khóa nó, và bắt buộc phải dùng ở đây.

6. **Availability and durability → Deployment options → Multi-AZ DB instance
   deployment (2 instances)** - một primary cộng một standby không đọc được
   ở AZ thứ hai, khớp với hai private subnet đã tạo ở bước 1.

![](/images/5-Workshop/5.5-RDS/rds_config.png)

7. **DB instance identifier**: `caerus-db`. **Credentials management: Self
   managed** - không dùng AWS Secrets Manager, để tránh dùng một dịch vụ mà
   dự án không có mục đích sử dụng nào khác. Ghi lại master username và
   password ngay lập tức; password không thể khôi phục lại từ Console sau đó.

8. **Connectivity**: cùng VPC với application, **DB subnet group:
   `caerus-private-subnet-group`** (đã tạo ở bước 3), **Public access: No**,
   và một security group riêng (`caerus-rds-sg`, tạo ra chưa có rule nào -
   rule inbound cho phép security group của application sẽ được thêm vào khi
   security group đó tồn tại, ở mục 5.7.3)

![](/images/5-Workshop/5.5-RDS/rds_connectivity.png)