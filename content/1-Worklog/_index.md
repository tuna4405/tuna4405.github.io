---
title: "Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

This worklog covers the internship from 15/06/2026 to 31/07/2026, recorded
weekly, seven weeks in total.

The project - **Caerus**, a cinema seat booking platform built by a
two-person team - was chosen in Week 1, not left until later: the API
specification and database schema were agreed and frozen before any AWS
service was studied in depth. Weeks 2 and 3 cover the AWS fundamentals needed
to build and deploy it. Week 4 builds the application locally, deliberately
leaving out the two endpoints that need real AWS infrastructure to exist.
Week 5 deploys the system and removes its two single points of failure -
the compute tier and the database. Week 6 completes the AWS-dependent
endpoints, fronts the whole application with a single HTTPS domain, takes
the compute tier off the public internet entirely, and puts monitoring in
place. Week 7 proves the system's central guarantee against the deployed
environment and closes with the written report.

Each week below lists the objectives set at the start of the week, the tasks
carried out day by day, and what was actually achieved.

**Week 1:** [Orientation and project definition](1.1-Week1/)

**Week 2:** [Account foundations and identity and access management](1.2-Week2/)

**Week 3:** [Compute, storage, and managed databases](1.3-Week3/)

**Week 4:** [Caerus - core endpoints, local only](1.4-Week4/)

**Week 5:** [Caerus - deploying to AWS](1.5-Week5/)

**Week 6:** [Caerus - AWS-dependent features, CDN, network hardening, and monitoring](1.6-Week6/)

**Week 7:** [Caerus - testing, report, and submission](1.7-Week7/)
