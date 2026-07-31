---
title: "Nhật ký tuần 4"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4: Caerus - Các Endpoint Cốt lõi, Chỉ chạy Local

* Xây dựng backend và frontend song song theo đúng hợp đồng (contract) của Tuần 1, trên một cơ sở dữ liệu local chạy bằng Docker.
* Triển khai giao dịch đặt vé với row-level locking, yêu cầu kỹ thuật cốt lõi của dự án.
* Đạt được một ứng dụng chạy được trên localhost, với các endpoint phụ thuộc AWS được hoãn lại một cách rõ ràng.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Tạo GitHub repository và cấu hình branch protection để mọi thay đổi đều phải merge qua pull request <br> - **[Nhánh backend]** Thiết lập bộ khung dự án Express và một container PostgreSQL trong Docker Compose, chạy schema của Tuần 1 dưới dạng migration <br> - **[Nhánh frontend]** Thiết lập bộ khung dự án Vite và React, vẽ sơ đồ màn hình | 06/07/2026 | 06/07/2026 | |
| 3 | - **[Nhánh backend]** Triển khai authentication (JSON Web Tokens, băm mật khẩu bằng bcrypt) và các endpoint danh sách/chi tiết sự kiện <br> - **[Nhánh frontend]** Xây dựng màn hình danh sách sự kiện và chi tiết sự kiện dựa trên các file JSON giả lập (mock) có cấu trúc giống hệt response API đã thống nhất | 07/07/2026 | 07/07/2026 | |
| 4 | - **[Nhánh backend]** Triển khai endpoint sơ đồ ghế và giao dịch đặt vé: `SELECT ... FOR UPDATE` trên các dòng ghế được yêu cầu, sắp xếp theo primary key để tránh deadlock, chỉ commit khi mọi ghế được yêu cầu vẫn còn trống <br> - **[Nhánh frontend]** Xây dựng màn hình chọn ghế dựa trên dữ liệu mock, bao gồm các trạng thái ghế đã có người đặt và xung đột đặt vé (booking-conflict) | 08/07/2026 | 08/07/2026 | <https://www.postgresql.org/docs/16/explicit-locking.html> |
| 5 | - **[Nhánh backend]** Triển khai hủy vé và endpoint danh sách vé đã đặt, với quy tắc một booking không thể bị hủy sau giờ chiếu (showtime) <br> - **[Nhánh frontend]** Xây dựng màn hình danh sách vé đã đặt và thao tác hủy vé <br> - Chủ động hoãn lại hai endpoint cần AWS: tải poster (admin) và tải vé PDF, vì chưa có bucket S3 nào tồn tại và không có nơi nào để hai thao tác này ghi dữ liệu vào | 09/07/2026 | 09/07/2026 | |
| 6 | - Tích hợp (integration), làm việc cùng nhau trên một máy: kết nối frontend với API local đang chạy thật và xử lý các điểm không khớp mà dữ liệu mock đã che giấu <br> - Bọc mọi lời gọi API vào một module client duy nhất, để việc đổi base URL sau này (từ local sang deployed) chỉ là một thay đổi duy nhất | 10/07/2026 | 10/07/2026 | |
| 7 | - Kiểm thử luồng đặt vé end-to-end trên local: xác nhận việc làm mới sơ đồ ghế phản ánh đúng booking của người dùng khác, và một lượt thử thứ hai trên ghế đã được đặt sẽ bị từ chối <br> - **Cột mốc đạt được:** ứng dụng chạy end-to-end trên localhost, chỉ còn hai endpoint tải poster (admin) và tải vé chưa được triển khai | 11/07/2026 | 11/07/2026 | |

### Kết quả đạt được Tuần 4:

* Triển khai giao dịch đặt vé với row-level locking trên một instance PostgreSQL thật, chứ không chỉ thiết kế trên giấy.
* Xây dựng được một frontend chưa từng bị chặn tiến độ bởi backend, phát triển dựa trên dữ liệu mock có cấu trúc đúng theo bản đặc tả đã đóng băng, sau đó chuyển sang dùng API local thật chỉ trong một bước.
* Đạt được một ứng dụng local chạy được trong khi vẫn xác định đúng hai endpoint nào chưa thể tồn tại, thay vì âm thầm tạo stub cho chúng rồi quên mất lý do.
* Thiết lập được mẫu (pattern) module client duy nhất, giúp việc trỏ frontend sang API đã triển khai ở Tuần 5 chỉ là một thay đổi nhỏ chứ không phải viết lại.
