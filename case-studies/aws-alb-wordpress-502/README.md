# AWS ALB / WordPress 502 Production Incident

## Overview

A production WordPress workload behind an AWS Application Load Balancer started returning HTTP 502 responses because the backend target was failing the ALB health check.

The objective was to restore application availability while avoiding unnecessary infrastructure changes.

## Environment

* AWS Application Load Balancer
* AWS VPC
* Route tables
* Security Groups
* Network ACLs
* Linux host
* Docker
* Apache
* WordPress
* Amazon RDS

## Impact

The ALB considered the WordPress backend unhealthy.

As a result, client requests through the load balancer returned HTTP 502 responses.

## Initial Hypotheses

I considered several possible failure domains:

1. WordPress application failure
2. Incorrect ALB health-check configuration
3. VPC routing problems
4. Security Group or NACL restrictions
5. Docker or Apache connectivity issues

## Investigation

I traced the complete request path:

```text
Client
  |
  v
AWS ALB
  |
  v
Target Group
  |
  v
VPC / Routing
  |
  v
Security Group / NACL
  |
  v
Host
  |
  v
Docker
  |
  v
Apache
  |
  v
WordPress
```

I reviewed:

* ALB target health
* Route tables
* Security Groups
* Network ACLs
* Container status
* Docker logs
* Apache logs
* Direct backend connectivity
* HTTP responses using curl

## Evidence

The ALB health-check requests were reaching the WordPress container.

However, WordPress consistently returned HTTP 302 responses.

A separate service on the same host returned HTTP 200, which helped establish that basic network connectivity was functioning.

I then tested the backend directly and inspected Docker and Apache logs to validate the exact response being observed by the ALB.

## Root Cause

The health check was targeting the WordPress root endpoint.

WordPress redirected the request to its installation path because the application was not yet in its expected configured state.

The ALB therefore considered the backend unhealthy.

## Remediation

I completed/corrected the WordPress application configuration and changed the health-check behavior to use an endpoint capable of returning a successful HTTP response without depending on the application redirect.

## Validation

I validated:

* Backend HTTP response
* Docker container status
* Apache behavior
* ALB target health
* End-to-end application access

Recovery was confirmed when the target became healthy and the ALB resumed forwarding requests successfully.

## Key Engineering Lesson

A healthy network path does not necessarily mean that an application will satisfy a load balancer health check.

Health checks should validate application availability using predictable responses and should avoid dependencies on redirects, installation flows, authentication, or other application states that can incorrectly signal backend failure.

## Skills Demonstrated

* AWS ALB
* AWS VPC networking
* Security Groups
* Network ACLs
* Docker
* Apache
* WordPress
* RDS integration
* Production troubleshooting
* Incident response
* Root-cause analysis
* Health-check design
