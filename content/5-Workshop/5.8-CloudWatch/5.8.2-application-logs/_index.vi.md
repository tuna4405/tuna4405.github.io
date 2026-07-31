---
title : "Đẩy log ứng dụng"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---

Mặc định, log `pm2` của API Express chỉ tồn tại trên đúng instance EC2 đã sinh ra
chúng - vẫn ổn khi chạy `pm2 logs caerus-api` qua Instance Connect, nhưng trở nên vô
dụng ngay khi có hai instance và một sự cố có thể nằm ở bất kỳ cái nào. CloudWatch
agent đẩy log của cả hai instance vào chung một log group.

1. **Cài và cấu hình agent** trên từng instance:

   ```bash
   sudo dnf install -y amazon-cloudwatch-agent
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
   ```

   Trỏ nó vào `~/.pm2/logs/caerus-api-out.log` và
   `~/.pm2/logs/caerus-api-error.log`, log group là `/caerus/ec2/api`, sau đó:

   ```bash
   sudo systemctl enable amazon-cloudwatch-agent
   sudo systemctl start amazon-cloudwatch-agent
   ```

2. **Cấp cho instance role những quyền cần thiết cho việc này** -
   `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` - thêm dưới
   dạng một statement trên `caerus-ec2-s3-role`, bên cạnh các quyền S3 từ mục 5.6.3.

3. **Đặt thời hạn lưu trữ cho log group** - 7 ngày. CloudWatch Logs mặc định là
   "Never expire", vốn âm thầm tích lũy chi phí lưu trữ mãi mãi; đặt một thời hạn
   ngắn và rõ ràng là một thao tác năm giây đáng làm với mọi log group được tạo ra
   trong dự án này.

4. **Chạy một truy vấn Logs Insights trả lời một câu hỏi thật** - không chỉ là "log
   có xuất hiện ở đây không", mà là thứ một người vận hành thật sẽ hỏi, chẳng hạn
   nhánh xung đột đặt vé bị chạm tới bao nhiêu lần:

   ```
   fields @timestamp, @message
   | filter @message like /SEAT_ALREADY_BOOKED/
   | stats count() by bin(1h)
   ```

   Đây cũng là một buổi diễn tập tự nhiên cho alarm sẽ được dựng ngay sau đó ở mục
   5.8.3, vốn theo dõi một tín hiệu liên quan một cách tự động thay vì đòi hỏi ai đó
   phải chạy tay truy vấn này.

<!-- ![Logs Insights query result across both instances' streams](/images/5-Workshop/5.8-CloudWatch/5.8.2-application-logs/example.png) -->
