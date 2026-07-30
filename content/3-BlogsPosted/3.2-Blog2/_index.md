---
title: "Blog 2"
date: 2026-06-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# HOW SEATGEEK CONTROLS AUTHORIZATION, AUTHENTICATION, AND RATE LIMITING IN A MULTI-TENANT SAAS APPLICATION

**Published on: 23-07-2026** 

**Link: https://www.facebook.com/groups/awsstudygroupfcj/permalink/2222000205231606/**

### Why I wrote this

One of the harder problems in building a SaaS system that serves many customers
at once is this: how do you make sure customer A cannot accidentally, or
deliberately, consume all the shared capacity and degrade things for customer B?

This is exactly the problem SeatGeek faced. They are a ticketing platform
serving tens of millions of tickets a day, and the way they solved it using AWS
serverless services taught me several things worth passing on.

### Background: when every service handles its own authentication

Previously, B2B partners queried SeatGeek's business data - potentially
terabytes of it - through a number of different identity and access tools. The
problem was that each internal application implemented its own authorization
logic. That meant duplicated effort, no standardisation, and increasing
difficulty keeping control as the number of tenants grew.

SeatGeek set three criteria when looking for a solution:

1. Continue using Auth0, the identity provider already in place.
2. Avoid adding infrastructure operations overhead, preferring to stitch
   together managed serverless services.
3. Scale smoothly with demand, without paying for idle or over-provisioned
   capacity.

### Key points

**API Gateway as the single front door, with no auth logic in individual
services.** Every SeatGeek API passes through one gateway, where a custom Lambda
authorizer validates the token, rather than each backend service doing it
independently.

**Tiered usage plans to prevent the noisy neighbour problem.** SeatGeek created
tiered plans - bronze, silver, gold - each with its own requests-per-second
limit. Each tenant receives an API key bound to a specific usage plan, so no
tenant can consume the shared capacity that others depend on.

**DynamoDB as the invisible bridge between Auth0 and API Gateway.** Rather than
making tenants manage their own API keys, DynamoDB holds a mapping table between
the tenant ID, which Auth0 manages, and the API key, which API Gateway manages.
Key management becomes entirely transparent to the tenant.

**Automated tenant onboarding with Terraform.** When a new tenant arrives, the
system automatically creates the tenant ID in Auth0, creates a new API key in
API Gateway, and stores the link in DynamoDB. No manual steps.

### Following one request

A B2B partner wants to query twelve months of ticketing data. The flow runs:

```
Tenant
  -> Auth0 (machine-to-machine authentication)
  -> JWT token
  -> API Gateway
  -> Lambda authorizer
  -> DynamoDB (look up API key by tenant ID)
  -> API Gateway checks rate limit against the usage plan
  -> Backend handles the request
```

**Authentication step.** The tenant authenticates machine-to-machine and
receives a JWT containing the claims needed for the authorization that follows:
tenant ID, expiry, scope, and signature.

**Authorization step.** API Gateway extracts the token from the request and
passes it to the Lambda authorizer. The authorizer fetches the token validation
key from Auth0 - and the interesting detail is that this key is **cached in the
authorizer's own memory**, so Auth0 is called only once per Lambda execution
environment start. That reduces latency and avoids hammering the identity
provider.

**Rate limiting step.** Once the authorizer has validated the token and returned
the corresponding API key from DynamoDB, API Gateway checks whether that tenant
has exceeded the rate limit of its usage plan. If it has, API Gateway returns
HTTP 429 Too Many Requests immediately - the request never reaches the backend.

A further detail I liked: API Gateway can cache the authorizer's result for up
to five minutes, so the same token is not revalidated repeatedly within that
window. That takes significant load off both Lambda and DynamoDB.

### Conclusion

What impressed me most about SeatGeek's architecture is how completely they
separated authorization logic from the individual business services, turning it
into a shared layer at API Gateway. That solves two problems at once: it
standardises authentication across the whole system, and it removes the need for
every team to reinvent the same wheel.

I also learned that caching here is not only a performance optimisation. It
serves as cost control and as protection for an external identity provider.
Caching the token validation key inside Lambda, combined with caching the
authorizer response at the API Gateway layer, is a multi-level caching pattern
worth applying to any system that authenticates at high frequency.

For anyone studying how to build multi-tenant SaaS properly, this is a valuable
case study: you do not need to build a complex identity service of your own.
Combining API Gateway, a Lambda authorizer, and DynamoDB carefully is enough to
produce a protection layer that is both standardised and cheap to operate.

### Reference

* How SeatGeek uses AWS Serverless to control authorization, authentication, and
  rate-limiting in a multi-tenant SaaS application - AWS Architecture Blog:
  https://aws.amazon.com/blogs/architecture/how-seatgeek-uses-aws-to-control-authorization-authentication-and-rate-limiting-in-a-multi-tenant-saas-application/

<!-- Screenshot of the published post goes in
     static/images/3-BlogsPosted/3.2-Blog2/ and is referenced like:

-->
![Published post on the AWS Study Group community page](/images/3-BlogsPosted/3.2-Blog2/post.png)