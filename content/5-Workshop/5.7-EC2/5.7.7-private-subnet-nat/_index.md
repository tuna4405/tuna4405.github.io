---
title : "Private Subnets, NAT, and Systems Manager"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7.7 </b> "
---

The load balancer already narrowed `caerus-ec2-sg` to admit traffic from
nothing but `caerus-alb-sg` (section 5.7.5), which means the instances no
longer need a public IP to serve traffic at all - the load balancer is the
only thing that ever calls them directly. This section removes the public IP
and the open SSH port outright, giving the compute tier the same "no route to
the internet" property the database already got in section 5.5.4.

#### Why a separate subnet pair, not the database's

RDS's private subnets (section 5.5.4) were built with a route table that has
**no** `0.0.0.0/0` route at all - correct for RDS, which never initiates
outbound traffic, but wrong for EC2, which needs outbound access for `npm ci`
and OS patching. Reusing the database's subnets would mean adding an internet
route to a route table that was deliberately built without one, muddying a
design decision that was correct as written. A second, separate private
subnet pair - one per Availability Zone, matching the two instances - keeps
the two tiers' network policies independent even though both end up "private"
in the everyday sense of the word.

1. **Create two private subnets for the application tier**,
   `caerus-private-app-a` (same AZ as `caerus-api-1`) and
   `caerus-private-app-b` (same AZ as `caerus-api-2`), CIDR blocks that don't
   overlap any existing subnet, including RDS's.

2. **Create a NAT Gateway**, `caerus-nat`, in one of the **public** subnets
   (where the ALB already lives) - a NAT gateway must sit in a subnet with a
   route to an Internet Gateway, never in a private one. Allocate a new
   Elastic IP for it at creation time.

3. **Create a route table for the app-tier private subnets**, with a
   `0.0.0.0/0` route pointing at `caerus-nat` - this is the opposite of RDS's
   route table, and the entire reason the two tiers use separate subnets.
   Associate both new subnets with it.

{{% notice note %}}
A single NAT gateway, not one per Availability Zone, is a deliberate cost
trade-off here: two NAT gateways would double the hourly charge for
redundancy that only protects *outbound* traffic (package installs, patching)
- it has no effect on the availability of client-facing requests, which still
flow entirely through the load balancer regardless of which AZ's NAT gateway
is up. If the AZ holding the NAT gateway has an outage, the instance in the
other AZ temporarily loses outbound internet access but keeps serving traffic
normally.
{{% /notice %}}

4. **Grant Systems Manager permission** on the EC2 instance role
   (`caerus-ec2-s3-role`): attach the AWS-managed policy
   `AmazonSSMManagedInstanceCore`. This is what lets the SSM Agent already
   running on Amazon Linux register itself and accept commands - entirely
   over an outbound connection the NAT gateway now provides, no inbound rule
   needed.

5. **Reuse the running instances' exact setup rather than redeploying from
   scratch**: EC2 Console → select `caerus-api-1` → **Actions → Image and
   templates → Create image**. This snapshots the instance's disk - Node.js,
   the deployed code, `pm2`'s process list - into an AMI that can be launched
   directly into the new private subnets.

6. **Launch a replacement instance from that AMI** into
   `caerus-private-app-a`, same instance type, same `caerus-ec2-sg`, same IAM
   instance profile (now carrying the SSM permission from step 4). Repeat for
   a second replacement in `caerus-private-app-b`, from an AMI of
   `caerus-api-2`.

7. **Verify access before touching the old instances**: EC2 Console →
   select a new instance → **Connect → Session Manager → Connect**, or
   `aws ssm start-session --target <instance-id>` from a terminal with the
   AWS CLI configured. Confirm `pm2 list` shows the API running, and
   `curl localhost:3000/api/v1/health` answers from inside the session.

8. **Register both new instances with `caerus-tg`** and wait for both to
   report **Healthy** before deregistering the old ones - the same
   zero-downtime pattern used when the second instance was first added in
   section 5.7.5.

9. **Remove the SSH rule from `caerus-ec2-sg` entirely** (no replacement
   rule - Session Manager needs no inbound port at all), then terminate the
   two original public-subnet instances once the replacements have carried
   real traffic without incident.

{{% notice warning %}}
Do this step last, deliberately. Removing SSH access is irreversible for the
*old* instances the moment you terminate them - if something is wrong with
the new instances' Session Manager access, you want the old ones still
reachable as a fallback until you've actually confirmed the replacements work,
not just launched.
{{% /notice %}}

10. **Deregister and deregister-then-delete the AMIs** once both replacement
    instances have been running reliably for a while - an AMI and its backing
    EBS snapshot both continue to cost storage even though the instance it
    was cloned from no longer exists, so this is a real, easy-to-forget line
    item (see [Cost and Resource Management](/5-Workshop/5.10-Cost/)).

After this section, `<instance-public-ip>:3000` no longer resolves to
anything - by design. The only ways into the compute tier are the load
balancer (for application traffic) and Session Manager (for administration),
neither of which depends on either instance having a public IP.

<!-- ![Session Manager connected to a private-subnet instance, alongside the target group showing both replacement instances Healthy](/images/5-Workshop/5.7-EC2/5.7.7-private-subnet-nat/example.png) -->
