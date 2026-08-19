# Objective 2.5 — Given a set of requirements, provision the appropriate cloud resources

> **Domain 2.0 — Deployment (19% of exam)** · Exam CV0-004 V4 · Objectives doc v5.0
> **Status:** Exam-ready · **Version:** 2.0 (enhanced) · Supersedes `full notes/Objective-2.5-Provision-Cloud-Resources.md`

---

## 0. How to use this note

| Pass | What to read | Time |
|---|---|---|
| **1st (learn)** | Sections 1 → 7 in order | ~50 min |
| **2nd (drill)** | Section 2.3 (the constraint hierarchy) + Section 2.4 (the trade-off web) + Section 5 (worked example) | ~20 min |
| **3rd (test)** | Section 10 (practice) + Section 11 (PBQ drills) | ~30 min |
| **Exam eve** | Section 12 (60-second recall sheet) only | ~4 min |

> 📌 **This is the Domain 2 capstone — a synthesis objective.** Each of the eight requirement categories was taught in depth elsewhere; this note is about **combining and trading them off**. If your earlier objectives are solid, the work here is learning the *answering method* in Section 2, not new facts.

---

## 1. Official objective coverage

> **2.5 Given a set of requirements, provision the appropriate cloud resources.**
> - Storage requirements
> - Performance requirements
> - Security requirements
> - Cost requirements
> - Availability requirements
> - Compliance requirements
> - Network requirements
> - Compute requirements

### 1.1 What the verb tells you

**"Given a set of requirements, provision"** — an **application** objective, and the most PBQ-shaped one in Domain 2. You are handed a word problem containing several requirements and must select a configuration that satisfies **all** of them.

**The answering method that scores:**

```text
   ① EXTRACT every stated requirement — there are usually 3-5 hidden
      in the sentences, not a tidy list.
   ② FILTER on the non-negotiables first (compliance, regulatory).
   ③ SATISFY the business-defined targets (availability, security,
      performance).
   ④ OPTIMISE cost WITHIN what remains.
   ⑤ ELIMINATE answers that miss ANY requirement — or that
      overshoot at higher cost.
```

### 1.2 Where these eight were taught

This objective is a re-ask of most of Domain 1. Use this map to revise:

| Requirement | Primary source objective |
|---|---|
| **Storage** | **1.4** — object/block/file, tiers, IOPS vs throughput |
| **Compute** | **1.1** (service models), **1.6/1.7** (containers/VMs), **1.10** (VM/container/serverless) |
| **Network** | **1.3** — VPC, subnets, load balancers, CDN, firewalls |
| **Performance** | **1.10** — latency vs throughput, IOPS, bottleneck analysis |
| **Availability** | **1.2** — regions/AZs, RTO/RPO, nines, hot/warm/cold |
| **Cost** | **1.8** — billing models, right-sizing, tagging |
| **Security** | **4.3/4.4/4.5** — IAM, encryption, network controls |
| **Compliance** | **4.2** — frameworks, residency, audit evidence |

### 1.3 Coverage checklist

- [ ] I can extract **all** requirements from a scenario, including implied ones
- [ ] I know **compliance and regulatory requirements filter first** — they are non-negotiable
- [ ] I know the correct answer is the **cheapest option that meets every requirement**
- [ ] I know an answer that **exceeds** requirements at higher cost is usually **wrong**
- [ ] I can name which requirements **pull against each other**
- [ ] I can convert a vague requirement into a **measurable** one
- [ ] I can size storage, IOPS, and bandwidth from stated numbers
- [ ] I know **security defaults**: least privilege, encryption on, no public access
- [ ] I know availability targets drive **multi-AZ vs multi-region**

---

## 2. The core mental model

### 2.1 Requirements → resources

```text
   REQUIREMENT                    DRIVES                    DECISION
   ─────────────────────────────────────────────────────────────────────
   STORAGE ─────────────► type + tier + durability ──► object / block /
     "10 TB, rarely read"                               file · hot→archive

   COMPUTE ─────────────► model + size + billing ────► VM / container /
     "spiky, event-driven"                              serverless · spot/
                                                        reserved/on-demand

   NETWORK ─────────────► topology + exposure ───────► VPC, public vs
     "no public IPs"                                    private subnets,
                                                        NAT, LB, CDN, VPN

   PERFORMANCE ─────────► capacity + tier ───────────► provisioned IOPS,
     "p95 < 100 ms"                                     instance family,
                                                        cache, CDN

   AVAILABILITY ────────► redundancy + failure ──────► multi-AZ /
     "99.99%, survive an AZ"  domain                    multi-region,
                                                        LB, auto-scaling

   SECURITY ────────────► controls ──────────────────► IAM least
     "encrypted, audited"                               privilege, KMS,
                                                        SG/NACL, logging

   COST ────────────────► the CHEAPEST option that ──► right-size,
     "minimise spend"        still meets ALL of the      reserved/spot,
                             above                       tiering, schedule

   COMPLIANCE ──────────► ★ CONSTRAINS EVERYTHING ───► region lock,
     "PCI DSS, EU only"      ABOVE — applied FIRST      encryption
                                                        mandatory, audit
                                                        trail, isolation
```

### 2.2 Requirements must be measurable

```text
   ✗ VAGUE (not a requirement)        ✓ MEASURABLE (a requirement)
   ───────────────────────────        ────────────────────────────────
   "It must be fast"                  "p95 response under 200 ms"
   "Highly available"                 "99.99% monthly; survive AZ loss"
   "Cheap"                            "Under $4,000/month"
   "Lots of storage"                  "40 TB now, +15% per year, 7-year
                                       retention"
   "Secure"                           "Encrypted at rest and in transit;
                                       no public access; access audited"
   "Scalable"                         "Handle 10× baseline within 5 min"

   ★ If a scenario gives you a NUMBER, it is there to be used —
     it maps to a specific configuration choice.
```

### 2.3 ★ The constraint hierarchy — the order to apply requirements

