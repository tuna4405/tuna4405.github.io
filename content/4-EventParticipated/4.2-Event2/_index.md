---
title: "Event 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: FCAJ Agentic AI Build Week — Community Day

### Event Objectives

- Present the systems built by teams during the Agentic AI Build Week hackathon
- Show how agentic AI patterns are assembled from AWS services in practice
- Share the experience of building an end-to-end prototype under a 24-hour
  deadline, including what went wrong
- Give first-time participants a realistic account of what a hackathon involves

### Presenting Teams

- **Plan V** — *Solution Architect Professional Native App*
  (Pham Tien Thuan Phat, Huynh Hoang Long, Le Minh Nghia, Tran Dai Vi, Nguyen An)
- **Signal Scout** — *Corporate signal detection platform*
  (Le Tan Luc, Đo Hoang Hieu, Trieu Quoc Hao, Nguyen Duy Khiem, Nguyen Cong Minh, Nguyen Tran Minh Quan)
- **One Team** — *KFC Bot Agent*, winner of the AABW Hackathon
  (Anh Duy, Tran Dong, Doan Trung, Minh Viet, Anshul Roy)
- **3KA** — *S.H.E.P.H.E.R.D* and the hackathon journey
  (Huynh An Khuong, Nguyen Quoc Huy, Ngo Quang Khoi, Hoang Le Thanh Duc, Dang Nguyen Phuoc Loc, Dang Truong Hung)

---

### Key Highlights

#### Plan V — Solution Architect Professional Native App

The problem was framed as a conversation anyone in consulting would recognise: a
customer asks for an AI system design for their standard operating procedure
documents, wants it by Thursday, and then wants it immediately. Meanwhile the
solution architect has to extract requirements, draft an initial architecture,
produce diagrams, and estimate cloud cost.

Their tool addresses each of those steps. It analyses requirements from natural
language and structured documents, drafts hybrid-cloud-aware architecture
options aligned to company standards, generates editable diagrams using the
official AWS architecture icons, produces directional cost estimates for the
`ap-southeast-1` region, and surfaces its own assumptions and the gaps it found
in the requirements. Refinement happens through a chat sidebar with per-project
custom instructions.

The before-and-after comparison was the clearest part of the presentation:

| Before | After |
|---|---|
| Read the requirements document line by line, manually | Upload and chat naturally — a requirements catalogue in minutes |
| Start from a blank page every time | A grounded first draft to react to |
| Write infrastructure as code manually | Infrastructure as code generated |
| Cost estimation by experience-dependent guesswork | A directional estimate produced alongside the architecture |

What I found notable was the framing of the output as *a first draft to react
to* rather than a finished artefact. The tool is positioned as removing the
blank page, not removing the architect.

#### Signal Scout — early detection of corporate strategic change

This team built a platform that detects restructuring and strategic-change
signals in companies early, aimed at corporate strategy teams, enterprise risk
management, competitive intelligence, and B2B account management.

The system is genuinely multi-agent. A Lambda function fronts an AgentCore
runtime that orchestrates two subagents: a **crawler subagent** that gathers
evidence from external sources, and an **analysis subagent** that interprets it,
with Bedrock Guardrails applied. Short-term memory is held in AgentCore Memory,
sessions in S3, and results in DynamoDB. The user-facing side runs through Route
53, Amplify, API Gateway, WAF, and Cognito, with CloudWatch and CloudTrail for
observability and Secrets Manager and IAM for credentials and access.

Their stated value propositions were disciplined: transparent and verifiable
analysis, every conclusion supported by evidence, and explicitly
*human-controlled* decision support. The system is designed to inform a
Maintain, Adapt, or Accelerate decision, not to make it.

The part I appreciated most was the **cost slide** — a full line-item breakdown
across minimum, mid, and maximum usage, covering every service including the
non-AWS dependencies:

| | Min | Mid | Max |
|---|---|---|---|
| AWS services total | ≈ $17 | ≈ $35 | ≈ $130 |
| Third-party crawling | ~$35 | ~$30 | ~$200 |
| Observability tooling | $0–29 | $29 | $29 |
| **Total** | **≈ $81** | **≈ $94** | **≈ $359** |

They then presented a revised, more cost-efficient architecture — showing the
cost analysis had actually fed back into the design rather than being produced
afterwards to satisfy a slide requirement.

#### One Team — KFC Bot Agent (hackathon winner)

The winning team opened with a real industry failure rather than their own idea:
McDonald's ended an AI drive-thru trial after testing automated ordering in more
than a hundred US locations. Their reading of it was precise — the lesson was
not that AI ordering is a bad idea, but that **ordering is a systems problem**.
An ordering agent has to handle items, quantities, variants, voucher rules, cart
state, and errors, where natural language is messy, business rules are strict,
orders must be verified, and mistakes turn directly into money.

