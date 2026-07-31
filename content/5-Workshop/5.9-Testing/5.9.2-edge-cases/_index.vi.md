---
title : "Các trường hợp biên"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.9.2 </b> "
---

Ngoài bài kiểm thử concurrency, đây là một danh sách ngắn các trường hợp mà bản đặc
tả (mục 5.3.1) cam kết một cách rõ ràng và đáng được xác nhận trên hệ thống đã triển
khai thay vì chỉ tin vào code.

| Trường hợp | Kỳ vọng | Thực tế |
|---|---|---|
| Hủy một booking sau khi giờ chiếu đã trôi qua | `409 BOOKING_NOT_CANCELLABLE` | *(điền vào từ lần chạy của bạn)* |
| Hủy một booking vốn đã bị hủy | `409 BOOKING_NOT_CANCELLABLE` | |
| Đặt hơn sáu ghế trong một request | `400 VALIDATION_ERROR` | |
| Đặt vé với JWT đã hết hạn hoặc sai định dạng | `401 UNAUTHORIZED` | |
| Người dùng không phải admin gọi `POST /events` | `403 FORBIDDEN` | |
| Yêu cầu một id suất chiếu không tồn tại | `404 NOT_FOUND` | |
| Tải vé cho một booking đã bị hủy | `404 NOT_FOUND` (có chủ đích - xem bên dưới) | |

**Dòng cuối cùng là một quyết định thiết kế, không phải một sơ suất.** Một booking đã
bị hủy trả về `404` cho `POST /bookings/:id/ticket` thay vì một mã xung đột `409`,
với lập luận rằng đối với người gọi thì nó nên trông giống "ở đây không có gì để tải
xuống cả", chứ không phải "có một xung đột trạng thái cần giải quyết" - điều này được
ghi rõ trong bản đặc tả chứ không để mặc như một hệ quả ngầm của việc nhánh code nào
tình cờ chạy trước.

{{% notice note %}}
Hãy chạy từng dòng trên hệ thống đã triển khai và thay "điền vào từ lần chạy của bạn"
bằng status code cùng trường `code` của lỗi thực tế quan sát được - một bảng còn để
trống là một checklist, không phải một kết quả kiểm thử.
{{% /notice %}}

<!-- ![Example of one edge case's actual request/response, e.g. the expired-token 401](/images/5-Workshop/5.9-Testing/5.9.2-edge-cases/example.png) -->
