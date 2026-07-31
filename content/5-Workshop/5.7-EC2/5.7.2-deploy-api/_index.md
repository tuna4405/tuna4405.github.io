---
title : "Deploy the API with pm2"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---

1. **Launch the instance** - Amazon Linux 2023, `t3.micro`, no key pair
   (EC2 Instance Connect only), the `caerus-ec2-s3-role` instance profile
   from section 5.6.3, tagged `Owner`.

2. **Package the backend locally** and stage it through the deploy bucket
   rather than `git clone`-ing directly onto the instance, so the instance
   never needs GitHub credentials of its own:

   ```powershell
   robocopy backend "$env:TEMP\deploy" /E /XD node_modules .git /XF .env
   Compress-Archive -Path "$env:TEMP\deploy\*" -DestinationPath "$env:TEMP\backend.zip" -Force
   ```

   Upload `backend.zip` to `caerus-backend`.

3. **On the instance**, via EC2 Instance Connect:

   ```bash
   sudo dnf install -y nodejs20 postgresql16 unzip
   mkdir -p ~/caerus-api && cd ~/caerus-api
   aws s3 cp s3://caerus-backend/backend.zip .
   unzip backend.zip
   npm ci --omit=dev
   ```

4. **Create `.env`** - the one file that is never in the zip and never in
   version control:

   ```ini
   DATABASE_URL=postgresql://<user>:<password>@<rds-endpoint>:5432/caerus
   JWT_SECRET=<random, e.g. `openssl rand -hex 32`>
   PORT=3000
   CINEMA_TIMEZONE=Asia/Ho_Chi_Minh
   AWS_REGION=ap-southeast-1
   S3_BUCKET_IMAGES=caerus-images-dev
   S3_BUCKET_TICKETS=caerus-tickets-dev
   ```

5. **Run under `pm2`** so the process survives a disconnect and a reboot:

   ```bash
   sudo npm install -g pm2
   pm2 start src/server.js --name caerus-api
   pm2 startup   # run the sudo command it prints
   pm2 save
   ```

6. **Verify**, first locally on the instance, then from outside:

   ```bash
   curl http://localhost:3000/api/v1/health
   # {"ok":true}
   ```

   then, from a browser or `curl` on the developer's own machine, the same
   path against the instance's public DNS name.

<!-- ![pm2 status showing caerus-api online, and a curl response from outside the instance](/images/5-Workshop/5.7-EC2/5.7.2-deploy-api/example.png) -->
