---
title : "Xây dựng dashboard"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.8.1 </b> "
---

1. Vào **CloudWatch Console → Dashboards → Create dashboard**, đặt tên là
   `caerus-overview`.

2. **Thêm một widget cho mỗi metric**, tổng cộng sáu widget:

   | Widget | Metric | Loại |
   |---|---|---|
   | EC2 CPU | `CPUUtilization`, cả hai instance trong cùng một widget | Line |
   | RDS CPU | `CPUUtilization` (`caerus-db`) | Line |
   | RDS Connections | `DatabaseConnections` | Line |
   | RDS Free Storage | `FreeStorageSpace` | Line |
   | ALB Requests | `RequestCount` (`caerus-alb`, trong mục "Per AppELB Metrics" - tổng số không tách theo AZ) | Line |
   | ALB Target Health | `HealthyHostCount` + `UnHealthyHostCount` (`caerus-tg`), cùng một widget | Line |

3. **Tạo traffic thực trước khi chụp screenshot** - duyệt trang web, đặt vài
   ghế, tải một vé - một dashboard với các đường biểu đồ phẳng tuyệt đối chỉ
   chứng minh rằng các widget đã được thêm vào, chứ không chứng minh rằng có
   bất kỳ điều gì thực sự đang được quan sát.

<!-- ![Dashboard caerus-overview với traffic thực trên mọi widget](/images/5-Workshop/5.8-CloudWatch/5.8.1-dashboard/example.png) -->
