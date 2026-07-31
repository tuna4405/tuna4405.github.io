---
title : "Amazon EC2 and Deployment"
date : 2026-06-01
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Overview

The compute story, told in the order it actually happened: a practice launch
on a personal IAM user, a single EC2 instance deployed and reachable, a
security group tightened around it, the frontend pointed at it with the
resulting CORS failure fixed - and only then, once that was solid, a second
instance and an Application Load Balancer in front of both, Amazon CloudFront
in front of the load balancer and the S3 site bucket together, and finally the
instances themselves moved off the public internet entirely, into private
subnets behind a NAT gateway and administered through Systems Manager instead
of SSH. This is the section where the application stops being reachable only
from the developer's own machine and becomes reachable from anywhere - while
the instances serving it become reachable from nowhere but the load balancer.

#### Content

- [Launch and Terminate Practice](5.7.1-launch-practice/)
- [Deploy the API with pm2](5.7.2-deploy-api/)
- [Lock Down Security Groups](5.7.3-security-groups/)
- [Frontend Build and CORS](5.7.4-frontend-and-cors/)
- [Load Balancer and a Second Instance](5.7.5-load-balancer/)
- [CloudFront: One HTTPS Domain for Everything](5.7.6-cloudfront/)
- [Private Subnets, NAT, and Systems Manager](5.7.7-private-subnet-nat/)

