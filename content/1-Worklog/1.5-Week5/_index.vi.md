---
title: "Nhật ký tuần 5"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu Tuần 5: Caerus - Đưa lên AWS

* Dời cơ sở dữ liệu sang Amazon RDS và trang tĩnh sang Amazon S3, đưa API chạy trên Amazon EC2.
* Có được một ứng dụng mà công chúng truy cập được, sau đó gỡ bỏ điểm lỗi đơn (single point of failure) ở cả tầng compute lẫn tầng cơ sở dữ liệu.
* Khép lại tuần với hai instance EC2 nằm sau một load balancer và một cơ sở dữ liệu Multi-AZ đặt trong private subnet.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Nhánh backend]** Khởi chạy một instance RDS PostgreSQL Single-AZ rồi chạy các file migration và seed trên đó, không đổi gì ngoài connection string <br> - **[Nhánh frontend]** Tạo đủ bốn bucket S3 (trang frontend, poster sự kiện, vé được sinh ra, gói triển khai backend) | 13/07/2026 | 13/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3 | - **[Nhánh backend]** Tạo instance role cho EC2, thu hẹp phạm vi đúng vào những bucket S3 mà nó phải chạm tới, theo đúng quy ước đặt tên của tài khoản <br> - **[Nhánh frontend]** Bật static website hosting cho bucket trang web <br> - Cùng nhau: rà lại các security group và thu hẹp để cơ sở dữ liệu chỉ nhận traffic từ security group của instance ứng dụng, chứ không nhận từ những địa chỉ bất kỳ | 14/07/2026 | 14/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - **[Nhánh backend]** Khởi chạy instance EC2, cài Node.js, và cho API chạy dưới `pm2` <br> - **[Nhánh frontend]** Build bản production của React, publish lên bucket trang web, và trỏ nó tới địa chỉ public của API trên EC2 <br> - Gỡ lỗi cross-origin vốn đã lường trước bằng cách cấu hình các origin được phép ngay trên API, thay vì tìm cách lách ở phía trình duyệt <br> - **Cột mốc đạt được:** ứng dụng chạy thật (live) trên AWS, trên một instance EC2 duy nhất | 15/07/2026 | 15/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 5 | - Một instance EC2 duy nhất chính là một điểm lỗi đơn (single point of failure): khởi chạy thêm instance thứ hai ở một Availability Zone khác, cùng AMI và cùng cấu hình <br> - Tạo một target group và một Application Load Balancer (ALB) đặt trước cả hai instance, rồi kiểm thử traffic luân phiên giữa chúng trước khi động vào bất cứ thứ gì khác <br> - Chỉ sau khi chắc chắn load balancer chạy tốt, nhóm mới thu hẹp security group của EC2 để chỉ nhận traffic từ security group của load balancer, và trỏ frontend sang DNS name của load balancer | 16/07/2026 | 16/07/2026 | |
| 6 | - Dời RDS sang Multi-AZ bên trong một private subnet không có route ra internet: tạo hai private subnet cùng một route table không mang route `0.0.0.0/0`, và một DB subnet group mới <br> - Vấp phải một lỗi validation đã được biết đến của RDS console/CLI khi dời subnet group của một instance đang tồn tại sang subnet group khác trong cùng VPC; vượt qua bằng cách xóa instance đó - nó chỉ chứa dữ liệu seed - rồi tạo lại hẳn với Multi-AZ và private subnet group được chọn ngay từ lúc khởi tạo <br> - Phát hiện loại storage mặc định của template Production (Provisioned IOPS SSD, 100 GiB) và đưa về General Purpose SSD 20 GiB trước khi tạo bất cứ thứ gì - nếu để nguyên như vậy, đây sẽ là dòng lớn nhất trên toàn bộ hóa đơn | 17/07/2026 | 17/07/2026 | |
| 7 | - Buffer: kiểm tra hostname của endpoint không thay đổi sau khi tạo lại RDS, nghĩa là `DATABASE_URL` không cần sửa gì, và xác nhận cơ sở dữ liệu vẫn truy cập được từ EC2 dù không hề có route nào ra internet <br> - Chạy lại luồng đặt vé end-to-end thêm một lượt trên môi trường vừa dựng lại để chắc chắn không có gì bị lỗi ngược (regression) | 18/07/2026 | 18/07/2026 | |

### Kết quả đạt được Tuần 5:

* Chuyển từ cơ sở dữ liệu Docker cục bộ sang RDS được quản lý, và từ filesystem cục bộ sang S3, bằng cách thay cấu hình chứ không thay code.
* Có được một bản triển khai mà công chúng truy cập được, rồi chủ động gỡ bỏ cả hai điểm lỗi đơn của nó: thêm một instance EC2 thứ hai sau load balancer, và một cơ sở dữ liệu Multi-AZ.
* Chẩn đoán được một hạn chế có thật của RDS console và tìm ra lối đi vòng thay vì bị chặn lại, chọn con đường nhanh hơn nhưng vẫn hợp lý vì khi đó cơ sở dữ liệu chỉ chứa dữ liệu seed có thể bỏ đi.
* Bắt được một cấu hình mặc định của console (Provisioned IOPS storage) mà nếu để nguyên sẽ chiếm phần lớn nhất trong hóa đơn cả tháng, ngay trước khi nó kịp được tạo ra.
