---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives: Caerus - Design and Local Build

* Agree the API specification and database schema as a frozen contract before writing code.
* Build the backend and frontend in parallel against that contract.
* Reach a fully working application on localhost.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Reviewed AWS core concepts together: IAM, Regions and Availability Zones, security groups, Free Tier, billing <br> - **[Backend lane]** Created the shared AWS account, billing alarm, and one IAM user per team member <br> - **[Frontend lane]** Created the GitHub repository and configured branch protection so all work merges through pull requests <br> - Agreed two working habits: terminate practice resources the same day, and tag every resource with an `Owner` value | 13/07/2026 | 13/07/2026 |  |
| 3 | - Design session - the most important day of the project: <br> &emsp; + Agreed the five-table schema: `users`, `events`, `seats`, `bookings`, `booking_seats` <br> &emsp; + Agreed every endpoint, request and response shape, and the standard error format <br> &emsp; + Decided seats belong to a screening rather than to a physical room <br> &emsp; + Decided prices are stored as integers in Vietnamese dong <br> &emsp; + Froze both documents: neither may be changed unilaterally <br> - **[Backend lane]** Wrote up the database schema document <br> - **[Frontend lane]** Wrote up the API specification and drew the architecture diagram | 14/07/2026 | 14/07/2026 |  |
| 4 | - Parallel local development: <br> - **[Backend lane]** Express API with PostgreSQL running in Docker; implemented the booking transaction using `SELECT ... FOR UPDATE` with consistent lock ordering <br> - **[Frontend lane]** React application with the event list, seat picker, and bookings screens, built against mock JSON files shaped exactly like the agreed responses <br> - Wrapped every API call in a single client module so switching from mocks to the live API is one change | 15/07/2026 | 17/07/2026 | <https://www.postgresql.org/docs/16/explicit-locking.html> |
| 6 | - Integration, working together at one machine: one driving, one debugging <br> - Connected the frontend to the live API and resolved the mismatches the mocks had hidden <br> - Tested the booking flow end to end and confirmed a seat map refresh reflects another user's booking <br> - **Milestone reached:** the complete application running on localhost | 18/07/2026 | 19/07/2026 |  |

### Week 7 Achievements:

* Produced two agreed contract documents that allowed both developers to work simultaneously rather than one waiting on the other.
* Implemented the booking transaction with row-level locking, the central technical requirement of the project.
* Built a frontend that was never blocked on backend progress, by developing against mock data shaped to the specification.
* Completed local integration on schedule, with the whole application demonstrable on localhost.
