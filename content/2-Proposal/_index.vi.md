---
title: "Bản đề xuất"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Caerus - Nền tảng đặt ghế xem phim
## Bảo đảm tính toàn vẹn của ghế trong điều kiện concurrency bằng các dịch vụ quản lý của AWS

### 1. Tóm tắt tổng quan

Caerus là một ứng dụng web đặt ghế xem phim được xây dựng để minh họa một bản triển
khai AWS hoàn chỉnh, trải trên các dịch vụ compute, lưu trữ, cơ sở dữ liệu, mạng, và
giám sát. Khách hàng duyệt các suất chiếu, chọn ghế trên một sơ đồ ghế trực tiếp, đặt
tối đa sáu ghế trong một giao dịch, xem lại các vé đã đặt, hủy trước giờ chiếu, và
tải vé PDF. Quản trị viên tạo suất chiếu và tải ảnh poster lên.

Yêu cầu kỹ thuật định danh của dự án là **một ghế không bao giờ được bán hai lần**,
kể cả khi hai khách hàng chọn cùng một ghế tại cùng một khoảnh khắc. Chỉ một ràng
buộc đó đã dẫn dắt việc chọn cơ sở dữ liệu quan hệ, chiến lược khóa ở mức dòng trong
giao dịch đặt vé, và cả kế hoạch kiểm thử. Mọi thứ khác trong hệ thống tồn tại để
phục vụ cho một lượt đặt vé đúng đắn.

Nền tảng chạy trong region `ap-southeast-1` và được một nhóm hai người xây dựng trong
bốn tuần, với backend và frontend phát triển song song dựa trên một hợp đồng API
chung được thống nhất trước khi viết bất kỳ dòng code nào. Tầng compute và tầng dữ
liệu đều nằm sau hai ranh giới cân bằng tải/failover độc lập - một Application Load
Balancer trải trên hai instance EC2, và một cơ sở dữ liệu RDS Multi-AZ - với cả các
instance lẫn cơ sở dữ liệu đều nằm trong private subnet, không có đường đi trực tiếp
ra internet.

### 2. Phát biểu bài toán

#### Vấn đề là gì?

Bán ghế đã giữ chỗ khó hơn vẻ ngoài của nó rất nhiều. Cách cài đặt ngây thơ - đọc
tình trạng còn trống của ghế, rồi ghi một booking - chứa một race condition: hai
request đều có thể đọc thấy "còn trống" trước khi bên nào kịp ghi, và rạp bán một
chiếc ghế hai lần. Lỗi này diễn ra âm thầm, chập chờn, và chỉ xuất hiện đúng vào
những điều kiện quan trọng nhất, khi một suất chiếu ăn khách mở bán và nhiều khách
hàng cùng đổ dồn vào một vài chỗ đẹp.

Một đồ án sinh viên bỏ qua điều này sẽ cho ra một hệ thống đặt vé trông có vẻ chạy
được khi demo nhưng hỏng từ gốc khi đưa vào production. Một dự án đối diện với nó thì
buộc phải lập luận về giao dịch, mức isolation, và cơ chế khóa - và đó chính là loại
bài toán mà hạ tầng đám mây sinh ra để hỗ trợ chứ không phải để tự nó giải quyết
thay.

Ngoài tính đúng đắn, một rạp phim nhỏ không có đội ngũ hạ tầng. Mọi giải pháp đều
phải chịu được việc một instance hỏng mà không gây gián đoạn dịch vụ, phải giữ cơ sở
dữ liệu và các máy chủ ứng dụng nằm ngoài internet công cộng, và phải đủ khả năng
quan sát để một sự cố được phát hiện trước khi khách hàng báo lại.

#### Giải pháp

