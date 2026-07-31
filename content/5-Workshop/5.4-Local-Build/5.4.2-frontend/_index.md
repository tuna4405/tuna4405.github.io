---
title : "Frontend: React with Mock Data"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

1. **Scaffold a Vite + React application** and build the three screens that
   do not need a live backend to be useful: the event list, the seat picker,
   and the bookings list.

2. **Shape mock data exactly like the frozen API spec**, in plain JSON files
   under `src/mocks/` - not approximately like it, but field-for-field
   identical, including the camelCase naming and the nested `pagination`
   object on the events list. A mock that is close-but-not-quite the real
   shape teaches the frontend the wrong lesson and produces a second round of
   bugs on integration day that have nothing to do with the network.

3. **Wrap every API call in one client module** (`src/api/client.js`),
   never called ad hoc from a component. A single `request()` helper
   attaches the `Authorization` header, parses the standard error envelope,
   and throws a typed `ApiError` the UI can switch on by `code`. Every screen
   calls a named function (`getEvents()`, `createBooking()`) that this module
   exports - never `fetch` directly.

**Why this is worth doing carefully rather than quickly:** the entire benefit
of this approach lands on integration day. Switching every screen from mocks
to a live API becomes a change to one file - which base URL `request()` talks
to - rather than a change scattered across every component that happens to
call an endpoint. The frontend lane in section 5.4.3 was never blocked
waiting on backend progress, and it never had to be partially rewritten once
the real API existed.

<!-- ![Event list running against mock data](/images/5-Workshop/5.4-Local-Build/5.4.2-frontend/example.png) -->
