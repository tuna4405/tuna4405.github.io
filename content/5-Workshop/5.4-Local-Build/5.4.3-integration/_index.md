---
title : "Integration"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

1. **Point the client module at the local API** (`http://localhost:3000/api/v1`)
   instead of the mock files, with both developers at one machine - one
   driving, one reading responses in the network tab.

2. **Work through the mismatches the mocks had hidden.** In practice these
   were small and specific: a date format the mock had simplified, a field
   present in the real response that the mock had omitted, and a loading
   state the UI had never needed to show against instant mock data. None of
   this was a surprise about the *shape* of the API - the frozen spec had
   already settled that - it was entirely about the small realities of a real
   network call.

3. **Run the booking flow end to end**: load the seat map, select seats,
   submit a booking, and confirm the seat map reflects the change on refresh.
   Then open the same seat map in a second browser tab and confirm a seat
   booked in the first tab shows as unavailable after a refresh in the
   second - a first, informal look at the concurrency guarantee that gets a
   real test in section 5.9.

<!-- ![Working seat picker against the local API, booked seats greyed out](/images/5-Workshop/5.4-Local-Build/5.4.3-integration/example.png) -->
