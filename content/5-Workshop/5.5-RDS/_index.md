---
title : "Amazon RDS for PostgreSQL"
date : 2026-06-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Overview

This section is infrastructure only: standing up a managed PostgreSQL
instance with the properties a real deployment needs - Multi-AZ for
automatic failover, and a private subnet with no route to the internet at
all - rather than starting simple and upgrading later. Nothing runs against
the database yet; there is no compute anywhere in the VPC able to reach it
until section 5.7 exists, and running `001_init.sql` and `seed.sql` from a
developer's own machine would mean opening `caerus-rds-sg` to a personal IP
just to throw the rule away afterward. That step - and the first real
end-to-end check that the schema and seed data actually work - happens once,
from inside the application instance itself, in section 5.7.7.

#### Content

- [Launch the DB Instance](5.5.1-launch-instance/)

