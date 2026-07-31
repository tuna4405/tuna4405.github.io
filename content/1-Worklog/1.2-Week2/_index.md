---
title: "Week 2 Worklog"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives: Account Groundwork and Identity and Access Management

* Set up a shared AWS account and secure it, with spending controls established before anything billable gets provisioned.
* Get to grips with the IAM identity model and be able to both read and write policy documents.
* Understand what makes roles preferable to long-lived access keys, and apply least privilege to a setup for two people.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Accounts and billing: <br> &emsp; + The root user compared with an IAM user, and the reasons the root user has no place in daily work <br> &emsp; + AWS Free Tier: the always-free, 12-month, and trial offers <br> - **Practice:** opened the shared AWS account, turned on MFA for the root user, and put a billing alarm in place before creating anything else <br> - Fixed on `ap-southeast-1` (Singapore) as the Region the whole project would work in | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - IAM core concepts: <br> &emsp; + Users, groups, roles, and policies <br> &emsp; + Authentication as against authorisation <br> &emsp; + The way a request gets evaluated: explicit deny, explicit allow, implicit deny <br> &emsp; + What a policy document is made of: `Effect`, `Action`, `Resource`, `Condition` | 23/06/2026 | 23/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 4 | - A closer look at IAM roles: <br> &emsp; + Trust policy as against permissions policy <br> &emsp; + Instance profiles: how credentials reach an EC2 instance without any key being stored <br> &emsp; + Naming and tagging treated as governance: a project prefix on every role name, an `Owner` value tagged on every resource | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 5 | - **Practice:** <br> &emsp; + Created a shared developer group along with one IAM user per team member, MFA enforced on both <br> &emsp; + Attached a scoped policy opening up only the service consoles actually needed, with `iam:CreateUser` and `iam:AttachUserPolicy` denied so that neither user can widen their own permissions <br> &emsp; + Tried a denied action on purpose and read through the error it produced | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 6 | - VPC fundamentals: <br> &emsp; + CIDR blocks, subnets, and what it actually is that makes a subnet public or private <br> &emsp; + Route tables, the internet gateway, and the default local route <br> &emsp; + Security groups (stateful, attached to an interface) as against network ACLs (stateless, attached to a subnet) | 26/06/2026 | 26/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 7 | - Self-study: read up on AWS Budgets and Cost Anomaly Detection - a spending alarm driven by a forecast, as against a model that learns what normal spend looks like and flags anything departing from it - which bears directly on the billing alarm configured back on Day 2 <br> - Noted that a NAT gateway and an idle load balancer are the two components most likely to produce an unexpected charge further into the project | 27/06/2026 | 27/06/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html> |

### Week 2 Achievements:

* Opened and secured a shared AWS account, MFA and a billing alarm both in place before a single billable resource existed.
* Able to read a policy JSON document and say in advance whether a given request comes out allowed or denied.
* Put together a working group-and-user setup on least privilege with MFA, confirmed by testing a denied action instead of assuming it.
* Understand the reasons an application running on EC2 belongs on an instance role rather than on embedded access keys.
* Able to tell a security group apart from a network ACL, and explain why referring to a security group by ID beats a raw IP range.
