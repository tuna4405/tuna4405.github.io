---
title : "Frontend: React với dữ liệu giả"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

1. **Dựng khung một ứng dụng Vite + React** và xây ba màn hình vốn không cần một
   backend chạy thật mới dùng được: danh sách sự kiện, màn chọn ghế, và danh sách
   vé đã đặt.

2. **Tạo dữ liệu giả có cấu trúc đúng y bản đặc tả API đã đóng băng**, dưới dạng
   các file JSON thuần trong `src/mocks/` - không phải gần giống, mà phải giống
   từng trường một, kể cả cách đặt tên camelCase và object `pagination` lồng bên
   trong danh sách sự kiện. Một bản mock gần-đúng-nhưng-chưa-hẳn sẽ dạy cho
   frontend một bài học sai và đẻ ra một đợt lỗi thứ hai vào ngày tích hợp, những
   lỗi chẳng liên quan gì tới mạng.

3. **Bọc mọi lời gọi API vào một module client duy nhất** (`src/api/client.js`),
   không bao giờ gọi tùy tiện từ một component. Một hàm trợ giúp `request()` duy
   nhất gắn header `Authorization`, phân tích lớp vỏ lỗi chuẩn, và ném ra một
   `ApiError` có kiểu để giao diện phân nhánh theo `code`. Mỗi màn hình gọi một hàm
   có tên rõ ràng (`getEvents()`, `createBooking()`) do module này export - không
   bao giờ gọi thẳng `fetch`.

**Vì sao việc này đáng làm kỹ thay vì làm nhanh:** toàn bộ lợi ích của cách tiếp
cận này rơi vào đúng ngày tích hợp. Việc chuyển mọi màn hình từ dữ liệu giả sang
API chạy thật trở thành một thay đổi trong một file duy nhất - `request()` nói
chuyện với base URL nào - chứ không phải một thay đổi rải khắp mọi component tình
cờ có gọi tới một endpoint. Nhánh frontend ở mục 5.4.3 chưa từng bị chặn để chờ
tiến độ backend, và cũng chưa từng phải viết lại một phần nào khi API thật xuất
hiện.

<!-- ![Event list running against mock data](/images/5-Workshop/5.4-Local-Build/5.4.2-frontend/example.png) -->
