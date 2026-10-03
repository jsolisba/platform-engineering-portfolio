# EKS FastAPI AI Platform

## Overview

I built and maintained a FastAPI-based AI question-answering service running on Amazon EKS.

The platform integrated Kubernetes, AWS networking, an Application Load Balancer, vLLM, Aurora PostgreSQL, and ElastiCache.

I personally owned the application and infrastructure integration, including the API layer, deployment architecture, and supporting AWS/Kubernetes components.

## Architecture

The platform used a private VPC architecture with the application deployed on Amazon EKS.

```text
                    Client
                      |
                      v
                 AWS ALB
                      |
                      v
              Amazon EKS
                      |
                 FastAPI API
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
        vLLM     Aurora PostgreSQL  ElastiCache
```

## Environment

* Amazon EKS
* AWS private VPC
* Application Load Balancer
* FastAPI
* Python
* vLLM
* Aurora PostgreSQL
* ElastiCache
* Kubernetes

## Platform Responsibilities

I personally worked on:

* FastAPI API integration
* Application deployment architecture
* Kubernetes integration
* AWS infrastructure integration
* ALB integration
* Connectivity to downstream services
* Database integration
* Caching integration

## Architecture Considerations

A key architectural consideration was preventing failures in downstream components from becoming uncontrolled failures across the entire service.

I treated the main dependencies as separate failure domains:

```text
API Request
    |
    v
FastAPI
    |
    +--------> vLLM
    |
    +--------> Aurora PostgreSQL
    |
    +--------> ElastiCache
```

This separation made it possible to reason about application behavior based on the specific dependency involved rather than treating the entire platform as a single failure domain.

## Deployment Approach

I tested the application locally before deploying it through Kubernetes.

The Kubernetes deployment was then validated before exposing the service through the ALB.

Validation focused on:

* API behavior
* Kubernetes deployment behavior
* Connectivity to dependencies
* Application integration
* ALB exposure

## Validation Flow

```text
Local Application
       |
       v
API Validation
       |
       v
Kubernetes Deployment
       |
       v
Dependency Connectivity
       |
       v
ALB Integration
       |
       v
End-to-End Validation
```

## Reliability Considerations

The architecture considered several independent failure domains:

### API Layer

FastAPI was responsible for request handling and integration with downstream services.

### AI Inference

vLLM represented a separate dependency for AI inference.

### Database

Aurora PostgreSQL provided persistent application data storage.

### Cache

ElastiCache provided the caching layer.

### Platform

EKS provided the Kubernetes execution environment for the application.

Separating these components allowed troubleshooting and failure analysis to be performed at the dependency level.

## Key Engineering Lesson

AI applications are distributed systems.

The API layer, inference engine, database, cache, Kubernetes platform, and network path can fail independently.

Treating these components as separate failure domains makes it easier to reason about failures, validate dependencies, and avoid uncontrolled failure propagation.

## Skills Demonstrated

* Amazon EKS
* Kubernetes
* AWS VPC
* AWS Application Load Balancer
* FastAPI
* Python
* vLLM
* Aurora PostgreSQL
* ElastiCache
* Application / infrastructure integration
* Kubernetes deployment
* Failure-domain analysis
* Platform architecture
* AI application infrastructure
