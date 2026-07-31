---
title : "CloudFront: One HTTPS Domain for Everything"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.7.6 </b> "
---

At this point the site is served over plain HTTP from an S3 website endpoint,
and the API is served over plain HTTP from the load balancer, on two
different domains. This section puts a single Amazon CloudFront distribution
in front of both, so the whole application is reachable over HTTPS on one
domain - and does it using CloudFront's path-based routing rather than two
separate distributions.

#### The idea in one sentence

One CloudFront distribution can hold more than one origin. A **behavior**
maps a URL path pattern to a specific origin: requests to `/api/*` go to the
load balancer, and every other request (the default behavior) goes to the S3
site bucket. Both are served from the *same* distribution domain
(`dxxxxxxxxxxxxx.cloudfront.net`) - CloudFront does not need, and does not
use, a different domain per origin.

1. **Create an Origin Access Control** (CloudFront Console → Origin access →
   Create control setting), signing behaviour **Sign requests
   (recommended)**, origin type **S3**. This is what will let the S3 origin
   go fully private again.

2. **Create the distribution** with the S3 bucket as the first (default)
   origin, selecting the bucket's **REST endpoint**
   (`caerus-frontend-web.s3.ap-southeast-1.amazonaws.com`), *not* the website
   endpoint the console suggests for a bucket with static hosting enabled -
   Origin Access Control only works against the REST endpoint. Attach the
   OAC from step 1. Set **Default root object: `index.html`**.

3. **Replace the bucket's public-read policy** with the one CloudFront
   offers to generate, scoped to the CloudFront service principal and this
   specific distribution's ARN:

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

   Then **disable Static website hosting** on the bucket entirely and
   **re-enable Block all public access** - the bucket is private again, and
   the only thing that can read it is this one distribution.

4. **Add a second origin** pointing at the load balancer's DNS name,
   protocol **HTTP only** (the load balancer itself has no HTTPS listener -
   CloudFront terminates TLS at the edge and talks plain HTTP to the origin,
   which is the standard shape of this setup, not a shortcut).

5. **Add a behavior for `/api/*`** targeting the load balancer origin:
   viewer protocol policy **Redirect HTTP to HTTPS**, allowed methods **GET,
   HEAD, OPTIONS, PUT, POST, PATCH, DELETE** (the API uses more than just
   GET), cache policy **CachingDisabled** (API responses must never be
   cached), origin request policy **AllViewerExceptHostHeader** (forwards the
   `Authorization` header carrying the JWT through to the load balancer
   unchanged).

6. **Add two custom error responses** on the distribution, mapping both
   `403` and `404` to `/index.html` with an HTTP response code of `200` -
   this replaces the SPA-fallback job that S3's own error-document setting
   used to do, now that static website hosting is off.

7. **Wait for the distribution to deploy**, then point the frontend at it:

   ```ini
   VITE_API_BASE_URL=https://<distribution-domain>/api/v1
   ```

   Rebuild and re-upload `dist/`.

#### Two things that went wrong here, worth knowing in advance

**CloudFront's bundled WAF protections need IAM permissions the account
might not have granted yet.** Enabling the Free-tier security protections
during distribution creation calls `wafv2:CreateWebACL` on the caller's
behalf, against a WAF resource in **`us-east-1`** specifically - WAF for
CloudFront always lives there regardless of which region the rest of the
architecture uses. If the IAM user lacks this permission, distribution
creation fails with an `AccessDenied` naming the exact action and resource.
The fix is an inline policy granting `wafv2:CreateWebACL` and its usual
companions (`UpdateWebACL`, `DeleteWebACL`, `GetWebACL`, `TagResource`) - and,
less obviously, a *second* statement for resource type `managedruleset`,
since CloudFront's default protections reference AWS Managed Rule Groups and
WAF checks permission on both the Web ACL being created and every managed
rule group it references.

**A leftover `:3000` in the frontend's base URL fails in a way that looks
like a CORS or connectivity problem but is neither.** CloudFront only
accepts standard viewer-facing ports; a base URL copied forward from the
single-EC2 era (`https://<distribution-domain>:3000/api/v1`) simply cannot
connect - not a permissions error, not a CORS error, just a connection that
never completes. The browser's network tab shows a request with no response
headers at all, which is the tell: compare that against a genuine CORS
failure, which does complete with a real (rejected) response. The fix is
removing the port entirely - CloudFront's internal hops to the load balancer
and then to the instance's port 3000 are invisible to the client by design.

**The CloudFront-managed WAF Web ACL silently blocked poster uploads, and the
custom error response from step 6 hid the block completely.** Enabling
CloudFront's bundled protections creates a Web ACL running AWS Managed Rule
Groups, including `AWSManagedRulesCommonRuleSet`'s `SizeRestrictions_BODY`
rule, which blocks any request body over roughly 8 KB by default - fine for
JSON payloads, but a poster image upload is routinely far larger. WAF
returned `403` for the oversized `POST /api/v1/events/:id/banner` request,
and the distribution's own custom error response (step 6, `403` →
`/index.html` with a `200` override, meant only for S3-origin SPA routing)
intercepted that 403 and served the frontend's `index.html` back with a fake
`200` status - so the browser saw an apparently successful response with no
S3 object ever created and no error ever reaching the Express error handler.
The tell was in the response headers, not the status code: `Server:
AmazonS3` and `Content-Type: text/html` on what should have been a JSON
response from the API. The fix is a targeted rule override, not disabling
WAF: open the Web ACL → the `AWSManagedRulesCommonRuleSet` rule group →
override `SizeRestrictions_BODY`'s action to **Count** instead of the
inherited **Block**, leaving every other rule in the group (SQLi, XSS,
known-bad-input patterns) still enforced.

{{% notice note %}}
This is also a reminder that CloudFront's custom error responses apply to
the *entire distribution*, not just the origin they were written for - a
403/404 from the API origin gets the same SPA-fallback treatment as a 403/404
from the S3 origin. There is no per-behavior override for this in the
standard console flow; it is a real limitation worth designing around rather
than a misconfiguration to fix.
{{% /notice %}}

{{% notice note %}}
After this section, `caerus-frontend-web`'s old website endpoint and a
direct call to `<alb-dns-name>` both still exist as URLs, but neither is the
one anything should use going forward - the distribution domain is now the
single front door for the whole application, HTTPS included.
{{% /notice %}}

<!-- ![CloudFront distribution behaviors: /api/* to the ALB origin, Default(*) to the S3 origin](/images/5-Workshop/5.7-EC2/5.7.6-cloudfront/example.png) -->
