---
title : "Multi-AZ và Private Subnet"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.5.4 </b> "
---

Khi ứng dụng đã chạy trọn vẹn trên một instance Single-AZ nằm trong subnet mặc định
(public), mục này bổ sung hai cải tiến mang dáng dấp production vốn không nằm trong
kế hoạch chín tuần ban đầu: tự động failover, và đưa cơ sở dữ liệu ra khỏi mọi
đường mạng dẫn tới internet.

#### Vì sao cơ sở dữ liệu lại nằm trong public subnet ngay từ đầu

"Public access: No" (mục 5.5.1) quyết định việc RDS có gán một public IP và có nhận
kết nối từ bên ngoài VPC hay không - nó hoàn toàn không nói gì về chuyện instance
nằm trong *subnet* nào. Các subnet duy nhất của VPC mặc định đều có route tới một
Internet Gateway, nên một instance Single-AZ được tạo trên DB subnet group mặc định,
nói cho chặt chẽ, vẫn nằm trong một public subnet suốt thời gian đó, chỉ được bảo vệ
bởi security group. Dời nó vào một private subnet thật sự - loại có route table
hoàn toàn không có đường ra internet - là một lớp phòng thủ thứ hai, độc lập, vẫn
đứng vững ngay cả khi một security group nào đó bị cấu hình sai.

1. **Tạo hai private subnet, mỗi Availability Zone một cái** (RDS Multi-AZ yêu cầu
   một DB subnet group trải trên ít nhất hai AZ): `caerus-private-1a` ở
   `ap-southeast-1a`, `caerus-private-1b` ở `ap-southeast-1b`, mỗi cái mang một
   khối CIDR không chồng lấn với các subnet sẵn có của VPC.

2. **Tạo một route table không có route `0.0.0.0/0`** rồi gắn cả hai subnet mới vào
   nó - chính *sự vắng mặt* của route tới internet gateway mới làm cho một subnet
   trở thành private, chứ không phải cái tên đặt cho nó. Không cần NAT gateway: RDS
   không bao giờ tự khởi tạo traffic outbound ra internet.

3. **Tạo một DB subnet group mới** (`caerus-private-subnet-group`) dùng hai private
   subnet vừa tạo.

4. **Bật Multi-AZ.** RDS Console → chọn instance → **Modify → Availability &
   durability → Multi-AZ deployment**, chọn **Create a standby instance**. Việc này
   đòi hỏi phải chuyển template của instance từ Free Tier sang **Production** trước
   - Free Tier vô hiệu hóa hẳn lựa chọn này, bởi một bản standby không nằm trong
   hạn mức Free Tier.

#### Chỗ này thực sự đã trở nên rắc rối

Dời DB subnet group của một instance Multi-AZ đang tồn tại không phải là một thao
tác một bước gọn gàng. Hai lần thử riêng biệt - qua AWS CLI và qua chính wizard
Modify của Console - đều thất bại với các biến thể của cùng một lỗi validation gây
hiểu nhầm, đại ý rằng một instance không thể được dời sang một subnet group "trong
một VPC khác", kể cả khi subnet group đích nằm trong *cùng* một VPC. Đây là một
điểm gợn đã được biết đến trong cách RDS kiểm tra việc đổi subnet group trên một
instance vốn ngay từ đầu không dùng subnet group tùy chỉnh (khác mặc định).

{{% notice warning %}}
Có hai cách vòng cho đúng lỗi này: tạm thời tắt Multi-AZ, đổi subnet group, rồi bật
lại; hoặc đi vòng qua một subnet group tùy chỉnh trung gian dựng từ chính các public
subnet *hiện có* trước khi chuyển sang subnet group private. Cả hai đều hợp lệ.
Trong dự án này, do cơ sở dữ liệu lúc đó không chứa gì ngoài dữ liệu seed, phương án
nhanh hơn và chắc chắn hơn đã được chọn: **xóa instance và tạo một cái mới**, chọn
thẳng private subnet group và Multi-AZ ngay tại thời điểm khởi tạo, nơi toàn bộ
logic validation kia không được áp dụng. Nếu cơ sở dữ liệu có thể bỏ đi được, hãy ưu
tiên cách này thay vì ngồi debug đường di chuyển.
{{% /notice %}}

5. **Nếu tạo lại**: xóa instance cũ (bỏ qua snapshot cuối cùng vì đây chỉ là dữ
   liệu seed dùng xong bỏ), rồi lặp lại mục 5.5.1 với **Templates: Production**,
   **Multi-AZ: Create a standby instance**, và **DB subnet group:
   `caerus-private-subnet-group`** đều được chọn ngay lúc tạo, sau đó lặp lại mục
   5.5.2 để nạp lại schema và dữ liệu seed.

6. **Một cái bẫy thứ hai, không liên quan, trong chính wizard đó**: template
   Production mặc định đặt storage là **Provisioned IOPS SSD (io2)** ở mức 100 GiB,
   vốn không thuộc Free Tier và tính phí riêng cho cả dung lượng lẫn số IOPS được
   cấp phát - vào cỡ hàng trăm đô la Mỹ mỗi tháng ở mức IOPS đó. Hãy đổi **Storage
   type** sang **General Purpose SSD (gp3)** và **Allocated storage** về **20 GiB**
   trước khi tạo.

7. **Kiểm chứng**: hostname của endpoint không đổi (RDS giữ nguyên DNS name qua
   kiểu thay đổi này), nên không có gì trong `DATABASE_URL` của ứng dụng cần sửa.
   Hãy xác nhận từ một instance EC2 trong cùng VPC (mục 5.7) rằng cơ sở dữ liệu vẫn
   truy cập được - một private subnet chặn *internet*, chứ không chặn traffic từ
   những nơi khác bên trong cùng VPC, và đó đúng là mẫu truy cập mà ứng dụng cần.

<!-- ![RDS instance details showing Multi-AZ enabled and the private DB subnet group](/images/5-Workshop/5.5-RDS/5.5.4-multi-az-and-private-subnet/example.png) -->
