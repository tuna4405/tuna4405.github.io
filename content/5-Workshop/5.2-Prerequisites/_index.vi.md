---
title : "Các bước chuẩn bị"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Tổng quan

Mọi thứ trong workshop này giả định rằng những điều sau đây đã được chuẩn
bị sẵn trước cú click đầu tiên trên AWS Console.

**Region.** Mọi tài nguyên đều được tạo trong **ap-southeast-1** (Singapore).
Kiểm tra bộ chọn region ở góc trên bên phải của Console trước mỗi bước - một
tài nguyên được tạo sai region là nguồn gốc phổ biến nhất của kiểu nhầm lẫn
"hôm qua vẫn chạy được" trong một dự án hai người.

**Thiết lập account và IAM.** Cả hai thành viên trong nhóm đều làm việc với
tư cách IAM user, không bao giờ dùng account root user, bên trong một nhóm
`caerus-developers` dùng chung. Policy của nhóm cấp quyền truy cập Console
vào EC2, RDS, S3, VPC/NAT, CloudFront, WAF, Systems Manager, CloudWatch, và
SNS, quyền chỉ đọc (read-only) vào danh sách user của IAM (cần thiết để
trang MFA của chính Console có thể render) và vào Billing/Cost Explorer, và
tường minh từ chối `iam:CreateUser`, `iam:DeleteUser`, và
`iam:AttachUserPolicy` - hai developer user không thể tự nâng quyền cho
chính mình.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ServiceConsoles",
      "Effect": "Allow",
      "Action": [
        "ec2:*", "rds:*", "s3:*", "cloudfront:*", "wafv2:*",
        "ssm:*", "cloudwatch:*", "sns:*", "logs:*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "ReadOnlyIamAndBilling",
      "Effect": "Allow",
      "Action": ["iam:ListUsers", "iam:GetUser", "ce:*", "aws-portal:View*"],
      "Resource": "*"
    },
    {
      "Sid": "DenyUserEscalation",
      "Effect": "Deny",
      "Action": ["iam:CreateUser", "iam:DeleteUser", "iam:AttachUserPolicy"],
      "Resource": "*"
    }
  ]
}
```

{{% notice note %}}
Đây chỉ minh họa hình dạng chung của policy, không phải bản export chính
xác từng byte - hãy thay thế nó bằng đoạn JSON thực tế từ nhóm
`caerus-developers` trong account của bạn trước khi publish.
{{% /notice %}}

**Quy ước đặt tên và gắn tag**, được thực thi bằng một permission boundary
chứ không chỉ là một gợi ý:

- Mọi tên IAM role đều phải bắt đầu bằng `caerus-` (`caerus-ec2-s3-role`) -
  tạo một role dưới bất kỳ tên nào khác sẽ thất bại với một lỗi
  `AccessDenied` không rõ ràng thay vì một thông báo lỗi vi phạm quy tắc
  đặt tên rõ ràng, nên hãy lường trước lỗi đó một lần và đừng bối rối vì
  nó.
- Mọi tài nguyên đều được gắn tag `Owner` với tên của developer tạo ra nó,
  để một đợt tăng đột biến chi phí có thể được truy vết về đúng một người
  trong Cost Explorer chỉ trong vài giây thay vì phải kiểm tra toàn bộ
  account.
- Một billing alarm được kích hoạt ở mức **US$150** - được đặt cao hơn mức
  chi phí vận hành thực tế dự kiến của kiến trúc này (xem [Quản lý chi phí
  và tài nguyên](/5-Workshop/5.10-Cost/)) thay vì đặt ở một giá trị tượng
  trưng, để nó chỉ kích hoạt khi có sai sót thực sự - một NAT gateway thứ
  hai, một loại storage RDS quá lớn - mà không kích hoạt trong quá trình
  vận hành bình thường, như dự kiến. NAT gateway và Application Load
  Balancer là hai thành phần tốn phí theo giờ bất kể lưu lượng truy cập, và
  cùng nhau đã chiếm khoảng một nửa hóa đơn hàng tháng.

**Công cụ cục bộ**, cần thiết trước mục 5.4:

- Node.js 20 trở lên
- Docker Desktop, để chạy một container PostgreSQL 16 cục bộ
- Một trình soạn thảo code và một terminal sử dụng thành thạo cả `psql` lẫn
  `curl`

**Cổng cục bộ (local ports)**, được cố định cho toàn bộ dự án để máy của
hai developer hoạt động giống hệt nhau:

| Port | Được dùng bởi |
|---|---|
| 5173 | Vite frontend dev server |
| 3000 | Express API |
| 5433 | PostgreSQL chạy Docker (map từ cổng 5432 của container) |
