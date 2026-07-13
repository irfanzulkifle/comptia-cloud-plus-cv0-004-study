## SECTION 1 — Exam Objectives

- **Objective 1.8 — Summarize cost considerations related to cloud usage.** Part of Domain 1.0 (Cloud Architecture).
- Tests your ability to recognize how cloud costs are generated and the levers you can pull to control them.
- The four sub-objectives:
  - **Billing models** — the four ways you are charged for compute: Dedicated host, Reserved resources, Pay-as-you-go, Spot instances.
  - **Resource metering** — how the provider measures what you used (CPU hours, GB stored, data transferred) to bill you.
  - **Tagging** — labels applied to resources so costs can be grouped, allocated, and reported.
  - **Rightsizing** — matching provisioned capacity to actual need, eliminating waste.
- Core question: *"Are we paying the right amount for the value we get?"*
- The exam gives a scenario and asks which model, tool, or action reduces cost without harming the workload.
- Master the trade-offs (commitment vs. flexibility, discount vs. interruption risk) to pass the cost questions.

---

## SECTION 2 — EXAM KNOWLEDGE

### Dedicated Host
- **Definition:** A physical server dedicated entirely to your organization — no other tenants share the hardware.
- **Why it matters:** Control physical placement of instances; satisfies regulatory/licensing needs (e.g., per-socket software licenses on dedicated hardware).
- **Analogy:** Renting a whole house instead of an apartment — you control the locks, nobody else lives there.
- **Example:** A bank runs an old database licensed "per physical core" on a dedicated host, proving no neighbor shares the box.

### Reserved Resources
- **Definition:** Commit to a fixed amount of capacity (commonly 1 or 3 years) for a significant discount vs. on-demand.
- **Why it matters:** Steady, predictable workloads are far cheaper; trade-off is commitment (you pay whether or not you use it).
- **Analogy:** A monthly transit pass — pay up front, ride as much as you want, cheaper than daily tickets.
- **Example:** 10 web servers running 24/7 reserved for 3 years cuts the bill ~40-60%.

### Pay-as-you-go (On-Demand)
- **Definition:** Pay only for what you consume, billed per second/hour, no commitment, no upfront cost.
- **Why it matters:** Maximum flexibility for short-term/unpredictable/first-time workloads; most expensive per-hour rate.
- **Analogy:** A pay-per-use laundromat — coins only when you wash, but each load costs more than owning a machine.
- **Example:** A two-week prototype test; you pay for exactly 14 days and owe nothing after.

### Spot Instance
- **Definition:** Buy unused capacity at a steep discount (often 70-90% off), but the provider can reclaim it on short notice.
- **Why it matters:** Great for fault-tolerant, interruptible work (batch, rendering, big-data); unsuitable for databases.
- **Analogy:** Standby airline seats — cheap, but you may be bumped if a full-fare passenger shows up.
- **Example:** A studio renders 1,000 video frames overnight; reclaimed nodes simply restart those frames.

### Resource Metering
- **Definition:** The provider measures consumption of each billable resource — compute time, storage GB-months, network egress, API calls.
- **Why it matters:** Metering is the raw data behind every invoice; if unmetered, you cannot bill, budget, or alert.
- **Analogy:** The spinning disk on a taxi meter — records every mile and minute for an accurate fare.
- **Example:** A VM billed 730 hours/month; storage per GB-month; egress per GB sent out.

### Tagging
- **Definition:** Attaching key-value labels (e.g., `Department=Finance`, `Environment=Prod`) to resources.
- **Why it matters:** Powers cost allocation, automation, access control, accountability; without tags a bill is an unexplained lump sum.
- **Analogy:** Color-coding file folders so you instantly see which drawer belongs to which team.
- **Example:** Cost allocation tags reveal Marketing spent $4,200 vs. Engineering $11,000, driving chargeback.

### Rightsizing
- **Definition:** Adjusting specs (CPU, RAM, disk, instance type) to match measured demand — down-size waste, up-size laggards.
- **Why it matters:** Over-provisioning is the #1 source of cloud waste; rightsizing can cut spend 20-40% with no performance loss.
- **Analogy:** Wearing a coat that fits instead of three sizes too big — same warmth, less cost.
- **Example:** A DB VM at 8% CPU right-sized large→medium, halving cost with headroom to spare.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Always-On E-Commerce Backend:** 24/7 storefront buys 3-year Reserved Instances (~55% savings), adds on-demand for Black Friday spikes, then releases them — low steady cost, elastic peaks.
- **Scenario 2 — Nightly Batch Render Farm:** A studio renders on Spot at ~80% off; a reclaimed node's frames are simply re-queued by an on-demand watchdog — cheap, interruption-tolerant throughput.
- **Scenario 3 — Regulated Healthcare Workload:** A hospital uses Dedicated Hosts so no other customer shares hardware, satisfying per-socket licensing and PHI isolation audits.
- **Scenario 4 — Cost-Center Chargeback:** Cost allocation tags reveal a forgotten test env burning $900/month; tagging it leads to its shutdown — a quick, tag-driven win.
- **Scenario 5 — Rightsizing Rescue:** Monitoring shows 10-15% CPU; right-sizing large→medium and moving the DB to reserved drops compute spend 38% in a quarter with zero perf change.

