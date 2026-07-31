---
title : "Các bước chuẩn bị"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Tổng quan

Mọi thứ trong workshop này giả định những điều sau đã sẵn sàng trước cú nhấp chuột
đầu tiên trên AWS Console.

**Region.** Mọi tài nguyên đều được tạo trong **ap-southeast-1** (Singapore). Hãy
kiểm tra ô chọn region ở góc trên bên phải Console trước mỗi bước - một tài nguyên
được tạo nhầm region chính là nguồn gốc phổ biến nhất của kiểu bối rối "hôm qua nó
còn chạy mà" trong một dự án hai người.

**Thiết lập tài khoản và IAM.** Cả hai thành viên đều làm việc dưới tư cách IAM
user, không bao giờ dùng account root user, bên trong một group dùng chung
`caerus-developers`. Policy của group cấp quyền truy cập Console vào EC2, RDS, S3,
VPC/NAT, CloudFront, WAF, Systems Manager, CloudWatch, và SNS, quyền chỉ đọc vào
danh sách user của IAM (cần để trang MFA của chính Console hiển thị được) và vào
Billing/Cost Explorer, đồng thời từ chối rõ ràng `iam:CreateUser`,
`iam:DeleteUser`, và `iam:AttachUserPolicy` - hai user lập trình viên không thể tự
nâng quyền của chính mình.

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "CoreProjectServices",
			"Effect": "Allow",
			"Action": [
				"elasticloadbalancing:CreateLoadBalancer",
				"elasticloadbalancing:CreateTargetGroup",
				"elasticloadbalancing:CreateListener",
				"elasticloadbalancing:RegisterTargets",
				"elasticloadbalancing:DescribeLoadBalancers",
				"elasticloadbalancing:DescribeTargetGroups",
				"elasticloadbalancing:DescribeListeners",
				"elasticloadbalancing:DescribeTargetHealth",
				"elasticloadbalancing:ModifyLoadBalancerAttributes",
				"elasticloadbalancing:DeleteLoadBalancer",
				"elasticloadbalancing:DeleteTargetGroup",
				"elasticloadbalancing:AddTags",
				"ec2-instance-connect:SendSSHPublicKey",
				"ec2:*",
				"rds:*",
				"s3:*",
				"apigateway:*",
				"cloudwatch:*",
				"logs:*",
				"sns:*",
				"cloudfront:*"
			],
			"Resource": "*"
		},
		{
			"Effect": "Allow",
			"Action": "wafv2:*",
			"Resource": "*"
		},
		{
			"Effect": "Allow",
			"Action": "iam:CreateServiceLinkedRole",
			"Resource": "arn:aws:iam::857481978603:role/aws-service-role/elasticloadbalancing.amazonaws.com/AWSServiceRoleForElasticLoadBalancing",
			"Condition": {
				"StringEquals": {
					"iam:AWSServiceName": "elasticloadbalancing.amazonaws.com"
				}
			}
		},
		{
			"Sid": "CloudFrontManagedWAFManagedRuleSet",
			"Effect": "Allow",
			"Action": [
				"wafv2:CreateWebACL",
				"wafv2:UpdateWebACL",
				"wafv2:GetWebACL"
			],
			"Resource": "arn:aws:wafv2:us-east-1:857481978603:global/managedruleset/*/*"
		},
		{
			"Sid": "CloudFrontManagedWAF",
			"Effect": "Allow",
			"Action": [
				"wafv2:CreateWebACL",
				"wafv2:UpdateWebACL",
				"wafv2:DeleteWebACL",
				"wafv2:GetWebACL",
				"wafv2:ListWebACLs",
				"wafv2:TagResource",
				"wafv2:UntagResource",
				"wafv2:ListTagsForResource"
			],
			"Resource": "arn:aws:wafv2:us-east-1:857481978603:global/webacl/*"
		},
		{
			"Sid": "ManageCaerusRolesOnly",
			"Effect": "Allow",
			"Action": [
				"iam:CreateRole",
				"iam:DeleteRole",
				"iam:AttachRolePolicy",
				"iam:DetachRolePolicy",
				"iam:PutRolePolicy",
				"iam:DeleteRolePolicy",
				"iam:TagRole",
				"iam:UntagRole",
				"iam:UpdateAssumeRolePolicy",
				"iam:CreateInstanceProfile",
				"iam:DeleteInstanceProfile",
				"iam:AddRoleToInstanceProfile",
				"iam:RemoveRoleFromInstanceProfile"
			],
			"Resource": [
				"arn:aws:iam::857481978603:role/caerus-*",
				"arn:aws:iam::857481978603:instance-profile/caerus-*"
			]
		},
		{
			"Sid": "PassCaerusRolesToComputeOnly",
			"Effect": "Allow",
			"Action": "iam:PassRole",
			"Resource": "arn:aws:iam::857481978603:role/caerus-*",
			"Condition": {
				"StringEquals": {
					"iam:PassedToService": [
						"ec2.amazonaws.com",
						"lambda.amazonaws.com"
					]
				}
			}
		},
		{
			"Sid": "ServiceLinkedRolesForAwsServices",
			"Effect": "Allow",
			"Action": "iam:CreateServiceLinkedRole",
			"Resource": "*",
			"Condition": {
				"StringEquals": {
					"iam:AWSServiceName": [
						"rds.amazonaws.com",
						"monitoring.rds.amazonaws.com",
						"apigateway.amazonaws.com",
						"ops.apigateway.amazonaws.com"
					]
				}
			}
		},
		{
			"Sid": "IamReadOnly",
			"Effect": "Allow",
			"Action": [
				"iam:GetRole",
				"iam:ListRoles",
				"iam:GetRolePolicy",
				"iam:ListRolePolicies",
				"iam:ListAttachedRolePolicies",
				"iam:GetInstanceProfile",
				"iam:ListInstanceProfiles",
				"iam:ListInstanceProfilesForRole",
				"iam:GetPolicy",
				"iam:GetPolicyVersion",
				"iam:ListPolicies"
			],
			"Resource": "*"
		},
		{
			"Sid": "SelfManageOwnCredentials",
			"Effect": "Allow",
			"Action": [
				"iam:ChangePassword",
				"iam:GetUser",
				"iam:CreateVirtualMFADevice",
				"iam:EnableMFADevice",
				"iam:ResyncMFADevice",
				"iam:DeactivateMFADevice",
				"iam:ListMFADevices",
				"iam:CreateAccessKey",
				"iam:DeleteAccessKey",
				"iam:ListAccessKeys",
				"iam:UpdateAccessKey"
			],
			"Resource": [
				"arn:aws:iam::857481978603:user/${aws:username}",
				"arn:aws:iam::857481978603:mfa/${aws:username}"
			]
		},
		{
			"Sid": "ListVirtualMfaForConsole",
			"Effect": "Allow",
			"Action": [
				"iam:ListVirtualMFADevices",
				"iam:ListUsers",
				"iam:GetAccountSummary"
			],
			"Resource": "*"
		},
		{
			"Sid": "ViewBillingReadOnly",
			"Effect": "Allow",
			"Action": [
				"billing:Get*",
				"billing:List*",
				"ce:Get*",
				"ce:Describe*",
				"ce:List*",
				"budgets:ViewBudget",
				"budgets:DescribeBudget*",
				"freetier:Get*",
				"cur:DescribeReportDefinitions"
			],
			"Resource": "*"
		}
	]
}
```


**Quy ước đặt tên và gắn tag**, được bảo đảm bằng một permission boundary chứ
không chỉ để ở mức khuyến nghị:

- Mọi tên IAM role đều phải bắt đầu bằng `caerus-` (`caerus-ec2-s3-role`) - tạo
  một role với tên khác sẽ thất bại kèm một thông báo `AccessDenied` chẳng giúp
  ích gì, thay vì một lỗi vi phạm quy ước đặt tên rõ ràng, nên hãy lường trước
  rằng bạn sẽ gặp lỗi đó một lần và đừng bị nó làm cho hoang mang.
- Mọi tài nguyên đều được gắn tag `Owner` mang tên lập trình viên đã tạo ra nó, nhờ
  vậy một đợt tăng chi phí có thể truy ra tới một con người trong Cost Explorer chỉ
  sau vài giây, thay vì phải kiểm toán cả tài khoản.
- Một billing alarm kích hoạt ở mức **US$150** - đặt cao hơn mức chi tiêu thực tế
  dự kiến của kiến trúc này (xem [Quản lý chi phí và tài
  nguyên](/5-Workshop/5.10-Cost/)) chứ không đặt ở một con số tượng trưng, để nó
  báo động khi có sai sót thật - một NAT gateway thứ hai, một loại storage RDS quá
  khổ - mà không đồng thời báo động vì hoạt động bình thường, đã lường trước. NAT
  gateway và Application Load Balancer là hai thành phần tính tiền theo giờ bất kể
  lưu lượng ra sao, và riêng hai thứ đó đã chiếm khoảng một nửa hóa đơn hằng tháng.

**Công cụ cục bộ**, cần có trước mục 5.4:

- Node.js 20 trở lên
- Docker Desktop, để chạy một container PostgreSQL 16 cục bộ
- Một trình soạn thảo code và một terminal quen thuộc với cả `psql` lẫn `curl`

**Các cổng cục bộ**, cố định cho toàn dự án để máy của hai lập trình viên hoạt động
giống hệt nhau:

| Cổng | Dùng bởi |
|---|---|
| 5173 | Vite frontend dev server |
| 3000 | Express API |
| 5433 | PostgreSQL chạy trên Docker (ánh xạ từ cổng 5432 của container) |
