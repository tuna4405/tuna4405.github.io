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
				"ec2:*",
				"rds:*",
				"s3:*",
				"cloudwatch:*",
				"logs:*",
				"sns:*",
				"cloudfront:*"
			],
			"Resource": "*"
		},
		{
			"Sid": "StartSession",
			"Effect": "Allow",
			"Action": "ssm:StartSession",
			"Resource": [
				"arn:aws:ec2:*:*:instance/*",
				"arn:aws:ssm:*:*:document/SSM-SessionManagerRunShell"
			]
		},
		{
			"Sid": "SessionVisibility",
			"Effect": "Allow",
			"Action": [
				"ssm:DescribeSessions",
				"ssm:GetConnectionStatus",
				"ssm:DescribeInstanceProperties",
				"ec2:DescribeInstances"
			],
			"Resource": "*"
		},
		{
			"Sid": "EndOwnSession",
			"Effect": "Allow",
			"Action": [
				"ssm:TerminateSession",
				"ssm:ResumeSession"
			],
			"Resource": "arn:aws:ssm:*:*:session/${aws:username}-*"
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
					"iam:PassedToService": "ec2.amazonaws.com"
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
						"monitoring.rds.amazonaws.com"
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


**Quy ước đặt tên và gắn tag**, được thực thi bằng các mẫu ARN giới hạn theo
`caerus-*` trong hai statement `ManageCaerusRolesOnly` và
`PassCaerusRolesToComputeOnly` ở trên - không phải một IAM permission
boundary riêng biệt, chỉ đơn giản là policy của chính group từ chối khớp với
bất kỳ tên nào khác - chứ không chỉ là một gợi ý:

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