---

## SECTION 4 — COMPARISON TABLES

### Table 1 — Billing Models at a Glance

| Model | Upfront Cost | Flexibility | Discount vs On-Demand | Best For | Risk |
|---|---|---|---|---|---|
| Pay-as-you-go (On-Demand) | None | Highest | 0% (baseline) | Spiky, short, unknown workloads | Most expensive per hour |
| Reserved Resources | Partial or full | Low (committed) | ~40-60% | Steady, predictable baseline | Pay even if unused |
| Spot Instance | None | Medium (can be reclaimed) | ~70-90% | Fault-tolerant batch jobs | Interruption / termination |
| Dedicated Host | High | Low | Little/none (price premium) | Licensing & compliance | Costly; you own the box |

### Table 2 — Reserved vs Dedicated Host

| Factor | Reserved Resources | Dedicated Host |
|---|---|---|
| Tenancy | Shared hardware, reserved capacity | Entire physical server to you |
| License benefit | No special license help | Use your own per-socket licenses |
| Compliance isolation | Logical only | Physical isolation |
| Typical discount | High (40-60%) | Usually a premium, not a discount |
| Use case | Save money on steady load | Meet licensing/regulatory rules |

### Table 3 — On-Demand vs Reserved vs Spot (Decision)

| Question | On-Demand | Reserved | Spot |
|---|---|---|---|
| Is the workload always on? | Maybe | Yes | No |
| Can it tolerate interruption? | Yes | Yes | Yes (required) |
| Do you want zero commitment? | Yes | No | Yes |
| Is cheapest possible rate the goal? | No | Sometimes | Yes |

### Table 4 — Resource Metering Dimensions

| Resource Type | Metered By | Common Unit |
|---|---|---|
| Compute (VM) | Running time | Hours / seconds |
| Block storage | Provisioned or used space | GB-month |
| Object storage | Stored + retrieval | GB-month + requests |
| Network egress | Data leaving cloud | GB transferred |
| Load balancer | Hours + data processed | Hours + GB |

### Table 5 — Tagging Strategy

| Tag Key | Example Value | Used For |
|---|---|---|
| Environment | Prod / Dev / Test | Separate non-prod waste |
| CostCenter | CC-1234 | Chargeback to business unit |
| Owner | jdoe@corp.com | Accountability |
| Project | Atlas | Track initiative spend |

### Table 6 — Rightsizing Levers

| Lever | Action | Typical Saving |
|---|---|---|
| Instance size down | Large → Medium | Up to 50% of that VM |
| Family change | General → Compute-optimized | Better fit, lower cost |
| Storage tier move | Hot → Cold/Archive | 50-90% on storage |
| Schedule off | Stop nights/weekends | ~65% on dev/test |

---

## SECTION 5 — AWS PROVIDER MAPPING

This section maps every CompTIA Cloud+ cost concept to its **AWS-specific** service or feature. AWS is the provider most referenced on the exam for concrete examples.

| CompTIA Concept | AWS Service / Feature |
|---|---|
| Dedicated host | **Amazon EC2 Dedicated Hosts** — physical servers dedicated to your account, supporting bring-your-own-license (BYOL). |
| Reserved resources | **EC2 Reserved Instances (RIs)** and **Savings Plans** — commit to 1- or 3-year terms for discounted rates. |
| Pay-as-you-go | **EC2 On-Demand Instances** — per-second/hour billing, no commitment. |
| Spot instance | **EC2 Spot Instances** — spare capacity at deep discounts, reclaimable with a 2-minute warning. |
| Resource metering | **Amazon CloudWatch** (metrics/usage) and the **AWS Billing & Cost Management Dashboard** (line-item metering). |
| Tagging | **AWS Cost Allocation Tags** (user-defined and AWS-generated) consumed by **Cost Explorer** and **AWS Budgets**. |
| Rightsizing | **AWS Compute Optimizer** (recommendations) and the **Right Sizing** feature in **Cost Explorer**. |

**Extra AWS cost tools worth knowing:**
- **AWS Cost Explorer** — visualize and forecast spend, find rightsizing opportunities.
- **AWS Budgets** — set custom cost/thresholds and get alerts.
- **AWS Trusted Advisor** — flags idle resources and under-utilized EC2 for rightsizing.
- **Savings Plans** — flexible commitment (compute usage) that covers EC2, Fargate, and Lambda, often preferred over RIs for flexibility.

Remember: the exam uses provider-neutral language, but when it shows an AWS screenshot or names a service, these mappings are what connect the concept to the tool.

---

## SECTION 6 — PRACTICE QUESTIONS

**Q1.** Which billing model charges per second with no commitment and is the most expensive per hour?
A. Reserved Instance
B. Dedicated Host
C. Pay-as-you-go (On-Demand)
D. Spot Instance
**Answer:** C — On-demand offers maximum flexibility at the highest hourly rate.

