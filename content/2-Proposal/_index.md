---
title: "Proposal"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Caerus - A Cinema Seat Booking Platform
## Guaranteeing Seat Integrity Under Concurrency with AWS Managed Services

### 1. Executive Summary

Caerus is a cinema seat booking web application built to demonstrate a complete
AWS deployment across compute, storage, database, serverless, and monitoring
services. Customers browse screenings, pick seats from a live seat map, book up
to six seats in a single transaction, view their bookings, cancel before
showtime, and download a PDF ticket. Administrators create screenings and upload
poster images.

The project's defining technical requirement is that **a seat can never be sold
twice**, even when two customers select the same seat at the same instant. This
single constraint drives the choice of a relational database, the row-level
locking strategy in the booking transaction, and the testing plan. Everything
else in the system exists to serve a correct booking.

The platform runs in the `ap-southeast-1` region and is built by a two-person
team over four weeks, with the backend and frontend developed in parallel
against a shared API contract agreed before any code was written.

### 2. Problem Statement

#### What's the Problem?

Selling reserved seats is deceptively hard. The naive implementation - read the
seat's availability, then write a booking - contains a race condition: two
requests can both read "available" before either writes, and the cinema sells
one chair twice. The failure is silent, intermittent, and only appears under the
exact conditions that matter most, when a popular screening opens and many
customers converge on the same few good seats.

A student project that ignores this produces a booking system that appears to
work in a demo and is fundamentally broken in production. A project that
addresses it has to reason about transactions, isolation, and locking - which is
precisely the kind of problem cloud infrastructure is meant to support rather
than solve on its own.

Beyond correctness, a small cinema operator has no infrastructure team. Any
solution must be inexpensive to run, require no server administration for
routine operation, and remain observable enough that a failure is noticed before
customers report it.

#### The Solution

Caerus stores seats, bookings, and users in Amazon RDS for PostgreSQL and
performs booking inside a single database transaction that locks the requested
seat rows with `SELECT ... FOR UPDATE` before verifying availability. Two
concurrent requests for the same seat cannot both succeed: one acquires the row
lock, the other blocks, and when the winner commits, the loser sees the updated
status, rolls back, and receives a `409 SEAT_ALREADY_BOOKED` response naming the
conflicting seats so the interface can highlight them. Either every requested
seat is booked or none is.

The remaining components are chosen to keep operational cost and effort low. The
React frontend is a static site served directly from Amazon S3, so there is no
web server to maintain. The Express API runs on a single Amazon EC2 instance.
Poster images and generated PDF tickets live in S3. Ticket generation - an
infrequent, bursty, stateless operation - runs on AWS Lambda behind Amazon API
Gateway rather than consuming capacity on the API instance. Amazon CloudWatch
collects metrics and application logs, and an alarm publishes to Amazon SNS when
the booking failure rate exceeds five percent.

#### Benefits and Return on Investment

The system replaces manual or spreadsheet-based seat allocation with an
interface customers operate themselves, and it does so with a correctness
guarantee that can be demonstrated rather than asserted. For the operator, the
running cost is negligible: the entire architecture sits within the AWS Free
Tier for the first twelve months, and the components that would eventually
dominate the bill - the EC2 instance and the RDS instance - are the two that
would be resized first if demand grew.

For the team, the project produces transferable evidence of competence across
seven AWS services, a working comparison between EC2-hosted and Lambda-hosted
implementations of equivalent endpoints, and a concrete argument for relational
over NoSQL storage grounded in an actual invariant rather than a textbook
example.

### 3. Solution Architecture

The architecture separates three concerns: static content delivery, transactional
compute, and asynchronous document generation. The browser loads the React
application from an S3 static website endpoint, then makes REST calls to the
Express API on EC2 for all core operations. Ticket generation is routed
separately through API Gateway to a Lambda function, which reads the booking
from the same RDS database and writes the resulting PDF to S3, returning a
time-limited pre-signed download URL.

![Caerus architecture](/images/2-Proposal/architecture.png)

#### AWS Services Used

- **Amazon EC2**: Hosts the Express API server under `pm2`. A single instance in
  a public subnet handles authentication, event listing, seat maps, booking, and
  cancellation.
- **Amazon RDS for PostgreSQL**: Stores the five core tables - `users`,
  `events`, `seats`, `bookings`, and `booking_seats`. Chosen for ACID
  transactions and row-level locking.
- **Amazon S3**: Three roles across two buckets - static hosting for the React
  build, storage for event posters, and storage for generated PDF tickets.
- **AWS Lambda**: One function generating PDF tickets on demand. Stateless,
  invoked rarely, and billed only while running.
