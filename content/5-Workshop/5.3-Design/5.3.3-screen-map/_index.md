---
title : "Screens and User Flows"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.3.3 </b> "
---

Two paths through the same set of screens, distinguished by the `role` on
the JWT rather than by a separate application.

**Customer path:**

| Screen | Calls |
|---|---|
| Home (event list) | `GET /events` |
| Event detail | `GET /events/:id`, `GET /events/:id/seats` |
| Seat selection / confirm booking | `POST /bookings` |
| My bookings | `GET /bookings` |
| Booking detail | `GET /bookings/:id`, `DELETE /bookings/:id`, `POST /bookings/:id/ticket` |
| Login / Register | `POST /auth/login`, `POST /auth/register` |

**Admin path** (same login screen, `role: "admin"` on the token):

| Screen | Calls |
|---|---|
| Manage screenings (list) | `GET /events` |
| Create screening | `POST /events` |
| Upload poster | `POST /events/:id/banner` |

{{% notice note %}}
There is deliberately no "edit screening" or "delete screening" screen. Once
seats exist for an event, changing its shape retroactively is a much harder
problem than creating a new one - out of scope for this project, and worth
saying so explicitly rather than leaving a silent gap in the screen map.
{{% /notice %}}

![Screen map: customer path and admin path](/images/5-Workshop/5.3-Design/5.3.3-screen-map/caerus_screen_map.png)
