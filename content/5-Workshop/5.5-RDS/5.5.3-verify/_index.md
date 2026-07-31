---
title : "Verify the Connection"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---

1. **Confirm the seed data is actually there**, directly against the
   database:

   ```sql
   SELECT count(*) FROM events;
   -- expect 3, matching seed.sql
   ```

2. **Point the still-local Express API at RDS** by changing only
   `DATABASE_URL` in `backend/.env` to the RDS connection string, and restart
   the API - no code change, only configuration.

3. **Hit a real endpoint and confirm the response carries the seeded data:**

   ```bash
   curl http://localhost:3000/api/v1/events
   ```

   The response should list the same three screenings the `SELECT count(*)`
   in step 1 confirmed exist, proving the running application - not just a
   `psql` session - can reach the managed database end to end before EC2 is
   even part of the picture.

{{% notice note %}}
This step deliberately keeps the API running on the developer's own machine
while pointing it at the real database. Isolating "does the API code work
against RDS" from "is EC2 configured correctly" means a failure in section
5.7 can be narrowed to the EC2/networking side with confidence, rather than
re-litigating whether the database connection itself is the problem.
{{% /notice %}}

<!-- ![curl response showing seeded events served from RDS](/images/5-Workshop/5.5-RDS/5.5.3-verify/example.png) -->