Caerus lưu ghế, booking, và người dùng trong Amazon RDS for PostgreSQL, và thực hiện
việc đặt vé bên trong một giao dịch cơ sở dữ liệu duy nhất, giao dịch này khóa các
dòng ghế được yêu cầu bằng `SELECT ... FOR UPDATE` trước khi kiểm tra tình trạng còn
trống. Hai request đồng thời cho cùng một ghế không thể cùng thành công: một bên lấy
được khóa dòng, bên kia bị chặn lại, và khi bên thắng commit, bên thua nhìn thấy
trạng thái đã cập nhật, rollback, rồi nhận về một response `409 SEAT_ALREADY_BOOKED`
có nêu tên những ghế đang xung đột để giao diện làm nổi bật chúng lên. Hoặc là mọi
ghế được yêu cầu đều được đặt, hoặc là không ghế nào cả.

Các thành phần còn lại được chọn vì những đảm bảo mang dáng dấp production thật sự
chứ không phải vì một bản demo rẻ nhất có thể. Frontend React là một bản build tĩnh
được phục vụ qua Amazon CloudFront từ một bucket S3 riêng tư. API Express chạy trên
hai instance Amazon EC2, đặt trong private subnet sau một Application Load Balancer,
nhờ đó mất một instance nào cũng không gây gián đoạn; việc cài package và vá lỗi hệ
điều hành theo chiều outbound đi ra internet qua một NAT gateway, còn các instance
được quản trị qua AWS Systems Manager Session Manager thay vì SSH, nên hoàn toàn
không có cổng inbound nào mở ra internet. RDS chạy Multi-AZ, cũng nằm trong một
private subnet không có đường ra internet. Ảnh poster và vé PDF được sinh ra nằm
trong S3, do chính API render, và được phục vụ qua các presigned URL thời hạn ngắn.
CloudFront cũng đứng trước API thông qua load balancer, nên toàn bộ ứng dụng - cả
trang web lẫn API - đều truy cập được qua HTTPS trên một tên miền duy nhất, với AWS
WAF soi traffic ngay tại biên trước khi nó kịp chạm tới load balancer. Amazon
CloudWatch thu thập metric và log ứng dụng, còn các alarm gửi thông báo tới Amazon
SNS khi một target trở nên không khỏe hoặc cơ sở dữ liệu tiến gần tới giới hạn tài
nguyên.

#### Lợi ích và hiệu quả đầu tư

Hệ thống thay thế việc phân ghế thủ công hoặc bằng bảng tính bằng một giao diện mà
khách hàng tự thao tác, và làm được điều đó kèm một cam kết về tính đúng đắn có thể
đem ra chứng minh chứ không chỉ khẳng định suông. Với người vận hành, kiến trúc được
cố ý dựng theo đúng hình hài mà một bản triển khai production sẽ dùng, chứ không phải
hình hài rẻ nhất đủ để qua một buổi demo: không một instance nào hỏng mà làm sập
trang, cơ sở dữ liệu tự động failover, và cả tầng compute lẫn tầng dữ liệu đều không
tiếp cận trực tiếp được từ internet. Mọi tài nguyên đều được gắn tag theo chủ sở hữu
và theo dõi bằng một billing alarm, nên chi phí vận hành thật là một con số đã biết,
được giám sát, chứ không phải một bất ngờ.

Với nhóm thực hiện, dự án tạo ra bằng chứng năng lực có thể chuyển giao được trên tám
dịch vụ AWS, một minh chứng cụ thể về mạng phòng thủ nhiều lớp (private subnet ở cả
tầng compute lẫn tầng dữ liệu, một NAT gateway chỉ cho phép chiều outbound, một load
balancer và một CDN kèm WAF là những điểm vào công khai duy nhất), và một lập luận cụ
thể cho việc chọn lưu trữ quan hệ thay vì NoSQL, dựa trên một bất biến có thật chứ
không phải một ví dụ trong sách giáo khoa.

### 3. Kiến trúc giải pháp

