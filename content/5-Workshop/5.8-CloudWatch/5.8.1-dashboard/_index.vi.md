---
title : "Xây dựng dashboard"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.8.1 </b> "
---

1. **CloudWatch Console → Dashboards → Create dashboard**, đặt tên
   `caerus-overview`.

2. **Thêm mỗi metric một widget**, tổng cộng sáu cái:

   | Widget | Metric | Kiểu |
   |---|---|---|
   | EC2 CPU | `CPUUtilization`, cả hai instance trong cùng một widget | Line |
   | RDS CPU | `CPUUtilization` (`caerus-db`) | Line |
   | RDS Connections | `DatabaseConnections` | Line |
   | RDS Free Storage | `FreeStorageSpace` | Line |
   | ALB Requests | `RequestCount` (`caerus-alb`, nằm dưới mục "Per AppELB Metrics" - con số tổng chưa tách theo AZ) | Line |
   | ALB Target Health | `HealthyHostCount` + `UnHealthyHostCount` (`caerus-tg`), chung một widget | Line |

3. **Tạo ra traffic thật trước khi chụp màn hình** - duyệt trang web, đặt vài ghế,
   tải một vé xuống - một dashboard toàn những đường thẳng tắp chỉ chứng minh rằng
   các widget đã được thêm vào, chứ không chứng minh có thứ gì thực sự đang được
   quan sát.

<!-- ![caerus-overview dashboard with real traffic on every widget](/images/5-Workshop/5.8-CloudWatch/5.8.1-dashboard/example.png) -->
