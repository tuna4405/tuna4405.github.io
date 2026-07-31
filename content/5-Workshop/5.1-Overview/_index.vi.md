---
title : "Giới thiệu"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Tổng quan

Caerus là một ứng dụng đặt ghế xem phim. Mỗi suất chiếu có bố cục cố định 6x10 -
sáu hàng, mỗi hàng mười ghế - và một khách hàng có thể giữ tối đa sáu ghế trong
một lần đặt. Quản trị viên tạo suất chiếu và đính kèm ảnh poster; khách hàng duyệt
các suất chiếu sắp tới, mở sơ đồ ghế, đặt vé, xem lại các vé đã đặt, hủy trước giờ
chiếu, và tải vé PDF.

Bài toán mà dự án này thực sự minh họa không phải là đặt ghế theo kiểu CRUD - mà là
điều gì xảy ra khi hai khách hàng chọn cùng một ghế tại cùng một khoảnh khắc. Một
cách cài đặt ngây thơ sẽ đọc tình trạng còn trống của ghế rồi ghi booking thành hai
bước tách rời, để lại một khe cửa mà trong đó cả hai request đều đọc thấy "còn
trống" trước khi bất kỳ bên nào commit. Rạp bán một chiếc ghế hai lần, một cách âm
thầm, và chỉ xảy ra đúng dưới những điều kiện tải mà một rạp phim thật sự quan tâm.
Mọi quyết định kiến trúc nằm sau điều này - việc chọn cơ sở dữ liệu quan hệ, row
lock `SELECT ... FOR UPDATE` bên trong giao dịch đặt vé, và bài kiểm thử concurrency
riêng ở [Kiểm thử](/5-Workshop/5.9-Testing/) - đều tồn tại để đóng khe cửa ấy lại.

**Các dịch vụ AWS được dùng, và vì sao:**

- **Amazon EC2** - chạy API Express dưới `pm2`, hai instance trải trên hai
  Availability Zone để API sống sót khi mất một instance, đặt trong private subnet,
  không có public IP và hoàn toàn không mở SSH vào.
- **NAT Gateway** - cho các instance trong private subnet quyền truy cập internet
  chỉ theo chiều outbound để chạy `npm ci` và vá lỗi hệ điều hành, mà không bao giờ
  nhận một kết nối đi vào.
- **AWS Systems Manager** - Session Manager cung cấp shell tương tác vào cả hai
  instance để triển khai và debug, đi qua đúng đường outbound mà NAT gateway đã có
  sẵn, thay thế hoàn toàn cho SSH.
- **Amazon RDS for PostgreSQL** - năm bảng cốt lõi (`users`, `events`, `seats`,
  `bookings`, `booking_seats`), được chọn chính vì khả năng khóa ở mức dòng và các
  đảm bảo giao dịch; triển khai Multi-AZ bên trong private subnet riêng của nó,
  tách khỏi private subnet của các instance EC2, nhờ đó cơ sở dữ liệu hoàn toàn
  không có đường ra internet.
- **Amazon S3** - static hosting cho trang React đã build (riêng tư, chỉ phục vụ
  qua CloudFront), nơi lưu poster sự kiện, nơi lưu vé PDF được sinh ra, và một
  bucket thứ tư chỉ dùng để tạm chứa gói triển khai backend.
- **Application Load Balancer** - điểm vào duy nhất cho traffic API tới cả hai
  instance EC2, thay cho một rule security group trước đây vốn cho phép traffic từ
  "IP của tôi" bằng một rule chỉ cho phép traffic từ load balancer.
- **Amazon CloudFront** - một distribution, một tên miền HTTPS, định tuyến `/api/*`
  tới load balancer và mọi thứ còn lại tới bucket S3 chứa trang web theo mẫu đường
  dẫn, với origin access control khóa bucket lại chỉ cho CloudFront truy cập.
- **AWS WAF** - gắn vào CloudFront distribution, soi mọi request theo các managed
  rule group ngay tại biên trước khi nó chạm tới load balancer hay origin S3.
- **Amazon CloudWatch và SNS** - một dashboard bao quát EC2, RDS, và load balancer,
  một log group cho ứng dụng, và các alarm gửi cảnh báo qua email thay vì nằm im ở
  trạng thái OK.
- **AWS IAM** - một instance role cho EC2, thu hẹp đúng vào các prefix S3 và quyền
  Systems Manager mà nó cần, mọi tên role đều mang tiền tố `caerus-` mà tài khoản
  bắt buộc.
- **Amazon VPC** - VPC mặc định được mở rộng thêm một cặp private subnet cho các
  instance EC2, một cặp private subnet riêng cho cơ sở dữ liệu, và một gateway
  endpoint để traffic từ EC2 tới S3 không bao giờ rời khỏi mạng của AWS.

Việc tạo vé - render PDF rồi ghi vào S3 - chạy in-process ngay bên trong chính API
Express chứ không phải như một function riêng; nó từng được xây và triển khai dưới
dạng một AWS Lambda function từ sớm rồi chủ động được đưa trở lại, khi khối lượng
công việc hóa ra quá nhỏ để xứng đáng với một deployable thứ hai mang theo IAM role
và bước deploy riêng. Kiến trúc bên dưới là trạng thái cuối cùng, trong đó không
còn Lambda nào.

Sơ đồ kiến trúc bên dưới là trạng thái cuối cùng mà workshop này đi tới, chứ không
phải điểm khởi đầu - các mục tiếp theo dựng nó lên theo đúng thứ tự mà sơ đồ được
đọc: cơ sở dữ liệu và lưu trữ trước, rồi đến compute, rồi tới các tầng mạng riêng tư
và CDN, cuối cùng là khả năng quan sát.

![Kiến trúc cuối cùng của Caerus](/images/5-Workshop/5.1-Overview/architecture.png)

<!-- NOTE for the report author: use the reviewed architecture diagram, with
the frontend S3 bucket relabelled "S3 (Frontend)" rather than "Static
Website" - static website hosting on that bucket was disabled once
CloudFront + OAC took over in section 5.7.6. -->
