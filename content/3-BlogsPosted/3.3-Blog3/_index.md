---
title: "Blog 3"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS CONFIG AND CONFORMANCE PACKS

**Published on: 30-07-2026** <!-- FILL IN date -->

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2228770524554574/** <!-- FILL IN post URL -->

### Why I wrote this

Another problem I suspect most people new to AWS run into: when creating
resources in the console, you tend to click through quickly and skip a lot of
options. After a while, you no longer remember which bucket is public, which RDS
instance has encryption switched off, or which security group has a port open to
the internet.

That led me to **AWS Config**, the service that records the configuration state
of resources in an account and evaluates them against predefined rules.
Alongside it is the **Conformance Pack**, which the AWS documentation describes
as a collection of AWS Config rules and remediation actions packaged to be
deployed as a single entity in one account and region, or across an entire
organisation in AWS Organizations.

Put simply: instead of enabling each check individually, you can deploy a whole
set of rules at once.

### Key concepts

* **Configuration Item** - a record of a resource's configuration state at a
  point in time. Every time a resource is created, modified, or deleted, AWS
  Config generates a new record. That is what produces the change history for
  that resource.
* **Config Rule** - a rule that evaluates whether a resource is COMPLIANT or
  NON_COMPLIANT. AWS provides a long list of managed rules, for example checking
  whether an S3 bucket blocks public access, or whether an RDS instance has
  encryption enabled.
* **Remediation Action** - the corrective action taken when a resource fails.
  It can run automatically, or manually after review.

A conformance pack is a YAML file that groups these rules and remediation
actions together.

### What templates are available

AWS provides a number of sample templates ready to use, selectable in the
console or downloadable from GitHub. They fall into a few main groups:

* Operational Best Practices for individual services such as S3, RDS, IAM, EC2.
* Operational Best Practices aligned to the pillars of the AWS Well-Architected
  Framework.
* Templates mapped to familiar standards such as the CIS AWS Foundations
  Benchmark, NIST 800-53, HIPAA, and PCI DSS.

One point AWS states very clearly in the documentation, and which I think
matters: these sample templates are **not designed to guarantee compliance with
any particular governance standard**. They do not replace internal processes and
do not guarantee passing a compliance assessment. They are a review aid, not a
certification.

### Deployment steps

1. Enable AWS Config in the region you want to use, choose the resource types to
   record, and create an IAM role giving Config permission to read
   configuration.
2. Open the AWS Config Console, choose **Conformance packs**, then **Deploy
   conformance pack**.
3. Choose the template source. *Use sample template* takes one from the
   built-in list. *Template is ready* is for a template of your own, which can
   come from S3, from an SSM document, or be uploaded directly. Note that a
   template larger than 50 KB must be stored in S3.
4. Name the conformance pack. The name must be unique, up to 256 alphanumeric
   characters, and may contain hyphens but not spaces.
5. Provide parameters if needed. Many templates accept parameters to adjust
   thresholds, for example the maximum age of an access key. This is a good way
   to customise a template without editing it directly.
6. Deploy, then review the dashboard, which shows the compliance ratio, the
   rules that failed, and exactly which resources are in violation.

One further note from the documentation: check the list of rules available in
the region you intend to deploy to, because not every rule exists in every
region. If a template contains an unsupported rule, it may need editing before
deployment.

### Advantages

* A whole set of rules can be deployed at once, instead of enabling each one by
  hand.
* Templates already exist for individual services and for well-known standards,
  so you do not have to invent the checklist yourself.
* The template is YAML, so it can be committed to Git and managed as code.
* It can be deployed across multiple accounts in AWS Organizations to create a
  shared baseline.
* Configuration history is retained, which is useful when you need to know when
  a setting was changed.

### Points worth knowing

**On cost.** This is the part I think students should read carefully before
switching it on. AWS Config charges on three components: configuration items
recorded, rule evaluations, and conformance pack evaluations.

According to the pricing page, a configuration item costs 0.003 USD per record
under continuous recording. Rule evaluations and conformance pack evaluations
cost 0.001 USD for the first 100,000 per region per month, with tiered
reductions after that.

What is worth noting is that evaluations are **not covered by the free tier**,
meaning they are charged from the first one. A conformance pack containing a few
dozen rules, running across many resources, can generate far more evaluations
than you would initially imagine. If you are only using it to learn, I would
limit the resource types being recorded and remember to delete the conformance
pack after testing.

**On automatic remediation.** I would not enable automatic remediation from the
outset. A corrective action that runs against the wrong thing can affect
resources that are in use. Running in manual mode first is safer.

**On reading the results.** A 100 percent compliance ratio only means the rules
in that pack all passed. It does not mean the system is secure. Equally, a
failing rule is not necessarily a problem, because it depends on the context of
use. What matters is understanding what each rule actually checks.

### When to use it

* Establishing a shared configuration baseline across several accounts or team
  members.
* Quickly reviewing where the current environment has drifted from best
  practice.
* Needing configuration change history for audit or incident investigation.
* Wanting to automatically correct a class of misconfiguration that recurs
  often.

For a personal account with only a few resources, I would start with one or two
individual Config rules to get familiar, then move to a conformance pack later.

### Conclusion

AWS Config answered two questions I had previously been vague about: how are my
resources currently configured, and how has that configuration changed over
time.

The conformance pack means I do not have to invent the list of things worth
checking, and can start from a set of rules AWS has already assembled.

### References

* AWS Config - Conformance Packs:
  https://docs.aws.amazon.com/config/latest/developerguide/conformance-packs.html
* AWS Config - Deploying Conformance Packs:
  https://docs.aws.amazon.com/config/latest/developerguide/conformance-pack-deploy.html
* AWS Config - Conformance Pack Sample Templates:
  https://docs.aws.amazon.com/config/latest/developerguide/conformancepack-sample-templates.html
* AWS Config - List of AWS Config Managed Rules:
  https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html
* AWS Config Pricing:
  https://aws.amazon.com/config/pricing/

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.3-Blog3/ and is referenced like:
-->

![Published post on the AWS Study Group community page](/images/3-BlogsPosted/3.3-Blog3/post.png)