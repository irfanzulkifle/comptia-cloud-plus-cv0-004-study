# CompTIA Cloud+ CV0-004: Study Repository

[![CompTIA Cloud+](https://img.shields.io/badge/Certification-CompTIA%20Cloud%2B%20CV0--004-0078D4?style=for-the-badge&logo=comptia&logoColor=white)](https://www.comptia.org/certifications/cloud)
[![Objectives](https://img.shields.io/badge/Exam--Ready%20Set-33%20%2F%2033%20Objectives-brightgreen?style=for-the-badge)](./full%20notes/exam-ready/README.md)
[![Status](https://img.shields.io/badge/Status-Active%20Study-yellow?style=for-the-badge)](./ROADMAP.md)
[![Maintenance](https://img.shields.io/badge/Maintained-Yes-blueviolet?style=for-the-badge)](./CHANGELOG.md)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](./LICENSE)
[![Markdown](https://img.shields.io/badge/Docs-Markdown-000000?style=for-the-badge&logo=markdown)](https://commonmark.org)

> Study repository documenting preparation for the **CompTIA Cloud+ CV0-004** certification, built to demonstrate cloud engineering depth, documentation discipline and continuous learning.

---

## Purpose

This repository is more than a set of study notes. It is a **living knowledge base** that proves at a glance that I can:

- Translate dense technical specifications into clear, structured documentation.
- Reason about trade-offs (cost vs. availability, control vs. speed, security vs. usability).
- Apply cloud concepts to real cloud-provider offerings (AWS, Azure, GCP).
- Maintain professional technical documentation with version history, roadmaps and contribution standards.

It is designed to be readable by **Cloud Engineers, DevOps Engineers, Network Engineers, Infrastructure Engineers and Technical Recruiters** in under 60 seconds.

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
├── full notes/                            ← v1 notes, all 33 objectives
│   ├── Objective-1.1-*.md … Objective-6.3-*.md
│   └── exam-ready/                        ← ★ v2 EXAM-READY SET (33 / 33) ✅
│       ├── README.md                      ← Index, exam facts, revision order
│       └── Objective-1.1-*.md … Objective-6.3-*.md
│
├── objectives/                            ← Earlier structured notes
│   ├── domain-1/                          ← Cloud Architecture (23%)
│   │   ├── 1.1-cloud-service-models-and-shared-responsibility.md
│   │   ├── 1.2-service-availability-concepts.md
│   │   ├── 1.3-cloud-networking-concepts.md
│   │   ├── 1.4-storage-resources-and-technologies.md
│   │   ├── 1.5-cloud-native-design-concepts.md
│   │   ├── 1.6-containerization-concepts.md
│   │   ├── 1.7-virtualization-concepts.md
│   │   ├── 1.8-cost-considerations.md
│   │   └── 1.9-database-concepts.md
│   ├── CloudPlus_Objective_1.10_Notes.md  ← Domain 1.10 (Methods for Optimizing Workloads)
│   ├── CloudPlus_Objective_1.11_Notes.md  ← Domain 1.11 (Evolving Technologies in the Cloud)
│   ├── domain-2/                          ← Deployment (19%)
│   ├── domain-3/                          ← Operations (17%)
│   ├── domain-4/                          ← Security (19%)
│   ├── domain-5/                          ← DevOps Fundamentals (10%)
│   └── domain-6/                          ← Troubleshooting (12%)
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

## Exam Facts

Source of truth: **CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0**.

| | |
|---|---|
| Exam code | **CV0-004** (V4) |
| Number of questions | Maximum of **90** |
| Question types | Multiple-choice **and performance-based (PBQs)** |
| Length | **90 minutes** |
| Passing score | **750** (on a scale of 100–900) |
| Recommended experience | 2–3 years as a systems administrator or cloud engineer; CompTIA Network+ and Server+ or equivalent |

---

## Exam Progress Tracker

> CV0-004 has **six** domains and **33** objectives. See [`progress/exam-progress.md`](./progress/exam-progress.md) for detail.

| Domain | Topic | Weight | Objectives | Status |
|---|---|---:|:---:|:---:|
| **1.0** | Cloud Architecture | 23% | 11 (1.1 – 1.11) | ✅ Complete |
| **2.0** | Deployment | 19% | 5 (2.1 – 2.5) | ✅ Complete |
| **3.0** | Operations | 17% | 4 (3.1 – 3.4) | ✅ Complete |
| **4.0** | Security | 19% | 6 (4.1 – 4.6) | ✅ Complete |
| **5.0** | DevOps Fundamentals | 10% | 4 (5.1 – 5.4) | ✅ Complete |
| **6.0** | Troubleshooting | 12% | 3 (6.1 – 6.3) | ✅ Complete |
| | **Total** | **100%** | **33 / 33** | ✅ |
| | **Exam scheduled** | | | 🎯 TBD |

> ⚠️ **Note for anyone comparing against older material:** CV0-004 restructured the blueprint. The retired **CV0-003** had five domains (Architecture & Design 13%, Security 20%, Deployment 23%, Operations & Support 22%, Troubleshooting 22%). Domain numbers, names and weights are **not** interchangeable between the two versions.

---

## ★ Exam-Ready Note Set — [`full notes/exam-ready/`](./full%20notes/exam-ready/README.md)

A v2 rewrite of every objective, built directly against the official objectives document. The v1 notes in [`full notes/`](./full%20notes/) are preserved unchanged.

| | |
|---|---|
| Coverage | **33 / 33 objectives**, all six domains |
| Size | ~31,600 lines |
| Practice questions | **825**, each with an explanation of why every wrong answer is wrong |
| Per note | Verbatim CompTIA bullets + coverage checklist · core mental model · concepts in depth · comparison tables · exam traps · keyword triggers · 25 scenario questions · PBQ-style drills · 60-second recall sheet · cross-references |
| Diagrams | ASCII (always renders) plus native **Mermaid** |

**Start here:** the [set index](./full%20notes/exam-ready/README.md) carries the exam facts, the objective-verb table, and a suggested final-week revision order.

---

## Key Learning Areas — Domain 1.0 Cloud Architecture (23%)

> Domains 2.0 – 6.0 are covered in full by the [exam-ready set](./full%20notes/exam-ready/README.md).

### 1.1: Cloud Service Models & Shared Responsibility
IaaS, PaaS, SaaS, FaaS and the **shared responsibility model** that defines who secures what. Foundation for every cloud decision. → [notes](./objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md)

### 1.2: Service Availability
Regions, Availability Zones, edge locations, RTO/RPO, hot/warm/cold sites, cloud bursting, multicloud. → [notes](./objectives/domain-1/1.2-service-availability-concepts.md)

### 1.3: Cloud Networking
VPCs/VNets, subnets, routing, load balancers (L4/L7), DNS, CDN, VPN, Direct Connect, NAT, security groups, NACLs. → [notes](./objectives/domain-1/1.3-cloud-networking-concepts.md)

### 1.4: Storage
Block, file, object, archival. RAID, replication, snapshots, lifecycle policies, tiered storage, performance metrics (IOPS, throughput, latency). → [notes](./objectives/domain-1/1.4-storage-resources-and-technologies.md)

### 1.5: Cloud-Native Design
Microservices, 12-factor app, statelessness, elasticity, event-driven architecture, API-first design, queues, pub/sub. → [notes](./objectives/domain-1/1.5-cloud-native-design-concepts.md)

### 1.6: Containerization
Docker, container images, registries, Kubernetes (pods, deployments, services, namespaces, RBAC), Helm, serverless containers. → [notes](./objectives/domain-1/1.6-containerization-concepts.md)

### 1.7: Virtualization
Hypervisors (Type 1 / Type 2), vCPU / vRAM, NUMA, oversubscription, live migration, snapshots, resource pooling. → [notes](./objectives/domain-1/1.7-virtualization-concepts.md)

### 1.8: Cost Considerations
CapEx vs OpEx, TCO, Reserved / On-Demand / Spot, right-sizing, tagging, FinOps, chargeback / showback. → [notes](./objectives/domain-1/1.8-cost-considerations.md)

### 1.9: Database Concepts
Relational vs non-relational, OLTP vs OLAP, ACID vs BASE, replication, sharding, caching, connection pooling, managed databases. → [notes](./objectives/domain-1/1.9-database-concepts.md)

### 1.10: Methods for Optimizing Workloads
Picking the right **compute model** (VM / Container / Serverless), tuning **storage IOPS vs throughput**, **container orchestration**, **workflow** orchestration, optimizing **network latency vs throughput** and leveraging **managed services** to cut ops overhead. → [notes](./CloudPlus_Objective_1.10_Notes.md)

### 1.11: Evolving Technologies in the Cloud
The 7 **AI/ML** capabilities (text recognition, translation, visual recognition, sentiment, voice-to-text, text-to-voice, generative AI) and the 4 **IoT** components (sensors, gateways, communication, transmission protocols). → [notes](./CloudPlus_Objective_1.11_Notes.md)

---

## Sample Architecture Diagrams

All diagrams are native **Mermaid**, render directly on GitHub without extra tooling.

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

> I believe the best way to learn something is to **document it as if I were teaching it** to someone joining the team tomorrow. This repository comes from that belief.

- For **recruiters** open any note in the [exam-ready set](./full%20notes/exam-ready/README.md) — for example [6.2 Network Troubleshooting](./full%20notes/exam-ready/Objective-6.2-Network-Troubleshooting.md) — to see the depth and structure of my technical writing.
- For **hiring managers** review the [diagrams](./diagrams/) and [cheatsheets](./cheatsheets/) to assess how I synthesize and apply cloud knowledge.
- For **peers** [open an issue](../../issues) if you spot an error or [submit a PR](../../pulls) with improvements.

The repository follows GitHub best practices: semantic commit history, structured Markdown, Mermaid diagrams, a roadmap, a changelog and a security policy. Every file is meant to render correctly on GitHub's web UI.

---

## Quick Links

- [Study Guide](./docs/STUDY-GUIDE.md)
- [Exam Progress](./progress/exam-progress.md)
- [Roadmap](./ROADMAP.md)
- [Changelog](./CHANGELOG.md)
- [Contributing](./CONTRIBUTING.md)
- [Security](./SECURITY.md)

---

**Author:** CompTIA Cloud+ CV0-004 Candidate
**Last updated:** see [CHANGELOG.md](./CHANGELOG.md)
