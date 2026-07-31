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
AWS deployment across compute, storage, database, networking, and monitoring
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
against a shared API contract agreed before any code was written. The compute
and data layers run behind two independent load-balancing/failover boundaries -
an Application Load Balancer across two EC2 instances, and a Multi-AZ RDS
database - with both instances and the database sitting in private subnets with
no direct route to the internet.

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
solution must tolerate a single instance failing without an outage, keep the
database and the application servers off the public internet, and remain
observable enough that a failure is noticed before customers report it.

#### The Solution

Caerus stores seats, bookings, and users in Amazon RDS for PostgreSQL and
performs booking inside a single database transaction that locks the requested
seat rows with `SELECT ... FOR UPDATE` before verifying availability. Two
concurrent requests for the same seat cannot both succeed: one acquires the row
lock, the other blocks, and when the winner commits, the loser sees the updated
status, rolls back, and receives a `409 SEAT_ALREADY_BOOKED` response naming the
conflicting seats so the interface can highlight them. Either every requested
seat is booked or none is.

The remaining components are chosen for genuine production-shaped guarantees
rather than for the cheapest possible demo. The React frontend is a static
build served through Amazon CloudFront from a private S3 bucket. The Express
API runs on two Amazon EC2 instances, in private subnets behind an Application
Load Balancer, so either instance can be lost without an outage; outbound
package installs and OS patching reach the internet through a NAT gateway, and
the instances are administered through AWS Systems Manager Session Manager
rather than SSH, so no inbound port is open to the internet at all. RDS runs
Multi-AZ, also in a private subnet with no route to the internet. Poster
images and generated PDF tickets live in S3, rendered by the API itself and
served through short-lived presigned URLs. CloudFront also fronts the API
through the load balancer, so the whole application - site and API - is
reachable over HTTPS on a single domain, with AWS WAF inspecting traffic at the
edge before it reaches the load balancer at all. Amazon CloudWatch collects
metrics and application logs, and alarms publish to Amazon SNS when a target
becomes unhealthy or the database approaches its resource limits.

#### Benefits and Return on Investment

The system replaces manual or spreadsheet-based seat allocation with an
interface customers operate themselves, and it does so with a correctness
guarantee that can be demonstrated rather than asserted. For the operator, the
architecture is deliberately built to the same shape a production deployment
would use rather than the cheapest shape that would pass a demo: no single
instance failure takes the site down, the database fails over automatically,
and neither the compute layer nor the data layer is directly reachable from
the internet. Every resource is tagged by owner and tracked against a billing
alarm, so the real running cost is a known, monitored number rather than a
surprise.

For the team, the project produces transferable evidence of competence across
eight AWS services, a concrete demonstration of defense-in-depth networking
(private subnets at both the compute and data tiers, a NAT gateway for
outbound-only access, a load balancer and a CDN with WAF as the only public
entry points), and a concrete argument for relational over NoSQL storage
grounded in an actual invariant rather than a textbook example.

### 3. Solution Architecture

The architecture separates two concerns: static content delivery and
transactional compute, both reachable through one CDN edge. The browser loads
the React application and calls the Express API through the same Amazon
CloudFront distribution, which routes by path - `/api/*` to the Application
Load Balancer, everything else to the private S3 site bucket. The API handles
every operation itself, including rendering the ticket PDF, and writes to S3
and RDS from inside the VPC.

![Caerus architecture](/images/2-Proposal/architecture.png)

<!-- NOTE for the report author: replace with the final reviewed diagram
showing ALB + two private-subnet EC2 instances + NAT gateway, Multi-AZ RDS in
a private subnet, and CloudFront (with WAF) fronting both the ALB and the S3
site bucket. -->

#### AWS Services Used

- **Amazon EC2**: Hosts the Express API server under `pm2`, two instances
  across two Availability Zones in private subnets, handling authentication,
  event listing, seat maps, booking, cancellation, and ticket PDF generation.
- **Application Load Balancer**: The only path into the EC2 instances,
  distributing traffic across both and health-checking each one continuously.
- **NAT Gateway**: Gives the private-subnet EC2 instances outbound-only
  internet access for package installs and OS patching, without ever accepting
  an inbound connection from the internet.
- **Amazon RDS for PostgreSQL**: Stores the five core tables - `users`,
  `events`, `seats`, `bookings`, and `booking_seats`. Chosen for ACID
  transactions and row-level locking; deployed Multi-AZ in a private subnet
  with automatic failover to a standby.
- **Amazon S3**: Four buckets - private static hosting for the React build
  (served only through CloudFront), storage for event posters, storage for
  generated PDF tickets, and a staging bucket for the backend deployment
  package.
- **Amazon CloudFront**: One distribution, one HTTPS domain, path-based
  routing between the S3 site bucket and the load balancer, with Origin
  Access Control keeping the site bucket private.
- **AWS WAF**: Bound to the CloudFront distribution, inspecting every request
  at the edge against managed rule groups before it reaches the load balancer
  or the S3 origin.
