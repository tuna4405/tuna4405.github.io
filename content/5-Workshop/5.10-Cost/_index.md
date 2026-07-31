---
title : "Cost and Resource Management"
date : 2026-06-01
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

#### Overview

The final architecture does not fit inside the twelve-month Free Tier, and it
is not trying to. RDS runs Multi-AZ in a private subnet from its very first
launch (section 5.5.1), the compute tier gets its own private subnets and a
NAT gateway per Availability Zone before its very first instance ever exists
(section 5.7.2), and CloudFront fronts the frontend bucket from its first
deployed build (section 5.6.2) - none of it is a later upgrade bolted onto a
cheaper starting point. What genuinely gets added once the first working
deployment is solid is redundancy in the compute layer - a second EC2
instance and an Application Load Balancer (section 5.7.5) - plus a second
CloudFront behavior and WAF routing the API through the same distribution
(section 5.7.6). Together, all of it trades Free Tier headroom for a property
a real deployment would actually want: automatic database failover, no single
point of failure in the compute layer, a single HTTPS domain with edge
protection, and a compute tier with no inbound port open to the internet at
all. The honest number is a real monthly bill, not a rounding error, and it
is worth stating plainly rather than letting the report imply the system is
still costless.

**The real recurring cost, by component:**

| Component | Why it costs, regardless of traffic | Rough cost |
|---|---|---|
| NAT Gateway (×2, one per AZ) | Billed hourly per gateway, plus per-GB data processing charges | ~US$65-70/month baseline |
| Application Load Balancer | Billed hourly, plus LCU-hours under load | ~US$16/month baseline |
| RDS Multi-AZ | The standby instance is billed identically to the primary - Free Tier only ever covered Single-AZ | roughly double a Single-AZ instance of the same class |
| Amazon EC2 (×2) | Combined hours of two instances exceed the Free Tier's 750-hour allowance once both run a full month | modest, instance-class dependent |
| Amazon CloudFront + WAF | Usage-based: per GB transferred, per 10,000 HTTPS requests, per WAF rule evaluated | a few dollars at this project's demonstration traffic |
| Amazon S3 (4 buckets) | Well under the Free Tier's 5 GB allowance at this data volume | negligible |
| Data transfer | Nominal at demonstration traffic levels | negligible |

**Estimated total: roughly US$120-145/month.** The two NAT gateways and the
load balancer alone account for over half of it, and all three bill at the
same hourly rate whether the application serves real traffic or sits
completely idle - this is the direct cost of the private-networking decision
in section 5.7.2 and the load-balancing decision in section 5.7.5, not a
surprise to be discovered later. Running one shared NAT gateway instead of
one per AZ would recover roughly US$32-35/month of that, at the cost of the
Multi-AZ-consistent egress story described in section 5.7.2 - a trade worth
naming explicitly rather than defaulting into silently.

**The single most expensive mistake avoided, not made:** the RDS Multi-AZ
console wizard's Production template defaults storage to **Provisioned IOPS
SSD (io2)** at 100 GiB rather than the General Purpose SSD (gp3) used
throughout the rest of this project - io2 bills separately for storage and
for every provisioned IOPS, and left unchanged at that default would have
been the largest line on the entire bill by a wide margin, larger than the
NAT gateway and load balancer combined. Caught and corrected before creating
the instance in section 5.5.1; worth stating explicitly here because it is
the kind of console default that is easy to click past without reading.

**Governance that kept this visible rather than surprising:** every resource
created carries the `Owner` tag from section 5.2, so Cost Explorer grouped by
that tag attributes any spike to a specific developer within seconds. The
billing alarm threshold was raised from a token guardrail value to roughly
**US$150** - above the expected run-rate, so it still fires on a genuine
mistake (a third NAT gateway left running, an oversized RDS class, a leftover
unattached Elastic IP) without also firing on ordinary, expected operation
the way a US$10 alarm would at this cost profile.

<!-- ![Cost Explorer filtered by the Owner tag, showing the ALB and RDS Multi-AZ lines](/images/5-Workshop/5.10-Cost/example.png) -->
