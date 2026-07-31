---
title: "Nhật ký tuần 5"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu Tuần 5: Caerus - Triển khai lên AWS

* Chuyển cơ sở dữ liệu sang Amazon RDS và trang tĩnh sang Amazon S3, triển khai API lên Amazon EC2.
* Đạt được một ứng dụng có thể truy cập công khai, sau đó loại bỏ điểm lỗi đơn (single point of failure) ở cả tầng compute lẫn tầng cơ sở dữ liệu.
* Kết thúc tuần với hai instance EC2 đứng sau một load balancer và một cơ sở dữ liệu Multi-AZ trong private subnet.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Nhánh backend]** Khởi chạy một instance RDS PostgreSQL Single-AZ và chạy các file migration, seed trên đó, chỉ thay đổi connection string <br> - **[Nhánh frontend]** Tạo cả bốn bucket S3 (trang frontend, poster sự kiện, vé được tạo ra, gói triển khai backend) | 13/07/2026 | 13/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3 | - **[Nhánh backend]** Tạo instance role cho EC2 giới hạn phạm vi (scoped) đúng các bucket S3 cần dùng, theo đúng quy ước đặt tên của tài khoản <br> - **[Nhánh frontend]** Bật static website hosting trên bucket trang web <br> - Cùng nhau: xem xét và siết chặt lại các security group để cơ sở dữ liệu chỉ chấp nhận traffic từ security group của instance ứng dụng, không phải từ các địa chỉ bất kỳ | 14/07/2026 | 14/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - **[Nhánh backend]** Khởi chạy instance EC2, cài Node.js, và triển khai API dưới `pm2` <br> - **[Nhánh frontend]** Build bản production của React, publish lên bucket trang web, và trỏ nó tới địa chỉ public của API trên EC2 <br> - Xử lý lỗi cross-origin đã lường trước bằng cách cấu hình các origin được phép trên API thay vì tìm cách lách ở phía trình duyệt <br> - **Cột mốc đạt được:** ứng dụng chạy thật (live) trên AWS, trên một instance EC2 duy nhất | 15/07/2026 | 15/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 5 | - Một instance EC2 duy nhất là một điểm lỗi đơn (single point of failure): khởi chạy thêm một instance thứ hai ở một Availability Zone khác, cùng AMI và cấu hình <br> - Tạo một target group và một Application Load Balancer (ALB) đứng trước cả hai instance, kiểm thử traffic luân phiên giữa chúng trước khi đụng vào bất kỳ thứ gì khác <br> - Chỉ sau khi xác nhận load balancer hoạt động tốt, mới siết chặt security group của EC2 để chỉ chấp nhận traffic từ security group của load balancer, và trỏ frontend sang DNS name của load balancer | 16/07/2026 | 16/07/2026 | |
| 6 | - Chuyển RDS sang Multi-AZ bên trong một private subnet không có route ra internet: tạo hai private subnet và một route table không có route `0.0.0.0/0`, cùng một DB subnet group mới <br> - Gặp phải một lỗi validation đã biết của RDS console/CLI khi chuyển subnet group của một instance hiện có sang subnet group khác trong cùng VPC; xử lý bằng cách xóa instance (vốn chỉ chứa dữ liệu seed) và tạo lại trực tiếp với Multi-AZ và private subnet group được chọn ngay từ lúc khởi tạo <br> - Phát hiện và sửa lại loại storage mặc định của template Production (Provisioned IOPS SSD, 100 GiB) về General Purpose SSD 20 GiB trước khi tạo - nếu để nguyên, đây sẽ là dòng chi phí lớn nhất trên toàn bộ hóa đơn | 17/07/2026 | 17/07/2026 | |
| 7 | - Buffer: xác nhận hostname của endpoint không đổi sau khi tạo lại RDS, nên `DATABASE_URL` không cần thay đổi, và xác nhận cơ sở dữ liệu vẫn có thể truy cập được từ EC2 dù hoàn toàn không có route ra internet <br> - Chạy lại luồng đặt vé end-to-end trên môi trường vừa dựng lại để xác nhận không có gì bị lỗi ngược (regression) | 18/07/2026 | 18/07/2026 | |

### Kết quả đạt được Tuần 5:

* Di chuyển từ cơ sở dữ liệu Docker cục bộ sang RDS được quản lý, và từ filesystem cục bộ sang S3, chỉ bằng cách thay đổi cấu hình chứ không thay đổi code.
* Đạt được một bản triển khai có thể truy cập công khai, sau đó chủ động loại bỏ hai điểm lỗi đơn của nó: một instance EC2 thứ hai đứng sau load balancer, và một cơ sở dữ liệu Multi-AZ.
* Chẩn đoán và tìm cách vượt qua một hạn chế thật sự của RDS console thay vì bị chặn lại, chọn con đường nhanh hơn nhưng vẫn hợp lý vì cơ sở dữ liệu lúc đó chỉ chứa dữ liệu seed có thể bỏ đi.
* Phát hiện một cấu hình mặc định của console (Provisioned IOPS storage) mà nếu để nguyên sẽ chiếm phần lớn nhất trong hóa đơn cả tháng, trước khi nó kịp được tạo ra.
