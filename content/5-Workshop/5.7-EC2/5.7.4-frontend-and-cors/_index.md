---
title : "Frontend Build and CORS"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.7.4 </b> "
---

`caerus-server-1` has no public IP, so nothing on the internet - including
the frontend already live on CloudFront since section 5.6.2 - can reach it
yet. The load balancer that finally opens a real path in does not exist until
section 5.7.5, but the CORS problem it will eventually surface is cheaper to
find and fix now, locally, than to discover for the first time against
production traffic.

1. **Open the SSM port-forwarding tunnel from section 5.7.2** in one
   terminal, keep it running, and point a *local* frontend dev server at the
   tunnel instead of building for CloudFront:

   ```ini
   # frontend/.env
   VITE_API_BASE_URL=http://localhost:3000/api/v1
   ```

   ```bash
   cd frontend
   npm run dev
   ```

2. **Open the dev server in a browser (`http://localhost:5173`) and expect
   the API calls to fail anyway**, tunnel notwithstanding. The browser
   console shows something to the effect of:

   > Access to fetch at `http://localhost:3000/api/v1/events` from origin
   > `http://localhost:5173` has been blocked by CORS policy: No
   > 'Access-Control-Allow-Origin' header is present on the requested
   > resource.

   This is expected, not a bug to be surprised by - the dev server and the
   tunneled API are still different origins (different port counts as
   different origin), and the browser enforces same-origin policy on the
   *frontend's* behalf regardless of what the backend intends to allow, and
   regardless of the SSM tunnel underneath being nothing more than a plumbing
   detail the browser never sees.

3. **Fix it on the backend**, not by fighting the browser: `app.js`
   whitelists the exact origin(s) allowed to call the API.

   ```js
   const allowedOrigins = [
     'https://<distribution-domain>',
     'http://localhost:5173',
   ];
   app.use(cors({ origin: allowedOrigins }));
   ```

   The CloudFront distribution domain goes in this list now even though
   nothing can reach it end-to-end until section 5.7.5 - the origin the
   browser will eventually send never changes, only the backend's own address
   does, so there is nothing to redo here later.

   {{% notice warning %}}
   Get the **origin** in this list exactly right - scheme, host, and no
   trailing slash. An entry that is close but not identical (wrong scheme, a
   stale domain copied from an earlier stage) looks correct at a glance and
   fails with the identical CORS error, which makes it a genuinely easy
   mistake to ship and a slightly annoying one to spot - check the browser's
   address bar against the string in `app.js` character for character if this
   happens.
   {{% /notice %}}

4. **Redeploy the backend** (repeat the relevant steps of 5.7.2, over the same
   SSM session) and reload `http://localhost:5173` - the same request that
   failed in step 2 now succeeds, tunnel and all.

<!-- ![Browser console showing the CORS error against the SSM-tunneled backend, and the same request succeeding after the fix](/images/5-Workshop/5.7-EC2/5.7.4-frontend-and-cors/example.png) -->
