---
title : "Verify Poster Upload and Display"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.6.4 </b> "
---

Because `caerus-images-dev` is private (section 5.6.1), the poster flow is
not "upload a file, store its URL, done" - it is upload the file, store the
object's **key**, and sign a fresh, time-limited URL every time the poster is
actually rendered.

1. **Log in as an admin** and open **Create screening**, filling in the
   screening details and attaching a portrait 2:3 poster image
   (`image/jpeg` or `image/png`, up to 5 MB, enforced by `multer` on the way
   in).

2. **Trace the request**: `POST /events/:id/banner` receives the image as
   multipart form data, uploads the buffer to `caerus-images-dev` under a key
   like `events/{id}/banner.jpg`, and stores that **key** - not a URL - in
   the event's `banner_url` column.

3. **Confirm the object landed in S3**: open `caerus-images-dev` in the
   Console and find the uploaded object at the expected key.

4. **Reload the event list or event detail page** and confirm the poster
   renders. What actually happens on every `GET /events` request is that the
   API takes the stored key and calls `getSignedImageUrl()`, producing a
   presigned URL valid for one hour before handing the response to the
   browser - the bucket itself never becomes public, and the URL a browser
   ever sees is single-use in the sense that it stops working after expiry.

{{% notice note %}}
Open the browser's network tab and inspect the `bannerUrl` field in the
`GET /events` response: it is a full `https://caerus-images-dev.s3...`
URL carrying `X-Amz-Signature`, `X-Amz-Expires`, and related query
parameters - visible proof that the URL is signed and temporary, not a
plain public link.
{{% /notice %}}

<!-- ![Event card rendering the uploaded poster, and the signed URL in the network tab](/images/5-Workshop/5.6-S3/5.6.4-verify-upload/example.png) -->
