---
title: "Self-Assessment"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

During my internship at **Amazon Web Services Viet Nam Company Limited** from
**15/06/2026** to **14/08/2026**, as part of the Workforce Bootcamp - First
Cloud Journey programme, I had the opportunity to move from studying cloud
computing in theory to designing, building, and operating a complete system on
AWS.

The first six weeks were spent working through the core service groups -
identity and access management, networking, compute, storage, databases,
serverless, and monitoring - with a practical exercise attached to each. The
final weeks were spent applying all of it to **Caerus**, a cinema seat booking
platform built by a two-person team and deployed across Amazon EC2, Amazon RDS,
Amazon S3, API Gateway and Amazon CloudWatch.

Through this work I improved my skills in cloud architecture and service
selection, relational database design, transactional programming and
concurrency control, API design, deployment and network security configuration,
observability, cost management, and technical documentation. Working in a pair
also taught me the value of agreeing an interface contract before writing code,
which is what allowed two people to build simultaneously rather than one waiting
on the other.

In terms of work ethic, I kept to the schedule agreed at the start of the
project, maintained the daily habits the team committed to - terminating
practice resources the same day and tagging every resource with its owner - and
raised problems with my teammate early rather than working around them alone.

To reflect objectively on the internship period, I evaluate myself against the
following criteria:

| No. | Criteria | Description | Good | Fair | Average |
| --- | --- | --- | --- | --- | --- |
| 1 | **Professional knowledge & skills** | Understanding of the field, applying knowledge in practice, proficiency with tools, work quality | ✅ | ☐ | ☐ |
| 2 | **Ability to learn** | Ability to absorb new knowledge and learn quickly | ✅ | ☐ | ☐ |
| 3 | **Proactiveness** | Taking initiative, seeking out tasks without waiting for instructions | ☐ | ✅ | ☐ |
| 4 | **Sense of responsibility** | Completing tasks on time and ensuring quality | ✅ | ☐ | ☐ |
| 5 | **Discipline** | Adhering to schedules, rules, and work processes | ✅ | ☐ | ☐ |
| 6 | **Progressive mindset** | Willingness to receive feedback and improve oneself | ✅ | ☐ | ☐ |
| 7 | **Communication** | Presenting ideas and reporting work clearly | ☐ | ✅ | ☐ |
| 8 | **Teamwork** | Working effectively with colleagues and participating in teams | ✅ | ☐ | ☐ |
| 9 | **Professional conduct** | Respecting colleagues, partners, and the work environment | ✅ | ☐ | ☐ |
| 10 | **Problem-solving skills** | Identifying problems, proposing solutions, and showing creativity | ✅ | ☐ | ☐ |
| 11 | **Contribution to project/team** | Work effectiveness, innovative ideas, recognition from the team | ✅ | ☐ | ☐ |
| 12 | **Overall** | General evaluation of the entire internship period | ✅ | ☐ | ☐ |

### What Went Well

* **Learning breadth.** I began the programme with no practical AWS experience
  and finished it able to design a multi-service architecture and justify each
  service choice against an alternative rather than by default.

* **Working from a contract.** Agreeing the API specification and database
  schema before writing any code was the single decision that kept the project
  on schedule. It meant the frontend was never blocked waiting for the backend,
  and it made integration a matter of fixing small mismatches rather than
  reconciling two incompatible designs.

* **Solving the problem the project was actually about.** Seat booking under
  concurrency is not a user-interface problem or an infrastructure problem; it
  is a transaction problem. I learned to lock the contested rows explicitly,
  order the locks consistently to avoid deadlocks, and then prove the guarantee
  with a deliberate two-client race rather than assuming it held.

* **Cost discipline.** Setting a billing alarm before provisioning anything,
  tagging every resource with an owner, and terminating practice resources the
  same day meant cost never became a problem that had to be dealt with
  retrospectively.

### Needs Improvement

* **Proactiveness beyond the plan.** I executed the agreed schedule reliably,
  but I tended to work through the task list rather than looking ahead and
  proposing improvements before they became necessary. Several issues we hit
  during deployment were foreseeable, and I should have raised them at design
  time instead of discovering them under time pressure.

* **Communication and presentation.** I am comfortable explaining technical
  decisions in writing, but less confident presenting them verbally to an
  audience that has not read the documents. This is the skill I would most like
  to develop next.

* **Depth beyond the working path.** The system works and is verified against
  its core guarantee, but the engineering around it is thinner than I would like:
  deployment is manual rather than automated, infrastructure was created through
  the console rather than defined as code, and automated testing is limited to
  the concurrency case rather than covering the API broadly.

* **Production-grade architecture.** The application runs on a single instance
  in a public subnet. I understand what a more robust deployment would involve -
  private subnets with a bastion or session-based access, a load balancer, an
  auto scaling group, and a multi-AZ database - but I have not yet built one, so
  my knowledge of those patterns is conceptual rather than practical.

* **Estimating time.** My original estimates for deployment and cross-origin
  configuration were optimistic. The buffer days built into the plan absorbed
  the overrun, but the estimates themselves need to improve.
