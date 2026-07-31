---
title: "Week 4 Worklog"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives: Caerus - Core Endpoints, Localhost Only

* Build backend and frontend side by side against the Week 1 contract, on a local database running in Docker.
* Implement the booking transaction with row-level locking, the technical requirement at the heart of the project.
* Get to a working application on localhost, with the AWS-dependent endpoints held back on purpose.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Created the GitHub repository and set up branch protection so that everything merges by way of a pull request <br> - **[Backend lane]** Stood up the Express project skeleton alongside a PostgreSQL container in Docker Compose, and ran the Week 1 schema as a migration <br> - **[Frontend lane]** Stood up the Vite and React project skeleton and drew out the screen map | 06/07/2026 | 06/07/2026 | |
| 3 | - **[Backend lane]** Implemented authentication (JSON Web Tokens, bcrypt password hashing) together with the event listing and detail endpoints <br> - **[Frontend lane]** Built the event list and event detail screens on top of mock JSON files shaped exactly like the agreed API responses | 07/07/2026 | 07/07/2026 | |
| 4 | - **[Backend lane]** Implemented the seat map endpoint and the booking transaction: `SELECT ... FOR UPDATE` over the requested seat rows, ordered by primary key so deadlocks do not arise, committing only where every requested seat remains available <br> - **[Frontend lane]** Built the seat picker screen on mock data, the seat-taken and booking-conflict states included | 08/07/2026 | 08/07/2026 | <https://www.postgresql.org/docs/16/explicit-locking.html> |
| 5 | - **[Backend lane]** Implemented booking cancellation and the bookings-list endpoint, with a rule that no booking can be cancelled once its showtime has passed <br> - **[Frontend lane]** Built the bookings screen along with the cancel action <br> - Held back the two endpoints that depend on AWS on purpose - admin poster upload and ticket-PDF download - given that neither S3 bucket exists yet and neither operation has anywhere to write to | 09/07/2026 | 09/07/2026 | |
| 6 | - Integration, the two of us working at one machine: connected the frontend to the live local API and cleared up the mismatches the mocks had kept out of sight <br> - Wrapped every API call inside a single client module, so that swapping the base URL later on - local for deployed - comes down to one change | 10/07/2026 | 10/07/2026 | |
| 7 | - Tested the booking flow end to end locally: confirmed that refreshing the seat map picks up another user's booking, and that a second attempt on a seat already booked gets rejected <br> - **Milestone reached:** the application runs end to end on localhost, admin poster upload and ticket download being the only two endpoints still unimplemented | 11/07/2026 | 11/07/2026 | |

### Week 4 Achievements:

* Implemented the booking transaction with row-level locking against a real PostgreSQL instance, rather than designing it on paper alone.
* Built a frontend that never once waited on backend progress, developed on mock data shaped to the frozen specification and then switched over to the live local API in a single step.
* Got to a working local application while keeping track of which two endpoints cannot exist yet, instead of quietly stubbing them out and losing the reason why.
* Established the single-client-module pattern that turns pointing the frontend at the deployed API in Week 5 into a small change rather than a rewrite.
