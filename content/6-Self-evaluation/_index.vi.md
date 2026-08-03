---
title: "Tự đánh giá"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 6. </b> "
# Vẫn giữ trên website, nhưng không đưa vào báo cáo LaTeX: nội dung này được
# chuyển vào phần Tổng kết của báo cáo (report/TongKet.tex).
includeInReport: false
---

Trong kỳ thực tập tại **Công ty TNHH Amazon Web Services Việt Nam** từ
**15/06/2026** đến **14/08/2026**, thuộc chương trình Workforce Bootcamp - First
Cloud Journey, tôi có cơ hội chuyển từ việc học điện toán đám mây trên lý thuyết
sang thiết kế, xây dựng, và vận hành một hệ thống hoàn chỉnh trên AWS.

Sáu tuần đầu được dành để đi qua các nhóm dịch vụ cốt lõi - quản lý danh tính và
truy cập, mạng, compute, lưu trữ, cơ sở dữ liệu, serverless, và giám sát - mỗi nhóm
đều kèm theo một bài thực hành. Những tuần cuối được dành để áp dụng toàn bộ những
điều đó vào **Caerus**, một nền tảng đặt ghế xem phim do một nhóm hai người xây dựng
và triển khai trên Amazon EC2, Amazon RDS, Amazon S3, API Gateway và Amazon
CloudWatch.

Qua công việc này, tôi đã nâng cao được kỹ năng về kiến trúc đám mây và lựa chọn
dịch vụ, thiết kế cơ sở dữ liệu quan hệ, lập trình giao dịch và kiểm soát
concurrency, thiết kế API, triển khai và cấu hình bảo mật mạng, khả năng quan sát,
quản lý chi phí, và viết tài liệu kỹ thuật. Việc làm việc theo cặp cũng dạy cho tôi
giá trị của việc thống nhất một hợp đồng giao diện trước khi viết code, và đó chính
là điều cho phép hai người cùng xây dựng song song thay vì người này phải chờ người
kia.

Về tinh thần làm việc, tôi bám sát lịch trình đã thống nhất từ đầu dự án, duy trì
những thói quen hằng ngày mà nhóm đã cam kết - terminate tài nguyên thực hành ngay
trong ngày và gắn tag chủ sở hữu cho mọi tài nguyên - và nêu vấn đề với đồng đội từ
sớm thay vì tự mình xoay xở vòng qua chúng.

Để nhìn lại kỳ thực tập một cách khách quan, tôi tự đánh giá theo các tiêu chí sau:

| STT | Tiêu chí | Mô tả | Tốt | Khá | Trung bình |
| --- | --- | --- | --- | --- | --- |
| 1 | **Kiến thức & kỹ năng chuyên môn** | Hiểu biết về lĩnh vực, vận dụng kiến thức vào thực tế, thành thạo công cụ, chất lượng công việc | ✅ | ☐ | ☐ |
| 2 | **Khả năng học hỏi** | Khả năng tiếp thu kiến thức mới và học nhanh | ✅ | ☐ | ☐ |
| 3 | **Tính chủ động** | Chủ động đề xuất, tự tìm việc mà không chờ được giao | ☐ | ✅ | ☐ |
| 4 | **Tinh thần trách nhiệm** | Hoàn thành công việc đúng hạn và bảo đảm chất lượng | ✅ | ☐ | ☐ |
| 5 | **Tính kỷ luật** | Tuân thủ lịch trình, quy định, và quy trình làm việc | ✅ | ☐ | ☐ |
| 6 | **Tinh thần cầu tiến** | Sẵn sàng tiếp nhận góp ý và tự hoàn thiện bản thân | ✅ | ☐ | ☐ |
| 7 | **Giao tiếp** | Trình bày ý tưởng và báo cáo công việc một cách rõ ràng | ☐ | ✅ | ☐ |
| 8 | **Làm việc nhóm** | Phối hợp hiệu quả với đồng nghiệp và tham gia vào tập thể | ✅ | ☐ | ☐ |
| 9 | **Tác phong chuyên nghiệp** | Tôn trọng đồng nghiệp, đối tác, và môi trường làm việc | ✅ | ☐ | ☐ |
| 10 | **Kỹ năng giải quyết vấn đề** | Nhận diện vấn đề, đề xuất giải pháp, và thể hiện sự sáng tạo | ✅ | ☐ | ☐ |
| 11 | **Đóng góp cho dự án/nhóm** | Hiệu quả công việc, ý tưởng đổi mới, sự ghi nhận từ nhóm | ✅ | ☐ | ☐ |
| 12 | **Đánh giá tổng thể** | Đánh giá chung cho toàn bộ kỳ thực tập | ✅ | ☐ | ☐ |

