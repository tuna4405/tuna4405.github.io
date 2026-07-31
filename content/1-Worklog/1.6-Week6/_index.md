---
title: "Week 6 Worklog"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives: Caerus - AWS-Dependent Features, CDN, Network Hardening, and Monitoring

* Implement the two endpoints that needed real AWS infrastructure to exist: poster upload and ticket generation.
* Put a single HTTPS domain and edge protection in front of the whole application.
* Take the compute tier off the public internet entirely, and put dashboards and alarms in place.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Backend lane]** Implemented admin poster upload: the image is written to the private images bucket, and every read is served through a freshly signed, short-lived pre-signed URL rather than a public bucket <br> - **[Frontend lane]** Built the admin upload form and wired the poster onto event cards | 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 3 | - Studied Lambda and API Gateway together <br> - **[Backend lane]** Built ticket generation as a Lambda function invoked directly from the API: it renders the PDF, writes it to the tickets bucket, and returns a pre-signed download URL; also tried moving booking cancellation to a second Lambda <br> - **[Frontend lane]** Wired the ticket download button to the new response shape | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 4 | - Reconsidered both serverless moves on evidence rather than leaving them as first implemented: <br> &emsp; + Moved cancellation back to the API server - it shares the booking transaction's locking logic and gained nothing from being split out <br> &emsp; + Decided to remove the ticket-generation Lambda too, once it became clear the workload was too small and infrequent to justify a second deployable with its own IAM role and deploy step; moved PDF rendering in-process into the Express API <br> - Updated the API specification's change log to record both reversals with reasoning, rather than editing history away <br> - Started Amazon CloudFront: one distribution, path-based routing between the S3 site bucket and the load balancer | 22/07/2026 | 22/07/2026 | |
| 5 | - Finished the CloudFront setup: Origin Access Control locking the site bucket private again, custom error responses for SPA routing, and the single HTTPS domain live for both the site and the API <br> - Found that CloudFront's bundled WAF was silently blocking poster uploads over its default request-body size limit, disguised as a fake success by the same custom error response meant for SPA fallback; fixed by overriding the specific WAF rule's action instead of disabling WAF entirely <br> - **Published Blog 2** (the SeatGeek case study read in Week 1) to the AWS Study Group community | 23/07/2026 | 23/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/> |
| 6 | - Took the compute tier off the public internet: created a private subnet pair for the EC2 instances (separate from the database's, so their route tables stay independent), a NAT gateway for outbound-only package installs and patching, and granted Systems Manager permission on the instance role <br> - Replaced both running instances with AMI-based clones launched into the new private subnets, verified over Session Manager rather than SSH, then removed the SSH rule from the security group entirely | 24/07/2026 | 24/07/2026 | |
| 7 | - Built the CloudWatch dashboard (EC2, RDS, load balancer) and shipped application logs to a log group <br> - Configured alarms on target-group health and database resource pressure, wired to an SNS topic delivering email <br> - Deliberately triggered an alarm to confirm it actually fires and notifies, rather than trusting it unverified in the OK state | 25/07/2026 | 25/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |

### Week 6 Achievements:

* Implemented both AWS-dependent endpoints deferred since Week 4, each reading and writing through presigned URLs rather than a public bucket.
* Built a Lambda function correctly, proved it worked, and still removed it once the evidence showed a persistent server was the better fit - a reversed decision with its reasoning kept, not a mistake hidden.
* Diagnosed a CloudFront-plus-WAF interaction that disguised a real block as a fake success, and fixed it with a targeted rule override rather than disabling protection wholesale.
* Took the entire compute tier off the public internet - no public IP, no inbound SSH - while keeping deployment and debugging access through Systems Manager Session Manager.
* Published the second blog post of the internship, turning background reading from Week 1 into a written case study for the community.
* Built a dashboard and alarms covering every remaining service, with at least one alarm proven to fire and notify rather than left untested.
