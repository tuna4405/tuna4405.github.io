---
title: "Blog 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AWS BUDGETS AND COST ANOMALY DETECTION

**Published on: 30-07-2026** <!-- FILL IN date -->

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2229088634522763/** <!-- FILL IN post URL -->

### Why I wrote this

A problem I think most students hit early: you create resources to practise
with, and then forget to delete them. The Free Tier has limits, and once you
pass them the charges start accruing without any signal. You find out when you
open the bill at the end of the month.

Looking into it, I found two tools in the AWS cost management family that
address this from different directions. **AWS Budgets** lets you set a spending
threshold and be notified when actual or forecasted spend crosses it. **AWS Cost
Anomaly Detection** uses machine learning to learn an account's spending habits
and then alerts on spend that departs from them.

Put simply, Budgets answers "have I gone over the limit I set", while Cost
Anomaly Detection answers "is anything being spent that looks unusual". The two
complement each other.

### Key concepts

* **Budget** - a cost or usage threshold you define. AWS Budgets supports cost
  budgets, usage budgets, and budgets tracking the utilisation and coverage of
  Reserved Instances and Savings Plans.
* **Alert** - the notification sent when a threshold is reached. It can be set
  against either actual or forecasted spend. The forecast alert is the more
  useful of the two, because it fires when AWS predicts you *will* exceed the
  threshold, leaving time to act.
* **Budget Action** - an automatic response when a threshold is crossed. An
  action can apply an IAM policy, apply a Service Control Policy, or stop EC2
  and RDS instances.
* **Cost Monitor** - the Cost Anomaly Detection object that defines what is
  being watched.
* **Alert Subscription** - the configuration of who receives alerts, how often,
  and at what threshold.

### What Cost Anomaly Detection can monitor

Spend can be segmented along four dimensions: AWS Services, Linked Accounts,
Cost Allocation Tags, and Cost Categories.

For a personal account, an AWS Services monitor is the right choice. This
monitor type automatically includes new services as you start using them, so it
does not need reconfiguring every time you try something new. The limits are one
AWS Service monitor and up to 500 custom monitors.

Alert subscriptions have three frequencies. DAILY and WEEKLY notifications are
delivered by email; IMMEDIATE is delivered through SNS.

One point that is easy to misread: the alert threshold only decides *when a
notification is sent*, not how the detection algorithm behaves. Anomalies below
the threshold are still recorded and visible in the console - they simply do not
trigger an alert.

### Creating a budget

1. Open the AWS Billing and Cost Management Console and choose **Budgets**, then
   create a new budget.
2. Choose the budget type. For basic bill monitoring, a cost budget is what you
   want.
3. Set a name, a period (usually monthly), and the threshold amount. You can
   scope the budget to specific services here if you only want to watch EC2 or
   RDS rather than the whole account.
4. Configure the alert. Enter the threshold as a percentage or an absolute
   amount, choose actual or forecasted spend, and enter the notification email.
   I would set several levels, for example 50, 80, and 100 percent, so you find
   out early rather than only once you have gone over.
5. Review and create. A confirmation email is sent to each address you entered,
   and it must be confirmed before notifications will be delivered.

### Creating a cost monitor

1. In the same console, choose **Cost Anomaly Detection** and create a new
   monitor.
2. Choose the monitor type - AWS Services for a personal account.
3. Create an alert subscription, choosing the frequency and entering an email
   address or SNS topic.
4. Enter the alert threshold, either as an absolute amount or as a percentage
   above expected spend.
5. Create the monitor, then wait. A new monitor can take up to 24 hours to begin
   detecting, and the model needs roughly 10 days of historical spend data for a
   service before it can detect anomalies in that service.

### Advantages

* You learn about a cost problem within hours to a day, rather than at the end
  of the month.
* Forecast-based alerts give you time to act before you actually exceed the
  threshold.
* Cost Anomaly Detection does not require a threshold per service, because it
  learns what normal looks like.
* Alerts come with root cause analysis identifying which service or usage type
  drove the unusual spend.
* Budget Actions allow automatic intervention rather than notification alone.
* Monitoring by tag or cost category makes it possible to separate spend by
  project.

### Points worth knowing

**On cost.** This is the part I found easiest to get wrong, because a lot of
articles online carry outdated information. According to the current AWS pricing
page, monitoring a budget and receiving notifications is **free**. What is
charged is Budget Actions: the first two action-enabled budgets each month are
free, after which each action-enabled budget costs 0.10 USD per day. AWS Budgets
Reports, meaning scheduled reports delivered by email, cost 0.01 USD per report
delivered. Cost Anomaly Detection is free, including creating monitors,
detection, and alerting. For personal bill monitoring, then, there is
effectively no cost.

**On latency.** Neither tool is real-time. Budget data refreshes roughly three
times a day, and Cost Anomaly Detection evaluates once daily. This is worth
accepting rather than resenting: both detect problems far sooner than waiting
for the bill, but neither prevents charges from being incurred in the moment.

**On new accounts.** Because the model needs history, Cost Anomaly Detection
will not be effective on a freshly opened account or a service you have only
just started using. In that first period, AWS Budgets with a manually set
threshold is what to rely on.

**On Budget Actions.** Think carefully before enabling automatic actions.
Automatically stopping EC2 instances or applying an IAM policy can affect
resources that are in use. If the account is only being used for learning, an
email alert is enough.

**On setting thresholds.** A threshold set too low produces notifications you do
not need, and after a while you start ignoring them. Set too high, the alert
arrives too late to matter.

### When to use them

* Using a personal account for learning and wanting to avoid unexpected charges.
* Needing to cap spend for a specific environment, such as development.
* Wanting to catch resources that were left running by accident.
* Needing to separate and track cost per project or per team.
* Wanting automatic intervention when spend crosses a threshold, rather than
  notification alone.

For a personal account I would start with a simple account-wide cost budget with
a few alert levels, then enable Cost Anomaly Detection once a few weeks of spend
data exist.

### Conclusion

The two tools answer different questions. AWS Budgets works from a threshold you
set, so it fits when you already know what you want to spend. Cost Anomaly
Detection learns normal spend for itself, so it catches unusual increases you
would never have thought to set a threshold for.

The part I found most worth saying is that the monitoring and alerting in both
is free, and yet it is exactly the thing that gets skipped when you are starting
out with AWS.

### References

* AWS Budgets - Managing your costs with AWS Budgets:
  https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html
* AWS Budgets Pricing:
  https://aws.amazon.com/aws-cost-management/aws-budgets/pricing/
* AWS Cost Anomaly Detection - Detecting unusual spend:
  https://docs.aws.amazon.com/cost-management/latest/userguide/manage-ad.html
* AWS Cost Anomaly Detection - FAQs:
  https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/faqs/
* AWS Cost Management API - AnomalySubscription:
  https://docs.aws.amazon.com/aws-cost-management/latest/APIReference/API_AnomalySubscription.html

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.1-Blog1/ and is referenced like:
-->
![Published post on the AWS Study Group community page](/images/3-BlogsPosted/3.1-Blog1/post.png)
