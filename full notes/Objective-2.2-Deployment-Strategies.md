---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 2.2: Deployment Strategies"
aliases: [Objective-2.2-Deployment-Strategies, Domain-2.2-Deployment-Strategies]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 2.2
status: active
created: 2026-06-12
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-2.2, deployment-strategies, blue-green, canary, rolling, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Objective 2.2: Deployment Strategies

## SECTION 1 — Exam Objectives

- **Objective 2.2:** *Given a scenario, implement appropriate deployment strategies.*
- **The four strategies you must know:**
  - **Blue-green** — two identical environments; cut all traffic over at once.
  - **Canary** — release to a small % of users first, then widen.
  - **Rolling** — replace instances in batches; no second full environment.
  - **In-place** — stop, update, restart on the same infrastructure.
- **Why it matters:** A bad deployment causes outages that cost money per minute. Pick the right strategy for the business's budget, risk tolerance, RTO/RPO, and traffic patterns.
- **How CompTIA tests it:** Scenario-driven. They describe a workload and ask you to pick the right strategy — real-world judgment, not memorization.

## SECTION 2 — EXAM KNOWLEDGE

### Blue-Green Deployment
- **Definition:** Run two identical production environments (Blue = live, Green = new). Shift 100% of traffic from Blue to Green at once via a load balancer or DNS. Rollback = repoint traffic back to Blue.
- **Why it matters:** Near-zero downtime and instant, low-risk rollback; fully validates the new version in production-like conditions before users see it.
- **Analogy:** Open a second restaurant with the new menu next door. When ready, send all customers there; if it fails, send them back.
- **Example:** Storefront on Blue, new version on Green; an Elastic Load Balancer target-group swap moves all shoppers to Green in seconds, with a flip-back if a bug appears.

### Canary Deployment
- **Definition:** Release the new version to a small subset of users (e.g., 5%), then gradually increase exposure (25% → 50% → 100%) while watching metrics. Traffic split by %, header, geography, or cohort.
- **Why it matters:** Limits the "blast radius" of a bad release, gathers real-user telemetry, and catches regressions early before full rollout.
- **Analogy:** Serve the new dish to 5% of diners first; if they love it, expand; if they hate it, pull it before anyone else notices.
- **Example:** A social app routes 5% of feed-API traffic to the canary; error rate stays flat, so exposure grows over 48 hours; a memory leak at 50% triggers automatic rollback.

### Rolling Deployment
- **Definition:** Replace old-version instances with new ones in batches (a few at a time) until all run the new version. The app stays partially available; no duplicate full environment.
- **Why it matters:** Less downtime than in-place without paying for a second full environment; automatable with max-unavailable / max-surge settings.
- **Analogy:** Swap the menu table by table — Table 1 gets the new menu, then Table 2 — until the whole restaurant is updated; customers see a mix mid-swap.
- **Example:** A SaaS updates 200 pods with maxUnavailable=1, maxSurge=1, so at least 199 always serve traffic while one updates at a time.

### In-Place Deployment
- **Definition:** Stop the running app, install the new version on the same infrastructure, and restart. Also called "replace" or "redeploy."
- **Why it matters:** Cheapest and simplest; fine for non-critical or scheduled-maintenance workloads, but causes downtime and has slow, manual rollback.
- **Analogy:** Shut the whole kitchen, swap the menu, reopen — cheapest, but the restaurant is closed during the change.
- **Example:** An internal HR portal updated during a Sunday 02:00–04:00 window: stop, patch, restart — no second environment justified for an internal tool.

