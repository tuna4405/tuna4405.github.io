---
title: "Week 9 Worklog"
date: 2026-06-01
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives: Caerus — Serverless, Monitoring, and Testing

* Move ticket generation to a Lambda function behind API Gateway.
* Put dashboards, log collection, and an alarm in place across all services.
* Prove the concurrency guarantee against the deployed system.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Studied Lambda and API Gateway together <br> - Both team members completed the hello-world function tutorial individually <br> - Reviewed which endpoints are genuinely suited to a function: infrequent, stateless, and bursty | 27/07/2026 | 27/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/> |
| 3 | - Serverless build: <br> - **[Backend lane]** Built the ticket generation function, its execution role, and the API Gateway route; the function renders the PDF, writes it to the tickets bucket, and returns a pre-signed URL <br> - **[Frontend lane]** Wired the download button to the new route and confirmed the response shape is unchanged from the specification <br> - Revised the plan: booking cancellation stays on the API server rather than moving to a function, since it shares the same transaction logic as booking and gains nothing from being split out <br> - Updated the specification change log accordingly | 28/07/2026 | 29/07/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/> |
| 4 | - Studied CloudWatch together in the morning <br> - **[Backend lane]** Installed the CloudWatch agent on the instance and shipped the API logs to a log group; added instance and database metrics to a dashboard <br> - **[Frontend lane]** Added function and gateway metrics to the same dashboard | 30/07/2026 | 30/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |
| 5 | - **[Backend lane]** Created the alarm on booking failure rate, wired to an SNS topic delivering email <br> - **[Frontend lane]** Captured screenshots across every service and started the shared report evidence folder <br> - Deliberately triggered the alarm to confirm it fires and notifies, rather than leaving it untested in the OK state | 31/07/2026 | 31/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/> |
| 7 | - Testing and polish, working together: <br> &emsp; + The concurrency test: two browsers, the same seat, the same moment — one booking succeeded and the other received a conflict response naming the contested seat <br> &emsp; + Edge cases: cancelling after showtime, exceeding the seat limit per booking, expired tokens, non-admin access to admin routes <br> &emsp; + Refreshed the demonstration data so seeded screenings are in the future | 01/08/2026 | 01/08/2026 |  |

### Week 9 Achievements:

* Moved an endpoint from a persistent server to an on-demand function without any change visible to the frontend, demonstrating that the compute choice is an operational decision rather than a functional one.
* Revised an earlier design decision on evidence, keeping cancellation on the API server because splitting it would have duplicated transaction logic for no benefit.
* Built a dashboard covering compute, database, function, and gateway metrics, with application logs searchable in one place.
* Proved the no-double-booking guarantee against the deployed system rather than asserting it, with both the successful and the rejected request captured as evidence.
