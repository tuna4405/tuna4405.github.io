---
title: "Nhật ký tuần 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu Tuần 1: Nhập môn và Xác định Phạm vi Dự án

* Làm quen với nhóm First Cloud Journey và nắm rõ chương trình được tổ chức ra sao, có những quy định gì, và phải nộp những sản phẩm nào (deliverables).
* Lập nhóm hai người và chốt một dự án mà khó khăn cốt lõi của nó không thể xử lý chỉ bằng tầng ứng dụng (application layer).
* Chốt cứng danh sách endpoint API và schema cơ sở dữ liệu cùng một lúc, trước khi viết bất kỳ dòng code nào.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Buổi định hướng: giới thiệu chương trình First Cloud Journey, các quy định, các sản phẩm phải nộp, và những gì báo cáo cần có <br> - Lập nhóm hai người và chốt cách chia việc: một nhánh backend, một nhánh frontend, cả hai cùng tiến song song dựa trên một hợp đồng (contract) chung | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Rà soát các ý tưởng dự án và chốt nền tảng đặt vé xem phim theo chỗ ngồi, với lý do việc bán ghế đã giữ chỗ trong điều kiện concurrency đặt ra bài toán về tính toàn vẹn giao dịch (transactional integrity) chứ không đơn thuần là CRUD <br> - Đặt tên dự án là Caerus <br> - Đọc thêm quanh bài toán: một case study về cách SeatGeek, nền tảng bán vé đang chạy thực tế, tổ chức authorization và rate limiting cho nhiều tenant cùng lúc - ở giai đoạn này chỉ mang tính tham khảo, không phải thứ Caerus cần đến, bởi đây là ứng dụng single-tenant và không có yêu cầu rate limiting cho nhiều tenant | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/> |
| 4 | - Phát biểu chính xác bài toán cốt lõi: khi hai khách hàng chọn cùng một ghế tại cùng một thời điểm, tuyệt đối không được để cả hai cùng thành công <br> - Liệt kê trọn vẹn các endpoint API cần có: auth, danh sách và chi tiết sự kiện (event), sơ đồ ghế, đặt vé (booking), hủy vé, tải vé, tạo sự kiện (admin), tải poster (admin) | 17/06/2026 | 17/06/2026 | |
| 5 | - Viết schema cơ sở dữ liệu ngay trong cùng buổi làm việc với danh sách endpoint chứ không làm sau, vì mỗi bên đều ràng buộc bên còn lại: năm bảng - `users`, `events`, `seats`, `bookings`, `booking_seats` <br> - Chốt rằng ghế gắn với một suất chiếu (screening) chứ không gắn với phòng vật lý, nhờ vậy tình trạng còn chỗ (availability) là rõ ràng cho từng suất <br> - Chốt rằng giá vé được lưu bằng số nguyên theo đơn vị đồng Việt Nam, tuyệt đối không dùng số thực (float) | 18/06/2026 | 18/06/2026 | |
| 6 | - Chốt hình dạng request/response và định dạng lỗi chuẩn cho mọi endpoint, cùng với cách khóa dòng (row-locking) dùng cho giao dịch đặt vé (`SELECT ... FOR UPDATE`) <br> - Đóng băng cả hai tài liệu - bản đặc tả API và schema cơ sở dữ liệu - kèm nguyên tắc rằng từ đây trở đi không lập trình viên nào được tự ý sửa | 19/06/2026 | 19/06/2026 | |
| 7 | - Tự học: rà lại các tài liệu đã đóng băng thêm một lượt để tìm chỗ còn thiếu trước khi coi là bản cuối, và đọc trước về IAM để chuẩn bị cho tuần kế tiếp | 20/06/2026 | 20/06/2026 | |

### Kết quả đạt được Tuần 1:

* Lập được nhóm hai người với ranh giới backend/frontend tách bạch, nhờ đó có thể làm song song ngay từ tuần xây dựng đầu tiên.
* Chốt được một dự án mà yêu cầu định danh của nó - không bao giờ có ghế nào bị bán hai lần - nằm ngoài khả năng xử lý của riêng tầng ứng dụng, và giải thích được vì sao.
* Cho ra hai tài liệu hợp đồng (contract) đã đóng băng (bản đặc tả API và schema cơ sở dữ liệu), thống nhất trong cùng một buổi làm việc, trước khi có dòng code nào.
* Đọc một case study thực tế về authorization cho nhiều tenant thuần túy như tài liệu mở rộng, và giải thích được vì sao các cơ chế riêng của nó (tiered usage plans, API key cho từng tenant) không liên quan gì đến một dự án single-tenant như Caerus.
