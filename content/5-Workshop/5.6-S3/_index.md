---
title : "Amazon S3"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Overview

Four buckets, each with one job. `caerus-frontend-web` serves the built React
site directly to the public - the only one of the four that is public at all,
and only until section 5.7.6 hands that job to CloudFront instead.
`caerus-images-dev` holds event poster uploads and `caerus-tickets-dev` holds
generated PDF tickets, both private, both read only through short-lived
presigned URLs the API signs on request. `caerus-backend` holds nothing but
the zipped deployment package used to get code onto EC2 in section 5.7 - it
is infrastructure plumbing, not part of the running application, which is why
it will not appear in the architecture diagram's request flow.



#### Content

- [Create the Buckets](5.6.1-create-buckets/)
- [Static Website Hosting](5.6.2-static-hosting/)
- [IAM Role for Image Upload](5.6.3-iam-role/)
- [Verify Poster Upload and Display](5.6.4-verify-upload/)

