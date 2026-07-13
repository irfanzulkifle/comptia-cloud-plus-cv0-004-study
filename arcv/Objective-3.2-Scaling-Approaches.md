---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 3.2: Scaling Approaches"
aliases: [Objective-3.2-Scaling-Approaches, Domain-3.2-Scaling-Approaches, Scaling, Horizontal-Scaling, Vertical-Scaling, Auto-Scaling]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 3.2
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-3.2, scaling, horizontal, vertical, auto-scaling, triggered, scheduled, manual, trending, load, event, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 3.0 Operations
## 📘 Objective 3.2: Given a Scenario, Configure Appropriate Scaling Approaches

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 3.0 Operations = 17% of total exam
> **Objective 3.2 Focus:** Choosing HOW resources scale — by **Type** (Horizontal vs Vertical) and by **Approach** (Triggered: trending/load/event; Scheduled; Manual) — to match a workload's demand pattern.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 3.2
### Objective Name: *Given a scenario, configure appropriate scaling approaches.*

**What this objective means (beginner-friendly):**

Imagine a restaurant. When it gets busy, you have two ways to serve more customers:
- **Horizontal scaling** = add more tables/waiters (more copies of the same thing). If one table breaks, the others keep serving.
- **Vertical scaling** = replace a small table with a bigger one (more powerful single unit). But there's a biggest-table limit, and swapping it causes a pause.

And you have different ways to *decide when* to scale:
- **Triggered by load** = "when the queue hits 50 people, add a table" (reacts to demand now).
- **Triggered by trending** = "lunch rush comes daily at 12, so scale up just before, based on the pattern."
- **Triggered by event** = "a new batch job dropped, scale up to process it."
- **Scheduled** = "every weekday at 11:55am, add tables — we know lunch is coming."
- **Manual** = "the manager walks over and adds a table by hand."

3.2 is about picking the right **type** (horizontal/vertical) and the right **approach** (triggered/scheduled/manual) for a given workload. It pairs tightly with 3.1 (you watch metrics, then scale) and 2.5 (you provision the right compute).

**Why it matters in the real world:**
- Wrong scaling = outages (under-scale at peak) or wasted money (over-scale when idle).
- Horizontal scaling is the cloud-native path to resilience and elasticity; vertical has hard ceilings.
- Matching approach to pattern (predictable vs spiky vs event-driven) is a core ops skill.

**Why CompTIA tests it:**
- 3.2 is scenario-heavy: "spiky traffic" → horizontal + triggered-by-load; "known daily peak" → scheduled or trending; "biggest DB instance" → vertical (with ceiling caveat).
- The **horizontal vs vertical** distinction and the **triggered/scheduled/manual** approaches are classic distractors.
- It builds on 3.1 (metrics trigger scaling) and feeds 2.5 (provisioned compute).

---

## SECTION 2 — EXAM KNOWLEDGE

### 3.2.1 — SCALING TYPES

#### Horizontal Scaling (Scale Out / In)
**Definition:** Adding or removing **instances/nodes** (more copies) to handle load. Capacity grows by count, not size.
**Purpose:** Handle more concurrent load with resilience (no SPOF), near-limitless growth.
**How it works:** Auto-scaling group / VM scale set / managed instance group adds VMs behind a load balancer; stateless design lets any node serve.
**Advantages:** No downtime (add in parallel), resilient (lose one, others serve), very high ceiling, elastic cost (scale to zero/in).
**Disadvantages:** Needs stateless/load-balanced architecture; data layer must scale too (sharding, read replicas); more complex.
**When to use:** Web/app tiers, stateless services, unpredictable or high-scale load, HA requirements.
**When NOT to use:** Monolithic stateful app that can't run on multiple nodes; tiny steady workload where one box is simpler.

