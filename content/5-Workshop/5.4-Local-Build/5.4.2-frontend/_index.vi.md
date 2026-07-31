---
title : "Frontend: React với dữ liệu giả"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

1. **Dựng khung một ứng dụng Vite + React** và xây dựng ba màn hình không
   cần backend thật cũng có thể hoạt động: danh sách sự kiện, bộ chọn ghế,
   và danh sách các lượt đặt.

2. **Tạo dữ liệu giả có cấu trúc giống hệt đặc tả API đã đóng băng**, dưới
   dạng các file JSON thuần túy trong `src/mocks/` - không phải giống gần
   đúng, mà giống hoàn toàn từng trường một, bao gồm cả cách đặt tên
   camelCase và object `pagination` lồng bên trong danh sách sự kiện. Một
   dữ liệu giả gần giống nhưng không hoàn toàn giống cấu trúc thật sẽ dạy
   cho frontend một bài học sai và tạo ra một vòng lỗi thứ hai vào ngày
   tích hợp mà chẳng liên quan gì đến network cả.

3. **Bọc mọi lệnh gọi API trong một module client duy nhất**
   (`src/api/client.js`), không bao giờ được gọi tùy tiện từ một
   component. Một hàm hỗ trợ `request()` duy nhất gắn header
   `Authorization`, phân tích cấu trúc lỗi chuẩn, và ném ra một `ApiError`
   có kiểu (typed) mà UI có thể rẽ nhánh theo `code`. Mỗi màn hình gọi một
   hàm có tên (`getEvents()`, `createBooking()`) mà module này export ra -
   không bao giờ gọi `fetch` trực tiếp.

**Vì sao đáng để làm việc này một cách cẩn thận thay vì làm nhanh cho
xong:** toàn bộ lợi ích của cách tiếp cận này đến vào ngày tích hợp. Việc
chuyển mọi màn hình từ dữ liệu giả sang API thật trở thành một thay đổi
trong một file duy nhất - base URL nào mà `request()` giao tiếp - thay vì
một thay đổi rải rác trên mọi component có gọi đến một endpoint. Nhánh
frontend ở mục 5.4.3 chưa bao giờ bị chặn lại để chờ tiến độ của backend,
và nó cũng không bao giờ phải viết lại một phần nào sau khi API thật đã
tồn tại.

<!-- ![Danh sách sự kiện chạy với dữ liệu giả](/images/5-Workshop/5.4-Local-Build/5.4.2-frontend/example.png) -->
