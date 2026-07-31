---
title : "Siết chặt Security Group"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.7.3 </b> "
---

Hai security group, được tạo ra trước cả khi những tài nguyên chúng bảo vệ tồn
tại (mục 5.5.1 và 5.7.2 đều đã tham chiếu trước tên `caerus-rds-sg` và
`caerus-ec2-sg`):

**`caerus-ec2-sg`** - instance của application, được tạo ra **không có bất kỳ
rule inbound nào**:

| Type | Port | Source |
|---|---|---|
| *(không có)* | - | - |

Chưa có gì cần cho phép, và không có gì cần được cho phép cả. Việc quản trị đi
qua Systems Manager, thứ chạm tới instance qua một kết nối *đi ra* xuyên qua
các NAT gateway từ mục 5.7.2 - không rule inbound, không key pair, liên quan
tại bất kỳ thời điểm nào. Việc kiểm tra trong lúc phát triển đi qua một đường
hầm SSM port-forwarding (cũng được khởi tạo từ phía đi ra). Lưu lượng inbound
duy nhất mà instance ứng dụng này từng cần là lưu lượng ứng dụng từ load
balancer, và load balancer đó chưa tồn tại cho tới mục 5.7.5 - nên group này
mang đúng số 0 rule cho tới khi mục đó thêm vào đúng một rule: Custom TCP,
port 3000, source là **security group `caerus-alb-sg`**.

**`caerus-rds-sg`** - được tạo ra trống, sau đó chỉ được cấp đúng một rule khi
`caerus-ec2-sg` đã tồn tại để tham chiếu tới:

| Type | Port | Source (trước) | Source (sau) |
|---|---|---|---|
| PostgreSQL | 5432 | *(chưa có rule - không thể truy cập)* | `caerus-ec2-sg` |

**Source** của rule này chính là security group của EC2, được chọn theo tên
trong console, không phải một CIDR block hay một địa chỉ IP - và điều này
đúng bất kể `caerus-ec2-sg` có mang rule inbound nào của riêng nó hay không,
vì một rule tham chiếu theo security group khớp theo tư cách thành viên của
group đó, không phải theo việc group đó cho phép những gì. Đây là điểm đáng
nói rõ ràng: RDS chấp nhận lưu lượng từ *bất kỳ instance nào mang security
group đó*, bất kể instance đó hiện đang có địa chỉ IP nào, hay thậm chí có IP
hay không. Một rule dựa trên IP sẽ cần cập nhật mỗi khi một instance được
thay thế, hoặc phải nới rộng ra một dải đủ lớn tới mức trở nên vô nghĩa; một
rule tham chiếu theo security group thì không bao giờ cần cập nhật, và luôn
giữ đúng mức chặt chẽ "chỉ ứng dụng này mới truy cập được database này" trong
suốt thời gian cả hai group còn tồn tại.

{{% notice note %}}
Không có kiểu "cửa thoát hiểm" nào như "nếu mất quyền truy cập SSH, sửa rule
về lại My IP" trong dự án này, vì chưa từng có rule nào gắn quyền truy cập
với một địa chỉ IP ngay từ đầu. Việc mất IP ở nhà, đổi mạng, hay bàn giao dự
án cho một thành viên mới không thay đổi gì cách các instance này được truy
cập tới - Systems Manager xác thực qua IAM, không phải qua việc kết nối xuất
phát từ đâu.
{{% /notice %}}

<!-- ![caerus-ec2-sg with zero inbound rules, and caerus-rds-sg admitting 5432 from caerus-ec2-sg](/images/5-Workshop/5.7-EC2/5.7.3-security-groups/example.png) -->
