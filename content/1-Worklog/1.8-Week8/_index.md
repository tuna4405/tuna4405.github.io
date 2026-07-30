---
title: "Week 8 Worklog"
date: 2026-06-01
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives: Caerus - Deploying to AWS

* Move the database to Amazon RDS and the static site to Amazon S3.
* Deploy the API to Amazon EC2 and secure the connection between the two.
* Have the application publicly reachable.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Studied RDS and S3 together in the morning <br> - Launched the RDS instance and ran the migration and seed files against it with a changed connection string <br> - Created both S3 buckets and enabled static website hosting on the site bucket | 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3 | - S3 in practice: <br> - Implemented poster upload through the AWS SDK and created the IAM role for the instance to write to the images bucket <br> - Built the admin upload interface and poster display on event cards <br> - Noted the account constraint that every role name must carry the `caerus-` prefix, so the role was named accordingly | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 4 | - Studied EC2 together <br> - Both team members completed the launch-and-terminate exercise on their own IAM user <br> - Confirmed in the Console afterwards that no instance was left running | 22/07/2026 | 22/07/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 5 | - Deployment: <br> - Launched the EC2 instance, installed Node.js, deployed the API under a process manager, and narrowed the security groups so the database accepts traffic only from the instance's security group <br> - Built the production React bundle, published it to S3, and pointed it at the EC2 API <br> - Resolved the expected cross-origin failures by configuring permitted origins on the API <br> - Updated the specification: timestamps now represent showtimes in `Asia/Ho_Chi_Minh`, and the event image is defined as a portrait poster | 23/07/2026 | 24/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 7 | - Buffer: fixed outstanding issues from the deployment and verified the booking flow end to end against the deployed environment <br> - **Milestone reached:** the application live on AWS | 25/07/2026 | 26/07/2026 |  |

### Week 8 Achievements:

* Migrated from a local Docker database to a managed RDS instance by changing only a connection string, validating the decision to keep migrations as plain SQL files.
* Published the frontend to S3 static website hosting and the API to EC2, with the two communicating across origins.
* Configured security groups so the database is reachable only from the application instance rather than from the internet.
* Resolved cross-origin request failures at the API rather than working around them in the browser.
