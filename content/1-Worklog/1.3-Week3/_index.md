---
title: "Week 3 Worklog"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives: Compute, Storage, and Managed Databases

* Understand EC2 instance sizing, storage, and how to keep a web process running on an instance.
* Understand S3 well enough to use it for static hosting and for private, presigned uploads.
* Understand managed relational databases and the locking guarantees a booking system depends on.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - EC2 fundamentals: <br> &emsp; + Instance families and Free Tier eligible sizes <br> &emsp; + Amazon Machine Images, key pairs, and SSH access <br> &emsp; + EBS volume types, and what happens to a volume on stop versus terminate <br> - **Practice:** launched an instance, connected over SSH, installed Node.js, and kept a small HTTP server alive under a process manager | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 3 | - Load balancing and scaling concepts (studied, not yet built): Elastic Load Balancing, target groups, health checks, and why a single instance is an acceptable starting point for a small project but not an ending point <br> - Terminated the practice instance the same day and confirmed nothing was left running | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 4 | - Amazon S3 fundamentals: <br> &emsp; + Buckets, objects, keys, and the flat namespace <br> &emsp; + Block Public Access and bucket policies <br> &emsp; + Static website hosting <br> &emsp; + Pre-signed URLs for time-limited access to a private object, and CORS configuration on a bucket | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 5 | - Amazon RDS: <br> &emsp; + Supported engines, instance classes, and what the managed service takes over (patching, backups, failover) <br> &emsp; + Multi-AZ deployments, subnet groups, and public accessibility <br> &emsp; + VPC gateway endpoints for S3, and why keeping that traffic off the public internet matters for both security and cost | 02/07/2026 | 02/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 6 | - Database concepts underpinning a booking system: <br> &emsp; + ACID properties and transaction isolation levels <br> &emsp; + Pessimistic locking with `SELECT ... FOR UPDATE`, ordered consistently to avoid deadlocks, versus optimistic concurrency control <br> - **Practice:** launched an RDS PostgreSQL instance, connected from a local client, and created a table | 03/07/2026 | 03/07/2026 | <https://www.postgresql.org/docs/16/transaction-iso.html> |
| 7 | - Self-study: read about AWS Config and Conformance Packs - recording a resource's configuration over time and evaluating it against rule sets - as a way to catch exactly the kind of console default (a bucket left public, a security group left open) that is easy to click past when moving quickly | 04/07/2026 | 04/07/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html> |

### Week 3 Achievements:

* Can launch, configure, connect to, and terminate an EC2 instance, and can keep a Node.js process running under a process manager.
* Can configure a bucket for static hosting and explain pre-signed URLs as a way to share private objects without making a bucket public.
* Can launch an RDS instance, connect to it, and explain what the managed service handles automatically.
* Can explain ACID, isolation levels, and row-level locking, and identified `SELECT ... FOR UPDATE` as the mechanism the booking transaction needs.
* Aware of which networking and compute components carry an hourly charge regardless of traffic, ahead of designing the deployed architecture.
