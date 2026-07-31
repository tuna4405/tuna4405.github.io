---
title : "Private Networking and the First Instance"
date : 2026-06-01
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---

#### Why a separate subnet pair, not the database's

RDS's private subnets (section 5.5.1) sit behind a route table with **no**
`0.0.0.0/0` route at all - correct for RDS, which never initiates outbound
traffic, but wrong for EC2, which needs outbound access for `npm ci` and OS
patching. Reusing the database's subnets would mean adding an internet route
to a route table that was deliberately built without one, muddying a design
decision that is correct as written. A second, dedicated private subnet
pair - one per Availability Zone - keeps the two tiers' network policies
independent even though both end up "private" in the everyday sense of the
word.

1. **Create two private subnets for the application tier**,
   `caerus-app-private-1a` (`ap-southeast-1a`) and `caerus-app-private-1b`
   (`ap-southeast-1b`), CIDR blocks that don't overlap any existing subnet,
   including RDS's `caerus-private-1a`/`1b`.

2. **Create a NAT gateway per Availability Zone**, `caerus-nat-1a` and
   `caerus-nat-1b`, each in the **public** subnet of its own AZ (a NAT
   gateway must sit in a subnet with a route to an Internet Gateway, never in
   a private one), each with its own newly allocated Elastic IP.

   {{% notice note %}}
   One NAT gateway per AZ, not a single shared one, is a deliberate choice to
   keep the compute tier's availability story consistent with the database's
   Multi-AZ design (section 5.5.1): if the AZ holding `caerus-nat-1a` has an
   outage, `caerus-app-private-1b` keeps its own independent outbound path
   through `caerus-nat-1b`, rather than losing egress along with it. The
   trade-off is roughly double the hourly NAT charge of a single shared
   gateway - accepted here because the RDS side of this architecture already
   pays the equivalent premium for Multi-AZ, and a compute tier that loses
   outbound access the moment one AZ hiccups would undercut that same
   Multi-AZ guarantee.
   {{% /notice %}}

3. **Create one route table per AZ**, `caerus-app-private-rt-1a` (route
   `0.0.0.0/0` → `caerus-nat-1a`) and `caerus-app-private-rt-1b` (route
   `0.0.0.0/0` → `caerus-nat-1b`) - the opposite of RDS's route table, and the
   entire reason the two tiers use separate subnets. Associate each with its
   matching subnet from step 1.

4. **Grant Systems Manager permission** on the EC2 instance role
   (`caerus-ec2-s3-role`, section 5.6.3): attach the AWS-managed policy
   `AmazonSSMManagedInstanceCore`. This is what lets the SSM Agent already
   running on Amazon Linux register itself and accept commands - entirely
   over an outbound connection the NAT gateways now provide, with no inbound
   rule needed at all. No key pair, no SSH, at any point in this project.

#### Launching the first instance

5. **Launch `caerus-server-1`** - Amazon Linux 2023, `t3.micro`, **no key
   pair**, subnet `caerus-app-private-1a`, auto-assign public IP **disabled**,
   the `caerus-ec2-s3-role` instance profile, tagged `Owner`. It has no public
   IP by design - the load balancer in section 5.7.5 will be the only thing
   that ever calls it directly.

6. **Package the backend locally** and stage it through the deploy bucket
   rather than `git clone`-ing directly onto the instance, so the instance
   never needs GitHub credentials of its own:

   ```powershell
   robocopy backend "$env:TEMP\deploy" /E /XD node_modules .git /XF .env
   Compress-Archive -Path "$env:TEMP\deploy\*" -DestinationPath "$env:TEMP\backend.zip" -Force
   ```

   Upload `backend.zip` to `caerus-backend`.

7. **Connect via Session Manager**, not EC2 Instance Connect - the instance
   has no public IP for Instance Connect to reach in the first place. EC2
   Console → select `caerus-server-1` → **Connect → Session Manager →
   Connect**, or `aws ssm start-session --target <instance-id>` from a
   terminal with the AWS CLI configured. The session lands as `ssm-user`;
   switch to `ec2-user` first so file ownership and the `pm2` process started
   below line up with the account any later automation would expect:

   ```bash
   sudo su - ec2-user
   sudo dnf install -y nodejs20 postgresql16 unzip
   mkdir -p ~/caerus-api && cd ~/caerus-api
   aws s3 cp s3://caerus-backend/backend.zip .
   unzip backend.zip
   npm ci --omit=dev
   ```

8. **Create `.env`** - the one file that is never in the zip and never in
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

9. **Run under `pm2`** so the process survives a disconnect and a reboot:

   ```bash
   sudo npm install -g pm2
   pm2 start src/server.js --name caerus-api
   pm2 startup   # run the sudo command it prints
   pm2 save
   ```

10. **Verify from inside the session first**:

    ```bash
    curl http://localhost:3000/api/v1/health
    # {"ok":true}
    ```

    then, from a developer's own machine, without ever exposing the instance
    to the internet: open a local port-forwarding session -

    ```bash
    aws ssm start-session --target <instance-id> \
      --document-name AWS-StartPortForwardingSession \
      --parameters '{"portNumber":["3000"],"localPortNumber":["3000"]}'
    ```

    and `curl http://localhost:3000/api/v1/health` against the tunnel from a
    second terminal. This is the pattern used for every external check of a
    private instance until section 5.7.5 gives the compute tier a real public
    entry point.

<!-- ![Session Manager connected to caerus-server-1, and a curl response through an SSM port-forwarding tunnel](/images/5-Workshop/5.7-EC2/5.7.2-deploy-api/example.png) -->