The problem they targeted was the moment a brand loses an order: the customer is
hungry and the intent appears mid-conversation, but ordering forces them to
switch app, create an account, and navigate a menu — and the momentum
disappears. Human-only chat support does not scale across channels, shifts, and
traffic spikes.

Their product is a multi-channel conversational ordering agent running inside
Zalo and Messenger, with no app switching, no new account flow, and no repeated
explanation.

The architectural point they made is the one I think about most:

> **A chatbot replies. An agent acts.**

They described a five-step loop — understand the ordering intent, plan the
required steps, call tools to search trusted business data, act by updating the
cart and applying promotions, then verify against the real cart state. Their
summary of it: *the model understands, the tools decide what is real.* The
language model is not trusted with the state of the order; it is trusted only to
interpret the request.

#### 3KA — S.H.E.P.H.E.R.D and the hackathon journey

This presentation was structured as a story rather than a product demonstration,
covering four stages: signing up and choosing a track, building under pressure,
demo day and judging, and reflections.

The system, S.H.E.P.H.E.R.D — Smart Human-flow Evaluation, Prediction, Hazard
Detection, Response, and Dispatch — analyses live camera footage at venues to
detect and track people, measure crowd density, estimate queue conditions,
identify early signs of congestion, predict overcrowding, raise proactive
alerts, and recommend staff actions. It was built with YOLO and ByteTrack for
detection and tracking, Amazon SageMaker, Amazon Bedrock AgentCore with Strands
Agent for the agentic layer, and a React dashboard.

The agentic layer had two distinct roles: an **autonomous monitor** watching
live metrics and raising alerts without being asked, and an **operator copilot**
letting staff ask natural-language questions and receive answers backed by live
metrics and prediction tools. The problem framing was concrete — venue staff
must watch entrances, queues, booths, and movement simultaneously, and manual
monitoring is slow, reactive, hard to scale, and prone to missed incidents.

They were unusually honest about the experience. Their fears before day one were
listed plainly: not skilled enough, fear of failing, clueless, too little time.
Their biggest challenges were no AI background, first time working with AWS,
limited time, code that did not work, and sleep deprivation. The emotional arc
they described ran from overwhelmed, through finding flow once the idea clicked,
to pride at having actually built something.

Their advice to first-time participants:

- **Just sign up** — don't wait until you feel ready
- **Find a team early** — different skills beat identical ones
- **Scope it tiny** — one feature, done well
- **Talk to everyone** — mentors and other teams are why you're there

---

### Key Takeaways

**Agents are defined by their tools, not their model.** Every team drew the same
boundary: the language model interprets intent, and tools perform and verify
actions against real state. One Team's formulation — the model understands, the
tools decide what is real — is the clearest statement of it I have heard, and it
is fundamentally a correctness argument rather than an AI one.

**Cost belongs in the design, not after it.** Signal Scout produced a
line-item estimate across three usage levels *and then redesigned* for
efficiency. That is a discipline I had thought of as an operations concern
rather than a design input.

**A grounded draft beats a blank page.** Plan V's positioning of their tool as
producing something to react to rather than something to accept is a more honest
account of what these systems are good for than most product claims.

**Constraints produce better scope than ambition does.** "Scope it tiny — one
feature, done well" came from a team that had 24 hours; it applies just as well
to a project with four weeks.

---

### Applying to Work

The most transferable idea was the tool-verification boundary. In my own project
the equivalent is that the application layer proposes a booking, but the database
transaction decides whether it is real — the seat rows are locked and re-checked
before anything is committed, and no amount of confidence at the application
layer can override the state in the database. Hearing four teams arrive at the
same principle in a different domain made me more confident that placing the
guarantee in the data layer was the right choice rather than an awkward one.

Signal Scout's cost table changed how I approached the cost section of my own
report. Rather than asserting the project sits inside the Free Tier, I have
structured it as line items with the components that would dominate the bill
identified explicitly — which is also what makes the estimate useful to anyone
reading it.

The `ap-southeast-1` region appearing in Plan V's cost estimation was a small
but reassuring detail: the same region choice I had made for latency reasons,
made independently by a team optimising for cost.

Finally, 3KA's list of fears — not skilled enough, clueless, too little time —
was almost exactly what I felt at the start of the programme. Seeing a team say
it out loud on stage, having then built and demonstrated a working system, was
probably the most useful thing I took from the day.

<!-- Add photos to static/images/4-EventParticipated/4.2-Event2/ and reference them here, e.g.

-->
#### Event photo
![Me at the Agentic AI Build Week Community Day](/images/4-EventParticipated/4.2-Event2/selfie.jpg)

