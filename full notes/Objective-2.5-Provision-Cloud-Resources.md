# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.5: Given a set of requirements, provision the appropriate cloud resources

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Objective 2.5 Focus:** Given requirements, choose the right Storage, Compute, Network, Performance, Security, Cost, Availability, and Compliance resources.

---

## SECTION 1 — Exam Objectives

- **Objective number:** 2.5
- **Objective name:** *Given a set of requirements, provision the appropriate cloud resources.*
- **What it tests:** Given a scenario with stated needs, you select the correct cloud building blocks (Storage, Compute, Network, Performance, Security, Cost, Availability, Compliance) to meet each need.
- **The eight requirement categories (from the official objectives):**
  - **Storage requirements** — how much data, what type, how fast, how durable.
  - **Compute requirements** — how much processing power, what shape (VM, container, serverless).
  - **Network requirements** — connectivity, isolation, bandwidth, IP addressing.
  - **Performance requirements** — speed, latency, throughput, scaling headroom.
  - **Security requirements** — who can access what, encryption, network protection.
  - **Cost requirements** — stay within budget, pick the cheapest fit-for-purpose option.
  - **Availability requirements** — how much uptime / how resilient to failure.
  - **Compliance requirements** — laws and standards the solution must obey (HIPAA, PCI, GDPR, etc.).
- **Why it matters in the real world:**
  - Picking the wrong resource wastes money (oversized VMs) or causes outages (single AZ, no redundancy).
  - Every real deployment balances these eight forces against each other — e.g., max availability usually costs more; strict compliance may limit region choice.
  - The exam presents word problems ("a hospital needs to store 10 TB of patient X-rays cheaply but encrypted and auditable") and asks which services/configuration fit.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.5.1 Storage Requirements
- **Definition:** Storage requirements drive *what kind of storage* you provision — object, block, or file — plus performance tier and durability/class.
- **Why it matters:** Choosing the wrong class wastes money or fails latency/retention needs; lifecycle policies auto-move data between tiers to cut cost — a classic exam lever.
- **Analogy:** Storage is like choosing between a filing cabinet (block), a shared drive (file), and a warehouse box (object) — each fits a different way you need to reach the data.
- **Example:** "Frequent small reads" → SSD/Standard; "rare archival" → Glacier/cold tier; "shared Linux filesystem" → file storage/NFS; "VM boot disk" → block storage.

### 2.5.2 Compute Requirements
- **Definition:** Compute requirements decide *how you run code* — VM, container, or serverless — plus specialized shapes (GPU, burstable, optimized) and billing model.
- **Why it matters:** Matching workload shape to instance type and billing (on-demand, reserved, spot) controls cost and scalability; wrong choice overpays or can't scale.
- **Analogy:** Compute is like choosing a dedicated office (VM), a shared co-working desk (container), or a pay-per-minute hot desk you only rent when used (serverless).
- **Example:** Steady 24/7 load → reserved; spiky/unpredictable → autoscaling or serverless; short background jobs → functions; legacy monolith → VM.

### 2.5.3 Network Requirements
- **Definition:** Network requirements cover *how resources connect and are isolated* — VPC, subnets, route tables, gateways, load balancers, DNS, firewalls, VPN/Direct Connect, CDN.
- **Why it matters:** Public vs private subnet and stateful security groups vs stateless NACLs determine exposure and traffic flow; misconfig exposes resources or breaks connectivity.
- **Analogy:** Network is the building's layout — private rooms (private subnets), front door (IGW), hallway signs (route tables), and a reception desk (load balancer) directing visitors.
- **Example:** "Low latency worldwide" → CDN + multi-region; "no public IPs" → private subnets + NAT; "hybrid cloud" → VPN/Direct Connect.

### 2.5.4 Performance Requirements
- **Definition:** Performance requirements specify *speed and capacity targets* — latency, throughput, IOPS, and headroom for growth.
- **Why it matters:** More performance usually costs more, so the skill is meeting the *required* target without over-provisioning; right-sizing and autoscaling hit the target efficiently.
- **Analogy:** Performance is like the number of checkout lanes — too few and lines form (latency), too many and you pay idle staff (over-provisioning).
- **Example:** "Consistent low-latency database" → provisioned IOPS SSD; "handles Black Friday spikes" → autoscaling group behind a load balancer.