### Những điều đã làm tốt

* **Độ rộng của việc học.** Tôi bắt đầu chương trình mà không có kinh nghiệm thực tế
  nào với AWS và kết thúc nó với khả năng thiết kế một kiến trúc nhiều dịch vụ và
  biện luận cho từng lựa chọn dịch vụ bằng cách đặt nó cạnh một phương án thay thế,
  chứ không phải chọn theo mặc định.

* **Làm việc dựa trên một bản hợp đồng.** Việc thống nhất bản đặc tả API và schema cơ
  sở dữ liệu trước khi viết bất kỳ dòng code nào là quyết định duy nhất giữ cho dự án
  đúng tiến độ. Nó có nghĩa là frontend chưa bao giờ bị chặn để chờ backend, và biến
  khâu tích hợp thành chuyện sửa vài điểm lệch nhỏ thay vì hòa giải hai thiết kế
  không tương thích.

* **Giải đúng bài toán mà dự án thực sự nói về.** Đặt ghế trong điều kiện concurrency
  không phải một bài toán giao diện người dùng hay một bài toán hạ tầng; nó là một
  bài toán giao dịch. Tôi học được cách khóa tường minh những dòng đang bị tranh
  chấp, lấy khóa theo một thứ tự nhất quán để tránh deadlock, rồi chứng minh cam kết
  đó bằng một cuộc chạy đua hai client có chủ đích thay vì mặc định rằng nó đúng.

* **Kỷ luật về chi phí.** Việc đặt một billing alarm trước khi khởi tạo bất cứ thứ
  gì, gắn tag chủ sở hữu cho mọi tài nguyên, và terminate tài nguyên thực hành ngay
  trong ngày đã khiến chi phí không bao giờ trở thành một vấn đề phải xử lý về sau.

### Những điều cần cải thiện

* **Chủ động vượt ra ngoài kế hoạch.** Tôi thực thi lịch trình đã thống nhất một cách
  đáng tin cậy, nhưng lại có xu hướng làm hết danh sách công việc thay vì nhìn xa hơn
  và đề xuất cải tiến trước khi chúng trở thành bắt buộc. Nhiều vấn đề mà nhóm gặp
  trong lúc triển khai vốn có thể lường trước, và lẽ ra tôi nên nêu chúng ra ngay ở
  khâu thiết kế thay vì phát hiện ra khi đang chịu áp lực thời gian.

* **Giao tiếp và thuyết trình.** Tôi thoải mái khi giải thích các quyết định kỹ thuật
  bằng văn bản, nhưng kém tự tin hơn khi trình bày chúng bằng lời trước một người
  chưa đọc tài liệu. Đây là kỹ năng tôi muốn phát triển nhất trong thời gian tới.

* **Chiều sâu vượt ra ngoài con đường chạy được.** Hệ thống chạy được và đã được kiểm
  chứng ở cam kết cốt lõi của nó, nhưng phần kỹ thuật xung quanh thì mỏng hơn tôi
  mong muốn: việc triển khai vẫn làm tay chứ chưa tự động hóa, hạ tầng được tạo qua
  console chứ chưa định nghĩa dưới dạng code, và kiểm thử tự động mới giới hạn ở
  trường hợp concurrency chứ chưa phủ rộng ra toàn bộ API.

* **Kiến trúc ở mức production.** Ứng dụng chạy trên một instance duy nhất trong một
  public subnet. Tôi hiểu một bản triển khai vững vàng hơn sẽ cần những gì - private
  subnet với bastion hoặc truy cập theo phiên, một load balancer, một auto scaling
  group, và một cơ sở dữ liệu multi-AZ - nhưng tôi chưa từng tự tay dựng lên, nên
  hiểu biết của tôi về những mẫu đó vẫn còn ở mức khái niệm chứ chưa phải thực hành.

* **Ước lượng thời gian.** Các ước lượng ban đầu của tôi cho khâu triển khai và cấu
  hình cross-origin đều lạc quan. Những ngày dự phòng cài sẵn trong kế hoạch đã hấp
  thụ được phần vượt tiến độ, nhưng bản thân khả năng ước lượng thì vẫn cần cải
  thiện.