- **Amazon CloudWatch**: Dashboards for EC2 CPU, RDS connections/storage/CPU,
  and load balancer request count and target health; log groups for Express
  application logs; alarms on target health and database resource pressure.
- **Amazon SNS**: Delivers alarm notifications by email.
- **AWS IAM**: Two developer users in a shared group, an EC2 instance role
  granting scoped S3 access and Systems Manager management, without embedded
  credentials anywhere.
- **AWS Systems Manager**: Session Manager gives shell access to both EC2
  instances for deployment and debugging, entirely over the same outbound
  path the NAT gateway already provides - no SSH port is exposed.
- **Amazon VPC**: A public subnet pair for the load balancer and NAT gateway,
  a private subnet pair for the two EC2 instances, a separate private subnet
  pair for RDS, and a gateway endpoint so EC2-to-S3 traffic never leaves the
  AWS network.

#### Component Design

- **Frontend**: A Vite and React single-page application built to static
  assets and synchronised to the site bucket. All API calls pass through one
  client module, so redirecting the application from mock data to the local
  API and then to the deployed API is a single change.
- **API layer**: Express with routes, thin controllers, and services holding
  the actual logic. Authentication is stateless JSON Web Tokens; passwords are
  hashed with bcrypt. The booking transaction lives in one service function.
- **Data layer**: Seats belong to a screening rather than to a physical room,
  so availability is unambiguous per showing. Each screening generates a fixed
  layout of six rows by ten seats. Seat availability is stored as a column so
  there is a concrete row to lock; total and available seat counts are
  computed at query time so they cannot drift.
- **Ticket generation**: The API renders the PDF in-process, writes it to the
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
transaction and PDF ticket rendering. The frontend builds the event list, seat
picker, and bookings screens against mock JSON files shaped exactly like the
agreed responses. Integration follows, connecting the interface to the live
API and resolving mismatches.

**Phase 3 - Migrate to managed AWS services (7 days).** The same migration and
seed SQL files run against a Multi-AZ RDS instance in a private subnet. All
four S3 buckets are created. The API is deployed to two EC2 instances in
private subnets behind an Application Load Balancer, reachable outbound only
through a NAT gateway and administered through Systems Manager Session
Manager rather than SSH; security groups are narrowed so the database accepts
traffic only from the application tier and the application tier accepts
traffic only from the load balancer.

**Phase 4 - CDN, monitoring, and verification (7 days).** Amazon CloudFront is
placed in front of both the load balancer and the S3 site bucket for a single
HTTPS domain, with AWS WAF enabled at the edge. CloudWatch dashboards, log
shipping, and health/resource alarms are configured. The concurrency test is
executed against the deployed system, followed by edge-case testing and
polish.

#### Technical Requirements

- **Local development**: Node.js 20 or later, Docker Desktop running PostgreSQL
  16, and the AWS CLI. Local ports are fixed at 5173 for the development server,
  3000 for the API, and 5433 for the database container.
- **Runtime**: Amazon Linux on EC2, Node.js managed by `pm2` for
  restart-on-boot, administered exclusively through Systems Manager Session
  Manager (no SSH), and a Multi-AZ RDS instance running PostgreSQL in a
  private subnet.
- **Access control**: Every IAM role created for the project carries a
  `caerus-` name prefix, enforced by the account's permission boundary. Every
  resource is tagged with an `Owner` value identifying which developer created
  it, so costs can be attributed in Cost Explorer.
- **Cross-origin access**: In production, the frontend and the API are served
  from the same CloudFront domain, so no cross-origin request ever leaves the
  browser. CORS only needs to permit the local Vite dev server talking to a
  local or deployed API during development.

### 5. Timeline & Milestones

The project runs for four weeks inside the wider internship period.

- **Week 1 - Fundamentals and local build.** AWS core concepts, account and IAM
  setup, the design session, parallel development, and local integration.
  *Milestone: the complete application running on localhost.*
- **Week 2 - Managed services and deployment.** RDS, S3, and EC2 studied and
  then used; the application deployed behind a load balancer and reachable on
  a public address.
  *Milestone: live on AWS.*
- **Week 3 - Networking, CDN, and observability.** Private subnets and NAT for
  the compute tier, CloudFront and WAF for a single HTTPS domain, CloudWatch
  dashboards and alarms, then concurrency and edge-case testing.
  *Milestone: feature-complete and verified.*
- **Week 4 - Report and demonstration.** Written report, screenshots assembled,
  demonstration script and backup recording prepared.
  *Milestone: submitted.*

Two buffer days are reserved at the end of Week 2 and two more at the end of
Week 3, on the assumption that networking and CDN configuration will consume
more time than planned.

### 6. Budget Estimation

<!-- TODO: generate an exact estimate at https://calculator.aws for the final
     instance types/sizes chosen and paste the share link on the line below,
     replacing this comment. -->

This architecture is intentionally not optimised to stay inside the Free
Tier - a load balancer, a NAT gateway, and a Multi-AZ database are all real,
by-the-hour costs regardless of traffic, taken on deliberately for the
production-shaped guarantees described in Section 3.

