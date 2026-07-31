---
title : "Dọn dẹp tài nguyên"
date : 2026-06-01
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Tổng quan

Việc gỡ bỏ (teardown) phải được thực hiện theo đúng thứ tự phụ thuộc, nếu
không console sẽ đơn giản là từ chối xóa - một security group vẫn đang
được một instance đang chạy tham chiếu tới, hay một target group vẫn còn
gắn với một load balancer, thì không thể bị xóa trước lượt. Thứ tự bên
dưới đi từ ngoài vào trong: tầng CDN và load-balancing ở rìa ngoài cùng
của kiến trúc sẽ bị gỡ trước tiên, tiếp theo là compute và data, và cuối
cùng là phần khung IAM/monitoring mà mọi thứ khác đều phụ thuộc vào.

1. **CloudFront**: chọn distribution, **Disable**, đợi trạng thái đạt
   *Deployed* ở trạng thái đã disable, sau đó **Delete**. Xóa Web ACL
   (WAF) và Origin Access Control sau đó nếu không còn gì khác tham chiếu
   đến chúng.
2. **Application Load Balancer và target group**: xóa load balancer
   trước, sau đó xóa target group giờ đã không còn gắn với gì - console
   bắt buộc theo đúng thứ tự này.
3. **Cả hai EC2 instance**: terminate, sau đó xác nhận trên console rằng
   không còn instance nào tồn tại ở bất kỳ trạng thái nào (kể cả
   "stopped," trạng thái này vẫn phát sinh chi phí lưu trữ EBS dù không
   tính phí compute). Không có AMI nào cần dọn ở đây cả - cả hai instance
   đều được khởi tạo trực tiếp vào private subnet của chúng ngay từ đầu
   (mục 5.7.2), chưa bao giờ được clone từ một instance đang chạy.
4. **NAT Gateway**: xóa `caerus-nat-1a` và `caerus-nat-1b`, sau đó
   **release Elastic IP** của từng cái khi gateway tương ứng đã không còn -
   một EIP không gắn với bất kỳ resource nào đang chạy sẽ liên tục bị tính
   phí, khác với khi nó gắn với một NAT gateway hoặc instance đang hoạt
   động.
5. **RDS**: xóa instance. Nếu dữ liệu chỉ là seed data có thể bỏ đi, bỏ
   qua final snapshot; nếu cần được lưu giữ, hãy chủ động tạo một
   snapshot thay vì dựa vào mặc định.
6. **S3 buckets**: làm rỗng cả bốn bucket (một bucket không rỗng thì
   không thể xóa được), sau đó xóa chính các bucket đó.
7. **IAM**: xóa `caerus-ec2-s3-role`.
8. **CloudWatch và SNS**: xóa các alarm, dashboard, log group
   `/caerus/ec2/api`, và SNS topic `caerus-alerts` (các email subscription
   sẽ tự động bị xóa cùng với topic).
9. **Security groups**: `caerus-alb-sg`, `caerus-ec2-sg`, `caerus-rds-sg` -
   có thể xóa được vì giờ không còn gì tham chiếu đến chúng.
10. **Các thành phần VPC bổ sung**: cả hai cặp private subnet - của
    database từ mục 5.5.1 (một route table dùng chung, cộng với DB subnet
    group) và của tầng application từ mục 5.7.2 (hai route table, mỗi
    Availability Zone một cái, khớp với hai NAT gateway của nó). Không cái
    nào trong số này tự nó phát sinh chi phí nếu để nguyên, nhưng cũng nên
    xóa chúng để có một account thực sự sạch sẽ nếu dự án đã hoàn toàn kết
    thúc chứ không chỉ tạm dừng.

**Kết thúc bằng một screenshot chứng minh account đã sạch** - mục
"Instances" của EC2 không có instance nào đang chạy, mục "Databases" của
RDS trống, và console của cả CloudFront lẫn Load Balancer đều hiển thị
không còn resource nào - đây là bằng chứng mạnh nhất có thể cho thấy không
có gì đang âm thầm phát sinh chi phí sau khi báo cáo được nộp.

<!-- ![Console EC2, RDS, và CloudFront đều không còn resource nào](/images/5-Workshop/5.11-Cleanup/example.png) -->
