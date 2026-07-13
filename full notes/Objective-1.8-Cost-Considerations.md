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

1. A workload runs 24/7 for 3 years with steady state. The LOWEST long-term cost model is:
   - **A.** On-Demand
   - **B.** Reserved Instances (1- or 3-yr)
   - **C.** Spot
   - **D.** Dedicated Host

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Reservations give steep discounts for steady, long-lived usage vs On-Demand.

**Why A is wrong:** On-Demand is most expensive for steady 24/7.
**Why C is wrong:** Spot is for interruptible, not steady.
**Why D is wrong:** Dedicated Host is premium.

</details>

2. A batch job can tolerate interruption and needs the cheapest compute. Use:
   - **A.** On-Demand
   - **B.** Spot Instances
   - **C.** Reserved
   - **D.** Dedicated Host

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Spot uses spare capacity at large discounts and can be reclaimed, ideal for fault-tolerant batch.

**Why A is wrong:** On-Demand costs more.
**Why C is wrong:** Reserved is for steady use.
**Why D is wrong:** Dedicated is premium.

</details>

3. You must comply with licensing that requires a physical dedicated server. Which model?
   - **A.** Shared On-Demand
   - **B.** Dedicated Host
   - **C.** Spot
   - **D.** Lambda

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Dedicated Hosts give you a physical server dedicated to you, satisfying per-socket/core license rules.

**Why A is wrong:** Shared violates dedicated licensing.
**Why C is wrong:** Spot is shared.
**Why D is wrong:** Lambda is shared multitenant.

</details>

4. The PRIMARY lever to reduce a runaway AWS bill is first:
   - **A.** Delete the account
   - **B.** Identify what is driving cost via billing/Cost Explorer tagging
   - **C.** Buy more RI blindly
   - **D.** Disable CloudTrail

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** You cannot optimize what you cannot see; tag resources and analyze spend with Cost Explorer.

**Why A is wrong:** Destructive and wrong.
**Why C is wrong:** Blind RI buying wastes money.
**Why D is wrong:** Disabling logging hides the problem.

</details>

5. Which service shows cost and usage over time with filtering by tag/service?
   - **A.** CloudTrail
   - **B.** AWS Cost Explorer
   - **C.** GuardDuty
   - **D.** Trusted Advisor (only)

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Cost Explorer visualizes spend by service, tag, and time to find optimization targets.

**Why A is wrong:** CloudTrail is API audit.
**Why C is wrong:** GuardDuty is threat detection.
**Why D is wrong:** Trusted Advisor advises but Cost Explorer is the analytics tool.

</details>

6. Rightsizing means:
   - **A.** Buying the biggest instance
   - **B.** Matching instance type/size to actual utilization to avoid waste
   - **C.** Disabling monitoring
   - **D.** Always using On-Demand

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Rightsizing selects the cheapest instance that meets performance, eliminating over-provisioning.

**Why A is wrong:** Oversized wastes money.
**Why C is wrong:** Monitoring helps rightsizing.
**Why D is wrong:** On-Demand is not a sizing strategy.

</details>

7. A tag is used in cost management to:
   - **A.** Encrypt data
   - **B.** Attribute spend to a team/project/environment
   - **C.** Set IAM
   - **D.** Route traffic

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Cost allocation tags group and attribute costs so each team/project is accountable.

**Why A is wrong:** Tags don't encrypt.
**Why C is wrong:** IAM is separate.
**Why D is wrong:** Routing is network.

</details>

8. Compute Savings Plans provide discounts in exchange for:
   - **A.** A fixed region lock only
   - **B.** A committed $/hr spend on compute, flexible across instance families
   - **C.** Buying hardware
   - **D.** Disabling Auto Scaling

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Savings Plans give discounts for a $/hr commitment and are flexible across instance types/regions.

**Why A is wrong:** They are more flexible than RIs.
**Why C is wrong:** No hardware purchase.
**Why D is wrong:** Auto Scaling still works.

</details>

9. Which is a CAPEX-like option in an otherwise OPEX cloud?
   - **A.** On-Demand
   - **B.** Reserved/Committed Use (long-term commitment)
   - **C.** Spot
   - **D.** Lambda

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Long-term commitments behave like CAPEX planning (predictable, upfront) within OPEX billing.

**Why A is wrong:** On-Demand is pure OPEX variable.
**Why C is wrong:** Spot is variable.
**Why D is wrong:** Lambda is per-use.

</details>

10. A dev environment used 8 hours/day, 5 days/week should avoid:
   - **A.** Stopping instances off-hours
   - **B.** Running 24/7 On-Demand year-round
   - **C.** Scheduled scaling
   - **D.** Smaller instances

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Leaving dev running nights/weekends wastes ~70% of the bill; schedule stop/start.

