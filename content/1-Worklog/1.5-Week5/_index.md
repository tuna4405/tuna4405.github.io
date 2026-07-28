---
title: "Week 5 Worklog"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives: Storage and Databases

* Understand S3 well enough to use it for static hosting, user uploads, and generated files.
* Understand managed relational databases and how they differ from a self-managed installation.
* Understand the relational versus NoSQL trade-off in terms of the guarantees each provides.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Amazon S3 fundamentals: <br> &emsp; + Buckets, objects, keys, and the flat namespace <br> &emsp; + Storage classes and lifecycle rules <br> &emsp; + Versioning and durability | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 3 | - S3 access control and delivery: <br> &emsp; + Block Public Access, bucket policies, and why ACLs are discouraged <br> &emsp; + Static website hosting <br> &emsp; + Pre-signed URLs for time-limited access to private objects <br> &emsp; + CORS configuration on a bucket <br> - **Practice:** host a static page from a bucket and reach it at the website endpoint | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - Amazon RDS: <br> &emsp; + Supported engines and instance classes <br> &emsp; + What the managed service takes over: patching, backups, failover <br> &emsp; + Multi-AZ deployments versus read replicas <br> &emsp; + Automated backups, manual snapshots, and point-in-time recovery <br> &emsp; + Subnet groups, parameter groups, and public accessibility | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 5 | - Database concepts underpinning a booking system: <br> &emsp; + ACID properties and what each guarantees <br> &emsp; + Transaction isolation levels and the anomalies each prevents <br> &emsp; + Pessimistic locking with `SELECT ... FOR UPDATE` versus optimistic concurrency control <br> &emsp; + Deadlocks, and how consistent lock ordering avoids them | 02/07/2026 | 02/07/2026 | <https://www.postgresql.org/docs/16/transaction-iso.html> |
| 6 | - Amazon DynamoDB and the NoSQL comparison: <br> &emsp; + Tables, items, partition keys, and sort keys <br> &emsp; + On-demand versus provisioned capacity <br> &emsp; + Where a key-value store is the better fit, and where transactional integrity forces a relational choice <br> - **Practice:** launch an RDS instance, connect from a local client, and create a table | 03/07/2026 | 03/07/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/> |

### Week 5 Achievements:

* Can configure a bucket for static website hosting and explain the access-control decisions involved.
* Understand pre-signed URLs as a way to share private objects without making a bucket public.
* Can launch an RDS instance and connect to it, and can explain what the managed service handles on my behalf.
* Can explain ACID, isolation levels, and row-level locking, and identified `SELECT ... FOR UPDATE` as the mechanism a seat booking system needs.
* Can argue the relational versus NoSQL choice from the guarantees required rather than from general preference.
