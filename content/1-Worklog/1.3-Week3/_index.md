---
title: "Week 3 Worklog"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives: Networking with Amazon VPC

* Understand VPC components and how traffic is routed in and out of a network.
* Distinguish security groups from network ACLs and know when each applies.
* Understand VPC endpoints and the cost implications of NAT gateways.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - VPC fundamentals: <br> &emsp; + CIDR blocks and address planning <br> &emsp; + Subnets, and what actually makes a subnet public or private <br> &emsp; + How subnets map onto Availability Zones | 15/06/2026 | 15/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 3 | - Routing and gateways: <br> &emsp; + Route tables and the default local route <br> &emsp; + Internet gateway <br> &emsp; + NAT gateway versus NAT instance, and the hourly cost of a NAT gateway <br> - Noted that a forgotten NAT gateway is one of the most common unexpected charges | 16/06/2026 | 16/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 4 | - Traffic filtering: <br> &emsp; + Security groups: stateful, allow rules only, attached to an interface <br> &emsp; + Network ACLs: stateless, allow and deny, attached to a subnet <br> &emsp; + Referencing one security group from another instead of hard-coding addresses | 17/06/2026 | 17/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 5 | - VPC endpoints: <br> &emsp; + Gateway endpoints for Amazon S3 and DynamoDB <br> &emsp; + Interface endpoints and AWS PrivateLink <br> &emsp; + Why keeping S3 traffic off the public internet matters for both security and data transfer cost | 18/06/2026 | 18/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |
| 6 | - **Practice:** <br> &emsp; + Build a VPC by hand: two subnets, route tables, and an internet gateway <br> &emsp; + Launch an instance into the public subnet and confirm connectivity <br> &emsp; + Tighten a security group and observe the connection failing <br> &emsp; + Delete the VPC and its dependencies in the correct order | 19/06/2026 | 19/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/> |

### Week 3 Achievements:

* Can build a working VPC from an empty Region and explain the purpose of every component created.
* Can articulate the practical difference between a security group and a network ACL, and know that referencing a security group by ID is preferable to referencing an IP range.
* Understand where a gateway endpoint fits in an architecture that moves data between compute and S3.
* Aware of which networking components carry an hourly charge regardless of traffic, and why they must be deleted after practice.
