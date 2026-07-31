---
title : "Kiểm thử đặt trùng ghế"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.9.1 </b> "
---

Mọi mục khác trong workshop này tồn tại là để phục vụ đúng một bài kiểm thử này.
Khẳng định đang được đem ra kiểm chứng: **một ghế không bao giờ có thể bị bán hai
lần**, kể cả khi hai request cho cùng một ghế đến gần như cùng một khoảnh khắc.

1. **Mở hai phiên trình duyệt tách biệt** (hai trình duyệt khác nhau, hoặc một cửa
   sổ thường và một cửa sổ ẩn danh, đăng nhập bằng hai người dùng khác nhau) trên
   trang đã triển khai, cả hai cùng xem sơ đồ ghế của cùng một suất chiếu.

2. **Chọn cùng một ghế ở cả hai phiên**, rồi gửi cả hai booking sát nhau về thời gian
   nhất có thể trong khả năng bấm tay của hai người - đủ sát để load balancer hoàn
   toàn có thể định tuyến hai request tới hai instance EC2 khác nhau, và đó đúng là
   kịch bản mà row lock phải trụ vững.

3. **Ghi lại cả hai response.** Một request nhận `201 Created` kèm booking đã được
   xác nhận. Request còn lại nhận:

   ```json
   {
     "error": {
       "code": "SEAT_ALREADY_BOOKED",
       "message": "One or more selected seats have just been booked by another user.",
       "conflictingSeatIds": [12]
     }
   }
   ```

4. **Xác nhận sơ đồ ghế sau đó** cho thấy ghế ấy được đặt đúng một lần - không phải
   hai lần, cũng không phải không lần nào - và rằng giao diện của phía thua cuộc làm
   nổi bật đúng chiếc ghế được nêu tên trong `conflictingSeatIds` chứ không hiện ra
   một thông báo lỗi chung chung.

**Vì sao điều này chứng minh cơ chế khóa chứ không phải chỉ chứng minh giao diện.**
Kiểu lỗi đáng quan tâm mà bài kiểm thử này phòng ngừa không phải là "cái nút bấm được
hai lần" - một cái nút bị vô hiệu sau lần bấm đầu tiên sẽ che giấu chuyện đó hoàn
toàn mà chẳng sửa được gì. Nó là cuộc chạy đua ở mức cơ sở dữ liệu: hai giao dịch
`SELECT ... FOR UPDATE` nhắm vào cùng một dòng ghế không thể cùng đi qua được cái
khóa, bất kể truy vấn nào được phát ra từ instance EC2 nào trong hai cái. Giao dịch
của một request commit và cập nhật dòng dữ liệu; giao dịch của request kia, khi được
giải phóng khỏi trạng thái chờ, đọc lại trạng thái vừa được cập nhật ấy bên trong
giao dịch của chính nó rồi rollback gọn gàng kèm response báo xung đột - không có
trạng thái dở dang, không có khách hàng bị tính tiền hai lần, không có ghế nào bị bán
cho hai người.

<!-- ![Side by side: the 201 response and the 409 response with conflictingSeatIds, plus the seat map after](/images/5-Workshop/5.9-Testing/5.9.1-concurrency/example.png) -->
