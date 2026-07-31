---
title: "Blog 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# SEATGEEK KIỂM SOÁT AUTHORIZATION, AUTHENTICATION, VÀ RATE LIMITING TRONG MỘT ỨNG DỤNG SAAS ĐA TENANT NHƯ THẾ NÀO

**Ngày đăng: 23-07-2026**

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/**

### Vì sao mình viết bài này

Một trong những bài toán khó khi xây một hệ thống SaaS phục vụ nhiều khách hàng cùng
lúc là: làm sao bảo đảm khách hàng A không thể, dù vô tình hay cố ý, ngốn hết dung
lượng dùng chung và làm suy giảm chất lượng dịch vụ của khách hàng B?

Đây đúng là bài toán mà SeatGeek đã đối mặt. Họ là một nền tảng bán vé phục vụ hàng
chục triệu vé mỗi ngày, và cách họ giải quyết bằng các dịch vụ serverless của AWS đã
dạy cho mình vài điều đáng chia sẻ lại.

### Bối cảnh: khi mỗi dịch vụ tự lo phần xác thực của mình

Trước đây, các đối tác B2B truy vấn dữ liệu kinh doanh của SeatGeek - có thể lên tới
hàng terabyte - thông qua nhiều công cụ quản lý danh tính và truy cập khác nhau. Vấn
đề là mỗi ứng dụng nội bộ lại tự cài đặt logic authorization của riêng nó. Điều đó dẫn
tới trùng lặp công sức, không có chuẩn chung, và càng lúc càng khó kiểm soát khi số
lượng tenant tăng lên.

SeatGeek đặt ra ba tiêu chí khi đi tìm giải pháp:

1. Tiếp tục dùng Auth0, nhà cung cấp danh tính vốn đã có sẵn.
2. Tránh làm tăng gánh nặng vận hành hạ tầng, ưu tiên ghép nối các dịch vụ serverless
   được quản lý sẵn.
3. Mở rộng mượt mà theo nhu cầu, mà không phải trả tiền cho phần dung lượng nhàn rỗi
   hoặc cấp phát dư.

### Những điểm chính

**API Gateway làm cánh cửa vào duy nhất, và không có logic xác thực nào nằm trong các
dịch vụ riêng lẻ.** Mọi API của SeatGeek đều đi qua một gateway, nơi một Lambda
authorizer tùy chỉnh kiểm tra token, thay vì để từng dịch vụ backend tự làm việc đó
một cách độc lập.

**Các usage plan phân tầng để ngăn bài toán hàng xóm ồn ào.** SeatGeek tạo ra các gói
phân tầng - bronze, silver, gold - mỗi gói có giới hạn số request mỗi giây riêng. Mỗi
tenant nhận một API key gắn với một usage plan cụ thể, nhờ đó không tenant nào có thể
ngốn hết phần dung lượng dùng chung mà những tenant khác đang trông cậy vào.

**DynamoDB làm cây cầu vô hình giữa Auth0 và API Gateway.** Thay vì bắt các tenant tự
quản lý API key của mình, DynamoDB giữ một bảng ánh xạ giữa tenant ID do Auth0 quản lý
và API key do API Gateway quản lý. Việc quản lý key trở nên hoàn toàn trong suốt đối
với tenant.

**Tự động hóa việc tiếp nhận tenant mới bằng Terraform.** Khi một tenant mới xuất
hiện, hệ thống tự động tạo tenant ID trong Auth0, tạo một API key mới trong API
Gateway, và lưu mối liên kết vào DynamoDB. Không có bước thủ công nào.

### Đi theo một request

Một đối tác B2B muốn truy vấn dữ liệu bán vé của mười hai tháng. Luồng xử lý diễn ra
như sau:

```
Tenant
  -> Auth0 (machine-to-machine authentication)
  -> JWT token
  -> API Gateway
  -> Lambda authorizer
  -> DynamoDB (look up API key by tenant ID)
  -> API Gateway checks rate limit against the usage plan
  -> Backend handles the request
```

**Bước xác thực.** Tenant xác thực theo kiểu machine-to-machine và nhận về một JWT
chứa các claim cần cho bước authorization tiếp theo: tenant ID, thời hạn, scope, và
chữ ký.

**Bước phân quyền.** API Gateway trích token ra khỏi request rồi chuyển cho Lambda
authorizer. Authorizer lấy khóa xác thực token từ Auth0 - và chi tiết thú vị là khóa
này được **cache ngay trong bộ nhớ của chính authorizer**, nên Auth0 chỉ bị gọi một
lần cho mỗi lần môi trường thực thi Lambda khởi động. Điều đó giảm độ trễ và tránh dội
bom nhà cung cấp danh tính.

**Bước giới hạn tần suất.** Khi authorizer đã xác thực xong token và trả về API key
tương ứng từ DynamoDB, API Gateway kiểm tra xem tenant đó đã vượt giới hạn tần suất
của usage plan hay chưa. Nếu đã vượt, API Gateway trả về ngay HTTP 429 Too Many
Requests - request không bao giờ chạm tới backend.

Một chi tiết nữa mình thích: API Gateway có thể cache kết quả của authorizer trong tối
đa năm phút, nên cùng một token sẽ không bị xác thực lại nhiều lần trong khoảng thời
gian đó. Việc này giảm tải đáng kể cho cả Lambda lẫn DynamoDB.

### Kết luận

Điều gây ấn tượng nhất với mình trong kiến trúc của SeatGeek là mức độ triệt để mà họ
tách logic authorization ra khỏi từng dịch vụ nghiệp vụ riêng lẻ, biến nó thành một
tầng dùng chung đặt tại API Gateway. Cách đó giải quyết hai vấn đề cùng lúc: nó chuẩn
hóa việc xác thực trên toàn hệ thống, và nó xóa bỏ nhu cầu mỗi đội lại phải phát minh
lại đúng một cái bánh xe.

Mình cũng học được rằng cache ở đây không chỉ là một tối ưu về hiệu năng. Nó còn đóng
vai trò kiểm soát chi phí và bảo vệ một nhà cung cấp danh tính bên ngoài. Việc cache
khóa xác thực token bên trong Lambda, kết hợp với cache kết quả authorizer ở tầng API
Gateway, là một mẫu cache nhiều tầng đáng áp dụng cho bất kỳ hệ thống nào phải xác
thực với tần suất cao.

Với những ai đang tìm hiểu cách xây dựng SaaS đa tenant cho đúng, đây là một case study
giá trị: bạn không cần tự xây một dịch vụ danh tính phức tạp của riêng mình. Kết hợp
API Gateway, một Lambda authorizer, và DynamoDB một cách cẩn thận là đã đủ tạo ra một
lớp bảo vệ vừa chuẩn hóa vừa rẻ để vận hành.

### Tài liệu tham khảo

* How SeatGeek uses AWS Serverless to control authorization, authentication, and
  rate-limiting in a multi-tenant SaaS application - AWS Architecture Blog:
  https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.2-Blog2/ and is referenced like:

-->
![Bài viết đã đăng trên trang cộng đồng AWS Study Group](/images/3-BlogsPosted/3.2-Blog2/post.png)
