# CompTIA Cloud+ CV0-004 — Study Repository

[![CompTIA Cloud+](https://img.shields.io/badge/Certification-CompTIA%20Cloud%2B%20CV0--004-0078D4?style=for-the-badge&logo=comptia&logoColor=white)](https://www.comptia.org/certifications/cloud)
[![Domain Status](https://img.shields.io/badge/Domain%201-Complete-brightgreen?style=for-the-badge)](./progress/exam-progress.md)
[![Status](https://img.shields.io/badge/Status-Active%20Study-yellow?style=for-the-badge)](./ROADMAP.md)
[![Maintenance](https://img.shields.io/badge/Maintained-Yes-blueviolet?style=for-the-badge)](./CHANGELOG.md)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](./LICENSE)
[![Markdown](https://img.shields.io/badge/Docs-Markdown-000000?style=for-the-badge&logo=markdown)](https://commonmark.org)

> Study repository documenting preparation for the **CompTIA Cloud+ CV0-004** certification — built to demonstrate cloud engineering depth, documentation discipline, and continuous learning.

---

## Purpose

This repository is more than a set of study notes. It is a **living knowledge base** that proves — at a glance — that I can:

- Translate dense technical specifications into clear, structured documentation.
- Reason about trade-offs (cost vs. availability, control vs. speed, security vs. usability).
- Apply cloud concepts to real cloud-provider offerings (AWS, Azure, GCP).
- Maintain professional technical documentation with version history, roadmaps, and contribution standards.

It is designed to be readable by **Cloud Engineers, DevOps Engineers, Network Engineers, Infrastructure Engineers, and Technical Recruiters** in under 60 seconds.

---

## Skills Demonstrated

| Domain | Specific Skills |
|---|---|
| **Cloud Computing** | IaaS / PaaS / SaaS / FaaS, shared responsibility, deployment models (public/private/hybrid/community/multicloud) |
| **AWS** | EC2, Lambda, Fargate, EKS, S3, EBS, Aurora, CloudWatch, IAM, Route 53, CloudFormation, Bedrock |
| **Microsoft Azure** | Azure VMs, Functions, Container Apps, AKS, Cosmos DB, Monitor, Entra ID, Bicep |
| **Google Cloud** | Compute Engine, Cloud Run, Cloud Functions, GKE, Cloud SQL, BigQuery, Vertex AI |
| **Virtualization** | Hypervisors (Type 1 / Type 2), vCPU, vRAM, NUMA, oversubscription, live migration |
| **Containers** | Docker, Kubernetes architecture, pods, deployments, services, namespaces, serverless containers |
| **Networking** | VPC/VNet, subnets, routing, load balancers (L4/L7), DNS, CDN, VPN, Direct Connect, SD-WAN |
| **Security** | IAM, RBAC, least privilege, encryption at rest/in transit, KMS, secrets management, compliance |
| **DevOps** | CI/CD pipelines, IaC (Terraform, CloudFormation, Bicep), GitOps, configuration management |
| **Disaster Recovery** | RTO, RPO, hot / warm / cold sites, pilot light, backup strategies, multicloud DR |
| **High Availability** | Regions, AZs, edge locations, auto-scaling, load balancing, clustering, failover |
| **Storage** | Block, file, object, archival; RAID levels; storage tiering; replication |
| **Databases** | Relational vs. non-relational, OLTP vs. OLAP, replication, sharding, caching |
| **Cost Optimization** | Reserved / Spot / On-Demand, TCO, tagging, right-sizing, FinOps |
| **Documentation** | Markdown, Mermaid diagrams, structured templates, version-controlled knowledge |

---

## Repository Structure

```text
.
├── README.md                              ← You are here
├── LICENSE                                ← MIT
├── CHANGELOG.md                           ← Release history
├── ROADMAP.md                             ← Study roadmap
├── CONTRIBUTING.md                        ← How to contribute
├── CODE_OF_CONDUCT.md                     ← Community standards
├── SECURITY.md                            ← Security policy
│
├── docs/                                  ← High-level study guidance
│   └── STUDY-GUIDE.md
│
├── objectives/                            ← All exam objectives
│   ├── domain-1/                          ← Cloud Architecture & Design (23%) ✅
│   │   ├── 1.1-cloud-service-models-and-shared-responsibility.md
│   │   ├── 1.2-service-availability-concepts.md
│   │   ├── 1.3-cloud-networking-concepts.md
│   │   ├── 1.4-storage-resources-and-technologies.md
│   │   ├── 1.5-cloud-native-design-concepts.md
│   │   ├── 1.6-containerization-concepts.md
│   │   ├── 1.7-virtualization-concepts.md
│   │   ├── 1.8-cost-considerations.md
│   │   ├── 1.9-database-concepts.md
│   │   ├── 1.10-methods-for-optimizing-workloads.md         ← source: ../CloudPlus_Objective_1.10_Notes.md
│   │   └── 1.11-evolving-technologies-in-the-cloud.md       ← source: ../CloudPlus_Objective_1.11_Notes.md
│   ├── domain-2/                          ← Cloud Operations (Coming)
│   ├── domain-3/                          ← Cloud Security (Coming)
│   ├── domain-4/                          ← DevOps Fundamentals (Coming)
│   └── domain-5/                          ← Troubleshooting (Coming)
│
├── diagrams/                              ← Mermaid architecture diagrams
│   ├── README.md
│   ├── shared-responsibility.md
│   ├── high-availability.md
│   ├── disaster-recovery.md
│   ├── kubernetes-architecture.md
│   └── cicd-pipeline.md
│
├── cheatsheets/                           ← Fast-review study aids
│   ├── domain-1-cheatsheet.md
│   ├── shared-responsibility-matrix.md
│   └── exam-day-quick-ref.md
│
├── flashcards/                            ← Q&A flashcards (Markdown / Anki)
│   └── domain-1-flashcards.md
│
├── practice-questions/                    ← Self-authored practice Qs
│   └── domain-1-practice.md
│
├── resources/                             ← External references
│   ├── exam-resources.md
│   └── cloud-provider-mapping.md          ← CV0-004 → AWS / Azure / GCP
│
└── progress/                              ← Visible learning progress
    ├── exam-progress.md
    └── learning-log.md
```

---

## Exam Progress Tracker

> Updated automatically as objectives are completed. See [`progress/exam-progress.md`](./progress/exam-progress.md) for details.

| Domain | Topic | Weight | Status | Notes |
|---|---|---:|:---:|---|
| **1.0** | Cloud Architecture & Design | 23% | ✅ Complete | 11 / 11 objectives |
| **2.0** | Cloud Operations | 17% | ⏳ Planned | See [ROADMAP.md](./ROADMAP.md) |
| **3.0** | Cloud Security | 20% | ⏳ Planned | |
| **4.0** | DevOps Fundamentals | 17% | ⏳ Planned | |
| **5.0** | Troubleshooting | 23% | ⏳ Planned | |
| | **Exam scheduled** | | 🎯 TBD | |

**Legend:** ✅ Complete · 🟡 In Progress · ⏳ Planned · 🎯 Target

---

## Key Learning Areas (Domain 1 — Complete)

### 1.1 — Cloud Service Models & Shared Responsibility
IaaS, PaaS, SaaS, FaaS — and the **shared responsibility model** that defines who secures what. Foundation for every cloud decision. → [notes](./objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md)

### 1.2 — Service Availability
Regions, Availability Zones, edge locations, RTO/RPO, hot/warm/cold sites, cloud bursting, multicloud. → [notes](./objectives/domain-1/1.2-service-availability-concepts.md)

### 1.3 — Cloud Networking
VPCs/VNets, subnets, routing, load balancers (L4/L7), DNS, CDN, VPN, Direct Connect, NAT, security groups, NACLs. → [notes](./objectives/domain-1/1.3-cloud-networking-concepts.md)

### 1.4 — Storage
Block, file, object, archival. RAID, replication, snapshots, lifecycle policies, tiered storage, performance metrics (IOPS, throughput, latency). → [notes](./objectives/domain-1/1.4-storage-resources-and-technologies.md)

### 1.5 — Cloud-Native Design
Microservices, 12-factor app, statelessness, elasticity, event-driven architecture, API-first design, queues, pub/sub. → [notes](./objectives/domain-1/1.5-cloud-native-design-concepts.md)

### 1.6 — Containerization
Docker, container images, registries, Kubernetes (pods, deployments, services, namespaces, RBAC), Helm, serverless containers. → [notes](./objectives/domain-1/1.6-containerization-concepts.md)

### 1.7 — Virtualization
Hypervisors (Type 1 / Type 2), vCPU / vRAM, NUMA, oversubscription, live migration, snapshots, resource pooling. → [notes](./objectives/domain-1/1.7-virtualization-concepts.md)

### 1.8 — Cost Considerations
CapEx vs OpEx, TCO, Reserved / On-Demand / Spot, right-sizing, tagging, FinOps, chargeback / showback. → [notes](./objectives/domain-1/1.8-cost-considerations.md)

### 1.9 — Database Concepts
Relational vs non-relational, OLTP vs OLAP, ACID vs BASE, replication, sharding, caching, connection pooling, managed databases. → [notes](./objectives/domain-1/1.9-database-concepts.md)

### 1.10 — Methods for Optimizing Workloads
Picking the right **compute model** (VM / Container / Serverless), tuning **storage IOPS vs throughput**, **container orchestration**, **workflow** orchestration, optimizing **network latency vs throughput**, and leveraging **managed services** to cut ops overhead. → [notes](./CloudPlus_Objective_1.10_Notes.md)

### 1.11 — Evolving Technologies in the Cloud
The 7 **AI/ML** capabilities (text recognition, translation, visual recognition, sentiment, voice-to-text, text-to-voice, generative AI) and the 4 **IoT** components (sensors, gateways, communication, transmission protocols). → [notes](./CloudPlus_Objective_1.11_Notes.md)

---

## Sample Architecture Diagrams

All diagrams are native **Mermaid** — render directly on GitHub without extra tooling.

| Diagram | Description |
|---|---|
| [Shared Responsibility Model](./diagrams/shared-responsibility.md) | Customer vs provider responsibilities across IaaS / PaaS / SaaS / FaaS |
| [High Availability Architecture](./diagrams/high-availability.md) | Multi-AZ, multi-region HA pattern with auto-scaling |
| [Disaster Recovery Strategy](./diagrams/disaster-recovery.md) | RTO/RPO trade-offs and hot/warm/cold site selection |
| [Kubernetes Architecture](./diagrams/kubernetes-architecture.md) | Control plane, worker nodes, pods, services |
| [CI/CD Pipeline](./diagrams/cicd-pipeline.md) | Code → Build → Test → Deploy with IaC and policy gates |

---

## Technologies Covered

**Cloud Providers:** AWS · Azure · Google Cloud · Oracle Cloud · IBM Cloud
**Compute:** EC2 · Lambda · Fargate · Azure VMs · Azure Functions · Cloud Run · Compute Engine
**Containers:** Docker · Kubernetes · EKS · AKS · GKE · Helm · Istio
**Storage:** S3 · EBS · EFS · FSx · Azure Blob · Azure Disk · Cloud Storage · Persistent Disk
**Networking:** VPC · VNet · Cloud Router · Route 53 · Azure Front Door · Cloud CDN · Cloudflare
**Databases:** Aurora · RDS · DynamoDB · Cosmos DB · Cloud SQL · BigQuery · AlloyDB
**IaC / DevOps:** Terraform · CloudFormation · Bicep · Ansible · GitHub Actions · ArgoCD
**Monitoring:** CloudWatch · Azure Monitor · Cloud Logging · Prometheus · Grafana
**Security:** IAM · Entra ID · Cloud IAM · KMS · WAF · GuardDuty · Security Center

---

## Why This Repository Exists

> I believe the best way to learn something is to **document it as if I were teaching it** to someone joining the team tomorrow. This repository is the artifact of that belief.

- For **recruiters**: open the [Domain 1.1 notes](./objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md) to see the depth and structure of my technical writing.
- For **hiring managers**: review the [diagrams](./diagrams/) and [cheatsheets](./cheatsheets/) to assess how I synthesize and apply cloud knowledge.
- For **peers**: feel free to [open an issue](../../issues) if you spot an error, or [submit a PR](../../pulls) with improvements.

The repository follows GitHub best practices: semantic commit history, structured Markdown, Mermaid diagrams, a roadmap, a changelog, and a security policy. Every file is meant to render correctly on GitHub's web UI.

---

## Quick Links

- 📘 [Study Guide](./docs/STUDY-GUIDE.md)
- 📊 [Exam Progress](./progress/exam-progress.md)
- 🗺️ [Roadmap](./ROADMAP.md)
- 📝 [Changelog](./CHANGELOG.md)
- 🤝 [Contributing](./CONTRIBUTING.md)
- 🔒 [Security](./SECURITY.md)

---

**Author:** CompTIA Cloud+ CV0-004 Candidate
**Last updated:** see [CHANGELOG.md](./CHANGELOG.md)
