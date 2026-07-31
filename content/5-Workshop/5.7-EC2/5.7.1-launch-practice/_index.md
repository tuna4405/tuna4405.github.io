---
title : "Launch and Terminate Practice"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.7.1 </b> "
---

Before touching the project's own instances, each team member ran this
exercise independently on their own IAM user, purely to build the muscle
memory of launch, connect, and clean up.

1. **Launch** a Free Tier eligible Amazon Linux instance, connect over EC2
   Instance Connect, and install Node.js.
2. **Run something real on it** - a small HTTP server reachable from a
   browser at the instance's public IP, confirming the security group's
   inbound rule actually admits the traffic it claims to.
3. **Terminate it the same day**, then return to the Console and confirm
   nothing is left running - no instance, no orphaned Elastic IP.

{{% notice note %}}
An unattached Elastic IP and a running instance both bill by the hour
regardless of whether anyone is using them. "Terminate practice resources
the same day" is not a suggestion in this project, it is the single habit
that keeps a two-person team's Free Tier usage inside the 750-hour monthly
allowance, since that allowance is shared across every instance running
simultaneously, not granted per instance.
{{% /notice %}}

<!-- ![Instance running, then the same instance's terminated state in the Console](/images/5-Workshop/5.7-EC2/5.7.1-launch-practice/example.png) -->
