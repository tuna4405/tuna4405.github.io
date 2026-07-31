---
title : "CloudFront for the Frontend"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---

`caerus-frontend-web` stays private for its entire life. Amazon CloudFront,
set up here, is the only thing that will ever read it - from the very first
deployed build onward. There is no intermediate stage where the site is
served directly from an S3 website endpoint and later locked back down: at
this point the only origin behind the distribution is this bucket; once the
API is deployed, section 5.7.6 adds a second origin and a routing rule to the
*same* distribution, but nothing about the setup below changes when that
happens.

1. **Create an Origin Access Control** (CloudFront Console → Origin access →
   Create control setting), signing behaviour **Sign requests
   (recommended)**, origin type **S3**. This is what lets the bucket stay
   fully private while still being readable by this one distribution.

2. **Create the distribution**, with `caerus-frontend-web`'s **REST
   endpoint** (`caerus-frontend-web.s3.ap-southeast-1.amazonaws.com`) as the
   origin - not a website endpoint, since static website hosting is never
   turned on for this bucket, and Origin Access Control only works against
   the REST endpoint in any case. Attach the OAC from step 1. Set
   **Default root object: `index.html`**.

3. **Attach the bucket policy CloudFront offers to generate**, scoped to the
   CloudFront service principal and this specific distribution's ARN:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Sid": "AllowCloudFrontServicePrincipal",
       "Effect": "Allow",
       "Principal": { "Service": "cloudfront.amazonaws.com" },
       "Action": "s3:GetObject",
       "Resource": "arn:aws:s3:::caerus-frontend-web/*",
       "Condition": {
         "StringEquals": {
           "AWS:SourceArn": "arn:aws:cloudfront::<account-id>:distribution/<distribution-id>"
         }
       }
     }]
   }
   ```

4. **Add two custom error responses**, mapping both `403` and `404` to
   `/index.html` with an HTTP response code of `200`. This is the
   single-page-application fallback: React Router handles a route like
   `/events/3` entirely client-side, so a hard refresh on that URL has to
   still resolve to `index.html` rather than a literal error from S3, which
   has no idea the route exists.

5. **Build and upload the site**:

   ```bash
   cd frontend && npm run build
   ```


6. **Wait for the distribution to deploy**, then open its domain
   (`dxxxxxxxxxxxxx.cloudfront.net`) and confirm the application loads. API
   calls will not work yet - there is no backend deployed at this point in
   the workshop - but the static application itself should render.



![CloudFront distribution with the S3 origin, OAC attached, and the site loading at the distribution domain](/images/5-Workshop/5.6-S3/caerus_cloudfront.png)
