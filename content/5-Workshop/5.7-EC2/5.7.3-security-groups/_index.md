---
title : "Lock Down Security Groups"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.7.3 </b> "
---

Two security groups, created before the resources they protect even existed
(section 5.5.1 and 5.7.2 both referenced `caerus-rds-sg` and
`caerus-ec2-sg` by name in advance):

**`caerus-ec2-sg`** - the application instance:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| Custom TCP | 3000 | My IP |

**`caerus-rds-sg`** - created empty, then given exactly one rule once
`caerus-ec2-sg` existed to reference:

| Type | Port | Source (before) | Source (after) |
|---|---|---|---|
| PostgreSQL | 5432 | *(no rule - unreachable)* | `caerus-ec2-sg` |

The rule's **Source** is the EC2 security group itself, selected by name in
the console, not a CIDR block or an IP address. This is the point worth
making explicitly: RDS admits traffic from *any instance carrying that
security group*, regardless of what IP address that instance happens to
have today. An IP-based rule would need updating every time the instance is
stopped and restarted, or would have to be widened to a range broad enough
to be meaningless; a security-group-referencing rule needs updating never,
and stays exactly as tight as "only this application can reach this
database" for as long as the two groups exist.

{{% notice note %}}
If SSH access is ever lost after a home IP address changes, the fix is this
same panel: edit the SSH rule's source back to "My IP" so the console
re-resolves the current address. This rule itself is temporary - section
5.7.7 removes it entirely once the instances move to private subnets and
switch to Systems Manager Session Manager for administration.
{{% /notice %}}

<!-- ![caerus-rds-sg inbound rules, before (empty) and after (5432 from caerus-ec2-sg)](/images/5-Workshop/5.7-EC2/5.7.3-security-groups/example.png) -->
