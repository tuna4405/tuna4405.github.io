---
title : "Kiểm thử đặt trùng ghế"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.9.1 </b> "
---

Mọi phần khác trong workshop này đều tồn tại để phục vụ cho bài kiểm thử
duy nhất này. Điều đang được kiểm chứng: **một ghế không bao giờ được bán
hai lần**, ngay cả khi hai yêu cầu đặt cùng một ghế đến gần như cùng một
thời điểm.

1. **Mở hai phiên trình duyệt riêng biệt** (hai trình duyệt khác nhau, hoặc
   một cửa sổ bình thường và một cửa sổ private/incognito, đăng nhập bằng
   hai người dùng khác nhau) trên trang web đã triển khai, cả hai cùng xem
   sơ đồ ghế của cùng một suất chiếu (screening).

2. **Chọn cùng một ghế ở cả hai phiên**, và gửi cả hai yêu cầu đặt vé gần
   nhau về thời gian nhất có thể mà hai người thao tác click thủ công có
   thể làm được - đủ gần để load balancer hoàn toàn có thể định tuyến hai
   yêu cầu này đến hai EC2 instance khác nhau, đây chính xác là kịch bản mà
   row lock phải đảm bảo giữ vững.

3. **Ghi lại cả hai phản hồi.** Một yêu cầu nhận được `201 Created` cùng
   với booking đã được xác nhận. Yêu cầu còn lại nhận được:

   ```json
   {
     "error": {
       "code": "SEAT_ALREADY_BOOKED",
       "message": "One or more selected seats have just been booked by another user.",
       "conflictingSeatIds": [12]
     }
   }
   ```

4. **Xác nhận rằng sau đó sơ đồ ghế** hiển thị ghế đó đã được đặt đúng một
   lần - không phải hai lần, cũng không phải không lần nào - và giao diện
   của client thua cuộc phải highlight đúng ghế được nêu trong
   `conflictingSeatIds`, chứ không chỉ hiển thị một lỗi chung chung.

**Vì sao điều này chứng minh được row lock, chứ không chỉ là giao diện.**
Kiểu lỗi đáng quan tâm mà việc này phòng chống không phải là "nút bấm có
thể click được hai lần" - một nút bị disable sau một lần click sẽ che giấu
hoàn toàn vấn đề này mà không thực sự khắc phục được gì. Đây là race
condition ở cấp độ database: hai transaction `SELECT ... FOR UPDATE` cùng
nhắm vào một dòng ghế không thể cùng vượt qua khóa (lock), bất kể truy vấn
nào trong số đó được phát ra từ EC2 instance nào. Transaction của một yêu
cầu commit và cập nhật dòng dữ liệu; transaction của yêu cầu còn lại, sau
khi được mở khóa, đọc lại trạng thái vừa được cập nhật ngay trong chính
transaction của nó và rollback một cách sạch sẽ cùng với phản hồi xung đột
- không có trạng thái dở dang, không khách hàng nào bị tính phí hai lần,
không ghế nào bị bán cho hai người.

<!-- ![So sánh song song: phản hồi 201 và phản hồi 409 kèm conflictingSeatIds, cùng sơ đồ ghế sau đó](/images/5-Workshop/5.9-Testing/5.9.1-concurrency/example.png) -->
