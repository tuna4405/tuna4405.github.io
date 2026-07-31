---
title : "Static Website Hosting"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---

1. **`caerus-frontend-web` → Properties → Static website hosting → Enable**,
   with **Index document: `index.html`** and **Error document: `index.html`**
   - the same file for both, because this is a single-page application:
   React Router handles `/events/3` client-side, so a hard refresh on that
   URL must still resolve to `index.html` rather than a literal 404 from S3.

2. **Attach a public-read bucket policy:**

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Sid": "PublicReadGetObject",
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:GetObject",
       "Resource": "arn:aws:s3:::caerus-frontend-web/*"
     }]
   }
   ```

3. **Build and upload the site**:

   ```bash
   cd frontend && npm run build
   ```

   Then upload the *contents* of `frontend/dist/` to the bucket root - not
   the `dist` folder itself. Dragging the folder in produces
   `caerus-frontend-web/dist/index.html` instead of
   `caerus-frontend-web/index.html`, and the website endpoint serves a blank
   listing instead of the application.

4. **Open the website endpoint** (Properties tab, bottom of the Static
   website hosting panel) and confirm the application loads.

<!-- ![The built React site served from the S3 website endpoint](/images/5-Workshop/5.6-S3/5.6.2-static-hosting/example.png) -->
