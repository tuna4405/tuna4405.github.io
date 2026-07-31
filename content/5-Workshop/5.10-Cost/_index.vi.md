---
title : "Quản lý chi phí và tài nguyên"
date : 2026-06-01
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

#### Tổng quan

Kiến trúc cuối cùng không nằm gọn trong Free Tier mười hai tháng, và nó cũng
không cố gắng làm vậy. RDS chạy Multi-AZ trong một private subnet ngay từ
lần khởi tạo đầu tiên (mục 5.5.1), tầng compute có private subnet và một NAT
gateway riêng cho mỗi Availability Zone của chính nó trước cả khi instance
đầu tiên tồn tại (mục 5.7.2), và CloudFront phục vụ bucket frontend ngay từ
bản build đầu tiên được deploy (mục 5.6.2) - không cái nào trong số đó là
một bước nâng cấp gắn thêm vào sau một điểm khởi đầu rẻ hơn. Thứ thực sự
được thêm vào một khi bản triển khai hoạt động đầu tiên đã ổn định là tính
dự phòng (redundancy) ở tầng compute - một EC2 instance thứ hai và một
Application Load Balancer (mục 5.7.5) - cộng thêm một CloudFront behavior và
WAF thứ hai định tuyến API qua cùng distribution đó (mục 5.7.6). Tất cả cộng
lại, mọi thứ đều đánh đổi dư địa Free Tier để lấy một đặc tính mà một hệ
thống triển khai thực tế thực sự cần: failover
database tự động, không có điểm lỗi đơn (single point of failure) ở tầng
compute, một domain HTTPS duy nhất với bảo vệ ở edge, và một tầng compute
không mở bất kỳ cổng inbound nào ra internet. Con số trung thực là một hóa
đơn hàng tháng thực sự, không phải một sai số làm tròn, và điều này đáng
được nêu rõ ràng thay vì để báo cáo ngầm ý rằng hệ thống vẫn miễn phí.

**Chi phí định kỳ thực tế, theo từng thành phần:**

| Thành phần | Vì sao phát sinh chi phí, bất kể traffic | Chi phí ước tính |
|---|---|---|
| NAT Gateway (×2, mỗi AZ một cái) | Tính phí theo giờ cho mỗi gateway, cộng thêm phí xử lý dữ liệu theo GB | ~US$65-70/tháng (mức cơ bản) |
| Application Load Balancer | Tính phí theo giờ, cộng thêm LCU-hours khi có tải | ~US$16/tháng (mức cơ bản) |
| RDS Multi-AZ | Instance standby được tính phí giống hệt instance primary - Free Tier chỉ từng bao gồm Single-AZ | gấp khoảng đôi so với một instance Single-AZ cùng class |
| Amazon EC2 (×2) | Tổng số giờ của hai instance vượt quá hạn mức 750 giờ của Free Tier ngay khi cả hai chạy đủ một tháng | ở mức vừa phải, tùy thuộc vào instance class |
| Amazon CloudFront + WAF | Tính theo mức sử dụng: theo GB truyền tải, theo mỗi 10.000 HTTPS request, theo mỗi WAF rule được đánh giá | vài đô la ở mức traffic demo của dự án này |
| Amazon S3 (4 buckets) | Thấp hơn nhiều so với hạn mức 5 GB của Free Tier ở khối lượng dữ liệu này | không đáng kể |
| Data transfer | Không đáng kể ở mức traffic demo | không đáng kể |

**Tổng ước tính: khoảng US$120-145/tháng.** Chỉ riêng hai NAT gateway và load
balancer đã chiếm hơn một nửa số đó, và cả ba đều tính phí theo cùng mức giá
theo giờ bất kể ứng dụng đang phục vụ traffic thực hay hoàn toàn nhàn rỗi -
đây là chi phí trực tiếp của quyết định về hạ tầng mạng private ở mục 5.7.2
và quyết định về load balancing ở mục 5.7.5, không phải một điều bất ngờ
được phát hiện sau này. Dùng chung một NAT gateway duy nhất thay vì mỗi AZ
một cái sẽ tiết kiệm được khoảng US$32-35/tháng trong số đó, đánh đổi lấy
việc mất đi tính nhất quán Multi-AZ của đường egress đã mô tả ở mục 5.7.2 -
một sự đánh đổi đáng nêu rõ ràng thay vì mặc định chọn một cách âm thầm.

**Sai lầm tốn kém nhất đã được tránh, chứ không phải đã mắc phải:** template
Production của console wizard RDS Multi-AZ mặc định chọn storage là
**Provisioned IOPS SSD (io2)** ở mức 100 GiB thay vì General Purpose SSD
(gp3) được dùng xuyên suốt phần còn lại của dự án - io2 tính phí riêng cho
storage và cho mỗi IOPS được cấp phát, và nếu để nguyên mặc định đó thì đây
sẽ là dòng chi phí lớn nhất trên toàn bộ hóa đơn, bỏ xa cả NAT gateway và
load balancer cộng lại. Điều này đã được phát hiện và sửa trước khi tạo
instance ở mục 5.5.1; đáng nêu rõ ở đây vì đây là kiểu giá trị mặc định
trên console rất dễ bị click qua mà không đọc kỹ.

**Cơ chế quản trị giúp giữ chi phí này luôn hiển thị thay vì gây bất ngờ:**
mọi resource được tạo ra đều mang tag `Owner` từ mục 5.2, nên Cost Explorer
khi nhóm theo tag đó có thể quy bất kỳ đợt tăng chi phí đột biến nào về
đúng một developer cụ thể chỉ trong vài giây. Ngưỡng của billing alarm đã
được nâng từ một giá trị guardrail mang tính tượng trưng lên khoảng
**US$150** - cao hơn mức chi tiêu dự kiến, nên nó vẫn kích hoạt khi có một
sai lầm thực sự (một NAT gateway thứ ba bị bỏ quên đang chạy, một RDS class
quá khổ, một Elastic IP còn sót lại không gắn với gì) mà không kích hoạt với
hoạt động bình thường, đúng như dự kiến, như cách một alarm US$10 sẽ làm ở
mức chi phí này.

<!-- ![Cost Explorer lọc theo tag Owner, hiển thị các dòng chi phí của ALB và RDS Multi-AZ](/images/5-Workshop/5.10-Cost/example.png) -->
