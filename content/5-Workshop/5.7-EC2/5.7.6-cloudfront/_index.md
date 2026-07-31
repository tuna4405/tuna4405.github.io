---
title : "CloudFront: Adding the API to the Same Domain"
date : 2026-06-01
weight : 6
chapter : false
pre : " <b> 5.7.6 </b> "
---

The frontend has been served through the CloudFront distribution created in
section 5.6.2 since before EC2 even existed. This section adds the second
half: routing `/api/*` on that *same* distribution to the load balancer, so
the whole application - site and API - is reachable over HTTPS on one domain.
Nothing about the existing S3 origin, the Origin Access Control, or the SPA
error-response behaviour changes; a **behavior** simply maps a URL path
pattern to a specific origin, and a distribution can hold more than one.

1. **Add a second origin** pointing at the load balancer's DNS name, protocol
   **HTTP only** (the load balancer itself has no HTTPS listener - CloudFront
   terminates TLS at the edge and talks plain HTTP to the origin, which is
   the standard shape of this setup, not a shortcut).

2. **Add a behavior for `/api/*`** targeting the load balancer origin:
   viewer protocol policy **Redirect HTTP to HTTPS**, allowed methods **GET,
   HEAD, OPTIONS, PUT, POST, PATCH, DELETE** (the API uses more than just
   GET), cache policy **CachingDisabled** (API responses must never be
   cached), origin request policy **AllViewerExceptHostHeader** (forwards the
   `Authorization` header carrying the JWT through to the load balancer
   unchanged).

3. **Wait for the distribution to redeploy**, then point the frontend at it
   for API calls too:

   ```ini
   VITE_API_BASE_URL=https://<distribution-domain>/api/v1
   ```

   Rebuild, re-upload `dist/`, and invalidate the CloudFront cache (as every
   frontend redeploy since section 5.6.2 has required).

#### Two things that went wrong here, worth knowing in advance

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

**The CloudFront-managed WAF Web ACL (from section 5.6.2) silently blocked
poster uploads once real API traffic started flowing through it, and the
existing SPA custom error response hid the block completely.** The Web ACL
runs AWS Managed Rule Groups, including `AWSManagedRulesCommonRuleSet`'s
`SizeRestrictions_BODY` rule, which blocks any request body over roughly
8 KB by default - fine for JSON payloads, but a poster image upload is
routinely far larger. WAF returned `403` for the oversized
`POST /api/v1/events/:id/banner` request, and the custom error response set
up in section 5.6.2 (`403` → `/index.html` with a `200` override, meant only
for S3-origin SPA routing) intercepted that `403` and served the frontend's
`index.html` back with a fake `200` status - so the browser saw an
apparently successful response with no S3 object ever created and no error
ever reaching the Express error handler. The tell was in the response
headers, not the status code: `Server: AmazonS3` and `Content-Type:
text/html` on what should have been a JSON response from the API. The fix is
a targeted rule override, not disabling WAF: open the Web ACL → the
`AWSManagedRulesCommonRuleSet` rule group → override `SizeRestrictions_BODY`'s
action to **Count** instead of the inherited **Block**, leaving every other
rule in the group (SQLi, XSS, known-bad-input patterns) still enforced.

{{% notice note %}}
This is also a reminder that CloudFront's custom error responses apply to
the *entire distribution*, not just the origin they were written for - a
403/404 from the API origin gets the same SPA-fallback treatment as a 403/404
from the S3 origin. There is no per-behavior override for this in the
standard console flow; it is a real limitation worth designing around rather
than a misconfiguration to fix.
{{% /notice %}}

{{% notice note %}}
After this section, a direct call to `<alb-dns-name>` still exists as a URL,
but it is not the one anything should use going forward - the distribution
domain is now the single front door for the whole application, HTTPS
included.
{{% /notice %}}

<!-- ![CloudFront distribution behaviors: /api/* to the ALB origin, Default(*) to the S3 origin](/images/5-Workshop/5.7-EC2/5.7.6-cloudfront/example.png) -->
