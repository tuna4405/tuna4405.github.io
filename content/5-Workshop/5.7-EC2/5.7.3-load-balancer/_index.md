---
title : "Load Balancer and a Second Instance"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.7.3 </b> "
---

1. **Launch a second instance**, `caerus-server-2`, identical to
   `caerus-server-1` in AMI, instance type, `caerus-ec2-sg`, and IAM instance
   profile - the only difference is the subnet, `caerus-app-private-1b`
   (section 5.7.2), in the other Availability Zone, so the load balancer
   actually gains something from having two targets and each AZ keeps its own
   independent NAT gateway. Deploy the same backend package to it (section
   5.7.2) and confirm it answers `/api/v1/health` from inside its own Session
   Manager session before touching anything shared.

2. **Create a security group for the load balancer**, `caerus-alb-sg`,
   admitting HTTP on port 80 from anywhere - this is the system's actual
   public entry point, and the only one that will ever exist.

3. **Create a target group**, `caerus-tg`, protocol HTTP, port 3000, health
   check path `/api/v1/health`, and register both instances.

4. **Create the Application Load Balancer**, `caerus-alb`, Internet-facing,
   spanning **public** subnets in both Availability Zones (the load balancer
   itself lives in the public subnets alongside the NAT gateways - it is the
   one component in this chain that is supposed to be reachable directly from
   the internet), security group `caerus-alb-sg`, with an HTTP:80 listener
   forwarding to `caerus-tg`.

5. **Add the one rule `caerus-ec2-sg` has been waiting for** (section 5.7.3
   left it empty on purpose): Custom TCP, port 3000, source **security group
   `caerus-alb-sg`**. This is not tightening an existing rule - it is the
   first and only inbound rule this security group will ever carry, and
   nothing before this moment could reach port 3000 on either instance
   without already being inside a Session Manager session.

6. **Test through the load balancer**:

   ```bash
   curl http://<alb-dns-name>/api/v1/health
   ```

   Called repeatedly, traffic alternates between the two instances with no
   visible difference in the response - which is exactly the property being
   verified. This is also the first `curl` in the entire project to reach the
   compute tier from a completely ordinary terminal, no AWS CLI, no SSM
   tunnel, no active session required.

7. **Point the frontend at the load balancer's DNS name** instead of the
   local SSM tunnel from section 5.7.4 (`VITE_API_BASE_URL=http://<alb-dns-name>/api/v1`),
   rebuild, upload to `caerus-frontend-web`, and invalidate the CloudFront
   cache (section 5.6.2) so the change is actually served. The
   `allowedOrigins` list in `app.js` needs no change - the distribution
   domain was already added to it in section 5.7.4, before the load balancer
   even existed to serve it. Reload the site at its CloudFront domain: the
   CORS problem diagnosed and fixed locally in section 5.7.4 does not
   resurface, because it was never actually specific to the tunnel - it was
   the same origin check, exercised early on cheaper infrastructure.

<!-- ![Target group showing both instances Healthy, and the CloudFront-hosted frontend successfully calling the API through the load balancer](/images/5-Workshop/5.7-EC2/5.7.5-load-balancer/example.png) -->
