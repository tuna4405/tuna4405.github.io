---
title : "Launch the DB Instance"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

The database is deployed Multi-AZ, in a private subnet with no route to the
internet at all, from this first launch - not as a later upgrade. A
Single-AZ instance in whatever subnet the console defaults to would work for
a demo, but it carries a single point of failure and sits on a network path
that has no reason to exist, so there is no reason to build that version
first only to replace it.

#### Why a private subnet at all

"Public access: No" (used below) controls whether RDS assigns a public IP and
accepts connections from outside the VPC - it says nothing about which
*subnet* the instance sits in. An instance created against the default VPC's
subnets is, strictly speaking, sitting in a public subnet the whole time,
protected only by a security group. A genuinely private subnet - one whose
route table has no path to the internet at all - is a second, independent
layer of defence that holds even if a security group is ever misconfigured.

1. **Create two private subnets, one per Availability Zone** (RDS Multi-AZ
   requires a DB subnet group spanning at least two AZs): `caerus-private-1a`
   in `ap-southeast-1a`, `caerus-private-1b` in `ap-southeast-1b`, each with a
   CIDR block that does not overlap the VPC's other subnets.

![](/images/5-Workshop/5.5-RDS/2rds_private_subnet.png)

2. **Create a route table with no `0.0.0.0/0` route** and associate both new
   subnets with it - the *absence* of an internet-gateway route is what makes
   a subnet private, not the name given to it. No NAT gateway is needed here:
   RDS never initiates outbound internet traffic on its own.

![](/images/5-Workshop/5.5-RDS/caerus-private-rt.png)

3. **Create a DB subnet group**, `caerus-private-subnet-group`, from those two
   private subnets.

4. **RDS Console → Create database → Full configuration** (the other option,
   "Easy create", hides the Multi-AZ and private-subnet controls this section
   depends on behind "recommended" defaults chosen for you), then pick the
   engine. Two large buttons sit side by side here - **Amazon Aurora** and
   **PostgreSQL** - and it is genuinely easy to click the wrong one, including
   the option labelled "Aurora PostgreSQL Compatible", which is still Aurora.
   Pick plain **PostgreSQL**, version 16.x, to match the `postgres:16` image
   used locally.



5. **Templates: Production.** Multi-AZ is not available under the Free Tier
   template at all - Production is what unlocks it, and is required here.

6. **Availability and durability.** → Deployment options → Multi-AZ DB instance
   deployment (2 instances) - a primary plus one non-readable standby in a
   second AZ, matching the two private subnets from step 1.


![](/images/5-Workshop/5.5-RDS/rds_config.png)

7. **DB instance identifier**: `caerus-db`. **Credentials management:
   Self managed** - not AWS Secrets Manager, to avoid a service the project
   has no other use for. Record the master username and password
   immediately; the password is not recoverable from the Console afterwards.

8. **Connectivity**: same VPC as the application, **DB subnet group:
   `caerus-private-subnet-group`** (created in step 3), **Public access: No**,
   and a dedicated security group (`caerus-rds-sg`, created with no rules yet
   - the inbound rule admitting the application's security group is added
   once that security group exists, in section 5.7.3).

![](/images/5-Workshop/5.5-RDS/rds_connectivity.png)
