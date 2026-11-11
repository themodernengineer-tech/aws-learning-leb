# Architecture Lab 02 — The Capacity Gamble

**AWS Cloud Practitioner | Cloud Economics | Compute Architecture**

## Project Overview

How should a cloud application handle unpredictable traffic without paying for excessive unused capacity?

In this architecture study, I investigate that question using **WildWatch**, a fictional wildlife monitoring platform that receives sensor observations from remote devices.

The objective is to compare three AWS compute approaches and understand how each affects **cost, scalability, availability, security, and operational complexity**.

## The Engineering Challenge

WildWatch processes approximately **1 million sensor observations per month**, with occasional traffic spikes reaching 20 times the average workload.

A continuously running server may waste capacity during quiet periods. A serverless solution may reduce idle compute costs but introduces different operational and architectural considerations.

The challenge is to determine which approach best fits the workload rather than assuming that one AWS service is always the right choice.

### Workload Assumptions

| Parameter | Assumption |
|---|---|
| Monthly observations | 1,000,000 |
| Peak traffic | 20× normal volume |
| Processing time | 150 ms per observation |
| Processing memory | 256 MB |
| Data retention | 30 days |
| AWS Region | Canada West (Calgary) |

*These values are hypothetical inputs for architectural evaluation.*

## What I Designed

### Architecture A — Fixed EC2 Processing

I created an AWS architecture diagram using **diagrams.net and official AWS architecture icons**, modeling a single-instance environment for processing incoming sensor observations.

![WildWatch — Fixed EC2 Architecture](architecture/01-fixed-ec2.png)

**Components and their responsibilities:**

| Component | Architectural Role |
|---|---|
| Amazon VPC | Defines the isolated virtual network |
| Availability Zone | Identifies the infrastructure failure domain |
| Public Subnet | Provides the proposed network placement for EC2 |
| Internet Gateway | Supports internet connectivity for the VPC |
| Public Route Table | Defines the subnet's routing relationship with the Internet Gateway |
| Amazon EC2 | Runs the proposed sensor-processing application |
| Security Group | Restricts inbound and outbound network traffic |
| Amazon EBS | Provides persistent block storage attached to EC2 |
| Wildlife Sensors | Represent external devices submitting observations |

**Proposed data flow:**

`Wildlife Sensors → Internet Gateway → EC2 Processing Application → EBS`

The route table defines network routing; it is not a separate application-processing hop.

### Architecture Decisions

**Why fixed EC2?**

A single EC2 instance provides a straightforward baseline for estimating continuously provisioned compute capacity. It also exposes the trade-offs of running a workload on one server.

**Why a public subnet?**

The conceptual design examines direct internet-facing ingestion through a public EC2 endpoint. A production system would require appropriate routing, public addressing, TLS, authentication, and restrictive security group rules.

**Why EBS?**

EBS provides attached persistent block storage. It is included to account for storage costs and lifecycle considerations, but it should not be mistaken for a managed application database.

### Limitations

This architecture has several important constraints:

- A single EC2 instance creates a single-instance failure point.
- Capacity does not automatically increase during traffic spikes.
- Direct internet exposure requires careful security configuration.
- EBS storage is tied to an Availability Zone.
- The ingestion endpoint, application authentication, and production data-management design remain unspecified.

These limitations motivate the subsequent elastic and serverless architecture comparisons.

## Architecture Comparison

| Alternative | Proposed AWS Services | Status |
|---|---|---|
| A — Fixed Compute | EC2, EBS, VPC | Diagram created |
| B — Elastic Compute | EC2 Auto Scaling, Application Load Balancer | Pending |
| C — Serverless | API Gateway, Lambda, DynamoDB | Pending |

The comparison will evaluate estimated monthly costs and architectural trade-offs using AWS Pricing Calculator.

## What I Learned

This architecture exercise examines:

- How AWS Regions, Availability Zones, VPCs, and subnets relate.
- How route tables and Internet Gateways support public-subnet connectivity.
- How security groups restrict instance network access.
- Why EC2 and EBS have different lifecycle and cost implications.
- Why fixed compute capacity can become a limitation when workloads fluctuate.
- How to communicate AWS infrastructure using architecture diagrams.

## Project Files

- [Architecture A — PNG](architecture/01-fixed-ec2.png)
- [Architecture A — Editable Diagram](architecture/01-fixed-ec2.drawio)

## Implementation Status

**Architecture study in progress.**

Architecture A has been designed as a conceptual model. No EC2 instances, VPC resources, load balancers, or databases have been provisioned for this lab.

The diagram is **not evidence of deployment, security validation, or performance testing**. Pricing results and the remaining architecture alternatives will be documented as they are completed.

---

**Objective:** Develop practical AWS architecture skills by analyzing engineering requirements, designing alternative solutions, and documenting technical decisions with supporting evidence.
