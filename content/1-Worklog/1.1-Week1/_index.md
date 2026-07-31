---
title: "Week 1 Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives: Onboarding and Scoping the Project

* Meet the First Cloud Journey cohort and get clear on how the programme is structured, what its rules are, and what has to be handed in.
* Put the two-person team together and settle on a project whose core difficulty the application layer cannot handle on its own.
* Lock down the API endpoint list and the database schema at the same time, ahead of any code being written.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Orientation session: an introduction to the First Cloud Journey programme, its rules, its deliverables, and what the report has to contain <br> - Set up the two-person team and settled how the work would be split: one backend lane, one frontend lane, both moving forward at once against a shared contract | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Went through the candidate ideas and settled on a cinema seat booking platform, on the grounds that selling reserved seats under concurrency puts transactional integrity to work rather than plain CRUD <br> - Gave the project the name Caerus <br> - Background reading around the problem: a case study on the way SeatGeek, a ticketing platform running in production, organises authorization and rate limiting for many tenants at once - context only at this stage, not something Caerus calls for, given it is a single-tenant application with no multi-tenant rate limiting requirement | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/> |
| 4 | - Stated the core problem exactly: where two customers pick the same seat at the same moment, it must never be the case that both succeed <br> - Wrote out the complete set of API endpoints required: auth, event listing and detail, seat map, booking, cancellation, ticket download, admin event creation, admin poster upload | 17/06/2026 | 17/06/2026 | |
| 5 | - Wrote the database schema in the same session as the endpoint list rather than afterwards, since each one constrains the other: five tables - `users`, `events`, `seats`, `bookings`, `booking_seats` <br> - Settled that a seat belongs to a screening and not to a physical room, which keeps availability unambiguous for each showing <br> - Settled that prices are held as integers in Vietnamese dong, never as floats | 18/06/2026 | 18/06/2026 | |
| 6 | - Settled the request/response shape and a standard error format for every endpoint, along with the row-locking approach behind the booking transaction (`SELECT ... FOR UPDATE`) <br> - Froze both documents - the API specification and the database schema - under a rule that neither developer alters them on their own from here on | 19/06/2026 | 19/06/2026 | |
| 7 | - Self-study: went over the frozen documents once more looking for gaps before treating them as final, and read ahead on IAM in preparation for the week to come | 20/06/2026 | 20/06/2026 | |

### Week 1 Achievements:

* Formed a two-person team with backend and frontend cleanly separated, which makes parallel work possible from the very first week of building.
* Settled on a project whose defining requirement - that no seat is ever sold twice - lies beyond the reach of the application layer alone, and can account for why.
* Produced two frozen contract documents (the API specification and the database schema) agreed in a single session, before any code existed.
* Read a real-world case study on multi-tenant authorization as exploratory background, and can explain why its particular mechanisms (tiered usage plans, per-tenant API keys) have no bearing on a single-tenant project such as Caerus.
