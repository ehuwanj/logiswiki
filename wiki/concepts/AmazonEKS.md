---
title: "Amazon EKS"
type: concept
tags: [aws, kubernetes, eks, container, orchestration]
sources:
  - "[raw/shipit/ADR2 Backend server technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844191/ADR2+Backend+server+technology)"
last_updated: 2026-05-16
---

## Definition

Amazon EKS (Elastic Kubernetes Service) is AWS's managed Kubernetes cluster service. It handles control plane operations while workloads run on EC2 worker nodes (or Fargate). GLS ShipIT department had an existing EKS cluster (originally for GPP), which was reused for ShipIT Farm backend instances (ADR2).

## Key Information

- GLS uses EKS for multiple ShipIT applications; ShipIT Farm deployed in a separate namespace (possibly separate node group)
- Istio service mesh already available in the existing cluster
- Certificate management and load balancing (ALB) already integrated
- Auto-scaling and load balancing supported out of the box
- Stateless application containers connect to shared Aurora Serverless v2 database
- ShipIT Farm scheduler (single-node pod) is never externally reachable - deployed in cluster but inaccessible from outside

**Comparison with Amazon ECS Fargate:**
- ECS Fargate: cheaper for simple cases; fully serverless containers; easier setup
- EKS: more complexity but existing infrastructure; broader tooling (Istio, cert-manager)

## Related Links

- [[ShipItFarm]] - uses EKS for backend hosting
- [[AuroraServerless]] - database connected from EKS pods
- [[shipit-adr2-backend-server-technology]] - ADR choosing EKS
