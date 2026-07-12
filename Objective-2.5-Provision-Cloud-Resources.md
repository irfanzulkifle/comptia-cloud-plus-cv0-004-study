---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 2.5: Provision Cloud Resources"
aliases: [Objective-2.5-Provision-Cloud-Resources, Domain-2.5-Provision-Cloud-Resources, Resource-Provisioning, Requirements-Mapping]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 2.5
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-2.5, provisioning, requirements, storage, compute, network, performance, security, cost, availability, compliance, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.5: Given a Set of Requirements, Provision the Appropriate Cloud Resources

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 2.0 Deployment Domain = 19% of total exam
> **Objective 2.5 Focus:** Translating a requirements list into the correct cloud resources — weighing Storage, Compute, Network, Performance, Security, Cost, Availability, and Compliance constraints to pick the right services and configurations.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 2.5
### Objective Name: *Given a set of requirements, provision the appropriate cloud resources.*

**What this objective means (beginner-friendly):**

Imagine you're a personal shopper. The customer hands you a list: "I need something that holds 10 TB, loads in under 1 second, only I can open it, costs under $200/month, never goes down, and complies with banking rules." Your job is to pick the **exact right item** from the cloud catalogue — not the biggest, not the cheapest, but the one that fits *all* the requirements.

That's 2.5. You're given a scenario with several constraints (storage, compute, network, performance, security, cost, availability, compliance) and you must provision the matching resources. It's the capstone of Domain 2 — you migrate (2.3), deploy with code (2.4), then **provision the right stuff** (2.5).

**Why it matters in the real world:**
- Picking the wrong resource wastes money (oversized) or causes outages (undersized), or breaks compliance (unencrypted, wrong region).
- Every cloud bill, incident, and audit traces back to a provisioning decision. Getting requirements → resources right is the core cloud admin skill.
- Trade-offs are constant: cheaper usually means less availability; more secure sometimes means more cost/latency. You balance them.

**Why CompTIA tests it:**
- 2.5 is the **scenario-judgment** objective — the exam throws a requirements paragraph and asks "which resource/config fits?" It pulls together everything from 1.0–2.4.
- The eight requirement categories are explicitly listed — expect at least one question mapping a requirement to a resource choice.
- It's the last Deployment objective; finishing Domain 2 means you can take a workload from "idea" to "running, right-sized, compliant resource."

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.5.1 — STORAGE REQUIREMENTS

**What it covers:** Capacity, type (object/block/file), performance tier (hot/cold/archive), durability, IOPS/throughput, redundancy, encryption.

**How to map to resources:**
- **Unstructured, massive, web-served** → Object storage (S3 / Blob / Cloud Storage).
- **Database or OS disk needing low latency** → Block storage (EBS / Disk / Persistent Disk), SSD-backed for IOPS.
- **Shared file system (many servers)** → File storage (EFS / Azure Files / Filestore), NFS/SMB.
- **Rarely accessed, keep for years** → Archive/cold tier (Glacier / Blob Archive / Archive class).
- **High IOPS DB** → Provisioned IOPS (io2 / Ultra Disk / Extreme PD).

**Exam angle:** "10 TB video, streamed to customers, rarely edited" → object storage standard; "transaction DB, <1 ms, 10k IOPS" → block SSD + provisioned IOPS.

### 2.5.2 — COMPUTE REQUIREMENTS

**What it covers:** vCPU, RAM, GPU, instance family, auto-scaling, serverless vs VM vs container, right-sizing.

**How to map:**
- **Steady general app** → Fixed VM / instance (right-sized).
- **Variable/spiky load** → Auto-scaling group + load balancer.
- **Event-driven, short tasks** → Serverless (Lambda / Functions / Cloud Run).
- **Containers / microservices** → Managed K8s (EKS / AKS / GKE) or container service.
- **ML / rendering** → GPU instances.
- **Batch / scheduled** → Spot/preemptible or batch service.

**Exam angle:** "Unpredictable traffic, must not over-provision" → auto-scaling; "runs 100 ms on demand, millions/day" → serverless.

### 2.5.3 — NETWORK REQUIREMENTS

**What it covers:** Bandwidth, latency, VPN/Direct Connect, public vs private, DNS, firewall/security groups, subnet design, CDN, hybrid connectivity.

**How to map:**
- **Private internal traffic only** → Private subnets, no public IP, security groups/NSG.
- **Low-latency on-prem link** → VPN or dedicated interconnect (Direct Connect / ExpressRoute / Interconnect).
- **Global low-latency users** → CDN (CloudFront / Front Door / CDN) + global LB.
- **Public web app** → Public subnet + ALB + WAF.
- **Many VPCs/accounts** → VPC peering / transit gateway / hub-spoke.

**Exam angle:** "Users worldwide, static assets" → CDN; "secure link to on-prem data centre" → VPN/Direct Connect; "no internet exposure" → private subnet + NAT only for egress.


---

### 2.5.4 — PERFORMANCE REQUIREMENTS

**What it covers:** Latency, throughput, IOPS, response time, scalability headroom, caching.

**How to map:**
- **Low DB latency** → SSD block + provisioned IOPS, in-memory cache (ElastiCache / Redis / Memorystore).
- **High read throughput** → Read replicas, CDN for static, caching layer.
- **Compute-bound** → More/faster vCPU or GPU.
- **Need headroom** → Auto-scaling + load testing baseline.

**Exam angle:** "Page must load <100 ms globally" → CDN + cache; "DB under heavy reads" → read replica + Redis.

