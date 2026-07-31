---
title: "Week 3 Worklog"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives: Compute, Storage, and Managed Database Services

* Get to grips with EC2 instance sizing and storage, and with keeping a web process alive on an instance.
* Learn enough about S3 to put it to work for static hosting and for private uploads served through presigned URLs.
* Understand managed relational databases and the locking guarantees that a booking system leans on.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - EC2 fundamentals: <br> &emsp; + Instance families and the sizes that qualify for the Free Tier <br> &emsp; + Amazon Machine Images, key pairs, and SSH access <br> &emsp; + EBS volume types, and what becomes of a volume on stop as against terminate <br> - **Practice:** launched an instance, connected to it over SSH, installed Node.js, and kept a small HTTP server alive under a process manager | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 3 | - Load balancing and scaling concepts (studied at this point, not yet built): Elastic Load Balancing, target groups, health checks, and why one instance is a reasonable place for a small project to start from but not to finish at <br> - Terminated the practice instance that same day and checked that nothing had been left running | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 4 | - Amazon S3 fundamentals: <br> &emsp; + Buckets, objects, keys, and the flat namespace <br> &emsp; + Block Public Access and bucket policies <br> &emsp; + Static website hosting <br> &emsp; + Pre-signed URLs granting time-limited access to a private object, and CORS configuration on a bucket | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 5 | - Amazon RDS: <br> &emsp; + The engines on offer, the instance classes, and the work the managed service assumes on your behalf (patching, backups, failover) <br> &emsp; + Multi-AZ deployments, subnet groups, and public accessibility <br> &emsp; + VPC gateway endpoints for S3, and what keeping that traffic away from the public internet buys you in both security and cost | 02/07/2026 | 02/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 6 | - The database concepts a booking system rests on: <br> &emsp; + ACID properties and transaction isolation levels <br> &emsp; + Pessimistic locking via `SELECT ... FOR UPDATE`, taken in a consistent order so deadlocks do not arise, set against optimistic concurrency control <br> - **Practice:** launched an RDS PostgreSQL instance, connected to it from a local client, and created a table | 03/07/2026 | 03/07/2026 | <https://www.postgresql.org/docs/16/transaction-iso.html> |
| 7 | - Self-study: read up on AWS Config and Conformance Packs - recording how a resource is configured over time and checking it against rule sets - as a way of catching precisely the kind of console default (a bucket left public, a security group left open) that is easy to click straight past when moving at speed | 04/07/2026 | 04/07/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html> |

### Week 3 Achievements:

* Able to launch, configure, connect to, and terminate an EC2 instance, and to keep a Node.js process alive under a process manager.
* Able to set a bucket up for static hosting and to explain pre-signed URLs as a means of sharing private objects with no need to make a bucket public.
* Able to launch an RDS instance, connect to it, and describe what the managed service takes care of on its own.
* Able to explain ACID, isolation levels, and row-level locking, having identified `SELECT ... FOR UPDATE` as the mechanism the booking transaction calls for.
* Aware of which networking and compute components bill by the hour whatever the traffic, in advance of designing the deployed architecture.
