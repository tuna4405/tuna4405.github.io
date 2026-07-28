---
title: "Week 1 Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives: Orientation and Cloud Fundamentals

* Meet the First Cloud Journey cohort and understand the programme structure, rules, and deliverables.
* Understand what cloud computing is and how AWS organises its global infrastructure.
* Create and secure a personal AWS account, and put cost controls in place before using any service.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Orientation session: introduction to the First Cloud Journey programme <br> - Read and take notes on the internship rules, deliverables, and report requirements | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Cloud computing fundamentals: <br> &emsp; + On-premises vs cloud: capital versus operating expenditure <br> &emsp; + Service models: IaaS, PaaS, SaaS <br> &emsp; + Deployment models: public, private, hybrid <br> - The AWS Shared Responsibility Model: what AWS secures versus what the customer secures | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - AWS global infrastructure: <br> &emsp; + Regions, Availability Zones, and edge locations <br> &emsp; + How to choose a Region: latency, cost, and data residency <br> - Overview of the main service categories: compute, storage, database, networking, monitoring <br> - Decided on `ap-southeast-1` (Singapore) as the working Region for the whole internship | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Accounts and billing: <br> &emsp; + Root user versus IAM user, and why the root user should not be used daily <br> &emsp; + AWS Free Tier: the difference between always-free, 12-month, and trial offers <br> - **Practice:** <br> &emsp; + Create an AWS Free Tier account <br> &emsp; + Enable MFA on the root user <br> &emsp; + Explore the Billing dashboard and Cost Explorer <br> &emsp; + Create a billing alarm so any unexpected charge is noticed early | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - Tour of the AWS Management Console: navigation, Region selector, service search <br> - AWS CLI: installation, credential configuration, profiles, output formats <br> - **Practice:** <br> &emsp; + Install and configure the AWS CLI <br> &emsp; + Verify identity with `aws sts get-caller-identity` <br> &emsp; + List Regions and describe available services from the command line | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 7 | - The AWS Well-Architected Framework: an overview of the six pillars <br> - Self-study: reviewed the week's notes and prepared questions on IAM for next week | 06/06/2026 | 06/06/2026 | <https://aws.amazon.com/architecture/well-architected/> |

### Week 1 Achievements:

* Understood the fundamental cloud service and deployment models, and can explain where responsibility sits between AWS and the customer.
* Can explain the relationship between Regions, Availability Zones, and edge locations, and justify a Region choice on latency and cost grounds.
* Created a working AWS Free Tier account with MFA enabled on the root user.
* Configured a billing alarm before provisioning any billable resource, establishing cost awareness as a habit rather than an afterthought.
* Installed and configured the AWS CLI and used it to inspect account and Region information alongside the Console.