**Why A is wrong:** Stopping saves money.
**Why C is wrong:** Scheduling helps.
**Why D is wrong:** Right-sizing helps.

</details>

11. EBS volumes left attached to stopped instances still:
   - **A.** Cost nothing
   - **B.** Incurs storage cost
   - **C.** Auto-delete
   - **D.** Become free

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** You pay for provisioned EBS storage even when the instance is stopped.

**Why A is wrong:** Storage is billed regardless of instance state.
**Why C is wrong:** Not auto-deleted.
**Why D is wrong:** Not free.

</details>

12. Data egress (out to internet) is generally:
   - **A.** Free
   - **B.** Charged per GB
   - **C.** Included in instance price
   - **D.** Always banned

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS charges for data transfer OUT to the internet; intra-AZ/in is often free.

**Why A is wrong:** Egress is billable.
**Why C is wrong:** Not bundled.
**Why D is wrong:** Allowed, not banned.

</details>

13. Which helps detect idle/underused resources to cut cost?
   - **A.** CloudTrail only
   - **B.** Trusted Advisor / Cost and Usage reports
   - **C.** Route 53
   - **D.** NAT gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Trusted Advisor flags idle resources (unused EIPs, low-utilization EC2) for savings.

**Why A is wrong:** CloudTrail is API logs.
**Why C is wrong:** DNS.
**Why D is wrong:** Egress component.

</details>

14. A company wants predictable monthly spend and can commit to steady usage. Best fit:
   - **A.** Pure Spot
   - **B.** Reserved + Savings Plans
   - **C.** Always On-Demand
   - **D.** Dedicated Host for everything

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Reservations/Savings Plans convert variable cost into predictable discounted commitment.

**Why A is wrong:** Spot is unpredictable.
**Why C is wrong:** On-Demand is variable and pricey.
**Why D is wrong:** Dedicated is for licensing, not general.

</details>

15. Per-request serverless billing (pay only when code runs) applies to:
   - **A.** Always-on EC2
   - **B.** Lambda
   - **C.** Reserved RDS
   - **D.** EBS

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Lambda bills per invocation duration, scaling to zero when idle — no idle cost.

**Why A is wrong:** EC2 bills while running.
**Why C is wrong:** RDS bills while provisioned.
**Why D is wrong:** EBS bills while provisioned.

</details>

16. Provisioned IOPS (io2) costs more because:
   - **A.** It is archive storage
   - **B.** You pay for guaranteed performance, not just capacity
   - **C.** It is free tier
   - **D.** It is object storage

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** io2 prices guaranteed IOPS/throughput separately from capacity — performance you pay for.

**Why A is wrong:** It is block, not archive.
**Why C is wrong:** Not free.
**Why D is wrong:** Not object.

</details>

17. To avoid surprise bills from a forgotten test resource, you should:
   - **A.** Never tag
   - **B.** Set billing alarms / budget alerts
   - **C.** Disable Cost Explorer
   - **D.** Use only On-Demand

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Budgets and CloudWatch billing alarms alert you before spend runs away.

**Why A is wrong:** Tagging aids accountability.
**Why C is wrong:** Hiding data is bad.
**Why D is wrong:** On-Demand can still surprise.

</details>

18. The difference between CAPEX and OPEX in cloud is:
   - **A.** Same thing
   - **B.** CAPEX = upfront owned assets; OPEX = pay-as-you-go operating expense
   - **C.** Cloud is only CAPEX
   - **D.** On-prem is only OPEX

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Cloud shifts you from buying assets (CAPEX) to consuming services (OPEX), improving cash flow.

**Why A is wrong:** They differ.
**Why C is wrong:** Cloud is mostly OPEX.
**Why D is wrong:** On-prem is mostly CAPEX.

</details>

19. Storage cost for an EBS volume is driven mainly by:
   - **A.** Number of API calls only
   - **B.** Provisioned size and type (gp3 vs io2), not actual used space
   - **C.** The instance's CPU
   - **D.** Free tier only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EBS is billed on provisioned GB and volume type, regardless of how full the disk is.

**Why A is wrong:** API calls are separate/minor.
**Why C is wrong:** Instance CPU is billed separately.
**Why D is wrong:** Free tier is limited, not the rule.

</details>

20. A 2-week spikes-only workload with unpredictable timing is cheapest on:
   - **A.** 3-year Reserved Instances
   - **B.** On-Demand or Spot
   - **C.** Dedicated Host
   - **D.** Long-term Savings Plan lock-in

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** For short, unpredictable spikes, On-Demand/Spot avoids paying for idle reserved capacity.

**Why A is wrong:** 3-yr RI is wasted when idle most of the year.
**Why C is wrong:** Dedicated is premium and underused.
**Why D is wrong:** Long locks waste money on spikes.

</details>