### 2.5.5 Security Requirements
- **Definition:** Security requirements define *who can do what and how data is protected* — IAM, encryption, network security, secrets management, logging/audit.
- **Why it matters:** A "secure by default" answer (encryption on, public access blocked, least-privilege IAM) almost always beats an open convenience option on the exam.
- **Analogy:** Security is like locks, a guest list, and a security camera — only the right people get in, their actions are logged, and the door is never left open.
- **Example:** Lock down default deny, encrypt buckets, enable MFA on sensitive roles, scope permissions narrowly; audit with CloudTrail/flow logs.

### 2.5.6 Cost Requirements
- **Definition:** Cost requirements mean *staying within budget while still meeting the other needs* — right-sizing, billing model, cheaper tiers, terminating idle resources.
- **Why it matters:** Higher availability and performance cost more; the exam loves "cheapest option that still meets the requirement" as the correct answer.
- **Analogy:** Cost is like grocery shopping on a list — buy the store brand that does the job (Infrequent Access, Spot) instead of the premium always-on option you don't need.
- **Example:** Infrequent Access for rarely read backups, Spot for stateless render jobs, reserved instances for a 24/7 database.

### 2.5.7 Availability Requirements
- **Definition:** Availability requirements state *how resilient to failure the system must be* (often uptime %, e.g., 99.9% vs 99.99%) — multi-AZ, multi-region, LB, autoscaling, backups.
- **Why it matters:** The more nines, the more redundant (and expensive) the design; RTO/RPO tie directly to how you architect recovery.
- **Analogy:** Availability is like having a backup generator and a second site — if one power source fails, the lights stay on.
- **Example:** "Must survive AZ failure" → multi-AZ; "global users, no downtime during region outage" → multi-region + Route 53 failover; "single VM is fine" → lowest cost, lowest availability.

### 2.5.8 Compliance Requirements
- **Definition:** Compliance requirements are *legal/regulatory or industry-standard obligations* the solution must satisfy — HIPAA, PCI-DSS, GDPR, SOC 2, FedRAMP, data-residency laws.
- **Why it matters:** Compliance shapes every other choice — it may force a region, demand encryption + audit logging, forbid public exposure, or require provider certifications.
- **Analogy:** Compliance is like building-code law — the provider supplies a code-compliant building, but you must wire it and use it compliantly (encrypt, restrict, log).
- **Example:** "Patient records" / "credit cards" / "EU citizens' data" → compliance-driven design (encryption, audit trails, region lock).

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **E-commerce site for Black Friday.** Requirements: high performance + high availability + cost control. Solution: Auto Scaling group of EC2 behind an Application Load Balancer across 3 AZs, RDS Multi-AZ for the database, CloudFront CDN at the edge, and Spot instances for batch image-processing — scales for the spike, survives AZ failure, controls cost.
- **Hospital storing 10 TB of patient X-rays.** Requirements: storage + compliance (HIPAA) + security + cost. Solution: S3 with encryption-at-rest (KMS), S3 Object Lock for immutability, lifecycle policy to Glacier for cold archives, strict IAM bucket policies, and CloudTrail audit logging — meets HIPAA while keeping archival cheap.
- **Startup API with unpredictable traffic.** Requirements: compute + cost + performance. Solution: AWS Lambda (serverless) behind API Gateway — pays only per request, scales to zero when idle, no servers to patch; CloudWatch for performance monitoring. Avoids paying for idle always-on VMs.
- **Multinational company with EU data-residency law.** Requirements: compliance (GDPR) + network + security. Solution: VPC in the EU region only, no cross-region replication of personal data, private subnets, VPN to on-prem, and IAM scoped to EU. Data never leaves the allowed geography.
- **Financial app needing 99.99% uptime.** Requirements: availability + security + compliance (PCI-DSS). Solution: Multi-region active-passive with Route 53 failover, Multi-AZ RDS, encryption everywhere, WAF + security groups, and daily backups with tested restore — survives region failure and passes PCI audit.

---

## SECTION 4 — COMPARISON TABLES