```text
   ┌──────────────────────────────────────────────────────────────┐
   │  ① COMPLIANCE / REGULATORY        NON-NEGOTIABLE             │
   │     Filters options OUT before anything else is considered.   │
   │     Region lock · mandatory encryption · audit retention ·    │
   │     data residency · required isolation.                      │
   │     ⚠ You cannot trade this away for cost or convenience.    │
   ├──────────────────────────────────────────────────────────────┤
   │  ② SECURITY + AVAILABILITY        BUSINESS-DEFINED FLOORS    │
   │     Minimum acceptable posture and uptime. Design to the      │
   │     stated target — not above it, not below it.               │
   ├──────────────────────────────────────────────────────────────┤
   │  ③ PERFORMANCE                    MEET THE TARGET            │
   │     Enough to satisfy the stated figure, with sensible        │
   │     headroom. Exceeding it costs money for nothing.           │
   ├──────────────────────────────────────────────────────────────┤
   │  ④ COST                           OPTIMISE WITHIN THE REST   │
   │     The LAST filter, applied to whatever still qualifies.     │
   │     ★ Correct answer = CHEAPEST option that meets EVERY       │
   │       requirement above.                                      │
   └──────────────────────────────────────────────────────────────┘
```

> ★ **This hierarchy is the single most useful thing in this objective.** Cost is never optimised *first* — it is optimised *last*, among the options that already satisfy compliance, security, availability, and performance. An answer that is cheapest but breaks a compliance requirement is always wrong; so is an answer that gold-plates availability the scenario never asked for.

### 2.4 The trade-off web — which requirements fight each other

```text
                        COST
                       ╱  │  ╲
              cheaper ╱   │   ╲ cheaper
             means   ╱    │    ╲ means
            less    ╱     │     ╲ less
                   ╱      │      ╲
        AVAILABILITY   PERFORMANCE   SECURITY
             │            │             │
             │            │             │
             └────────────┼─────────────┘
                          │
                     COMPLIANCE
              ★ constrains ALL of them and
                cannot be traded away

   TENSIONS TO RECOGNISE
   ─────────────────────────────────────────────────────────────
   Availability ↔ Cost      each extra nine multiplies redundancy
   Performance  ↔ Cost      provisioned IOPS, bigger instances
   Security     ↔ Cost      dedicated tenancy, HSMs, private links
   Security     ↔ Usability MFA, least privilege, no public access
   Compliance   ↔ Cost      region lock removes cheaper options
   Compliance   ↔ Performance residency may force a distant region

   ALIGNMENTS (rarer, worth spotting)
   ─────────────────────────────────────────────────────────────
   Right-sizing        → cost AND performance both improve
   Caching / CDN       → performance AND cost (less egress) improve
   Managed services    → availability AND ops cost both improve
   Auto-scaling        → cost AND availability both improve
```

---

## 3. The eight requirement categories

For each: **what to ask**, **what it drives**, and the typical decision.

### 3.1 Storage requirements

| Ask | Drives |
|---|---|
| How much data now, and growing how fast? | Capacity and lifecycle planning |
| Structured or unstructured? Shared by many hosts? | **Object / block / file** (see 1.4) |
| How often is it read? | **Hot / warm / cold / archive** tier |
| Random small I/O or large sequential? | **IOPS vs throughput** optimisation |
| How long must it be kept, and must it be immutable? | Retention policy, **object lock/WORM** |
| How durable and how available must it be? | Replication and redundancy options |

**Typical mappings:** shared filesystem for many servers → **file**; VM boot disk or database → **block**; images, backups, logs, data lake → **object**; rarely read but must be retained → **archive tier with a lifecycle policy**.

### 3.2 Compute requirements

| Ask | Drives |
|---|---|
| Long-running or event-driven? | **VM/container vs serverless** (see 1.10) |
| Steady or spiky? | Reserved vs on-demand vs spot (see 1.8) |
| Interruptible? | **Spot** eligibility |
| CPU-bound, memory-bound, or GPU? | **Instance family** selection |
| Legacy, needs OS control? | **VM** |
| Needs OS-level isolation from other tenants? | **Dedicated host** or VM, not a container |

**Typical mappings:** steady 24/7 → **reserved**; bursty/event-driven → **serverless**; fault-tolerant batch → **spot**; microservices with variable load → **containers**; legacy with kernel dependencies → **VM**.

### 3.3 Network requirements

| Ask | Drives |
|---|---|
| Must it be reachable from the internet? | **Public vs private subnet** (see 1.3) |
| Outbound only? | **NAT gateway** |
| Connect to on-premises? | **VPN** or **dedicated connection** |
| Must traffic avoid the internet entirely? | Dedicated connection **+ IPsec if encryption is also required** |
| Global users, static content? | **CDN** |
| Route by URL path? | **L7 load balancer**; static IP or non-HTTP → **L4** |
| Address space and interconnection? | CIDR planning, **no overlaps** |

### 3.4 Performance requirements

| Ask | Drives |
|---|---|
| Latency target (p95/p99)? | Region/edge placement, caching, instance family |
| Throughput target (MB/s, req/s)? | Bandwidth, volume type, parallelism |
| Random small I/O? | **Provisioned IOPS SSD** |
| Large sequential? | **Throughput-optimised** volume |
| Peak vs baseline ratio? | **Auto-scaling** policy and headroom |
| Users far from the region? | **CDN or additional region** — bandwidth will not fix latency |

### 3.5 Security requirements

**The safe defaults — when a scenario is silent, these are almost always the right answer:**

| Control | Default posture |
|---|---|
| Access | **Least privilege**, role-based, MFA on privileged accounts |
| Public exposure | **Blocked** unless explicitly required |
| Encryption at rest | **On**, with managed or customer-managed keys |
| Encryption in transit | **TLS everywhere** |
| Network placement | **Private subnets**, security groups scoped to source |
| Secrets | In a **secret store**, never in code or images (see 2.4) |
| Logging | **Audit logging enabled** and retained |

> ⚠️ **On the exam, the more restrictive option is usually correct** — provided it still meets the functional requirement. "Allow from 0.0.0.0/0" is almost never the answer.

### 3.6 Cost requirements

| Lever | Applies when |
|---|---|
| **Right-size** | Always — from measured utilisation, never from source specs (see 2.3) |
| **Reserved/committed** | Steady, predictable, long-lived workloads |
| **Spot** | Interruptible, restartable, fault-tolerant work |
| **Storage tiering + lifecycle** | Access frequency changes over time |
| **Scheduling** | Non-production outside business hours (~70% saving) |
| **Managed services** | Reduces operational cost, increases unit price |
| **CDN/caching** | Cuts egress and origin load |

