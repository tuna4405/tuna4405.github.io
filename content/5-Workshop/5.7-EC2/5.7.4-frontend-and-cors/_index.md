---
title : "Frontend Build and CORS"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.7.4 </b> "
---

1. **Point the frontend at the deployed API** by setting
   `VITE_API_BASE_URL` in `frontend/.env` to
   `http://<ec2-public-ip>:3000/api/v1`, then build and upload:

   ```bash
   cd frontend
   npm run build
   ```

   Upload the *contents* of `dist/` to `caerus-frontend-web` (section
   5.6.2's warning about not dragging the folder itself applies again here).

2. **Open the site's website endpoint and expect it to fail.** The browser
   console shows something to the effect of:

   > Access to fetch at `http://<ec2-ip>:3000/api/v1/events` from origin
   > `http://caerus-frontend-web.s3-website-ap-southeast-1.amazonaws.com`
   > has been blocked by CORS policy: No 'Access-Control-Allow-Origin'
   > header is present on the requested resource.

   This is expected, not a bug to be surprised by - the site and the API are
   different origins (different host entirely), and the browser enforces
   same-origin policy on the *frontend's* behalf regardless of what the
   backend intends to allow.

3. **Fix it on the backend**, not by fighting the browser: `app.js`
   whitelists the exact origin(s) allowed to call the API.

   ```js
   const allowedOrigins = [
     'http://caerus-frontend-web.s3-website-ap-southeast-1.amazonaws.com',
     'http://localhost:5173',
   ];
   app.use(cors({ origin: allowedOrigins }));
   ```

   {{% notice warning %}}
   Get the **region** in this string exactly right. A whitelist entry with
   the wrong region (`us-east-1` copied from an example rather than the
   project's actual `ap-southeast-1`) looks correct at a glance and fails
   with the identical CORS error, which makes it a genuinely easy mistake to
   ship and a slightly annoying one to spot - check the browser's address
   bar against the string in `app.js` character for character if this
   happens.
   {{% /notice %}}

4. **Redeploy the backend** (repeat the relevant steps of 5.7.2) and reload
   the site - the same request that failed in step 2 now succeeds.

<!-- ![Browser console showing the CORS error, and the same request succeeding after the fix](/images/5-Workshop/5.7-EC2/5.7.4-frontend-and-cors/example.png) -->