### 2.5.5 — SECURITY REQUIREMENTS

**What it covers:** Encryption (at rest/in transit), IAM/least privilege, network isolation, firewalls, secrets management, WAF, audit logging.

**How to map:**
- **Encrypt data** → SSE (KMS/Key Vault/Cloud KMS); TLS for transit.
- **Control access** → IAM roles/policies, least privilege, MFA.
- **Isolate** → Private subnets, security groups/NSG, private endpoints.
- **Protect web app** → WAF (AWS WAF / App Gateway WAF / Cloud Armor).
- **Secrets** → Secrets Manager / Key Vault / Secret Manager (not hard-coded).

**Exam angle:** "Only authorised users, encrypted, audited" → IAM + KMS encryption + CloudTrail/audit logs + private subnet.

### 2.5.6 — COST REQUIREMENTS

**What it covers:** Budget caps, pricing model (on-demand/reserved/spot), egress fees, right-sizing, cost allocation tags.

**How to map:**
- **Predictable 24/7** → Reserved / Savings Plan / committed use (cheaper than on-demand).
- **Fault-tolerant batch** → Spot / preemptible.
- **Unknown/spiky** → On-demand + auto-scale down.
- **Cut storage cost** → Lifecycle policy to tier cold data to archive.
- **Track spend by team** → Cost allocation tags / billing buckets.

**Exam angle:** "Steady workload, minimise cost" → Reserved; "cheap, can be interrupted" → Spot; "avoid egress surprise" → keep traffic in-region / use CDN.

### 2.5.7 — AVAILABILITY REQUIREMENTS

**What it covers:** Uptime target (SLA), redundancy, multi-AZ/region, failover, RTO/RPO, backups.

**How to map:**
- **High availability** → Multi-AZ (2+ zones), load balancer, auto-scaling.
- **Disaster recovery** → Multi-region + backup/replication meeting RTO/RPO.
- **No SPOF** → Redundant components, health checks, failover.
- **Database HA** → Multi-AZ DB, read replicas.

**Exam angle:** "99.99% uptime, no downtime on AZ failure" → Multi-AZ + LB; "RTO 1h, RPO 5m" → snapshot/backup cadence + cross-region replica.

### 2.5.8 — COMPLIANCE REQUIREMENTS

**What it covers:** Industry/regulatory standards (PCI-DSS, HIPAA, SOC 2, ISO 27001, FedRAMP, GDPR/PDPA), data residency, auditability.

**How to map:**
- **Regulated data** → In-region storage (data residency), encryption, access controls, audit logs.
- **Certified services** → Choose provider region/service with the required attestation.
- **Auditable** → Enable logging/audit trail, config compliance scanning.
- **Retention** → Archive tier with retention lock (WORM) for legal hold.

**Exam angle:** "Banking, data must stay in-country, PCI" → in-region + encrypted + PCI-compliant services + audit logs; may block certain regions (ties to 2.3 regulatory).

> **The 2.5 decision loop:** Read ALL requirements → find the strictest constraint (often compliance/availability) → pick resources satisfying it → verify the others still fit (cost/performance) → right-size. Never optimise one dimension (e.g., cheapest) at the expense of a hard constraint (compliance/availability).


---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — E-commerce site (multi-requirement)
**Requirements:** Global users, <100 ms, peak sales spikes, PCI data, 99.99% uptime, budget-conscious.
- **Compute:** Auto-scaling group behind ALB (handles spikes); containerised services on EKS/AKS/GKE.
- **Storage:** Product images → object (S3/Blob/Storage) + CDN; catalog DB → managed DB Multi-AZ.
- **Network:** CDN for static, WAF on ALB, private subnets for DB, VPN/Direct to on-prem.
- **Security:** TLS + encryption at rest (KMS/Key Vault), IAM least-privilege, WAF, secrets manager.
- **Availability:** Multi-AZ, read replicas, automated backups.
- **Compliance:** PCI-compliant services, in-region, audit logs.
- **Cost:** Reserved for baseline, on-demand/scale for peaks, lifecycle to archive old logs.

### 3.2 — Healthcare records (compliance-heavy)
**Requirements:** Patient data, HIPAA-like, in-country, encrypted, auditable, DR RTO 4h.
- **Storage:** Encrypted object + archive with retention lock (WORM) for legal hold.
- **Compute:** Private-subnet VMs, no public IP; bastion/jump for admin.
- **Network:** Private endpoints, no internet ingress; VPN to clinic sites.
- **Security:** KMS encryption, IAM, audit logging (CloudTrail/Activity Log/audit logs).
- **Availability:** Multi-AZ DB, cross-region backup for DR.
- **Compliance:** In-region only (regulatory), attestation present, retention policy.

### 3.3 — Data lake / analytics (storage + performance)
**Requirements:** 500 TB, rarely queried cold data + hot recent, low query latency on recent, cheap overall.
- **Storage:** Hot recent → standard object; cold → Glacier/Archive class via lifecycle; structured → warehouse (Redshift/Synapse/BigQuery).
- **Compute:** Query engine serverless (Athena/Serverless SQL/BigQuery); batch ETL on spot.
- **Cost:** Lifecycle tiering + spot ETL keeps it cheap; cost-allocation tags per team.

