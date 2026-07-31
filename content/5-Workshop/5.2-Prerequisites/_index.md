---
title : "Prerequisites"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Overview

Everything in this workshop assumes the following is already in place before
the first AWS Console click.

**Region.** Every resource is created in **ap-southeast-1** (Singapore).
Check the region selector in the top-right of the Console before every step -
a resource created in the wrong region is the single most common source of
"it worked yesterday" confusion in a two-person project.

**Account and IAM setup.** Both team members work as IAM users, never as the
account root user, inside a shared `caerus-developers` group. The group
policy grants Console access to EC2, RDS, S3, VPC/NAT, CloudFront, WAF,
Systems Manager, CloudWatch, and SNS, read-only access to IAM's user list
(needed for the Console's own MFA page to render) and to Billing/Cost
Explorer, and explicitly denies `iam:CreateUser`, `iam:DeleteUser`, and
`iam:AttachUserPolicy` - the two developer users cannot escalate their own
permissions.

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


**Naming and tagging conventions**, enforced by a permission boundary rather
than left as a suggestion:

- Every IAM role name must start with `caerus-` (`caerus-ec2-s3-role`) -
  creating a role under any other name fails with an unhelpful `AccessDenied`
  rather than a clear naming-policy error, so expect that failure once and
  don't be thrown by it.
- Every resource is tagged `Owner` with the creating developer's name, so a
  cost spike can be traced to a person in Cost Explorer within seconds rather
  than requiring an audit of the whole account.
- A billing alarm fires at **US$150** - set above this architecture's actual
  expected run-rate (see [Cost and Resource
  Management](/5-Workshop/5.10-Cost/)) rather than at a token value, so it
  fires on a genuine mistake - a second NAT gateway, an oversized RDS
  storage type - without also firing on ordinary, expected operation. The
  NAT gateway and the Application Load Balancer are the two components that
  cost money by the hour regardless of traffic, and together already account
  for roughly half the monthly bill.

**Local tooling**, needed before section 5.4:

- Node.js 20 or later
- Docker Desktop, for a local PostgreSQL 16 container
- A code editor and a terminal comfortable with both `psql` and `curl`

**Local ports**, fixed for the whole project so the two developers' machines
behave identically:

| Port | Used by |
|---|---|
| 5173 | Vite frontend dev server |
| 3000 | Express API |
| 5433 | Dockerised PostgreSQL (mapped from the container's 5432) |
