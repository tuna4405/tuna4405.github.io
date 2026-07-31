---
title : "Quản lý chi phí và tài nguyên"
date : 2026-06-01
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

#### Tổng quan

Kiến trúc cuối cùng không nằm gọn trong Free Tier mười hai tháng, và nó cũng không cố
làm điều đó. Mọi phần mở rộng được xây sau lần triển khai chạy được đầu tiên - RDS
Multi-AZ (mục 5.5.4), Application Load Balancer (mục 5.7.5), CloudFront và WAF (mục
5.7.6), và NAT gateway cho EC2 trong private subnet (mục 5.7.7) - đều đánh đổi khoảng
trống Free Tier để lấy một tính chất mà một bản triển khai thật sự sẽ muốn có: cơ sở
dữ liệu tự động failover, không còn điểm lỗi đơn ở tầng compute, một tên miền HTTPS
duy nhất kèm lớp bảo vệ tại biên, và một tầng compute hoàn toàn không mở cổng inbound
nào ra internet. Con số trung thực là một hóa đơn hằng tháng có thật, không phải một
sai số làm tròn, và nó đáng được nói thẳng ra thay vì để báo cáo ngụ ý rằng hệ thống
vẫn còn miễn phí.

**Chi phí định kỳ thật sự, theo từng thành phần:**

| Thành phần | Vì sao nó tốn tiền, bất kể lưu lượng | Chi phí ước chừng |
|---|---|---|
| NAT Gateway | Tính theo giờ, cộng thêm phí xử lý dữ liệu theo mỗi GB | ~US$32-35/tháng ở mức nền |
| Application Load Balancer | Tính theo giờ, cộng thêm LCU-hour khi có tải | ~US$16/tháng ở mức nền |
| RDS Multi-AZ | Instance standby bị tính phí y hệt instance chính - Free Tier chưa bao giờ bao gồm gì ngoài Single-AZ | khoảng gấp đôi một instance Single-AZ cùng class |
| Amazon EC2 (×2) | Tổng số giờ của hai instance vượt hạn mức 750 giờ của Free Tier khi cả hai chạy trọn một tháng | vừa phải, tùy theo instance class |
| Amazon CloudFront + WAF | Theo mức sử dụng: mỗi GB truyền đi, mỗi 10.000 request HTTPS, mỗi rule WAF được đánh giá | vài đô la ở mức traffic trình diễn của dự án này |
| Amazon S3 (4 bucket) | Thấp hơn nhiều so với hạn mức 5 GB của Free Tier ở khối lượng dữ liệu này | không đáng kể |
| Truyền dữ liệu | Không đáng kể ở mức traffic trình diễn | không đáng kể |

**Tổng ước tính: khoảng US$90-110/tháng.** Riêng NAT gateway và load balancer đã
chiếm hơn một nửa con số đó, và cả hai đều tính cùng một mức phí theo giờ dù ứng dụng
đang phục vụ traffic thật hay nằm không hoàn toàn - đây là cái giá trực tiếp của
những quyết định về private subnet và cân bằng tải ở mục 5.7.5 và 5.7.7, chứ không
phải một bất ngờ để phát hiện ra sau này.

**Sai lầm đắt đỏ nhất đã tránh được, chứ không mắc phải:** template Production trong
wizard RDS Multi-AZ của console mặc định đặt storage là **Provisioned IOPS SSD
(io2)** ở mức 100 GiB thay vì General Purpose SSD (gp3) được dùng xuyên suốt phần còn
lại của dự án này - io2 tính phí riêng cho dung lượng và cho từng IOPS được cấp phát,
và nếu để nguyên mặc định đó thì nó sẽ là dòng lớn nhất trên toàn bộ hóa đơn với
khoảng cách rất xa, lớn hơn cả NAT gateway và load balancer cộng lại. Điều này đã
được phát hiện và sửa trước khi tạo instance ở mục 5.5.4; nó đáng được nêu rõ ở đây
bởi đây đúng là kiểu cấu hình mặc định của console mà người ta rất dễ bấm lướt qua mà
không đọc.

**Cách quản trị đã giữ cho chuyện này luôn nhìn thấy được thay vì gây bất ngờ:** mọi
tài nguyên được tạo ra đều mang tag `Owner` từ mục 5.2, nhờ đó Cost Explorer nhóm
theo tag ấy quy được bất kỳ đợt tăng vọt nào về một lập trình viên cụ thể chỉ trong
vài giây. Ngưỡng của billing alarm được nâng từ một giá trị canh chừng tượng trưng
lên khoảng **US$150** - cao hơn mức chi tiêu dự kiến, nên nó vẫn báo động khi có sai
sót thật (một NAT gateway thứ hai, một RDS class quá khổ, một EBS snapshot của AMI bị
bỏ quên) mà không đồng thời báo động vì hoạt động bình thường đã lường trước, như
cách một alarm US$10 sẽ làm với mức chi phí này.

<!-- ![Cost Explorer filtered by the Owner tag, showing the ALB and RDS Multi-AZ lines](/images/5-Workshop/5.10-Cost/example.png) -->
