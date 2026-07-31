---
title : "Amazon EC2 and Deployment"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Overview

The compute tier's network placement was a decision made before the first
instance ever launched, not a lesson learned afterward: private subnets, a
NAT gateway per Availability Zone, and Systems Manager for administration were
all in place before `caerus-server-1` existed, the same way section 5.5.1 put
RDS in a private subnet from its very first launch rather than as a later
migration. No instance in this project has ever had a public IP or an open
SSH port. The story told here is a practice launch on a personal IAM user,
the private networking built for the application tier, a single instance
deployed into it and administered entirely through Session Manager, a
security group that starts with no inbound rule at all because nothing yet
needs one, the frontend pointed at that instance through an SSM tunnel to
catch the inevitable CORS failure before any real traffic exists, and then a
second instance and an Application Load Balancer - the first component in
this whole chain that actually opens a path in from the public internet, on
purpose, in exactly one place. This is the section where the application
becomes reachable from anywhere, while the instances serving it stay
reachable from nowhere but that one load balancer.

#### Content

- [Launch and Terminate Practice](5.7.1-launch-practice/)
- [Private Networking and the First Instance](5.7.2-deploy-api/)
- [Lock Down Security Groups](5.7.3-security-groups/)
- [Frontend Build and CORS](5.7.4-frontend-and-cors/)
- [Load Balancer and a Second Instance](5.7.5-load-balancer/)
- [CloudFront: One HTTPS Domain for Everything](5.7.6-cloudfront/)

