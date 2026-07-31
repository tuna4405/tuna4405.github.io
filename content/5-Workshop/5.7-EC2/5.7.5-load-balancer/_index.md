---
title : "Load Balancer and a Second Instance"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.7.5 </b> "
---

A single EC2 instance is a single point of failure - fine for the first
working deployment, worth improving once it is stable. This section adds a
second instance in a different Availability Zone and an Application Load
Balancer in front of both, without ever taking the first instance offline
while doing it.

1. **Launch a second instance**, `caerus-api-2`, identical to the first in
   AMI, instance type, `caerus-ec2-sg`, and IAM instance profile - the only
   difference is the subnet, chosen in a different Availability Zone than
   the first instance's subnet, so the load balancer actually gains
   something from having two targets. Deploy the same backend package to it
   (section 5.7.2) and confirm it answers `/api/v1/health` on its own public
   IP before touching anything shared.

2. **Create a security group for the load balancer**, `caerus-alb-sg`,
   admitting HTTP on port 80 from anywhere - this, not the EC2 security
   group, is now the system's actual public entry point.

3. **Create a target group**, `caerus-tg`, protocol HTTP, port 3000, health
   check path `/api/v1/health`, and register both instances.

4. **Create the Application Load Balancer**, `caerus-alb`, Internet-facing,
   spanning both instances' public subnets, security group `caerus-alb-sg`,
   with an HTTP:80 listener forwarding to `caerus-tg`. The load balancer
   itself stays in these public subnets permanently - it is the instances,
   not the load balancer, that move to private subnets later in section
   5.7.7.

5. **Test through the load balancer before changing anything else** - both
   instances still reachable directly at this point, so a load-balancer
   misconfiguration cannot take the application down while it is being
   debugged:

   ```bash
   curl http://<alb-dns-name>/api/v1/health
   ```

   Called repeatedly, traffic alternates between the two instances with no
   visible difference in the response - which is exactly the property being
   verified.

6. **Only once step 5 is confirmed working**, tighten `caerus-ec2-sg`: remove
   the rule admitting port 3000 from "My IP" and replace it with port 3000
   from **security group `caerus-alb-sg`**. From this point on, calling
   `http://<instance-ip>:3000` directly no longer works - by design, since
   the load balancer is now the only sanctioned path in.

{{% notice note %}}
Step 5 exists specifically so that if the load balancer is misconfigured,
the system degrades to "reachable the old way" rather than "down entirely"
while it gets fixed. Locking the security group before confirming the load
balancer works removes that safety net for no benefit.
{{% /notice %}}

7. **Point the frontend at the load balancer's DNS name** instead of either
   instance's IP (`VITE_API_BASE_URL=http://<alb-dns-name>/api/v1`), rebuild,
   and redeploy the site. The frontend now survives either instance being
   stopped, restarted, or replaced, since it never depended on a specific
   instance's IP address in the first place.

<!-- ![Target group showing both instances Healthy](/images/5-Workshop/5.7-EC2/5.7.5-load-balancer/example.png) -->
