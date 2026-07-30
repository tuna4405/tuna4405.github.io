---
title: "Blogs Posted"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

During the internship I wrote and published three blog posts to the
[AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) community.
All three were written in Vietnamese for that audience; the summaries below are
in English.

Two of the posts came out of problems I ran into directly while learning: losing
track of what I had provisioned, and worrying about cost on a personal account.
The third is a summary of an AWS Architecture Blog case study that answered a
question I had while designing my own API.

### [Blog 1 - AWS Budgets and Cost Anomaly Detection](3.1-Blog1/)

Two cost management tools that answer different questions. AWS Budgets alerts
when spending crosses a threshold you define, including a forecast-based alert
that fires before you actually exceed it. Cost Anomaly Detection learns an
account's normal spending pattern and flags departures from it without any
threshold being set. The post covers the concepts, the console steps for both,
the current pricing (monitoring and alerting are free; only Budget Actions and
scheduled reports are charged), and the practical limits: neither tool is
real-time, and anomaly detection needs roughly ten days of history per service
before it works.

### [Blog 2 - How SeatGeek Controls Authorization and Rate Limiting for a Multi-Tenant SaaS](3.2-Blog2/)

A summary of an AWS Architecture Blog case study on the noisy-neighbour problem:
preventing one tenant from consuming shared capacity at the expense of others.
SeatGeek moved authorization out of individual services and into a single Lambda
authorizer at API Gateway, mapped tenants to API keys through DynamoDB, and used
tiered usage plans to enforce per-tenant request limits. The post follows one
request end to end and highlights the multi-level caching that keeps the design
both fast and cheap.

### [Blog 3 - AWS Config and Conformance Packs](3.3-Blog3/)

A look at how to find out what you have actually configured. AWS Config records
the configuration state of every resource and evaluates it against rules;
a conformance pack deploys a whole set of those rules as one unit. The post
covers the core concepts, the available sample templates, the six deployment
steps, and two warnings worth reading before enabling it: rule evaluations are
not in the free tier, and a high compliance score means only that the rules in
that pack passed, not that the environment is secure.
