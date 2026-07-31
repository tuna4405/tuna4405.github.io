---
title : "Ship Application Logs"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---

By default, the Express API's `pm2` logs exist only on whichever EC2
instance produced them - fine for `pm2 logs caerus-api` over Instance
Connect, useless the moment there are two instances and a failure could be on
either one. The CloudWatch agent ships both instances' logs into one shared
log group.

1. **Install and configure the agent** on each instance:

   ```bash
   sudo dnf install -y amazon-cloudwatch-agent
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
   ```

   Point it at `~/.pm2/logs/caerus-api-out.log` and
   `~/.pm2/logs/caerus-api-error.log`, log group `/caerus/ec2/api`, then:

   ```bash
   sudo systemctl enable amazon-cloudwatch-agent
   sudo systemctl start amazon-cloudwatch-agent
   ```

2. **Grant the instance role the permissions this needs** -
   `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` - added
   as a statement on `caerus-ec2-s3-role` alongside the S3 permissions from
   section 5.6.3.

3. **Set a retention period on the log group** - 7 days. CloudWatch Logs
   defaults to "Never expire", which quietly accumulates storage cost
   forever; a short, explicit retention is a five-second fix worth doing on
   every log group created in this project.

4. **Run one Logs Insights query that answers a real question** - not just
   "do logs appear here", but something a real operator would actually ask,
   such as how often the booking-conflict path is hit:

   ```
   fields @timestamp, @message
   | filter @message like /SEAT_ALREADY_BOOKED/
   | stats count() by bin(1h)
   ```

   This is also a natural rehearsal for the alarm built next in section
   5.8.3, which watches a related signal automatically instead of requiring
   someone to run this query by hand.

<!-- ![Logs Insights query result across both instances' streams](/images/5-Workshop/5.8-CloudWatch/5.8.2-application-logs/example.png) -->
