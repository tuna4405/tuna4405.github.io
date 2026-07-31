---
title : "Dọn dẹp tài nguyên"
date : 2026-06-01
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Tổng quan

Việc tháo dỡ phải diễn ra theo đúng thứ tự phụ thuộc, nếu không console sẽ đơn giản
là từ chối lệnh xóa - một security group vẫn đang được một instance đang chạy tham
chiếu tới, hay một target group vẫn còn gắn vào một load balancer, đều không thể gỡ
bỏ khi chưa tới lượt. Thứ tự bên dưới đi từ ngoài vào trong: tầng CDN và cân bằng tải
được thêm vào sau cùng ở mục 5.7 sẽ được hạ xuống trước, tiếp đến là tầng compute và
dữ liệu, và cuối cùng mới tới phần khung sườn IAM/giám sát mà mọi thứ khác từng dựa
vào.

1. **CloudFront**: chọn distribution, bấm **Disable**, chờ trạng thái về *Deployed* ở
   thể vô hiệu hóa, rồi bấm **Delete**. Xóa Web ACL (WAF) và Origin Access Control
   sau đó nếu không còn thứ gì tham chiếu tới chúng.
2. **Application Load Balancer và target group**: xóa load balancer trước, rồi mới
   tới target group giờ đã không còn gắn vào đâu - console cưỡng chế đúng thứ tự này.
3. **Cả hai instance EC2**: terminate, rồi xác nhận trong console rằng không cái nào
   còn ở bất kỳ trạng thái nào (kể cả "stopped", vốn vẫn phát sinh chi phí lưu trữ
   EBS dù không còn tính tiền compute). Đồng thời **gỡ đăng ký các AMI** đã tạo ở
   mục 5.7.7 và **xóa các EBS snapshot đứng sau chúng** - một AMI cùng snapshot của
   nó tiếp tục tốn phí lưu trữ vô thời hạn sau khi instance mà chúng được nhân bản
   từ đó đã biến mất, và rất dễ bị quên chính vì chúng là một bước làm một lần chứ
   không phải thứ được ngó tới thường xuyên.
4. **NAT Gateway**: xóa `caerus-nat` trước, rồi **giải phóng Elastic IP của nó** khi
   gateway đã biến mất - một EIP không gắn vào thứ gì đang chạy sẽ bị tính tiền liên
   tục, khác với một EIP đang gắn vào một NAT gateway hay một instance đang hoạt
   động.
5. **RDS**: xóa instance. Nếu dữ liệu là dữ liệu seed có thể bỏ đi, hãy bỏ qua
   snapshot cuối cùng; nếu cần giữ lại, hãy chủ động tạo một snapshot thay vì trông
   chờ vào giá trị mặc định.
6. **Các bucket S3**: dọn rỗng từng bucket trong cả bốn cái (một bucket chưa rỗng thì
   không xóa được), rồi mới xóa chính các bucket đó.
7. **IAM**: xóa `caerus-ec2-s3-role`.
8. **CloudWatch và SNS**: xóa các alarm, dashboard, log group `/caerus/ec2/api`, và
   SNS topic `caerus-alerts` (các subscription email sẽ tự động bị gỡ cùng với
   topic).
9. **Các security group**: `caerus-alb-sg`, `caerus-ec2-sg`, `caerus-rds-sg` - giờ đã
   xóa được vì không còn gì tham chiếu tới chúng.
10. **Các phần bổ sung trong VPC**: cả hai cặp private subnet - của cơ sở dữ liệu từ
    mục 5.5.4 và của tầng ứng dụng từ mục 5.7.7 - cùng hai route table của chúng và
    DB subnet group. Bản thân những thứ này không tốn tiền nếu để nguyên, nhưng vẫn
    nên xóa luôn để có một tài khoản thật sự sạch sẽ, nếu dự án đã kết thúc hẳn chứ
    không phải chỉ tạm dừng.

**Kết thúc bằng một ảnh chụp màn hình chứng minh tài khoản đã sạch** - mục
"Instances" của EC2 không còn cái nào đang chạy, mục "Databases" của RDS trống rỗng,
và console của CloudFront lẫn Load Balancer đều hiển thị con số không tài nguyên -
bằng chứng mạnh mẽ nhất có thể có rằng không còn thứ gì đang âm thầm phát sinh chi
phí sau khi báo cáo đã nộp.

<!-- ![EC2, RDS, and CloudFront consoles all showing no resources remaining](/images/5-Workshop/5.11-Cleanup/example.png) -->