### 3.4 — Mobile app backend (spiky + cost)
**Requirements:** Millions of short requests/day, unpredictable, low cost, global.
- **Compute:** Serverless (Lambda/Functions/Cloud Run) — pay per invoke, auto-scales to zero.
- **Storage:** Object for assets + managed NoSQL (DynamoDB/Cosmos/Bigtable) for session.
- **Network:** Global CDN + API gateway.
- **Cost:** Serverless + on-demand = near-zero idle cost.

### 3.5 — HPC / ML training (compute + performance)
**Requirements:** GPU-heavy, weeks of batch, fault-tolerant, cheap.
- **Compute:** GPU instances / spot (preemptible) for interruptible training.
- **Storage:** High-throughput parallel file system or fast object + cache.
- **Network:** High-bandwidth intra-cluster (placement groups / proximity).
- **Cost:** Spot/preemptible + termination-tolerant job = large saving.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Storage Type by Requirement

| Need | Pick | Why |
|---|---|---|
| Unstructured, web-served, massive | Object (S3/Blob/Storage) | Scales infinitely, cheap, HTTP-served |
| DB / OS disk, low latency | Block SSD (EBS/Disk/PD) | Low-latency random I/O |
| Shared by many servers | File (EFS/Files/Filestore) | NFS/SMB, concurrent mount |
| Rarely accessed, keep years | Archive (Glacier/Archive class) | Lowest cost |
| High IOPS transaction DB | Block + provisioned IOPS | Sustained low-latency throughput |

### 4.2 — Compute Type by Requirement

| Need | Pick | Why |
|---|---|---|
| Steady general app | Fixed VM/instance | Predictable, simple |
| Spiky/unpredictable | Auto-scaling + LB | No over-provision, handles peaks |
| Short event tasks | Serverless (Lambda/Functions/Run) | Pay per use, scales to zero |
| Microservices | Managed K8s (EKS/AKS/GKE) | Orchestration, scaling |
| ML/render | GPU instance | Parallel math |
| Batch, interruptible | Spot/preemptible | Up to 90% cheaper |

### 4.3 — Cost Model by Requirement

| Pattern | Model | Why |
|---|---|---|
| Predictable 24/7 | Reserved / Savings / Committed | Cheapest steady-state |
| Spiky / unknown | On-demand + autoscale | Pay only what you use |
| Fault-tolerant batch | Spot / preemptible | Huge discount, can be killed |
| Cold data | Lifecycle → archive | Cut storage cost |
| Cross-team billing | Cost allocation tags | Attribute spend |

### 4.4 — Availability Tiers

| Target | Provisioning |
|---|---|
| Single instance OK | One AZ, no redundancy |
| 99.9%+ | Multi-AZ + LB + autoscale |
| 99.99% / DR | Multi-region + backup/replica meeting RTO/RPO |
| No data loss (RPO~0) | Synchronous replica / multi-AZ DB |

### 4.5 — Requirement → Resource Cheat Table

| Requirement | Drives which resource choice |
|---|---|
| Storage | Type (object/block/file), tier, IOPS |
| Compute | vCPU/RAM/GPU, scaling model |
| Network | Public/private, VPN/Direct, CDN, firewall |
| Performance | SSD/IOPS, cache, read replica, CDN |
| Security | Encryption, IAM, WAF, private subnet, secrets |
| Cost | Reserved/Spot, lifecycle, right-sizing, tags |
| Availability | Multi-AZ/region, LB, autoscale, backups |
| Compliance | In-region, certified service, audit logs, retention |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — Optimise cost, ignore compliance.**
Question: "Cheapest region has the compute we need."
Correct: If data must stay in-country (regulatory), the cheap region is **disallowed** — compliance beats cost.
Why wrong: Picking cheapest violates the harder constraint.

**Trap #2 — Object storage for a database.**
Question: "Low-latency transactional DB need."
Correct: **Block storage** (EBS/Disk/PD), not object. Object is for unstructured, not random I/O DB.
Why wrong: Object storage can't serve as a low-latency DB disk.

**Trap #3 — Single instance for high availability.**
Question: "99.99% uptime required."
Correct: **Multi-AZ + load balancer + autoscale** — single instance is a SPOF.
Why wrong: One instance fails = outage; HA needs redundancy.

**Trap #4 — On-demand for steady 24/7.**
Question: "Always-on predictable workload, minimise cost."
Correct: **Reserved / Savings Plan / Committed Use**, not on-demand.
Why wrong: On-demand is most expensive for steady state.

**Trap #5 — Spot for critical non-interruptible.**
Question: "Mission-critical DB, must never be killed."
Correct: **Reserved/On-demand + Multi-AZ**, NOT spot (spot can be reclaimed).
Why wrong: Spot interruption would kill a critical system.

**Trap #6 — Public subnet for a database.**
Question: "Database should not be internet-reachable."
Correct: **Private subnet**, no public IP, security groups/NSG; optionally private endpoint.
Why wrong: Public exposure = security failure.

**Trap #7 — Ignoring egress in cost.**
Question: "Cheap compute but heavy cross-region data movement."
Correct: Count **egress fees**; keep traffic in-region or use CDN; cost model incomplete without it.
Why wrong: Egress can dwarf compute savings.

**Trap #8 — No encryption when security is required.**
Question: "Data must be encrypted at rest and in transit."
Correct: **SSE (KMS/Key Vault) + TLS**; missing either fails the requirement.
Why wrong: Half-measures fail a hard security constraint.

**Trap #9 — Fixed size for spiky load.**
Question: "Traffic varies 10x daily."
Correct: **Auto-scaling + LB**; fixed size either over-provisions (cost) or under-serves peaks (outage).
Why wrong: No elasticity = wrong on both cost and performance.

