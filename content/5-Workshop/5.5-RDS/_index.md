---
title : "Amazon RDS for PostgreSQL"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Overview

Moving the database off a local container is, in the simple case, changing a
connection string: the same `001_init.sql` and `seed.sql` files run unchanged
against a managed RDS instance. This section covers that simple case first
(sections 5.5.1-5.5.3), then goes further than the original plan required:
enabling Multi-AZ for automatic failover and moving the instance into a
private subnet with no route to the internet at all (section 5.5.4) - and is
honest about the AWS quirks that made the second part harder than it should
have been.

#### Content

- [Launch the DB Instance](5.5.1-launch-instance/)
- [Run the Migration and Seed](5.5.2-migrate-and-seed/)
- [Verify the Connection](5.5.3-verify/)
- [Multi-AZ and the Private Subnet](5.5.4-multi-az-and-private-subnet/)