Kiến trúc tách bạch hai mối quan tâm: phân phối nội dung tĩnh và compute mang tính
giao dịch, cả hai đều tiếp cận được qua một biên CDN duy nhất. Trình duyệt nạp ứng
dụng React và gọi API Express thông qua cùng một Amazon CloudFront distribution, nơi
định tuyến theo đường dẫn - `/api/*` đi về Application Load Balancer, mọi thứ còn lại
đi về bucket S3 riêng tư chứa trang web. API tự xử lý mọi thao tác, kể cả việc render
vé PDF, và ghi vào S3 cùng RDS từ bên trong VPC.

![Kiến trúc Caerus](/images/2-Proposal/architecture.png)

<!-- NOTE for the report author: replace with the final reviewed diagram
showing ALB + two private-subnet EC2 instances + NAT gateway, Multi-AZ RDS in
a private subnet, and CloudFront (with WAF) fronting both the ALB and the S3
site bucket. -->

#### Các dịch vụ AWS được sử dụng

- **Amazon EC2**: Chạy máy chủ API Express dưới `pm2`, hai instance trải trên hai
  Availability Zone trong private subnet, đảm nhận xác thực, liệt kê sự kiện, sơ đồ
  ghế, đặt vé, hủy vé, và sinh vé PDF.
- **Application Load Balancer**: Con đường duy nhất đi vào các instance EC2, phân
  phối traffic cho cả hai và liên tục health-check từng cái.
- **NAT Gateway**: Cho các instance EC2 trong private subnet quyền ra internet chỉ
  theo chiều outbound để cài package và vá lỗi hệ điều hành, mà không bao giờ nhận
  một kết nối đi vào từ internet.
- **Amazon RDS for PostgreSQL**: Lưu năm bảng cốt lõi - `users`, `events`, `seats`,
  `bookings`, và `booking_seats`. Được chọn vì giao dịch ACID và khóa ở mức dòng;
  triển khai Multi-AZ trong một private subnet với khả năng tự động failover sang bản
  standby.
- **Amazon S3**: Bốn bucket - static hosting riêng tư cho bản build React (chỉ phục
  vụ qua CloudFront), nơi lưu poster sự kiện, nơi lưu vé PDF được sinh ra, và một
  bucket trung chuyển cho gói triển khai backend.
- **Amazon CloudFront**: Một distribution, một tên miền HTTPS, định tuyến theo đường
  dẫn giữa bucket S3 chứa trang web và load balancer, với Origin Access Control giữ
  cho bucket đó luôn riêng tư.
- **AWS WAF**: Gắn vào CloudFront distribution, soi mọi request ngay tại biên theo
  các managed rule group trước khi nó chạm tới load balancer hay origin S3.
- **Amazon CloudWatch**: Dashboard cho CPU của EC2, số kết nối/dung lượng/CPU của
  RDS, cùng số lượng request và tình trạng target của load balancer; log group cho
  log ứng dụng Express; alarm cho tình trạng target và áp lực tài nguyên của cơ sở dữ
  liệu.
- **Amazon SNS**: Gửi thông báo alarm qua email.
- **AWS IAM**: Hai user lập trình viên trong một group dùng chung, một instance role
  cho EC2 cấp quyền S3 đã thu hẹp phạm vi cùng quyền quản lý qua Systems Manager, mà
  không nhúng credentials ở bất cứ đâu.
- **AWS Systems Manager**: Session Manager cung cấp quyền truy cập shell vào cả hai
  instance EC2 để triển khai và debug, hoàn toàn qua đúng đường outbound mà NAT
  gateway đã có sẵn - không cổng SSH nào bị phơi ra.
- **Amazon VPC**: Một cặp public subnet cho load balancer và NAT gateway, một cặp
  private subnet cho hai instance EC2, một cặp private subnet riêng cho RDS, và một
  gateway endpoint để traffic từ EC2 tới S3 không bao giờ rời khỏi mạng của AWS.

#### Thiết kế các thành phần