- **Amazon API Gateway**: Fronts the Lambda function with a REST route, keeping
  the request and response shapes identical to the equivalent EC2 endpoint.
- **Amazon CloudWatch**: Dashboards for EC2 CPU, RDS connections and latency,
  Lambda duration and errors, and API Gateway status codes; log groups for
  Express application logs; one alarm on booking failure rate.
- **Amazon SNS**: Delivers alarm notifications by email.
- **AWS IAM**: Two developer users in a shared group, instance and execution
  roles granting scoped S3 and RDS access without embedded credentials.
- **Amazon VPC**: Default VPC with a public subnet, an internet gateway, and a
  gateway endpoint for S3 so image and ticket traffic between the instance and
  S3 does not traverse the public internet.

#### Component Design

- **Frontend**: A Vite and React single-page application built to static assets
  and synchronised to the site bucket. All API calls pass through one client
  module, so redirecting the application from mock data to the local API and
  then to the deployed API is a single change.
- **API layer**: Express with routes, thin controllers, and services holding the
  actual logic. Authentication is stateless JSON Web Tokens; passwords are
  hashed with bcrypt. The booking transaction lives in one service function.
- **Data layer**: Seats belong to a screening rather than to a physical room, so
  availability is unambiguous per showing. Each screening generates a fixed
  layout of six rows by ten seats. Seat availability is stored as a column so
  there is a concrete row to lock; total and available seat counts are computed
  at query time so they cannot drift.
- **Ticket generation**: A Lambda function renders the PDF, writes it to the
  tickets bucket, and returns a pre-signed URL with a short expiry rather than
  making the object public.
- **Money and time**: Prices are stored as integers in Vietnamese dong, never
  floats. The booking total is snapshotted at purchase so a later price change
  cannot rewrite history. Timestamps are stored and transmitted in UTC but
  represent showtimes in `Asia/Ho_Chi_Minh`, and date filtering is performed
  against the Vietnamese calendar date.

### 4. Technical Implementation

#### Implementation Phases

**Phase 1 - Design the contract (2 days).** Agree the API specification and the
database schema in a single session before any code is written, and freeze both
as documents that neither developer changes unilaterally. This is what allows
the backend and frontend to be built simultaneously rather than sequentially.

**Phase 2 - Parallel local development (5 days).** The backend implements the
Express API against a Dockerised PostgreSQL container, including the booking
transaction. The frontend builds the event list, seat picker, and bookings
screens against mock JSON files shaped exactly like the agreed responses.
Integration follows, connecting the interface to the live API and resolving
mismatches.

**Phase 3 - Migrate to managed AWS services (7 days).** The same migration and
seed SQL files run against RDS with a different connection string. Both S3
buckets are created and static hosting enabled. The API is deployed to EC2 under
`pm2`, security groups are narrowed so the database accepts traffic only from
the application instance, and the production frontend build is published to S3.

**Phase 4 - Serverless, monitoring, and verification (7 days).** Ticket
generation moves to Lambda behind API Gateway. CloudWatch dashboards, log
shipping, and the failure-rate alarm are configured. The concurrency test is
executed against the deployed system, followed by edge-case testing and polish.

#### Technical Requirements

- **Local development**: Node.js 20 or later, Docker Desktop running PostgreSQL
  16, and the AWS CLI. Local ports are fixed at 5173 for the development server,
  3000 for the API, and 5433 for the database container.
- **Runtime**: Amazon Linux on a Free Tier eligible EC2 instance class, Node.js
  managed by `pm2` for restart-on-boot, and a Free Tier eligible RDS instance
  class running PostgreSQL.
- **Access control**: Every IAM role created for the project carries a
  `caerus-` name prefix, enforced by the account's permission boundary. Every
  resource is tagged with an `Owner` value identifying which developer created
  it, so costs can be attributed in Cost Explorer.
- **Cross-origin access**: The static site and the API occupy different origins,
  so the API must return CORS headers permitting the site bucket's website
  endpoint and the local development server.

### 5. Timeline & Milestones

The project runs for four weeks inside the wider internship period.

- **Week 1 - Fundamentals and local build.** AWS core concepts, account and IAM
  setup, the design session, parallel development, and local integration.
  *Milestone: the complete application running on localhost.*
- **Week 2 - Managed services and deployment.** RDS, S3, and EC2 studied and
  then used; the application deployed and reachable on a public address.
  *Milestone: live on AWS.*
- **Week 3 - Serverless and observability.** Lambda and API Gateway, CloudWatch
  dashboards and alarms, then concurrency and edge-case testing.
  *Milestone: feature-complete and verified.*
- **Week 4 - Report and demonstration.** Written report, screenshots assembled,
  demonstration script and backup recording prepared.
  *Milestone: submitted.*