**Trap #10 — Read replica for write latency.**
Question: "Need lower write latency / IOPS."
Correct: **Provisioned IOPS / faster block / cache**, not a read replica (replicas help reads, not writes).
Why wrong: Replicas don't speed up writes.

**Trap #11 — CDN for dynamic per-user content only.**
Question: "Mostly dynamic, personalised responses."
Correct: CDN helps static/cacheable; dynamic needs compute + cache layer (Redis) + scaling.
Why wrong: CDN alone won't fix dynamic performance.

**Trap #12 — Serverless for long-running steady job.**
Question: "12-hour continuous batch job."
Correct: **VM/batch**, not serverless (timeout/ cost limits); serverless suits short events.
Why wrong: Serverless has duration limits and is inefficient for long runs.

**Trap #13 — No retention for compliance/legal hold.**
Question: "Records must be kept 7 years, immutable."
Correct: **Archive tier with retention lock (WORM)**; ordinary deletion fails legal hold.
Why wrong: Editable storage can't satisfy immutable retention.

**Trap #14 — VPN vs Direct Connect confusion.**
Question: "Consistent low-latency, high-bandwidth link to on-prem."
Correct: **Dedicated interconnect** (Direct Connect/ExpressRoute/Interconnect) — VPN rides the public internet, variable latency.
Why wrong: VPN is fine for occasional/secure, not for steady high-bandwidth low-latency.

**Trap #15 — Right-size ignored.**
Question: "App uses 5% of a huge instance."
Correct: **Right-size** (smaller instance / autoscale) to cut cost; don't leave it oversized.
Why wrong: Oversizing wastes money — a core 2.5 skill.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Global E-commerce
**Scenario:** Public web store, global customers, <100 ms load, sales spikes 10x on promo, PCI card data, 99.99% uptime, cost-conscious.
**Question:** Provision the resources to meet ALL requirements.
**Walkthrough:**
1. **Compute:** Auto-scaling group + ALB (spikes); containers on EKS/AKS/GKE.
2. **Storage:** Images → object + CDN; catalog → managed DB Multi-AZ.
3. **Network:** CDN (static), WAF on ALB, DB in private subnet, VPN/Direct to on-prem.
4. **Security:** TLS + KMS encryption + IAM least-priv + secrets manager + WAF.
5. **Availability:** Multi-AZ, read replicas, backups.
6. **Compliance:** PCI-compliant services, in-region, audit logs.
7. **Cost:** Reserved baseline + on-demand peak + lifecycle-archive logs.
**Bonus Q:** Promo crashed the DB. Fix? → Read replica + Redis cache + autoscale DB.

### PBQ 2 — In-Country Healthcare
**Scenario:** Patient records, HIPAA-like, must stay in-country, encrypted, auditable, DR RTO 4h, no public exposure.
**Question:** Provision compliant resources.
**Walkthrough:**
1. **Storage:** Encrypted object + archive with retention lock (WORM) for legal hold.
2. **Compute:** Private-subnet VMs, no public IP, jump host for admin.
3. **Network:** Private endpoints, VPN to clinics, no internet ingress.
4. **Security:** KMS encryption, IAM, audit logging.
5. **Availability:** Multi-AZ DB + cross-region backup (DR, RTO 4h).
6. **Compliance:** In-region only; attestation present; retention policy.
**Bonus Q:** Can you use the cheapest foreign region? → No — regulatory blocks it (2.3/2.5 link).

### PBQ 3 — Cheap Spiky Mobile Backend
**Scenario:** Millions of short requests/day, unpredictable, low cost, global users.
**Question:** Best compute + storage + network provisioning.
**Walkthrough:**
1. **Compute:** Serverless (Lambda/Functions/Cloud Run) — scales to zero, pay per invoke.
2. **Storage:** Object for assets + managed NoSQL for session.
3. **Network:** Global CDN + API gateway.
4. **Cost:** Serverless + on-demand = near-zero idle.
**Bonus Q:** Why not reserved VMs? → Idle cost when traffic is zero; serverless fits spiky better.

### PBQ 4 — Data Lake Cost + Performance
**Scenario:** 500 TB, recent data queried fast, old data rarely touched, keep costs low, attribute spend per team.
**Question:** Provision storage + compute + cost controls.
**Walkthrough:**
1. **Storage:** Hot recent → standard object; cold → Glacier/Archive via lifecycle; warehouse for structured.
2. **Compute:** Serverless query (Athena/BigQuery) + spot ETL.
3. **Cost:** Lifecycle tiering + spot + **cost allocation tags** per team.
**Bonus Q:** What if a team's bill spikes? → Tags reveal which team; right-size their tiering/query.

### PBQ 5 — HA Web App (availability trap)
**Scenario:** Internal app, 99.99% uptime, must survive an AZ failure, moderate steady traffic.
**Question:** Provision for HA without overspending.
**Walkthrough:**
1. **Compute:** 2+ instances across **Multi-AZ** behind an ALB + autoscale (min 2).
2. **Storage:** Managed DB Multi-AZ; assets on object (durable 11 9s).
3. **Network:** Private app tier, public only at LB; health checks.
4. **Availability:** No SPOF; auto-recover on instance/AZ failure.
5. **Cost:** Reserved for steady baseline (not on-demand).
**Bonus Q:** Single instance in one AZ — compliant? → No, AZ failure = outage; fails 99.99%.


---

