---
title : "Cleaning Up Resources"
date : 2026-06-01
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Overview

Teardown has to happen in dependency order, or the console simply refuses
the delete - a security group still referenced by a running instance, or a
target group still attached to a load balancer, cannot be removed out of
turn. The order below is outermost-to-innermost: the CDN and load-balancing
layer added last in section 5.7 comes down first, compute and data come
down next, and the IAM/monitoring scaffolding that everything else depended
on comes down last.

1. **CloudFront**: select the distribution, **Disable**, wait for the status
   to reach *Deployed* in its disabled state, then **Delete**. Delete the
   Web ACL (WAF) and the Origin Access Control afterward if nothing else
   references them.
2. **Application Load Balancer and target group**: delete the load balancer
   first, then the now-unattached target group - the console enforces this
   order.
3. **Both EC2 instances**: terminate, then confirm in the console that
   neither remains in any state (including "stopped," which still incurs
   EBS storage cost even while not billing for compute). Also **deregister
   the AMIs** created in section 5.7.7 and **delete their backing EBS
   snapshots** - an AMI and its snapshot keep costing storage indefinitely
   after the instance they were cloned from is gone, and are easy to forget
   precisely because they were a one-time step rather than something
   revisited regularly.
4. **NAT Gateway**: delete `caerus-nat` first, then **release its Elastic
   IP** once the gateway is gone - an EIP not attached to anything running
   bills continuously, unlike one attached to an active NAT gateway or
   instance.
5. **RDS**: delete the instance. If the data is disposable seed data, skip
   the final snapshot; if it should be preserved, take one explicitly rather
   than relying on the default.
6. **S3 buckets**: empty each of the four buckets (a non-empty bucket
   cannot be deleted), then delete the buckets themselves.
7. **IAM**: delete `caerus-ec2-s3-role`.
8. **CloudWatch and SNS**: delete the alarms, the dashboard, the
   `/caerus/ec2/api` log group, and the `caerus-alerts` SNS topic (email
   subscriptions are removed automatically with the topic).
9. **Security groups**: `caerus-alb-sg`, `caerus-ec2-sg`, `caerus-rds-sg` -
   deletable now that nothing references them.
10. **VPC additions**: both private subnet pairs - the database's from
    section 5.5.4 and the application tier's from section 5.7.7 - along
    with their two route tables and the DB subnet group. None of these cost
    anything to leave in place on their own, but remove them too for a
    genuinely clean account if the project is fully finished rather than
    paused.

**Finish with a screenshot proving the account is clean** - EC2 "Instances"
showing none running, RDS "Databases" empty, and the CloudFront and Load
Balancer consoles both showing zero resources - the strongest possible
evidence that nothing is quietly accruing cost after the report is
submitted.

<!-- ![EC2, RDS, and CloudFront consoles all showing no resources remaining](/images/5-Workshop/5.11-Cleanup/example.png) -->