- **Frontend**: Một ứng dụng single-page bằng Vite và React, được build thành tài
  nguyên tĩnh rồi đồng bộ lên bucket trang web. Mọi lời gọi API đều đi qua một module
  client duy nhất, nhờ đó việc chuyển ứng dụng từ dữ liệu giả sang API cục bộ rồi
  sang API đã triển khai chỉ là một thay đổi duy nhất.
- **Tầng API**: Express với các route, controller mỏng, và service nắm phần logic
  thật. Xác thực dùng JSON Web Token không trạng thái; mật khẩu được băm bằng bcrypt.
  Giao dịch đặt vé nằm gọn trong một hàm service.
- **Tầng dữ liệu**: Ghế thuộc về một suất chiếu chứ không thuộc về một phòng vật lý,
  nên tình trạng còn chỗ là rõ ràng cho từng suất. Mỗi suất chiếu sinh ra một bố cục
  cố định sáu hàng nhân mười ghế. Tình trạng còn trống của ghế được lưu thành một cột
  để có một dòng cụ thể mà khóa; tổng số ghế và số ghế còn trống được tính ngay lúc
  truy vấn nên chúng không thể trôi lệch.
- **Sinh vé**: API render PDF ngay trong tiến trình, ghi vào bucket tickets, và trả
  về một pre-signed URL với thời hạn ngắn thay vì để object đó công khai.
- **Tiền và thời gian**: Giá vé được lưu bằng số nguyên đơn vị đồng Việt Nam, không
  bao giờ dùng số thực. Tổng tiền của một booking được chụp lại tại thời điểm mua để
  một lần đổi giá về sau không thể viết lại lịch sử. Dấu thời gian được lưu và truyền
  theo UTC nhưng đại diện cho giờ chiếu theo `Asia/Ho_Chi_Minh`, và việc lọc theo
  ngày được thực hiện theo ngày trên lịch Việt Nam.

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai

**Giai đoạn 1 - Thiết kế bản hợp đồng (2 ngày).** Thống nhất bản đặc tả API và schema
cơ sở dữ liệu trong một buổi làm việc duy nhất trước khi viết bất kỳ dòng code nào,
rồi đóng băng cả hai thành những tài liệu mà không lập trình viên nào được tự ý sửa.
Đây chính là điều cho phép backend và frontend được xây đồng thời thay vì tuần tự.

**Giai đoạn 2 - Phát triển cục bộ song song (5 ngày).** Backend cài đặt API Express
trên một container PostgreSQL chạy bằng Docker, bao gồm giao dịch đặt vé và việc
render vé PDF. Frontend dựng các màn hình danh sách sự kiện, chọn ghế, và vé đã đặt
dựa trên các file JSON giả lập có cấu trúc đúng y các response đã thống nhất. Sau đó
là khâu tích hợp, nối giao diện với API đang chạy thật và xử lý các điểm lệch.

**Giai đoạn 3 - Chuyển sang các dịch vụ quản lý của AWS (7 ngày).** Đúng các file SQL
migration và seed đó chạy trên một instance RDS Multi-AZ trong private subnet. Cả bốn
bucket S3 được tạo. API được triển khai lên hai instance EC2 trong private subnet,
đứng sau một Application Load Balancer, chỉ ra ngoài được qua NAT gateway và được
quản trị qua Systems Manager Session Manager thay vì SSH; các security group được thu
hẹp để cơ sở dữ liệu chỉ nhận traffic từ tầng ứng dụng và tầng ứng dụng chỉ nhận
traffic từ load balancer.

**Giai đoạn 4 - CDN, giám sát, và kiểm chứng (7 ngày).** Amazon CloudFront được đặt
trước cả load balancer lẫn bucket S3 chứa trang web để có một tên miền HTTPS duy
nhất, với AWS WAF bật lên tại biên. Các dashboard CloudWatch, việc đẩy log, và các
alarm về sức khỏe/tài nguyên được cấu hình. Bài kiểm thử concurrency được chạy trên
hệ thống đã triển khai, tiếp đó là kiểm thử các trường hợp biên và hoàn thiện.

