---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives: Caerus - Verification, Report, and Submission

* Demonstrate the no-double-booking guarantee on the deployed system instead of merely asserting it.
* Check the documented edge cases against the real environment.
* Put the final report together and submit it.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - The concurrency test: two browser sessions signed in as two different users, both picking the same seat on the same screening and submitting as near to simultaneously as we could manage <br> - Confirmed that one request came back `201 Created` and the other a `409 SEAT_ALREADY_BOOKED` naming the contested seat, and that the seat map afterwards showed that seat booked exactly once | 27/07/2026 | 27/07/2026 | |
| 3 | - Edge-case testing on the deployed system: cancelling after showtime, cancelling a booking already cancelled, booking more than six seats, a token expired or malformed, a non-admin calling an admin route, and downloading a ticket for a cancelled booking <br> - Wrote down the status code and error code actually observed on each row against what the specification says, rather than leaving the table as a checklist | 28/07/2026 | 28/07/2026 | |
| 4 | - Put the report structure together and started writing, working from the frozen specification, the worklog, and the architecture as it actually ended up built <br> - Went back through every AWS console the project had touched and captured the screenshots the report refers to | 29/07/2026 | 29/07/2026 | |
| 5 | - **Published Blog 1** (AWS Budgets and Cost Anomaly Detection, out of Week 2's self-study) and **Blog 3** (AWS Config and Conformance Packs, out of Week 3's self-study) to the AWS Study Group community <br> - Finished writing the report content, the cost section included, which reflects the architecture's real monthly run-rate rather than a Free Tier estimate that no longer held | 30/07/2026 | 30/07/2026 | <https://www.facebook.com/groups/awsstudygroupfcj/permalink/2229088634522763/> |
| 6 | - Final review of the report and the deployed system side by side, correcting the handful of places where the two had drifted apart over the week's changes <br> - Recorded a demonstration video as a fallback should anything fail live during the presentation <br> - **Milestone reached:** report submitted | 31/07/2026 | 31/07/2026 | |

### Week 7 Achievements:

* Demonstrated the project's central claim - that a seat can never be sold twice - on the real deployed system, with the successful request and the rejected one both captured as evidence.
* Checked every documented edge case against the deployed environment instead of taking the specification on trust.
* Published the internship's remaining two blog posts, closing out the reading that had started as self-study back in Weeks 2 and 3.
* Submitted a report whose architecture, cost, and testing sections all describe the system as it was genuinely built and run, not as it had been planned seven weeks earlier.
