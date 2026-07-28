---
title: "Week 4 Worklog"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives: Compute with Amazon EC2

* Understand instance sizing, pricing models, and storage options.
* Be able to deploy and keep a web application running on an instance.
* Understand the concepts behind load balancing and automatic scaling.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - EC2 fundamentals: <br> &emsp; + Instance families and what each is optimised for <br> &emsp; + Instance sizing and which classes are Free Tier eligible <br> &emsp; + Purchasing options: on-demand, reserved, savings plans, and spot | 22/06/2026 | 22/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 3 | - Instance configuration: <br> &emsp; + Amazon Machine Images and where they come from <br> &emsp; + Key pairs and SSH access <br> &emsp; + User data scripts for first-boot configuration <br> &emsp; + Instance metadata service | 23/06/2026 | 23/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 4 | - Storage for instances: <br> &emsp; + EBS volume types and their performance characteristics <br> &emsp; + EBS versus instance store, and what happens to each on stop and terminate <br> &emsp; + Snapshots and restoring from them <br> &emsp; + Elastic IP addresses, and why an unattached one is charged | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 5 | - **Practice:** <br> &emsp; + Launch an instance, connect over SSH, and install Node.js <br> &emsp; + Run a small HTTP server and reach it from a browser <br> &emsp; + Keep the process alive across a reboot using a process manager <br> &emsp; + Terminate the instance the same day and confirm in the Console that nothing remains | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 6 | - Scaling and availability concepts: <br> &emsp; + Elastic Load Balancing and target groups <br> &emsp; + Auto Scaling groups, launch templates, and health checks <br> &emsp; + Vertical versus horizontal scaling <br> - Self-study: considered which of these a small project genuinely needs and which are out of scope | 26/06/2026 | 26/06/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |

### Week 4 Achievements:

* Can launch, configure, connect to, and terminate an EC2 instance confidently, and can select an appropriate instance class for a given workload.
* Deployed a running Node.js application on an instance and kept it alive under a process manager.
* Understand the difference between stopping and terminating an instance, and which storage survives each.
* Established the habit of terminating practice resources on the same day they are created.
* Can explain how a load balancer and an auto scaling group would improve availability, and can articulate why a single instance may still be an acceptable choice for a small project.