#### Yêu cầu kỹ thuật

- **Phát triển cục bộ**: Node.js 20 trở lên, Docker Desktop chạy PostgreSQL 16, và
  AWS CLI. Các cổng cục bộ được cố định ở 5173 cho dev server, 3000 cho API, và 5433
  cho container cơ sở dữ liệu.
- **Môi trường chạy**: Amazon Linux trên EC2, Node.js được `pm2` quản lý để tự khởi
  động lại khi máy boot, quản trị hoàn toàn qua Systems Manager Session Manager
  (không SSH), và một instance RDS Multi-AZ chạy PostgreSQL trong private subnet.
- **Kiểm soát truy cập**: Mọi IAM role được tạo cho dự án đều mang tiền tố tên
  `caerus-`, được permission boundary của tài khoản cưỡng chế. Mọi tài nguyên đều
  được gắn tag `Owner` cho biết lập trình viên nào đã tạo ra nó, nhờ đó chi phí có
  thể quy về từng người trong Cost Explorer.
- **Truy cập cross-origin**: Ở môi trường production, frontend và API được phục vụ từ
  cùng một tên miền CloudFront, nên không có request cross-origin nào rời khỏi trình
  duyệt. CORS chỉ cần cho phép Vite dev server cục bộ nói chuyện với một API cục bộ
  hoặc đã triển khai trong lúc phát triển.

### 5. Tiến độ & các cột mốc

Dự án kéo dài bốn tuần nằm trong kỳ thực tập rộng hơn.

- **Tuần 1 - Kiến thức nền tảng và bản dựng cục bộ.** Các khái niệm cốt lõi của AWS,
  thiết lập tài khoản và IAM, buổi thiết kế, phát triển song song, và tích hợp cục
  bộ.
  *Cột mốc: ứng dụng hoàn chỉnh chạy được trên localhost.*
- **Tuần 2 - Các dịch vụ quản lý và triển khai.** RDS, S3, và EC2 được học rồi đưa
  vào dùng; ứng dụng được triển khai sau một load balancer và truy cập được trên một
  địa chỉ công khai.
  *Cột mốc: chạy thật trên AWS.*
- **Tuần 3 - Mạng, CDN, và khả năng quan sát.** Private subnet cùng NAT cho tầng
  compute, CloudFront và WAF cho một tên miền HTTPS duy nhất, dashboard và alarm
  CloudWatch, rồi kiểm thử concurrency và các trường hợp biên.
  *Cột mốc: đủ tính năng và đã được kiểm chứng.*
- **Tuần 4 - Báo cáo và trình diễn.** Viết báo cáo, tập hợp ảnh chụp màn hình, chuẩn
  bị kịch bản trình diễn và bản ghi hình dự phòng.
  *Cột mốc: đã nộp.*

Hai ngày dự phòng được dành ra ở cuối Tuần 2 và hai ngày nữa ở cuối Tuần 3, với giả
định rằng việc cấu hình mạng và CDN sẽ ngốn nhiều thời gian hơn dự kiến.

### 6. Ước tính ngân sách

<!-- TODO: generate an exact estimate at https://calculator.aws for the final
     instance types/sizes chosen and paste the share link on the line below,
     replacing this comment. -->

Kiến trúc này cố ý không được tối ưu để nằm trong Free Tier - một load balancer, một
NAT gateway, và một cơ sở dữ liệu Multi-AZ đều là những khoản chi phí thật, tính theo
giờ bất kể lưu lượng, được chấp nhận một cách có chủ đích để đổi lấy những đảm bảo
mang dáng dấp production đã mô tả ở Mục 3.

#### Chi phí hạ tầng