### Strategy Selection Cheat-Sheet
- Need instant rollback + zero downtime, cost no object → **Blue-green**.
- Need to limit risk to a few users and watch metrics → **Canary**.
- Need a balance of uptime and cost, stateless app → **Rolling**.
- Cheapest, downtime OK (maintenance window) → **In-place**.
- Strong statefulness + can't run two versions → **In-place** or **Rolling** with care.
- Regulated cutover with audit trail → **Blue-green** (clean before/after).

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **E-commerce Black Friday (Blue-green):** Retailer keeps Blue + Green stacks on AWS; new storefront validated on Green, then an ELB target-group swap moves 100% of shoppers to Green in seconds; a pricing bug 10 minutes later is fixed by flipping back to Blue.
- **Mobile app backend (Canary):** Social platform releases new feed-ranking API to 5% via a weighted ALB rule; error-rate telemetry stays flat, exposure grows to 25% → 50% → 100% over 48h; a memory leak at 50% triggers automatic rollback to stable.
- **Stateless microservice fleet (Rolling):** SaaS updates 200 container pods with a rolling update (maxUnavailable=1, maxSurge=1), so at least 199 pods always serve traffic while one updates at a time.
- **Internal HR portal (In-place):** Company updates its employee self-service portal during a Sunday 02:00–04:00 maintenance window — stop, patch, restart — with no second environment justified for an internal tool.
- **Banking core cutover (Blue-green + Canary hybrid):** Bank deploys new core to Green, runs parallel runs, then canary-routes 1% of transactions to Green for a week before full cutover — combining both strategies for maximum safety.

## SECTION 4 — COMPARISON TABLES

**Table 1 — Strategy at a Glance**

| Strategy | Downtime | Rollback Speed | Extra Cost | Risk Exposure | Best For |
|----------|----------|----------------|------------|---------------|----------|
| Blue-green | Near-zero | Instant | High (2 envs) | Low | Critical zero-downtime apps |
| Canary | Minimal | Fast (per stage) | Low (slice) | Very low | Uncertain/risk-averse releases |
| Rolling | Minimal* | Slow (re-roll) | None | Medium | Stateless, cost-sensitive |
| In-place | Full | Manual/slow | None | High | Maintenance windows, internal |

\* If capacity is preserved via max-surge.

**Table 2 — Decision Factors**

| If the scenario says… | Choose |
|----------------------|--------|
| "Instant rollback," "no downtime," cost irrelevant | Blue-green |
| "Gradual rollout," "monitor metrics," "limit to 5%" | Canary |
| "Replace instances in batches," "no second env" | Rolling |
| "Lowest cost," "scheduled downtime OK" | In-place |
| "Stateful app can't run two versions" | In-place / Rolling |

**Table 3 — Rollback Characteristics**

| Strategy | Rollback Mechanism | Time to Roll Back | Data Loss Risk |
|----------|-------------------|-------------------|----------------|
| Blue-green | Repoint traffic to Blue | Seconds | Low (if synced) |
| Canary | Shift % back to stable | Seconds–minutes | Low |
| Rolling | Re-deploy old version batch-by-batch | Minutes–hours | Medium |
| In-place | Manually redeploy old build | Minutes–hours | High (during window) |

**Table 4 — Infrastructure Requirements**

| Strategy | Duplicate Env? | Load Balancer? | Feature Flags? | Monitoring Need |
|----------|----------------|----------------|---------------|-----------------|
| Blue-green | Yes (full) | Yes | No | Standard |
| Canary | No (slice) | Yes | Recommended | High |
| Rolling | No | Yes | No | Standard |
| In-place | No | No | No | Basic |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-only services and features that implement each 2.2 strategy:

- **Blue-green (AWS):**
  - **AWS CodeDeploy** — native blue/green deployments for EC2, ECS, and Lambda (traffic-shift with immediate/linear/canary options).
  - **Elastic Load Balancing (ALB/NLB)** — swap target groups to cut traffic over instantly.
  - **Amazon Route 53** — weighted/DNS failover between two environments.
  - **AWS Elastic Beanstalk** — supports blue/green via immutable or traffic-splitting deployments.

