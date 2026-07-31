---
title: "Week 1 Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives: Orientation and Project Definition

* Meet the First Cloud Journey cohort and understand the programme structure, rules, and deliverables.
* Form the two-person team and choose a project whose central problem cannot be solved by the application layer alone.
* Freeze the API endpoint list and the database schema together, before writing any code.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Orientation session: introduction to the First Cloud Journey programme, rules, deliverables, and report requirements <br> - Formed the two-person team and agreed a working split: one backend lane, one frontend lane, both developed in parallel against a shared contract | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Reviewed candidate project ideas and picked a cinema seat booking platform, because selling reserved seats under concurrency exercises transactional integrity rather than simple CRUD <br> - Named the project Caerus <br> - Read around the problem space: a case study on how SeatGeek, a real ticketing platform, structures authorization and rate limiting for many tenants at once - purely as background reading at this stage, not something Caerus needs, since it is a single-tenant application with no multi-tenant rate limiting requirement | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/> |
| 4 | - Defined the core problem precisely: two customers selecting the same seat at the same instant must never both succeed <br> - Drafted the full list of API endpoints needed: auth, event listing and detail, seat map, booking, cancellation, ticket download, admin event creation, admin poster upload | 17/06/2026 | 17/06/2026 | |
| 5 | - Drafted the database schema alongside the endpoint list, in the same session rather than afterward, since the two depend on each other: five tables - `users`, `events`, `seats`, `bookings`, `booking_seats` <br> - Decided seats belong to a screening rather than to a physical room, so availability is unambiguous per showing <br> - Decided prices are stored as integers in Vietnamese dong, never floats | 18/06/2026 | 18/06/2026 | |
| 6 | - Agreed the request/response shape and standard error format for every endpoint, and the row-locking approach for the booking transaction (`SELECT ... FOR UPDATE`) <br> - Froze both documents - the API specification and the database schema - as a rule that neither developer changes unilaterally from this point on | 19/06/2026 | 19/06/2026 | |
| 7 | - Self-study: reviewed the frozen documents once more for gaps before treating them as final, and read ahead on IAM in preparation for next week | 20/06/2026 | 20/06/2026 | |

### Week 1 Achievements:

* Formed a two-person team with a clear backend/frontend split that allows parallel work from the very first week of building.
* Chose a project whose defining requirement - no seat ever sold twice - cannot be met by the application layer alone, and can explain why.
* Produced two frozen contract documents (API specification and database schema) agreed in the same session, before a single line of code was written.
* Read a real-world case study on multi-tenant authorization purely as exploratory background, and can explain why its specific mechanisms (tiered usage plans, per-tenant API keys) do not apply to a single-tenant project like Caerus.
