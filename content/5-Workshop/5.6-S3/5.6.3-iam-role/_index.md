---
title : "IAM Role for Image Upload"
date : 2026-06-01
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---

1. **IAM Console → Roles → Create role**, trusted entity **AWS service**,
   use case **EC2**. Name it **`caerus-ec2-s3-role`** - the account's
   permission boundary rejects any role name without the `caerus-` prefix,
   so this is not optional stylistically, it is enforced.

2. **Attach an inline policy** scoped to exactly the three buckets the API
   needs, and nothing else:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ReadDeployZip",
         "Effect": "Allow",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::caerus-backend/*"
       },
       {
         "Sid": "ReadWriteImages",
         "Effect": "Allow",
         "Action": ["s3:GetObject", "s3:PutObject"],
         "Resource": "arn:aws:s3:::caerus-images-dev/*"
       },
       {
         "Sid": "ReadWriteTickets",
         "Effect": "Allow",
         "Action": ["s3:GetObject", "s3:PutObject"],
         "Resource": "arn:aws:s3:::caerus-tickets-dev/*"
       }
     ]
   }
   ```

3. **Attach the role to the EC2 instance** as its instance profile when
   launching in section 5.7.2 (or afterwards, via Actions → Security →
   Modify IAM role, on an already-running instance).

**Why an instance role instead of an access key in `.env`.** An access key
pasted into an environment file is a long-lived secret that has to be
generated, distributed to every developer, rotated on a schedule, and kept
out of version control by discipline alone. An instance role has none of
that: the AWS SDK on the instance calls the instance metadata service, gets
short-lived temporary credentials scoped to exactly this role's policy, and
refreshes them automatically before they expire - there is no secret to
leak, because there is no long-lived secret at all. The only reason
`getSignedImageUrl()` (used to hand out a time-limited download link for a
private object) works without the caller ever seeing raw AWS credentials is
that it signs the URL using these same instance-role credentials, on the
instance, and only the resulting signed URL leaves the server.

<!-- ![Inline policy attached to caerus-ec2-s3-role](/images/5-Workshop/5.6-S3/5.6.3-iam-role/example.png) -->