- **Canary (AWS):**
  - **AWS CodeDeploy** — `Canary10Percent5Minutes` / `Canary10Percent15Minutes` traffic-shift options.
  - **Application Load Balancer** — weighted target groups for percentage splits.
  - **Amazon API Gateway** — canary release stages for APIs.
  - **AWS Lambda** — alias weighted routing (e.g., 90% v1 / 10% v2).
  - **Amazon CloudWatch** — metrics/alarms that drive automated rollback.

- **Rolling (AWS):**
  - **AWS CodeDeploy** — `AllAtOnce` / `OneAtATime` / `HalfAtATime` rolling configurations.
  - **Amazon EC2 Auto Scaling** — instance refresh with max instance percentage / min healthy percentage.
  - **Amazon ECS** — rolling update deployment type (minimum healthy percent / maximum percent).
  - **Elastic Beanstalk** — rolling / rolling-with-additional-batch deployments.

- **In-place (AWS):**
  - **AWS CodeDeploy** — `InPlace` deployment type (stop, update, restart on same instances).
  - **AWS Systems Manager (SSM) Run Command / Patch Manager** — push updates to running instances.
  - **Elastic Beanstalk** — standard in-place deployment (default).

---

## SECTION 6 — PRACTICE QUESTIONS

1. A company must release a critical customer-facing app with zero downtime and needs the ability to revert in seconds if a defect appears. Cost is not a constraint. Which strategy fits best?
   - **A.** Rolling
   - **B.** Canary
   - **C.** Blue-green
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Blue-green runs two full environments so you flip all traffic at once and roll back in seconds via the load balancer.

**Why A is wrong:** Rolling replaces instances in batches; rollback is slow (re-deploy old version).
**Why B is wrong:** Canary limits blast radius but full rollback isn't instant across all users.
**Why D is wrong:** In-place stops the service; it has downtime and manual/slow rollback.

</details>

2. You want to expose a new feature to 5% of users first and watch error rates before widening. Which strategy?
   - **A.** In-place
   - **B.** Canary
   - **C.** Blue-green
   - **D.** Rolling

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Canary releases to a small cohort (e.g., 5%) and promotes based on metrics like error rate.

**Why A is wrong:** In-place is a stop/patch/restart — no staged user exposure.
**Why C is wrong:** Blue-green shifts 100% at once, not 5% first.
**Why D is wrong:** Rolling updates instances in batches, not by user percentage.

</details>

3. An update replaces running instances a few at a time while the app stays available, with no second full environment. Which strategy?
   - **A.** Rolling
   - **B.** Blue-green
   - **C.** Canary
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Rolling updates instances in batches with no duplicate full environment, keeping the app available.

**Why B is wrong:** Blue-green requires a second full (Green) environment.
**Why C is wrong:** Canary is about user-percentage exposure, typically via a load balancer slice, not batch instance replacement.
**Why D is wrong:** In-place stops the app (downtime), it doesn't keep it available.

</details>

4. During a scheduled Sunday maintenance window, an internal tool is stopped, patched, and restarted. Which strategy?
   - **A.** Canary
   - **B.** Blue-green
   - **C.** In-place
   - **D.** Rolling

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** In-place (stop, patch, restart on same infra) is cheapest and fine when downtime is planned.

**Why A is wrong:** Canary needs live traffic to slice — no use for a tool down in maintenance.
**Why B is wrong:** Blue-green is overkill and costly for a planned-downtime internal tool.
**Why D is wrong:** Rolling avoids downtime, which isn't needed in a maintenance window.

</details>

5. Which strategy has the highest infrastructure cost during deployment?
   - **A.** Rolling
   - **B.** In-place
   - **C.** Blue-green
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Blue-green runs two full production environments simultaneously — the highest extra cost.

**Why A is wrong:** Rolling uses no duplicate env, so cost is low.
**Why B is wrong:** In-place uses the same infra — zero extra environment cost.
**Why D is wrong:** Canary only runs a small slice, not a full second env.

</details>

