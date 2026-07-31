---
title: "Week 2 Worklog"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives: Account Foundations and Identity and Access Management

* Create and secure a shared AWS account, with cost controls in place before provisioning anything billable.
* Understand the IAM identity model and be able to read and write policy documents.
* Understand why roles are preferred over long-lived access keys, and apply least privilege to a two-person setup.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Accounts and billing: <br> &emsp; + Root user versus IAM user, and why the root user should not be used daily <br> &emsp; + AWS Free Tier: always-free, 12-month, and trial offers <br> - **Practice:** created the shared AWS account, enabled MFA on the root user, and set a billing alarm before creating any other resource <br> - Decided on `ap-southeast-1` (Singapore) as the working Region for the whole project | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - IAM core concepts: <br> &emsp; + Users, groups, roles, and policies <br> &emsp; + Authentication versus authorisation <br> &emsp; + How a request is evaluated: explicit deny, explicit allow, implicit deny <br> &emsp; + Anatomy of a policy document: `Effect`, `Action`, `Resource`, `Condition` | 23/06/2026 | 23/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 4 | - IAM roles in depth: <br> &emsp; + Trust policy versus permissions policy <br> &emsp; + Instance profiles: how an EC2 instance obtains credentials without stored keys <br> &emsp; + Naming and tagging as governance: every role name carrying a project prefix, every resource tagged with an `Owner` value | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 5 | - **Practice:** <br> &emsp; + Created a shared developer group and one IAM user per team member, both with MFA enforced <br> &emsp; + Attached a scoped policy granting only the service consoles needed, denying `iam:CreateUser` and `iam:AttachUserPolicy` so neither user can escalate their own permissions <br> &emsp; + Deliberately attempted a denied action and read the resulting error | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 6 | - VPC fundamentals: <br> &emsp; + CIDR blocks, subnets, and what actually makes a subnet public or private <br> &emsp; + Route tables, the internet gateway, and the default local route <br> &emsp; + Security groups (stateful, attached to an interface) versus network ACLs (stateless, attached to a subnet) | 26/06/2026 | 26/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 7 | - Self-study: read about AWS Budgets and Cost Anomaly Detection - a forecast-based spending alarm versus a model that learns normal spend and flags departures from it - directly useful for the billing alarm just configured on Day 2 <br> - Noted that a NAT gateway and an idle load balancer are the two components most likely to generate an unexpected charge later in the project | 27/06/2026 | 27/06/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html> |

### Week 2 Achievements:

* Created and secured a shared AWS account, with MFA and a billing alarm in place before any billable resource existed.
* Can read a policy JSON document and predict whether a given request will be allowed or denied.
* Built a working group-and-user setup with least privilege and MFA, verified by testing a denied action rather than assuming it.
* Understand why an application running on EC2 should use an instance role rather than embedded access keys.
* Can distinguish a security group from a network ACL, and explain why referencing a security group by ID is preferable to a raw IP range.
