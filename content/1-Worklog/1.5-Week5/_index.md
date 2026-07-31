---
title: "Week 5 Worklog"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives: Caerus - Moving onto AWS

* Shift the database onto Amazon RDS and the static site onto Amazon S3, and get the API running on Amazon EC2.
* Reach an application the public can get to, then take out the single point of failure in the compute tier and in the database tier alike.
* Close the week with two EC2 instances behind a load balancer and a Multi-AZ database sitting in a private subnet.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Backend lane]** Launched a Single-AZ RDS PostgreSQL instance and ran the migration and seed files against it, with nothing changed but the connection string <br> - **[Frontend lane]** Created all four S3 buckets (frontend site, event posters, generated tickets, backend deployment package) | 13/07/2026 | 13/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3 | - **[Backend lane]** Created the EC2 instance role, scoped to just the S3 buckets it has to reach, in line with the account's naming convention <br> - **[Frontend lane]** Turned on static website hosting for the site bucket <br> - Together: went back over the security groups and narrowed them so the database takes traffic from the application instance's security group only, and not from arbitrary addresses | 14/07/2026 | 14/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - **[Backend lane]** Launched the EC2 instance, installed Node.js, and got the API running under `pm2` <br> - **[Frontend lane]** Built the production React bundle, published it to the site bucket, and pointed it at the EC2 API's public address <br> - Cleared the cross-origin failure we had expected by configuring permitted origins on the API, rather than working around it in the browser <br> - **Milestone reached:** the application live on AWS, on one EC2 instance | 15/07/2026 | 15/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 5 | - One EC2 instance amounts to a single point of failure: launched a second one in a different Availability Zone, on an identical AMI and configuration <br> - Created a target group and an Application Load Balancer in front of the pair, and tested traffic alternating between them before anything else was touched <br> - Only after the load balancer was confirmed working did we narrow the EC2 security group to take traffic from the load balancer's security group alone, and point the frontend at the load balancer's DNS name | 16/07/2026 | 16/07/2026 | |
| 6 | - Moved RDS to Multi-AZ inside a private subnet with no route out to the internet: created two private subnets plus a route table carrying no `0.0.0.0/0` route, and a new DB subnet group <br> - Ran into a known RDS console/CLI validation error when moving an existing instance's subnet group between VPC-adjacent subnet groups; got past it by deleting the instance - it held seed data only - and recreating it outright with Multi-AZ and the private subnet group chosen at creation time <br> - Spotted the Production template's default storage type (Provisioned IOPS SSD, 100 GiB) and put it back to General Purpose SSD at 20 GiB before creating anything - had it gone through untouched, it would have been the biggest line on the whole bill | 17/07/2026 | 17/07/2026 | |
| 7 | - Buffer: checked that the endpoint hostname had survived the RDS recreation unchanged, meaning `DATABASE_URL` needed no edit, and confirmed the database was still reachable from EC2 despite having no route to the internet whatsoever <br> - Ran the booking flow end to end once more against the rebuilt environment to confirm nothing had regressed | 18/07/2026 | 18/07/2026 | |

### Week 5 Achievements:

* Migrated off a local Docker database onto managed RDS, and off a local filesystem onto S3, by changing configuration instead of code.
* Reached a deployment the public can get to, then took out both of its single points of failure on purpose: a second EC2 instance behind a load balancer, and a Multi-AZ database.
* Diagnosed a genuine RDS console limitation and found a way around it rather than being halted by it, taking the quicker and equally sound path given the database held nothing but disposable seed data.
* Spotted a console default - Provisioned IOPS storage - that would have taken over the whole month's bill, and caught it before the resource was ever created.
