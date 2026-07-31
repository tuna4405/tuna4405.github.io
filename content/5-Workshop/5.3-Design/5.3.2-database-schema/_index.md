---
title : "Database Schema"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

Five tables, all in one PostgreSQL database:

- **`users`** - id, name, email, bcrypt `password_hash`, `role` (`customer`
  or `admin`).
- **`events`** - one row per screening (not per movie): title, description,
  `starts_at` (timestamptz), duration, auditorium, price, `banner_url`.
- **`seats`** - one row per seat per screening, `seat_row` (`A`-`F`),
  `seat_number` (`1`-`10`), and a `status` of `available` or `booked`.
- **`bookings`** - user, event, `total_price`, `status`
  (`confirmed`/`cancelled`), `cancelled_at`.
- **`booking_seats`** - the join table between a booking and the specific
  seats it holds.

**The `seat_row` naming gotcha.** The column is `seat_row`, not `row` -
`ROW` is a reserved word in PostgreSQL (and a builtin function), so a column
literally named `row` either fails outright or forces every query touching it
into an awkward quoted identifier. The API still calls it `row` in JSON,
because that is the name a frontend developer wants; the translation from
`seat_row`/`seat_number` to `row`/`number` happens once, in the row-mapping
function on the backend, not scattered across every query.

**Four design decisions worth explaining, not just stating:**

1. **Seats belong to a screening, not to a physical room.** A 6x10 layout is
   generated fresh for every `event`, so "is seat A1 available" is always a
   question about one specific showing, never a question that needs a second
   join back to a room's schedule to disambiguate.
2. **`totalSeats` and `availableSeats` are computed at query time, never
   stored as counters.** A stored counter can drift from reality the moment a
   booking and a counter update are not perfectly atomic; a `COUNT(*)` over
   the `seats` table cannot drift, because there is nothing to keep in sync.
3. **Money is an integer column in Vietnamese dong, and a booking's
   `total_price` is snapshotted at creation.** If the screening's price
   changes later, past bookings must not be silently rewritten - the
   snapshot is what a customer actually paid.
4. **Cancelling a booking is a status flip, never a row delete.** `status`
   moves to `cancelled` and `cancelled_at` is stamped; the row survives, so
   the booking history a report or a support conversation might need still
   exists.

![Entity-relationship diagram](/images/5-Workshop/5.3-Design/5.3.2-database-schema/erd.png)
