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

**`caerus-ec2-sg`** - the application instance, created with **no inbound
rule at all**:

| Type | Port | Source |
|---|---|---|
| *(none)* | - | - |

There is nothing to admit yet, and nothing needs admitting. Administration
goes through Systems Manager, which reaches the instance over an *outbound*
connection through the NAT gateways from section 5.7.2 - no inbound rule, no
key pair, involved at any point. Verification during development goes through
an SSM port-forwarding tunnel (also outbound-initiated). The only inbound
traffic this application instance will ever need is application traffic from
the load balancer, and that load balancer does not exist until section 5.7.5 -
so this group carries zero rules until that section adds exactly one:
Custom TCP, port 3000, source **security group `caerus-alb-sg`**.

**`caerus-rds-sg`** - created empty, then given exactly one rule once
`caerus-ec2-sg` existed to reference:

| Type | Port | Source (before) | Source (after) |
|---|---|---|---|
| PostgreSQL | 5432 | *(no rule - unreachable)* | `caerus-ec2-sg` |

The rule's **Source** is the EC2 security group itself, selected by name in
the console, not a CIDR block or an IP address - and it works this way
regardless of whether `caerus-ec2-sg` carries any inbound rules of its own,
since a security-group-referencing rule matches on group membership, not on
what that group admits. This is the point worth making explicitly: RDS admits
traffic from *any instance carrying that security group*, regardless of what
IP address that instance happens to have today, or whether it has one at all.
An IP-based rule would need updating every time an instance is replaced, or
would have to be widened to a range broad enough to be meaningless; a
security-group-referencing rule needs updating never, and stays exactly as
tight as "only this application can reach this database" for as long as the
two groups exist.

{{% notice note %}}
There is no "if SSH access is lost, edit the rule back to My IP" escape hatch
in this project, because there was never a rule tying access to an IP address
to begin with. Losing a home IP address, switching networks, or handing the
project to a new team member changes nothing about how these instances are
reached - Systems Manager authorizes through IAM, not through where the
connection happens to originate.
{{% /notice %}}

<!-- ![caerus-ec2-sg with zero inbound rules, and caerus-rds-sg admitting 5432 from caerus-ec2-sg](/images/5-Workshop/5.7-EC2/5.7.3-security-groups/example.png) -->
