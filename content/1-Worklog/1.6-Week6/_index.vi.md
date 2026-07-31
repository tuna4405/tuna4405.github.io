---
title: "Nhật ký tuần 6"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu Tuần 6: Caerus - Các Tính năng Dựa trên AWS, CDN, Tăng cường Bảo mật Mạng, và Khả năng Quan sát

* Cài đặt hai endpoint chỉ tồn tại được khi đã có hạ tầng AWS thật: tải poster và tạo vé.
* Đặt một tên miền HTTPS duy nhất cùng lớp bảo vệ biên (edge protection) trước toàn bộ ứng dụng.
* Kéo tầng compute ra khỏi internet công cộng hoàn toàn, và dựng xong dashboard cùng các alarm.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Nhánh backend]** Cài đặt chức năng tải poster cho admin: ảnh được đưa vào bucket images riêng tư, và mọi lượt đọc đều đi qua một pre-signed URL vừa ký, thời hạn ngắn, thay cho một bucket public <br> - **[Nhánh frontend]** Dựng form tải lên cho admin và nối poster ra các thẻ (card) sự kiện | 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 3 | - Tìm hiểu Lambda và API Gateway song song với nhau <br> - **[Nhánh backend]** Dựng chức năng tạo vé thành một Lambda function được gọi thẳng từ API: nó render PDF, ghi vào bucket tickets, rồi trả về một pre-signed download URL; đồng thời cũng thử đưa chức năng hủy vé sang một Lambda thứ hai <br> - **[Nhánh frontend]** Nối nút tải vé với cấu trúc response mới | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 4 | - Xem xét lại cả hai lần chuyển sang serverless dựa trên bằng chứng, thay vì để nguyên như lần cài đặt đầu: <br> &emsp; + Đưa chức năng hủy vé trở lại API server - nó dùng chung logic khóa (locking) của giao dịch đặt vé và chẳng được lợi gì khi bị tách ra <br> &emsp; + Quyết định bỏ luôn Lambda tạo vé, khi đã thấy rõ khối lượng công việc quá nhỏ và quá thưa để xứng đáng với một deployable thứ hai mang theo IAM role và bước deploy riêng; việc render PDF được đưa vào chạy in-process ngay trong API Express <br> - Cập nhật change log của bản đặc tả API để cả hai lần đảo ngược đều được ghi lại kèm lý do, thay vì xóa lịch sử đi <br> - Bắt tay vào Amazon CloudFront: một distribution duy nhất, định tuyến theo đường dẫn (path) giữa bucket S3 của trang web và load balancer | 22/07/2026 | 22/07/2026 | |
| 5 | - Hoàn tất cấu hình CloudFront: Origin Access Control đưa bucket trang web trở lại private, custom error response phục vụ routing của SPA, và một tên miền HTTPS duy nhất chạy thật cho cả trang web lẫn API <br> - Phát hiện WAF đi kèm CloudFront đang lặng lẽ chặn những lượt tải poster vượt quá giới hạn kích thước request-body mặc định, bị che thành một "thành công giả" bởi chính custom error response vốn dành cho SPA fallback; khắc phục bằng cách ghi đè action của đúng rule WAF đó thay vì tắt hẳn WAF <br> - **Đăng Blog 2** (case study về SeatGeek đã đọc từ Tuần 1) lên cộng đồng AWS Study Group | 23/07/2026 | 23/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/> |
| 6 | - Kéo tầng compute ra khỏi internet công cộng: tạo một cặp private subnet cho các instance EC2 (để tách khỏi subnet của cơ sở dữ liệu, nhờ đó hai route table độc lập với nhau), một NAT gateway chỉ phục vụ chiều outbound để cài package và vá lỗi (patching), và cấp quyền Systems Manager trên instance role <br> - Thay cả hai instance đang chạy bằng các bản clone dựng từ AMI, khởi chạy vào những private subnet mới, xác minh qua Session Manager thay vì SSH, rồi gỡ bỏ hẳn rule SSH ra khỏi security group | 24/07/2026 | 24/07/2026 | |
| 7 | - Dựng dashboard CloudWatch (EC2, RDS, load balancer) và đẩy application log vào một log group <br> - Cấu hình alarm cho tình trạng sức khỏe (health) của target group và cho áp lực tài nguyên của cơ sở dữ liệu, nối tới một SNS topic gửi email <br> - Cố ý kích hoạt một alarm để chắc chắn nó thật sự báo động (fire) và gửi thông báo, thay vì tin tưởng nó khi nó đang nằm yên ở trạng thái OK mà chưa từng kiểm chứng | 25/07/2026 | 25/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |

### Kết quả đạt được Tuần 6:

* Cài đặt xong cả hai endpoint phụ thuộc AWS bị để lại từ Tuần 4, mỗi endpoint đều đọc và ghi thông qua presigned URL chứ không qua bucket public.
* Dựng một Lambda function đúng cách, chứng minh nó chạy được, rồi vẫn gỡ bỏ khi bằng chứng cho thấy một server thường trực (persistent server) mới là lựa chọn phù hợp hơn - một quyết định bị đảo ngược nhưng vẫn giữ nguyên lý do, không phải một sai lầm bị giấu đi.
* Chẩn đoán được tương tác giữa CloudFront và WAF khiến một lượt chặn thật bị che thành thành công giả, và khắc phục bằng một lần ghi đè đúng trọng tâm vào rule cụ thể thay vì tắt cả lớp bảo vệ.
* Kéo toàn bộ tầng compute ra khỏi internet công cộng - không public IP, không cho SSH đi vào - mà vẫn giữ được đường triển khai và debug qua Systems Manager Session Manager.
* Đăng bài blog thứ hai của kỳ thực tập, biến phần đọc thêm từ Tuần 1 thành một case study viết lại cho cộng đồng.
* Dựng một dashboard cùng các alarm phủ hết những dịch vụ còn lại, trong đó ít nhất một alarm đã được chứng minh là thật sự báo động và gửi thông báo chứ không bị bỏ mặc chưa kiểm chứng.
