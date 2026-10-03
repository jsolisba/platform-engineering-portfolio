                    ┌───────────────┐
                    │   Internet    │
                    └───────┬───────┘
                            │
                            ▼
                         AWS ALB
                            │
                            ▼
                     ┌─────────────┐
                     │     EKS     │
                     │             │
                     │  FastAPI    │
                     └──────┬──────┘
                            │
               ┌────────────┼────────────┐
               ▼            ▼            ▼
            vLLM       Aurora PG     ElastiCache
EKS
private VPC
ALB
FastAPI
vLLM
Aurora PostgreSQL
ElastiCache
failure domains
deployment
request handling
database dependency
caching
observability
troubleshooting
