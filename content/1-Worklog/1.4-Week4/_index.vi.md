---
title: "Nhật ký tuần 4"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4: Caerus - Các Endpoint Cốt lõi, Chỉ trên Localhost

* Dựng backend và frontend song song theo đúng hợp đồng (contract) của Tuần 1, trên một cơ sở dữ liệu local chạy trong Docker.
* Cài đặt giao dịch đặt vé kèm row-level locking, yêu cầu kỹ thuật nằm ở trung tâm dự án.
* Đưa được ứng dụng chạy trên localhost, còn các endpoint phụ thuộc AWS thì chủ động để lại.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Tạo GitHub repository và bật branch protection để mọi thay đổi đều đi qua pull request mới được merge <br> - **[Nhánh backend]** Dựng bộ khung dự án Express cùng một container PostgreSQL trong Docker Compose, rồi chạy schema của Tuần 1 dưới dạng migration <br> - **[Nhánh frontend]** Dựng bộ khung dự án Vite và React, đồng thời vẽ ra sơ đồ màn hình | 06/07/2026 | 06/07/2026 | |
| 3 | - **[Nhánh backend]** Cài đặt authentication (JSON Web Tokens, băm mật khẩu bằng bcrypt) cùng các endpoint danh sách và chi tiết sự kiện <br> - **[Nhánh frontend]** Dựng màn hình danh sách sự kiện và chi tiết sự kiện trên nền các file JSON giả lập (mock) có cấu trúc y hệt response API đã thống nhất | 07/07/2026 | 07/07/2026 | |
| 4 | - **[Nhánh backend]** Cài đặt endpoint sơ đồ ghế và giao dịch đặt vé: `SELECT ... FOR UPDATE` trên các dòng ghế được yêu cầu, sắp theo primary key để deadlock không xảy ra, và chỉ commit khi mọi ghế được yêu cầu vẫn còn trống <br> - **[Nhánh frontend]** Dựng màn hình chọn ghế trên dữ liệu mock, bao gồm cả trạng thái ghế đã có người đặt và trạng thái xung đột đặt vé (booking-conflict) | 08/07/2026 | 08/07/2026 | <https://www.postgresql.org/docs/16/explicit-locking.html> |
| 5 | - **[Nhánh backend]** Cài đặt chức năng hủy vé và endpoint danh sách vé đã đặt, kèm quy tắc rằng một booking không thể hủy khi giờ chiếu (showtime) đã trôi qua <br> - **[Nhánh frontend]** Dựng màn hình danh sách vé đã đặt cùng thao tác hủy vé <br> - Chủ động để lại hai endpoint phụ thuộc AWS - tải poster (admin) và tải vé PDF - vì chưa có bucket S3 nào và cả hai thao tác đều chưa có chỗ để ghi dữ liệu vào | 09/07/2026 | 09/07/2026 | |
| 6 | - Tích hợp (integration), hai người cùng ngồi trên một máy: nối frontend với API local đang chạy thật và gỡ những chỗ lệch nhau mà dữ liệu mock đã che đi <br> - Gói mọi lời gọi API vào bên trong một module client duy nhất, nhờ đó việc đổi base URL về sau - từ local sang deployed - chỉ còn là một thay đổi duy nhất | 10/07/2026 | 10/07/2026 | |
| 7 | - Kiểm thử luồng đặt vé end-to-end trên local: xác nhận rằng làm mới sơ đồ ghế sẽ thấy được booking của người dùng khác, và lượt thử thứ hai vào một ghế đã đặt sẽ bị từ chối <br> - **Cột mốc đạt được:** ứng dụng chạy end-to-end trên localhost, chỉ còn hai endpoint tải poster (admin) và tải vé là chưa được cài đặt | 11/07/2026 | 11/07/2026 | |

### Kết quả đạt được Tuần 4:

* Cài đặt giao dịch đặt vé với row-level locking trên một instance PostgreSQL thật, chứ không dừng ở việc thiết kế trên giấy.
* Dựng được một frontend chưa lần nào phải chờ tiến độ của backend, phát triển trên dữ liệu mock đúng theo bản đặc tả đã đóng băng rồi chuyển sang API local thật chỉ trong một bước.
* Đưa được ứng dụng local vào trạng thái chạy được mà vẫn theo dõi rõ hai endpoint nào chưa thể tồn tại, thay vì lặng lẽ tạo stub cho chúng rồi đánh mất lý do phía sau.
* Thiết lập được mẫu (pattern) một module client duy nhất, biến việc trỏ frontend sang API đã triển khai ở Tuần 5 thành một thay đổi nhỏ chứ không phải viết lại.