| Requirement | Drives the choice of… | Example decision |
|---|---|---|
| Storage | Type & tier (object/block/file, hot/cold) | Rare backups → Glacier; VM disk → EBS SSD |
| Compute | Shape (VM/container/serverless) & size | Spiky jobs → Lambda; steady DB → reserved VM |
| Network | Isolation, connectivity, edge | Global users → CDN + multi-region VPC |

| Compute model | Best for | Cost profile |
|---|---|---|
| Virtual Machines | Full control, legacy apps | Pay for uptime (reserved saves $) |
| Containers | Microservices, portability | Efficient, shared OS |
| Serverless / Functions | Event-driven, spiky, short tasks | Pay per invocation, scales to zero |

| Availability target | Design approach | Relative cost |
|---|---|---|
| Single instance | One VM, one AZ | Lowest |
| Multi-AZ | Duplicate across AZs + LB | Medium |
| Multi-region | Active/passive across regions | Highest |

| Storage class | Access pattern | Cost |
|---|---|---|
| Standard / SSD | Frequent, low-latency | Highest |
| Infrequent Access | Occasional | Lower |
| Archive (Glacier) | Rare, long-term | Lowest |

| Compliance law | Triggers | Typical control |
|---|---|---|
| HIPAA | Health data | Encryption + audit + access control |
| PCI-DSS | Card payments | Network segmentation + logging |
| GDPR | EU personal data | Data residency + consent + erasure |

| Cost lever | What it does | Trade-off |
|---|---|---|
| Right-sizing | Match capacity to need | None if done well |
| Reserved instances | Discount for commitment | Less flexibility |
| Spot instances | Cheap idle capacity | Can be interrupted |

---

## SECTION 5 — AWS PROVIDER MAPPING

**Objective 2.5 → AWS services for provisioning by requirement:**

- **Storage** — **S3** (object storage, with Standard / IA / Glacier tiers + lifecycle policies), **EBS** (block volumes for EC2), **EFS** (shared file storage / NFS). Choose class by access frequency and durability need.
- **Compute** — **EC2** (virtual machines, many instance families: general, compute-, memory-, GPU-, burstable), **ECS / EKS** (containers), **Lambda** (serverless functions), **Fargate** (serverless containers). Billing: On-Demand, Reserved, Spot.
- **Network** — **VPC** (isolated network), **Subnets** (public/private), **Internet Gateway / NAT Gateway**, **ALB/NLB** (load balancing), **Route 53** (DNS + failover), **CloudFront** (CDN), **VPN / Direct Connect** (hybrid), **Security Groups / NACLs** (traffic filtering).
- **Performance** — **Auto Scaling Groups** (handle spikes), **CloudWatch** (metrics), **Provisioned IOPS (io2/io1) EBS** (low-latency DB), **Elastic Load Balancing**, **Global Accelerator** (edge performance).
- **Security** — **IAM** (least-privilege roles/policies), **KMS** (encryption keys), **Secrets Manager / Parameter Store** (secrets), **WAF** (web firewall), **CloudTrail** (audit), **VPC Flow Logs**.
- **Cost** — **AWS Cost Explorer / Budgets** (tracking), **Savings Plans / Reserved Instances** (discounts), **Spot Instances** (cheap capacity), **S3 lifecycle** (tier down cold data).
- **Availability** — **Multi-AZ** (RDS, Auto Scaling), **Multi-region + Route 53 failover**, **Elastic Load Balancing + health checks**, **AWS Backup** (RPO/RTO), provider SLAs.
- **Compliance** — **AWS Artifact** (reports/certifications), **AWS Config** (continuous compliance evaluation), **KMS + CloudTrail** (encrypt + audit), **Region selection** for data residency (GDPR), service scopes for HIPAA / PCI-DSS eligibility.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A workload runs only a few seconds per day and is unpredictable. Best compute choice?
   A. Reserved EC2  B. Lambda (serverless)  C. Always-on VM  D. GPU instance

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Lambda (serverless) scales to zero and bills per invocation — ideal for short, unpredictable runs.
> **Why A is wrong:** Reserved EC2 is for steady 24/7 load, not rare unpredictable runs.
> **Why C is wrong:** Always-on VM bills continuously even when idle.
> **Why D is wrong:** GPU instances are for specialized compute, not short spiky jobs.

