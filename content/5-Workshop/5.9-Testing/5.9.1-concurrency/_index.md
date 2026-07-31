---
title : "The Double-Booking Test"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.9.1 </b> "
---

Every other section in this workshop exists in service of this one test.
The claim under test: **a seat can never be sold twice**, even when two
requests for the same seat arrive at effectively the same instant.

1. **Open two separate browser sessions** (two browsers, or one normal and
   one private/incognito window, logged in as two different users) on the
   deployed site, both viewing the seat map for the same screening.

2. **Select the same seat in both**, and submit both bookings as close
   together in time as two people manually clicking can manage - close
   enough that the load balancer may well route the two requests to
   different EC2 instances, which is exactly the scenario the row lock has
   to hold under.

3. **Record both responses.** One request receives `201 Created` with the
   confirmed booking. The other receives:

   ```json
   {
     "error": {
       "code": "SEAT_ALREADY_BOOKED",
       "message": "One or more selected seats have just been booked by another user.",
       "conflictingSeatIds": [12]
     }
   }
   ```

4. **Confirm the seat map afterward** shows the seat as booked exactly once
   - not twice, not zero times - and that the losing client's UI highlights
   the specific seat named in `conflictingSeatIds` rather than showing a
   generic failure.

**Why this proves the lock, not just the UI.** The interesting failure mode
this guards against is not "the button was clickable twice" - a disabled
button after one click would hide that entirely without fixing anything. It
is the database-level race: two `SELECT ... FOR UPDATE` transactions
targeting the same seat row cannot both proceed past the lock, regardless of
which of the two EC2 instances issued either query. One request's
transaction commits and updates the row; the other's transaction, once
unblocked, re-reads the now-updated status inside its own transaction and
rolls back cleanly with the conflict response - no partial state, no
double-charged customer, no seat sold to two people.

<!-- ![Side by side: the 201 response and the 409 response with conflictingSeatIds, plus the seat map after](/images/5-Workshop/5.9-Testing/5.9.1-concurrency/example.png) -->
