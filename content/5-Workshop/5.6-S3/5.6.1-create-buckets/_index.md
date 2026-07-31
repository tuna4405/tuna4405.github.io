---
title : "Create the Buckets"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---

Four buckets, created in `ap-southeast-1`, each with a public-access decision
made deliberately rather than left at the default:

| Bucket | Public access | Purpose |
|---|---|---|
| `caerus-frontend-web` | Public (for now) | Serves the built React site |
| `caerus-images-dev` | Private | Event poster uploads |
| `caerus-tickets-dev` | Private | Generated PDF tickets |
| `caerus-backend` | Private | Deployment zip staging only |

1. **Create all four** with **S3 Console → Create bucket**, same region as
   everything else.

2. **`caerus-frontend-web`** is the only one that needs public reads at this
   stage - static website hosting (section 5.6.2) requires it. Uncheck
   **Block all public access** on this bucket only.

3. **The other three keep Block all public access ON** (the default). The
   API reaches them through an IAM role (section 5.6.3), never through a
   public bucket policy - there is no reason for anyone outside the
   application to read a poster or a ticket directly by URL, which is
   exactly why generated tickets and uploaded posters go through presigned
   URLs rather than a public bucket, even though both are technically
   "images anyone with the link could view".

{{% notice note %}}
The public access on `caerus-frontend-web` is temporary, not a permanent
design decision - section 5.7.6 replaces it with a CloudFront distribution
using Origin Access Control, at which point public access is switched back
off and the bucket becomes reachable only through CloudFront.
{{% /notice %}}

<!-- ![Four buckets listed in the S3 console](/images/5-Workshop/5.6-S3/5.6.1-create-buckets/example.png) -->