2. 10 TB of rarely accessed backups should use which storage class?
   A. S3 Standard  B. EBS SSD  C. S3 Glacier  D. EFS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** S3 Glacier is the cold/archive tier, cheapest for rarely accessed backups.
> **Why A is wrong:** S3 Standard is for frequent access — too costly for rare backups.
> **Why B is wrong:** EBS SSD is block storage for running instances, not bulk backup archive.
> **Why D is wrong:** EFS is shared file storage, not a cold archive tier.

3. To survive a single data-center failure, deploy across:
   A. One AZ  B. Multiple AZs  C. One region only  D. One subnet

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Multi-AZ design tolerates an Availability Zone outage (a DC failure).
> **Why A is wrong:** One AZ fails entirely if that DC fails.
> **Why C is wrong:** One region can still be a single AZ — region ≠ AZ redundancy.
> **Why D is wrong:** One subnet is within a single AZ, so no failure tolerance.

4. Encrypting data at rest on S3 is best done with:
   A. IAM roles  B. KMS keys  C. Security groups  D. NAT Gateway

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** KMS provides managed encryption keys for S3 data at rest.
> **Why A is wrong:** IAM controls access, not encryption at rest.
> **Why C is wrong:** Security groups filter traffic, not encrypt data.
> **Why D is wrong:** NAT Gateway provides outbound internet, not encryption.

5. A hospital storing patient records must follow:
   A. GDPR  B. HIPAA  C. PCI-DSS  D. FedRAMP

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** HIPAA governs protected health information (patient records) in the US.
> **Why A is wrong:** GDPR governs EU personal data, not specifically health records.
> **Why C is wrong:** PCI-DSS governs cardholder/payment data, not health.
> **Why D is wrong:** FedRAMP governs US federal cloud authorization, not health records specifically.

6. EU personal data that must not leave the EU reflects a:
   A. Performance requirement  B. Compliance/data-residency requirement  C. Cost requirement  D. Network bandwidth

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** GDPR data-residency is a compliance constraint restricting where data may physically reside.
> **Why A is wrong:** Performance is about speed/latency, not data location law.
> **Why C is wrong:** Cost is budget, not a legal residency rule.
> **Why D is wrong:** Network bandwidth is throughput, not data-residency law.

7. For steady 24/7 database load, cheapest committed compute is:
   A. On-Demand  B. Spot  C. Reserved Instances  D. Lambda

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Reserved Instances / Savings Plans discount steady-state usage over a commitment term.
> **Why A is wrong:** On-Demand has no discount for steady 24/7 use.
> **Why B is wrong:** Spot can be interrupted — unsuitable for a steady DB.
> **Why D is wrong:** Lambda isn't for a steady always-on database host.

8. Distributing traffic across instances and enabling health checks uses a:
   A. NAT Gateway  B. Load Balancer  C. VPN  D. Subnet

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Load balancers (ALB/NLB) spread traffic across targets and perform health checks.
> **Why A is wrong:** NAT Gateway provides outbound internet, not traffic distribution.
> **Why C is wrong:** VPN is a secure tunnel, not a traffic distributor.
> **Why D is wrong:** A subnet is a network segment, not a distribution mechanism.

9. Public vs private subnets, route tables, and IGW are part of:
   A. IAM  B. VPC networking  C. S3  D. Lambda

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Subnets, route tables, and Internet Gateways are core VPC networking constructs.
> **Why A is wrong:** IAM is identity/access, not network topology.
> **Why C is wrong:** S3 is object storage, unrelated to VPC subnets.
> **Why D is wrong:** Lambda is compute, not networking.

10. Global users needing low latency should add a:
    A. Single AZ  B. CDN (CloudFront)  C. NAT Gateway  D. Reserved instance

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A CDN (CloudFront) caches content at edge locations near users, cutting global latency.
> **Why A is wrong:** Single AZ increases latency for distant users.
> **Why C is wrong:** NAT Gateway is for outbound internet, not latency optimization.
> **Why D is wrong:** Reserved instance is a billing discount, not a latency tool.

11. Fault-tolerant batch rendering that can be interrupted suits:
    A. On-Demand  B. Spot Instances  C. Reserved  D. Multi-AZ RDS

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Spot Instances offer deep discounts for fault-tolerant, interruptible work like batch rendering.
> **Why A is wrong:** On-Demand costs more with no interruption tolerance needed.
> **Why C is wrong:** Reserved is for steady committed use, not interruptible batch.
> **Why D is wrong:** Multi-AZ RDS is a database HA option, not for batch rendering.

