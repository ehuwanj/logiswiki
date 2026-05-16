---
title: "ShipIT ADR2: Backend Server Technology"
type: source
tags: [source, shipit, farm, kubernetes, eks, adr]
sources:
  - "[raw/shipit/ADR2 Backend server technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844191/ADR2+Backend+server+technology)"
last_updated: 2026-05-16
---

## Summary

This ADR selects the hosting technology for ShipIT Farm backend instances, which must run as stateless Java EE applications on WildFly in a scalable cluster. Four options were evaluated: manual EC2 instances, auto-scaling EC2 with AMI, Amazon ECS (Fargate), and Amazon EKS. Amazon EKS was chosen because an EKS cluster already existed within the ShipIT department and provides Kubernetes advantages including auto-scaling, load balancing, and service mesh via Istio.

## Key Claims

- Decision: Option 4 - Amazon EKS (managed Kubernetes) cluster.
- Farm backend instances must be stateless; all state stored externally in the database.
- ShipIT Farm will be deployed in a separate namespace (possibly separate node group) within the existing EKS cluster for workload isolation.
- EKS integrates with ALB (Application Load Balancer) already used by other ShipIT microservices.
- ECS Fargate (option 3) was also viable and cheaper for simple cases, but EKS was preferred due to existing infrastructure.
- No true vendor lock-in: containerized applications can run on any Kubernetes cluster.

## Evidence and Notes

- Current setup: Java EE application on WildFly; one database per backend installation (monolithic deployment).
- Requirement: support stateless backend with shared Aurora database (see ADR1).
- Manual EC2 (option 1): no auto-scaling, high ops effort.
- ECS Fargate (option 3): good lightweight alternative, integrates with ALB, serverless container pricing.
- EKS complexity: high initial setup; Kubernetes has many features needing initial configuration.

## Related Links

- [[ShipItFarm]] - the ShipIT Farm context
- [[AmazonEKS]] - Amazon EKS concept
- [[AuroraServerless]] - database chosen in ADR1
- [[ShipIt]] - ShipIT backend (WildFly)
- [[shipit-adr1-database-technology]] - database decision
- [[shipit-adr3-synchronizer-deployment]] - Synchronizer deployment follow-up
