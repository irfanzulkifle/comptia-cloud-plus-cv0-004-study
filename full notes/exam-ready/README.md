# Exam-Ready Notes — CompTIA Cloud+ CV0-004

Enhanced, exam-focused rewrite of the notes in [`../`](../). The originals are untouched — this folder is the **v2 study set**.

**Source of truth:** CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0.

## What "exam-ready" means here

Every file follows the same section *sequence* so you can study, drill, and revise the same way for every objective. The count flexes with the objective's size — larger objectives split "concepts in depth" across several sections (1.3 has 13, most have 12):

| Section | Purpose |
|---|---|
| 0. How to use this note | Study passes and timings |
| 1. Official objective coverage | Verbatim CompTIA bullets + self-check checklist |
| 2. Core mental model | The one diagram that carries the objective |
| 3. Concepts in depth | Definition · you manage · provider manages · billing · best for · limitations · exam triggers · real products |
| 4. Deep-dive on the hardest sub-topic | Matrices and decision flows |
| 5. Adjacent terms | Acronyms from CompTIA's list that show up as distractors |
| 6. Comparison tables | At-a-glance, clue→answer, multi-cloud mapping |
| 7. Exam traps | Distractor patterns and the truth |
| 8. Keyword triggers | Fast keyword → answer drill |
| 9. Practice questions | 25 scenario MCQs with *why each wrong answer is wrong* |
| 10. PBQ-style drills | Matrix completion, matching, ordering |
| 11. 60-second recall sheet | Exam-eve revision block |
| 12. Cross-references | Links to related objectives |

Diagrams are ASCII (always renders) plus **Mermaid** (renders natively on GitHub).

## Exam facts

| | |
|---|---|
| Exam code | CV0-004 V4 |
| Questions | Maximum of 90 |
| Types | Multiple-choice **and performance-based** |
| Length | 90 minutes |
| Passing score | 750 |
| Recommended experience | 2–3 years as a sysadmin or cloud engineer; Network+ and Server+ or equivalent |

| Domain | Weight |
|---|---:|
| 1.0 Cloud Architecture | 23% |
| 2.0 Deployment | 19% |
| 3.0 Operations | 17% |
| 4.0 Security | 19% |
| 5.0 DevOps Fundamentals | 10% |
| 6.0 Troubleshooting | 12% |

## Progress

### Domain 1.0 — Cloud Architecture (23%)

| Obj | Title | Status |
|---|---|---|
| 1.1 | [Cloud service models & shared responsibility](./Objective-1.1-Cloud-Service-Models.md) | ✅ Done |
| 1.2 | [Service availability](./Objective-1.2-Service-Availability.md) | ✅ Done |
| 1.3 | [Cloud networking](./Objective-1.3-Cloud-Networking.md) | ✅ Done |
| 1.4 | [Storage resources & technologies](./Objective-1.4-Storage-Resources.md) | ✅ Done |
| 1.5 | [Cloud-native design](./Objective-1.5-Cloud-Native-Design.md) | ✅ Done |
| 1.6 | [Containerization](./Objective-1.6-Containerization.md) | ✅ Done |
| 1.7 | [Virtualization](./Objective-1.7-Virtualization.md) | ✅ Done |
| 1.8 | [Cost considerations](./Objective-1.8-Cost-Considerations.md) | ✅ Done |
| 1.9 | [Database concepts](./Objective-1.9-Database-Concepts.md) | ✅ Done |
| 1.10 | [Optimizing workloads](./Objective-1.10-Optimizing-Workloads.md) | ✅ Done |
| 1.11 | [Evolving technologies (AI/ML, IoT)](./Objective-1.11-Evolving-Technologies.md) | ✅ Done |

**Domain 1.0 complete — 11 / 11 objectives.**

### Domain 2.0 — Deployment (19%)

| Obj | Title | Status |
|---|---|---|
| 2.1 | [Cloud deployment models](./Objective-2.1-Cloud-Deployment-Models.md) | ✅ Done |
| 2.2 | [Deployment strategies](./Objective-2.2-Deployment-Strategies.md) | ✅ Done |
| 2.3 | [Cloud migration](./Objective-2.3-Cloud-Migration.md) | ✅ Done |
| 2.4 | [Code, deploy & configure](./Objective-2.4-Code-Deploy-Configure.md) | ✅ Done |
| 2.5 | [Provision cloud resources](./Objective-2.5-Provision-Cloud-Resources.md) | ✅ Done |

**Domain 2.0 complete — 5 / 5 objectives.**

### Domain 3.0 — Operations (17%)

| Obj | Title | Status |
|---|---|---|
| 3.1 | [Observability](./Objective-3.1-Observability.md) | ✅ Done |
| 3.2 | [Scaling approaches](./Objective-3.2-Scaling-Approaches.md) | ✅ Done |
| 3.3 | [Backup and recovery](./Objective-3.3-Backup-Recovery.md) | ✅ Done |
| 3.4 | [Resource life cycle](./Objective-3.4-Resource-Lifecycle.md) | ✅ Done |

**Domain 3.0 complete — 4 / 4 objectives.**

### Domain 4.0 — Security (19%)

| Obj | Title | Status |
|---|---|---|
| 4.1 | [Vulnerability management](./Objective-4.1-Vulnerability-Management.md) | ✅ Done |
| 4.2 | [Compliance and regulation](./Objective-4.2-Compliance-Regulation.md) | ✅ Done |
| 4.3 | [Identity and access management](./Objective-4.3-Identity-Access-Management.md) | ✅ Done |
| 4.4 | [Security best practices](./Objective-4.4-Security-Best-Practices.md) | ✅ Done |
| 4.5 | [Security controls](./Objective-4.5-Security-Controls.md) | ✅ Done |
| 4.6 | Monitor suspicious activities | ⬜ |

### Domains 5.0 – 6.0

⬜ Not started — see [`../`](../) for the v1 notes covering 5.1 – 6.3.

| Domain | Objectives | Weight | Status |
|---|---:|---:|---|
| 5.0 DevOps Fundamentals | 4 | 10% | ⬜ |
| 6.0 Troubleshooting | 3 | 12% | ⬜ |

---

## The five objective verbs in Domain 1

CompTIA's verb tells you how deeply to study each objective. Calibrate accordingly:

| Verb | Objectives | Depth demanded |
|---|---|---|
| "Given a scenario" | 1.1 | Apply judgement to a situation |
| "Explain" | 1.2, 1.3, 1.5, 1.9 | Precise definitions and mechanisms |
| "Compare and contrast" | 1.4, 1.6, 1.7, 1.10 | Differences along specific axes — study the contrast tables |
| "Summarize" | 1.8 | Describe at a high level |
| "Identify" | 1.11 | Recognise the technology and what it does |
