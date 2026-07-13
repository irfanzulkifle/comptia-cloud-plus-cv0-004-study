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
   A. Rolling
   B. Canary
   C. Blue-green
   D. In-place
   **Answer: C — Blue-green provides near-zero downtime and instant traffic-flip rollback.**

2. You want to expose a new feature to 5% of users first and watch error rates before widening. Which strategy?
   A. In-place
   B. Canary
   C. Blue-green
   D. Rolling
   **Answer: B — Canary releases gradually to a small cohort with metric-driven promotion.**

3. An update replaces running instances a few at a time while the app stays available, with no second full environment. Which strategy?
   A. Rolling
   B. Blue-green
   C. Canary
   D. In-place
   **Answer: A — Rolling updates instances in batches without a duplicate environment.**

4. During a scheduled Sunday maintenance window, an internal tool is stopped, patched, and restarted. Which strategy?
   A. Canary
   B. Blue-green
   C. In-place
   D. Rolling
   **Answer: C — In-place is cheapest and acceptable when downtime is planned.**

5. Which strategy has the highest infrastructure cost during deployment?
   A. Rolling
   B. In-place
   C. Blue-green
   D. Canary
   **Answer: C — Blue-green runs two full production environments simultaneously.**

6. Which strategy offers the slowest rollback if a defect is found late in the rollout?
   A. Blue-green
   B. Rolling
   C. Canary
   D. In-place
   **Answer: B — Rolling requires re-deploying the old version batch-by-batch.**

7. A stateful legacy app cannot run two versions at once and downtime is tolerated. Best choice?
   A. Blue-green
   B. In-place
   C. Canary
   D. Rolling
   **Answer: B — In-place avoids version coexistence for stateful systems.**

8. AWS CodeDeploy traffic-shift options like Canary10Percent5Minutes implement which strategy?
   A. Blue-green
   B. Rolling
   C. In-place
   D. Canary
   **Answer: D — CodeDeploy canary shifts implement Canary deployments.**

9. Which strategy keeps both old and new versions running simultaneously, requiring version-tolerant apps?
   A. In-place
   B. Blue-green
   C. Rolling
   D. Canary
   **Answer: C — Rolling runs mixed old/new instances during the update.**

10. For a regulated system needing a clean audit trail of before/after states, which is preferred?
    A. Rolling
    B. In-place
    C. Blue-green
    D. Canary
    **Answer: C — Blue-green gives a clean, separable before/after environment.**

11. An ALB weighted target-group split sending 10% of traffic to a new version describes which strategy?
    A. Canary
    B. Rolling
    C. Blue-green
    D. In-place
    **Answer: A — Percentage traffic splits are the hallmark of Canary.**

12. Which strategy is best for a stateless microservice fleet on a tight budget?
    A. Blue-green
    B. Rolling
    C. In-place
    D. Canary
    **Answer: B — Rolling balances uptime and cost without a duplicate env.**

13. Elastic Beanstalk immutable deployments are an AWS implementation of which strategy?
    A. In-place
    B. Rolling
    C. Canary
    D. Blue-green
    **Answer: D — Immutable deployments spin up a fresh set, like blue-green.**

14. If the priority is limiting the "blast radius" of a bad release, choose:
    A. In-place
    B. Blue-green
    C. Canary
    D. Rolling
    **Answer: C — Canary limits impact to a small user subset.**

15. Which strategy typically causes full application downtime?
    A. Canary
    B. Rolling
    C. Blue-green
    D. In-place
    **Answer: D — In-place stops the service during the update.**

16. Amazon ECS rolling update with minimum/maximum healthy percent implements which strategy?
    A. Blue-green
    B. Rolling
    C. Canary
    D. In-place
    **Answer: B — ECS rolling updates replace tasks gradually.**

17. A team wants instant rollback but cannot afford two full environments. Best compromise?
    A. Canary
    B. Blue-green
    C. In-place
    D. Rolling
    **Answer: A — Canary gives fast staged rollback at low extra cost.**

18. Route 53 weighted routing between two environments is used in which strategy?
    A. Canary
    B. Rolling
    C. Blue-green
    D. In-place
    **Answer: C — DNS-weighted cutover between envs is blue-green.**

19. Which deployment needs the most mature monitoring and feature-flag tooling?
    A. In-place
    B. Rolling
    C. Canary
    D. Blue-green
    **Answer: C — Canary depends on metrics to decide promotion/rollback.**

20. You must patch 200 pods one at a time while keeping 199 available. Which strategy?
    A. In-place
    B. Rolling
    C. Blue-green
    D. Canary
    **Answer: B — Rolling updates instances incrementally with capacity preserved.**
