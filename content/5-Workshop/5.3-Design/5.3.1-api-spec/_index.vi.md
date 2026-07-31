---
title : "Đặc tả API"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

Một đặc tả API chỉ hữu ích như một hợp đồng (contract) nếu cả hai bên đều
có thể tin tưởng rằng nó sẽ không thay đổi bất ngờ dưới chân mình. Đặc tả
của Caerus là một tài liệu Markdown duy nhất với một quy tắc ở đầu tài
liệu: không ai được thay đổi nó một cách đơn phương, và mọi thay đổi - kể
cả những thay đổi được thực hiện sau này, sau khi đã deploy, chẳng hạn như
chuyển compute nền tảng của một endpoint từ Lambda trở về EC2 - đều được
ghi lại trong một bảng change-log kèm ngày tháng và lý do, không bao giờ bị
chỉnh sửa âm thầm. Kỷ luật đó chính là điều cho phép backend và frontend
được xây dựng song song trong mục 5.4: frontend viết code dựa theo cấu trúc
của đặc tả cùng với các file JSON giả (mock) trước khi API thật tồn tại, và
ngày tích hợp (integration day) là để kết nối hai nửa đã đúng sẵn từ trước
chứ không phải để khám phá xem phía bên kia thực sự đã xây dựng những gì.

**Các quy ước, được thống nhất một lần và không xem xét lại theo từng
endpoint:**

- Mọi request body và response body đều là JSON.
- Timestamp được truyền đi dưới dạng ISO 8601 theo UTC, nhưng mỗi giờ chiếu
  mà chúng đại diện là một suất chiếu theo múi giờ `Asia/Ho_Chi_Minh`. Việc
  lưu trữ và định dạng truyền tải (wire format) luôn giữ UTC; chỉ việc diễn
  giải và hiển thị mới chuyển sang giờ địa phương, và filter `?date` trên
  danh sách sự kiện được diễn giải như một ngày theo lịch Việt Nam, không
  phải theo UTC.
- Tiền luôn là một số nguyên tính bằng đồng Việt Nam. Không bao giờ là số
  thực (float), và không bao giờ là chuỗi (string).
- Xác thực (authentication) sử dụng JWT bearer token trong header
  `Authorization`; token là opaque đối với client và mang id cùng role của
  user.

**Cấu trúc lỗi chuẩn.** Mọi thất bại, bất kể endpoint nào, đều trả về cùng
một envelope:

```json
{
  "error": {
    "code": "SEAT_ALREADY_BOOKED",
    "message": "One or more selected seats have just been booked by another user.",
    "conflictingSeatIds": [12, 13]
  }
}
```

Client xử lý rẽ nhánh dựa theo `code`, không bao giờ dựa theo `message` -
message là dành cho con người, code là dành cho chương trình.

| Code | HTTP status | Ý nghĩa |
|---|---|---|
| `VALIDATION_ERROR` | 400 | Request body không vượt qua được validation |
| `UNAUTHORIZED` | 401 | Token bị thiếu, sai định dạng, hoặc đã hết hạn |
| `FORBIDDEN` | 403 | Đã xác thực, nhưng không được phép truy cập tài nguyên này |
| `NOT_FOUND` | 404 | Tài nguyên không tồn tại, hoặc bên gọi không có quyền biết nó tồn tại |
| `SEAT_ALREADY_BOOKED` | 409 | Một hoặc nhiều ghế được yêu cầu đã bị người khác đặt trước |
| `BOOKING_NOT_CANCELLABLE` | 409 | Booking đã bị hủy trước đó, hoặc suất chiếu đã qua |
| `EMAIL_ALREADY_EXISTS` | 409 | Email đăng ký đã được đăng ký trước đó |

**Tóm tắt các endpoint:**

| Method | Path | Auth | Mục đích |
|---|---|---|---|
| POST | `/auth/register` | - | Tạo tài khoản |
| POST | `/auth/login` | - | Lấy JWT |
| GET | `/events` | - | Liệt kê các suất chiếu sắp tới |
| GET | `/events/:id` | - | Chi tiết suất chiếu |
| POST | `/events` | admin | Tạo suất chiếu (tự động sinh 60 ghế) |
| POST | `/events/:id/banner` | admin | Tải lên hình ảnh poster |
| GET | `/events/:id/seats` | - | Sơ đồ ghế |
| POST | `/bookings` | user | Đặt ghế (atomic, tối đa 6 ghế) |
| GET | `/bookings` | user | Các lượt đặt của tôi |
| GET | `/bookings/:id` | user | Chi tiết lượt đặt |
| DELETE | `/bookings/:id` | user | Hủy lượt đặt |
| POST | `/bookings/:id/ticket` | user | Tạo và tải vé PDF |

<!-- ![Trích đoạn của api-spec.md đã đóng băng, thể hiện cấu trúc lỗi và định nghĩa một endpoint](/images/5-Workshop/5.3-Design/5.3.1-api-spec/example.png) -->
