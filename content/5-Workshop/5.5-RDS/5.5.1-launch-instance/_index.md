---
title : "Launch the DB Instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

**RDS Console → Create database.**

1. **Standard create**, then pick the engine. Two large buttons sit side by
   side here - **Amazon Aurora** and **PostgreSQL** - and it is genuinely easy
   to click the wrong one, including the option labelled "Aurora PostgreSQL
   Compatible", which is still Aurora. Pick plain **PostgreSQL**, version
   16.x, to match the `postgres:16` image used locally.

   {{% notice warning %}}
   Aurora and RDS PostgreSQL are billed and managed differently. If a later
   step behaves unexpectedly and a component is named `Aurora` where
   `PostgreSQL` was expected, this is the step to revisit.
   {{% /notice %}}

2. **Templates: Free Tier** for this first launch (section 5.5.4 later
   switches to Production, which is a separate, deliberate step - Free Tier
   caps the instance class and disables Multi-AZ outright).

3. **DB instance identifier**: `caerus-db`. **Credentials management:
   Self managed** - not AWS Secrets Manager, to keep the setup within Free
   Tier and avoid a service the project has no other use for. Record the
   master username and password immediately; the password is not recoverable
   from the Console afterwards.

4. **Connectivity**: same VPC as the application, **Public access: No**, and
   a dedicated security group (`caerus-rds-sg`, created with no rules yet -
   the inbound rule admitting the application's security group is added once
   that security group exists, in section 5.7.3).

5. **Additional configuration → Initial database name: `caerus`.** Filling
   this in now avoids a manual `CREATE DATABASE` after the instance is
   available.

6. **Tag `Owner`** with the creating developer's name, then create the
   database and wait for status **Available** (a few minutes for Single-AZ).

<!-- ![RDS create-database wizard: engine choice, instance class, connectivity panel](/images/5-Workshop/5.5-RDS/5.5.1-launch-instance/example.png) -->
