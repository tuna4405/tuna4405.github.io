---
title : "Introduction"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Overview

Caerus is a cinema seat booking application. Every screening has a fixed 6x10
layout - six rows, ten seats each - and a customer can hold up to six seats in
a single booking. Administrators create screenings and attach a poster image;
customers browse upcoming screenings, open a seat map, book, view their
bookings, cancel before showtime, and download a PDF ticket.

The problem this project actually demonstrates is not seat booking as CRUD -
it is what happens when two customers select the same seat at the same
instant. A naive implementation reads a seat's availability and then writes a
booking as two separate steps, leaving a window in which both requests read
"available" before either commits. The cinema sells one chair twice, silently,
and only under exactly the load conditions a real cinema cares about. Every
architectural decision downstream of this - the choice of a relational
database, the `SELECT ... FOR UPDATE` row lock inside the booking transaction,
and the dedicated concurrency test in [Testing](/5-Workshop/5.9-Testing/) -
exists to close that window.

**AWS services used, and why:**

- **Amazon EC2** - runs the Express API under `pm2`, two instances across two
  Availability Zones so the API survives the loss of one instance, sitting in
  private subnets with no public IP and no inbound SSH at all.
- **NAT Gateway** - gives those private-subnet instances outbound-only
  internet access for `npm ci` and OS patching, without ever accepting an
  inbound connection.
- **AWS Systems Manager** - Session Manager provides interactive shell access
  to both instances for deployment and debugging over the same outbound path
  the NAT gateway already provides, replacing SSH entirely.
- **Amazon RDS for PostgreSQL** - the five core tables (`users`, `events`,
  `seats`, `bookings`, `booking_seats`), chosen specifically for row-level
  locking and transactional guarantees; deployed Multi-AZ inside its own
  private subnet, separate from the EC2 instances', so the database has no
  route to the internet at all.
- **Amazon S3** - static hosting for the built React site (private, served
  only through CloudFront), storage for event posters, storage for generated
  PDF tickets, and a fourth bucket used only to stage the backend deployment
  package.
- **Application Load Balancer** - the single entry point for API traffic
  across both EC2 instances, replacing a security group rule that used to
  admit traffic from "my IP" with one that admits traffic only from the load
  balancer.
- **Amazon CloudFront** - one distribution, one HTTPS domain, routing `/api/*`
  to the load balancer and everything else to the S3 site bucket by path
  pattern, with the origin access control locking the bucket to CloudFront
  only.
- **AWS WAF** - bound to the CloudFront distribution, inspecting every request
  against managed rule groups at the edge before it reaches the load balancer
  or the S3 origin.
- **Amazon CloudWatch and SNS** - a dashboard across EC2, RDS, and the load
  balancer, an application log group, and alarms that page over email rather
  than sitting silently in the OK state.
- **AWS IAM** - one instance role for EC2 scoped to exactly the S3 prefixes and
  Systems Manager permissions it needs, every role name carrying the
  `caerus-` prefix the account enforces.
- **Amazon VPC** - the default VPC extended with a private subnet pair for the
  EC2 instances, a separate private subnet pair for the database, and a
  gateway endpoint so EC2-to-S3 traffic never leaves the AWS network.

Ticket generation - rendering the PDF and writing it to S3 - runs in-process
inside the same Express API, not as a separate function; it was built and
deployed as an AWS Lambda function early on and deliberately moved back once
the workload turned out too small to justify a second deployable with its own
IAM role and deploy step. The architecture below is the end state, with no
Lambda in it.

The architecture diagram below is the end state this workshop arrives at, not
the starting point - the sections that follow build it up in the same order
the diagram reads: database and storage first, then compute, then the private
networking and CDN layers, then observability.

![Caerus final architecture](/images/5-Workshop/5.1-Overview/architecture.png)

<!-- NOTE for the report author: use the reviewed architecture diagram, with
the frontend S3 bucket relabelled "S3 (Frontend)" rather than "Static
Website" - static website hosting on that bucket was disabled once
CloudFront + OAC took over in section 5.7.6. -->
