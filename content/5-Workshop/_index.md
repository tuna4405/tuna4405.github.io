---
title: "Workshop"
date: 2026-06-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Caerus: A Cinema Seat Booking Platform on AWS

#### Overview

Caerus is a cinema seat booking platform: customers browse screenings, pick
seats from a live 6x10 seat map, book up to six seats in one transaction,
cancel before showtime, and download a PDF ticket; administrators create
screenings and upload poster art. The one requirement that shapes every other
decision in this workshop is that **a seat can never be sold twice**, even
when two customers click the same seat in the same instant - which is why the
booking transaction, the locking strategy, and the concurrency test all get a
dedicated section rather than a passing mention.

The deployment runs entirely in **ap-southeast-1** and evolves in stages
across this workshop rather than appearing fully formed: a static React site
on Amazon S3, an Express API on Amazon EC2 behind an Application Load
Balancer with two instances across two Availability Zones, PostgreSQL on
Amazon RDS running Multi-AZ inside a private subnet, Amazon CloudFront (with
AWS WAF) in front of both the site and the API for HTTPS on a single domain,
and Amazon CloudWatch with SNS watching all of it. The compute layer itself
ends up fully private: both EC2 instances sit behind a NAT gateway with no
public IP and no SSH port open at all, administered instead through AWS
Systems Manager Session Manager - the same operational posture the database
already has in section 5.5.1, arrived at for the same reason.

Each section below builds on the state left by the one before it. Follow them
in order the first time through.

#### Content

1. [Introduction](5.1-Overview/)
2. [Prerequisites](5.2-Prerequisites/)
3. [System Design](5.3-Design/)
4. [Local Development](5.4-Local-Build/)
5. [Amazon RDS for PostgreSQL](5.5-RDS/)
6. [Amazon S3](5.6-S3/)
7. [Amazon EC2 and Deployment](5.7-EC2/)
8. [CloudWatch and SNS](5.8-CloudWatch/)
9. [Testing](5.9-Testing/)
10. [Cost and Resource Management](5.10-Cost/)
11. [Cleaning Up Resources](5.11-Cleanup/)
12. [Repository, Live Site, and Demo](5.12-Links-and-Demo/)
