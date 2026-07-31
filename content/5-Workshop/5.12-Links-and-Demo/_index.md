---
title : "Repository, Live Site, and Demo"
date : 2026-06-01
weight : 12
chapter : false
pre : " <b> 5.12 </b> "
---

Everything described in the sections above resolves to three concrete,
checkable artifacts: the code, the running system, and a recording of it
working. All three are linked here rather than scattered across the report.

#### Source Code

<!-- FILL IN: GitHub repository URL, e.g. https://github.com/<org>/caerus-booking -->

The repository contains both the `backend` and `frontend` packages, the SQL
migration and seed files referenced in [Amazon RDS](/5-Workshop/5.5-RDS/), and
the `docs/` folder holding the frozen API specification and database schema
from [System Design](/5-Workshop/5.3-Design/).

#### Live Application

**[https://d2xqaej6i413ey.cloudfront.net](https://d2xqaej6i413ey.cloudfront.net)**

This is the CloudFront distribution domain described in
[CloudFront: One HTTPS Domain for Everything](/5-Workshop/5.7-EC2/5.7.6-cloudfront/)
- the same domain serves both the React frontend and, under `/api/*`, the
Express API behind the load balancer. A demo account is available for sign-in
(see the report appendix for credentials), or an admin account for creating
screenings and uploading posters.

{{% notice note %}}
This link only resolves while the underlying infrastructure is running. If it
is reviewed after [Cleaning Up Resources](/5-Workshop/5.11-Cleanup/) has been
carried out, the demonstration video below is the record of it working.
{{% /notice %}}

#### Demonstration Video

<!-- FILL IN: link to the recorded walkthrough, e.g. a YouTube/Drive link -->

Recorded end to end against the deployed environment, not the local one:
signing in, browsing screenings, the seat map, a normal booking, the
concurrency test from [Testing](/5-Workshop/5.9-Testing/) run across two
browser sessions, cancellation, and downloading the resulting PDF ticket.

<!-- ![Screenshot of the live application's home page](/images/5-Workshop/5.12-Links-and-Demo/example.png) -->
