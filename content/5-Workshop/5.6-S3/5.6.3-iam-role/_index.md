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


