---
title: "Các blog đã đăng"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong kỳ thực tập, tôi đã viết và đăng ba bài blog lên cộng đồng
[AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Cả ba bài đều
được viết bằng tiếng Việt cho nhóm độc giả đó; phần tóm tắt bên dưới là bản tiếng
Việt tương ứng.

Hai trong số các bài viết ra đời từ những vấn đề tôi gặp trực tiếp trong lúc học: mất
dấu những gì mình đã tạo ra, và lo lắng về chi phí trên một tài khoản cá nhân. Bài
thứ ba là bản tóm tắt một case study trên AWS Architecture Blog, vốn trả lời đúng một
câu hỏi tôi có trong lúc thiết kế API của chính mình.

### [Blog 1 - AWS Budgets và Cost Anomaly Detection](3.1-Blog1/)

Hai công cụ quản lý chi phí trả lời hai câu hỏi khác nhau. AWS Budgets cảnh báo khi
chi tiêu vượt qua một ngưỡng bạn tự đặt, bao gồm cả cảnh báo dựa trên dự báo vốn kích
hoạt trước khi bạn thật sự vượt ngưỡng. Cost Anomaly Detection học thói quen chi tiêu
bình thường của một tài khoản rồi gắn cờ những gì lệch khỏi nó mà không cần đặt ngưỡng
nào. Bài viết trình bày các khái niệm, các bước thao tác trên console cho cả hai, mức
giá hiện hành (giám sát và cảnh báo đều miễn phí; chỉ Budget Actions và báo cáo theo
lịch mới tính tiền), cùng những giới hạn thực tế: không công cụ nào chạy thời gian
thực, và phần phát hiện bất thường cần khoảng mười ngày dữ liệu lịch sử cho mỗi dịch
vụ trước khi hoạt động được.

### [Blog 2 - SeatGeek kiểm soát authorization và rate limiting cho một SaaS đa tenant như thế nào](3.2-Blog2/)

Bản tóm tắt một case study trên AWS Architecture Blog về bài toán "hàng xóm ồn ào":
làm sao ngăn một tenant ngốn hết dung lượng dùng chung gây thiệt cho các tenant khác.
SeatGeek đưa phần authorization ra khỏi từng dịch vụ riêng lẻ và gom vào một Lambda
authorizer duy nhất tại API Gateway, ánh xạ tenant với API key thông qua DynamoDB, và
dùng các usage plan phân tầng để cưỡng chế giới hạn số request cho từng tenant. Bài
viết đi theo một request từ đầu đến cuối và làm nổi bật cơ chế cache nhiều tầng vốn
giữ cho thiết kế này vừa nhanh vừa rẻ.

### [Blog 3 - AWS Config và Conformance Packs](3.3-Blog3/)

Một góc nhìn về cách tìm ra bạn đã thực sự cấu hình những gì. AWS Config ghi lại
trạng thái cấu hình của mọi tài nguyên và đối chiếu chúng với các rule; một conformance
pack triển khai trọn một bộ rule như một khối duy nhất. Bài viết trình bày các khái
niệm cốt lõi, những template mẫu sẵn có, sáu bước triển khai, và hai lời cảnh báo
đáng đọc trước khi bật nó lên: việc đánh giá rule không nằm trong free tier, và một
điểm tuân thủ cao chỉ có nghĩa là các rule trong pack đó đã đạt, chứ không có nghĩa là
môi trường của bạn an toàn.