### 3.7 Availability requirements

| Stated requirement | Design |
|---|---|
| Single instance acceptable | One AZ — lowest cost, no resilience |
| Survive an **instance** failure | Two+ instances behind a load balancer |
| Survive an **AZ** failure | **Multi-AZ** across two or more zones |
| Survive a **region** failure | **Multi-region** with DNS failover |
| **99.9%** (≈8.76 h/yr) | Multi-AZ, automated recovery |
| **99.99%** (≈52.6 min/yr) | Multi-AZ + auto-scaling + fast automated failover |
| **99.999%** (≈5.26 min/yr) | Multi-region active-active |
| **RPO = 0** | **Synchronous** replication → within a region |
| **RTO in minutes** | Warm standby or active-active |

### 3.8 Compliance requirements

| Requirement | Constrains |
|---|---|
| **Data residency / sovereignty** | The **region** — sometimes the only compliant option |
| **PCI DSS** | Network segmentation, encryption, logging, restricted access |
| **HIPAA** | Encryption, access controls, audit trails, agreements with the provider |
| **GDPR** | Residency, data-subject rights, retention limits, processor terms |
| **SOC 2 / ISO 27001** | Documented controls and **evidence** |
| **Retention / legal hold** | **Immutability (object lock/WORM)** and retention periods |
| **Single tenancy mandated** | **Dedicated host** or private cloud (see 1.8, 2.1) |

> ★ **Compliance is a filter, not a feature.** Apply it first: it removes regions, tenancy models, and services from consideration before you weigh anything else.

---

## 4. Reading the scenario

### 4.1 Requirements are usually implied, not listed

```text
   SCENARIO SENTENCE                        →  REQUIREMENT EXTRACTED

   "a hospital"                             →  COMPLIANCE (HIPAA-class):
                                               encryption, audit, access
   "patient records"                        →  SECURITY + retention
   "10 TB of imaging, rarely accessed
    after 90 days"                          →  STORAGE: object, lifecycle
                                               hot → archive at 90 days
   "must be retained for 7 years"           →  RETENTION + immutability
   "must remain available during
    maintenance"                            →  AVAILABILITY: multi-AZ
   "budget is limited"                      →  COST: cheapest that fits
   "clinicians access it from the hospital
    network only"                           →  NETWORK: private, no
                                               public access, VPN

   ★ Seven requirements from six sentences. The exam hides them
     in ordinary business language.
```

### 4.2 The elimination method

```text
   For each candidate answer, ask in order:

   ① Does it VIOLATE a compliance/regulatory requirement?  → ELIMINATE
   ② Does it FAIL to meet the availability target?          → ELIMINATE
   ③ Does it FAIL a security requirement?                   → ELIMINATE
   ④ Does it FAIL the performance target?                   → ELIMINATE
   ⑤ Of what remains — which is CHEAPEST?                   → ANSWER

   ⚠ Also eliminate answers that EXCEED the requirement at extra
     cost. Multi-region active-active when the scenario asked only
     to survive an AZ failure is the WRONG answer — it is correct
     engineering for a different question.
```

---

## 5. Worked example

> **Scenario.** A regional insurer must store 20 TB of scanned claim documents, growing 20% a year. Documents are read frequently for 60 days, then almost never, but must be retained for 7 years and **must not be alterable**. The processing service runs a few hundred short jobs per day at unpredictable times. Records are subject to a national data-residency law. The claims portal must survive the loss of a data centre but not a whole region. Staff access it only from the corporate network. Budget is tight.

```text
   EXTRACT                          →  DECIDE
   ─────────────────────────────────────────────────────────────────────
   ① "national data-residency law"  →  COMPLIANCE FILTER FIRST:
                                        in-country region ONLY.
                                        Removes cheaper foreign regions
                                        from consideration entirely.

   ② "must not be alterable",
      "retain 7 years"              →  OBJECT LOCK / WORM + 7-year
                                        retention policy

   ③ "20 TB scanned documents",
      "frequent 60 days then rarely"→  OBJECT storage + LIFECYCLE POLICY:
                                        hot → warm at 60 days →
                                        archive later
                                        (NOT block — no in-place edits,
                                         not file — no shared filesystem
                                         need)

   ④ "few hundred short jobs,
      unpredictable times"          →  SERVERLESS (event-driven, bursty,
                                        idle most of the day, scale to
                                        zero)

   ⑤ "survive loss of a data centre
      but not a whole region"       →  MULTI-AZ — explicitly NOT
                                        multi-region. Multi-region here
                                        would OVERSHOOT and cost more.

   ⑥ "staff access from the
      corporate network only"       →  PRIVATE subnets, no public
                                        endpoint, VPN or dedicated
                                        connection from the office,
                                        security groups scoped to the
                                        corporate range

   ⑦ "budget is tight"              →  APPLIED LAST: lifecycle tiering,
                                        serverless instead of idle VMs,
                                        right-sized multi-AZ (not
                                        multi-region), reserved capacity
                                        only for anything steady

   ⑧ implied by "insurer" +
      "claim documents"             →  SECURITY defaults: encryption at
                                        rest and in transit, least
                                        privilege, audit logging
```

**The two decisions that carry the marks:** ⑤ choosing **multi-AZ and not multi-region** — because the requirement stated a data-centre failure, not a regional one — and ① letting **residency filter the region first**, before any cost consideration.

---

## 6. Sizing calculations

Scenarios often contain numbers that map to a specific size.

