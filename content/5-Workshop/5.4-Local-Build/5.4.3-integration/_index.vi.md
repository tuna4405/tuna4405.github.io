---
title : "Tích hợp"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

1. **Trỏ module client sang API cục bộ** (`http://localhost:3000/api/v1`) thay cho
   các file dữ liệu giả, với cả hai lập trình viên ngồi chung một máy - một người
   gõ, một người đọc response trong tab network.

2. **Xử lý những điểm lệch mà dữ liệu giả đã che đi.** Trên thực tế chúng đều nhỏ
   và cụ thể: một định dạng ngày mà bản mock đã đơn giản hóa, một trường có mặt
   trong response thật nhưng bản mock đã bỏ sót, và một trạng thái đang tải mà giao
   diện chưa bao giờ cần hiển thị khi chạy trên dữ liệu giả trả về tức thì. Không
   điều nào trong số đó là bất ngờ về *hình dạng* của API - bản đặc tả đã đóng băng
   giải quyết xong chuyện đó rồi - mà hoàn toàn chỉ là những thực tế nhỏ nhặt của
   một lời gọi mạng thật.

3. **Chạy luồng đặt vé end-to-end**: nạp sơ đồ ghế, chọn ghế, gửi một booking, và
   xác nhận sơ đồ ghế phản ánh đúng thay đổi sau khi làm mới. Sau đó mở đúng sơ đồ
   ghế ấy trong một tab trình duyệt thứ hai và xác nhận một ghế vừa được đặt ở tab
   thứ nhất sẽ hiện là không còn trống sau khi làm mới ở tab thứ hai - một cái nhìn
   đầu tiên, còn phi chính thức, vào cam kết về concurrency vốn sẽ được kiểm thử
   thật sự ở mục 5.9.

<!-- ![Working seat picker against the local API, booked seats greyed out](/images/5-Workshop/5.4-Local-Build/5.4.3-integration/example.png) -->