| Thành phần | Vì sao nó tốn tiền | Chi phí ước chừng |
|---|---|---|
| Application Load Balancer | Tính theo giờ bất kể lưu lượng, cộng thêm LCU-hour khi có tải | ~US$16/tháng ở mức nền |
| NAT Gateway | Tính theo giờ cộng phí xử lý dữ liệu theo mỗi GB | ~US$32-35/tháng ở mức nền |
| RDS Multi-AZ | Instance standby bị tính phí y hệt instance chính | khoảng gấp đôi một instance single-AZ cùng class |
| Amazon EC2 (×2) | Hai instance chạy liên tục; tổng số giờ vượt hạn mức 750 giờ của Free Tier khi cả hai chạy trọn một tháng | vừa phải, tùy theo instance class |
| Amazon CloudFront + WAF | Theo mức sử dụng (mỗi GB, mỗi 10.000 request HTTPS, mỗi rule WAF được đánh giá) | vài đô la ở mức traffic trình diễn |
| Amazon S3 | Thấp hơn nhiều so với hạn mức Free Tier ở khối lượng dữ liệu này | không đáng kể |
| Truyền dữ liệu | Không đáng kể ở mức traffic trình diễn | không đáng kể |

**Tổng ước tính: khoảng US$90-110 mỗi tháng** ở mức traffic trình diễn, bị chi phối
bởi phí theo giờ của NAT gateway và load balancer chứ không phải bởi mức sử dụng thực
tế - cả hai đều tiếp tục tính cùng một mức phí dù ứng dụng phục vụ một request mỗi
ngày hay một nghìn request. Hãy thay con số này bằng liên kết AWS Pricing Calculator
nêu ở trên khi đã tạo được ước tính cho đúng các instance class được chọn.

Billing alarm được đặt cao hơn hẳn mức chi tiêu dự kiến này (khoảng US$150) chứ không
đặt ở một giá trị canh chừng tượng trưng - với hồ sơ chi phí của kiến trúc này, một
ngưỡng thấp sẽ báo động vì hoạt động bình thường thay vì chỉ báo khi có sai sót thật.
Mọi tài nguyên vẫn mang tag `Owner`, nên Cost Explorer nhóm theo chủ sở hữu quy được
bất kỳ đợt tăng vọt nào về một lập trình viên cụ thể chỉ trong vài giây, bất kể ngưỡng
alarm được đặt ở đâu.

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro | Mức ảnh hưởng | Xác suất |
|---|---|---|
| Ghế bị đặt trùng khi có các request đồng thời | Cao | Trung bình |
| Lỗi request cross-origin trong lúc phát triển cục bộ | Thấp | Trung bình |
| Chi phí hằng tháng vượt ước tính (giờ nhàn rỗi của NAT/ALB cộng dồn ngay cả khi không có traffic) | Trung bình | Trung bình |
| Một rule ở biên bị cấu hình sai (cache policy của CDN, managed rule của WAF) âm thầm phá hỏng một request hợp lệ | Trung bình | Trung bình |
| Mất quyền quản trị vào một instance EC2 trong private subnet | Trung bình | Thấp |
| Khâu triển khai ngốn mất thời gian dành cho các giai đoạn sau | Trung bình | Trung bình |

#### Chiến lược giảm thiểu

- **Concurrency**: Khóa các dòng ghế bằng `SELECT ... FOR UPDATE` sắp theo primary
  key, để mọi giao dịch đồng thời đều lấy khóa theo cùng một trình tự và không thể
  deadlock. Kiểm chứng bằng câu update có điều kiện và khẳng định số dòng bị ảnh
  hưởng, nhờ đó một nhánh code đi vòng qua cơ chế khóa sẽ bị phát hiện thay vì được
  âm thầm bỏ qua.
- **Request cross-origin**: Xem CORS là mối bận tâm chỉ của môi trường phát triển cục
  bộ, bởi traffic production là same-origin thông qua CloudFront; hãy cấu hình rõ
  ràng các origin cục bộ được phép ngay trên API thay vì tìm cách lách trình duyệt.