## SECTION 7 — MEMORY AIDS

### 7.1 — The 8 requirement categories
**"SCAP-NCCS"** → **S**torage, **C**ompute, **A**vailability, **P**erformance, **N**etwork, **C**ost, **C**ompliance, **S**ecurity.
Or simpler: **"Store Compute, Avail Perf, Net Cost, Comply Secure."**

### 7.2 — Find the hard constraint first
**"Compliance and Availability are the bosses. Cost and Performance are the employees."**
Most scenarios have one non-negotiable constraint (compliance/availability). Satisfy it first, then optimise the rest. Never let "cheapest" override a hard constraint.

### 7.3 — Storage picker
**"Object = stuff on the web. Block = the DB disk. File = shared drive. Archive = the basement."**
- Object: unstructured, web-served → S3/Blob/Storage
- Block: low-latency DB/OS → EBS/Disk/PD
- File: shared by many → EFS/Files/Filestore
- Archive: rarely touched → Glacier/Archive

### 7.4 — Compute picker
**"Spiky = scale. Short = serverless. Steady = reserve. GPU = learn. Batch = spot."**
- Spiky → auto-scaling + LB
- Short events → serverless
- Steady 24/7 → reserved
- ML/render → GPU
- Interruptible batch → spot

### 7.5 — Availability ladder
**"One = risk. Two AZ = safe. Region = DR."**
- Single → SPOF
- Multi-AZ + LB → HA (99.9x%)
- Multi-region + replica → DR (RTO/RPO)

### 7.6 — Cost ladder
**"Reserve the steady. Spot the batch. Lifecycle the cold. Tag the teams."**

