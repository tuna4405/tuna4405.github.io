---
title : "Local Development"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Overview

Before any AWS resource exists, Caerus runs completely on localhost: a
Dockerised PostgreSQL container for the backend, mock JSON files for the
frontend, and a single client module that makes switching from mocks to a
real API a one-line change later. This section is intentionally the
shortest and least AWS-flavoured one in the workshop - it exists to prove the
application logic is correct before any cloud variable is introduced, so that
if something breaks after deployment, the bug is in the deployment, not in
the code.


#### Content

- [Backend: Express and Dockerized PostgreSQL](5.4.1-backend/)
- [Frontend: React with Mock Data](5.4.2-frontend/)
- [Integration](5.4.3-integration/)

