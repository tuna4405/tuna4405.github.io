---
title: "Week 4 Worklog"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives: Caerus - Core Endpoints, Local Only

* Build the backend and frontend in parallel against the Week 1 contract, on a local Dockerised database.
* Implement the booking transaction with row-level locking, the project's central technical requirement.
* Reach a working application on localhost, with the AWS-dependent endpoints explicitly deferred.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Created the GitHub repository and configured branch protection so all work merges through pull requests <br> - **[Backend lane]** Set up the Express project skeleton and a PostgreSQL container in Docker Compose, and ran the Week 1 schema as a migration <br> - **[Frontend lane]** Set up the Vite and React project skeleton and drew the screen map | 06/07/2026 | 06/07/2026 | |
| 3 | - **[Backend lane]** Implemented authentication (JSON Web Tokens, bcrypt password hashing) and the event listing/detail endpoints <br> - **[Frontend lane]** Built the event list and event detail screens against mock JSON files shaped exactly like the agreed API responses | 07/07/2026 | 07/07/2026 | |
| 4 | - **[Backend lane]** Implemented the seat map endpoint and the booking transaction: `SELECT ... FOR UPDATE` on the requested seat rows, ordered by primary key to avoid deadlocks, committing only if every requested seat is still available <br> - **[Frontend lane]** Built the seat picker screen against mock data, including the seat-taken and booking-conflict states | 08/07/2026 | 08/07/2026 | <https://www.postgresql.org/docs/16/explicit-locking.html> |
| 5 | - **[Backend lane]** Implemented booking cancellation and the bookings-list endpoint, with a rule that a booking cannot be cancelled after its showtime <br> - **[Frontend lane]** Built the bookings screen and the cancel action <br> - Explicitly deferred the two endpoints that need AWS: admin poster upload and ticket-PDF download, since neither S3 bucket exists yet and there is nowhere for either operation to write to | 09/07/2026 | 09/07/2026 | |
| 6 | - Integration, working together at one machine: connected the frontend to the live local API and resolved the mismatches the mocks had hidden <br> - Wrapped every API call in one client module, so switching the base URL later (local to deployed) is a single change | 10/07/2026 | 10/07/2026 | |
| 7 | - Tested the booking flow end to end locally: confirmed a seat map refresh reflects another user's booking, and that a second attempt on an already-booked seat is rejected <br> - **Milestone reached:** the application runs end to end on localhost, with admin poster upload and ticket download the only two endpoints not yet implemented | 11/07/2026 | 11/07/2026 | |

### Week 4 Achievements:

* Implemented the booking transaction with row-level locking against a real PostgreSQL instance, not just designed it on paper.
* Built a frontend that was never blocked on backend progress, developed against mock data shaped to the frozen specification, then switched to the live local API in one step.
* Reached a working local application while correctly identifying which two endpoints cannot exist yet, rather than stubbing them silently and forgetting why.
* Established the single-client-module pattern that will make pointing the frontend at the deployed API, in Week 5, a small change rather than a rewrite.