```text
   ① STORAGE CAPACITY WITH GROWTH
      Year-N size = current × (1 + growth)^N

      20 TB growing 20%/yr, kept 3 years:
        Y1 = 20 × 1.2  = 24 TB
        Y2 = 24 × 1.2  = 28.8 TB
        Y3 = 28.8 × 1.2 = 34.6 TB
      ★ Provision for the RETENTION HORIZON, not today.

   ② IOPS FROM TRANSACTION RATE
      IOPS = transactions/sec × I/O operations per transaction

      2,000 tx/s × 4 I/O each = 8,000 IOPS
      → provisioned-IOPS SSD, not a general-purpose baseline

   ③ THROUGHPUT ↔ IOPS
      Throughput (MB/s) = IOPS × block size (KB) ÷ 1024
      8,000 IOPS at 16 KB ≈ 125 MB/s

   ④ BANDWIDTH FROM DEMAND
      Bandwidth = concurrent users × per-user rate
      5,000 users × 2 Mbps = 10 Gbps  → CDN offload is essential

   ⑤ AVAILABILITY TARGET → DOWNTIME BUDGET   (see 1.2)
      99.9%   → 8.76 h/yr    99.99%  → 52.6 min/yr
      99.95%  → 4.38 h/yr    99.999% → 5.26 min/yr

   ⑥ TRANSFER WINDOW  (see 2.3)
      Time = volume ÷ bandwidth · 1 TB @ 1 Gbps ≈ 2.2 h
      Real throughput ≈ 50-70% → double the estimate
```

---

## 7. Comparison tables

### 7.1 Requirement → resource quick map

| Requirement stated | Provision |
|---|---|
| "Shared filesystem for 10 servers" | **File storage** (RWX) |
| "Database with consistent low latency" | **Block, provisioned-IOPS SSD** |
| "Millions of images served globally" | **Object storage + CDN** |
| "Retain 7 years, must not be altered" | **Object + lifecycle to archive + object lock** |
| "Event-driven, idle most of the day" | **Serverless** |
| "Steady 24/7 production database" | **Reserved instances** |
| "Interruptible nightly batch" | **Spot instances** |
| "Licensed per physical socket" | **Dedicated host** |
| "No public IP addresses" | **Private subnets + NAT gateway** |
| "Connect to the data centre privately" | **VPN or dedicated connection** |
| "Route /api and /shop differently" | **L7 load balancer / Ingress** |
| "Survive an AZ failure" | **Multi-AZ** |
| "Survive a region failure" | **Multi-region + DNS failover** |
| "Zero data loss" | **Synchronous replication (in-region)** |
| "Data must stay in-country" | **Region lock** — compliance filter |
| "Encrypted and auditable" | **Encryption at rest/in transit + audit logging** |
| "Cheapest that meets the above" | Right-size, tier, schedule, commit |

### 7.2 Availability requirement → architecture

| Requirement | Architecture | Relative cost |
|---|---|---|
| No stated requirement / dev | Single instance, single AZ | 💲 |
| Survive instance failure | 2+ instances + load balancer, one AZ | 💲💲 |
| **Survive AZ failure** | **Multi-AZ** + LB + auto-scaling | 💲💲💲 |
| Survive region failure | Multi-region, active-passive + DNS failover | 💲💲💲💲 |
| Near-zero downtime globally | Multi-region **active-active** | 💲💲💲💲💲 |

### 7.3 What "cheapest that meets the requirement" looks like

| Scenario | ✗ Wrong (overshoots) | ✓ Right |
|---|---|---|
| "Survive an AZ failure" | Multi-region active-active | **Multi-AZ** |
| "Rarely accessed after 90 days" | Keep everything in the hot tier | **Lifecycle to cold/archive** |
| "Interruptible batch job" | On-demand instances | **Spot** |
| "Runs a few minutes a day" | Always-on VM | **Serverless** |
| "Steady 24/7 workload" | On-demand | **Reserved** |
| "Internal tool, business hours" | 24/7 running | **Scheduled shutdown** |
| "p95 under 200 ms for local users" | Multi-region deployment | **Single region + caching** |

---

## 8. Exam traps & distractor patterns

| # | Trap | The truth |
|---|---|---|
| 1 | "Pick the most robust option available" | The correct answer is the **cheapest that meets every stated requirement**. Overshooting is wrong |
| 2 | "Multi-region is always safer, so choose it" | If the requirement says **survive an AZ failure**, multi-AZ is correct — multi-region costs more for a requirement nobody asked for |
| 3 | "Optimise cost first" | Cost is the **last** filter, applied to options that already satisfy compliance, security, availability, and performance |
| 4 | "Compliance can be met later" | Compliance is **non-negotiable and applied first** — it removes regions, tenancy models, and services from consideration |
| 5 | "The cheapest answer is the right answer" | Only if it meets **every** requirement. Cheapest-but-non-compliant is always wrong |
| 6 | "Requirements are always listed explicitly" | They are **implied in business language** — "a hospital", "credit cards", "EU citizens" each carry compliance requirements |
| 7 | "'Fast' is a requirement" | Not until it is **quantified**. Look for the number in the scenario — it maps to a configuration |
| 8 | "More IOPS always improves performance" | Only for **random small I/O**. Sequential work is throughput-bound (see 1.10) |
| 9 | "Add bandwidth to fix latency for distant users" | Latency is bounded by distance — use a **CDN or a closer region** |
| 10 | "Encryption and public access are optional if not mentioned" | **Secure defaults apply**: least privilege, encryption on, public access blocked |
| 11 | "RPO = 0 across regions" | Zero data loss needs **synchronous** replication, which in practice means **within a region** |
| 12 | "Size compute from the existing server specs" | Size from **measured utilisation** — copying on-prem specs over-provisions permanently (see 2.3) |
| 13 | "Object storage works for a database" | It cannot modify data in place — databases need **block** |
| 14 | "Spot instances suit any workload that wants to save money" | Only **interruptible, restartable** work |
| 15 | "One answer per requirement" | A single scenario usually needs **several coordinated decisions** — storage *and* compute *and* network *and* availability |

**Disambiguation drill:**

| Pair | The deciding question |
|---|---|
| **Multi-AZ vs multi-region** | Does the requirement name an **AZ/data-centre** failure or a **regional** one? |
| **Reserved vs spot** | Is the workload **steady** or **interruptible**? |
| **Serverless vs VM** | **Event-driven and idle-heavy**, or long-running and steady? |
| **Object vs block** | Write-once/read-many, or **modified in place**? |
| **Hot vs archive tier** | How often is it **actually read**? |
| **Provisioned IOPS vs throughput-optimised** | **Random small** I/O or **large sequential**? |
| **Compliance vs security** | A **legal/framework obligation** vs a **technical control** |

---

## 9. Keyword → answer trigger table

