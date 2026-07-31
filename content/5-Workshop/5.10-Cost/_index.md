---
title : "Cost and Resource Management"
date : 2026-06-01
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

#### Overview

The final architecture does not fit inside the twelve-month Free Tier, and it
is not trying to. Every extension built after the first working deployment -
Multi-AZ RDS (section 5.5.4), the Application Load Balancer (section 5.7.5),
CloudFront and WAF (section 5.7.6), and the NAT gateway for private-subnet EC2
(section 5.7.7) - trades Free Tier headroom for a property a real deployment
would actually want: automatic database failover, no single point of failure
in the compute layer, a single HTTPS domain with edge protection, and a
compute tier with no inbound port open to the internet at all. The honest
number is a real monthly bill, not a rounding error, and it is worth stating
plainly rather than letting the report imply the system is still costless.

**The real recurring cost, by component:**

| Component | Why it costs, regardless of traffic | Rough cost |
|---|---|---|
| NAT Gateway | Billed hourly, plus per-GB data processing charges | ~US$32-35/month baseline |
| Application Load Balancer | Billed hourly, plus LCU-hours under load | ~US$16/month baseline |
| RDS Multi-AZ | The standby instance is billed identically to the primary - Free Tier only ever covered Single-AZ | roughly double a Single-AZ instance of the same class |
| Amazon EC2 (×2) | Combined hours of two instances exceed the Free Tier's 750-hour allowance once both run a full month | modest, instance-class dependent |
| Amazon CloudFront + WAF | Usage-based: per GB transferred, per 10,000 HTTPS requests, per WAF rule evaluated | a few dollars at this project's demonstration traffic |
| Amazon S3 (4 buckets) | Well under the Free Tier's 5 GB allowance at this data volume | negligible |
| Data transfer | Nominal at demonstration traffic levels | negligible |

**Estimated total: roughly US$90-110/month.** The NAT gateway and the load
balancer alone account for over half of it, and both bill at the same hourly
rate whether the application serves real traffic or sits completely idle -
this is the direct cost of the private-subnet and load-balancing decisions in
sections 5.7.5 and 5.7.7, not a surprise to be discovered later.

**The single most expensive mistake avoided, not made:** the RDS Multi-AZ
console wizard's Production template defaults storage to **Provisioned IOPS
SSD (io2)** at 100 GiB rather than the General Purpose SSD (gp3) used
throughout the rest of this project - io2 bills separately for storage and
for every provisioned IOPS, and left unchanged at that default would have
been the largest line on the entire bill by a wide margin, larger than the
NAT gateway and load balancer combined. Caught and corrected before creating
the instance in section 5.5.4; worth stating explicitly here because it is
the kind of console default that is easy to click past without reading.

**Governance that kept this visible rather than surprising:** every resource
created carries the `Owner` tag from section 5.2, so Cost Explorer grouped by
that tag attributes any spike to a specific developer within seconds. The
billing alarm threshold was raised from a token guardrail value to roughly
**US$150** - above the expected run-rate, so it still fires on a genuine
mistake (a second NAT gateway, an oversized RDS class, a forgotten AMI's EBS
snapshot) without also firing on ordinary, expected operation the way a
US$10 alarm would at this cost profile.

<!-- ![Cost Explorer filtered by the Owner tag, showing the ALB and RDS Multi-AZ lines](/images/5-Workshop/5.10-Cost/example.png) -->