Two buffer days are reserved at the end of Week 2 and two more at the end of
Week 3, on the assumption that deployment and CORS configuration will consume
more time than planned.

### 6. Budget Estimation

<!-- REPLACE the figures below with your own AWS Pricing Calculator estimate and
     paste the share link here, matching how the template does it. -->

The architecture is designed to run at no cost during the twelve-month Free Tier
window, with a billing alarm configured at a ten US dollar threshold as a guard
against mistakes rather than as an expected spend.

#### Infrastructure Costs

- **Amazon EC2**: One Free Tier eligible instance, within the 750 hours per
  month allowance. Exceeding the allowance requires running more than one
  instance simultaneously, which the daily terminate-after-practice habit is
  intended to prevent.
- **Amazon RDS**: One Free Tier eligible single-AZ instance with 20 GB of
  storage, within the 750 hours per month allowance.
- **Amazon S3**: Well under the 5 GB Free Tier allowance. Storage consists of a
  small React build, poster images, and generated PDF tickets.
- **AWS Lambda**: Within the one million free requests per month, which is
  permanent rather than twelve-month.
- **Amazon API Gateway**: Within the one million calls per month allowance.
- **Amazon CloudWatch**: Within the free allowance for metrics, dashboards, and
  log ingestion at this volume.
- **Data transfer**: Nominal at demonstration traffic levels.

**Estimated total: [VERIFY] per month during the Free Tier period.**

The principal cost risk is not steady-state usage but forgotten resources. A NAT
Gateway, an unattached elastic IP, or an instance left running after a practice
exercise would each exceed the entire rest of the estimate. This is mitigated by
tagging every resource with an owner, terminating practice resources the same
day they are created, and reviewing Cost Explorer grouped by owner tag whenever
the billing alarm fires.

### 7. Risk Assessment

#### Risk Matrix

| Risk | Impact | Probability |
|---|---|---|
| Double-booked seat under concurrent requests | High | Medium |
| Cross-origin request failures after deployment | Low | High |
| Cost overrun from forgotten or untagged resources | Medium | Medium |
| Single EC2 instance becomes unavailable | High | Low |
| Database exposed to the public internet | High | Low |
| Deployment consumes time reserved for later phases | Medium | Medium |

#### Mitigation Strategies

- **Concurrency**: Lock seat rows with `SELECT ... FOR UPDATE` ordered by
  primary key, so every concurrent transaction acquires locks in the same
  sequence and cannot deadlock. Verify by conditional update and assert the
  affected row count, so a code path that bypasses the lock is detected rather
  than silently tolerated.
- **Cross-origin requests**: Treat CORS as an expected task rather than a
  surprise, allocate time for it in the deployment phase, and configure the
  permitted origins on the API rather than working around the browser.
- **Cost**: Billing alarm at a low threshold, an owner tag on every resource,
  same-day termination of practice resources, and read-only billing access for
  developer users so the alarm configuration cannot be disabled accidentally.
- **Availability**: Accepted rather than mitigated. A single instance is a
  deliberate scope decision appropriate to the project's scale; the report
  documents what a production deployment would add - a load balancer, an
  auto-scaling group, and a multi-AZ database.
- **Database exposure**: The database security group accepts connections only
  from the application instance's security group, not from arbitrary addresses.
- **Schedule**: Buffer days at the end of the two heaviest weeks, and a
  frontend that works against mock data so it is never blocked waiting for the
  backend.

#### Contingency Plans

- If the deployed environment fails during the demonstration, present against
  the local Docker environment, which runs the identical schema and code.
- If ticket generation on Lambda is not completed in time, the endpoint remains
  available on the API server, since the specification defines the contract
  independently of which service implements it.
- Record a demonstration video in advance so a live failure does not prevent
  the work from being shown.

### 8. Expected Outcomes

#### Technical Improvements

A publicly reachable booking application in which seat availability is accurate
under concurrent load, verified by a deliberate two-client race for the same
seat producing exactly one successful booking and one conflict response.
Operational visibility through dashboards covering all four service categories,
with an alarm proven to fire and notify rather than merely configured.

#### Long-term Value

The project produces a working comparison of two compute models - a persistent
server and an on-demand function - implementing endpoints against the same
database with identical contracts, which is the clearest available illustration
of why the compute choice is an operational decision rather than a functional
one. It also produces an evidence-based argument for relational storage: the
booking invariant cannot be expressed as cheaply without transactions, while an
auxiliary feature such as recently viewed events would sit naturally in a
key-value store. Both arguments are grounded in this system rather than in a
general comparison, and both are reusable in future design work.
