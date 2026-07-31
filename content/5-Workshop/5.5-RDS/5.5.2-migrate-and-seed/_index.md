---
title : "Run the Migration and Seed"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

1. **Copy the RDS endpoint** from the instance's Connectivity & Security tab
   - a hostname like `caerus-db.xxxxxxxxxx.ap-southeast-1.rds.amazonaws.com`,
   never an IP address, since RDS may change the underlying address on
   failover.

2. **Run the same two files used locally**, against the endpoint instead of
   `localhost:5433`:

   ```bash
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -f db/migrations/001_init.sql
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -f db/seed.sql
   ```

3. **Confirm the tables landed:**

   ```bash
   psql "postgresql://<user>:<password>@<rds-endpoint>:5432/caerus" -c "\dt"
   ```

That these are the exact same files run against Docker in section 5.4.1,
byte for byte, is the entire justification for keeping migrations as plain
SQL rather than behind a framework-specific migration runner: there is no
second implementation to keep in sync with the first, and no risk of the
managed database silently diverging in schema from what was tested locally.

<!-- ![psql session against the RDS endpoint, migration and seed output](/images/5-Workshop/5.5-RDS/5.5.2-migrate-and-seed/example.png) -->
