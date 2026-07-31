---
title : "Các trường hợp biên"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.9.2 </b> "
---

Ngoài bài kiểm thử concurrency, đây là một danh sách ngắn các trường hợp mà
đặc tả (mục 5.3.1) đã cam kết rõ ràng và đáng để xác nhận trên hệ thống đã
triển khai thay vì chỉ tin tưởng vào mã nguồn.

| Trường hợp | Kỳ vọng | Thực tế |
|---|---|---|
| Hủy một booking sau khi suất chiếu đã diễn ra | `409 BOOKING_NOT_CANCELLABLE` | *(điền vào từ lần chạy thực tế của bạn)* |
| Hủy một booking đã bị hủy trước đó | `409 BOOKING_NOT_CANCELLABLE` | |
| Đặt hơn sáu ghế trong một yêu cầu | `400 VALIDATION_ERROR` | |
| Đặt vé với JWT hết hạn hoặc sai định dạng | `401 UNAUTHORIZED` | |
| Người dùng không phải admin gọi `POST /events` | `403 FORBIDDEN` | |
| Yêu cầu một screening id không tồn tại | `404 NOT_FOUND` | |
| Tải vé cho một booking đã bị hủy | `404 NOT_FOUND` (có chủ đích - xem bên dưới) | |

**Dòng cuối cùng là một quyết định thiết kế, không phải một sai sót.** Một
booking đã hủy trả về `404` cho `POST /bookings/:id/ticket` thay vì mã lỗi
xung đột `409`, với lý do rằng đối với người gọi, nó nên trông giống như
"không có gì ở đây để tải về", chứ không phải "có một xung đột trạng thái
cần giải quyết" - điều này được ghi rõ ràng trong đặc tả thay vì bị bỏ mặc
như một tác dụng phụ ngầm định của việc code path nào chạy trước.

{{% notice note %}}
Hãy chạy từng dòng trên hệ thống đã triển khai và thay thế "điền vào từ lần
chạy thực tế của bạn" bằng status code thực tế quan sát được cùng với
trường `code` lỗi tương ứng - một bảng còn để trống chỉ là một checklist,
chứ không phải kết quả kiểm thử.
{{% /notice %}}

<!-- ![Ví dụ về request/response thực tế của một trường hợp biên, ví dụ lỗi 401 do token hết hạn](/images/5-Workshop/5.9-Testing/5.9.2-edge-cases/example.png) -->
