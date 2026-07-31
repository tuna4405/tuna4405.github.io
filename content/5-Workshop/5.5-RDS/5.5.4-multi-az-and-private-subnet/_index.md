---
title : "Multi-AZ and the Private Subnet"
date : 2026-06-01
weight : 4
chapter : false
pre : " <b> 5.5.4 </b> "
---

With the application working end to end on a Single-AZ instance in the
default (public) subnet, this section adds two production-shaped
improvements that were not part of the original nine-week plan: automatic
failover, and taking the database off any network path to the internet.

#### Why the database was in a public subnet at all

"Public access: No" (section 5.5.1) controls whether RDS assigns a public IP
and accepts connections from outside the VPC - it says nothing about which
*subnet* the instance sits in. The default VPC's only subnets have a route to
an Internet Gateway, so a Single-AZ instance created against the default DB
subnet group is, strictly speaking, sitting in a public subnet the whole
time, protected only by the security group. Moving it into a genuinely
private subnet - one whose route table has no path to the internet at all -
is a second, independent layer of defence that holds even if a security
group is ever misconfigured.

1. **Create two private subnets, one per Availability Zone** (RDS Multi-AZ
   requires a DB subnet group spanning at least two AZs): `caerus-private-1a`
   in `ap-southeast-1a`, `caerus-private-1b` in `ap-southeast-1b`, each with a
   CIDR block that does not overlap the VPC's existing subnets.

2. **Create a route table with no `0.0.0.0/0` route** and associate both new
   subnets with it - the *absence* of an internet-gateway route is what
   makes a subnet private, not the name given to it. No NAT gateway is
   needed: RDS never initiates outbound internet traffic on its own.

3. **Create a new DB subnet group** (`caerus-private-subnet-group`) using the
   two new private subnets.

4. **Enable Multi-AZ.** RDS Console → the instance → **Modify → Availability
   & durability → Multi-AZ deployment**, choosing **Create a standby
   instance**. This requires switching the instance's template from Free
   Tier to **Production** first - Free Tier disables the option outright,
   since a standby is not part of the Free Tier allowance.

#### Where this got genuinely awkward

Moving an existing Multi-AZ instance's DB subnet group is not a clean
one-step operation. Two separate attempts - via the AWS CLI and via the
Console's own Modify wizard - both failed with variations of the same
misleading validation error, to the effect that an instance cannot be moved
to a subnet group "in a different VPC" even when the target subnet group is
in the *same* VPC. This is a known rough edge in how RDS validates a subnet
group change against an instance that is not using a custom (non-default)
subnet group to begin with.

{{% notice warning %}}
Two workarounds exist for this specific error: temporarily disable Multi-AZ,
change the subnet group, then re-enable it; or hop through an intermediate
custom subnet group built from the *existing* public subnets before moving
to the private one. Both are legitimate. In this project, since the
database held nothing but seed data at the time, the faster and more
reliable option was chosen instead: **delete the instance and create a new
one**, selecting the private subnet group and Multi-AZ directly at creation
time, where none of this validation logic applies. If the database is
expendable, prefer this over debugging the migration path.
{{% /notice %}}

5. **If recreating**: delete the old instance (skip the final snapshot for
   throwaway seed data), then repeat section 5.5.1 with **Templates:
   Production**, **Multi-AZ: Create a standby instance**, and **DB subnet
   group: `caerus-private-subnet-group`** all selected at creation, and
   repeat section 5.5.2 to reload the schema and seed data.

6. **A second, unrelated trap in the same wizard**: the Production template
   defaults storage to **Provisioned IOPS SSD (io2)** at 100 GiB, which is
   not Free-Tier-eligible and bills separately for both storage and
   provisioned IOPS - on the order of hundreds of US dollars a month at that
   IOPS setting. Change **Storage type** to **General Purpose SSD (gp3)** and
   **Allocated storage** to **20 GiB** before creating.

7. **Verify**: the endpoint hostname is unchanged (RDS keeps the same DNS
   name across this kind of change), so nothing in the application's
   `DATABASE_URL` needs to change. Confirm from an EC2 instance in the same
   VPC (section 5.7) that the database is still reachable - a private
   subnet blocks the *internet*, not traffic from elsewhere inside the same
   VPC, which is exactly the access pattern the application needs.

<!-- ![RDS instance details showing Multi-AZ enabled and the private DB subnet group](/images/5-Workshop/5.5-RDS/5.5.4-multi-az-and-private-subnet/example.png) -->
