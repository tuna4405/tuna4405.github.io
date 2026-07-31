---
title : "Tích hợp"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

1. **Trỏ module client về API cục bộ** (`http://localhost:3000/api/v1`)
   thay vì các file dữ liệu giả, với cả hai developer cùng ngồi một máy -
   một người thao tác, một người đọc response trong tab network.

2. **Xử lý các điểm lệch mà dữ liệu giả đã che giấu.** Trên thực tế những
   điểm này nhỏ và cụ thể: một định dạng ngày mà dữ liệu giả đã đơn giản
   hóa, một trường có mặt trong response thật mà dữ liệu giả đã bỏ sót, và
   một trạng thái loading mà UI chưa bao giờ cần hiển thị khi chạy với dữ
   liệu giả tức thì. Không có điều nào trong số này là một bất ngờ về
   *cấu trúc* của API - đặc tả đã đóng băng từ trước đã giải quyết xong
   điều đó - mà hoàn toàn là về những thực tế nhỏ nhặt của một lệnh gọi
   network thật.

3. **Chạy luồng đặt vé từ đầu đến cuối (end to end)**: tải sơ đồ ghế, chọn
   ghế, submit một lượt đặt, và xác nhận sơ đồ ghế phản ánh đúng thay đổi
   khi refresh. Sau đó mở cùng sơ đồ ghế đó trong một tab trình duyệt thứ
   hai và xác nhận một ghế đã được đặt ở tab đầu tiên hiển thị là không
   còn trống sau khi refresh ở tab thứ hai - một cái nhìn đầu tiên, không
   chính thức, về đảm bảo concurrency mà sẽ được kiểm thử thật sự ở mục
   5.10.

{{% notice note %}}
Đây là điểm cuối cùng trong dự án mà "nó chạy được" có thể được kiểm tra
trong vài giây trên localhost. Từ mục 5.5 trở đi, mọi bước xác minh đều
liên quan đến ít nhất một lần gọi mạng đến AWS, nên đáng để thực sự tự tin
vào cột mốc này trước khi tiếp tục.
{{% /notice %}}

<!-- ![Bộ chọn ghế hoạt động với API cục bộ, các ghế đã đặt hiển thị màu xám](/images/5-Workshop/5.4-Local-Build/5.4.3-integration/example.png) -->