| If you see… | Provision |
|---|---|
| shared by many servers · NFS/SMB | **File storage** |
| database · boot disk · in-place writes | **Block storage** |
| images, backups, logs, unlimited scale | **Object storage** |
| rarely accessed after N days | **Lifecycle policy → cold/archive** |
| must not be alterable · legal hold · 7 years | **Object lock / WORM + retention** |
| event-driven · idle most of the day · unpredictable bursts | **Serverless** |
| steady 24/7 · predictable baseline | **Reserved capacity** |
| interruptible · restartable · batch | **Spot** |
| per-socket licence · single-tenant hardware | **Dedicated host** |
| no public IPs · outbound patching only | **Private subnet + NAT gateway** |
| connect to the corporate data centre | **VPN or dedicated connection** |
| global users, slow static content | **CDN** |
| route by URL path or hostname | **L7 load balancer / Ingress** |
| survive a data-centre/AZ failure | **Multi-AZ** |
| survive a region failure | **Multi-region + DNS failover** |
| zero data loss | **Synchronous replication (in-region)** |
| 99.99% uptime | **Multi-AZ + auto-scaling + automated failover** |
| data must stay in-country · sovereignty | **Region lock — compliance filter first** |
| patient records · card data · EU citizens | **Compliance requirement is implied** |
| encrypted, access-controlled, audited | **Security defaults** |
| minimise cost | **Applied last — cheapest that meets everything else** |

---

## 10. Practice questions

<details>
<summary><b>Q1.</b> A workload must survive the failure of a single data centre but the business explicitly does not require regional resilience. Budget is constrained. What should be provisioned?</summary>

A. Single instance in one AZ · **B. Multi-AZ deployment behind a load balancer** · C. Multi-region active-active · D. Multi-region active-passive

**Correct: B — multi-AZ.** It meets the stated requirement (survive a data-centre/AZ failure) at the lowest cost that does so.
- **A wrong:** Does not survive an AZ failure.
- **C/D wrong:** Both **overshoot** — they solve regional failure, which the scenario explicitly excluded, at significantly higher cost. Exceeding the requirement is a wrong answer.
</details>

<details>
<summary><b>Q2.</b> A scenario states that citizen data must remain within the country, and the cheapest available region is overseas. What is the correct approach?</summary>

**A. Deploy only in the in-country region — compliance filters options before cost is considered** · B. Use the cheapest region and encrypt the data · C. Split data between both regions · D. Use the overseas region with a VPN

**Correct: A.** Regulatory residency is **non-negotiable** and applied first; it removes the cheaper region from consideration entirely.
- **B wrong:** Encryption does not satisfy a residency requirement — the data still physically resides overseas.
- **C wrong:** Any personal data outside the jurisdiction breaches the requirement.
- **D wrong:** A VPN changes the network path, not the data's location.
</details>

<details>
<summary><b>Q3.</b> Documents are read frequently for 60 days, then almost never, but must be kept for 7 years and must not be modifiable. What should be provisioned?</summary>

A. Block storage with snapshots · **B. Object storage with a lifecycle policy transitioning to archive, plus object lock with a 7-year retention** · C. File storage in the hot tier · D. Archive storage from day one

**Correct: B.** Object storage suits write-once/read-many documents; the lifecycle policy handles the changing access pattern; object lock enforces immutability for the retention period.
- **A wrong:** Block storage is for in-place modification and is far more expensive per GB.
- **C wrong:** Keeping seven years of rarely read data in the hot tier wastes money.
- **D wrong:** Archive retrieval takes hours, failing the first 60 days of frequent access.
</details>

<details>
<summary><b>Q4.</b> In what order should requirements be applied when selecting a configuration?</summary>

A. Cost, then performance, then availability, then compliance · **B. Compliance, then security and availability, then performance, then cost** · C. Performance, then cost, then compliance · D. All simultaneously with equal weight

**Correct: B.** Compliance filters options out first; cost is optimised **last**, among options that already qualify.
- **A/C wrong:** Optimising cost before compliance produces answers that are cheap and non-compliant.
- **D wrong:** The requirements have a genuine precedence — some are non-negotiable.
</details>

<details>
<summary><b>Q5.</b> A processing job runs a few hundred times per day at unpredictable moments, taking about eight seconds each. What compute should be provisioned?</summary>

A. A reserved 24/7 virtual machine · **B. Serverless functions** · C. A dedicated host · D. A multi-AZ VM cluster

**Correct: B — serverless.** Event-driven, short-running, and idle most of the day is precisely where scale-to-zero and per-invocation billing win.
- **A wrong:** A reserved VM would bill continuously for a workload that runs a few minutes per day.
- **C wrong:** Dedicated hosts are a premium option for licensing and isolation.
- **D wrong:** Substantial cost for availability the scenario did not request.
</details>

<details>
<summary><b>Q6.</b> An application requires 2,000 transactions per second, each generating 4 storage I/O operations. What IOPS should be provisioned?</summary>

A. 2,000 · **B. 8,000** · C. 500 · D. 16,000

**Correct: B.** IOPS = transactions/sec × I/O per transaction = 2,000 × 4 = **8,000**, which points to a provisioned-IOPS SSD rather than a general-purpose baseline.
- **A wrong:** That counts transactions, not I/O operations.
- **C wrong:** That divides rather than multiplies.
- **D wrong:** That doubles the requirement, over-provisioning at extra cost.
</details>

<details>
<summary><b>Q7.</b> A scenario describes a clinic storing patient records but does not explicitly mention compliance. What should you assume?</summary>

**A. Healthcare data implies regulatory obligations — encryption, access control, audit logging, and retention requirements apply** · B. No compliance requirement exists unless stated · C. Compliance is the provider's responsibility · D. Only encryption is required

**Correct: A.** The exam hides compliance requirements in ordinary business language — "hospital", "patient", "credit card", "EU citizens" all carry obligations.
- **B wrong:** Requirements are frequently implied rather than listed.
- **C wrong:** The customer remains accountable (see 1.1).
- **D wrong:** Encryption alone does not satisfy access control, audit, or retention.
</details>

<details>
<summary><b>Q8.</b> A requirement states "zero data loss in the event of a failure." What does this dictate?</summary>

A. Daily backups · **B. Synchronous replication, which in practice means within a region (across AZs)** · C. Asynchronous cross-region replication · D. Archive-tier storage

