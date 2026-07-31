---
title : "Alarms and Notifications"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---

1. **Create an SNS topic**, `caerus-alerts`, Standard type, with an email
   subscription per team member - each must individually click the
   confirmation link AWS emails, or SNS silently never delivers to that
   address.

2. **Three alarms, each pointed at `caerus-alerts`:**

   | Alarm | Metric | Condition |
   |---|---|---|
   | `caerus-alb-unhealthy-host` | `UnHealthyHostCount` (`caerus-tg`) | ≥ 1 for 2 of 3 datapoints (1 min) |
   | `caerus-rds-low-storage` | `FreeStorageSpace` (`caerus-db`) | < 2 GB (5 min) |
   | `caerus-rds-high-cpu` | `CPUUtilization` (`caerus-db`) | > 80% for 2 of 3 datapoints (5 min) |

   The first replaces what would otherwise be a per-instance EC2 status
   check alarm: with two instances behind a load balancer, "is the target
   group healthy" is the signal that actually matters, since one instance
   failing no longer takes the application down on its own.

3. **Prove at least one alarm actually fires and notifies**, rather than
   leaving all three sitting in OK state, which proves only that the alarm
   was created, not that it works. Stopping one EC2 instance briefly is
   enough to move `caerus-alb-unhealthy-host` into `ALARM` and trigger the
   email.

{{% notice warning %}}
An alarm that has only ever been observed in the OK state is unverified, not
working - the OK state is also what a completely misconfigured alarm looks
like. Capture both the alarm's `ALARM` state in the console and the
resulting email as evidence; either one alone is a weaker claim than both
together.
{{% /notice %}}

<!-- ![Alarm in ALARM state in the console, alongside the SNS notification email](/images/5-Workshop/5.8-CloudWatch/5.8.2-alarms/example.png) -->
