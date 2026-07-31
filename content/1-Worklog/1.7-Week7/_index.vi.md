---
title: "Nhật ký tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7: Caerus - Xác minh, Báo cáo, và Nộp bài

* Chứng minh cam kết không-bao-giờ-đặt-trùng-ghế (no-double-booking) trên hệ thống đã triển khai chứ không chỉ khẳng định miệng.
* Đối chiếu các trường hợp biên (edge case) đã ghi trong tài liệu với môi trường thật.
* Ráp lại bản báo cáo cuối cùng và nộp.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Bài kiểm thử concurrency: hai phiên trình duyệt đăng nhập bằng hai người dùng khác nhau, cùng chọn một ghế trên cùng một suất chiếu và bấm gửi sát nhau nhất có thể <br> - Xác nhận một request trả về `201 Created`, request kia trả về `409 SEAT_ALREADY_BOOKED` có nêu đích danh ghế đang tranh chấp, và sau đó sơ đồ ghế cho thấy ghế ấy được đặt đúng một lần | 27/07/2026 | 27/07/2026 | |
| 3 | - Kiểm thử các trường hợp biên trên hệ thống đã triển khai: hủy vé sau giờ chiếu, hủy một booking vốn đã bị hủy, đặt quá sáu ghế, token hết hạn hoặc sai định dạng, một người không phải admin gọi vào route admin, và tải vé cho một booking đã hủy <br> - Ghi lại status code và error code quan sát được trên thực tế ở từng dòng rồi đối chiếu với bản đặc tả, thay vì để bảng dừng ở dạng checklist | 28/07/2026 | 28/07/2026 | |
| 4 | - Ráp cấu trúc báo cáo và bắt đầu viết, dựa trên bản đặc tả đã đóng băng, nhật ký công việc, và kiến trúc đúng như nó đã được dựng nên <br> - Quay lại từng console AWS mà dự án từng chạm tới và chụp lại những ảnh màn hình mà báo cáo tham chiếu đến | 29/07/2026 | 29/07/2026 | |
| 5 | - **Đăng Blog 1** (AWS Budgets và Cost Anomaly Detection, ra từ phần tự học Tuần 2) và **Blog 3** (AWS Config và Conformance Packs, ra từ phần tự học Tuần 3) lên cộng đồng AWS Study Group <br> - Viết xong nội dung báo cáo, kể cả phần chi phí, vốn phản ánh mức chi tiêu hàng tháng thật của kiến trúc chứ không phải một ước tính theo Free Tier không còn đúng nữa | 30/07/2026 | 30/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2229088634522763/> |
| 6 | - Rà soát lần cuối, đặt báo cáo cạnh hệ thống đã triển khai và sửa vài chỗ mà hai bên đã lệch nhau qua các thay đổi trong tuần <br> - Quay một video demo làm phương án dự phòng phòng khi có sự cố lúc trình bày trực tiếp <br> - **Cột mốc đạt được:** đã nộp báo cáo | 31/07/2026 | 31/07/2026 | |

### Kết quả đạt được Tuần 7:

* Chứng minh được khẳng định cốt lõi của dự án - một ghế không bao giờ có thể bị bán hai lần - trên chính hệ thống thật đã triển khai, với cả request thành công lẫn request bị từ chối đều được lưu lại làm bằng chứng.
* Đối chiếu mọi trường hợp biên đã ghi trong tài liệu với môi trường đã triển khai thay vì tin vào bản đặc tả là đủ.
* Đăng nốt hai bài blog còn lại của kỳ thực tập, khép lại phần tài liệu vốn khởi đầu từ những buổi tự học ở Tuần 2 và Tuần 3.
* Nộp một bản báo cáo mà các phần kiến trúc, chi phí và kiểm thử đều mô tả hệ thống đúng như nó thực sự được dựng và vận hành, chứ không như bản kế hoạch bảy tuần trước đó.
