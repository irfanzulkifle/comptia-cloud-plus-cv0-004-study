# CompTIA Cloud+ CV0-004 — Objective 1.2: Service Availability

## SECTION 1 — Exam Objectives

**Objective:** 1.2 — *Explain concepts related to service availability.*

- **Resource availability** — where a service physically runs (Region, AZ, Cloud bursting, Edge, monitoring).
- **Disaster recovery (DR)** — what happens when things break (RTO, RPO, Hot/Warm/Cold sites).
- **Multicloud tenancy** — how to avoid putting all your eggs in one basket.

- **Why it matters:** a 1-hour e-commerce outage can cost millions; wrong DR site overspends or loses data; same-AZ "redundancy" is a single point of failure; multicloud avoids lock-in.
- **Why CompTIA tests it:** RTO/RPO appear on nearly every practice exam and PBQ; it is the foundation for every architecture question.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 Region
- **Definition:** A geographic area (e.g., `ap-southeast-1` Singapore) where a provider clusters two or more data centres into Availability Zones.
- **Why it matters:** chooses where data lives (sovereignty like Malaysia's PDPA), reduces latency, and isolates disasters — data does NOT auto-replicate across regions.
- **Analogy:** A region is like a country — own rules, own power grid; a house in Malaysia does not give you one in Japan.
- **Example:** Malaysian fintech keeps data in `ap-southeast-1` and runs a DR copy in `ap-northeast-1` (Tokyo).

### 2.2 Availability Zone (AZ)
- **Definition:** One or more physically separate data centres in a region with independent power, cooling, and networking, linked by low-latency fibre (<2 ms).
- **Why it matters:** gives in-region high availability and protects against a single building failure; tiny latency enables synchronous replication.
- **Analogy:** Two buildings on the same campus — if one burns, the other runs; a campus-wide typhoon can still hit both.
- **Example:** Web app across 2 AZs + load balancer; RDS Multi-AZ keeps a standby copy in a second AZ.

### 2.3 Cloud Bursting
- **Definition:** A hybrid pattern where on-prem handles normal load and overflow auto-spills to a public cloud when demand exceeds capacity.
- **Why it matters:** public-cloud cost for spiky workloads while keeping baseline on-prem for predictability, sovereignty, low latency.
- **Analogy:** A small home kitchen rents the neighbour's big kitchen only for a raya open house.
- **Example:** Retail site runs on-prem year-round, auto-bursts to AWS EC2 during 12.12 and Hari Raya peaks.

### 2.4 Edge Computing
- **Definition:** Compute and storage near the data source or user (cell towers, PoPs, local sites) instead of a central data centre.
- **Why it matters:** cuts latency, reduces bandwidth, enables offline operation, supports local data sovereignty.
- **Analogy:** A guard at your housing-gate checks visitors locally instead of calling HQ in KL every time.
- **Example:** Penang smart-factory runs AI defect detection on an edge node (sub-10 ms), sending only summary stats to the cloud.

### 2.5 Availability Monitoring
- **Definition:** Continuous observation, measurement, and alerting on uptime, performance, and health (health checks, synthetic tests, RUM, log aggregation, SLA tracking).
- **Why it matters:** detect outages before users, prove SLA compliance, enable automated failover, support capacity planning.
- **Analogy:** CCTV and alarm for your cloud — know the server room is on fire before customers tweet about it.
- **Example:** CloudWatch alarms watch CPU and HTTP error rates; failed AZ health checks pull it from rotation and page on-call in 2 minutes.
- **Key terms:** SLA (contractual uptime, e.g., 99.9%), SLO (internal target), SLI (actual measurement), MTTR (time to fix), MTTD (time to detect).

### 2.6 Recovery Time Objective (RTO)
- **Definition:** The maximum acceptable downtime after a disaster before business impact is unacceptable (measured in time).
- **Why it matters:** drives restore speed and DR site choice; lower RTO = higher cost (hot site, auto-failover).
- **Analogy:** Time to reopen after a flood — 10-minute tolerance needs a ready backup shop; a week allows a bare room.
- **Example:** E-commerce checkout RTO = 5 min; internal wiki RTO = 24 hours.

### 2.7 Recovery Point Objective (RPO)
- **Definition:** The maximum acceptable amount of data loss, measured in time ("how much data can we afford to lose?").
- **Why it matters:** drives backup frequency and replication tech; RPO = 0 needs synchronous replication.
- **Analogy:** How many orders you re-type after a crash — save every second (≈0) loses almost nothing; save daily may redo a day.
- **Example:** Stock trading RPO = 0; CRM RPO = 15 min; email archive RPO = 24 hours.

### 2.8 Hot Site
- **Definition:** A fully operational, continuously synchronized duplicate of production; near-real-time replication, automatic/near-instant failover.
- **Why it matters:** lowest RTO (minutes/seconds) and near-zero RPO for mission-critical workloads; most expensive DR option.
- **Analogy:** A twin restaurant with the same menu and live order mirror — flip the sign and open in seconds.
- **Example:** Hospital system mirrored across two regions with auto-failover; Region B serves patients within minutes, no data loss.

### 2.9 Warm Site
- **Definition:** A partially pre-configured DR site — hardware, network, and base software ready, but NO continuous production data copy.
- **Why it matters:** balances cost and recovery; RTO/RPO in hours; cheaper than hot (no full live duplicate 24/7).
- **Analogy:** A kitchen with stoves and ingredients stocked, but the chef only starts when you call — ready in an hour or two.
- **Example:** HR/ERP with servers pre-built in a second region; restore latest nightly backup, back in ~4 hours.

### 2.10 Cold Site
- **Definition:** A DR site with physical space, power, and networking but no pre-provisioned servers or data; recovery means provisioning hardware and restoring backup.
- **Why it matters:** cheapest DR option; meets offsite-DR compliance; RTO/RPO in days with highest data loss.
- **Analogy:** An empty rented shophouse — keys and wiring, but you bring every stove, ingredient, and recipe from scratch.
- **Example:** Encrypted backups in cloud cold storage + empty rack in another city; restore over 2–3 days.

### 2.11 Multicloud Tenancy
- **Definition:** Using two or more public cloud providers (e.g., AWS + Azure) to host different workloads or replicas.
- **Why it matters:** avoids vendor lock-in, enables best-of-breed, increases resilience (one outage ≠ total outage), gives pricing leverage.
- **Analogy:** Banking with two banks — if one freezes your account, the other still works.
- **Example:** Compute on AWS, ML on Azure, analytics on GCP, federated login + Direct Connect/ExpressRoute linking networks.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Malaysian e-commerce with seasonal spikes (Cloud Bursting + AZs):** core shop on two AZs in `ap-southeast-1` for everyday HA; during 11.11/12.12 a load balancer auto-bursts web servers to AWS EC2 then scales back — no over-provisioning, no Black-Friday downtime.
- **Regional bank DR (Hot site + RTO/RPO):** RTO = 15 min, RPO = 0; active-passive hot site in a second region with synchronous DB replication; on primary power outage, DNS failover shifts traffic in minutes with zero lost transactions.
- **Smart factory (Edge computing + monitoring):** Penang edge nodes run real-time defect detection (sub-10 ms) and buffer data during WAN outages; CloudWatch-style agents report node health and alert upstream before quality drops.
- **Startup cost control (Warm site + multicloud):** production on AWS, non-critical backups replicated to Azure; CRM uses a warm site — pre-built servers in a second region restored from hourly snapshots, RTO ~4 hours, cheaper than a hot site.
- **Government data residency (Region + Cold site):** citizen data only in a Malaysia-region cloud for PDPA; offsite cold site with quarterly backups; RTO in days, acceptable for archival records.

---

## SECTION 4 — COMPARISON TABLES

**Table 1 — RTO vs RPO**
| Aspect | RTO (Recovery Time Objective) | RPO (Recovery Point Objective) |
|--------|-------------------------------|--------------------------------|
| Measures | How long you can be DOWN | How much DATA you can LOSE |
| Unit | Time (min, hr, days) | Time (data-loss window) |
| Drives | DR site speed / failover | Backup frequency / replication |
| Example | "Back in 1 hour" | "Lose at most 15 min of data" |
| Low value cost | Lower RTO = more expensive | Lower RPO = more expensive |

**Table 2 — Hot / Warm / Cold Site**
| Feature | Hot Site | Warm Site | Cold Site |
|---------|----------|-----------|-----------|
| Pre-built hardware | Yes, full | Partial (servers ready) | Space only |
| Live data copy | Continuous | No (restore from backup) | No |
| RTO | Minutes–seconds | Hours | Days |
| RPO | Near-zero | Hours | Days |
| Cost | Highest | Medium | Lowest |
| Best for | Mission-critical | Business-critical | Archival/non-critical |

**Table 3 — Region vs Availability Zone**
| Aspect | Region | Availability Zone (AZ) |
|--------|--------|------------------------|
| Scope | Geographic area (country/area) | Separate DC in same region |
| Failure isolation | Region-wide events | Single-building events |
| Replication | Not automatic | Synchronous feasible (<2 ms) |
| Use | DR, latency, sovereignty | In-region HA |
| Egress cost | High cross-region | Low cross-AZ |

**Table 4 — Cloud Bursting vs Edge Computing**
| Aspect | Cloud Bursting | Edge Computing |
|--------|----------------|----------------|
| Direction | To public cloud when on-prem full | Stays out of central cloud |
| Goal | Handle capacity spikes | Low latency / bandwidth save |
| Data path | Central cloud | Local / near user |
| Example | Retail sale peak | Factory IoT |

**Table 5 — Single vs Multicloud**
| Aspect | Single Cloud | Multicloud |
|--------|--------------|------------|
| Complexity | Low | High |
| Lock-in risk | High | Low |
| Resilience | Provider outage = down | One outage ≠ total down |
| Cost control | One bill | Competition leverage |
| Skill needed | One platform | Multiple platforms |

**Table 6 — SLA / SLO / SLI**
| Term | Meaning | Example |
|------|---------|---------|
| SLA | Contractual uptime promise | 99.9% = 43 min down/yr |
| SLO | Internal target (stricter) | 99.95% |
| SLI | Actual measured value | 99.92% success rate |

---

## SECTION 5 — AWS PROVIDER MAPPING

This objective maps cleanly onto AWS services. Use this table for any AWS-flavoured exam question.

| Objective Concept | AWS Equivalent / Service |
|-------------------|--------------------------|
| **Region** | AWS Region (e.g., `ap-southeast-1` Singapore, `ap-southeast-3` Jakarta) — geographic area; data does not auto-replicate across regions. |
| **Availability Zone (AZ)** | AWS AZ — physically separate data centres in a region, linked by low-latency fibre. Use Multi-AZ for RDS, Auto Scaling groups spread across AZs. |
| **Cloud bursting** | EC2 Auto Scaling to absorb spikes; AWS Outposts extends AWS racks on-prem for a true hybrid burst; or route overflow from on-prem to EC2. |
| **Edge computing** | Amazon CloudFront (CDN edge caches), AWS Local Zones (compute near metro), AWS Wavelength (5G edge), AWS Outposts / Snowball Edge for on-prem/edge processing. |
| **Availability monitoring** | Amazon CloudWatch (metrics, alarms, dashboards), CloudWatch Synthetics (canary tests), AWS X-Ray (tracing), AWS Health Dashboard, Route 53 health checks. |
| **Disaster recovery (DR)** | AWS DR strategies: Backup & Restore, Pilot Light, Warm Standby, Multi-Site (active-active). AWS Elastic Disaster Recovery (CloudEndure) for replication. |
| **RTO / RPO** | RPO via AWS Backup (scheduled), RDS/Aurora automated backups & Multi-AZ, S3 Cross-Region Replication (near-zero RPO); RTO via Auto Scaling, Route 53 failover, Pilot Light/Warm Standby. |
| **Hot site** | AWS Multi-Region active-active with Route 53 health-check failover + Aurora Global Database (sub-second RPO) — lowest RTO/RPO. |
| **Warm site** | AWS Pilot Light (minimal core always running, scale out on failover) or Warm Standby (scaled-down duplicate in second region). |
| **Cold site** | AWS Backup & Restore using S3 / S3 Glacier / Glacier Deep Archive; restore servers from AMIs and data from backup on disaster. |

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which term describes the maximum acceptable downtime after a disaster?
   A. RPO
   B. RTO
   C. SLA
   D. MTTR

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** RTO measures the maximum acceptable downtime after a disaster.
> **Why A is wrong:** RPO is about data loss, not downtime.
> **Why C is wrong:** SLA is the contractual uptime promise.
> **Why D is wrong:** MTTR is time to repair, not the objective.

2. A company can lose at most 15 minutes of data. Which objective defines this?
   A. RTO
   B. SLO
   C. RPO
   D. MTTR

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** RPO is the acceptable data-loss window.
> **Why A is wrong:** RTO is downtime tolerance.
> **Why B is wrong:** SLO is an internal target.
> **Why D is wrong:** MTTR is repair time.

3. What is a geographic area containing multiple data centres called?
   A. Availability Zone
   B. Region
   C. Edge location
   D. Warm site

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A region is the geographic grouping of multiple data centres.
> **Why A is wrong:** An AZ is one or more DCs within a region.
> **Why C is wrong:** Edge location is a CDN/POP near users.
> **Why D is wrong:** Warm site is a DR option.

4. Two physically separate data centres in the same region with independent power are:
   A. Two regions
   B. Availability Zones
   C. Edge nodes
   D. Cloud bursts

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** They are separate Availability Zones in the same region.
> **Why A is wrong:** Two DCs in one region are AZs, not two regions.
> **Why C is wrong:** Edge nodes are for compute near users.
> **Why D is wrong:** Cloud bursting is a capacity pattern.

5. Running on-prem normally but spilling overflow to public cloud during peaks is:
   A. Edge computing
   B. Cloud bursting
   C. Cold site
   D. Multicloud

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** That is the cloud-bursting hybrid pattern (on-prem plus public-cloud overflow).
> **Why A is wrong:** Edge keeps processing out of the central cloud.
> **Why C is wrong:** Cold site is a DR site.
> **Why D is wrong:** Multicloud uses multiple providers.

6. Processing data at a cell tower instead of a central DC is an example of:
   A. Cloud bursting
   B. Edge computing
   C. Hot site
   D. Region failover

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Edge computing keeps processing near the data source or user.
> **Why A is wrong:** Cloud bursting sends overflow to the cloud.
> **Why C is wrong:** Hot site is a DR environment.
> **Why D is wrong:** Region failover is DR, not local processing.

7. A fully mirrored, auto-failover DR environment with near-zero RPO is a:
   A. Cold site
   B. Warm site
   C. Hot site
   D. Pilot light

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Hot site is the fully mirrored, auto-failover live duplicate.
> **Why A is wrong:** Cold site has no live copy.
> **Why B is wrong:** Warm site lacks a live data copy.
> **Why D is wrong:** Pilot light is a minimal always-on core (a warm variant).

8. A DR site with pre-built servers but no live data copy, recovered in hours, is:
   A. Hot site
   B. Warm site
   C. Cold site
   D. Edge site

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Warm site has pre-built servers but needs restore-from-backup.
> **Why A is wrong:** Hot site has a live copy.
> **Why C is wrong:** Cold site has no pre-built servers.
> **Why D is wrong:** Edge site is not a DR tier.

9. The cheapest DR option with RTO measured in days is the:
   A. Hot site
   B. Warm site
   C. Cold site
   D. Multi-site

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Cold site has only space/power, no servers, making it cheapest with RTO in days.
> **Why A is wrong:** Hot site is the most expensive, not cheapest.
> **Why B is wrong:** Warm site has pre-built servers.
> **Why D is wrong:** Multi-site active-active is costly.

10. Using AWS and Azure together to avoid lock-in is an example of:
    A. Single cloud
    B. Multicloud tenancy
    C. Cloud bursting
    D. Edge computing

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Multicloud uses two or more providers together.
> **Why A is wrong:** Single cloud is one provider.
> **Why C is wrong:** Cloud bursting is hybrid capacity.
> **Why D is wrong:** Edge computing is local processing.

11. Which AWS service is used for availability monitoring and alarms?
    A. CloudFront
    B. CloudWatch
    C. S3
    D. Route 53 only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CloudWatch provides metrics, alarms, and availability monitoring.
> **Why A is wrong:** CloudFront is a CDN.
> **Why C is wrong:** S3 is object storage.
> **Why D is wrong:** Route 53 is DNS/health checks, not the main monitoring service.

12. For lowest RTO/RPO on AWS, which design is best?
    A. Backup & Restore to Glacier
    B. Pilot Light
    C. Multi-Region active-active with Aurora Global DB
    D. Cold standby

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Active-active multi-region with Aurora Global DB gives minutes/seconds recovery.
> **Why A is wrong:** Glacier restore is days (cold).
> **Why B is wrong:** Pilot light is minimal, slower than active-active.
> **Why D is wrong:** Cold standby is days.

13. Cloud bursting most directly helps with:
    A. Data residency compliance
    B. Handling unpredictable capacity spikes
    C. Reducing RPO to zero
    D. Eliminating multicloud

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Bursting absorbs demand peaks cheaply without over-provisioning.
> **Why A is wrong:** Bursting does not address residency (single-region).
> **Why C is wrong:** It does not reduce RPO to zero.
> **Why D is wrong:** It is unrelated to multicloud.

14. Edge computing primarily reduces:
    A. Storage cost in the cloud
    B. Latency and bandwidth to central cloud
    C. Number of regions needed
    D. RTO

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Edge cuts latency and raw-data transport to the central cloud.
> **Why A is wrong:** Edge does not primarily reduce cloud storage cost.
> **Why C is wrong:** It does not reduce the number of regions.
> **Why D is wrong:** It does not directly change RTO.

15. An SLA of 99.9% allows roughly how much downtime per year?
    A. 4 minutes
    B. 43 minutes
    C. 8 hours
    D. 1 day

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** 99.9% availability allows roughly 43.8 minutes of downtime per year.
> **Why A is wrong:** 4 minutes is about 99.99%.
> **Why C is wrong:** 8 hours is far lower availability.
> **Why D is wrong:** 1 day is roughly 99.7% unavailable.

16. Which is a valid reason to choose a single region for data?
    A. Automatic cross-region replication
    B. Data sovereignty / residency law
    C. Free egress
    D. Higher RPO

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Regions support compliance with data-residency laws like PDPA.
> **Why A is wrong:** Replication is NOT automatic across regions.
> **Why C is wrong:** Egress is costly, not free.
> **Why D is wrong:** Residency aims to constrain, not raise RPO.

17. Deploying two VMs in the SAME AZ does NOT protect against:
    A. AZ failure
    B. Instance crash
    C. Application bug
    D. OS patch reboot

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** Same-AZ VMs share that AZ's failure domain, so an AZ failure hits both.
> **Why B is wrong:** Instance crash is covered by having two VMs.
> **Why C is wrong:** App bugs are not infrastructure faults.
> **Why D is wrong:** Patch reboots affect one VM; the other stays up.

18. AWS Local Zones and Wavelength are examples of:
    A. Regions
    B. Edge computing offerings
    C. Cold sites
    D. Multicloud tools

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Local Zones and Wavelength bring compute close to users/5G edge.
> **Why A is wrong:** They are not regions.
> **Why C is wrong:** They are not DR cold sites.
> **Why D is wrong:** They are AWS offerings, not multicloud tools.

19. A warm site is generally chosen when:
    A. RTO must be seconds and budget is unlimited
    B. Hours of downtime are tolerable and cost matters
    C. No data loss is allowed
    D. There is no DR requirement

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Warm balances cost with hours-level RTO and recovery.
> **Why A is wrong:** Seconds RTO with unlimited budget = hot site.
> **Why C is wrong:** No data loss allowed = hot site.
> **Why D is wrong:** Every production system should have DR.

20. Multicloud increases resilience because:
    A. One provider outage can still take everything down
    B. A single bill is simpler
    C. One provider's outage does not equal total outage
    D. It removes the need for RTO/RPO

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Spreading providers means one provider's outage is not a total outage.
> **Why A is wrong:** That describes single-cloud risk.
> **Why B is wrong:** Multicloud means multiple bills, not one.
> **Why D is wrong:** You still need RTO/RPO planning.
