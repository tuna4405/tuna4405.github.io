---
title: "Week 6 Worklog"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives: Serverless, Monitoring, and Project Scoping

* Understand the Lambda execution model and when a function is preferable to a server.
* Be able to expose a function through API Gateway.
* Set up monitoring and alerting, and choose a project for the remainder of the internship.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - AWS Lambda: <br> &emsp; + The execution model: handler, event, context <br> &emsp; + Memory, timeout, and how memory affects both speed and cost <br> &emsp; + Cold starts and what makes them worse <br> &emsp; + Execution roles and environment variables | 06/07/2026 | 06/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 3 | - Amazon API Gateway: <br> &emsp; + REST APIs versus HTTP APIs <br> &emsp; + Resources, methods, and integrations <br> &emsp; + Stages and deployments <br> &emsp; + Throttling and CORS configuration at the gateway <br> - **Practice:** hello-world Lambda function exposed through an API Gateway route | 07/07/2026 | 07/07/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/> |
| 4 | - Amazon CloudWatch: <br> &emsp; + Metrics, namespaces, and dimensions <br> &emsp; + Log groups, log streams, and the CloudWatch agent <br> &emsp; + Logs Insights queries <br> &emsp; + Dashboards | 08/07/2026 | 08/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |
| 5 | - Alarms and notifications: <br> &emsp; + Alarm states, thresholds, and evaluation periods <br> &emsp; + Amazon SNS topics and subscriptions <br> - **Practice:** create an alarm on an instance metric and receive the notification by email | 09/07/2026 | 09/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |
| 6 | - Project scoping for the remainder of the internship: <br> &emsp; + Reviewed candidate project ideas against the services studied so far <br> &emsp; + Selected a cinema seat booking platform, because seat allocation under concurrency exercises transactional integrity rather than simple CRUD <br> &emsp; + Formed a two-person team and agreed the split: one backend lane, one frontend lane <br> &emsp; + Sketched a first architecture and listed the services it would need <br> &emsp; + Named the project Caerus | 10/07/2026 | 10/07/2026 |  |

### Week 6 Achievements:

* Can write, deploy, and invoke a Lambda function, and can explain when an on-demand function is a better fit than a persistent server.
* Exposed a function through API Gateway and understand how stages and CORS are configured there.
* Built a CloudWatch dashboard and a working alarm delivering notifications through SNS.
* Selected a project whose central problem cannot be solved by the application layer alone, and agreed a division of work that allows two people to build in parallel.
