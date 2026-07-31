---
title : "API Specification"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

An API specification is only useful as a contract if both sides can be
confident it will not move under them. Caerus's spec is a single Markdown
document with a rule at the top: nobody changes it unilaterally, and every
change - including ones made later, after deployment, such as switching an
endpoint's underlying compute from Lambda back to EC2 - is logged in a
change-log table with a date and a reason, never edited away silently. That
discipline is what let the backend and frontend be built in parallel in
section 5.4: the frontend coded against the spec's shapes with mock JSON
files before the real API existed, and integration day was about connecting
two already-correct halves rather than discovering what the other side
actually built.

**Conventions, agreed once and never revisited per-endpoint:**

- All request and response bodies are JSON.
- Timestamps are transmitted as ISO 8601 in UTC, but every showtime they
  represent is a screening in `Asia/Ho_Chi_Minh`. Storage and the wire format
  stay UTC; only interpretation and display convert to local time, and the
  `?date` filter on the events list is interpreted as a Vietnamese calendar
  date, not a UTC one.
- Money is always an integer number of Vietnamese dong. Never a float, and
  never a string.
- Authentication is a JWT bearer token in the `Authorization` header; the
  token is opaque to the client and carries the user's id and role.

**The standard error shape.** Every failure, regardless of endpoint, returns
the same envelope:

```json
{
  "error": {
    "code": "SEAT_ALREADY_BOOKED",
    "message": "One or more selected seats have just been booked by another user.",
    "conflictingSeatIds": [12, 13]
  }
}
```

Clients switch on `code`, never on `message` - the message is for a human,
the code is for the program.

| Code | HTTP status | Meaning |
|---|---|---|
| `VALIDATION_ERROR` | 400 | Request body failed validation |
| `UNAUTHORIZED` | 401 | Missing, malformed, or expired token |
| `FORBIDDEN` | 403 | Authenticated, but not permitted for this resource |
| `NOT_FOUND` | 404 | Resource does not exist, or the caller has no right to know it does |
| `SEAT_ALREADY_BOOKED` | 409 | One or more requested seats were booked by someone else first |
| `BOOKING_NOT_CANCELLABLE` | 409 | Booking is already cancelled, or the showtime has passed |
| `EMAIL_ALREADY_EXISTS` | 409 | Registration email is already registered |

**Endpoint summary:**

| Method | Path | Auth | Purpose |
|---|---|---|---|
| POST | `/auth/register` | - | Create account |
| POST | `/auth/login` | - | Get JWT |
| GET | `/events` | - | List upcoming screenings |
| GET | `/events/:id` | - | Screening detail |
| POST | `/events` | admin | Create screening (auto-generates 60 seats) |
| POST | `/events/:id/banner` | admin | Upload poster image |
| GET | `/events/:id/seats` | - | Seat map |
| POST | `/bookings` | user | Book seats (atomic, up to 6) |
| GET | `/bookings` | user | My bookings |
| GET | `/bookings/:id` | user | Booking detail |
| DELETE | `/bookings/:id` | user | Cancel booking |
| POST | `/bookings/:id/ticket` | user | Generate and download PDF ticket |

<!-- ![Excerpt of the frozen api-spec.md, showing the error shape and one endpoint definition](/images/5-Workshop/5.3-Design/5.3.1-api-spec/example.png) -->