6. Which strategy offers the slowest rollback if a defect is found late in the rollout?
   - **A.** Blue-green
   - **B.** Rolling
   - **C.** Canary
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Rolling requires re-deploying the old version batch-by-batch, so late rollbacks are slow (minutes–hours).

**Why A is wrong:** Blue-green rollback is seconds (repoint traffic to Blue).
**Why C is wrong:** Canary rollback is fast (shift % back to stable).
**Why D is wrong:** In-place rollback is manual but not "late in the rollout" — rolling is slower for that case.

</details>

7. A stateful legacy app cannot run two versions at once and downtime is tolerated. Best choice?
   - **A.** Blue-green
   - **B.** In-place
   - **C.** Canary
   - **D.** Rolling

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** In-place avoids running two versions simultaneously, suiting stateful apps; planned downtime is acceptable.

**Why A is wrong:** Blue-green runs old+new at once — breaks a stateful app that can't coexist.
**Why C is wrong:** Canary still exposes two versions to users — bad for stateful.
**Why D is wrong:** Rolling runs mixed old/new instances, problematic for stateful systems.

</details>

8. AWS CodeDeploy traffic-shift options like Canary10Percent5Minutes implement which strategy?
   - **A.** Blue-green
   - **B.** Rolling
   - **C.** In-place
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: D**

**Why correct:** CodeDeploy canary traffic-shift options (e.g., Canary10Percent5Minutes) implement Canary deployments.

**Why A is wrong:** Blue-green uses an immediate/linear traffic shift to a second env, not a 10%-canary shift.
**Why B is wrong:** Rolling is AllAtOnce/OneAtATime, not a percentage canary shift.
**Why C is wrong:** In-place is a stop/update/restart, not a traffic shift.

</details>

9. Which strategy keeps both old and new versions running simultaneously, requiring version-tolerant apps?
   - **A.** In-place
   - **B.** Blue-green
   - **C.** Rolling
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Rolling runs mixed old/new instances during the update, so the app must tolerate both versions live.

**Why A is wrong:** In-place runs only the new version after restart.
**Why B is wrong:** Blue-green runs two full envs but only one serves all traffic at a time.
**Why D is wrong:** Canary is about user-percentage exposure, not necessarily mixed instances fleet-wide.

</details>

10. For a regulated system needing a clean audit trail of before/after states, which is preferred?
   - **A.** Rolling
   - **B.** In-place
   - **C.** Blue-green
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Blue-green gives a clean, separable before (Blue) / after (Green) environment, ideal for audit trails.

**Why A is wrong:** Rolling has no separable before/after — instances mix during rollout.
**Why B is wrong:** In-place has no distinct before/after env to audit.
**Why D is wrong:** Canary blends versions gradually, less cleanly separable than two full envs.

</details>

11. An ALB weighted target-group split sending 10% of traffic to a new version describes which strategy?
   - **A.** Canary
   - **B.** Rolling
   - **C.** Blue-green
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Percentage traffic splits (10% via weighted ALB target groups) are the hallmark of Canary.

**Why B is wrong:** Rolling splits by instance batches, not user percentage.
**Why C is wrong:** Blue-green shifts 100% at once, not 10%.
**Why D is wrong:** In-place has no traffic splitting.

</details>

12. Which strategy is best for a stateless microservice fleet on a tight budget?
   - **A.** Blue-green
   - **B.** Rolling
   - **C.** In-place
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Rolling balances uptime and cost with no duplicate env — ideal for stateless, budget-sensitive fleets.

**Why A is wrong:** Blue-green's duplicate env is expensive, conflicting with tight budget.
**Why C is wrong:** In-place causes downtime, not ideal for a service fleet.
**Why D is wrong:** Canary needs strong monitoring, adding cost/complexity beyond the budget need.

</details>

13. Elastic Beanstalk immutable deployments are an AWS implementation of which strategy?
   - **A.** In-place
   - **B.** Rolling
   - **C.** Canary
   - **D.** Blue-green

<details>
<summary>Reveal Answer</summary>

