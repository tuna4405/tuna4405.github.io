---
title: "Week 2 Worklog"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives: Identity and Access Management

* Understand the IAM identity model and be able to read and write policy documents.
* Understand why roles are preferred over long-lived access keys.
* Apply least privilege to a realistic multi-user setup.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - IAM core concepts: <br> &emsp; + Users, groups, roles, and policies <br> &emsp; + Authentication versus authorisation <br> &emsp; + How a request is evaluated: explicit deny, explicit allow, implicit deny | 08/06/2026 | 08/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 3 | - Anatomy of a policy document: <br> &emsp; + `Effect`, `Action`, `Resource`, `Condition` <br> &emsp; + Identity-based versus resource-based policies <br> &emsp; + AWS managed, customer managed, and inline policies <br> - Wildcards and the risks of `Action: "*"` | 09/06/2026 | 09/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 4 | - IAM roles in depth: <br> &emsp; + Trust policy versus permissions policy <br> &emsp; + Assuming a role and temporary credentials via STS <br> &emsp; + Instance profiles: how an EC2 instance obtains credentials without stored keys <br> &emsp; + Service-linked roles and execution roles | 10/06/2026 | 10/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 5 | - **Practice:** <br> &emsp; + Create an IAM group and attach a managed policy <br> &emsp; + Create two IAM users and add them to the group <br> &emsp; + Enforce MFA on both users <br> &emsp; + Deliberately attempt a denied action and read the resulting error <br> &emsp; + Use the IAM policy simulator to test a policy before applying it | 11/06/2026 | 11/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |
| 6 | - Guardrails and governance: <br> &emsp; + Permission boundaries and how they cap a user's maximum permissions <br> &emsp; + Naming and tagging conventions as a governance tool <br> &emsp; + Restricting billing access while leaving cost visibility intact <br> - Self-study: read the IAM security best practice guidance and compared it against my own account setup | 12/06/2026 | 12/06/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/> |

### Week 2 Achievements:

* Can read a policy JSON document and predict whether a given request will be allowed or denied.
* Understand why an application running on EC2 should use an instance role rather than embedded access keys, and can explain how the credentials are delivered.
* Built a working group-and-user setup with least privilege and MFA, and verified the restrictions by testing denied actions rather than assuming them.
* Understand how permission boundaries and naming conventions can enforce policy at scale.
