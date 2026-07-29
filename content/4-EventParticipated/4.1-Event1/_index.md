---
title: "Event 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Summary Report: FCAJ Saturday Meetup

### Event Objectives

- Show what technical roles actually involve day to day, as opposed to what job
  descriptions claim
- Present a production-grade AWS architecture in enough depth to be reasoned
  about rather than admired
- Map the paths available after the programme ends, from student groups to
  partner companies
- Give students a realistic picture of hiring and working culture inside
  multinational corporations

### Speakers

- **Mr. Dat Pham** — Data Analytics Engineer, and **Mr. Cuong Nguyen** — Process
  Engineer: *From real practice to culture at multinational corporations*
- **Dinh Trung Kien** — Lead developer at a startup, and **Nguyen Minh Tho** —
  Student: *A scalable URL shortening service on AWS*
- **Danh Hoang Hieu Nghi** — AI Engineer, AWS Community Builder, AWS Student
  Builder Group Leader: *From First Cloud AI Journey to AWS Partner*
- **Trong H. Truong** — DevOps Engineer at Endava Vietnam: *What does a DevOps
  Engineer really do?*

---

### Key Highlights

#### Talk 1 — What a Data Analytics Engineer really does, and how MNCs work

The first half made a point that applies well beyond analytics: the same job
title means different work depending on the industry, the business model, and
which department the role supports. Two contrasting examples were given. At a
food supply company, the work was daily through quarterly operational reporting,
dashboards for spotting trends and anomalies, root-cause analysis of business
metrics, and coordination across departments. At a consumer goods manufacturer,
it was factory machinery and IoT data, production cost optimisation, and digital
transformation initiatives.

Four skills were identified as the ones that actually differentiate: critical
thinking, communication, storytelling with data, and problem solving. The
emphasis was that a report which only presents numbers is incomplete — the
value comes from explaining *why* a metric moved and proposing what to do about
it.

A career progression model was presented that avoids fixating on job titles:

| Stage | What it means |
|---|---|
| **Follower** | Newly started. Guided step by step, learning the environment and accumulating fundamentals |
| **Learner** | Understands the available approaches but still needs mentor direction. Asks deep questions and learns fast |
| **Problem Solver** | Stops working to a checklist. Analyses hard business problems independently, proposes solutions, and commits to the quality of the output |
| **System Thinker** | Sees the whole picture. Understands cross-department dependencies, anticipates operational risk, weighs financial impact, and optimises the system rather than patching faults |
| **Super Star** | Builds vision and strategy, and develops the next generation of System Thinkers |

The second half covered hiring and culture at multinational corporations. The
standard recruitment pipeline was described as four stages: applicant tracking
system screening followed by a short English conversation with a recruiter; an
aptitude test — logic and algorithms for technical roles, situational judgement
for supply chain; a technical interview with a tech lead or manager working
through real problems using the STAR structure; and finally a culture-fit
interview with leadership assessing alignment with core values.

On culture itself, the talk quoted Dr. Giản Tư Trung's definition — that
corporate culture is how a business thinks, lives, and works, or more concretely
how each individual within it does so. Two contrasting cultures were given as
examples: **no-blame post-mortem** in technology firms, where a serious incident
triggers a search for the root cause and a system fix rather than an individual
to blame; and **caring and inclusive** in consumer goods firms, centred on
people and diversity.

The session closed with a historical arc — Japan's *Wakon Yōsai* principle of
retaining national identity while mastering Western technique, culminating in
the Toyota Production System; Korea's Han River miracle and its export-driven
conglomerates forced to meet strict international standards; and Vietnam's own
path from isolation through Đổi Mới to connecting to the global internet on 19
November 1997, and from there to FDI, manufacturing, and cloud.

#### Talk 2 — A scalable URL shortening service on AWS

This was the most directly technical session, and the most useful one for my own
project. It began with the naive design — user, frontend, backend, database —
and was honest about its trade-offs: easy to deploy and cheap, but vulnerable,
slow on reads, a single point of failure, and hard to scale.

The presented architecture then addressed each of those weaknesses:

- **Edge and frontend**: Route 53 for DNS, CloudFront for distribution, AWS WAF
  for filtering, and Amplify for hosting the static frontend
- **Compute**: containerised services on Amazon ECS with Fargate, spread across
  two Availability Zones behind an Application Load Balancer
- **Data**: Amazon DynamoDB as the durable store, reached through a gateway
  endpoint, with Amazon ElastiCache for Redis in front of it
- **Security**: AWS Secrets Manager, KMS, Certificate Manager, and IAM

The interesting part was the **Key Generation Service**. Rather than generating
a short code at request time and hoping it does not collide, a separate service
pre-generates codes and pushes them onto a Redis queue. Creating a link then
becomes: pop a code from the queue, write the mapping to DynamoDB, return. No
collision checking under load, no retry loop, no guessing under pressure.

Reads use the cache-aside pattern: look up Redis first, return on a hit, and
only query DynamoDB on a miss.

The summary distilled four principles: **separation of concerns** — read and
write paths are optimised independently rather than sharing a bottleneck;
**defense at the edge** — security and caching are pushed as close to the user
as possible so load never reaches the core; **pre-computation over on-demand**;
and the **cache-aside pattern** for keeping read latency low and database
pressure minimal.

