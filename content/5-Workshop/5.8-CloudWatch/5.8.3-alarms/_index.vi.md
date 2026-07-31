---
title : "Cảnh báo và thông báo"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.8.3 </b> "
---

1. **Tạo một SNS topic**, `caerus-alerts`, loại Standard, với một subscription email
   cho mỗi thành viên nhóm - từng người phải tự bấm vào liên kết xác nhận mà AWS gửi
   qua email, nếu không SNS sẽ âm thầm không bao giờ gửi tới địa chỉ đó.

2. **Ba alarm, tất cả đều trỏ tới `caerus-alerts`:**

   | Alarm | Metric | Điều kiện |
   |---|---|---|
   | `caerus-alb-unhealthy-host` | `UnHealthyHostCount` (`caerus-tg`) | ≥ 1 ở 2 trong 3 điểm dữ liệu (1 phút) |
   | `caerus-rds-low-storage` | `FreeStorageSpace` (`caerus-db`) | < 2 GB (5 phút) |
   | `caerus-rds-high-cpu` | `CPUUtilization` (`caerus-db`) | > 80% ở 2 trong 3 điểm dữ liệu (5 phút) |

   Cái đầu tiên thay cho thứ mà nếu không thì sẽ là một alarm status check của EC2
   theo từng instance: với hai instance đứng sau một load balancer, "target group có
   khỏe không" mới là tín hiệu thực sự quan trọng, bởi một instance hỏng không còn tự
   nó làm sập ứng dụng nữa.

3. **Chứng minh ít nhất một alarm thực sự báo động và gửi thông báo**, thay vì để cả
   ba nằm im ở trạng thái OK, vốn chỉ chứng minh alarm đã được tạo ra chứ không chứng
   minh nó hoạt động. Dừng một instance EC2 trong chốc lát là đủ để đưa
   `caerus-alb-unhealthy-host` sang trạng thái `ALARM` và kích hoạt email.

{{% notice warning %}}
Một alarm mà từ trước tới nay chỉ được nhìn thấy ở trạng thái OK là alarm chưa được
kiểm chứng, không phải alarm đang hoạt động - trạng thái OK cũng chính là bộ mặt của
một alarm bị cấu hình sai hoàn toàn. Hãy chụp lại cả trạng thái `ALARM` của nó trong
console lẫn email nhận được làm bằng chứng; chỉ một trong hai thứ là một lời khẳng
định yếu hơn hẳn so với cả hai gộp lại.
{{% /notice %}}

<!-- ![Alarm in ALARM state in the console, alongside the SNS notification email](/images/5-Workshop/5.8-CloudWatch/5.8.3-alarms/example.png) -->
