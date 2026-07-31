---
title : "Backend: Express and Dockerized PostgreSQL"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

1. **Start PostgreSQL 16 in Docker**, mapped to host port 5433 so it never
   collides with a PostgreSQL already installed on the developer's machine:

   ```yaml
   services:
     db:
       image: postgres:16
       environment:
         POSTGRES_USER: caerus
         POSTGRES_PASSWORD: caerus_dev
         POSTGRES_DB: caerus
       ports:
         - "5433:5432"
       volumes:
         - pgdata:/var/lib/postgresql/data
   volumes:
     pgdata:
   ```

2. **Load the schema and seed data** as plain `.sql` files, run with `psql` -
   the same two files run unchanged against RDS later in section 5.5, which
   is the entire point of keeping migrations as SQL rather than behind an ORM
   migration tool with its own runtime dependency.

3. **Implement the booking transaction** - the one piece of code the whole
   project exists to get right:

   ```sql
   BEGIN;

   -- Lock the requested seat rows, ordered by id, before reading their status.
   SELECT id, status FROM seats
   WHERE id = ANY($1) AND event_id = $2
   ORDER BY id
   FOR UPDATE;

   -- If any locked seat is not 'available', roll back and return 409
   -- SEAT_ALREADY_BOOKED with the conflicting ids.

   INSERT INTO bookings (user_id, event_id, total_price) VALUES (...) RETURNING id;
   INSERT INTO booking_seats (booking_id, seat_id) SELECT $1, unnest($2::int[]);
   UPDATE seats SET status = 'booked' WHERE id = ANY($1) AND status = 'available';

   COMMIT;
   ```

**Why `ORDER BY id` matters as much as `FOR UPDATE`.** `FOR UPDATE` alone
stops two transactions from both thinking a seat is free; it does not stop a
deadlock. If transaction A requests seats `[12, 13]` and transaction B
requests `[13, 12]` at the same moment, without a fixed lock order each can
end up holding the lock the other needs. Sorting the seat ids before locking
means every transaction acquires locks in the same sequence regardless of the
order the client sent them in, so two transactions can never form a cycle
waiting on each other.

As cheap insurance against a future code path that bypasses the lock
entirely, the final `UPDATE` re-checks `status = 'available'` and the service
layer asserts the affected row count matches the number of seats requested -
under the lock this can never fail, so if it ever does, that is a loud signal
something upstream skipped the transaction rather than a state to handle
gracefully.

<!-- ![Local docker-compose and psql session loading the schema](/images/5-Workshop/5.4-Local-Build/5.4.1-backend/example.png) -->