#### Talk 3 — From First Cloud AI Journey to AWS Partner

A talk about what comes after the programme. The speaker traced his own path
from the First Cloud Journey programme through the AWS Student Builder Group
programme (formerly AWS Cloud Clubs) and the AWS Community Builder programme, to
working at an AWS partner company.

The line that stuck was *getting the job is just a beginning* — followed by the
roles the programme's alumni have moved into: Solutions Architect, Head of
Solutions Architect, DevOps Engineer, Platform Engineer, Software Engineer. The
point was not that any single path is correct, but that the programme is an
entry point rather than a destination, and that community involvement is what
tends to keep the momentum going afterwards.

#### Talk 4 — What does a DevOps Engineer really do?

The most candid session of the day, and the funniest. Asked why he chose DevOps,
the speaker's answer was: he didn't — it happened.

He contrasted what job descriptions ask for with what people actually assume the
role is:

| The job description asks for | People assume it means |
|---|---|
| CI/CD pipelines | the person who writes CI/CD pipelines |
| IaC and configuration management | the Docker and Kubernetes person |
| Containers and orchestration | the cloud or platform engineer |
| Cloud providers | the person who deploys code |
| Logging and monitoring | the person who fixes production at midnight |

His advice on where to start was deliberately unglamorous: Linux, networking
basics, a programming language such as Python or Go, Git and CI/CD, and
containers. Then understand how applications actually run — build, test, deploy,
logs, configuration, environment variables. Then build small projects: deploy a
simple application, automate something, monitor it, break it, and fix it.

The closing section, *things I learned the hard way*, was the most valuable part
of the whole event:

- Copying commands is not the same as understanding
- Learn to identify the real owner of the problem
- Ask "why" before "how"
- Communication is part of the job
- DevOps is not about being a hero

---

### Key Takeaways

**On architecture.** Every weakness in a simple design has a specific,
named remedy, and the remedies compose. A single point of failure is answered by
multiple Availability Zones; read latency by a cache; write contention by
pre-computation. Recognising which weakness you actually have is the harder
skill.

**On roles.** A job title describes a category, not a set of tasks. Both the
analytics talk and the DevOps talk made the same point from opposite directions:
the work is defined by the domain and the organisation, not by the title on the
offer letter.

**On learning.** Two speakers independently warned against surface knowledge —
"copying commands is not the same as understanding" and the Learner stage's
requirement to ask questions with depth. Both framed genuine understanding as
the thing that separates someone who follows instructions from someone who can
be trusted with a problem.

**On culture.** The no-blame post-mortem is a technical practice as much as a
cultural one. Fixing the system rather than the person is the same instinct as
fixing the architecture rather than retrying the request.

---

### Applying to Work

The URL shortener talk mapped almost directly onto my own project, and changed
how I described it:

- **Single point of failure.** Caerus runs on one EC2 instance in one
  Availability Zone. Hearing the same weakness named and answered gave me the
  vocabulary to state it explicitly in my report as an accepted trade-off, along
  with what a production deployment would add — a load balancer, an auto scaling
  group, and a multi-AZ database.
- **Separation of read and write paths.** In Caerus the seat map is read far
  more often than bookings are written. Recognising that as a read/write
  asymmetry, rather than as one undifferentiated workload, is the first step
  toward caching the seat map without weakening the booking transaction.
- **Pre-computation over on-demand.** The Key Generation Service is the same
  instinct as generating a screening's sixty seats when the event is created
  rather than on first request — do the predictable work early so the
  latency-sensitive path stays short.

From the DevOps talk, "break it, then fix it" is essentially what the
double-booking test does: deliberately create the failure condition and confirm
the system handles it, rather than assuming it does.

From the analytics talk, the progression from Follower to Problem Solver gave me
a more honest frame for my own self-assessment: I can execute a plan reliably,
but proposing the plan is where I still need to grow.

---

### Event Experience

What made the day useful was that none of the four talks was a product pitch.
Each speaker described work they had actually done, including the parts that had
gone badly — a lead developer explaining why the simple version of his system
did not survive contact with traffic, a DevOps engineer admitting he had never
chosen the specialisation at all.

The URL shortener session was the one I took the most notes in, because it was
pitched at exactly the level I could follow while still learning something: every
component was one I had studied in isolation, and seeing them assembled to solve
specific named weaknesses was the first time the pieces connected into a system
rather than a list of services.

The session on multinational corporate culture was the one I expected least from
and got the most unexpected value out of. The recruitment pipeline in particular
was concrete in a way careers advice usually is not — four defined stages, each
testing something different, with the final one testing values rather than skill.

<!-- Add photos to static/images/4-EventParticipated/4.1-Event1/ and reference them here, e.g.

-->
#### Attendance record

No photographs were taken during the session, so I put my attendance as evidenced by the check-in record on the FCAJ portal.

![Check-in record for the FCAJ Saturday Meetup on the First Cloud AI Journey portal](/images/4-EventParticipated/4.1-Event1/checkin.png)