**Correct: B.** RPO = 0 requires the write to be committed at both locations before acknowledgement — feasible only at low latency, so within a region.
- **A wrong:** Daily backups permit up to a day of loss.
- **C wrong:** Asynchronous replication by definition allows some loss.
- **D wrong:** Storage tier is unrelated to replication mode.
</details>

<details>
<summary><b>Q9.</b> Which answer would be INCORRECT for a requirement of "99.9% availability, survive an AZ failure, minimise cost"?</summary>

A. Multi-AZ instances behind a load balancer · B. Auto-scaling across two AZs · **C. Multi-region active-active with global load balancing** · D. Multi-AZ managed database

**Correct: C — it is incorrect because it overshoots.** Multi-region active-active targets 99.999% and regional failure, at several times the cost, for a requirement that specified AZ resilience and cost minimisation.
- **A/B/D wrong (as answers to the question):** All three correctly meet the stated requirement.
</details>

<details>
<summary><b>Q10.</b> A requirement reads "the application must be fast." What should be done before provisioning?</summary>

A. Provision the largest available instance · **B. Quantify the requirement — for example a p95 latency target — because an unmeasurable requirement cannot be designed or verified against** · C. Enable auto-scaling and move on · D. Add a CDN regardless

**Correct: B.** Vague requirements must become measurable before they can drive a configuration decision or be validated afterwards.
- **A wrong:** Guessing large over-provisions and may not address the actual bottleneck.
- **C/D wrong:** Both may help, but only after the target and the bottleneck are known (see 1.10).
</details>

<details>
<summary><b>Q11.</b> Staff must access an internal application only from the corporate network, with no internet exposure. What should be provisioned?</summary>

**A. Resources in private subnets, reachable via VPN or a dedicated connection, with security groups scoped to the corporate range** · B. Public subnets with a strong password policy · C. A public load balancer with a WAF · D. Internet gateway with IP allowlisting only

**Correct: A.** No public endpoint at all is the most restrictive configuration that still meets the functional requirement.
- **B/C wrong:** Both expose the application to the internet unnecessarily.
- **D wrong:** Allowlisting reduces but does not remove internet exposure.
</details>

<details>
<summary><b>Q12.</b> Data is 20 TB today and grows 20% annually. What capacity should be planned for a 3-year retention horizon?</summary>

A. 20 TB · B. 26 TB · **C. Approximately 35 TB** · D. 60 TB

**Correct: C.** 20 × 1.2³ ≈ **34.6 TB**. Provision for the retention horizon, not today's footprint.
- **A wrong:** Ignores growth entirely.
- **B wrong:** Accounts for roughly one year only.
- **D wrong:** Substantially over-provisions.
</details>

<details>
<summary><b>Q13.</b> Which pairing of requirement to resource is INCORRECT?</summary>

A. "Shared by 12 servers simultaneously" → file storage · B. "Interruptible nightly batch" → spot instances · **C. "Transactional database" → object storage** · D. "Rarely accessed archives" → archive tier

**Correct: C.** Object storage cannot modify data in place and adds API latency — databases require **block** storage.
- **A/B/D wrong:** All three pairings are correct.
</details>

<details>
<summary><b>Q14.</b> A scenario specifies PCI DSS compliance. Which design elements does this MOST directly require?</summary>

**A. Network segmentation, encryption of cardholder data, restricted access, and comprehensive logging** · B. Multi-region deployment · C. Serverless compute · D. Spot instances for cost control

**Correct: A.** PCI DSS drives segmentation, encryption, access restriction, and auditability.
- **B wrong:** Regional resilience is an availability decision, not a PCI requirement.
- **C/D wrong:** Compute model and purchasing option are not compliance controls.
</details>

<details>
<summary><b>Q15.</b> Users on another continent report slow page loads for static content. Bandwidth utilisation is low. What should be provisioned?</summary>

A. A higher-bandwidth connection · **B. A CDN with edge locations near those users** · C. Provisioned IOPS storage · D. Larger instances

**Correct: B.** This is a **latency** problem caused by distance; caching at the edge is the direct remedy (see 1.10).
- **A wrong:** Bandwidth does not reduce latency, and the link is not saturated.
- **C/D wrong:** Neither addresses geographic round-trip time.
</details>

<details>
<summary><b>Q16.</b> A workload runs steadily 24/7 for at least three years and is already right-sized. Which purchasing option should be provisioned?</summary>

A. On-demand · **B. Reserved or committed-use capacity** · C. Spot · D. Dedicated host

**Correct: B.** Steady, predictable, long-lived load is exactly what commitment discounts reward (see 1.8).
- **A wrong:** The most expensive way to run continuously.
- **C wrong:** The workload is not described as interruptible.
- **D wrong:** A premium option chosen for licensing or isolation, not cost.
</details>

<details>
<summary><b>Q17.</b> Which statement about security requirements in a provisioning scenario is CORRECT?</summary>

A. If security is not mentioned, leave defaults open for simplicity · **B. Apply secure defaults — least privilege, encryption at rest and in transit, no public access, audit logging — unless a requirement explicitly demands otherwise** · C. Security can be added after go-live · D. Encryption alone satisfies most requirements

**Correct: B.** The more restrictive option that still meets the functional requirement is almost always the correct exam answer.
- **A wrong:** Open defaults are rarely correct.
- **C wrong:** Retrofitting security is both riskier and more expensive.
- **D wrong:** Access control, logging, and segmentation are distinct requirements.
</details>

<details>
<summary><b>Q18.</b> Which two requirements MOST directly pull against each other?</summary>

A. Performance and compliance · **B. Availability and cost** · C. Storage and network · D. Compute and compliance

**Correct: B.** Each additional nine of availability multiplies redundancy and therefore cost — the central trade-off in this objective.
- **A wrong:** Compliance may constrain region choice but is not primarily a performance trade.
- **C/D wrong:** Neither pair is a standard tension.
</details>

<details>
<summary><b>Q19.</b> Which requirement, if unmet, invalidates an otherwise perfect solution regardless of cost, performance, or availability?</summary>

A. Cost · B. Performance · **C. Compliance/regulatory** · D. Storage tier

**Correct: C.** Compliance is non-negotiable; a non-compliant design cannot be deployed no matter how well it performs.
- **A/B/D wrong:** All are negotiable trade-offs within compliant options.
</details>

