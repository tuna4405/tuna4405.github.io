---
title: "Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

This worklog documents the internship between 15/06/2026 and 31/07/2026, kept
week by week across seven weeks in total.

The project - **Caerus**, a cinema seat booking platform put together by a
team of two - was settled in Week 1 rather than postponed: the API
specification and the database schema were agreed and frozen before any AWS
service got studied in earnest. Weeks 2 and 3 go through the AWS
fundamentals required to build and ship it. Week 4 puts the application
together locally, intentionally holding back the two endpoints that need
real AWS infrastructure before they can exist. Week 5 gets the system
deployed and takes out both of its single points of failure - the compute
tier and the database. Week 6 finishes the AWS-dependent endpoints, places
the whole application behind one HTTPS domain, pulls the compute tier off
the public internet altogether, and sets up monitoring. Week 7 demonstrates
the system's central guarantee on the deployed environment itself and wraps
up with the written report.

For each week below there are the objectives set at the outset, the tasks
worked through day by day, and what came out of it in practice.

**Week 1:** [Onboarding and scoping the project](1.1-Week1/)

**Week 2:** [Account groundwork and identity and access management](1.2-Week2/)

**Week 3:** [Compute, storage, and managed database services](1.3-Week3/)

**Week 4:** [Caerus - core endpoints, localhost only](1.4-Week4/)

**Week 5:** [Caerus - moving onto AWS](1.5-Week5/)

**Week 6:** [Caerus - AWS-backed features, CDN, network hardening, and observability](1.6-Week6/)

**Week 7:** [Caerus - verification, report, and submission](1.7-Week7/)
