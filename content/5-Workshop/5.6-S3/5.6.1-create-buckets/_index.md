---
title : "Create the Buckets"
date : 2026-06-01
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---

Four buckets, created in `ap-southeast-1`, and all four kept fully private -
**Block all public access ON** for every one, no exceptions:

| Bucket | Purpose |
|---|---|
| `caerus-frontend-web` | Serves the built React site, exclusively through CloudFront (section 5.6.2) |
| `caerus-images-dev` | Event poster uploads |
| `caerus-tickets-dev` | Generated PDF tickets |
| `caerus-backend` | Deployment zip staging only |

1. **Create all four** with **S3 Console → Create bucket**, same region as
   everything else, **Block all public access** left checked (the default)
   on every one.

2. **None of the four is ever made public, including the frontend bucket.**
   `caerus-frontend-web` is read only by Amazon CloudFront, through Origin
   Access Control set up in the next section - there is no intermediate
   stage where the site is served directly from an S3 website endpoint. The
   other three are reached only through an IAM role (section 5.6.3): there
   is no reason for anyone outside the application to read a poster or a
   ticket directly by URL, which is exactly why generated tickets and
   uploaded posters go through presigned URLs rather than a public bucket,
   even though both are technically "images anyone with the link could
   view".

![Four buckets listed in the S3 console, all with Block all public access ON](/images/5-Workshop/5.6-S3/bucket_list.png)
