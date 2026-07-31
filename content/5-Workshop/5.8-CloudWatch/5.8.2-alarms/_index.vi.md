---
title : "Cảnh báo và thông báo"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---

1. **Tạo một SNS topic**, `caerus-alerts`, loại Standard, với một email
   subscription cho mỗi thành viên trong nhóm - mỗi người phải tự mình
   click vào link xác nhận mà AWS gửi qua email, nếu không SNS sẽ âm thầm
   không bao giờ gửi thông báo đến địa chỉ đó.

2. **Ba alarm, tất cả đều trỏ tới `caerus-alerts`:**

   | Alarm | Metric | Điều kiện |
   |---|---|---|
   | `caerus-alb-unhealthy-host` | `UnHealthyHostCount` (`caerus-tg`) | ≥ 1 trong 2 trên 3 datapoint (1 phút) |
   | `caerus-rds-low-storage` | `FreeStorageSpace` (`caerus-db`) | < 2 GB (5 phút) |
   | `caerus-rds-high-cpu` | `CPUUtilization` (`caerus-db`) | > 80% trong 2 trên 3 datapoint (5 phút) |

   Alarm đầu tiên thay thế cho những gì lẽ ra sẽ là một alarm status check
   riêng cho từng EC2 instance: với hai instance đứng sau một load
   balancer, tín hiệu thực sự quan trọng là "target group có khỏe mạnh hay
   không", vì một instance gặp sự cố không còn tự nó làm sập ứng dụng nữa.

3. **Chứng minh ít nhất một alarm thực sự kích hoạt và gửi thông báo**,
   thay vì để cả ba alarm nằm im ở trạng thái OK, điều đó chỉ chứng minh
   rằng alarm đã được tạo ra, chứ không chứng minh rằng nó hoạt động. Chỉ
   cần dừng một EC2 instance trong thời gian ngắn là đủ để đưa
   `caerus-alb-unhealthy-host` vào trạng thái `ALARM` và kích hoạt email.

{{% notice warning %}}
Một alarm chỉ từng được quan sát ở trạng thái OK là chưa được kiểm chứng,
chứ không phải là đang hoạt động - trạng thái OK cũng chính là biểu hiện của
một alarm bị cấu hình sai hoàn toàn. Hãy chụp lại cả trạng thái `ALARM` của
alarm trên console lẫn email thông báo kết quả làm bằng chứng; chỉ có một
trong hai là bằng chứng yếu hơn so với có cả hai.
{{% /notice %}}

<!-- ![Alarm ở trạng thái ALARM trên console, cùng với email thông báo từ SNS](/images/5-Workshop/5.8-CloudWatch/5.8.2-alarms/example.png) -->
