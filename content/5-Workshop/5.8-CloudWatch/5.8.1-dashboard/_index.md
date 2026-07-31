---
title : "Build the Dashboard"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.8.1 </b> "
---

1. **CloudWatch Console → Dashboards → Create dashboard**, name
   `caerus-overview`.

2. **Add one widget per metric**, six in total:

   | Widget | Metric | Type |
   |---|---|---|
   | EC2 CPU | `CPUUtilization`, both instances in one widget | Line |
   | RDS CPU | `CPUUtilization` (`caerus-db`) | Line |
   | RDS Connections | `DatabaseConnections` | Line |
   | RDS Free Storage | `FreeStorageSpace` | Line |
   | ALB Requests | `RequestCount` (`caerus-alb`, under "Per AppELB Metrics" - the un-split-by-AZ total) | Line |
   | ALB Target Health | `HealthyHostCount` + `UnHealthyHostCount` (`caerus-tg`), same widget | Line |

3. **Generate real traffic before taking the screenshot** - browse the site,
   book a few seats, download a ticket - a dashboard of perfectly flat lines
   demonstrates that widgets were added, not that anything is actually being
   observed.

<!-- ![caerus-overview dashboard with real traffic on every widget](/images/5-Workshop/5.8-CloudWatch/5.8.1-dashboard/example.png) -->
