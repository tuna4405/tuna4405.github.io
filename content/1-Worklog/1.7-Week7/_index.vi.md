---
title: "Nhật ký tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7: Caerus - Kiểm thử, Báo cáo, và Nộp bài

* Chứng minh cam kết không-bao-giờ-đặt-trùng-ghế (no-double-booking) trên chính hệ thống đã triển khai, chứ không chỉ khẳng định suông.
* Xác minh các trường hợp biên (edge case) đã ghi trong tài liệu trên môi trường thật.
* Tổng hợp và nộp báo cáo cuối cùng.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Bài kiểm thử concurrency: hai phiên trình duyệt, đăng nhập với hai người dùng khác nhau, cùng chọn một ghế trên cùng một suất chiếu và gửi request gần như đồng thời <br> - Xác nhận một request nhận được `201 Created` và request còn lại nhận `409 SEAT_ALREADY_BOOKED` nêu rõ ghế đang tranh chấp, và sau đó sơ đồ ghế cho thấy ghế đó chỉ được đặt đúng một lần | 27/07/2026 | 27/07/2026 | |
| 3 | - Kiểm thử các trường hợp biên trên hệ thống đã triển khai: hủy vé sau giờ chiếu, hủy một booking đã bị hủy trước đó, đặt hơn sáu ghế, token hết hạn hoặc sai định dạng, một người dùng không phải admin gọi route admin, và tải vé cho một booking đã bị hủy <br> - Ghi lại status code và error code thực tế quan sát được cho từng dòng, đối chiếu với bản đặc tả, thay vì để bảng chỉ dừng lại ở dạng checklist | 28/07/2026 | 28/07/2026 | |
| 4 | - Xây dựng cấu trúc báo cáo và bắt đầu viết, dựa trên bản đặc tả đã đóng băng, nhật ký công việc, và kiến trúc thực tế đã xây dựng <br> - Rà soát lại mọi console AWS đã dùng trong suốt dự án và chụp lại các ảnh chụp màn hình mà báo cáo tham chiếu tới | 29/07/2026 | 29/07/2026 | |
| 5 | - **Đăng Blog 1** (AWS Budgets và Cost Anomaly Detection, từ phần tự học ở Tuần 2) và **Blog 3** (AWS Config và Conformance Packs, từ phần tự học ở Tuần 3) lên cộng đồng AWS Study Group <br> - Hoàn tất nội dung báo cáo, bao gồm cả phần chi phí phản ánh đúng mức chi tiêu hàng tháng thực tế của kiến trúc thay vì một ước tính theo Free Tier không còn đúng nữa | 30/07/2026 | 30/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2229088634522763/> |
| 6 | - Rà soát cuối cùng báo cáo cùng với hệ thống đã triển khai, sửa lại vài chỗ mà hai bên đã lệch nhau do các thay đổi trong tuần <br> - Ghi lại một video demo làm phương án dự phòng trong trường hợp gặp sự cố khi trình bày trực tiếp <br> - **Cột mốc đạt được:** đã nộp báo cáo | 31/07/2026 | 31/07/2026 | |

### Kết quả đạt được Tuần 7:

* Chứng minh được cam kết cốt lõi của dự án - một ghế không bao giờ có thể bị bán hai lần - trên chính hệ thống thật đã triển khai, với cả request thành công lẫn request bị từ chối đều được ghi lại làm bằng chứng.
* Xác minh mọi trường hợp biên đã ghi trong tài liệu trên môi trường đã triển khai, thay vì chỉ tin tưởng vào bản đặc tả.
* Đăng nốt hai bài blog còn lại của kỳ thực tập, khép lại phần tài liệu đã bắt đầu tự học từ Tuần 2 và Tuần 3.
* Nộp một báo cáo mà các phần kiến trúc, chi phí, và kiểm thử đều mô tả đúng hệ thống như nó thực sự được xây dựng và vận hành, chứ không phải như kế hoạch ban đầu từ bảy tuần trước.