#### Infrastructure Costs

| Component | Why it costs | Rough cost |
|---|---|---|
| Application Load Balancer | Billed hourly regardless of traffic, plus LCU-hours under load | ~US$16/month baseline |
| NAT Gateway | Billed hourly plus per-GB data processing | ~US$32-35/month baseline |
| RDS Multi-AZ | Standby instance billed identically to the primary | roughly double a single-AZ instance of the same class |
| Amazon EC2 (×2) | Two instances running continuously; combined hours exceed the Free Tier's 750-hour allowance once both run a full month | modest, instance-class dependent |
| Amazon CloudFront + WAF | Usage-based (per GB, per 10,000 HTTPS requests, per WAF rule evaluated) | a few dollars at demonstration traffic |
| Amazon S3 | Well under the Free Tier allowance at this data volume | negligible |
| Data transfer | Nominal at demonstration traffic levels | negligible |

**Estimated total: roughly US$90-110 per month** at demonstration traffic
levels, dominated by the NAT gateway and the load balancer's hourly charges
rather than by actual usage - both continue billing at the same rate whether
the application serves one request a day or one thousand. Replace this figure
with the AWS Pricing Calculator link noted above once generated against the
exact instance classes chosen.

The billing alarm is set well above this expected run-rate (around US$150)
rather than at a token guardrail value - at this architecture's cost profile,
a low threshold would fire on ordinary operation rather than only on a
genuine mistake. Every resource still carries the `Owner` tag, so Cost
Explorer grouped by owner attributes any spike to a specific developer within
seconds regardless of what the alarm threshold is set to.

### 7. Risk Assessment

#### Risk Matrix

| Risk | Impact | Probability |
|---|---|---|
| Double-booked seat under concurrent requests | High | Medium |
| Cross-origin request failures during local development | Low | Medium |
| Monthly cost exceeds the estimate (idle NAT/ALB hours add up even at zero traffic) | Medium | Medium |
| A misconfigured edge rule (CDN cache policy, WAF managed rule) silently breaks a legitimate request | Medium | Medium |
| Losing administrative access to a private-subnet EC2 instance | Medium | Low |
| Deployment consumes time reserved for later phases | Medium | Medium |

#### Mitigation Strategies

- **Concurrency**: Lock seat rows with `SELECT ... FOR UPDATE` ordered by
  primary key, so every concurrent transaction acquires locks in the same
  sequence and cannot deadlock. Verify by conditional update and assert the
  affected row count, so a code path that bypasses the lock is detected rather
  than silently tolerated.
- **Cross-origin requests**: Treat CORS as a local-development-only concern,
  since production traffic is same-origin through CloudFront; configure the
  permitted local origins on the API explicitly rather than working around
  the browser.
- **Cost**: A billing alarm set against the realistic run-rate (not a token
  value), an owner tag on every resource, and read-only billing access for
  developer users so the alarm configuration cannot be disabled accidentally.
- **Edge misconfiguration**: Treat the CDN cache policy and WAF as things that
  can silently break a legitimate request rather than only ever blocking
  attackers - verify a real request/response round trip through the edge for
  every content type (JSON API responses, file uploads) before considering
  that layer done, not just for static assets.
- **Administrative access**: Systems Manager Session Manager depends on the
  instance reaching AWS's SSM endpoints outbound, which in turn depends on
  the NAT gateway; if that path is ever broken, the fallback is a temporary
  bastion host in a public subnet rather than reopening SSH on the private
  instances.
- **Schedule**: Buffer days at the end of the two heaviest weeks, and a
  frontend that works against mock data so it is never blocked waiting for the
  backend.

#### Contingency Plans

- If the deployed environment fails during the demonstration, present against
  the local Docker environment, which runs the identical schema and code.
- If CloudFront/WAF configuration is not completed in time, the Application
  Load Balancer's own HTTP endpoint remains directly reachable as a fallback,
  since the API's behavior does not depend on which layer fronts it.
- Record a demonstration video in advance so a live failure does not prevent
  the work from being shown.

### 8. Expected Outcomes

#### Technical Improvements

A publicly reachable booking application in which seat availability is accurate
under concurrent load, verified by a deliberate two-client race for the same
seat producing exactly one successful booking and one conflict response.
Operational visibility through dashboards covering every service category,
with an alarm proven to fire and notify rather than merely configured, and a
compute and data layer that are both unreachable from the internet by network
design rather than by security-group discipline alone.

#### Long-term Value

The project produces a working demonstration of defense-in-depth
infrastructure design: a public edge (CDN plus WAF) as the only entry point,
a load-balanced private compute tier reachable by nothing but that edge, and
a private, Multi-AZ data tier reachable by nothing but the compute tier -
each layer failing closed rather than open if a layer above it is
misconfigured. It also produces an evidence-based argument for relational
storage: the booking invariant cannot be expressed as cheaply without
transactions, while an auxiliary feature such as recently viewed events would
sit naturally in a key-value store. Both arguments are grounded in this
system rather than in a general comparison, and both are reusable in future
design work.