#### Vertical Scaling (Scale Up / Down)
**Definition:** Increasing or decreasing the **size** of an existing instance (more vCPU/RAM) — fewer, bigger boxes.
**Purpose:** More power for a single workload without re-architecting for distribution.
**How it works:** Stop instance → change type → restart; or live-resize where supported.
**Advantages:** Simple, no app changes, good for stateful/monolithic workloads, single-node licensing.
**Disadvantages:** **Hard ceiling** (max instance size), **downtime** during resize, SPOF risk (one big box), can't scale past the largest SKU.
**When to use:** Databases (often vertical first), legacy monoliths, apps that can't be distributed, moderate growth.
**When NOT to use:** Need beyond the largest instance; need zero-downtime HA; wildly spiky load (vertical can't react fast/elastically).

### 3.2.2 — SCALING APPROACHES

#### Triggered Scaling
Scaling that reacts automatically to a signal.

**Trending (predictive):**
- **Definition:** Scale based on **observed patterns over time** (e.g., "CPU rises every weekday 12–1pm") — often predictive/ML or scheduled-from-history.
- **Best for:** Recurring, predictable patterns; proactive scaling before the peak hits.
- **Exam cue:** "History shows daily lunch spike" → trending (scale up ahead of pattern).

**Load (reactive):**
- **Definition:** Scale based on **current demand metrics** (CPU%, request count, queue depth) crossing a threshold.
- **Best for:** Real-time demand that may not be perfectly predictable; reacts as it happens.
- **Exam cue:** "When CPU > 70%, add a node" → load-triggered.

**Event:**
- **Definition:** Scale in response to a **specific event** (a file landed, a job queued, a message published).
- **Best for:** Batch/event-driven workloads (process the upload, then scale back).
- **Exam cue:** "A 50 GB file arrives → spin up processors" → event-triggered.

#### Scheduled Scaling
**Definition:** Scale at **fixed times** regardless of current load (cron-like), based on known schedule.
**Best for:** Highly predictable demand (business hours, end-of-month batch, Black Friday window).
**Exam cue:** "Scale up weekdays 8am, down 8pm" → scheduled.

#### Manual Scaling
**Definition:** A human changes capacity by hand (console/CLI/IaC apply).
**Best for:** Stable/unknown workloads, testing, one-off needs, or when automation isn't warranted.
**Exam cue:** "Admin adds instances when they notice slowness" → manual (risky: slow, human-dependent).

> **Triggered vs Scheduled vs Manual:** Triggered = automatic, reacts to signal (trend/load/event). Scheduled = automatic, time-based. Manual = human-driven.


---

### 3.2.3 — MATCHING TYPE + APPROACH TO WORKLOAD (THE JUDGMENT CORE)

The exam gives a demand pattern and asks for the right scaling **type** and **approach**. Use this map:

| Workload pattern | Type | Approach | Why |
|---|---|---|---|
| Unpredictable spikes, stateless web | Horizontal | Triggered (load) | Elastic, resilient, reacts live |
| Known daily/weekly peak | Horizontal | Trending (predictive) or Scheduled | Scale ahead of pattern |
| Recurring business-hours demand | Horizontal | Scheduled | Fixed time, predictable |
| Batch job on file arrival | Horizontal | Triggered (event) | Scale for the job, then in |
| Monolithic/stateful DB, moderate growth | Vertical | Manual or Scheduled | Can't distribute; resize box |
| Need beyond largest instance | Horizontal | Triggered (load) | Vertical hit its ceiling |
| Zero-downtime HA requirement | Horizontal | Triggered (load) | Vertical needs a restart |
| Stable, low-change, testing | Either | Manual | Automation not worth it |

**Key principle:** Prefer **horizontal** for cloud-native, resilient, elastic, high-scale needs. Use **vertical** when the app can't be distributed (DB, monolith) or growth is modest — but remember its **ceiling + downtime**.

**Cross-objective link:**
- **3.1 (Observability):** The *metrics* (CPU, queue depth, request count) are what **trigger** scaling. You can't scale smartly without watching (3.1).
- **2.5 (Provisioning):** Scaling *provisions* the compute you planned in 2.5. Auto-scaling groups, instance families, and reserved baselines all come from 2.5 decisions.
- **2.2 (Deployment strategies):** Blue-green/canary (2.2) often combine with horizontal scaling for safe rollouts.

### 3.2.4 — COOL-DOWN, HEALTH CHECKS, AND BOUNDS

Real auto-scaling configs need guardrails (often implied in scenario questions):
- **Min/Max bounds:** never scale below a floor (availability) or above a ceiling (cost).
- **Cool-down / stabilization:** wait between scale actions so you don't thrash (scale up, then immediately down).
- **Health checks:** only count healthy nodes; replace unhealthy ones (ties to 3.1 + 2.5 HA).
- **Scale-in protection:** don't terminate a node mid-task (e.g., draining connections).

**Exam angle:** "Instances thrash up and down" → cool-down too short; "cost blew up" → max bound missing; "outage during scale" → min bound / health check issue.


---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — E-commerce (horizontal + triggered load + scheduled)
A retail site faces predictable lunch/evening bumps and unpredictable flash-sale spikes.
- **Type:** Horizontal (stateless web tier behind ALB).
- **Approach:** **Triggered by load** (CPU/request-count alarms add nodes) for spikes; **Scheduled** scale-up before known sale windows; reserved baseline + on-demand scale-out (2.5).
- **Guardrails:** min 2 (HA), max bound for cost, cool-down, health checks.

### 3.2 — Reporting batch (event-triggered horizontal)
Nightly, a 50 GB file lands in object storage → a function/queue triggers a fleet of workers to process it, then scales to zero.
- **Type:** Horizontal.
- **Approach:** **Triggered by event** (object-create / queue message).
- **Cost:** Pay only during processing; idle = zero.

### 3.3 — Global SaaS (trending/predictive)
Usage peaks every weekday 9am–6pm in a region.
- **Type:** Horizontal.
- **Approach:** **Trending (predictive)** scaling warms capacity before 9am using historical pattern; Load-triggered fine-tunes.

### 3.4 — Database (vertical first, then horizontal)
A relational DB grows; first **scale up** (bigger instance, more RAM) — simple, no app change.
- When it nears the largest SKU or needs HA: add **read replicas** (horizontal for reads), shard, or move to a distributed engine.
- **Approach:** Often **Scheduled/Manual** for planned resize (downtime window), since vertical needs a restart.

### 3.5 — Monolith legacy app (vertical + manual)
A non-distributable monolith on one big VM. Growth is slow.
- **Type:** Vertical (scale up the VM).
- **Approach:** **Manual/Scheduled** during a maintenance window (downtime acceptable, planned).
- **Risk:** Ceiling — eventually must re-architect (2.3) to go horizontal.

### 3.6 — Cross-cloud note
Every provider offers the same model: AWS Auto Scaling Groups / Azure VM Scale Sets / GCP Managed Instance Groups (horizontal, triggered/scheduled); resize instance type (vertical). The *concept* is identical.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Horizontal vs Vertical

| | Horizontal (out/in) | Vertical (up/down) |
|---|---|---|
| What changes | Number of instances | Size of one instance |
| Downtime | None (add in parallel) | Yes (restart to resize) |
| Ceiling | Very high / near-limitless | Hard max instance size |
| Resilience | High (no SPOF) | Low (one big box = SPOF) |
| Cost model | Pay per node, scale to zero | Bigger box costs more, always on |
| Best for | Stateless, HA, spiky, high-scale | Stateful/monolith, modest growth |
| Requires | Load-balanced, stateless design | Nothing (just resize) |
| Exam cue | "add more servers" / "no downtime" | "bigger machine" / "max size hit" |

### 4.2 — Scaling Approaches

| Approach | Trigger | Best for | Exam cue |
|---|---|---|---|
| Triggered — Load | Current metric threshold | Real-time/unpredictable demand | "CPU>70% → add node" |
| Triggered — Trending | Historical pattern (predictive) | Recurring, predictable peaks | "daily lunch spike pattern" |
| Triggered — Event | Specific event (file/job) | Batch/event-driven | "file arrives → process" |
| Scheduled | Fixed time (cron) | Known schedule | "weekdays 8am scale up" |
| Manual | Human action | Stable/testing/one-off | "admin adds when slow" |

### 4.3 — Which type+approach by pattern

| Pattern | Type | Approach |
|---|---|---|
| Spiky, stateless | Horizontal | Triggered (load) |
| Known daily peak | Horizontal | Trending or Scheduled |
| Batch on arrival | Horizontal | Triggered (event) |
| Monolith/DB, modest | Vertical | Manual/Scheduled |
| Beyond biggest box | Horizontal | Triggered (load) |
| Zero-downtime HA | Horizontal | Triggered (load) |

### 4.4 — Guardrails (auto-scaling)

| Problem | Missing guardrail |
|---|---|
| Thrashing up/down | Cool-down too short |
| Cost blow-up | Max bound missing |
| Outage during scale | Min bound / health check |
| Mid-task termination | Scale-in protection / draining |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — Vertical for a zero-downtime HA need.**
Question: "Must scale with no downtime and stay highly available."
Correct: **Horizontal** (add nodes in parallel, no restart).
Why wrong: Vertical needs a restart (downtime) and creates a SPOF — fails HA.

**Trap #2 — Vertical has no ceiling.**
Question: "Keep scaling up the DB forever."
Correct: **False** — vertical hits the max instance size; then you must go horizontal (replicas/sharding).
Why wrong: Every SKU has a top end.

**Trap #3 — Horizontal for a non-distributable monolith.**
Question: "Legacy monolith on one box, modest growth."
Correct: **Vertical** (simpler, no re-architect) — unless it outgrows the largest instance.
Why wrong: Horizontal needs stateless/load-balanced design the monolith lacks.

**Trap #4 — Load vs Trending confusion.**
Question: "History shows a daily 12pm spike; scale up just before."
Correct: **Trending (predictive)** — uses the pattern to act ahead.
Why wrong: Load-triggered reacts to the spike *after* it starts; trending pre-warms.

**Trap #5 — Event vs Load.**
Question: "A 50 GB file arrives → process it."
Correct: **Event-triggered** (specific occurrence).
Why wrong: Load-triggered watches a metric threshold, not a discrete event.

**Trap #6 — Scheduled for unpredictable spikes.**
Question: "Random viral spikes at any hour."
Correct: **Triggered (load)** — scheduled can't anticipate random spikes.
Why wrong: Scheduled only works for known fixed times.

**Trap #7 — Manual as the resilient choice.**
Question: "Need automatic reaction to demand."
Correct: **Triggered/scheduled** (automatic), not manual.
Why wrong: Manual is slow, human-dependent, and misses off-hours spikes.

**Trap #8 — No max bound → cost blow-up.**
Question: "Scaling ran wild and the bill exploded."
Correct: Missing **max bound** on the auto-scaling group.
Why wrong: Unbounded scale = uncapped cost.

**Trap #9 — No min bound → outage.**
Question: "Scaled all the way to zero during a lull, then can't serve."
Correct: Missing **min bound** (and possibly health checks).
Why wrong: Floor protects availability.

**Trap #10 — Thrashing = cool-down too short.**
Question: "Instances scale up then down then up repeatedly."
Correct: **Cool-down** too short between actions.
Why wrong: Need stabilization window.

**Trap #11 — Horizontal without stateless design.**
Question: "Add nodes but sessions break / data corrupts."
Correct: App isn't **stateless/load-balanced** — horizontal needs shared/stateless state.
Why wrong: Naive horizontal scaling breaks stateful apps.

**Trap #12 — Scaling the DB like the web tier.**
Question: "Auto-scale the relational DB horizontally on CPU."
Correct: DBs usually **vertical first** (or read replicas/sharding) — not naive horizontal.
Why wrong: Stateful DBs don't horizontally scale by just adding nodes.

**Trap #13 — Ignore health checks.**
Question: "Unhealthy nodes counted as capacity; users hit broken ones."
Correct: Need **health checks** to route only to healthy nodes.
Why wrong: Scaling without health = false capacity.

**Trap #14 — "Biggest VM" for spiky load.**
Question: "Spiky traffic, choose the largest single instance."
Correct: **Horizontal + triggered** — vertical can't react elastically/fast and has a ceiling.
Why wrong: Vertical is slow (restart) and capped.

**Trap #15 — Scaling without observability.**
Question: "Set scaling but no metrics to trigger it."
Correct: You need **3.1 metrics** (CPU/queue/requests) to drive triggered scaling.
Why wrong: No signal = no smart scaling.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Spiky Stateless Web Tier
**Scenario:** Public API, unpredictable traffic 1x–20x, must stay up, stateless, cost-conscious.
**Question:** Configure scaling.
**Walkthrough:**
1. **Type:** Horizontal (stateless behind LB).
2. **Approach:** **Triggered by load** (CPU/request-count alarms add/remove nodes).
3. **Guardrails:** min 2 (HA), max bound (cost), cool-down, health checks.
4. **Cost:** reserved baseline + on-demand scale-out (2.5).
**Bonus Q:** Why not vertical? → restart downtime + ceiling + SPOF; can't react elastically.

### PBQ 2 — Known Daily Peak
**Scenario:** App peaks every weekday 9am–6pm in-region; quiet nights/weekends.
**Question:** Best approach.
**Walkthrough:**
1. **Type:** Horizontal.
2. **Approach:** **Trending (predictive)** pre-warms before 9am; **Scheduled** (scale up 8:55am, down 6:05pm) is also valid.
3. Min/max bounds; health checks.
**Bonus Q:** Random viral spike at 11pm? → load-triggered catches it; scheduled alone wouldn't.

### PBQ 3 — Batch on File Arrival
**Scenario:** A 50 GB file lands nightly; process it, then idle.
**Question:** Configure.
**Walkthrough:**
1. **Type:** Horizontal fleet of workers.
2. **Approach:** **Event-triggered** (object-create / queue message spins up processors).
3. Scale to zero after completion → near-zero idle cost.
**Bonus Q:** Why not scheduled only? → file may arrive late/early; event is precise.

### PBQ 4 — Database Growth
**Scenario:** Relational DB growing; currently on one VM; modest, steady growth; needs HA soon.
**Question:** Scaling plan.
**Walkthrough:**
1. **First:** **Vertical** scale-up (more RAM/CPU) — simple, no app change.
2. **For HA/reads:** add **read replicas** (horizontal for reads).
3. **If nearing max SKU or needing write scale:** shard / move to distributed engine / re-architect (2.3).
4. **Approach:** Scheduled/Manual resize in a maintenance window (vertical needs restart).
**Bonus Q:** Can you auto-scale the DB horizontally on CPU like the web tier? → Not naively; stateful DBs need replicas/sharding.

### PBQ 5 — Guardrail Failure
**Scenario:** Auto-scaling group thrashing, bill exploded, then outage during a lull.
**Question:** Diagnose.
**Walkthrough:**
1. **Thrashing** → cool-down too short.
2. **Cost blow-up** → max bound missing.
3. **Outage at zero** → min bound missing + health checks.
4. Fix: set cool-down, min (e.g., 2) and max bounds, health-check gating.
**Bonus Q:** Users hitting broken nodes? → health checks not filtering unhealthy instances.


---

## SECTION 7 — MEMORY AIDS

### 7.1 — Horizontal vs Vertical (the core split)
**"Horizontal = more boxes. Vertical = bigger box."**
- Horizontal (out/in): add/remove instances — no downtime, resilient, high ceiling.
- Vertical (up/down): resize one instance — simple, but downtime + hard ceiling + SPOF.

### 7.2 — The Approaches
**"T-S-M"** → **T**riggered, **S**cheduled, **M**anual.
- **Triggered** = automatic, reacts to a signal (trend / load / event).
- **Scheduled** = automatic, time-based (cron).
- **Manual** = human hand.

### 7.3 — Triggered sub-trio
**"Trend / Load / Event"**
- **Trend** = history pattern → pre-warm (predictive).
- **Load** = current metric crosses threshold → react now.
- **Event** = a thing happened (file/job) → scale for it.

### 7.4 — Pick by pattern (story)
- "Spiky, stateless" → Horizontal + Load.
- "Same peak every day" → Horizontal + Trending/Scheduled.
- "File arrives" → Horizontal + Event.
- "Old monolith" → Vertical + Manual/Scheduled.
- "Need no downtime + HA" → Horizontal (always).

### 7.5 — Guardrails (the 3 bounds + 2 checks)
**"Min, Max, Cool-down; Health, Drain."**
- **Min** = floor (availability).
- **Max** = ceiling (cost).
- **Cool-down** = don't thrash.
- **Health checks** = only count healthy.
- **Drain/scale-in protection** = don't kill mid-task.

### 7.6 — Decision tree (ASCII)
```
Given a scaling need:
│
├─ Need NO downtime + HA + resilient? ─────► HORIZONTAL
├─ App can't be distributed (monolith/DB)? ─► VERTICAL (until ceiling)
├─ Demand unpredictable / spiky? ──────────► Triggered (LOAD)
├─ Recurring daily/weekly pattern? ────────► Trending (predictive) or Scheduled
├─ Fixed known times (business hrs)? ──────► Scheduled
├─ A specific event (file/job)? ───────────► Triggered (EVENT)
└─ Stable / testing / one-off? ────────────► Manual
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| Horizontal scale group | Auto Scaling Group (ASG) | VM Scale Sets (VMSS) | Managed Instance Groups (MIG) |
| Load balancer | ALB / NLB | Azure Load Balancer / App Gateway | Cloud Load Balancing |
| Triggered by load | ASG scaling policy (CloudWatch alarm) | VMSS autoscale (Monitor metric) | MIG autoscaler (Cloud Monitoring) |
| Triggered by schedule | Scheduled scaling (cron) | VMSS schedule | MIG schedule (cron) |
| Triggered by event | EventBridge / SQS → Lambda/workers | Event Grid → Functions | Pub/Sub / Eventarc → Cloud Run/Functions |
| Vertical resize | Change instance type (stop/start) | Resize VM | Change machine type |
| Predictive/trending | Predictive scaling (ASG) | Azure Monitor predictive | MIG (metric + forecast) |
| DB horizontal (reads) | RDS Read Replicas | Azure DB replicas | Cloud SQL / AlloyDB replicas |
| Health checks | ELB health checks / ASG | LB health probes | Health checks (MIG) |
| Bounds/cool-down | ASG min/max + cooldown | VMSS min/max + cooldown | MIG min/max + cooldown |

**Cross-cloud constant:** Horizontal = scale group + LB + health checks; Vertical = resize the instance type. Triggered/scheduled/manual exist on all three. The *concept* is identical; only the service name changes.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What is horizontal scaling?**
A: Adding/removing instances (more copies) to handle load — no downtime, resilient, scales by count.

**Q2: What is vertical scaling and its biggest drawback?**
A: Resizing one instance to be bigger/smaller. Drawback: downtime during resize, a hard size ceiling, and single-point-of-failure risk.

**Q3: What's the difference between scheduled and triggered scaling?**
A: Triggered reacts to a signal (metric/event/trend); scheduled acts at fixed times regardless of current load.

### 🎓 Cloud Administrator Questions

**Q4: A stateless web tier with unpredictable spikes needs to stay up and control cost. Your design?**
A: Horizontal auto-scaling group behind a LB, triggered by load (CPU/request metrics), with min (HA) and max (cost) bounds, cool-down, and health checks. Reserved baseline + on-demand scale-out.

**Q5: A relational DB is growing but is a single VM. How do you scale it?**
A: Vertical scale-up first (simple, no app change). Add read replicas for read scale/HA. If it nears the largest SKU or needs write scale, shard or re-architect to distributed — you can't naively auto-scale a stateful DB horizontally.

**Q6: Instances are thrashing up and down and the bill spiked. What's wrong?**
A: Cool-down too short (thrashing) and no max bound (cost). Set a stabilization window and a max instance count.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: Client wants zero-downtime scaling and high availability. Vertical or horizontal?**
A: Horizontal — adds nodes in parallel with no restart and no SPOF. Vertical requires a restart and creates a single big point of failure.

**Q8: "We know traffic doubles every weekday at noon." Best approach?**
A: Trending (predictive) scaling to pre-warm before noon, or scheduled scale-up at ~11:55am. Both beat reactive load-triggered for a known pattern.

**Q9: Why can't we just pick the biggest VM for spiky traffic?**
A: Vertical can't react elastically/fast (needs restart) and hits a ceiling. Horizontal + triggered handles spikes resiliently and scales back to save cost.

---

## SECTION 10 — FLASHCARDS

**Types**
Q: Add more instances to handle load?
A: Horizontal scaling (scale out/in).

Q: Make one instance bigger?
A: Vertical scaling (scale up/down).

Q: Horizontal needs what architecture?
A: Stateless + load-balanced.

Q: Vertical's hard limit?
A: Max instance size (ceiling) + downtime + SPOF.

**Approaches**
Q: React to CPU>70% threshold?
A: Triggered — Load.

Q: Use historical daily pattern to pre-warm?
A: Triggered — Trending (predictive).

Q: Scale when a file arrives?
A: Triggered — Event.

Q: Scale weekdays 8am–8pm fixed?
A: Scheduled.

Q: Human adds instances by hand?
A: Manual.

Q: Triggered vs Scheduled difference?
A: Triggered = signal-reactive; Scheduled = time-based.

**Guardrails**
Q: Prevents thrashing up/down?
A: Cool-down.

Q: Caps cost from runaway scale?
A: Max bound.

Q: Keeps minimum capacity for availability?
A: Min bound.

Q: Ensures only healthy nodes serve?
A: Health checks.

Q: Avoids killing a node mid-task?
A: Scale-in protection / draining.

**Mapping**
Q: AWS horizontal scale group?
A: Auto Scaling Group (ASG).

Q: Azure horizontal scale group?
A: VM Scale Sets (VMSS).

Q: GCP horizontal scale group?
A: Managed Instance Groups (MIG).

Q: AWS predictive scaling?
A: ASG predictive scaling.

**Traps**
Q: Zero-downtime HA → horizontal or vertical?
A: Horizontal.

Q: Monolith, modest growth → which type?
A: Vertical (until ceiling).

Q: Random spikes → scheduled or load-triggered?
A: Load-triggered.

Q: Bill exploded from scaling → missing?
A: Max bound.

Q: Scaled to zero, outage → missing?
A: Min bound + health checks.


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** Adding more VM instances behind a load balancer to handle load is:
A) Vertical scaling  B) Horizontal scaling  C) Scheduled scaling  D) Manual scaling
**Answer: B) Horizontal scaling.**
*Why others wrong:* Vertical resizes one; scheduled/manual are approaches, not types.

**E2.** Increasing a single VM's RAM and vCPU is:
A) Horizontal  B) Vertical  C) Event-triggered  D) Trending
**Answer: B) Vertical.**
*Why others wrong:* Horizontal adds instances; others are triggered types.

**E3.** Scaling at fixed times (e.g., weekdays 8am) is:
A) Triggered  B) Scheduled  C) Manual  D) Event
**Answer: B) Scheduled.**
*Why others wrong:* Triggered reacts to signal; manual is human; event is a specific occurrence.

**E4.** A human logs in and adds instances when slow. This is:
A) Manual  B) Scheduled  C) Load-triggered  D) Horizontal
**Answer: A) Manual.**
*Why others wrong:* Others are automatic or a type.

**E5.** Horizontal scaling requires the app to be:
A) Stateful  B) Stateless + load-balanced  C) Single-instance  D) Vertical
**Answer: B) Stateless + load-balanced.**
*Why others wrong:* Stateful/single-instance break naive horizontal scaling.

### MEDIUM (10)

**M1.** Unpredictable traffic spikes, stateless web tier, must stay up, control cost. Best:
A) Vertical + manual  B) Horizontal + load-triggered + bounds  C) Vertical + scheduled  D) Manual only
**Answer: B) Horizontal + load-triggered with min/max bounds.**
*Why others wrong:* Vertical has ceiling/downtime; manual misses off-hours spikes.

**M2.** "History shows a daily noon spike; scale up just before." Best:
A) Load-triggered  B) Trending (predictive)  C) Event  D) Manual
**Answer: B) Trending (predictive)** — acts on the pattern ahead of time.
*Why others wrong:* Load reacts after spike starts; event is discrete; manual is slow.

**M3.** "A 50 GB file lands → process it, then idle." Best:
A) Scheduled  B) Event-triggered horizontal  C) Vertical manual  D) Trending
**Answer: B) Event-triggered horizontal** (scale for the job, to zero after).
*Why others wrong:* Scheduled ignores actual arrival; vertical/manual don't fit elastic batch.

**M4.** Need to scale with NO downtime and high availability. Choose:
A) Vertical  B) Horizontal  C) Biggest VM  D) Manual vertical
**Answer: B) Horizontal** (no restart, no SPOF).
*Why others wrong:* Vertical needs restart + is SPOF; manual is slow.

**M5.** Auto-scaling group thrashing up/down repeatedly. Cause:
A) Max bound  B) Cool-down too short  C) Health check  D) Min bound
**Answer: B) Cool-down too short.**
*Why others wrong:* Max bounds cost; health checks filter nodes; min bounds availability.

**M6.** Bill exploded from runaway scaling. Missing:
A) Min bound  B) Max bound  C) Cool-down  D) Health check
**Answer: B) Max bound.**
*Why others wrong:* Min protects availability; cool-down stops thrash; health checks filter.

**M7.** Scaled to zero during a lull, then outage. Missing:
A) Max bound  B) Min bound + health checks  C) Cool-down  D) Schedule
**Answer: B) Min bound + health checks.**
*Why others wrong:* Max/cool-down/schedule don't keep a floor.

**M8.** Relational DB growing, single VM, modest steady growth. First step:
A) Horizontal auto-scale  B) Vertical scale-up  C) Shard now  D) Re-architect now
**Answer: B) Vertical scale-up** (simple, no app change).
*Why others wrong:* Horizontal/shard/re-architect are premature for modest, stateful growth.

**M9.** Random viral spikes at any hour. Best approach:
A) Scheduled  B) Load-triggered  C) Manual  D) Trending
**Answer: B) Load-triggered** (reacts to real-time demand).
*Why others wrong:* Scheduled can't anticipate random; manual is slow; trending needs a pattern.

**M10.** Users hitting broken nodes despite scaling. Missing:
A) Cool-down  B) Health checks  C) Max bound  D) Schedule
**Answer: B) Health checks** (route only to healthy nodes).
*Why others wrong:* Others don't filter unhealthy capacity.

### HARD (5)

**H1.** A stateful monolith on one VM hits the largest available instance size and still needs more capacity, with zero-downtime required. Best path:
A) Bigger VM (impossible)  B) Re-architect to stateless horizontal + triggered  C) Manual vertical  D) Scheduled vertical
**Answer: B) Re-architect (2.3) to stateless, then horizontal + triggered** — vertical ceiling is hit; only horizontal scales further with no downtime.
*Why others wrong:* A is impossible (max size); C/D still vertical (downtime + ceiling).

**H2.** Known peak every weekday 9am–6pm, PLUS random viral spikes. Most complete design:
A) Scheduled only  B) Load-triggered only  C) Horizontal: scheduled/trending for the pattern + load-triggered for spikes, with bounds  D) Vertical
**Answer: C)** — covers the predictable pattern AND the unpredictable spikes, with cost/availability bounds.
*Why others wrong:* Scheduled alone misses spikes; load alone reacts late to the pattern; vertical can't react elastically.

**H3.** Nightly batch: a file arrives, must process fast, then idle until tomorrow. Best cost+speed design:
A) Always-on big VM  B) Event-triggered horizontal fleet scaling to zero  C) Scheduled vertical  D) Manual
**Answer: B) Event-triggered horizontal fleet, scale to zero after** — pays only during processing.
*Why others wrong:* Always-on wastes money; vertical/manual don't elastic-batch well.

**H4.** Which combination gives resilient, elastic, cost-controlled scaling?
A) Vertical + manual  B) Horizontal + triggered/scheduled + min/max/cool-down/health  C) Biggest VM + scheduled  D) Manual + no bounds
**Answer: B)** — horizontal for resilience/elasticity, guardrails for cost/availability.
*Why others wrong:* Vertical/manual lack elasticity; no bounds = cost/availability risk.

**H5.** DB needs read scale + HA but writes stay on one primary. Best:
A) Vertical only  B) Read replicas (horizontal for reads) + vertical primary  C) Full horizontal auto-scale  D) Manual resize only
**Answer: B) Read replicas (horizontal for reads) + vertical-scaled primary** — stateful DB scales reads horizontally, writes vertically.
*Why others wrong:* Vertical-only caps reads; naive full horizontal doesn't fit a primary DB; manual alone isn't elastic.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Horizontal vs Vertical | 🔴 CRITICAL | Most-tested split; ceiling/downtime/SPOF |
| Triggered (load/trend/event) vs Scheduled vs Manual | 🔴 CRITICAL | Approach selection is core |
| "Zero-downtime HA" → horizontal | 🔴 CRITICAL | Classic scenario |
| Vertical ceiling + downtime | 🟠 HIGH | Why vertical fails at scale |
| DB scaling (vertical first, replicas) | 🟠 HIGH | Stateful nuance |
| Min/Max/Cool-down/Health bounds | 🟠 HIGH | Guardrail failure traps |
| Trending vs Load distinction | 🟡 MEDIUM | Predictive vs reactive |
| Event-triggered batch | 🟡 MEDIUM | File/job arrival |
| Stateless requirement for horizontal | 🟢 LOW | Implied architecture |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**Two scaling types**
- **Horizontal (out/in):** add/remove instances — no downtime, resilient, high ceiling, needs stateless+LB.
- **Vertical (up/down):** resize one instance — simple, but downtime + hard ceiling + SPOF.

**Three approaches**
- **Triggered:** automatic, reacts to signal — **Load** (metric threshold, reactive), **Trending** (historical pattern, predictive/pre-warm), **Event** (file/job arrival).
- **Scheduled:** automatic, fixed time (cron).
- **Manual:** human-driven.

**Match by pattern**
- Spiky/stateless → Horizontal + Load-triggered
- Known daily peak → Horizontal + Trending/Scheduled
- File/job arrival → Horizontal + Event-triggered
- Monolith/DB, modest → Vertical + Manual/Scheduled
- Beyond biggest box / zero-downtime HA → Horizontal + Load-triggered

**Guardrails**
- Min = floor (availability) · Max = ceiling (cost) · Cool-down = no thrash · Health checks = only healthy · Drain = no mid-task kill.

**Cross-objective links**
- 3.1 metrics (CPU/queue/requests) *trigger* scaling.
- 2.5 provisions the compute (instance families, reserved baseline).
- 2.3 Re-architect may be needed when vertical hits its ceiling.

**Acronyms**
- HA = High Availability · SPOF = Single Point of Failure
- ASG = Auto Scaling Group · VMSS = Virtual Machine Scale Set · MIG = Managed Instance Group
- LB = Load Balancer · VM = Virtual Machine · DB = Database
- CPU = Central Processing Unit · RAM = Random Access Memory · vCPU = virtual CPU
- API = Application Programming Interface

**Traps recap**
1. Zero-downtime HA → horizontal (not vertical). 2. Vertical has a ceiling + restart. 3. Monolith modest → vertical. 4. Load = reactive; Trending = predictive/pre-warm. 5. Event = file/job. 6. Scheduled = fixed time. 7. Manual = slow, misses off-hours. 8. No max → cost blow-up. 9. No min → outage at zero. 10. Thrashing = cool-down short. 11. Horizontal needs stateless. 12. DBs scale vertically first (replicas for reads). 13. No health checks = broken nodes served. 14. Biggest VM for spiky = wrong (ceiling + restart). 15. Scaling needs 3.1 metrics to trigger.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **Predictive autoscaling matured:** AWS ASG predictive scaling, Azure Monitor autoscale with forecast, GCP MIG with ML forecasts — the 3.2 "Trending" approach is now first-class, not just cron.
- **Kubernetes HPA/VPA/KEDA:** Horizontal Pod Autoscaler (metrics), Vertical Pod Autoscaler (resize pods), and KEDA (event-driven scaling from queues/Kafka) map directly to 3.2's types/approaches on containers.
- **Scale-to-zero serverless:** Lambda / Azure Functions / Cloud Run scale to zero by default — the ultimate horizontal event-triggered cost model.
- **Spot/Preemptible in scaling:** Auto-scaling groups can mix spot for cost (ties to 2.5); interruption handling becomes a scaling design concern.
- **FinOps guardrails:** Cost-anomaly alerts + max-bound enforcement are standard — the "max bound prevents bill blow-up" lesson is production-real.
- **Managed instance groups across clouds** now support health-based auto-healing (replace unhealthy nodes) — health checks as a scaling guardrail.
- **Chaos/load testing:** Teams validate scaling (bounds, cool-down) with game-days and load tests before relying on it — verifying the guardrails actually work.
- **DB horizontal reads:** Aurora Serverless v2, Cloud SQL autoscaler, and read-replica auto-scaling make the "vertical primary + horizontal replicas" pattern easier — the 3.2 DB nuance.

*End of Objective 3.2 notes.*
