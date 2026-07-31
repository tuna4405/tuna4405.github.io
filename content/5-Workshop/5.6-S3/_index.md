---
title : "Amazon S3"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Overview

Four buckets, each with one job, and all four fully private from the moment
they are created. `caerus-frontend-web` serves the built React site, read
only by the CloudFront distribution set up in this section - there is no
stage where it is served directly from an S3 website endpoint first.
`caerus-images-dev` holds event poster uploads and `caerus-tickets-dev` holds
generated PDF tickets, both read only through short-lived presigned URLs the
API signs on request. `caerus-backend` holds nothing but the zipped
deployment package used to get code onto EC2 in section 5.7 - it is
infrastructure plumbing, not part of the running application, which is why it
will not appear in the architecture diagram's request flow. None of these
buckets do anything observable yet - there is no running API able to write to
`caerus-images-dev` or read from `caerus-backend` until section 5.7 exists;
the first real object actually landing in any of them, and the poster upload
flow proven end to end, is checked in section 5.7.7.

#### Content

- [Create the Buckets](5.6.1-create-buckets/)
- [CloudFront for the Frontend](5.6.2-cloudfront-for-frontend/)
- [IAM Role for Image Upload](5.6.3-iam-role/)

