---
title : "Giới thiệu"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Tổng quan

Caerus là một ứng dụng đặt ghế xem phim. Mỗi suất chiếu có sơ đồ cố định
6x10 - sáu hàng, mười ghế mỗi hàng - và một khách hàng có thể giữ tối đa
sáu ghế trong một lượt đặt. Quản trị viên tạo suất chiếu và đính kèm hình
ảnh poster; khách hàng duyệt các suất chiếu sắp tới, mở sơ đồ ghế, đặt vé,
xem các lượt đặt của mình, hủy trước giờ chiếu, và tải vé PDF.

Vấn đề mà dự án này thực sự minh họa không phải là đặt ghế theo kiểu CRUD -
mà là điều gì xảy ra khi hai khách hàng chọn cùng một ghế tại cùng một thời
điểm. Một cách triển khai đơn giản sẽ đọc tình trạng còn trống của ghế rồi
sau đó ghi một booking như hai bước riêng biệt, để lại một khoảng hở trong
đó cả hai request đều đọc thấy "còn trống" trước khi bất kỳ request nào
commit. Rạp phim bán một chiếc ghế hai lần, một cách âm thầm, và chỉ xảy ra
đúng trong điều kiện tải mà một rạp phim thật sự quan tâm. Mọi quyết định
kiến trúc phía sau điều này - việc chọn relational database, row lock
`SELECT ... FOR UPDATE` bên trong booking transaction, và bài kiểm thử
concurrency riêng trong [Kiểm thử](/5-Workshop/5.9-Testing/) - đều tồn tại
để khép lại khoảng hở đó.

**Các dịch vụ AWS được sử dụng, và lý do:**

- **Amazon EC2** - chạy API Express dưới `pm2`, hai instance trải trên hai
  Availability Zone để API sống sót khi mất một instance, nằm trong private
  subnet, không có public IP và không mở cổng SSH inbound nào cả.
- **NAT Gateway** - cho phép các instance trong private subnet đó có quyền
  truy cập internet chỉ theo chiều outbound để chạy `npm ci` và vá lỗi hệ
  điều hành, mà không bao giờ chấp nhận một kết nối inbound nào.
- **AWS Systems Manager** - Session Manager cung cấp quyền truy cập shell
  tương tác đến cả hai instance để triển khai và debug qua cùng đường
  outbound mà NAT gateway đã cung cấp, thay thế hoàn toàn SSH.
- **Amazon RDS cho PostgreSQL** - năm bảng cốt lõi (`users`, `events`,
  `seats`, `bookings`, `booking_seats`), được chọn đặc biệt vì row-level
  locking và các đảm bảo transactional; được triển khai Multi-AZ bên trong
  private subnet riêng của nó, tách biệt với các private subnet của EC2
  instance, để database hoàn toàn không có route ra internet.
- **Amazon S3** - lưu trữ tĩnh cho trang React đã build (private, chỉ được
  phục vụ thông qua CloudFront), lưu trữ poster sự kiện, lưu trữ vé PDF
  được tạo ra, và một bucket thứ tư chỉ dùng để chứa tạm gói triển khai
  backend.
- **Application Load Balancer** - điểm vào duy nhất cho lưu lượng API trên
  cả hai EC2 instance, thay thế một quy tắc security group trước đây cho
  phép lưu lượng từ "my IP" bằng một quy tắc chỉ cho phép lưu lượng từ load
  balancer.
- **Amazon CloudFront** - một distribution, một domain HTTPS, định tuyến
  `/api/*` đến load balancer và mọi thứ còn lại đến S3 site bucket theo mẫu
  đường dẫn (path pattern), với origin access control khóa bucket chỉ cho
  phép CloudFront truy cập.
- **AWS WAF** - gắn vào CloudFront distribution, kiểm tra mọi request theo
  các managed rule group ngay tại edge trước khi request đến được load
  balancer hoặc S3 origin.
- **Amazon CloudWatch và SNS** - một dashboard bao quát EC2, RDS, và load
  balancer, một application log group, và các alarm gửi cảnh báo qua email
  thay vì nằm im lặng ở trạng thái OK.
- **AWS IAM** - một instance role cho EC2 được giới hạn phạm vi đúng bằng
  các S3 prefix và quyền Systems Manager mà nó cần, mọi tên role đều mang
  tiền tố `caerus-` mà tài khoản bắt buộc áp dụng.
- **Amazon VPC** - VPC mặc định được mở rộng thêm một cặp private subnet
  cho các EC2 instance, một cặp private subnet riêng cho database, và một
  gateway endpoint để lưu lượng EC2-đến-S3 không bao giờ rời khỏi mạng AWS.

Việc tạo vé - render PDF và ghi nó vào S3 - chạy trong cùng tiến trình
(in-process) bên trong chính API Express đó, không phải như một hàm riêng
biệt; nó từng được xây dựng và triển khai như một hàm AWS Lambda từ giai
đoạn đầu và được cố tình chuyển về lại khi khối lượng công việc hóa ra quá
nhỏ để biện minh cho một deployable thứ hai với IAM role và bước deploy
riêng của nó. Kiến trúc bên dưới là trạng thái cuối cùng, không còn Lambda
trong đó.

Sơ đồ kiến trúc bên dưới là trạng thái cuối cùng mà workshop này đi đến,
không phải điểm khởi đầu - các mục tiếp theo xây dựng nó theo đúng thứ tự
mà sơ đồ thể hiện: database trước, rồi đến storage cùng với CDN phục vụ nó
ngay từ bản build đầu tiên được deploy, rồi đến compute và lớp networking
riêng tư xung quanh nó, rồi đến observability.

![Caerus final architecture](/images/5-Workshop/5.1-Overview/architecture.png)

<!-- LƯU Ý cho tác giả báo cáo: sử dụng sơ đồ kiến trúc đã được rà soát, với
bucket S3 phía frontend được gắn nhãn là "S3 (Frontend, private)" thay vì
"Static Website" - static website hosting không bao giờ được bật cho bucket
đó; nó chỉ được đọc bởi CloudFront thông qua Origin Access Control kể từ
mục 5.6.2. -->