**Correct: D**

**Why correct:** Immutable deployments spin up a fresh set of instances and shift traffic — like blue-green.

**Why A is wrong:** In-place updates existing instances; immutable does the opposite.
**Why B is wrong:** Rolling updates in place in batches; immutable creates new instances.
**Why C is wrong:** Immutable shifts all-at-once to the new set, not a small percentage.

</details>

14. If the priority is limiting the "blast radius" of a bad release, choose:
   - **A.** In-place
   - **B.** Blue-green
   - **C.** Canary
   - **D.** Rolling

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Canary limits impact to a small user subset, minimizing blast radius of a bad release.

**Why A is wrong:** In-place exposes all users at once after restart.
**Why B is wrong:** Blue-green flips 100% — large blast radius if Green is bad.
**Why D is wrong:** Rolling eventually reaches all users; not as tightly scoped as canary.

</details>

15. Which strategy typically causes full application downtime?
   - **A.** Canary
   - **B.** Rolling
   - **C.** Blue-green
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: D**

**Why correct:** In-place stops the service during the update, causing full downtime.

**Why A is wrong:** Canary keeps the app available to the majority throughout.
**Why B is wrong:** Rolling keeps the app partially/fully available.
**Why C is wrong:** Blue-green has near-zero downtime via traffic flip.

</details>

16. Amazon ECS rolling update with minimum/maximum healthy percent implements which strategy?
   - **A.** Blue-green
   - **B.** Rolling
   - **C.** Canary
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** ECS rolling update (min/max healthy percent) replaces tasks gradually — the Rolling strategy.

**Why A is wrong:** Blue-green would stand up a separate task set and shift all traffic.
**Why C is wrong:** Canary would weight a percentage of requests, not just min/max healthy tasks.
**Why D is wrong:** In-place stops tasks; ECS rolling keeps the service up.

</details>

17. A team wants instant rollback but cannot afford two full environments. Best compromise?
   - **A.** Canary
   - **B.** Blue-green
   - **C.** In-place
   - **D.** Rolling

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Canary gives fast staged rollback at low extra cost (small slice), avoiding a full second env.

**Why B is wrong:** Blue-green needs two full environments — unaffordable here.
**Why C is wrong:** In-place rollback is manual/slow, not instant.
**Why D is wrong:** Rolling rollback is slow (re-deploy batches).

</details>

18. Route 53 weighted routing between two environments is used in which strategy?
   - **A.** Canary
   - **B.** Rolling
   - **C.** Blue-green
   - **D.** In-place

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** DNS-weighted cutover between two full environments (Route 53) is a blue-green mechanism.

**Why A is wrong:** Canary percentages are usually done at the ALB, not DNS-weighted across full envs.
**Why B is wrong:** Rolling has no two-environment DNS cutover.
**Why D is wrong:** In-place has a single environment.

</details>

19. Which deployment needs the most mature monitoring and feature-flag tooling?
   - **A.** In-place
   - **B.** Rolling
   - **C.** Canary
   - **D.** Blue-green

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Canary depends on metrics/monitoring to decide promotion or rollback at each stage.

**Why A is wrong:** In-place needs only basic monitoring.
**Why B is wrong:** Rolling needs standard monitoring, less mature than canary's staged gates.
**Why D is wrong:** Blue-green needs standard monitoring; the flip is binary, not metric-gated.

</details>

20. You must patch 200 pods one at a time while keeping 199 available. Which strategy?
   - **A.** In-place
   - **B.** Rolling
   - **C.** Blue-green
   - **D.** Canary

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Rolling updates instances incrementally (e.g., maxUnavailable=1) while preserving capacity — matches the 199-available goal.

**Why A is wrong:** In-place would stop pods, dropping availability below 199.
**Why C is wrong:** Blue-green would duplicate all 200, not patch one-at-a-time.
**Why D is wrong:** Canary targets user percentage, not per-pod incremental patching.

</details>
