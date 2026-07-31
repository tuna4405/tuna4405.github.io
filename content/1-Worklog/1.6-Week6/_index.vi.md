---
title: "Nhật ký tuần 6"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu Tuần 6: Caerus - Các Tính năng Phụ thuộc AWS, CDN, Tăng cường Bảo mật Mạng, và Giám sát

* Triển khai hai endpoint cần có hạ tầng AWS thật mới thực hiện được: tải poster và tạo vé.
* Đưa một tên miền HTTPS duy nhất và lớp bảo vệ biên (edge protection) ra trước toàn bộ ứng dụng.
* Đưa tầng compute ra hoàn toàn khỏi internet công cộng, và thiết lập dashboard cùng alarm.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Nhánh backend]** Triển khai tải poster (admin): ảnh được ghi vào bucket images riêng tư, và mọi lượt đọc đều được phục vụ qua một pre-signed URL vừa được ký, có thời hạn ngắn, thay vì dùng bucket public <br> - **[Nhánh frontend]** Xây dựng form tải lên cho admin và gắn poster vào các thẻ (card) sự kiện | 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 3 | - Tìm hiểu Lambda và API Gateway cùng nhau <br> - **[Nhánh backend]** Xây dựng chức năng tạo vé dưới dạng một Lambda function được gọi trực tiếp từ API: nó render PDF, ghi vào bucket tickets, và trả về một pre-signed download URL; cũng thử chuyển luôn chức năng hủy vé sang một Lambda thứ hai <br> - **[Nhánh frontend]** Gắn nút tải vé với cấu trúc response mới | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 4 | - Xem xét lại cả hai quyết định chuyển sang serverless dựa trên bằng chứng thực tế thay vì giữ nguyên như lúc triển khai đầu tiên: <br> &emsp; + Chuyển hủy vé trở lại API server - vì nó dùng chung logic khóa (locking) của giao dịch đặt vé và không được lợi gì khi tách riêng <br> &emsp; + Quyết định gỡ bỏ luôn Lambda tạo vé, sau khi thấy rõ khối lượng công việc quá nhỏ và không thường xuyên để biện minh cho một deployable thứ hai với IAM role và bước deploy riêng; chuyển việc render PDF vào chạy trực tiếp (in-process) trong API Express <br> - Cập nhật change log của bản đặc tả API để ghi lại cả hai lần đảo ngược quyết định kèm lý do, thay vì xóa bỏ lịch sử <br> - Bắt đầu với Amazon CloudFront: một distribution, định tuyến theo đường dẫn (path-based routing) giữa bucket S3 của trang web và load balancer | 22/07/2026 | 22/07/2026 | |
| 5 | - Hoàn thiện cấu hình CloudFront: Origin Access Control khóa bucket trang web về lại private, custom error response cho routing của SPA, và tên miền HTTPS duy nhất chạy thật cho cả trang web lẫn API <br> - Phát hiện WAF đi kèm CloudFront đang âm thầm chặn việc tải poster vượt quá giới hạn kích thước request-body mặc định, bị ngụy trang thành một "thành công giả" bởi chính custom error response dùng cho SPA fallback; khắc phục bằng cách ghi đè action của rule WAF cụ thể đó thay vì tắt WAF hoàn toàn <br> - **Đăng Blog 2** (case study về SeatGeek đã đọc ở Tuần 1) lên cộng đồng AWS Study Group | 23/07/2026 | 23/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/> |
| 6 | - Đưa tầng compute ra hoàn toàn khỏi internet công cộng: tạo một cặp private subnet cho các instance EC2 (tách riêng khỏi subnet của cơ sở dữ liệu, để route table của chúng độc lập với nhau), một NAT gateway để chỉ cho phép cài đặt package và vá lỗi (patching) theo chiều outbound, và cấp quyền Systems Manager trên instance role <br> - Thay thế cả hai instance đang chạy bằng các bản clone dựa trên AMI khởi chạy vào các private subnet mới, xác minh qua Session Manager thay vì SSH, sau đó gỡ bỏ hoàn toàn rule SSH khỏi security group | 24/07/2026 | 24/07/2026 | |
| 7 | - Xây dựng dashboard CloudWatch (EC2, RDS, load balancer) và đẩy application log vào một log group <br> - Cấu hình alarm cho tình trạng sức khỏe (health) của target group và áp lực tài nguyên cơ sở dữ liệu, kết nối tới một SNS topic gửi email <br> - Chủ động kích hoạt một alarm để xác nhận nó thực sự báo động (fire) và gửi thông báo, thay vì tin tưởng nó ở trạng thái OK mà chưa từng kiểm chứng | 25/07/2026 | 25/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |

### Kết quả đạt được Tuần 6:

* Triển khai cả hai endpoint phụ thuộc AWS đã bị hoãn từ Tuần 4, mỗi endpoint đọc và ghi thông qua presigned URL thay vì dùng bucket public.
* Xây dựng đúng một Lambda function, chứng minh nó hoạt động, và vẫn gỡ bỏ nó khi bằng chứng cho thấy một server thường trực (persistent server) phù hợp hơn - một quyết định bị đảo ngược nhưng vẫn giữ lại lý do, không phải một sai lầm bị che giấu.
* Chẩn đoán được một tương tác giữa CloudFront và WAF khiến một lượt chặn thật sự bị ngụy trang thành thành công giả, và khắc phục bằng cách ghi đè có mục tiêu một rule cụ thể thay vì tắt toàn bộ lớp bảo vệ.
* Đưa toàn bộ tầng compute ra khỏi internet công cộng - không có public IP, không cho SSH truy cập vào - trong khi vẫn giữ được quyền truy cập để triển khai và debug thông qua Systems Manager Session Manager.
* Đăng bài blog thứ hai của kỳ thực tập, biến tài liệu đọc thêm từ Tuần 1 thành một case study được viết lại cho cộng đồng.
* Xây dựng một dashboard và các alarm bao phủ mọi dịch vụ còn lại, với ít nhất một alarm đã được chứng minh là thực sự báo động và gửi thông báo chứ không để chưa từng kiểm chứng.
