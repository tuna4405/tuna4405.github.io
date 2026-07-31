---
title : "Testing"
date : 2026-06-01
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Overview

Every test in this section runs against the deployed system - through
CloudFront, across the load balancer, against the real RDS instance - rather
than localhost, because the guarantee being tested is precisely the one that
only matters under real infrastructure: two independent clients, arriving at
whichever of the two EC2 instances the load balancer happens to route them
to, competing for the same database row at the same instant.



#### Content

- [The Double-Booking Test](5.9.1-concurrency/)
- [Edge Cases](5.9.2-edge-cases/)

