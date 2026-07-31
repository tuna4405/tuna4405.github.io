---
title: "Nhật ký tuần 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2: Nền móng Tài khoản và Quản lý Danh tính, Truy cập (IAM)

* Mở và bảo mật một tài khoản AWS dùng chung, với các biện pháp kiểm soát chi tiêu được dựng lên trước khi khởi tạo bất cứ tài nguyên tính phí nào.
* Nắm được mô hình danh tính (identity model) của IAM và đọc được, viết được các tài liệu policy.
* Hiểu vì sao role tốt hơn access key tồn tại lâu dài (long-lived), và vận dụng nguyên tắc least privilege cho một nhóm hai người.

### Công việc thực hiện trong tuần:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Tài khoản và billing: <br> &emsp; + Root user đặt cạnh IAM user, và lý do root user không nên xuất hiện trong công việc hằng ngày <br> &emsp; + AWS Free Tier: các gói always-free, 12 tháng, và dùng thử (trial) <br> - **Thực hành:** mở tài khoản AWS dùng chung, bật MFA cho root user, và dựng billing alarm trước khi tạo bất cứ thứ gì khác <br> - Chốt `ap-southeast-1` (Singapore) là Region làm việc cho cả dự án | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Các khái niệm cốt lõi của IAM: <br> &emsp; + Users, groups, roles, và policies <br> &emsp; + Authentication đặt cạnh authorisation <br> &emsp; + Cách một request được đánh giá: explicit deny, explicit allow, implicit deny <br> &emsp; + Một tài liệu policy gồm những gì: `Effect`, `Action`, `Resource`, `Condition` | 23/06/2026 | 23/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 4 | - Nhìn kỹ hơn vào IAM roles: <br> &emsp; + Trust policy đặt cạnh permissions policy <br> &emsp; + Instance profiles: credentials đến được với một instance EC2 bằng cách nào mà không cần lưu key <br> &emsp; + Đặt tên và gắn tag như một hình thức quản trị (governance): tiền tố dự án trên mọi tên role, giá trị `Owner` gắn tag lên mọi tài nguyên | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 5 | - **Thực hành:** <br> &emsp; + Tạo một group developer dùng chung kèm một IAM user cho mỗi thành viên, cả hai đều bắt buộc MFA <br> &emsp; + Gắn một policy thu hẹp phạm vi, chỉ mở đúng những console dịch vụ thực sự cần, đồng thời deny `iam:CreateUser` và `iam:AttachUserPolicy` để không ai tự nới quyền cho chính mình được <br> &emsp; + Cố tình thực hiện một hành động bị từ chối và đọc kỹ thông báo lỗi nhận được | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 6 | - Kiến thức nền tảng về VPC: <br> &emsp; + CIDR block, subnet, và thứ thực sự quyết định một subnet là public hay private <br> &emsp; + Route table, internet gateway, và route local mặc định <br> &emsp; + Security group (stateful, gắn vào interface) đặt cạnh network ACL (stateless, gắn vào subnet) | 26/06/2026 | 26/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 7 | - Tự học: tìm hiểu AWS Budgets và Cost Anomaly Detection - một cảnh báo chi tiêu chạy theo dự báo (forecast), đặt cạnh một mô hình học mức chi tiêu bình thường rồi gắn cờ những gì lệch khỏi mức đó - liên quan trực tiếp tới billing alarm đã cấu hình ở Ngày 2 <br> - Ghi nhận rằng NAT gateway và một load balancer để nhàn rỗi (idle) là hai thành phần dễ tạo ra khoản phí ngoài dự tính nhất ở các giai đoạn sau của dự án | 27/06/2026 | 27/06/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html> |

### Kết quả đạt được Tuần 2:

* Mở và bảo mật một tài khoản AWS dùng chung, với MFA và billing alarm đều đã sẵn sàng trước khi tồn tại bất kỳ tài nguyên tính phí nào.
* Đọc được một tài liệu policy JSON và nói trước được một request cụ thể sẽ được cho phép hay bị từ chối.
* Dựng được một thiết lập group-và-user chạy tốt theo least privilege kèm MFA, xác nhận bằng cách thử một hành động bị từ chối thay vì chỉ suy đoán.
* Hiểu lý do một ứng dụng chạy trên EC2 nên dùng instance role chứ không phải access key nhúng sẵn trong code.
* Phân biệt được security group với network ACL, và giải thích được vì sao tham chiếu security group bằng ID lại hơn là dùng dải IP thô (raw IP range).
