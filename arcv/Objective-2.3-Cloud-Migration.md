---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 2.3: Cloud Migration"
aliases: [Objective-2.3-Cloud-Migration, Domain-2.3-Cloud-Migration, Migration-Types, 6-Rs]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 2.3
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-2.3, migration, rehost, replatform, re-architect, refactor, retain, retire, cloud-migration, 6-rs, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.3: Summarize Aspects of Cloud Migration

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 2.0 Deployment Domain = 19% of total exam
> **Objective 2.3 Focus:** Migration types (on-prem→cloud, cloud→on-prem, cloud→cloud), resource allocation & key considerations (storage, compute, networking, cost, compliance, vendor lock-in, etc.), and the six application migration strategies — the "6 R's" (Rehost, Replatform, Re-architect, Refactor, Retain, Retire).

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 2.3
### Objective Name: *Summarize aspects of cloud migration.*

**What this objective means (beginner-friendly):**

Imagine you are moving house. You decide:
1. **Where you are moving** — from your old landed house to a condo (on-premises → cloud), from a condo back to a landed house (cloud → on-premises), or from one condo to another condo (cloud → cloud). These are the **migration types**.
2. **What you take with you and how you pack it** — how much furniture (storage), how big a moving truck (compute), which roads you use (networking), your budget (cost), strata rules (compliance). These are the **resource allocation & considerations**.
3. **How you move each item** — carry the whole cabinet as-is (Rehost), put it on wheels first (Replatform), rebuild it into a modular shelf (Re-architect/Refactor), keep some things in storage you still use (Retain), or throw out the broken sofa (Retire). These are the **application migration strategies (the 6 R's)**.

Cloud migration is simply the process of moving workloads, data, and applications from one environment to another. 2.3 tests whether you understand *the kinds of moves*, *what to plan for*, and *the strategy you pick for each app*.

**Why it matters in the real world:**
- Most organisations are NOT born in the cloud. They have decades of on-premises systems. Moving them is one of the largest, riskiest, most expensive IT projects a company undertakes.
- Picking the wrong migration strategy wastes money (lift-and-shift a legacy app that should have been retired) or causes outages (re-architecting everything at once instead of phasing).
- Every migration must respect **regulatory, compliance, cost, and platform** constraints — get these wrong and you face fines, lock-in, or failed audits.

**Why CompTIA tests it:**
- 2.3 is a **"summarize"** objective — it is broader and more conceptual than 2.1/2.2. The exam expects you to recognise migration terminology and recommend the right strategy given a scenario.
- The **6 R's** (especially Rehost vs Replatform vs Refactor vs Retire) are classic scenario distractors: the exam gives a workload description and asks which strategy fits.
- It pairs with 2.1 (deployment models), 2.2 (deployment strategies), and 2.5 (provisioning). Domain 2 is the "how do we get stuff into and running in the cloud" domain.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.3.1 — MIGRATION TYPES

There are three macro "directions" a migration can take. CompTIA lists them explicitly:

#### A. On-Premises → Cloud (Lift to Cloud)

**Definition:** Moving workloads, applications, and data from an organisation's own data centre (or colocation) into a public/private/hybrid cloud.

**Purpose:**
- Escape data-centre capital cost (CapEx) and move to operational cost (OpEx).
- Gain elasticity, managed services, and geographic reach.
- Modernise legacy estate.

**How it works:**
1. **Assess** — inventory applications, dependencies, data volumes, compliance needs.
2. **Plan** — choose migration strategy per app (the 6 R's in 2.3.2).
3. **Migrate** — move via agents, replication, image import, database CDC, or re-deploy from code.
4. **Cut over** — switch traffic (DNS, load balancer) using a 2.2 deployment strategy (blue-green/canary).
5. **Optimise** — right-size, retire, decommission on-prem.

**Advantages:** Scalability, reduced hardware burden, access to managed services, faster provisioning, DR benefits.

**Disadvantages:** Data-transfer cost & time, bandwidth limits, possible re-architecture needs, temporary double-running cost, skills gap.

**When to use:** Ageing data centre, expiring hardware lease, need for agility, M&A consolidation, new geographic expansion.

**When NOT to use:** Strict data-sovereignty law forbidding off-prem data leaving the country (then use private cloud on-prem or cloud→on-prem), or workloads with sub-millisecond latency to on-prem equipment.

#### B. Cloud → On-Premises (Cloud Repatriation)

**Definition:** Moving workloads *back* from a public cloud to an on-premises or colocation environment.

**Purpose:**
- Control cost of predictable, always-on, high-volume workloads (cloud can be cheaper at small scale, pricier at massive steady-state).
- Meet data-sovereignty or air-gapped security requirements.
- Reduce unpredictable egress/API fees.

**How it works:** Export VMs/images, copy data (often physically via appliance due to volume), re-deploy on hypervisor (VMware, KVM, Hyper-V) or OpenStack, re-point networking.

**Advantages:** Predictable cost at scale, full control, data residency, no egress fees, lower latency to local systems.

**Disadvantages:** Re-incurs CapEx, you own patching/hardware/DR again, loses elasticity, skills needed on-prem.

**When to use:** Stable, high-utilisation workloads; regulated data that must stay in-country; cost optimisation after cloud bill surprise.

**When NOT to use:** Spiky/bursty workloads (you lose elasticity), greenfield with no data-centre team.

> **Exam tip:** Cloud→on-premises is often called **repatriation** or **cloud exit**. It is a *valid* migration type — don't assume "migration = only to cloud."

#### C. Cloud → Cloud (Cloud-to-Cloud / Provider Switch)

**Definition:** Moving workloads from one cloud provider (or one cloud account/region) to another — e.g., AWS → Azure, or Azure → GCP.

**Purpose:**
- Escape vendor lock-in, negotiate better pricing, use a provider's unique service, merge after acquisition, meet regional availability.

**How it works:** Re-deploy IaC (Terraform works cross-cloud), migrate data via provider transfer appliances or network, adapt service-specific config (S3→Blob, DynamoDB→Cosmos).

**Advantages:** Avoid lock-in, pick best-of-breed services, competitive pricing, multi-cloud resilience.

**Disadvantages:** High effort (services are NOT 1:1), data egress cost, re-skilling, possible re-architecture, interim dual-run cost.

**When to use:** Better service fit elsewhere, acquisition forcing standardisation, cost arbitrage, sovereignty/region gap.

**When NOT to use:** If the only reason is "AWS is expensive" without quantifying egress + re-engineering cost — sometimes optimising the current cloud is cheaper.

---

### 2.3.2 — RESOURCE ALLOCATION & CONSIDERATIONS

Before/while migrating, you must plan **how resources are allocated** and weigh **cross-cutting considerations**. CompTIA lists these explicitly.

#### Storage
- **What:** Capacity, tier (hot/cold/archive), block vs file vs object, IOPS, throughput, redundancy.
- **Why it matters:** Data is usually the heaviest thing to move. Egress + transfer time dominate migration timelines.
- **Exam angle:** "Large historical dataset, rarely accessed" → archive/cold tier; "database needing low latency" → block SSD. Match storage type to workload (ties to 1.4).

#### Platform Compatibility
- **What:** Will the app run on the target OS/hypervisor/runtime? Are dependencies (glibc, .NET version, drivers) available?
- **Why it matters:** Incompatible platform = failed migration or forced re-architect.
- **Exam angle:** "Legacy app needs Windows Server 2008" → check target supports it or replatform/refactor.

#### Compute
- **What:** vCPU, RAM, GPU, instance family, auto-scaling, right-sizing.
- **Why it matters:** Over-provisioning wastes money; under-provisioning causes outages.
- **Exam angle:** Match compute shape to workload; use scaling (3.2) post-migration.

#### Cost
- **What:** Migration cost (tools, bandwidth, Professional Services), run cost (compute/storage/network), egress fees, dual-run period.
- **Why it matters:** The #1 reason migrations stall or are reversed (repatriation).
- **Exam angle:** "Predictable 24/7 high-volume workload" → on-prem or reserved may beat on-demand; always include egress in TCO.

#### Networking
- **What:** Bandwidth, latency, VPN/Direct Connect/ExpressRoute/Interconnect, DNS, IP reachability, firewall rules.
- **Why it matters:** Network is the pipe for the move and for ongoing access. Undersized pipe = weeks of transfer.
- **Exam angle:** Huge data volume + tight timeline → use physical shipment appliance (AWS Snowball, Azure Data Box, GCP Transfer Appliance) not the internet.

#### Management Overhead
- **What:** Who operates it? Self-managed vs managed service; skill requirement; tooling (monitoring, patching).
- **Why it matters:** Lifting a DB as EC2 (self-managed) raises overhead vs managed RDS.
- **Exam angle:** "Want to reduce admin effort" → pick managed service (Replatform to PaaS).

#### Service Availability
- **What:** Is the target region/service available? SLA, quota limits, feature parity between regions.
- **Why it matters:** A service may exist in us-east-1 but not the required region (e.g., sovereign/APAC).
- **Exam angle:** "Must run in Malaysia region with feature X" → verify availability before committing.

#### Vendor Lock-In
- **What:** Dependency on proprietary APIs/services that make leaving hard/costly.
- **Why it matters:** Deep use of Lambda + DynamoDB + proprietary ML is hard to move; drives cloud→cloud pain.
- **Exam angle:** "Want to stay portable" → use containers + Terraform + open standards (Kubernetes, PostgreSQL).

#### Environmental (Power and Cooling)
- **What:** Physical data-centre power/cooling footprint, PUE, carbon goals.
- **Why it matters:** On-prem carries the power/cooling burden; cloud shifts it to the provider (who may be greener). Repatriation re-incurs it.
- **Exam angle:** "Company has green/carbon-reduction goals" → public cloud (provider renewable PUE) can help; on-prem adds power/cooling responsibility.

#### Regulatory
- **What:** Laws governing where/how data is stored and processed (GDPR, PDPA, HIPAA, financial regs).
- **Why it matters:** Violation = fines, legal exposure. Can forbid certain migrations.
- **Exam angle:** "Data must stay in-country" → private cloud on-prem or in-country region; blocks cloud→cloud to foreign region.

#### Compliance
- **What:** Industry/contractual standards (PCI-DSS, SOC 2, ISO 27001, FedRAMP) the target must meet.
- **Why it matters:** Target environment must be certifiable; some clouds/regions lack the attestation.
- **Exam angle:** "PCI workload" → ensure target has PCI-compliant services/attestation before migrating.


---

### 2.3.3 — APPLICATION MIGRATION STRATEGIES (THE "6 R's")

These are the strategies for moving *each application*. The exam loves scenario questions here. The canonical set (popularised by AWS, adopted by CompTIA) is:

| Strategy | Also called | Core idea | Change to app? |
|---|---|---|---|
| **Rehost** | Lift-and-Shift | Move as-is, no code change | None |
| **Replatform** | Lift-and-Reshape / "lift, tinker, shift" | Move + minor optimisation (e.g., to managed DB) | Minor |
| **Re-architect** | Re-architect (for cloud-native) | Redesign significantly, keep same language | Significant |
| **Refactor** | Re-engineer (serverless/cloud-native) | Rewrite for cloud-native (microservices, serverless) | Full rewrite |
| **Retain** | Keep / "do nothing" | Leave as-is (not worth moving yet) | None |
| **Retire** | Decommission | Shut down, don't migrate | Remove |

> **CompTIA's exact listed terms (from the PDF):** Rehost, Replatform, Re-architect, Refactor, Retain, Retire. Note the PDF lists **Re-architect** and **Refactor** as *separate* items — know the distinction (below).

#### 1. Rehost (Lift-and-Shift)

**Definition:** Move the application to the cloud with **no architectural changes and minimal/no code modification** — copy the VM/image or redeploy the same binaries.

**Purpose:** Fastest path to cloud; low risk; quick win; meets "get out of the data centre" deadline.

**How it works:** VM import / image conversion (AWS SMS, Azure Migrate, GCP Migrate for Compute Engine), or re-deploy same artifacts on IaaS.

**Advantages:** Speed, low effort, low risk, preserves existing app behaviour, easy to automate at scale.

**Disadvantages:** No cloud-native benefit (no auto-scaling, no managed services), may carry inefficiencies, can be *more* expensive if not right-sized later.

**When to use:** Tight deadline, large estate to move fast, legacy apps you don't want to touch, first phase before optimisation.

**When NOT to use:** App would be cheaper/better as managed PaaS; app is end-of-life (then Retire); app needs cloud-native scale.

#### 2. Replatform ("Lift, Tinker, and Shift")

**Definition:** Move the app **with a few cloud optimisations** that require little/no code change — e.g., swap self-managed DB for managed DB (RDS), move to containers, use object storage for static files.

**Purpose:** Capture *some* cloud benefit (managed services, less ops) without a full rewrite.

**How it works:** Keep app code; change the backing services (managed DB, managed cache, object storage, container orchestration).

**Advantages:** Cheaper ops than Rehost, low risk, faster than Refactor, real cloud leverage (managed services).

**Disadvantages:** Still not fully cloud-native; benefit capped; some integration effort.

**When to use:** "We want managed DB / less patching but can't rewrite now"; moderate timeline; stateful apps.

**When NOT to use:** When a full rewrite would unlock needed scale/features (then Refactor) or when even minor changes risk breaking a fragile app (then Rehost/Retain).

#### 3. Re-architect

**Definition:** **Significantly redesign** the application to be cloud-capable — often moving from monolith to modular/services — **while keeping the same programming language and core logic**.

**Purpose:** Gain elasticity, resilience, and cloud features without a from-scratch rewrite.

**How it works:** Break monolith into services, introduce queues/caches, adopt managed components — but retain existing language/stack.

**Advantages:** Cloud benefits (scale, resilience) without total rewrite; lower risk than Refactor; reuses team skills.

**Disadvantages:** More effort than Replatform; potential behaviour changes; needs design work.

**When to use:** App is too rigid for cloud but still strategically valuable; need elasticity without rewriting from zero.

**When NOT to use:** App is simple enough for Replatform, or so legacy that a clean Refactor is justified.

#### 4. Refactor (Re-engineer / Cloud-Native Rewrite)

**Definition:** **Rewrite** the application to be fully cloud-native — microservices, containers, serverless, event-driven — often changing language/framework.

**Purpose:** Maximum cloud benefit: elastic scale, pay-per-use, resilience, modern DevOps.

**How it works:** Decompose to microservices, adopt Kubernetes/serverless (Lambda/Functions/Cloud Run), event buses, managed PaaS.

**Advantages:** Best scalability/cost-at-scale, resilience, agility, future-proof.

**Disadvantages:** Highest effort/cost/risk, longest timeline, needs cloud-native skills, possible business disruption.

**When to use:** App is strategic, needs elastic scale, current architecture can't scale, long-term modernisation goal.

**When NOT to use:** Tight deadline, low strategic value, no cloud-native skills, stable app fine as-is.

#### 5. Retain (Keep / Do Nothing)

**Definition:** **Leave the application where it is** — do not migrate it (yet). Usually because it's not worth moving, or depends on something that can't move.

**Purpose:** Avoid wasted effort; phase the migration; respect constraints (compliance, dependency).

**How it works:** Exclude from migration wave; revisit later; possibly hybrid (some apps cloud, some retained on-prem).

**Advantages:** Saves cost/effort, reduces risk, respects dependencies/compliance, pragmatic.

**Disadvantages:** Perpetuates old environment, possible hybrid complexity, technical debt lingers.

**When to use:** Low-value or rarely used app; compliance blocks move; tight dependency on on-prem system; "not now" prioritisation.

**When NOT to use:** When the whole point was to decommission the data centre — retaining defeats it (unless hybrid by design).

#### 6. Retire (Decommission)

**Definition:** **Shut the application down** and do not migrate it — because it's redundant, unused, or replaced by another system.

**Purpose:** Cut cost and complexity; stop paying for duplicate/legacy systems.

**How it works:** Identify unused apps (via assessment/inventory), confirm no dependency, archive data if needed, decommission.

**Advantages:** Immediate cost saving, less to migrate/manage, reduces attack surface.

**Disadvantages:** Risk if dependencies missed; data-retention/legal hold considerations; user disruption if wrong app killed.

**When to use:** Duplicate app, end-of-life software, low/no usage, replaced by SaaS.

**When NOT to use:** If any active dependency or legal hold exists — then Retain, not Retire.

> **Decision summary for the exam:**
> - "Move fast, no changes" → **Rehost**
> - "Move + use managed DB, minimal change" → **Replatform**
> - "Redesign for cloud but keep our language" → **Re-architect**
> - "Full rewrite, cloud-native/serverless" → **Refactor**
> - "Not worth moving / can't move yet" → **Retain**
> - "Kill it, we don't need it" → **Retire**


---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — On-Premises → Cloud (Rehost then Replatform)

**AWS Example:**
A Malaysian retail chain runs its POS (point-of-sale) backend on VMware in a KL data centre. Lease expiring. They use **AWS Application Migration Service (MGN)** to **Rehost** 40 VMs to EC2 with minimal change (lift-and-shift), meeting the lease-exit date. Post-move, they **Replatform** the SQL Server to **Amazon RDS** (managed, Multi-AZ) and move product images to **S3** + **CloudFront**. Cutover uses **Route 53** weighted routing (canary from 2.2).

**Azure Example:**
A manufacturing firm uses **Azure Migrate** to discover and **Rehost** on-prem VMs to **Azure VMs**. They then **Replatform** the Oracle DB to **Azure Database for PostgreSQL** (managed) and front-end static assets to **Azure Blob + CDN**. Connectivity via **Azure ExpressRoute** from the factory.

**Google Cloud Example:**
A media company uses **Migrate to Virtual Machines (formerly Velostrata)** to **Rehost** to **Compute Engine**. They **Replatform** video transcoding to **Cloud Storage** + **Transcoder API**, and the web tier to **Google Kubernetes Engine (GKE)**. Data moved via **Storage Transfer Service**.

### 3.2 — Cloud → On-Premises (Repatriation)

**AWS Example:**
A SaaS company finds its steady 24/7 ingestion tier costs more on EC2 than owned hardware. They **Rehost** those workloads back to an on-prem **VMware vSphere** cluster (cloud exit), keeping bursty tier on AWS. Data exported via **AWS Snowball** for the bulk, then delta sync.

**Azure Example:**
A bank with data-residency law moves its core ledger from **Azure VMs** back to an in-country colocation running **Azure Stack HCI** (still "Azure" but on-prem). This satisfies sovereignty while keeping tooling.

**Google Cloud Example:**
A research lab with air-gapped requirements pulls a trained ML pipeline from **Vertex AI / GKE** back to an on-prem K8s cluster (Anthos on bare metal) to run inside a secured facility with no external connectivity.

### 3.3 — Cloud → Cloud

**AWS Example:**
A startup acquired by a Microsoft-shop parent must standardise on Azure. They use **Terraform** (provider-agnostic IaC) to re-deploy infra, migrate data S3→**Azure Blob** via **Azure Data Box**, and rewrite Lambda functions as **Azure Functions**. Deep DynamoDB use prompts a **Re-architect** to **Cosmos DB**.

**Azure Example:**
A gaming firm moves from Azure to **AWS** to use **GameLift** + **Global Accelerator**. They **Replatform** Azure SQL to **Aurora**, and rebuild Azure Functions as **Lambda**. Networking re-pointed via **Direct Connect**.

**Google Cloud Example:**
An AI company moves from AWS to GCP to use **Vertex AI** + **TPU**. They **Refactor** the training pipeline into **Vertex AI Pipelines** + **Cloud Run**, and migrate data via **BigQuery Data Transfer Service**.

### 3.4 — The 6 R's in production scenarios

- **Rehost:** A hospital moves 200 legacy VMs to cloud before a hardware refresh — speed over optimisation.
- **Replatform:** An insurer swaps self-managed MySQL on EC2 for **Amazon RDS** to cut DBA toil (no app code change).
- **Re-architect:** A monolithic .NET app is split into services behind **API Gateway** + **SQS** (same C# stack) to gain independent scaling.
- **Refactor:** A batch job rewritten as **AWS Lambda** + **EventBridge** (event-driven, serverless, pay-per-invoke).
- **Retain:** A niche on-prem CAD licence server kept on-prem (cloud has no equivalent, low value to move).
- **Retire:** 12 duplicate internal reporting tools decommissioned when a single **Power BI / QuickSight** dashboard replaces them.

### 3.5 — Consideration-driven examples

- **Networking/bandwidth:** 500 TB on-prem dataset → physically shipped with **AWS Snowball / Azure Data Box / GCP Transfer Appliance** instead of internet transfer (weeks vs days).
- **Regulatory/Compliance:** PDPA (Malaysia) data must stay in-region → deployed in **AWS ap-southeast-1 (Singapore)** or **ap-southeast-3 (Jakarta)** / Azure **Southeast Asia**; blocks moving to a foreign region.
- **Cost:** Steady high-volume → **Reserved Instances / Savings Plans / Reserved Capacity** vs on-demand; includes egress in TCO.
- **Vendor lock-in:** Team standardises on **Kubernetes + Terraform + PostgreSQL** to stay portable across AWS/Azure/GCP.
- **Environmental:** Company with net-zero goal chooses a cloud region powered by renewables (provider PUE ~1.1) over expanding its own cooling-heavy data centre.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — The Six Migration Strategies (6 R's)

| Strategy | Definition | Speed | Cost (effort) | Cloud Benefit | Management Overhead | Best Use Case | Exam Tip |
|---|---|---|---|---|---|---|---|
| **Rehost** | Lift-and-shift, no code change | Fastest | Low | Low (IaaS only) | High (self-managed) | Tight deadline, big estate | "Move as-is, quickly" |
| **Replatform** | Move + minor optimisation (managed DB) | Fast | Low-Med | Medium | Medium | Want managed services, no rewrite | "Use managed DB, little change" |
| **Re-architect** | Redesign for cloud, keep language | Slow | High | High | Medium | Need elasticity, keep stack | "Redesign but keep our code" |
| **Refactor** | Full cloud-native rewrite | Slowest | Highest | Max | Low (serverless/PaaS) | Strategic app, elastic scale | "Rewrite as microservices/serverless" |
| **Retain** | Leave as-is, don't move | N/A | None now | None | Unchanged | Can't/shouldn't move yet | "Not worth moving / compliance" |
| **Retire** | Decommission, don't move | N/A | Saves | None | Lower | Redundant/unused app | "We don't need this anymore" |

### 4.2 — Migration Types (Direction)

| Type | From → To | Driver | Main Risk | Exam Tip |
|---|---|---|---|---|
| **On-prem → Cloud** | Data centre → cloud | CapEx→OpEx, agility | Data transfer, downtime | Most common "migration" |
| **Cloud → On-prem** | Cloud → data centre | Cost at scale, sovereignty | Re-incur CapEx/ops | Called repatriation/cloud exit |
| **Cloud → Cloud** | Provider A → Provider B | Lock-in, best-of-breed | Non-1:1 services, egress | Use Terraform + containers |

### 4.3 — Storage Tiers (for migration planning)

| Tier | Access | Cost | Use in migration | Exam Tip |
|---|---|---|---|---|
| **Hot/Standard** | Frequent | High | Active DB/app data | Default for running workload |
| **Cool/Cold** | Infrequent | Low | Backups, old records | "Rarely accessed" → cool |
| **Archive** | Rare | Lowest | Compliance retention | "Never touch, keep 7 yrs" → archive |

### 4.4 — Rehost vs Replatform vs Refactor (the classic trio)

| | Rehost | Replatform | Refactor |
|---|---|---|---|
| Code change | None | Minimal | Full rewrite |
| Cloud-native | No | Partial (managed svc) | Yes |
| Time | Days-weeks | Weeks | Months |
| Risk | Low | Low-Med | High |
| Cost (build) | Low | Med | High |
| Cost (run) | High (if unoptimised) | Med | Low at scale |

### 4.5 — Key Considerations Cheat Table

| Consideration | Question it answers | Exam signal |
|---|---|---|
| Storage | How much, what tier, IOPS? | "Large cold dataset" → archive |
| Compute | vCPU/RAM, right-size, scale? | "24/7 steady" → reserve/on-prem |
| Networking | Bandwidth, VPN/Direct, DNS? | "500TB, 1 week" → transfer appliance |
| Cost | TCO incl. egress, dual-run? | "Predictable high volume" → repatriate |
| Platform compatibility | Will it run on target? | "Legacy OS" → check support |
| Management overhead | Self vs managed? | "Less admin" → managed/PaaS |
| Service availability | Region/service present? | "Feature X in MY region?" |
| Vendor lock-in | Portable or proprietary? | "Stay portable" → K8s+Terraform |
| Environmental | Power/cooling/carbon? | "Net-zero" → green cloud |
| Regulatory | Where can data live? | "Must stay in-country" → block foreign |
| Compliance | What attestations needed? | "PCI" → PCI-compliant target |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — "Migration always means TO the cloud."**
Question framing: "A company is migrating its workload..." → assume public cloud.
Correct: Could be **cloud → on-prem (repatriation)** or **cloud → cloud**. The word "migrate" alone does NOT mean "to cloud."
Why wrong: Assuming direction. Always read the source and destination.

**Trap #2 — Rehost vs Replatform confusion.**
Question: "Move the app with no code changes but switch its database to a managed service."
Correct: **Replatform** (lift, tinker, shift) — not Rehost (which is zero change) and not Refactor (no rewrite).
Why wrong: Any backing-service change = Replatform, even with no app-code edit.

**Trap #3 — Refactor vs Re-architect.**
Question: "Redesign the app as microservices on serverless, changing the language."
Correct: **Refactor** (full cloud-native rewrite).
Why wrong: **Re-architect** keeps the same language/stack — only Refactor implies a from-scratch cloud-native rewrite. The exam lists them separately; don't merge them.

**Trap #4 — Retain vs Retire.**
Question: "An app is rarely used and depends on an on-prem system that can't move."
Correct: **Retain** (keep, can't move).
Why wrong: **Retire** = shut down because it's redundant/unneeded. If it's still *used* or *depended upon*, you Retain, not Retire. Retire only when truly decommissioning.

**Trap #5 — "Use the internet to transfer 500 TB fast."**
Question: "Move 500 TB to cloud in one week, limited bandwidth."
Correct: **Physical transfer appliance** (AWS Snowball / Azure Data Box / GCP Transfer Appliance).
Why wrong: Internet/egress can't meet the window; appliances ship disks. Networking consideration drives this.

**Trap #6 — Ignoring egress cost in TCO.**
Question: "Compare on-prem vs cloud for a steady 24/7 high-volume workload."
Correct: Include **egress + API + data-transfer fees**; often repatriation or reserved is cheaper.
Why wrong: Candidates pick "cloud is always cheaper" — at massive steady state it isn't, once egress is counted.

**Trap #7 — Regulatory blocks the obvious choice.**
Question: "Move EU/MY customer data to a region with cheapest compute."
Correct: **Blocked by regulatory/data-sovereignty** — must stay in-region.
Why wrong: Cheapest region ≠ allowed region. Regulatory trumps cost.

**Trap #8 — Vendor lock-in from proprietary services.**
Question: "Team wants to avoid being stuck with one provider."
Correct: Use **containers + Terraform + open DB (PostgreSQL)** + Kubernetes.
Why wrong: Deep use of proprietary managed-only services (Lambda+DynamoDB proprietary features) increases lock-in; the exam rewards portable choices.

**Trap #9 — Platform compatibility assumed.**
Question: "Lift an app that needs Windows Server 2008 / old driver."
Correct: Check **target supports the OS/runtime**; else Replatform/Refactor or Retain.
Why wrong: Assuming it "just runs" causes failed migrations.

**Trap #10 — Service availability / region gap.**
Question: "Deploy feature X in the required country region."
Correct: Verify the **service exists in that region** before committing.
Why wrong: Many services are region-scoped; absence = replatform or different provider.

**Trap #11 — Rehost is "cheapest to run."**
Question: "We lift-and-shifted; why is the bill high?"
Correct: Rehost often **costs more if not right-sized/optimised** post-move.
Why wrong: Rehost is cheap to *build* but can be expensive to *run* unoptimised — that's why Replatform follows.

**Trap #12 — Retire without checking dependencies.**
Question: "Decommission the old reporting tool."
Correct: First confirm **no active dependency / legal hold**.
Why wrong: Retire too early = outage. Retain if any dependency remains.

**Trap #13 — "Refactor everything" for a deadline.**
Question: "Tight 2-week deadline to exit data centre."
Correct: **Rehost** (fast, low risk), optimise later.
Why wrong: Refactor takes months; impossible under a hard deadline. Match strategy to timeline.

**Trap #14 — Management overhead ignored.**
Question: "Reduce admin effort for a database."
Correct: **Replatform to managed DB (RDS / Azure DB / Cloud SQL)**.
Why wrong: Rehosting as a raw VM keeps you on the hook for patching/backups — higher management overhead.

**Trap #15 — Environmental consideration dismissed.**
Question: "Company has net-zero carbon goal."
Correct: Public cloud with renewable PUE can help; **repatriation re-incurs power/cooling burden**.
Why wrong: Environmental (power & cooling) is an explicit 2.3 consideration — not a distractor to ignore.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Data-Centre Exit Under Deadline
**Scenario:** A KL retailer must exit its leased data centre in 6 weeks. 120 VMs, 30 TB of active data, 200 TB of cold archival records. Bandwidth to cloud is limited (100 Mbps). Compliance: customer PII must stay in-region (Malaysia/Singapore).
**Question:** Recommend migration type, strategy per tier, and how to move the 200 TB.
**Walkthrough:**
1. **Type:** On-prem → cloud.
2. **Active 30 TB + 120 VMs:** **Rehost** via AWS MGN / Azure Migrate (fast, low risk) to meet the 6-week deadline.
3. **200 TB cold archive:** ship physically with **AWS Snowball / Azure Data Box** (internet can't meet window at 100 Mbps). Store in **archive/cold tier**.
4. **Compliance:** deploy in-region (ap-southeast-1 / Southeast Asia); never foreign region.
5. **Cutover:** blue-green/canary (2.2) via DNS weighted routing.
**Bonus Q:** After cutover the bill is high — what next? → **Replatform** (managed DB, right-size, Reserved Instances) to optimise run cost.

### PBQ 2 — Regulatory Block
**Scenario:** A healthcare provider wants to move patient records to the cheapest public cloud region. Law requires data to remain in the patient's country and meet HIPAA-like controls.
**Question:** What consideration blocks the plan, and what's the correct approach?
**Walkthrough:**
1. **Blocker:** **Regulatory** (data residency) + **Compliance** (HIPAA-attestation region). Cheapest region may be foreign/non-compliant.
2. **Correct:** Choose an in-country region that is compliance-certified; or a **private/hybrid cloud** on-prem for the records, with public cloud only for non-sensitive tiers.
3. **Strategy:** Rehost/Replatform the non-sensitive tier; **Retain** the sensitive system on-prem if no compliant cloud region exists.
**Bonus Q:** What if no in-country compliant region exists? → Retain on-prem or build private cloud; do NOT violate regulation.

### PBQ 3 — Cloud-to-Cloud After Acquisition
**Scenario:** Company A (AWS) is acquired by Company B (Azure shop). 80 workloads on AWS using Lambda + DynamoDB + S3. Must standardise on Azure within 4 months.
**Question:** Recommend strategy and tooling.
**Walkthrough:**
1. **Type:** Cloud → cloud.
2. **Tooling:** **Terraform** (provider-agnostic) re-deploys infra; **Azure Data Box** for S3→Blob bulk.
3. **Strategy:** S3→Blob = Replatform; Lambda→**Azure Functions** = Replatform; deep **DynamoDB** (proprietary) → **Re-architect** to **Cosmos DB**.
4. **Lock-in lesson:** heavy proprietary AWS use made the move harder — future portability via containers + open DB.
**Bonus Q:** Why is DynamoDB harder to move than S3? → S3 has near-equivalent (Blob); DynamoDB's proprietary NoSQL model needs re-architect.

### PBQ 4 — Repatriation for Cost
**Scenario:** A video-processing SaaS runs a steady 24/7 500-node fleet on public cloud. Egress + on-demand cost is unsustainable; workload is predictable, not bursty.
**Question:** Recommend direction and strategy.
**Walkthrough:**
1. **Direction:** **Cloud → on-prem (repatriation)** — predictable high volume is cheaper owned.
2. **Strategy:** **Rehost** the fleet to on-prem VMware/K8s; keep bursty tier on cloud (hybrid).
3. **Considerations:** re-incurs CapEx + power/cooling (environmental) + management overhead; accept because steady-state savings exceed it.
4. **Data:** bulk export via transfer appliance, delta sync, then cutover.
**Bonus Q:** When would you NOT repatriate? → If workload is bursty/spiky (lose elasticity) or no on-prem team exists.

### PBQ 5 — 6 R's Triage
**Scenario:** You assess an estate: (a) a fragile 15-yr-old app no one understands, (b) a duplicate reporting tool replaced by a new dashboard, (c) a customer-facing app needing elastic scale it can't get, (d) a niche on-prem licence server with no cloud equivalent.
**Question:** Assign the correct 6 R to each.
**Walkthrough:**
- (a) fragile legacy → **Rehost** (move as-is, don't touch) OR **Retain** if too risky — but given "move estate," Rehost.
- (b) duplicate/replaced → **Retire** (decommission).
- (c) needs elastic scale → **Refactor** (rewrite cloud-native) or **Re-architect** if keeping language.
- (d) no cloud equivalent, still used → **Retain** (leave on-prem).
**Bonus Q:** What if (a) is also end-of-life and unused? → **Retire**, not Rehost.

### PBQ 6 — Networking/Bandwidth Constraint
**Scenario:** 1 PB dataset, 2-week migration window, 50 Mbps link, strict in-region rule.
**Question:** How to move it and which storage tier?
**Walkthrough:**
1. **Transfer:** physical appliance (Snowball/Data Box/Transfer Appliance) — internet impossible in window.
2. **Tier:** classify — active → standard, cold → cool, compliance-keep → archive.
3. **Region:** in-region only (regulatory).
4. **Cutover:** replicate delta, then DNS cutover with canary.
**Bonus Q:** What if the appliance itself can't cross a border (sovereignty)? → use an in-country appliance or authorised carrier; never export the data.


---

## SECTION 7 — MEMORY AIDS

### 7.1 — The 6 R's mnemonic: "RRRRR R" → remember by effort ladder
**Effort from least → most:**
**Rehost** (none) → **Replatform** (tiny) → **Re-architect** (big redesign, same language) → **Refactor** (full rewrite) → **Retain** (do nothing) / **Retire** (kill it).

**Story:** You're moving house.
- **Rehost** = carry the whole fridge as-is.
- **Replatform** = put the fridge on wheels first (minor tweak).
- **Re-architect** = rebuild the fridge into a modular shelf (redesign, still appliances).
- **Refactor** = throw it out and buy a smart IoT fridge (rewrite).
- **Retain** = leave the old bookshelf at the old house (still using it).
- **Retire** = dump the broken chair (don't need it).

### 7.2 — Direction triad
**"Out, Back, Over"**
- **Out** = on-prem → cloud
- **Back** = cloud → on-prem (repatriation)
- **Over** = cloud → cloud

### 7.3 — "Big data, slow pipe → box it"
500 TB+ with a tight window and limited bandwidth → **physical transfer appliance** (Snowball/Data Box/Transfer Appliance). Internet is the trap.

### 7.4 — Considerations acronym (pick a few that stick)
**"SCP CNV ERCC"** — Storage, Compute, Platform compat, Cost, Networking, Vendor lock-in, Environmental, Regulatory, Compliance, (Management overhead), (Service availability).
Or simpler: **"Store Compute, Platform Cost, Net Vendor, Env Reg Comply, Manage, Avail."**

### 7.5 — Re-architect vs Refactor one-liner
- **Re-architect** = "Redesign, same language."
- **Refactor** = "Rewrite, cloud-native."
If the exam says "keep our C#/.NET," it's **Re-architect**, not Refactor.

### 7.6 — Retain vs Retire one-liner
- **Retain** = "Keep because we still use it / can't move it."
- **Retire** = "Kill because we don't need it."
Dependency or usage → Retain. Redundant → Retire.

### 7.7 — Decision tree (ASCII)
```
Given a workload to migrate:
│
├─ Can't/shouldn't move it? ──────────────► RETAIN
├─ Redundant / replaced / unused? ────────► RETIRE
├─ Move FAST, no changes? ────────────────► REHOST
├─ Move + use managed service, tiny change? ► REPLATFORM
├─ Redesign for cloud, KEEP language? ────► RE-ARCHITECT
└─ Full cloud-native rewrite (serverless/micro)? ► REFACTOR
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| **On-prem→cloud (VM)** | AWS Application Migration Service (MGN), VM Import | Azure Migrate | Migrate to Virtual Machines (Velostrata) |
| **Physical data transfer** | AWS Snowball / Snowcone | Azure Data Box | Transfer Appliance / Offline Disk |
| **Managed DB (Replatform)** | Amazon RDS | Azure Database (SQL/PostgreSQL/MySQL) | Cloud SQL |
| **Object storage** | S3 | Blob Storage | Cloud Storage |
| **Containers (portable)** | EKS / Fargate | AKS / Container Apps | GKE / Cloud Run |
| **Serverless (Refactor)** | Lambda | Azure Functions | Cloud Functions / Cloud Run |
| **IaC (portable)** | CloudFormation / Terraform | ARM / Bicep / Terraform | Deployment Manager / Terraform |
| **Dedicated/On-prem (Repatriation)** | Outposts / VMware Cloud on AWS | Azure Stack HCI / Azure Arc | Anthos (on bare metal) |
| **Network (private link)** | Direct Connect | ExpressRoute | Cloud Interconnect / VPN |
| **CDN** | CloudFront | Azure Front Door / CDN | Cloud CDN |
| **Cold/Archive storage** | S3 Glacier (Instant/Deep Archive) | Azure Blob Cool/Archive | Cloud Storage Nearline/Coldline/Archive |
| **Compliance attestations** | AWS Artifact (SOC 2, PCI, ISO) | Azure Compliance Manager | GCP Compliance Resource / Assured |
| **Open DB (avoid lock-in)** | RDS PostgreSQL / Aurora PostgreSQL | Azure DB for PostgreSQL | Cloud SQL PostgreSQL |
| **Message/event (Re-architect)** | SQS / EventBridge | Service Bus / Event Grid | Pub/Sub / Eventarc |

**Portability rule (vendor lock-in):** Terraform + Kubernetes + PostgreSQL + container images move across all three with the least friction. Deep proprietary services (DynamoDB streams, Lambda-only features, Cosmos unique modes) raise lock-in.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What is Rehost, and when would you use it?**
A: Rehost (lift-and-shift) moves an app to cloud with no code changes. Use it for fast data-centre exit, large estates, or legacy apps you don't want to modify. It's the quickest, lowest-risk strategy but gives no cloud-native benefit.

**Q2: Name the three migration types.**
A: On-premises→cloud, cloud→on-premises (repatriation), and cloud→cloud (provider switch).

**Q3: What's a transfer appliance and why use one?**
A: A physical device (Snowball/Data Box/Transfer Appliance) you ship disks on. Use it when data volume is huge and the network can't meet the migration window.

### 🎓 Cloud Administrator Questions

**Q4: A legacy app needs elastic scale it can't achieve. Re-architect or Refactor?**
A: If we keep the existing language/stack, **Re-architect** (redesign into services). If we rewrite fully cloud-native (serverless/microservices, possibly new language), **Refactor**. Both gain elasticity; Refactor is more invasive.

**Q5: How do you avoid vendor lock-in during migration?**
A: Standardise on portable tech — Terraform for IaC, Kubernetes for orchestration, PostgreSQL (open) for DB, container images. Minimise deep proprietary service dependencies.

**Q6: Customer data must stay in-country. How does that shape the plan?**
A: It's a **regulatory** constraint — deploy only in-region (or retain on-prem/private cloud). It can block the cheapest foreign region and may force Retain or a compliant region choice.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: Client says "cloud is too expensive." What do you check?**
A: Compute TCO including egress, API, and data-transfer fees, plus the dual-run period. For steady 24/7 high volume, reserved capacity or repatriation may beat on-demand. Also right-size and Replatform to managed services.

**Q8: How do you justify Replatform over Rehost to a cost-conscious CFO?**
A: Replatform adds minor effort but cuts ongoing management overhead (no DB patching/backups) and often run cost via managed services, improving TCO without a risky rewrite.

**Q9: Why might a company move BACK from cloud to on-prem?**
A: Predictable, high-volume steady-state workloads where owned hardware + no egress is cheaper; data-sovereignty/air-gap security; or escaping unpredictable cloud fees.

---

## SECTION 10 — FLASHCARDS

**6 R's**
Q: What is Rehost?
A: Lift-and-shift — move an app to cloud with no code changes.

Q: What is Replatform?
A: Lift, tinker, shift — move with minor optimisation (e.g., managed DB), little/no code change.

Q: What is Re-architect?
A: Significantly redesign the app for cloud while keeping the same language/stack.

Q: What is Refactor?
A: Full cloud-native rewrite (microservices/serverless), often new language.

Q: What is Retain?
A: Leave the app as-is; don't migrate it (can't move / still needed).

Q: What is Retire?
A: Decommission the app; don't migrate it (redundant/unused).

Q: Lowest-effort migration strategy?
A: Rehost (no change).

Q: Highest-effort migration strategy?
A: Refactor (full rewrite).

Q: "Move + switch to managed DB, no code edit" = ?
A: Replatform.

Q: "Redesign but keep our .NET code" = ?
A: Re-architect (not Refactor).

**Migration types**
Q: On-premises→cloud is also called?
A: Lift to cloud / cloud migration (most common direction).

Q: Cloud→on-premises is also called?
A: Repatriation / cloud exit.

Q: Cloud→cloud is also called?
A: Cloud-to-cloud migration / provider switch.

Q: Which migration type is often driven by cost at scale?
A: Cloud→on-premises (repatriation).

**Considerations**
Q: Which consideration covers bandwidth, VPN, DNS?
A: Networking.

Q: Which consideration covers power and cooling?
A: Environmental.

Q: Which consideration covers laws on where data lives?
A: Regulatory.

Q: Which consideration covers PCI-DSS / SOC 2 / ISO 27001?
A: Compliance.

Q: Which consideration covers dependency on proprietary APIs?
A: Vendor lock-in.

Q: Which consideration covers CPU/RAM/right-sizing?
A: Compute.

Q: Which consideration covers capacity, IOPS, tiers?
A: Storage.

Q: Which consideration covers who patches/operates it?
A: Management overhead.

Q: Which consideration covers region/service presence + SLA?
A: Service availability.

Q: Which consideration covers TCO, egress, dual-run?
A: Cost.

Q: Which consideration covers OS/runtime support on target?
A: Platform compatibility.

**Tools / mapping**
Q: AWS physical transfer appliance?
A: AWS Snowball / Snowcone.

Q: Azure physical transfer appliance?
A: Azure Data Box.

Q: GCP physical transfer appliance?
A: Transfer Appliance.

Q: AWS VM migration service?
A: AWS Application Migration Service (MGN).

Q: Azure discovery/migration tool?
A: Azure Migrate.

Q: Portable IaC across clouds?
A: Terraform.

Q: Open DB to reduce lock-in?
A: PostgreSQL (RDS / Azure DB / Cloud SQL).

**Traps**
Q: "Migrate" always means to public cloud?
A: No — can be cloud→on-prem or cloud→cloud.

Q: Retire when an app is still depended upon?
A: No — Retain if any dependency/usage remains.

Q: Rehost is cheapest to RUN?
A: Not necessarily — unoptimised Rehost can cost more; Replatform after.

Q: 500 TB, 1-week window, slow link — use internet?
A: No — use a physical transfer appliance.

Q: Cheapest region always allowed?
A: No — regulatory may force in-region only.


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** Which strategy moves an application to the cloud with no code changes?
A) Refactor  B) Replatform  C) Rehost  D) Re-architect
**Answer: C) Rehost.** Lift-and-shift requires no code modification.
*Why others wrong:* Refactor = full rewrite; Replatform = minor change + managed svc; Re-architect = redesign same language.

**E2.** Moving workloads from a public cloud back to an on-premises data centre is called:
A) Repatriation  B) Rehosting  C) Onboarding  D) Refactoring
**Answer: A) Repatriation (cloud exit).**
*Why others wrong:* Rehost = move as-is (direction-agnostic term); onboarding = new setup; refactoring = rewrite.

**E3.** Which consideration covers laws governing where data may be stored?
A) Compliance  B) Regulatory  C) Vendor lock-in  D) Management overhead
**Answer: B) Regulatory.** Compliance = industry/contractual standards; regulatory = laws (e.g., data residency).
*Why others wrong:* Compliance is standards like PCI; lock-in is proprietary dependency; management overhead is who operates it.

**E4.** For 500 TB of data with a one-week window and limited bandwidth, the best transfer method is:
A) Internet upload  B) Physical transfer appliance  C) Email  D) Manual retype
**Answer: B) Physical transfer appliance (Snowball/Data Box).**
*Why others wrong:* Internet can't meet the window at limited bandwidth; email/manual are absurd.

**E5.** Decommissioning an unused, redundant application instead of migrating it is the _____ strategy.
A) Retain  B) Retire  C) Rehost  D) Refactor
**Answer: B) Retire.**
*Why others wrong:* Retain = keep; Rehost = move as-is; Refactor = rewrite.

### MEDIUM (10)

**M1.** A company moves an app to cloud and switches its self-managed MySQL to a managed database service, with no application code changes. This is:
A) Rehost  B) Replatform  C) Refactor  D) Retire
**Answer: B) Replatform.** Moving + minor optimisation (managed DB) with little/no code change.
*Why others wrong:* Rehost = zero change; Refactor = rewrite; Retire = decommission.

**M2.** An organisation wants to avoid being locked to one cloud provider. The BEST choice is:
A) Deep use of proprietary serverless + NoSQL  B) Terraform + Kubernetes + PostgreSQL
C) Provider-only CLI  D) Region-pinned services
**Answer: B) Terraform + Kubernetes + PostgreSQL** — portable, open standards.
*Why others wrong:* Proprietary serverless/NoSQL increases lock-in; provider CLI and region-pinned services reduce portability.

**M3.** A 15-year-old fragile app no one understands must be moved before a lease ends in 3 weeks. Best strategy:
A) Refactor  B) Re-architect  C) Rehost  D) Retire
**Answer: C) Rehost.** Fast, low risk, no changes — fits a tight deadline for a fragile app.
*Why others wrong:* Refactor/Re-architect take months and risk breaking it; Retire only if unused.

**M4.** Customer PII must remain in the customer's country. This is a _____ constraint that may block the cheapest region.
A) Cost  B) Regulatory  C) Networking  D) Environmental
**Answer: B) Regulatory** (data residency law).
*Why others wrong:* Cost favours cheap region; networking = bandwidth; environmental = power/cooling.

**M5.** A steady, 24/7, high-volume workload has become expensive on public cloud. Most likely recommendation:
A) Rehost to bigger instances  B) Repatriate to on-prem  C) Refactor to serverless  D) Retire
**Answer: B) Repatriate (cloud→on-prem).** Predictable high volume is often cheaper owned; avoids egress.
*Why others wrong:* Rehost bigger = more cost; Refactor serverless suits spiky, not steady; Retire only if unused.

**M6.** Which is NOT one of the six listed migration strategies?
A) Rehost  B) Replatform  C) Rebuild  D) Retain
**Answer: C) Rebuild.** The official six are Rehost, Replatform, Re-architect, Refactor, Retain, Retire. "Rebuild" is not the listed term (Refactor/Re-architect cover rewrites).
*Why others wrong:* All three others are in the official list.

**M7.** To reduce database administration effort after a move, choose:
A) Rehost on a raw VM  B) Replatform to managed DB  C) Retain on-prem  D) Retire
**Answer: B) Replatform to managed DB (RDS/Azure DB/Cloud SQL).** Managed service cuts patching/backup toil.
*Why others wrong:* Raw VM keeps admin burden; Retain doesn't move it; Retire removes it.

**M8.** A niche on-prem licence server has no cloud equivalent and is still used. Strategy:
A) Retire  B) Refactor  C) Retain  D) Rehost
**Answer: C) Retain.** Still used, no cloud equivalent — leave it.
*Why others wrong:* Retire = decommission (wrong, it's used); Refactor/Rehost assume a cloud target exists.

**M9.** Redesigning a monolith into services while keeping the original C# language is:
A) Refactor  B) Re-architect  C) Rehost  D) Replatform
**Answer: B) Re-architect.** Significant redesign, same stack.
*Why others wrong:* Refactor implies full rewrite/new language; Rehost = no change; Replatform = minor tweak.

**M10.** Which consideration would make you verify a service exists in the required region before migrating?
A) Cost  B) Service availability  C) Vendor lock-in  D) Platform compatibility
**Answer: B) Service availability.** Region/feature parity must be confirmed.
*Why others wrong:* Cost = spend; lock-in = portability; platform compat = OS/runtime support.

### HARD (5)

**H1.** Company A (AWS: Lambda + DynamoDB + S3) is acquired by an Azure parent. Which component most likely forces a Re-architect rather than Replatform?
A) S3  B) Lambda  C) DynamoDB  D) EC2
**Answer: C) DynamoDB.** Its proprietary NoSQL model has no 1:1 Azure equivalent (Cosmos needs redesign); S3≈Blob and Lambda≈Functions are nearer Replatform.
*Why others wrong:* S3→Blob and Lambda→Functions are closer to Replatform; EC2→Azure VM is Rehost.

**H2.** A green-IT firm with net-zero goals is deciding between expanding its own data centre vs moving to a renewable-powered public cloud. Which 2.3 consideration is decisive?
A) Management overhead  B) Environmental (power & cooling)  C) Vendor lock-in  D) Compliance
**Answer: B) Environmental.** Public cloud renewable PUE shifts power/cooling burden and supports carbon goals; on-prem re-incurs it.
*Why others wrong:* Overhead = ops; lock-in = portability; compliance = attestations — none is the carbon driver.

**H3.** You must migrate 1 PB in 2 weeks over a 50 Mbps link, data must stay in-region, with active + cold tiers. Best combined approach:
A) Internet upload to archive tier  B) Appliance for bulk + in-region + tier classification  C) Retire everything  D) Refactor before moving
**Answer: B) Physical appliance for bulk, deploy in-region, classify active/cold/archive tiers.**
*Why others wrong:* Internet can't meet window; Retire ignores needed data; Refactor is irrelevant to transfer.

**H4.** After a successful Rehost, the monthly bill is higher than on-prem and the app can't auto-scale. The NEXT phase should be:
A) Retire the app  B) Replatform/right-size + Reserved  C) Re-architect immediately  D) Repatriate now
**Answer: B) Replatform (managed services) + right-size + Reserved Instances.** Optimises run cost without a risky rewrite; scale via 3.2.
*Why others wrong:* Retire only if unused; Re-architect is premature; repatriation is a bigger decision not yet justified.

**H5.** A healthcare app must meet HIPAA and keep data in-country, but the cheapest compliant region is at capacity (no quota). Correct action:
A) Deploy in the cheap foreign region anyway  B) Use a non-compliant managed service  C) Retain on-prem or private cloud until compliant capacity exists  D) Refactor to avoid the constraint
**Answer: C) Retain on-prem/private cloud (or wait for in-region quota).** Compliance + regulatory block the foreign/non-compliant path.
*Why others wrong:* A/B violate regulation; D doesn't remove the residency requirement.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Rehost (lift-and-shift) | 🔴 CRITICAL | Most-tested strategy; fast/no-change |
| Replatform | 🔴 CRITICAL | Common scenario (managed DB, minimal change) |
| Refactor vs Re-architect distinction | 🔴 CRITICAL | Classic distractor pair |
| Retain vs Retire | 🟠 HIGH | Easy to confuse; scenario-frequent |
| Migration types (out/back/over) | 🟠 HIGH | "Migrate" direction trap |
| Regulatory / data residency | 🔴 CRITICAL | Blocks the obvious cheap choice |
| Transfer appliance for big data | 🟠 HIGH | Bandwidth/time trap |
| Cost incl. egress / repatriation | 🟠 HIGH | "Cloud cheaper?" misconception |
| Vendor lock-in / portability | 🟡 MEDIUM | Terraform+K8s+PostgreSQL |
| Networking (bandwidth, VPN, DNS) | 🟡 MEDIUM | Drives appliance decision |
| Storage tiers (hot/cool/archive) | 🟡 MEDIUM | "Rarely accessed" → archive |
| Platform compatibility | 🟡 MEDIUM | Legacy OS/runtime check |
| Management overhead | 🟡 MEDIUM | Self vs managed |
| Service availability | 🟡 MEDIUM | Region/feature parity |
| Environmental (power/cooling) | 🟢 LOW | Explicit but rare direct question |
| Compliance (PCI/SOC2/ISO) | 🟠 HIGH | Attestation gate for target |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**Migration types**
- On-prem → cloud (lift to cloud)
- Cloud → on-prem (repatriation / cloud exit)
- Cloud → cloud (provider switch)

**The 6 R's (effort ladder)**
- Rehost = move as-is (no change) — fastest, lowest risk, no cloud benefit
- Replatform = move + minor optimise (managed DB) — little/no code change
- Re-architect = redesign, SAME language — significant, not a rewrite
- Refactor = full cloud-native rewrite (serverless/microservices) — highest effort
- Retain = leave as-is (can't/shouldn't move)
- Retire = decommission (redundant/unused)

**Quick picks**
- "Move fast, no changes" → Rehost
- "Managed DB, tiny change" → Replatform
- "Redesign, keep our code" → Re-architect
- "Rewrite cloud-native" → Refactor
- "Still used / can't move" → Retain
- "Don't need it" → Retire

**Considerations (11)**
Storage · Compute · Platform compatibility · Cost · Networking · Management overhead · Service availability · Vendor lock-in · Environmental (power/cooling) · Regulatory · Compliance

**Exam triggers → answer**
- "Migrate" alone → don't assume TO cloud (could be back/over)
- 500 TB + tight window + slow link → physical appliance (Snowball/Data Box)
- Data must stay in-country → Regulatory blocks foreign region → in-region or Retain
- Avoid lock-in → Terraform + Kubernetes + PostgreSQL
- High steady 24/7 cost → repatriate / reserved
- Reduce DB admin → Replatform to managed DB
- Fragile app + tight deadline → Rehost

**Acronyms**
- RTO = Recovery Time Objective · RPO = Recovery Point Objective
- IaC = Infrastructure as Code · PaaS = Platform as a Service · IaaS = Infrastructure as a Service
- PUE = Power Usage Effectiveness · PDPA = Personal Data Protection Act (MY)
- CDC = Change Data Capture · TCO = Total Cost of Ownership
- VM = Virtual Machine · OS = Operating System · DB = Database
- VPN = Virtual Private Network · DNS = Domain Name System

**Traps recap**
1. "Migrate" ≠ always to cloud. 2. Any backing-service change = Replatform (not Rehost). 3. Re-architect keeps language; Refactor rewrites. 4. Used/depended-on = Retain, not Retire. 5. Big data + slow pipe = appliance, not internet. 6. Count egress in TCO. 7. Regulatory beats cheap region. 8. Portable tech avoids lock-in. 9. Check platform compat. 10. Verify service availability in region. 11. Rehost can cost more to run. 12. Retire only after dependency check. 13. Deadline → Rehost. 14. Less admin → managed (Replatform). 15. Net-zero → environmental (cloud PUE).

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **AWS:** Application Migration Service (MGN) is the standard Rehost tool (Server Migration Service deprecated). Snowball Edge supports compute onboard for edge migration. Graviton (ARM) right-sizing cuts Rehost run cost. Outposts + VMware Cloud on AWS enable repatriation/hybrid.
- **Azure:** Azure Migrate is the unified discovery/assessment/migration hub. Azure Arc extends Azure control to on-prem/other clouds (key for Retain/hybrid). Azure Stack HCI supports repatriation. Data Box family covers offline transfer.
- **GCP:** "Migrate to Virtual Machines" (formerly Velostrata) for Rehost. Cross-cloud with Anthos (on bare metal) for repatriation/hybrid. Transfer Appliance + Storage Transfer Service for bulk.
- **Kubernetes / Containers:** K8s + Terraform are the de-facto portability layer — central to avoiding vendor lock-in (a 2.3 consideration). Managed K8s (EKS/AKS/GKE) common Replatform target.
- **AI-assisted migration:** AWS Migration Evaluator, Azure Migrate assessments, and third-party tools now use ML to recommend the 6 R per app automatically — expect "tool suggests strategy" scenarios.
- **FinOps:** Cloud cost optimisation (rightsizing, Reserved/Savings Plans, egress awareness) is now a first-class discipline — directly tied to the Cost and Repatriation considerations.
- **Sovereign/regulated cloud:** In-region zones (AWS ap-southeast-3 Jakarta, Azure Malaysia region) and "sovereign cloud" offerings grow — reinforcing the Regulatory/Compliance constraints in migration planning.
- **Serverless maturity:** Lambda / Azure Functions / Cloud Run make Refactor (rewrite cloud-native) more attainable for event-driven workloads — Refactor is no longer exotic.

*End of Objective 2.3 notes.*
