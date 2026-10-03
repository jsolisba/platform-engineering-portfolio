# AWS ALB / WordPress 502 Production Incident

## Overview

A production WordPress workload behind an AWS Application Load Balancer
started returning HTTP 502 responses.

The primary objective was to restore application availability while
avoiding unnecessary infrastructure changes that could introduce
additional risk.

## Environment

- AWS Application Load Balancer
- VPC
- Application subnet
- Security Groups
- Network ACLs
- Docker
- Apache
- WordPress

## Customer Impact

The ALB was unable to forward requests to the WordPress backend because
the target was considered unhealthy.

Users accessing the application through the load balancer received HTTP
502 responses.

## Initial Hypotheses

I initially considered several possible causes:

1. WordPress or Apache application failure
2. Incorrect ALB health-check configuration
3. Network routing problems
4. Security Group or NACL restrictions
5. Docker/container connectivity issues

## Investigation

I traced the complete request path:

ALB
  ↓
VPC routing
  ↓
Security Groups / NACLs
  ↓
Host
  ↓
Docker
  ↓
Apache
  ↓
WordPress

I reviewed:

- ALB target health
- Route tables
- Security Groups
- Network ACLs
- Container status
- Docker logs
- Apache logs
- Backend connectivity
- HTTP responses using curl

## Evidence

The ALB health checks were reaching the WordPress backend.

However, the WordPress endpoint used by the health check returned
HTTP 302 instead of the successful response expected by the load balancer.

A separate phpMyAdmin endpoint on the same host returned HTTP 200,
which helped confirm that the underlying network path was functioning.

The investigation therefore moved away from a general connectivity
failure and toward application/health-check behavior.

## Root Cause

The ALB health check was targeting the WordPress root path.

The WordPress application redirected that request to an installation
path because of an incomplete or unexpected WordPress configuration.

As a result, the ALB considered the target unhealthy.

## Mitigation

I corrected the WordPress configuration and adjusted the health-check
behavior so that the ALB could validate the backend using an endpoint
that returned a successful HTTP response.

## Validation

After the change I validated:

- ALB target health
- HTTP response from the backend
- Apache behavior
- Docker container status
- End-to-end application access

The target became healthy and the ALB resumed forwarding requests
successfully.

## Architecture Path

```text
Internet
   |
   v
AWS ALB
   |
   v
Target Group
   |
   v
Application Host
   |
   v
Docker
   |
   v
Apache
   |
   v
WordPress

Lessons Learned

The incident reinforced the importance of validating health checks as
part of the complete application request path.

A healthy network path does not necessarily mean that an application
will satisfy a load balancer health check.

Health checks should validate application availability without depending
on redirects, installation flows, authentication, or other behavior
that can incorrectly signal backend failure.

Skills Demonstrated
AWS ALB
AWS networking
Security Groups
Network ACLs
Docker
Apache
WordPress
Production troubleshooting
Incident response
Root-cause analysis
Application health checks
