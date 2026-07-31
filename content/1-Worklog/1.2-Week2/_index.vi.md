---
title: "Nhật ký tuần 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2: Nền tảng Tài khoản và Quản lý Danh tính, Truy cập (IAM)

* Tạo và bảo mật một tài khoản AWS dùng chung, với các biện pháp kiểm soát chi phí được thiết lập trước khi khởi tạo bất kỳ tài nguyên tính phí nào.
* Hiểu mô hình danh tính (identity model) của IAM và có thể đọc, viết các tài liệu policy.
* Hiểu vì sao role được ưu tiên hơn access key tồn tại lâu dài (long-lived), và áp dụng nguyên tắc least privilege cho một thiết lập hai người.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Tài khoản và billing: <br> &emsp; + Root user so với IAM user, và vì sao không nên dùng root user hàng ngày <br> &emsp; + AWS Free Tier: các ưu đãi always-free, 12 tháng, và dùng thử (trial) <br> - **Thực hành:** tạo tài khoản AWS dùng chung, bật MFA cho root user, và thiết lập billing alarm trước khi tạo bất kỳ tài nguyên nào khác <br> - Quyết định chọn `ap-southeast-1` (Singapore) làm Region làm việc cho toàn bộ dự án | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Các khái niệm cốt lõi của IAM: <br> &emsp; + Users, groups, roles, và policies <br> &emsp; + Authentication so với authorisation <br> &emsp; + Cách một request được đánh giá: explicit deny, explicit allow, implicit deny <br> &emsp; + Cấu trúc của một tài liệu policy: `Effect`, `Action`, `Resource`, `Condition` | 23/06/2026 | 23/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 4 | - Đi sâu vào IAM roles: <br> &emsp; + Trust policy so với permissions policy <br> &emsp; + Instance profiles: cách một instance EC2 lấy được credentials mà không cần lưu key <br> &emsp; + Đặt tên và gắn tag như một hình thức quản trị (governance): mọi tên role đều mang tiền tố dự án, mọi tài nguyên đều được gắn tag `Owner` | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 5 | - **Thực hành:** <br> &emsp; + Tạo một group developer dùng chung và một IAM user cho mỗi thành viên nhóm, cả hai đều bắt buộc MFA <br> &emsp; + Gắn một policy giới hạn phạm vi chỉ cấp quyền vào các console dịch vụ cần thiết, từ chối (deny) `iam:CreateUser` và `iam:AttachUserPolicy` để không user nào có thể tự nâng quyền cho chính mình <br> &emsp; + Chủ động thử một hành động bị từ chối và đọc thông báo lỗi trả về | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 6 | - Kiến thức nền tảng về VPC: <br> &emsp; + CIDR block, subnet, và điều thực sự khiến một subnet trở thành public hay private <br> &emsp; + Route table, internet gateway, và route local mặc định <br> &emsp; + Security group (stateful, gắn vào interface) so với network ACL (stateless, gắn vào subnet) | 26/06/2026 | 26/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 7 | - Tự học: đọc về AWS Budgets và Cost Anomaly Detection - một cảnh báo chi tiêu dựa trên dự báo (forecast) so với một mô hình học chi tiêu bình thường và gắn cờ khi có sự sai lệch - hữu ích trực tiếp cho billing alarm vừa cấu hình ở Ngày 2 <br> - Ghi nhận NAT gateway và một load balancer bị bỏ nhàn rỗi (idle) là hai thành phần có khả năng cao nhất phát sinh chi phí ngoài dự kiến sau này trong dự án | 27/06/2026 | 27/06/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html> |

### Kết quả đạt được Tuần 2:

* Tạo và bảo mật một tài khoản AWS dùng chung, với MFA và billing alarm được thiết lập trước khi có bất kỳ tài nguyên tính phí nào tồn tại.
* Có thể đọc một tài liệu policy JSON và dự đoán được một request cụ thể sẽ được cho phép hay bị từ chối.
* Xây dựng thành công một thiết lập group-và-user với least privilege và MFA, được xác minh bằng cách thử một hành động bị từ chối thay vì chỉ giả định.
* Hiểu vì sao một ứng dụng chạy trên EC2 nên dùng instance role thay vì access key được nhúng sẵn.
* Có thể phân biệt security group với network ACL, và giải thích vì sao tham chiếu đến một security group bằng ID lại tốt hơn dùng dải IP thô (raw IP range).