12. Stateful, instance-level firewall in AWS is a:
    A. NACL  B. Security Group  C. Route Table  D. IAM Policy

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Security Groups are stateful and evaluated at the instance/ENI level.
> **Why A is wrong:** NACLs are stateless (and subnet-level), the opposite attribute.
> **Why C is wrong:** Route Tables direct traffic, not firewall it.
> **Why D is wrong:** IAM Policy governs API/identity access, not network firewalling.

13. Continuous evaluation of resource compliance is provided by:
    A. CloudTrail  B. AWS Config  C. S3  D. EC2

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** AWS Config continuously evaluates and records resource compliance against rules.
> **Why A is wrong:** CloudTrail logs API activity (audit), not continuous config compliance.
> **Why C is wrong:** S3 is storage, not a compliance evaluator.
> **Why D is wrong:** EC2 is compute, not compliance evaluation.

14. RPO/RTO are most directly tied to which requirement?
    A. Cost  B. Availability  C. Network  D. Compute

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** RPO/RTO define recovery objectives — the core of availability/resilience design.
> **Why A is wrong:** Cost constrains the design but isn't what RPO/RTO measure.
> **Why C is wrong:** Network is one enabler, not the requirement RPO/RTO define.
> **Why D is wrong:** Compute is a building block, not the recovery-objective requirement.

15. Shared Linux filesystem across many servers calls for:
    A. EBS  B. EFS (file storage)  C. S3  D. Glacier

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** EFS is shared NFS-style file storage accessible concurrently by many servers.
> **Why A is wrong:** EBS is block storage attached to a single instance.
> **Why C is wrong:** S3 is object storage, not a mounted shared filesystem.
> **Why D is wrong:** Glacier is cold archive, not a live shared filesystem.

16. Low-latency, consistent database I/O is best met with:
    A. Standard S3  B. Provisioned IOPS EBS  C. Glacier  D. EFS IA

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Provisioned IOPS (io1/io2) EBS delivers consistent low-latency database performance.
> **Why A is wrong:** S3 is object storage with higher latency, not a DB volume.
> **Why C is wrong:** Glacier is cold archive, far too slow for a database.
> **Why D is wrong:** EFS IA is infrequent-access file storage, not low-latency block I/O.

17. Credit-card payment processing triggers which compliance set?
    A. HIPAA  B. PCI-DSS  C. GDPR  D. SOC 1

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** PCI-DSS governs cardholder-data environments (credit-card processing).
> **Why A is wrong:** HIPAA governs health data, not payments.
> **Why C is wrong:** GDPR governs EU personal data, not specifically payments.
> **Why D is wrong:** SOC 1 is about service-org controls, not card data specifically.

18. Handling Black Friday traffic spikes is best met with:
    A. Fixed single VM  B. Auto Scaling + ALB  C. One AZ  D. Manual scaling

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Auto Scaling groups behind an ALB absorb spikes resiliently and scale back down.
> **Why A is wrong:** A fixed single VM can't handle a spike and is a single point of failure.
> **Why C is wrong:** One AZ lacks resilience for peak events.
> **Why D is wrong:** Manual scaling is too slow to react to sudden spikes.

19. Auditing "who changed what" in AWS is done via:
    A. CloudTrail  B. CloudFront  C. EBS  D. NAT

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** CloudTrail logs all API activity, providing the "who changed what, when" audit trail.
> **Why B is wrong:** CloudFront is a CDN, not an audit log.
> **Why C is wrong:** EBS is block storage, not auditing.
> **Why D is wrong:** NAT is network address translation, not audit logging.

20. The cheapest storage tier for frequently read hot data is NOT:
    A. S3 Standard  B. EBS SSD  C. Glacier  D. EFS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Glacier is the cold/archive tier — not for frequently read hot data; it is the correct "NOT."
> **Why A is wrong:** S3 Standard is the hot/frequent-access tier.
> **Why B is wrong:** EBS SSD is high-performance block storage for active workloads.
> **Why D is wrong:** EFS is active shared file storage, not cold archive.
