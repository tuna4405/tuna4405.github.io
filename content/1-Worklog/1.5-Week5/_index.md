---
title: "Week 5 Worklog"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives: Caerus - Deploying to AWS

* Move the database to Amazon RDS and the static site to Amazon S3, and deploy the API to Amazon EC2.
* Reach a publicly reachable application, then remove the single point of failure in both the compute and the database tier.
* End the week with two EC2 instances behind a load balancer and a Multi-AZ database in a private subnet.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Backend lane]** Launched a Single-AZ RDS PostgreSQL instance and ran the migration and seed files against it, changing only the connection string <br> - **[Frontend lane]** Created all four S3 buckets (frontend site, event posters, generated tickets, backend deployment package) | 13/07/2026 | 13/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3 | - **[Backend lane]** Created the EC2 instance role scoped to the S3 buckets it needs, following the account's naming convention <br> - **[Frontend lane]** Enabled static website hosting on the site bucket <br> - Together: reviewed and tightened the security groups so the database accepts traffic only from the application instance's security group, not from arbitrary addresses | 14/07/2026 | 14/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - **[Backend lane]** Launched the EC2 instance, installed Node.js, and deployed the API under `pm2` <br> - **[Frontend lane]** Built the production React bundle, published it to the site bucket, and pointed it at the EC2 API's public address <br> - Resolved the expected cross-origin failure by configuring permitted origins on the API rather than working around it in the browser <br> - **Milestone reached:** the application live on AWS, on a single EC2 instance | 15/07/2026 | 15/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 5 | - A single EC2 instance is a single point of failure: launched a second instance in a different Availability Zone, identical AMI and configuration <br> - Created a target group and an Application Load Balancer in front of both instances, tested traffic alternating between them before touching anything else <br> - Only once the load balancer was confirmed working, tightened the EC2 security group to accept traffic from the load balancer's security group alone, and pointed the frontend at the load balancer's DNS name | 16/07/2026 | 16/07/2026 | |
| 6 | - Moved RDS to Multi-AZ inside a private subnet with no route to the internet: created two private subnets and a route table with no `0.0.0.0/0` route, and a new DB subnet group <br> - Hit a known RDS console/CLI validation error moving an existing instance's subnet group across VPC-adjacent subnet groups; resolved by deleting the (seed-data-only) instance and recreating it directly with Multi-AZ and the private subnet group selected at creation <br> - Caught and corrected the Production template's default storage type (Provisioned IOPS SSD, 100 GiB) back to General Purpose SSD at 20 GiB before creating - left unchanged, it would have been the largest line on the entire bill | 17/07/2026 | 17/07/2026 | |
| 7 | - Buffer: verified the endpoint hostname was unchanged after the RDS recreation, so `DATABASE_URL` needed no change, and confirmed the database was still reachable from EC2 despite having no route to the internet at all <br> - Re-ran the booking flow end to end against the rebuilt environment to confirm nothing regressed | 18/07/2026 | 18/07/2026 | |

### Week 5 Achievements:

* Migrated from a local Docker database to managed RDS, and from a local filesystem to S3, by changing configuration rather than code.
* Reached a publicly reachable deployment, then deliberately removed its two single points of failure: a second EC2 instance behind a load balancer, and a Multi-AZ database.
* Diagnosed and worked around a genuine RDS console limitation rather than being stopped by it, choosing the faster, equally valid path given the database held only disposable seed data.
* Caught a console default (Provisioned IOPS storage) that would have dominated the entire month's bill, before it was ever created.