<details>
<summary><b>Q20.</b> A team sizes new cloud instances by copying the CPU and memory of the on-premises servers being replaced. What is the flaw?</summary>

**A. On-premises hardware was sized for a multi-year peak, so copying it over-provisions permanently as a recurring cost** · B. Cloud instances use different CPU architectures · C. Cloud providers do not offer matching sizes · D. There is no flaw

**Correct: A.** Right-size from **measured utilisation** instead (see 2.3, 1.8).
- **B wrong:** Comparable families exist; that is not the issue.
- **C wrong:** Equivalent sizes are generally available.
- **D wrong:** This is one of the most common and expensive provisioning errors.
</details>

<details>
<summary><b>Q21.</b> A requirement states 5,000 concurrent users each consuming approximately 2 Mbps of video. What is the implication?</summary>

**A. Roughly 10 Gbps of egress — a CDN should absorb most of it to control cost and origin load** · B. 10 Mbps total · C. Provisioned IOPS is the constraint · D. Compute is the constraint

**Correct: A.** 5,000 × 2 Mbps = **10 Gbps**, which is both a bandwidth and an egress-cost problem best addressed by a CDN (see 1.3, 1.8).
- **B wrong:** Misapplies the multiplication.
- **C/D wrong:** The constraint described is network egress, not storage or compute.
</details>

<details>
<summary><b>Q22.</b> Which action correctly reflects "minimise cost" as a requirement?</summary>

A. Choose the cheapest option available regardless of other requirements · **B. Choose the cheapest option that still satisfies every other stated requirement** · C. Always choose serverless · D. Always choose spot instances

**Correct: B.** Cost is the final filter applied to already-qualifying options.
- **A wrong:** Cheapest-but-non-compliant or cheapest-but-unavailable is always wrong.
- **C/D wrong:** Both are cheapest only for specific workload profiles.
</details>

<details>
<summary><b>Q23.</b> An internal reporting tool is used 09:00–17:00 on weekdays and currently runs continuously. Which provisioning change best meets a cost requirement?</summary>

A. Move to spot instances · **B. Schedule automatic shutdown outside business hours** · C. Buy three-year reservations · D. Move to a dedicated host

**Correct: B.** Roughly 50 of 168 hours per week are needed — about a **70% saving** with no architectural change.
- **A wrong:** Interactive interactive workloads tolerate interruption poorly.
- **C wrong:** Reserving capacity that is idle 70% of the time is the wrong direction.
- **D wrong:** That increases cost.
</details>

<details>
<summary><b>Q24.</b> Which set of decisions would satisfy "survive an AZ failure, RPO of zero, minimise cost"?</summary>

**A. Multi-AZ deployment with synchronous replication between zones** · B. Multi-region with asynchronous replication · C. Single AZ with hourly snapshots · D. Multi-region active-active

**Correct: A.** Multi-AZ meets the failure-domain requirement, synchronous replication within a region delivers RPO 0, and it is the cheapest configuration that does both.
- **B wrong:** Asynchronous replication cannot deliver RPO 0.
- **C wrong:** Snapshots permit up to an hour of loss and do not survive an AZ failure.
- **D wrong:** Overshoots the requirement at much higher cost.
</details>

<details>
<summary><b>Q25.</b> What is the BEST general method for answering a provisioning scenario?</summary>

A. Select the most technically sophisticated option · **B. Extract all requirements including implied ones, eliminate answers that violate any, then choose the cheapest of those remaining** · C. Choose the option with the most services · D. Prioritise cost above all else

**Correct: B.** Requirements are frequently implied; elimination against every requirement, with cost applied last, is the reliable method.
- **A/C wrong:** Sophistication and service count are not correctness criteria — and overshooting is penalised.
- **D wrong:** Cost is the final filter, not the first.
</details>

---

## 11. PBQ-style drills

### Drill A — Extract the requirements

> "A national bank must retain 5 years of transaction logs. Logs are queried daily for the first month, then only during audits. Regulators require the data stay in-country and be tamper-proof. The trading platform must not lose a single transaction and must keep running if a data centre fails. Staff connect from branch offices. Reduce spend where possible."

List every requirement and the resource decision it drives.

<details><summary>Answers</summary>

| Requirement extracted | Decision |
|---|---|
| **Compliance:** in-country residency | **Region lock — applied first** |
| **Compliance:** tamper-proof, 5-year retention | **Object lock/WORM + 5-year retention policy** |
| **Storage:** daily for a month, then rare | **Object storage + lifecycle: hot → cold/archive at ~30 days** |
| **Availability:** "not lose a single transaction" | **RPO = 0 → synchronous replication (in-region)** |
| **Availability:** survive a data-centre failure | **Multi-AZ — not multi-region** |
| **Network:** branch office access | **VPN or dedicated connection; private subnets** |
| **Security:** implied by "bank"/transaction data | **Encryption at rest and in transit, least privilege, audit logging** |
| **Cost:** "reduce spend where possible" | **Applied last** — lifecycle tiering, right-sizing, reservations for steady load |

**The trap:** "not lose a single transaction" is an **RPO** statement (synchronous, in-region), while "keep running if a data centre fails" is an **availability** statement (multi-AZ). They are separate requirements from one sentence each.
</details>

### Drill B — Eliminate the wrong answers

> **Requirement:** survive an AZ failure · RPO of 15 minutes · data must remain in-country · minimise cost.

| # | Candidate | Verdict? |
|---|---|---|
| 1 | Single AZ, hourly snapshots, in-country region | |
| 2 | Multi-AZ, asynchronous replication (5-min lag), in-country | |
| 3 | Multi-region active-active, overseas secondary | |
| 4 | Multi-AZ, synchronous replication, in-country | |

<details><summary>Answers</summary>

1 → **ELIMINATE** — fails AZ resilience, and hourly snapshots exceed a 15-minute RPO
2 → **✓ CORRECT** — meets AZ resilience, meets RPO (5 min < 15 min), stays in-country, and is the cheapest qualifying option
3 → **ELIMINATE** — the overseas secondary **violates residency**, and it overshoots on cost
4 → Meets all requirements but **costs more than necessary** — synchronous replication exceeds a 15-minute RPO target. Correct engineering, wrong answer for "minimise cost"

