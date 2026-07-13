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

# CompTIA Cloud+ CV0-004 — Objective 2.3: Cloud Migration

## SECTION 1 — Exam Objectives

- **Objective Number:** 2.3
- **Objective Name:** *Summarize aspects of cloud migration.*
- **Sub-objectives (cover EVERY one):**
  - **Migration types:** On-premises→cloud, Cloud→on-premises, Cloud→cloud
  - **Resource allocation**
  - **Considerations:** Storage, Platform compatibility, Compute, Cost, Networking, Management overhead, Service availability, Vendor lock-in, Environmental (power & cooling), Regulatory, Compliance
  - **Application migration strategies (the 6 R's):** Rehost, Replatform, Re-architect, Refactor, Retain, Retire
- **What this objective means (beginner-friendly):** Imagine moving house. You decide *where* you move (on-prem→cloud, cloud→on-prem, cloud→cloud = migration types), *what you take and how you pack it* (storage, compute, networking, cost, compliance = resource allocation & considerations), and *how you move each item* (carry as-is = Rehost, put on wheels = Replatform, rebuild = Re-architect/Refactor, keep in storage = Retain, throw out = Retire = the 6 R's).
- **Why it matters:** Most organizations are not cloud-born; they have decades of on-prem systems. Migration is among the largest, riskiest, most expensive IT projects. Wrong strategy wastes money (lift-and-shift a legacy app that should be retired) or causes outages (re-architecting everything at once). Regulatory, compliance, cost, and platform constraints must be respected or you face fines, lock-in, or failed audits.
- **Why CompTIA tests it:** 2.3 is a "summarize" objective — broader and more conceptual. The exam expects recognition of migration terminology and recommendation of the right strategy given a scenario. The 6 R's (especially Rehost vs Replatform vs Refactor vs Retire) are classic distractors.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.3.1 — Migration Types

- **On-Premises → Cloud (lift to cloud):** The most common direction. Workloads, data, and applications move from a company's own data center to a public or private cloud. Drivers: reduce capital expenditure, improve scalability, retire aging hardware, enable remote work. Challenges: data transfer volume, network bandwidth, application refactoring, and cutting over without losing data. Examples: moving an on-prem SQL database to a managed cloud database, or virtual machines to cloud compute.
- **Cloud → On-Premises (repatriation / cloud exit):** Moving workloads back out of the cloud to a private data center or colocation facility. Drivers: unexpected cloud cost overruns, data-sovereignty or regulatory limits, latency/performance needs, loss of control, or vendor-risk reduction. Challenges: reverse data transfer, re-establishing hardware, and losing elastic scalability. CompTIA includes this to show migration is bidirectional.
- **Cloud → Cloud (cloud-to-cloud migration):** Moving workloads between different cloud providers (e.g., provider A to provider B) or between regions/accounts within a provider. Drivers: better pricing, required features, merger/acquisition consolidation, avoiding a single provider, or regional compliance. Challenges: provider-specific service differences, data egress fees, re-architecting for the target provider's primitives, and lock-in.

### 2.3.2 — Resource Allocation

- Resource allocation is the planning of compute, storage, and network capacity needed in the target environment to run migrated workloads successfully.
- You must right-size: too little causes performance degradation and outages; too much wastes money.
- Allocation decisions cover instance/types/sizes, storage tiers (hot/warm/cold), reserved vs on-demand capacity, and network throughput.
- Proper allocation prevents both under-provisioning (failed migrations) and over-provisioning (budget blowout); it is tightly linked to the cost and compute considerations below.

### 2.3.3 — Considerations

- **Storage:** Type (block, file, object), capacity, IOPS/latency needs, tiering, and data-transfer volume. Large datasets may need physical shipment (appliance) rather than network transfer.
- **Platform compatibility:** Will the OS, middleware, libraries, and dependencies run on the target platform? Incompatibilities force replatforming or re-architecting.
- **Compute:** CPU, memory, GPU, and scaling characteristics of the workload; matching instance families and auto-scaling behavior in the target.
- **Cost:** Upfront migration cost, ongoing run-rate, egress/fee structures, licensing, and total cost of ownership. Migrations often reveal hidden cloud costs.
- **Networking:** Bandwidth, latency, VPN/direct connect, IP addressing, DNS, and security groups. Network is frequently the bottleneck and the cause of cutover downtime.
- **Management overhead:** Who operates the environment post-migration? Managed services reduce overhead but may reduce control; self-managed increases overhead.
- **Service availability:** Target SLA/uptime, multi-AZ/region design, and DR posture must meet business RTO/RPO after migration.
- **Vendor lock-in:** Using proprietary managed services eases operations but makes future cloud→cloud or cloud→on-prem moves harder. Balance convenience against exit cost.
- **Environmental (power and cooling):** Moving to cloud can reduce a company's own power/cooling footprint (and carbon), a growing consideration; conversely, on-prem returns increase it.
- **Regulatory:** Laws governing where data may reside and how it is processed (e.g., data-residency requirements) constrain migration direction and target region.
- **Compliance:** Industry frameworks (PCI-DSS, HIPAA, SOC 2, GDPR) must remain satisfied post-migration; evidence, audit trails, and controls must carry over.

### 2.3.4 — Application Migration Strategies (the 6 R's)

- **Rehost (lift-and-shift):** Move the application to the cloud with minimal or no changes — same OS, same app, different hardware. Fastest, lowest-effort, lowest-risk-to-timeline, but captures few cloud benefits. Use when you need speed or a first step.
- **Replatform (lift-and-reshape):** Move with a few cloud optimizations that require minimal code changes — e.g., swapping a self-managed database for a managed cloud database, or moving to containers. Captures some cloud benefits without a full rebuild.
- **Re-architect / Re-architect (sometimes "Re-architect"):** Significantly modify or redesign the application to be cloud-native — microservices, serverless, managed queues. Highest effort and risk but maximum scalability, resilience, and cost efficiency. Use for strategic, long-lived apps.
- **Refactor:** Adjust code (often minor) to better exploit cloud capabilities without full redesign — e.g., tweaking to use a managed cache or message bus. Closely related to replatforming; CompTIA lists both, with refactor implying code-level changes for cloud fit.
- **Retain (revisit / keep):** Leave the application as-is, typically on-prem or in its current location, because it is not worth migrating now (compliance, cost, dependency, or low business value). Often a temporary or deliberate decision within a mixed estate.
- **Retire:** Decommission applications that are redundant, unused, or no longer deliver value. Common discovery during migration assessments — retiring saves cost and reduces attack surface.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **On-prem → cloud rehost:** A regional bank lifts its loan-processing VMs to cloud compute with no code changes over a weekend, gaining elasticity for month-end spikes without buying hardware.
- **Cloud → on-prem repatriation:** A video-rendering studio moves compute-intensive workloads back to its own GPU data center after cloud egress and instance costs exceeded on-prem operation, keeping latency-sensitive rendering local.
- **Cloud → cloud migration:** A company acquired by a competitor standardizes on the parent's cloud provider, migrating its workloads and data to reduce duplicate spend and consolidate tooling.
- **Replatform with managed database:** An e-commerce firm keeps its app code but replaces a self-managed MySQL server with a managed cloud database during migration, cutting operational toil and adding automated backups.
- **Retire + Rehost mix:** A migration assessment finds 40% of legacy apps unused; the team retires them and rehosts the remaining business-critical apps, halving the projected cloud bill.

---

## SECTION 4 — COMPARISON TABLES

**Table 1 — Migration Directions**

| Direction | Common Driver | Key Challenge | Example |
|-----------|---------------|---------------|---------|
| On-prem → cloud | Scalability, capex reduction | Data transfer, cutover | VMs to cloud compute |
| Cloud → on-prem | Cost, sovereignty, latency | Reverse transfer, lost elasticity | Repatriate GPU workloads |
| Cloud → cloud | Pricing, M&A, features | Egress fees, lock-in | Provider A → Provider B |

**Table 2 — The 6 R's**

| Strategy | Change Level | Effort | Cloud Benefit | Use When |
|----------|--------------|--------|---------------|----------|
| Rehost | None | Low | Low | Speed, first step |
| Replatform | Minor | Medium | Medium | Quick optimizations |
| Re-architect | Major redesign | High | High | Strategic apps |
| Refactor | Code tweak | Low–Med | Medium | Fit cloud services |
| Retain | None (keep) | None | None | Not worth moving |
| Retire | Decommission | None | Cost save | Redundant app |

**Table 3 — Key Considerations Map**

| Consideration | Migration Impact |
|---------------|------------------|
| Cost | TCO, egress, licensing |
| Vendor lock-in | Limits future moves |
| Compliance | Must carry post-move |
| Networking | Common cutover bottleneck |
| Regulatory | Constrains region/direction |
| Environmental | Power/cooling footprint |

**Table 4 — Rehost vs Replatform vs Re-architect**

| Aspect | Rehost | Replatform | Re-architect |
|--------|--------|-----------|--------------|
| Code changes | None | Minimal | Significant |
| Speed | Fastest | Moderate | Slowest |
| Risk to timeline | Low | Low–Med | High |
| Long-term savings | Low | Medium | High |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-only services for 2.3 migration aspects:

- **Migration orchestration & discovery:**
  - **AWS Migration Hub** — central tracking of application migrations across AWS tools.
  - **AWS Application Discovery Service** — collects on-prem server/dependency data to plan migration and identify Retain/Retire candidates.
  - **AWS Migration Evaluator** — builds a business case (cost/6-R recommendations).

- **Rehost (lift-and-shift):**
  - **AWS Application Migration Service (MGN)** — automated, agent-based rehost of physical/virtual/cloud servers to EC2.
  - **VM Import/Export** — import on-prem VM images as EC2 instances.
  - **AWS Server Migration Service (SMS)** — legacy agent-based VM migration (superseded by MGN).

- **Replatform / database migration:**
  - **AWS Database Migration Service (DMS)** — migrate databases to managed services (RDS, Aurora) with minimal downtime — a classic replatform enabler.
  - **AWS Schema Conversion Tool (SCT)** — converts source schemas to target engine schemas.

- **Storage / large-data transfer (resource allocation & storage consideration):**
  - **AWS Snowball / Snowcone / Snowmobile** — physical appliances for petabyte-scale migration when network transfer is impractical.
  - **AWS DataSync / Storage Gateway** — online data movement to S3/EFS/FSx.

- **Re-architect / Refactor (cloud-native):**
  - **AWS Lambda, Amazon ECS/EKS, Amazon API Gateway, Amazon SQS/SNS** — building blocks for re-architecting to serverless/microservices.
  - **AWS Elastic Beanstalk** — quick PaaS-style deploy supporting rehost/replatform.

- **Networking (consideration):**
  - **AWS Direct Connect / Site-to-Site VPN** — secure, high-bandwidth paths for migration cutover.

- **Compliance / regulatory (considerations):**
  - **AWS Artifact** — compliance reports and agreements; **AWS Config / CloudTrail** — audit trails that carry compliance post-migration.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Moving virtual machines from a company's own data center to a public cloud with no code changes is which migration type?
   - **A.** Cloud → on-premises
   - **B.** On-premises → cloud
   - **C.** Cloud → cloud
   - **D.** Retire

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** On-premises → cloud (lift-to-cloud) moves VMs out of the company DC to the public cloud with no code changes.

**Why A is wrong:** Cloud → on-premises moves workloads back out of the cloud, the reverse direction.
**Why C is wrong:** Cloud → cloud is between providers/regions, not from a company DC.
**Why D is wrong:** Retire decommissions the app; it doesn't move it.

</details>

2. A firm moves workloads back out of the cloud to its own data center due to cost overruns. This is:
   - **A.** Repatriation (cloud → on-premises)
   - **B.** On-premises → cloud
   - **C.** Cloud → cloud
   - **D.** Rehost

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Repatriation / cloud → on-premises reverses the original move, bringing workloads back to the DC.

**Why B is wrong:** On-premises → cloud is the original move into the cloud, not back out.
**Why C is wrong:** Cloud → cloud is between providers, not back to on-prem.
**Why D is wrong:** Rehost is a 6-R migration method (lift-and-shift), not a direction.

</details>

3. Migrating applications from one cloud provider to another is which type?
   - **A.** On-premises → cloud
   - **B.** Cloud → on-premises
   - **C.** Cloud → cloud
   - **D.** Retain

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Cloud → cloud moves workloads between providers or regions/accounts within a provider.

**Why A is wrong:** On-premises → cloud starts from a company DC, not a cloud.
**Why B is wrong:** Cloud → on-premises ends on-prem, not at another provider.
**Why D is wrong:** Retain leaves the app in place; it doesn't move it between clouds.

</details>

4. Lifting an app to the cloud with no changes is the _____ strategy.
   - **A.** Replatform
   - **B.** Rehost
   - **C.** Refactor
   - **D.** Re-architect

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Rehost (lift-and-shift) moves the app with no code changes — same OS, same app, new hardware.

**Why A is wrong:** Replatform makes minor cloud optimizations, not zero changes.
**Why C is wrong:** Refactor tweaks code to exploit cloud services.
**Why D is wrong:** Re-architect is a major redesign, not a simple lift.

</details>

5. Swapping a self-managed database for a managed cloud database during migration is:
   - **A.** Rehost
   - **B.** Retire
   - **C.** Replatform
   - **D.** Re-architect

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Replatform applies minor cloud optimizations (e.g., managed DB) with minimal code change.

**Why A is wrong:** Rehost moves the DB as-is without swapping to managed.
**Why B is wrong:** Retire decommissions it; this keeps and improves it.
**Why D is wrong:** Re-architect is a full redesign, more than a managed-DB swap.

</details>

6. Redesigning an app as microservices on serverless is which strategy?
   - **A.** Refactor
   - **B.** Rehost
   - **C.** Re-architect
   - **D.** Retain

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Re-architect is a significant redesign to cloud-native (microservices/serverless) for max benefit.

**Why A is wrong:** Refactor is lighter code tweaks, not a full microservices redesign.
**Why B is wrong:** Rehost makes no changes — the opposite of redesign.
**Why D is wrong:** Retain keeps the app as-is, not redesigned.

</details>

7. Decommissioning an unused legacy application during migration is:
   - **A.** Retain
   - **B.** Retire
   - **C.** Rehost
   - **D.** Replatform

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Retire decommissions redundant/unused apps, saving cost and reducing attack surface.

**Why A is wrong:** Retain keeps the app in place, not decommission it.
**Why C is wrong:** Rehost moves it, not removes it.
**Why D is wrong:** Replatform changes it, not decommissions it.

</details>

8. Deliberately leaving an app on-prem because it is not worth migrating is:
   - **A.** Retire
   - **B.** Rehost
   - **C.** Retain
   - **D.** Refactor

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Retain deliberately keeps workloads in place because migration isn't worth it.

**Why A is wrong:** Retire removes the app; this keeps it running.
**Why B is wrong:** Rehost would move it to the cloud.
**Why D is wrong:** Refactor changes code; this leaves it untouched.

</details>

9. Which consideration is most often the cutover bottleneck?
   - **A.** Regulatory
   - **B.** Networking
   - **C.** Environmental
   - **D.** Management overhead

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Networking (bandwidth/latency, VPN/Direct Connect) is frequently the bottleneck and cause of cutover downtime.

**Why A is wrong:** Regulatory constrains region/direction but isn't usually the cutover bottleneck.
**Why C is wrong:** Environmental (power/cooling) is a strategic concern, not a cutover bottleneck.
**Why D is wrong:** Management overhead is post-migration ops, not the cutover blocker.

</details>

10. Using proprietary managed services that make future exits harder illustrates:
   - **A.** Service availability
   - **B.** Vendor lock-in
   - **C.** Resource allocation
   - **D.** Compliance

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Vendor lock-in is the difficulty of leaving a provider because of proprietary managed services.

**Why A is wrong:** Service availability is about uptime/SLA, not exit difficulty.
**Why C is wrong:** Resource allocation is capacity planning, not lock-in.
**Why D is wrong:** Compliance is meeting regulations, not provider dependence.

</details>

11. Data-residency laws that restrict where data may be stored are a _____ constraint.
   - **A.** Cost
   - **B.** Regulatory
   - **C.** Environmental
   - **D.** Platform compatibility

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Regulatory constraints (laws like data-residency) restrict where/how data may be stored.

**Why A is wrong:** Cost is a budget consideration, not a legal residency restriction.
**Why C is wrong:** Environmental covers power/cooling footprint, not data location law.
**Why D is wrong:** Platform compatibility is about OS/dependency fit, not legal residency.

</details>

12. Right-sizing instances to avoid waste or outages is part of:
   - **A.** Resource allocation
   - **B.** Retire
   - **C.** Re-architect
   - **D.** Vendor lock-in

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Resource allocation plans compute/storage/network capacity — right-sizing avoids under/over-provisioning.

**Why B is wrong:** Retire is a 6-R decision, not capacity planning.
**Why C is wrong:** Re-architect is a redesign strategy, not sizing.
**Why D is wrong:** Vendor lock-in is an exit-cost consideration, not sizing.

</details>

13. AWS DMS is primarily used to support which strategy?
   - **A.** Rehost
   - **B.** Retire
   - **C.** Replatform (database)
   - **D.** Retain

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** AWS DMS migrates databases to managed services (RDS/Aurora) with minimal downtime — a classic replatform enabler.

**Why A is wrong:** Rehost of whole VMs is done by MGN/VM Import, not DMS.
**Why B is wrong:** DMS moves data forward, not decommissions it.
**Why D is wrong:** Retain keeps the DB in place; DMS migrates it.

</details>

14. For a petabyte dataset where network transfer is impractical, AWS offers:
   - **A.** DataSync
   - **B.** Snowball
   - **C.** Direct Connect
   - **D.** Migration Hub

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS Snowball/Snowcone/Snowmobile are physical appliances for petabyte-scale transfer when networks are impractical.

**Why A is wrong:** DataSync is online transfer; too slow for a petabyte with impractical network.
**Why C is wrong:** Direct Connect is a network link, not for shipping bulk data physically.
**Why D is wrong:** Migration Hub tracks migrations, it doesn't transfer data.

</details>

15. Which strategy captures the MOST long-term cloud benefit but has the HIGHEST effort?
   - **A.** Rehost
   - **B.** Replatform
   - **C.** Re-architect
   - **D.** Retain

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Re-architect (cloud-native redesign) yields maximum scalability/resilience benefit at the highest effort.

**Why A is wrong:** Rehost is fastest/lowest effort but captures few cloud benefits.
**Why B is wrong:** Replatform is medium effort/benefit, not the highest of either.
**Why D is wrong:** Retain makes no migration effort or benefit.

</details>

16. Minimizing code changes to exploit a managed cache is best described as:
   - **A.** Rehost
   - **B.** Refactor
   - **C.** Retire
   - **D.** Retain

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Refactor adjusts code (e.g., to use a managed cache) to fit cloud services with minimal redesign.

**Why A is wrong:** Rehost makes no code changes at all.
**Why C is wrong:** Retire decommissions the app; this keeps and tweaks it.
**Why D is wrong:** Retain leaves code untouched, not refactored.

</details>

17. AWS Application Discovery Service helps identify candidates for:
   - **A.** Rehost only
   - **B.** Retain and Retire
   - **C.** Re-architect only
   - **D.** Compliance only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Discovery collects dependency/data to reveal which apps to keep (Retain) or drop (Retire) — common assessment output.

**Why A is wrong:** Discovery informs all strategies, not rehost only.
**Why C is wrong:** It isn't limited to re-architect candidates.
**Why D is wrong:** It's an inventory tool, not a compliance assessor.

</details>

18. A merger consolidating onto one provider's cloud is an example of:
   - **A.** On-prem → cloud
   - **B.** Cloud → on-premises
   - **C.** Cloud → cloud
   - **D.** Replatform

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Consolidating onto the parent's provider after M&A moves workloads between clouds — cloud → cloud.

**Why A is wrong:** It starts from a cloud, not on-prem.
**Why B is wrong:** It ends on a cloud, not on-prem.
**Why D is wrong:** Replatform is a 6-R method, not a migration direction.

</details>

19. Which consideration addresses who operates the environment after migration?
   - **A.** Service availability
   - **B.** Management overhead
   - **C.** Platform compatibility
   - **D.** Environmental

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Management overhead covers who operates the environment post-migration (managed vs self-managed).

**Why A is wrong:** Service availability is about SLA/uptime, not operations ownership.
**Why C is wrong:** Platform compatibility is about OS/dependency fit, not operations.
**Why D is wrong:** Environmental is power/cooling footprint, not operations.

</details>

20. Moving to cloud to reduce a company's own power and cooling footprint relates to:
   - **A.** Regulatory
   - **B.** Environmental
   - **C.** Vendor lock-in
   - **D.** Cost

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Environmental consideration covers power/cooling footprint and carbon, reduced by moving to cloud.

**Why A is wrong:** Regulatory is legal/residency, not physical footprint.
**Why C is wrong:** Vendor lock-in is exit cost, unrelated to power/cooling.
**Why D is wrong:** Cost is budget; environmental is the power/cooling-specific angle.

</details>
