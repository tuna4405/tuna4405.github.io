---
title : "Đặc tả API"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

Một bản đặc tả API chỉ có ích với vai trò hợp đồng khi cả hai phía đều yên tâm rằng
nó sẽ không xê dịch dưới chân mình. Bản đặc tả của Caerus là một tài liệu Markdown
duy nhất, với một quy tắc đặt ngay đầu trang: không ai được tự ý sửa, và mọi thay
đổi - kể cả những thay đổi diễn ra về sau, sau khi đã triển khai, chẳng hạn việc
chuyển tầng compute phía dưới của một endpoint từ Lambda trở lại EC2 - đều được ghi
vào một bảng change-log kèm ngày tháng và lý do, không bao giờ bị sửa đi một cách
âm thầm. Chính kỷ luật ấy đã cho phép backend và frontend được xây song song ở mục
5.4: frontend viết code dựa trên các cấu trúc trong bản đặc tả với những file JSON
giả lập từ trước khi API thật tồn tại, và ngày tích hợp trở thành chuyện nối hai
nửa vốn đã đúng lại với nhau, chứ không phải chuyện đi khám phá xem phía bên kia đã
thực sự làm ra cái gì.

**Các quy ước, thống nhất một lần và không bàn lại theo từng endpoint:**

- Mọi request body và response body đều là JSON.
- Dấu thời gian được truyền theo chuẩn ISO 8601 ở múi giờ UTC, nhưng mọi giờ chiếu
  mà chúng đại diện đều là một suất chiếu theo `Asia/Ho_Chi_Minh`. Việc lưu trữ và
  định dạng trên đường truyền vẫn giữ UTC; chỉ khâu diễn giải và hiển thị mới đổi
  sang giờ địa phương, và bộ lọc `?date` trên danh sách sự kiện được hiểu là một
  ngày theo lịch Việt Nam, không phải theo UTC.
- Tiền luôn là một số nguyên đơn vị đồng Việt Nam. Không bao giờ là số thực, và
  không bao giờ là chuỗi.
- Xác thực dùng JWT bearer token đặt trong header `Authorization`; token là không
  trong suốt (opaque) đối với client và mang theo id cùng role của người dùng.

**Cấu trúc lỗi chuẩn.** Mọi thất bại, bất kể ở endpoint nào, đều trả về cùng một
lớp vỏ:

```json
{
  "error": {
    "code": "SEAT_ALREADY_BOOKED",
    "message": "One or more selected seats have just been booked by another user.",
    "conflictingSeatIds": [12, 13]
  }
}
```

Client phân nhánh theo `code`, không bao giờ theo `message` - thông điệp là dành
cho con người, còn mã là dành cho chương trình.

| Code | HTTP status | Ý nghĩa |
|---|---|---|
| `VALIDATION_ERROR` | 400 | Request body không qua được bước kiểm tra hợp lệ |
| `UNAUTHORIZED` | 401 | Token thiếu, sai định dạng, hoặc đã hết hạn |
| `FORBIDDEN` | 403 | Đã xác thực, nhưng không được phép với tài nguyên này |
| `NOT_FOUND` | 404 | Tài nguyên không tồn tại, hoặc người gọi không có quyền biết là nó tồn tại |
| `SEAT_ALREADY_BOOKED` | 409 | Một hoặc nhiều ghế được yêu cầu đã bị người khác đặt trước |
| `BOOKING_NOT_CANCELLABLE` | 409 | Booking đã bị hủy, hoặc giờ chiếu đã trôi qua |
| `EMAIL_ALREADY_EXISTS` | 409 | Email đăng ký đã được sử dụng |

**Tóm tắt các endpoint:**

| Method | Path | Auth | Mục đích |
|---|---|---|---|
| POST | `/auth/register` | - | Tạo tài khoản |
| POST | `/auth/login` | - | Lấy JWT |
| GET | `/events` | - | Liệt kê các suất chiếu sắp tới |
| GET | `/events/:id` | - | Chi tiết suất chiếu |
| POST | `/events` | admin | Tạo suất chiếu (tự sinh 60 ghế) |
| POST | `/events/:id/banner` | admin | Tải ảnh poster lên |
| GET | `/events/:id/seats` | - | Sơ đồ ghế |
| POST | `/bookings` | user | Đặt ghế (nguyên tử, tối đa 6) |
| GET | `/bookings` | user | Các vé tôi đã đặt |
| GET | `/bookings/:id` | user | Chi tiết booking |
| DELETE | `/bookings/:id` | user | Hủy booking |
| POST | `/bookings/:id/ticket` | user | Tạo và tải vé PDF |

<!-- ![Excerpt of the frozen api-spec.md, showing the error shape and one endpoint definition](/images/5-Workshop/5.3-Design/5.3.1-api-spec/example.png) -->
