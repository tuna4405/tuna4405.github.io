---
title : "Edge Cases"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.9.2 </b> "
---

Beyond the concurrency test, a short list of cases the specification
(section 5.3.1) commits to explicitly and that are worth confirming against
the deployed system rather than trusting the code alone.

| Case | Expected | Actual |
|---|---|---|
| Cancel a booking after its showtime has passed | `409 BOOKING_NOT_CANCELLABLE` | *(fill in from your run)* |
| Cancel an already-cancelled booking | `409 BOOKING_NOT_CANCELLABLE` | |
| Book more than six seats in one request | `400 VALIDATION_ERROR` | |
| Book with an expired or malformed JWT | `401 UNAUTHORIZED` | |
| Non-admin user calls `POST /events` | `403 FORBIDDEN` | |
| Request a screening id that does not exist | `404 NOT_FOUND` | |
| Download a ticket for a cancelled booking | `404 NOT_FOUND` (deliberate - see below) | |

**The last row is a design decision, not an oversight.** A cancelled booking
returns `404` for `POST /bookings/:id/ticket` rather than a `409` conflict
code, on the reasoning that to the caller it should look the same as "there
is nothing here to download," not "there is a state conflict to resolve" -
recorded explicitly in the specification rather than left as an implicit
side effect of whichever code path happened to run first.

{{% notice note %}}
Run each row against the deployed system and replace "fill in from your run"
with the actual observed status code and error `code` field - a table with
blanks is a checklist, not a test result.
{{% /notice %}}

<!-- ![Example of one edge case's actual request/response, e.g. the expired-token 401](/images/5-Workshop/5.9-Testing/5.9.2-edge-cases/example.png) -->
