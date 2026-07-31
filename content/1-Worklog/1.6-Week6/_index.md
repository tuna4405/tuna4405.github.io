---
title: "Week 6 Worklog"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives: Caerus - AWS-Backed Features, CDN, Network Hardening, and Observability

* Implement the two endpoints that needed real AWS infrastructure before they could exist: poster upload and ticket generation.
* Put one HTTPS domain and edge protection in front of the application as a whole.
* Pull the compute tier off the public internet altogether, and get dashboards and alarms in place.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **[Backend lane]** Implemented admin poster upload: the image goes into the private images bucket, and every read comes back through a freshly signed, short-lived pre-signed URL instead of a public bucket <br> - **[Frontend lane]** Built the admin upload form and wired the poster through onto the event cards | 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 3 | - Studied Lambda and API Gateway alongside each other <br> - **[Backend lane]** Built ticket generation as a Lambda function invoked straight from the API: it renders the PDF, writes it into the tickets bucket, and hands back a pre-signed download URL; also had a go at moving booking cancellation into a second Lambda <br> - **[Frontend lane]** Wired the ticket download button up to the new response shape | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 4 | - Revisited both serverless moves on the evidence, rather than letting them stand as first implemented: <br> &emsp; + Brought cancellation back to the API server - it shares the booking transaction's locking logic and gained nothing at all from being split out <br> &emsp; + Decided to drop the ticket-generation Lambda as well, once it was clear the workload was too small and too infrequent to warrant a second deployable carrying its own IAM role and deploy step; PDF rendering moved in-process into the Express API <br> - Updated the API specification's change log so both reversals are recorded with the reasoning behind them, instead of editing the history away <br> - Began on Amazon CloudFront: a single distribution, routing by path between the S3 site bucket and the load balancer | 22/07/2026 | 22/07/2026 | |
| 5 | - Finished the CloudFront setup: Origin Access Control putting the site bucket back to private, custom error responses for SPA routing, and the one HTTPS domain live for the site and the API both <br> - Found CloudFront's bundled WAF quietly blocking poster uploads that went over its default request-body size limit, dressed up as a fake success by the very custom error response meant for SPA fallback; fixed by overriding that specific WAF rule's action rather than switching WAF off altogether <br> - **Published Blog 2** (the SeatGeek case study read back in Week 1) to the AWS Study Group community | 23/07/2026 | 23/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/> |
| 6 | - Pulled the compute tier off the public internet: created a private subnet pair for the EC2 instances (kept apart from the database's, so the two route tables stay independent), a NAT gateway for outbound-only package installs and patching, and granted Systems Manager permission on the instance role <br> - Swapped both running instances for AMI-based clones launched into the new private subnets, verified over Session Manager instead of SSH, and then stripped the SSH rule out of the security group entirely | 24/07/2026 | 24/07/2026 | |
| 7 | - Built the CloudWatch dashboard (EC2, RDS, load balancer) and shipped application logs into a log group <br> - Configured alarms on target-group health and on database resource pressure, wired through to an SNS topic delivering email <br> - Set an alarm off on purpose to confirm it really does fire and notify, rather than trusting it unverified while it sat in the OK state | 25/07/2026 | 25/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |

### Week 6 Achievements:

* Implemented both AWS-dependent endpoints that had been held back since Week 4, each of them reading and writing through presigned URLs rather than a public bucket.
* Built a Lambda function properly, showed it worked, and removed it anyway once the evidence pointed to a persistent server being the better fit - a reversed decision with its reasoning preserved, not a mistake swept out of sight.
* Diagnosed a CloudFront-plus-WAF interaction that dressed a real block up as a fake success, and fixed it with a targeted rule override instead of turning protection off wholesale.
* Pulled the whole compute tier off the public internet - no public IP, no inbound SSH - while keeping deployment and debugging access open through Systems Manager Session Manager.
* Published the internship's second blog post, turning Week 1's background reading into a written case study for the community.
* Built a dashboard and alarms covering every service still in play, with at least one alarm proven to fire and notify rather than left untested.