### 7.7 — Decision tree (ASCII)
```
Given requirements, provision resources:
│
├─ Must stay in-country / regulated? ─────► IN-REGION + encrypted + audit (compliance FIRST)
├─ 99.99% / no AZ failure? ─────────────► Multi-AZ + LB + autoscale (availability)
├─ Low-latency DB? ─────────────────────► Block SSD + provisioned IOPS + cache
├─ Spiky traffic? ──────────────────────► Auto-scaling + serverless bits
├─ Unstructured / web assets? ───────────► Object + CDN
├─ Steady 24/7? ────────────────────────► Reserved / Savings / Committed
├─ Interruptible batch? ────────────────► Spot / preemptible
└─ Secrets / private? ──────────────────► Private subnet + IAM + KMS + WAF
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Requirement | AWS | Azure | GCP |
|---|---|---|---|
| Object storage | S3 | Blob Storage | Cloud Storage |
| Block storage | EBS | Managed Disks | Persistent Disk |
| File storage | EFS | Azure Files | Filestore |
| Archive tier | S3 Glacier (Deep Archive) | Blob Archive | Archive class |
| VMs / instances | EC2 | Azure VMs | Compute Engine |
| Containers / K8s | EKS / Fargate | AKS / Container Apps | GKE / Cloud Run |
| Serverless | Lambda | Azure Functions | Cloud Functions / Cloud Run |
| Auto-scaling | ASG | VMSS | MIG / autoscaler |
| GPU | EC2 GPU (p3/p4) | Azure GPU | GCP GPU |
| Spot | Spot Instances | Spot VMs | Preemptible / Spot |
| Reserved | Reserved / Savings Plan | Reserved / Savings | Committed Use |
| CDN | CloudFront | Front Door / CDN | Cloud CDN |
| WAF | AWS WAF | App Gateway WAF | Cloud Armor |
| Encryption keys | KMS | Key Vault | Cloud KMS |
| Secrets | Secrets Manager | Key Vault | Secret Manager |
| Private link | PrivateLink / endpoints | Private Endpoint | Private Service Connect |
| Dedicated network | Direct Connect | ExpressRoute | Cloud Interconnect |
| Managed DB | RDS / Aurora | Azure SQL / DB | Cloud SQL / AlloyDB |
| NoSQL | DynamoDB | Cosmos DB | Bigtable / Firestore |
| Data warehouse | Redshift | Synapse | BigQuery |
| Cache | ElastiCache | Cache for Redis | Memorystore |
| Audit logs | CloudTrail | Activity Log | Cloud Audit Logs |
| Cost tags | Cost Allocation Tags | Tags + Cost MGMT | Labels |

**Cross-cloud constant:** Object=cheap/unstructured, Block=DB disk, Multi-AZ=HA, Reserved=steady savings, KMS=encryption, Private subnet=isolation, CDN=global static — the *concept* maps everywhere; only the service name changes.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: A workload is steady 24/7 and predictable. Which pricing model do you pick?**
A: Reserved Instances / Savings Plan / Committed Use — far cheaper than on-demand for steady state.

**Q2: Where do you put a low-latency transactional database disk?**
A: Block storage (EBS / Managed Disk / Persistent Disk), SSD-backed, with provisioned IOPS if needed. Not object storage.

**Q3: What does Multi-AZ give you?**
A: High availability — if one AZ fails, the load balancer/failover shifts to the other; no single point of failure.

### 🎓 Cloud Administrator Questions

**Q4: A requirement says "data must stay in Malaysia and meet banking compliance." How do you provision?**
A: Choose an in-region deployment (ap-southeast-1/3 or Southeast Asia), encrypted storage (KMS), private subnets, IAM least-privilege, audit logging, and PCI/compliance-attested services. Compliance is the hard constraint — it overrides cheaper foreign regions.

**Q5: Traffic spikes 10x on promo days but is quiet otherwise. Compute strategy?**
A: Auto-scaling group behind a load balancer, with a reserved baseline for steady traffic and on-demand/scale-out for peaks. Avoids over-provisioning and outage.

**Q6: How do you keep storage cost down on 500 TB with mostly cold data?**
A: Lifecycle policies tiering old data to archive/cold class, object storage for unstructured, cost allocation tags per team, and right-sizing.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: Client wants 99.99% uptime but provisioned a single instance. What do you say?**
A: A single instance is a SPOF — an AZ failure = outage. We need Multi-AZ + load balancer + autoscale, and Multi-AZ database, to meet that SLA.

**Q8: "Why not just use the cheapest region?"**
A: If data has residency/compliance rules, the cheapest region may be disallowed. Also factor egress, latency, and service availability. The hard constraint wins.

**Q9: How do you balance cost vs security vs availability?**
A: Satisfy the non-negotiable constraints first (compliance, availability, security), then optimise cost within that envelope — right-sizing, reserved capacity, lifecycle, autoscaling.

---

## SECTION 10 — FLASHCARDS

**Storage**
Q: Unstructured, web-served, massive → which storage?
A: Object storage (S3/Blob/Cloud Storage).

Q: Low-latency transactional DB disk → which storage?
A: Block storage (EBS/Disk/Persistent Disk), SSD.

Q: Shared by many servers (NFS/SMB) → which storage?
A: File storage (EFS/Azure Files/Filestore).

Q: Rarely accessed, keep years → which tier?
A: Archive / cold tier (Glacier / Blob Archive).

Q: High IOPS DB → what block config?
A: Provisioned IOPS (io2 / Ultra Disk / Extreme PD).

**Compute**
Q: Spiky/unpredictable traffic → compute?
A: Auto-scaling group + load balancer.

Q: Short event tasks, millions/day → compute?
A: Serverless (Lambda/Functions/Cloud Run).

Q: Steady 24/7 predictable → pricing?
A: Reserved / Savings Plan / Committed Use.

Q: Fault-tolerant batch, cheap → pricing?
A: Spot / preemptible.

Q: ML training / rendering → compute?
A: GPU instance.

**Network / Security**
Q: Global low-latency static assets →?
A: CDN (CloudFront/Front Door/CDN).

Q: Secure link to on-prem, low latency →?
A: Dedicated interconnect (Direct Connect/ExpressRoute/Interconnect), not just VPN.

Q: DB must not be internet-reachable →?
A: Private subnet, no public IP, security groups/NSG.

Q: Encrypt data at rest →?
A: SSE with KMS / Key Vault / Cloud KMS.

Q: Protect web app from common attacks →?
A: WAF (AWS WAF / App Gateway WAF / Cloud Armor).

**Availability / Cost / Compliance**
Q: 99.99% uptime, AZ failure survivable →?
A: Multi-AZ + load balancer + autoscale.

Q: DR with RTO/RPO →?
A: Multi-region + backup/replica meeting targets.

Q: Data must stay in-country →?
A: In-region deployment (regulatory/compliance constraint).

Q: Attribute spend per team →?
A: Cost allocation tags / labels.

Q: Cut storage cost on cold data →?
A: Lifecycle policy to archive tier.

**Traps**
Q: Cheapest region with data residency rule → allowed?
A: No — compliance overrides cost.

Q: DB needs low latency → object storage?
A: No — block storage.

Q: 99.99% on a single instance → OK?
A: No — SPOF; needs Multi-AZ.

Q: Critical DB on spot → OK?
A: No — spot can be reclaimed; use reserved/Multi-AZ.

Q: Public subnet for database → OK?
A: No — must be private.


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** A low-latency transactional database needs fast disk I/O. The correct storage type is:
A) Object  B) Block (SSD)  C) File  D) Archive
**Answer: B) Block (SSD).** Databases need low-latency block storage.
*Why others wrong:* Object is unstructured; file is shared; archive is cold/slow.

**E2.** Global users accessing static images need low latency. Best add:
A) Bigger VM  B) CDN  C) More DB  D) VPN
**Answer: B) CDN.** Caches static content close to users.
*Why others wrong:* VM/DB don't solve geographic latency; VPN is for private access.

**E3.** A steady, always-on workload should use:
A) Spot  B) On-demand  C) Reserved  D) Preemptible
**Answer: C) Reserved.** Cheapest for predictable 24/7.
*Why others wrong:* Spot/preemptible are interruptible; on-demand is pricier for steady.

**E4.** To keep a database off the public internet, place it in:
A) Public subnet  B) Private subnet  C) CDN  D) Object storage
**Answer: B) Private subnet** (no public IP, security groups).
*Why others wrong:* Public subnet exposes it; CDN/object irrelevant to DB isolation.

**E5.** Data that must remain in the customer's country is a _____ requirement:
A) Cost  B) Compliance/Regulatory  C) Performance  D) Network
**Answer: B) Compliance/Regulatory** (data residency).
*Why others wrong:* Cost/perf/network don't enforce geography by law.

### MEDIUM (10)

**M1.** Traffic swings 10x daily. Best compute approach:
A) One large fixed VM  B) Auto-scaling + load balancer  C) Single reserved instance  D) Manual resize
**Answer: B) Auto-scaling + LB.** Handles spikes, no over-provision.
*Why others wrong:* Fixed sizes either over-cost or under-serve; manual is too slow.

**M2.** A mission-critical DB must never be interrupted. Avoid:
A) Multi-AZ  B) Reserved  C) Spot  D) Encryption
**Answer: C) Spot.** Spot can be reclaimed — fatal for critical DB.
*Why others wrong:* Multi-AZ/reserved/encryption are all appropriate.

**M3.** 500 TB, mostly cold, low cost. Best storage strategy:
A) All standard object  B) Lifecycle to archive + object  C) All block  D) All file
**Answer: B) Lifecycle to archive tier.** Cuts cost on cold data.
*Why others wrong:* All-standard wastes money; block/file wrong types for bulk cold.

**M4.** Need sub-millisecond read performance on a hot dataset. Add:
A) Read replica  B) In-memory cache (Redis)  C) Bigger VM  D) CDN
**Answer: B) In-memory cache.** Fastest read path for hot data.
*Why others wrong:* Replica helps scale reads but not sub-ms; VM/CDN don't target hot-cache latency.

**M5.** PCI card data, must be encrypted. Minimum:
A) TLS only  B) Encryption at rest (KMS) only  C) Both at rest + in transit  D) Private subnet only
**Answer: C) Both at rest + in transit.** Requirement says encrypted (implies both).
*Why others wrong:* Half-measures fail; private subnet isn't encryption.

**M6.** A 12-hour continuous batch job is best run on:
A) Serverless  B) VM/batch instance  C) CDN  D) Read replica
**Answer: B) VM/batch instance.** Serverless has duration limits; long jobs need VMs.
*Why others wrong:* Serverless times out; CDN/replica irrelevant.

**M7.** Want to attribute cloud spend to each team. Use:
A) Private subnets  B) Cost allocation tags / labels  C) CDN  D) Reserved instances
**Answer: B) Cost allocation tags / labels.**
*Why others wrong:* Others don't attribute billing.

**M8.** Consistent low-latency, high-bandwidth link to on-prem. Best:
A) VPN  B) Dedicated interconnect (Direct Connect/ExpressRoute)  C) CDN  D) Public IP
**Answer: B) Dedicated interconnect.** VPN rides public internet (variable latency).
*Why others wrong:* VPN is fine for occasional secure, not steady high-bandwidth low-latency.

**M9.** 99.99% uptime with AZ failure survival requires:
A) One big instance  B) Multi-AZ + LB + autoscale  C) Single region, no LB  D) Spot fleet
**Answer: B) Multi-AZ + LB + autoscale.** Removes SPOF.
*Why others wrong:* Single instance/fleet without AZ redundancy fails on AZ loss.

**M10.** Records must be kept 7 years, immutable (legal hold). Use:
A) Standard object, deletable  B) Archive tier with retention lock (WORM)  C) Block storage  D) CDN
**Answer: B) Archive + retention lock (WORM).** Immutable retention.
*Why others wrong:* Standard deletable fails legal hold; block/CDN wrong.

### HARD (5)

**H1.** Public store: global <100 ms, 10x promo spikes, PCI data, 99.99%, cost-conscious. The MOST complete provisioning is:
A) One big VM + object + on-demand  B) Auto-scale+LB, object+CDN, Multi-AZ DB, KMS+WAF+IAM, reserved baseline, in-region  C) Serverless only  D) Spot fleet + public DB
**Answer: B)** — satisfies perf (CDN/cache/autoscale), cost (reserved+scale), security (WAF/KMS/IAM), availability (Multi-AZ), compliance (in-region/PCI).
*Why others wrong:* A lacks HA/security/cost control; C can't hold steady DB/long jobs; D exposes/unstable.

**H2.** You pick the cheapest foreign region but data must stay in-country. Result:
A) Optimal cost  B) Compliance violation (must use in-region)  C) Faster  D) Better DR
**Answer: B) Compliance violation.** Regulatory overrides cheapest region.
*Why others wrong:* Cheap-but-illegal fails the hard constraint; no benefit offsets it.

**H3.** DB write latency is high under load. Which does NOT help?
A) Provisioned IOPS  B) Faster block  C) Read replica  D) In-memory cache for reads
**Answer: C) Read replica.** Replicas help reads, not write latency.
*Why others wrong:* IOPS/faster block/cache (offloading reads) all help write path indirectly or directly.

**H4.** Internal app needs 99.99% and AZ failure survival on a tight budget. Best balance:
A) Single reserved instance  B) Multi-AZ + LB + autoscale, reserved baseline  C) Spot fleet  D) Public subnet single VM
**Answer: B) Multi-AZ + LB + autoscale with reserved baseline** — HA met, cost controlled via reserved.
*Why others wrong:* A/D fail HA; C unstable for critical.

**H5.** Heavy cross-region data movement wasn't budgeted; compute was cheap. Biggest miss:
A) Right-sizing  B) Egress fees  C) Reserved  D) CDN
**Answer: B) Egress fees.** Cross-region transfer can dominate cost; must be in TCO.
*Why others wrong:* Right-sizing/reserved help but don't capture the transfer cost; CDN reduces but egress still applies.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Compliance overrides cost (in-region) | 🔴 CRITICAL | Hard constraint, common trap |
| Multi-AZ for HA / 99.99% | 🔴 CRITICAL | Single instance = SPOF trap |
| Storage type match (object/block/file/archive) | 🔴 CRITICAL | Core mapping question |
| Compute match (autoscale/serverless/reserved/spot/GPU) | 🔴 CRITICAL | Spiky vs steady vs batch |
| Security (encrypt, private, WAF, IAM) | 🟠 HIGH | Hard requirement |
| Cost model (reserved/spot/lifecycle/tags) | 🟠 HIGH | "minimise cost" scenarios |
| Network (CDN, VPN/Direct, private subnet) | 🟠 HIGH | Latency/isolation |
| Performance (IOPS, cache, read replica) | 🟠 HIGH | Latency/throughput |
| Availability (RTO/RPO, DR) | 🟠 HIGH | Backup/replica cadence |
| Right-sizing | 🟡 MEDIUM | Cost discipline |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**The 8 requirement categories**
Storage · Compute · Network · Performance · Security · Cost · Availability · Compliance

**Decision order:** Find the HARD constraint (compliance/availability/security) → satisfy it → optimise the rest (cost/perf).

**Storage picker**
- Unstructured/web → Object (S3/Blob/Storage)
- Low-latency DB → Block SSD (EBS/Disk/PD) + provisioned IOPS
- Shared → File (EFS/Files/Filestore)
- Cold/years → Archive (Glacier/Blob Archive)

**Compute picker**
- Spiky → auto-scale + LB
- Short events → serverless
- Steady 24/7 → reserved
- Batch/interruptible → spot
- ML/render → GPU

**Network/Security**
- Global static → CDN
- On-prem low-latency → Direct Connect/ExpressRoute/Interconnect
- DB isolated → private subnet, no public IP, SG/NSG
- Encrypt → KMS (at rest) + TLS (transit)
- Web app → WAF

**Availability ladder**
- Single = SPOF
- Multi-AZ + LB + autoscale = HA (99.9x%)
- Multi-region + replica = DR (RTO/RPO)

**Cost**
- Reserved (steady) · Spot (batch) · Lifecycle→archive (cold) · Tags (attribute)

**Exam triggers → answer**
- "Data must stay in-country" → in-region (compliance > cost)
- "99.99% / no AZ failure" → Multi-AZ + LB
- "Low-latency DB" → block SSD + provisioned IOPS
- "Spiky 10x" → auto-scaling + LB
- "Short events, millions/day" → serverless
- "Steady 24/7, cheap" → reserved
- "Interruptible batch, cheap" → spot
- "DB not internet-reachable" → private subnet
- "Encrypted" → KMS + TLS
- "500 TB mostly cold" → lifecycle to archive
- "Single instance for HA" → WRONG (SPOF)

**Acronyms**
- SPOF = Single Point of Failure · HA = High Availability · DR = Disaster Recovery
- RTO = Recovery Time Objective · RPO = Recovery Point Objective
- IOPS = Input/Output Operations Per Second · SSD = Solid-State Drive
- VM = Virtual Machine · DB = Database · CDN = Content Delivery Network
- KMS = Key Management Service · IAM = Identity and Access Management
- WAF = Web Application Firewall · VPN = Virtual Private Network
- SLA = Service Level Agreement · TCO = Total Cost of Ownership
- NIC = Network Interface Card · NFS/SMB = file-sharing protocols

**Traps recap**
1. Cheapest region ignored residency → compliance wins. 2. Object ≠ DB disk (block). 3. Single instance ≠ HA. 4. On-demand ≠ steady (reserved). 5. Spot ≠ critical (can be killed). 6. Public subnet ≠ DB. 7. Ignore egress = incomplete TCO. 8. Half-encryption fails. 9. Fixed size for spiky = wrong. 10. Read replica ≠ write latency. 11. CDN alone ≠ dynamic perf. 12. Serverless ≠ long batch. 13. No retention lock ≠ legal hold. 14. VPN ≠ steady low-latency link. 15. Oversized = wasted cost (right-size).

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **FinOps maturity:** Cloud Financial Operations is now standard — cost allocation tags, commitment planning (reserved/savings), and egress awareness are first-class. Directly the 2.5 Cost requirement.
- **Graviton / Ampere (ARM) instances** (AWS/GCP) and **Azure Cobalt** cut compute cost ~20–40% for right-sized workloads — a cost/compute lever.
- **Storage tiers deepened:** S3 Glacier **Instant Retrieval** (archive with milliseconds retrieval) and Azure **Cool/Archive** + **Blob Premium** give finer cost/perf tuning — lifecycle to archive is the 2.5 move.
- **Managed DB HA:** Multi-AZ is default-on for RDS/Aurora/Cloud SQL/ Azure DB; **Aurora Serverless v2** and **Cloud SQL Autoscaler** blur steady/spiky compute — but reserved still wins for steady.
- **Zero-Trust / private connectivity:** PrivateLink / Private Endpoint / Private Service Connect make "no public IP" the default secure pattern — the 2.5 security/private-subnet answer.
- **Sovereign/in-region regions:** AWS ap-southeast-3 (Jakarta), Azure Malaysia, GCP regions in regulated markets reinforce the **compliance > cost** rule for residency.
- **Spot/Preemptible + containers:** Spot pods / preemptible nodes with disruption-tolerant workloads cut batch cost dramatically — the 2.5 "interruptible batch" answer.
- **CDN + edge compute:** CloudFront Functions / Azure Front Door / Cloud CDN + edge runtimes shift dynamic-cacheable logic to the edge — performance requirement lever.
- **Unified cost + compliance dashboards:** AWS Cost Anomaly Detection, Azure Cost Management + Defender, GCP FinOps/Security Command Center tie cost + compliance visibility together — the 2.5 cross-cutting view.

*End of Objective 2.5 notes. Domain 2.0 (Deployment) complete.*