**Q2.** A company wants the cheapest compute for a fault-tolerant rendering job that can restart if interrupted. Which model fits best?
A. Dedicated Host
B. Spot Instance
C. Reserved Instance
D. On-Demand
**Answer:** B — Spot offers 70-90% off but can be reclaimed, ideal for interruptible batch work.

**Q3.** What is the primary reason to use a Dedicated Host?
A. To get the lowest possible price
B. To satisfy per-socket licensing or physical isolation requirements
C. To auto-scale with demand
D. To avoid tagging
**Answer:** B — Dedicated Hosts enable BYOL and physical isolation for compliance.

**Q4.** Reserved capacity is best suited for which type of workload?
A. Unpredictable, one-time testing
B. Steady, always-on baseline load
C. Short nightly batch only
D. Anything that must never be interrupted
**Answer:** B — Committing to steady load yields the biggest reservation discount.

**Q5.** What does "resource metering" primarily provide?
A. A list of security groups
B. The measured usage data behind every bill
C. A backup of virtual machines
D. DNS resolution records
**Answer:** B — Metering records consumption (hours, GB, transfers) that drives billing.

**Q6.** A cloud bill shows a single lump sum with no breakdown by team. What practice was likely missing?
A. Rightsizing
B. Resource metering
C. Tagging
D. Spot purchasing
**Answer:** C — Without cost allocation tags, spend cannot be attributed to teams.

**Q7.** Which action best describes "rightsizing"?
A. Buying more reserved capacity
B. Matching instance size to actual demand
C. Adding tags to resources
D. Switching to a dedicated host
**Answer:** B — Rightsizing eliminates waste by aligning specs with real utilization.

**Q8.** In AWS, which service gives rightsizing recommendations?
A. CloudWatch only
B. AWS Compute Optimizer
C. IAM
D. Route 53
**Answer:** B — Compute Optimizer recommends optimal instance types and sizes.

**Q9.** Cost allocation tags in AWS are consumed by which tool for reporting?
A. EC2
B. Cost Explorer
C. Lambda
D. S3
**Answer:** B — Cost Explorer and AWS Budgets use allocation tags for breakdowns.

**Q10.** A hospital must ensure no other customer shares its physical server due to regulations. Which model?
A. On-Demand
B. Spot
C. Dedicated Host
D. Reserved Instance
**Answer:** C — Dedicated Host provides single-tenant physical isolation.

**Q11.** Which billing model typically carries a price premium rather than a discount?
A. Spot Instance
B. Reserved Instance
C. Dedicated Host
D. On-Demand
**Answer:** C — Dedicated Hosts usually cost more than shared-tenant options.

**Q12.** What is a key risk of Spot Instances?
A. They cannot be automated
B. The provider may terminate them on short notice
C. They require a 3-year commitment
D. They are the most expensive option
**Answer:** B — Spot capacity can be reclaimed, interrupting the workload.

**Q13.** Moving rarely accessed storage from "hot" to "archive" tier is an example of:
A. Tagging
B. Metering
C. Rightsizing storage
D. Dedicated hosting
**Answer:** C — Tiering down is a storage rightsizing cost optimization.

**Q14.** Which AWS feature lets you set custom spend thresholds and receive alerts?
A. Savings Plans
B. AWS Budgets
C. Compute Optimizer
D. Dedicated Hosts
**Answer:** B — AWS Budgets triggers alerts when cost thresholds are breached.

**Q15.** Pay-as-you-go is most appropriate when:
A. Workload is constant for 3 years
B. You need zero commitment and short duration
C. Licensing requires physical isolation
D. You want maximum discount
**Answer:** B — On-demand fits unpredictable, short, commitment-free needs.

**Q16.** The trade-off of Reserved Instances is:
A. Higher hourly rate
B. Commitment cost even if unused
C. Risk of termination
D. No discount available
**Answer:** B — You pay for the committed capacity whether or not you use it.

**Q17.** Which term describes measuring data transferred out of the cloud to the internet?
A. Ingress metering
B. Network egress metering
C. Tagging
D. Rightsizing
**Answer:** B — Egress (data leaving) is a common metered and billable dimension.

**Q18.** A team stops dev/test VMs nights and weekends to save money. This is:
A. Rightsizing by schedule
B. Dedicated hosting
C. Spot purchasing
D. Tagging
**Answer:** A — Scheduling off non-prod resources is a schedule-based rightsizing win.

**Q19.** In AWS, Savings Plans are flexible commitments that can cover EC2, Fargate, and:
A. S3 storage
B. Lambda
C. Dedicated Hosts
D. Route 53
**Answer:** B — Savings Plans apply to compute including Lambda and Fargate.

**Q20.** The first step in the cloud cost-control lifecycle is typically:
A. Rightsizing
B. Choosing a dedicated host
C. Metering (measuring usage)
D. Deleting all resources
**Answer:** C — You must meter usage before you can tag, analyze, and optimize.
