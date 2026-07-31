---
title : "System Design"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Overview

Before either developer wrote a line of application code, the team agreed and
froze two documents: the API specification and the database schema. "Frozen"
meant literally that - neither document could be changed by one person
without the other's agreement, and every later change is recorded in each
document's own changelog rather than silently edited in place. This section
covers both contracts plus the screen map that ties them to what a user
actually sees. There is no AWS Console work in this section; everything here
is design that has to be right before section 5.4 starts building against it.


#### Content

- [API Specification](5.3.1-api-spec/)
- [Database Schema](5.3.2-database-schema/)
- [Screens and User Flows](5.3.3-screen-map/)

