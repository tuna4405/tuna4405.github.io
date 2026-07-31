---
title: "Nhật ký tuần 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu Tuần 1: Định hướng và Xác định Dự án

* Gặp gỡ nhóm First Cloud Journey và hiểu cấu trúc chương trình, quy định, cũng như các sản phẩm cần bàn giao (deliverables).
* Thành lập nhóm hai người và chọn một dự án mà vấn đề cốt lõi của nó không thể giải quyết chỉ bằng tầng ứng dụng (application layer).
* "Đóng băng" (freeze) danh sách endpoint API và schema cơ sở dữ liệu cùng lúc, trước khi viết bất kỳ dòng code nào.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Buổi định hướng: giới thiệu về chương trình First Cloud Journey, quy định, các sản phẩm cần bàn giao, và yêu cầu báo cáo <br> - Thành lập nhóm hai người và thống nhất cách phân chia công việc: một nhánh backend, một nhánh frontend, cả hai phát triển song song dựa trên một hợp đồng (contract) chung | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Xem xét các ý tưởng dự án và chọn nền tảng đặt vé xem phim theo chỗ ngồi, vì việc bán các ghế đã đặt trước dưới điều kiện concurrency đòi hỏi tính toàn vẹn giao dịch (transactional integrity) chứ không đơn thuần là CRUD <br> - Đặt tên dự án là Caerus <br> - Đọc thêm về không gian vấn đề: một case study về cách SeatGeek, một nền tảng bán vé thực tế, tổ chức authorization và rate limiting cho nhiều tenant cùng lúc - chỉ là tài liệu đọc thêm ở giai đoạn này, không phải thứ Caerus cần, vì đây là ứng dụng single-tenant, không có yêu cầu rate limiting đa tenant | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/> |
| 4 | - Xác định chính xác vấn đề cốt lõi: hai khách hàng chọn cùng một ghế tại cùng một thời điểm không bao giờ được phép cả hai đều thành công <br> - Soạn thảo đầy đủ danh sách các endpoint API cần thiết: auth, danh sách và chi tiết sự kiện (event), sơ đồ ghế, đặt vé (booking), hủy vé, tải vé, tạo sự kiện (admin), tải poster (admin) | 17/06/2026 | 17/06/2026 | |
| 5 | - Soạn thảo schema cơ sở dữ liệu song song với danh sách endpoint, trong cùng một buổi làm việc thay vì làm sau, vì hai thứ này phụ thuộc lẫn nhau: năm bảng - `users`, `events`, `seats`, `bookings`, `booking_seats` <br> - Quyết định ghế thuộc về một suất chiếu (screening) chứ không thuộc về một phòng vật lý, để tình trạng còn chỗ (availability) rõ ràng theo từng suất <br> - Quyết định giá vé được lưu dưới dạng số nguyên bằng đơn vị đồng Việt Nam, không bao giờ dùng số thực (float) | 18/06/2026 | 18/06/2026 | |
| 6 | - Thống nhất hình dạng request/response và định dạng lỗi chuẩn cho mọi endpoint, cùng cách tiếp cận khóa dòng (row-locking) cho giao dịch đặt vé (`SELECT ... FOR UPDATE`) <br> - "Đóng băng" cả hai tài liệu - bản đặc tả API và schema cơ sở dữ liệu - theo nguyên tắc từ thời điểm này không ai được tự ý thay đổi | 19/06/2026 | 19/06/2026 | |
| 7 | - Tự học: xem lại các tài liệu đã đóng băng thêm một lần nữa để tìm lỗ hổng trước khi coi chúng là bản cuối, và đọc trước về IAM để chuẩn bị cho tuần sau | 20/06/2026 | 20/06/2026 | |

### Kết quả đạt được Tuần 1:

* Thành lập nhóm hai người với sự phân chia backend/frontend rõ ràng, cho phép làm việc song song ngay từ tuần đầu tiên xây dựng.
* Chọn được một dự án mà yêu cầu cốt lõi của nó - không bao giờ có ghế nào bị bán hai lần - không thể đáp ứng chỉ bằng tầng ứng dụng, và có thể giải thích được vì sao.
* Tạo ra hai tài liệu hợp đồng (contract) đã đóng băng (bản đặc tả API và schema cơ sở dữ liệu) được thống nhất trong cùng một buổi làm việc, trước khi viết một dòng code nào.
* Đọc một case study thực tế về authorization đa tenant thuần túy như tài liệu tham khảo mở rộng, và có thể giải thích vì sao các cơ chế cụ thể của nó (tiered usage plans, API key theo từng tenant) không áp dụng cho một dự án single-tenant như Caerus.
