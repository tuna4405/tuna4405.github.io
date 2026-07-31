---
title : "Cleaning Up Resources"
date : 2026-06-01
weight : 12
chapter : false
pre : " <b> 5.12. </b> "
---

#### Overview

<!-- WHAT GOES HERE:
Teardown in dependency order so nothing blocks: Lambda and API Gateway, then
EC2, then RDS (with the final-snapshot decision), then empty and delete the
buckets, then the IAM roles, then the alarms and SNS topic. Finish with a
screenshot showing no running resources.
-->



<!-- Screenshots go in static/images/5-Workshop/5.12-Cleanup/ and are referenced like:
![description](/images/5-Workshop/5.12-Cleanup/example.png)
-->