- **Chi phí**: Một billing alarm đặt theo mức chi tiêu thực tế (không phải một giá
  trị tượng trưng), một tag chủ sở hữu trên mọi tài nguyên, và quyền chỉ đọc phần
  billing cho các user lập trình viên để cấu hình alarm không thể bị vô hiệu hóa do
  sơ suất.
- **Cấu hình sai ở biên**: Hãy xem cache policy của CDN và WAF là những thứ có thể âm
  thầm phá hỏng một request hợp lệ chứ không phải chỉ luôn chặn kẻ tấn công - hãy
  kiểm chứng một vòng request/response thật xuyên qua lớp biên cho từng loại nội dung
  (response JSON của API, tải file lên) trước khi coi tầng đó là xong, chứ không chỉ
  kiểm với tài nguyên tĩnh.
- **Quyền quản trị**: Systems Manager Session Manager phụ thuộc vào việc instance
  chạm được tới các endpoint SSM của AWS theo chiều outbound, và điều đó lại phụ
  thuộc vào NAT gateway; nếu đường đi ấy có lúc nào bị đứt, phương án dự phòng là một
  bastion host tạm thời trong public subnet chứ không phải mở lại SSH trên các
  instance private.
- **Tiến độ**: Các ngày dự phòng ở cuối hai tuần nặng nhất, và một frontend chạy được
  trên dữ liệu giả để nó không bao giờ bị chặn chờ backend.

#### Phương án dự phòng

- Nếu môi trường đã triển khai gặp sự cố trong lúc trình diễn, hãy trình bày trên môi
  trường Docker cục bộ, vốn chạy đúng schema và đúng code đó.
- Nếu việc cấu hình CloudFront/WAF không kịp hoàn tất, endpoint HTTP của chính
  Application Load Balancer vẫn truy cập trực tiếp được như một phương án dự phòng,
  bởi hành vi của API không phụ thuộc vào tầng nào đứng trước nó.
- Quay sẵn một video trình diễn từ trước để một sự cố lúc chạy trực tiếp không ngăn
  được việc trình bày thành quả.

### 8. Kết quả kỳ vọng

#### Cải tiến về kỹ thuật

Một ứng dụng đặt vé mà công chúng truy cập được, trong đó tình trạng còn ghế luôn
chính xác dưới tải đồng thời, được kiểm chứng bằng một cuộc chạy đua hai client có
chủ đích cho cùng một ghế, cho ra đúng một lượt đặt thành công và một response báo
xung đột. Khả năng quan sát trong vận hành thông qua các dashboard phủ mọi nhóm dịch
vụ, với một alarm đã được chứng minh là thực sự báo động và gửi thông báo chứ không
chỉ được cấu hình, cùng một tầng compute và một tầng dữ liệu đều không tiếp cận được
từ internet nhờ chính thiết kế mạng chứ không chỉ nhờ kỷ luật với security group.

#### Giá trị dài hạn

Dự án tạo ra một minh chứng chạy được cho việc thiết kế hạ tầng phòng thủ nhiều lớp:
một lớp biên công khai (CDN cộng WAF) là điểm vào duy nhất, một tầng compute riêng tư
có cân bằng tải mà không gì ngoài lớp biên đó chạm tới được, và một tầng dữ liệu riêng
tư chạy Multi-AZ mà không gì ngoài tầng compute chạm tới được - mỗi lớp đều đóng lại
chứ không mở ra khi lớp phía trên nó bị cấu hình sai. Nó cũng tạo ra một lập luận dựa
trên bằng chứng cho việc chọn lưu trữ quan hệ: bất biến của việc đặt vé không thể được
diễn đạt rẻ như vậy nếu thiếu giao dịch, trong khi một tính năng phụ trợ chẳng hạn như
các sự kiện vừa xem sẽ nằm rất tự nhiên trong một kho key-value. Cả hai lập luận đều
bám vào chính hệ thống này chứ không phải một phép so sánh chung chung, và cả hai đều
dùng lại được trong các công việc thiết kế sau này.