**The lesson:** option 4 shows that even a *technically valid* answer loses to a cheaper one that still meets every stated requirement.
</details>

### Drill C — Match requirement to provision

| # | Requirement | Provision? |
|---|---|---|
| 1 | 12 render nodes need the same project files simultaneously | |
| 2 | Nightly job, tolerates interruption, cheapest possible | |
| 3 | Must survive loss of an entire region | |
| 4 | No inbound internet connections, but must fetch OS patches | |
| 5 | Database with 12,000 IOPS of random reads | |
| 6 | Software licensed per physical CPU socket | |
| 7 | 200 GB of logs, read only during quarterly audits | |

<details><summary>Answers</summary>

1 → **File storage, ReadWriteMany** · 2 → **Spot instances** · 3 → **Multi-region + DNS failover** · 4 → **Private subnet + NAT gateway** · 5 → **Provisioned-IOPS SSD block storage** · 6 → **Dedicated host** · 7 → **Object storage, cold/archive tier**
</details>

### Drill D — Sizing

1. 8 TB growing 25% per year, retained 4 years — capacity to plan for?
2. 1,500 transactions/sec at 6 I/O each — IOPS?
3. That IOPS figure at a 32 KB block size — throughput?
4. A 99.99% target — annual downtime budget?

<details><summary>Answers</summary>

1. 8 × 1.25⁴ ≈ **19.5 TB**
2. 1,500 × 6 = **9,000 IOPS**
3. 9,000 × 32 ÷ 1024 ≈ **281 MB/s**
4. **≈52.6 minutes per year** (≈4.32 minutes per month)
</details>

---

## 12. 60-second recall sheet

```text
╔══════════════════════════════════════════════════════════════════════╗
║  2.5 — PROVISION FROM REQUIREMENTS  (Domain 2 capstone · PBQ-shaped) ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ THE METHOD                                                        ║
║   ① EXTRACT every requirement — many are IMPLIED, not listed         ║
║      ("hospital"/"card data"/"EU citizens" = COMPLIANCE)             ║
║   ② FILTER on COMPLIANCE/REGULATORY — NON-NEGOTIABLE, FIRST          ║
║   ③ MEET security + availability targets (as stated, not above)      ║
║   ④ MEET the performance target                                      ║
║   ⑤ THEN pick the CHEAPEST option that satisfies ALL of the above    ║
║   ⚠ ELIMINATE answers that OVERSHOOT at higher cost —                ║
║     multi-region when only AZ resilience was asked is WRONG          ║
╠══════════════════════════════════════════════════════════════════════╣
║  TRADE-OFFS   availability ↔ cost · performance ↔ cost ·             ║
║               security ↔ cost/usability · COMPLIANCE constrains ALL  ║
║  ALIGNMENTS   right-sizing, caching/CDN, managed services and        ║
║               auto-scaling improve TWO dimensions at once            ║
╠══════════════════════════════════════════════════════════════════════╣
║  AVAILABILITY → ARCHITECTURE                                         ║
║   instance failure → 2+ behind an LB                                 ║
║   AZ failure       → MULTI-AZ            99.9% → 8.76 h/yr           ║
║   REGION failure   → MULTI-REGION + DNS  99.99% → 52.6 min/yr        ║
║   RPO = 0          → SYNCHRONOUS → IN-REGION ONLY                    ║
╠══════════════════════════════════════════════════════════════════════╣
║  QUICK MAP  shared by many hosts → FILE · database/boot → BLOCK ·    ║
║   images/backups/logs → OBJECT · rarely read → LIFECYCLE to ARCHIVE ·║
║   immutable retention → OBJECT LOCK/WORM · event-driven/idle →       ║
║   SERVERLESS · steady 24/7 → RESERVED · interruptible → SPOT ·       ║
║   per-socket licence → DEDICATED HOST · no public IP → PRIVATE       ║
║   SUBNET + NAT · corporate access → VPN/DEDICATED · global static →  ║
║   CDN · path routing → L7 LB · residency → REGION LOCK               ║
╠══════════════════════════════════════════════════════════════════════╣
║  SECURITY DEFAULTS when unstated: LEAST PRIVILEGE · ENCRYPT at rest  ║
║   AND in transit · NO PUBLIC ACCESS · private subnets · secrets in a ║
║   secret store · AUDIT LOGGING ON                                    ║
╠══════════════════════════════════════════════════════════════════════╣
║  SIZING  capacity = current × (1+growth)^years                       ║
║          IOPS = tx/sec × I/O per tx                                  ║
║          throughput MB/s = IOPS × block KB ÷ 1024                    ║
║          bandwidth = users × per-user rate                           ║
║   ⚠ Size compute from MEASURED utilisation, never from on-prem specs ║
║   ⚠ "Fast"/"cheap"/"scalable" are NOT requirements until QUANTIFIED  ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 13. Cross-references

| Related objective | Connection |
|---|---|
| **1.1 Service models** | The compute requirement often resolves to IaaS vs PaaS vs SaaS vs FaaS |
| **1.2 Service availability** | Availability requirements → regions, AZs, RTO/RPO, the nines table |
| **1.3 Cloud networking** | Network requirements → subnets, gateways, load balancers, CDN, VPN |
| **1.4 Storage** | Storage requirements → object/block/file, tiers, IOPS vs throughput |
| **1.8 Cost considerations** | Cost requirements → billing models, right-sizing, tiering, scheduling |
| **1.10 Optimizing workloads** | Performance requirements → bottleneck analysis, latency vs throughput |
| **2.1 Deployment models** | Compliance and isolation requirements may force private or community |
| **2.3 Cloud migration** | The same eleven considerations, applied when moving rather than building |
| **2.4 Code to deploy** | The chosen configuration is **provisioned as IaC**, with tags applied automatically |
| **4.2 Compliance** | Frameworks, residency, and audit evidence — the filter applied first |
| **4.3–4.5 Security** | IAM, encryption, and network controls — the secure defaults |

> 🔑 **Carry this forward:** the correct provisioning answer is always the **cheapest configuration that satisfies every stated requirement** — no cheaper, and no more elaborate. Compliance filters first; cost decides last.

---

*Source of truth: CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0. Sizing formulas are standard capacity-planning arithmetic. Product names are illustrative; the exam is vendor-neutral.*
