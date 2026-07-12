# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.2: Given a Scenario, Implement Appropriate Deployment Strategies

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 2.0 Domain = 19% of total exam
> **Objective 2.2 Focus:** Choosing the right release strategy (Blue-green, Canary, Rolling, In-place) based on application requirements, risk tolerance, infrastructure, and rollback needs.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 2.2
### Objective Name: *Given a scenario, implement appropriate deployment strategies.*

**Sub-objectives (the four strategies you must know):**
- Blue-green
- Canary
- Rolling
- In-place

**What this objective means (beginner-friendly):**
Imagine you're updating the menu at a busy restaurant:
1. **Blue-green** — You open a brand-new restaurant next door with the new menu. When the new one is ready, you redirect all customers there. If the new menu is a disaster, you send everyone back to the old one. Two parallel kitchens, instant cutover.
2. **Canary** — You keep the old menu but offer the new dish to 5% of customers first (the ones who like to try new things). If they love it, you offer it to 25%, then 50%, then everyone. If they hate it, you pull it and nobody else ever sees it.
3. **Rolling** — You swap out one table at a time. Table 1 gets the new menu, then Table 2, then Table 3, until the whole restaurant is on the new menu. Customers see a mix during the swap.
4. **In-place** — You shut down the whole kitchen, replace the menu, then reopen. Cheapest, simplest, but the restaurant is closed during the change.

In cloud terms: these are the four ways to release a new version of your application — each has different trade-offs around **downtime, cost, risk, rollback speed, and complexity**.

**Why it matters in the real world:**
- A bad deployment strategy causes outages. Choosing the wrong one for a critical system can cost millions per minute (think: Black Friday e-commerce, payment processing, hospital systems).
- Modern DevOps and SRE teams are measured on deployment frequency + change failure rate + mean time to recovery — picking the right strategy drives all three.
- Cloud+ admins must recommend the right strategy when given business constraints: budget, risk tolerance, RTO/RPO, traffic patterns, regulatory requirements.

**Why CompTIA tests it:**
- 2.2 is a **scenario-driven** objective — the exam will describe a workload's characteristics and ask you to pick the right strategy.
- It tests real-world judgment, not just definitions. Mis-choosing the strategy is a top cause of real-world production incidents.
- It pairs tightly with 2.1 (deployment models) and 2.3 (migrations) — the three together form the heart of Domain 2.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.2.1 — Blue-Green Deployment

**Definition:**
A release strategy that runs **two identical production environments side-by-side** — one is the "Blue" (current live version), the other is the "Green" (new version). Traffic is shifted from Blue to Green all at once (atomic cutover) using a router, load balancer, or DNS change. Rollback is as simple as pointing traffic back to Blue.

**Purpose:**
- Achieve **near-zero downtime** during deployments.
- Enable **instant rollback** if the new version is broken.
- Allow the new version to be fully tested in production-like conditions before being exposed to users.

**How it works (step by step):**
1. **Blue environment** is live in production, serving 100% of user traffic.
2. The new version is deployed to the **Green environment** (which is identical in size and config).
3. Green is **tested** — smoke tests, integration tests, synthetic transactions, security scans.
4. The router/load balancer/DNS is **switched** so 100% of traffic now flows to Green. Blue goes idle.
5. Blue is **kept warm** (or kept running) for the duration of the new release, in case rollback is needed.
6. After confidence is established (hours or days), Blue is decommissioned or kept as the next deployment target.
7. Next deployment: roles reverse — Green becomes the new Blue, and a fresh Green is built for the next version.

**Critical detail — the database:**
- Stateless apps (e.g., API gateways, web frontends) are easy: just point the LB.
- **Stateful apps** (databases, session state) are the hard part. The Green and Blue environments typically **share the same database** (or use a replicated/CDC-fed DB). If you use two separate databases, you need a data-sync strategy.

**Advantages:**
- **Zero downtime** during the cutover.
- **Instant rollback** — flip the LB back to Blue. Seconds to recover.
- **Full production-like testing** — Green runs on production-sized infrastructure before users see it.
- **Reduced risk** — broken release affects 0% of users if you can detect issues pre-cutover.
- **Predictable** — no gradual metrics to interpret; it's a clean switch.

**Disadvantages:**
- **2× infrastructure cost** during the cutover window (you run both environments). For a brief window, this can double your bill.
- **Database complexity** — sharing state across two environments is non-trivial.
- **Stateful session issues** — in-flight user sessions may be lost on cutover unless sessions are externalized (Redis, DB).
- **Wasteful for low-traffic apps** — running two full environments for a tiny app is overkill.
- **Single big-bang switch** — if the new version has a subtle bug, you might not catch it during pre-cutover tests. Better for *catastrophic* failures than *subtle* performance regressions.
- **Cold-start of Green** — first traffic may hit un-warmed caches and DB pools, causing latency spikes.

**When to use:**
- **Critical applications** where any downtime is unacceptable (e.g., payment, banking, healthcare).
- **Major version releases** where you want a clean cutover with instant rollback safety net.
- **Regulated environments** that require the new version to be fully tested in production-like infra before exposure.
- **Apps with predictable, bursty traffic** (e.g., seasonal retail) where you can absorb the 2× cost during a controlled release window.
- **AWS Elastic Beanstalk, Azure App Service "Deployment Slots," Google App Engine "Versions"** — all support blue-green natively.

**When NOT to use:**
- **Tight budget** — you can't afford 2× infrastructure.
- **Massive stateful databases** with no clean way to share state across environments.
- **Continuous-delivery shops** deploying 50+ times a day — blue-green per-deploy is operationally expensive.
- **Apps with stateful user sessions that can't be externalized.**

**CompTIA cue words:** "zero downtime," "instant rollback," "parallel environment," "two production environments," "atomic cutover," "production-like testing."

---

### 2.2.2 — Canary Deployment

**Definition:**
A release strategy that **rolls out a new version to a small subset of users first** (the "canaries," like the canary in a coal mine). The new version is monitored for errors, latency, and business KPIs. If healthy, the rollout is **gradually increased** to a larger audience. If unhealthy, the canary is rolled back and 100% of users stay on the old version.

**Purpose:**
- Limit the **blast radius** of a bad release — only 5% of users are affected, not 100%.
- Test in **real production conditions** with real user traffic (not synthetic).
- Detect **subtle bugs, performance regressions, and business-metric regressions** that pre-prod tests miss.
- Enable **data-driven rollout decisions** based on live metrics (error rate, latency, conversion).

**How it works (step by step):**
1. The new version (canary) is deployed alongside the current stable version.
2. A small percentage of user traffic is routed to the canary (typically 1%–10%).
3. **Metrics are compared** between canary and stable:
   - Error rate (5xx HTTP, exceptions)
   - Latency (p50, p95, p99)
   - Business KPIs (conversion, revenue, signups)
   - Resource utilization (CPU, memory, DB connections)
4. If metrics are healthy, the canary percentage is **gradually increased** (e.g., 5% → 10% → 25% → 50% → 100%).
5. If metrics degrade, the canary is **rolled back** automatically or manually — only the small canary population is affected.
6. Once at 100%, the stable version is decommissioned.

**Canary dimensions — who is the "small subset"?**
- **Random % of users** (e.g., 5% of all requests).
- **Specific geography** (e.g., users in Australia only).
- **Specific user cohort** (e.g., internal employees, beta users, paying customers only).
- **Specific device or platform** (e.g., Chrome on desktop only).
- **Header or cookie** (e.g., `X-Canary: true`).

**Advantages:**
- **Lowest blast radius** — bugs affect a small percentage, not everyone.
- **Real production data** — synthetic tests miss real-world issues; canary catches them.
- **Data-driven decisions** — promote or rollback based on metrics, not gut feel.
- **Faster than waiting for full canary analysis** — you can gradually promote as confidence builds.
- **Cheaper than blue-green** — only 5–10% extra capacity during canary, not 100%.

**Disadvantages:**
- **Slower rollout** — a full canary canary canary can take hours or days.
- **Complex traffic routing** — you need service-mesh / load-balancer features that split traffic by percentage.
- **Difficult for stateful apps** — users with in-flight state may bounce between versions.
- **Hard to A/B test cleanly** — if you want *both* versions running for statistical comparison, canary is a tool for that too, but the analysis gets more complex.
- **Statistical noise** — at 1% canary, you may not have enough traffic to detect issues quickly. Need adequate volume.
- **Database schema changes are tricky** — both old and new versions must be compatible with the schema during the canary window.
- **Monitoring burden** — you need robust observability (metrics, logs, traces) to compare canary vs. stable.

**When to use:**
- **High-traffic consumer apps** where you have the volume to detect issues in a small canary.
- **Continuous-delivery shops** that deploy frequently and want low-risk rollouts.
- **Apps with strong observability** (Prometheus, Datadog, New Relic) — you need metrics to make the rollout decision.
- **When real-user testing matters more than absolute zero-downtime.**
- **Feature flags + canary** is a powerful combo — gradually enable a feature for a small cohort.

**When NOT to use:**
- **Low-traffic apps** — 1% of 100 users/day is meaningless.
- **Apps with no observability** — you can't make a rollout decision without metrics.
- **Hard database migrations** — the schema needs to be compatible with both old and new code during the canary.
- **Hard real-time / safety-critical systems** where a 1% failure rate is unacceptable (use blue-green for these).
- **Regulated environments** that require pre-prod certification of every release.

**CompTIA cue words:** "small subset," "gradual rollout," "percentage of users," "real production testing," "low blast radius," "metrics-driven promotion."

**Industry tools:**
- **AWS:** AWS App Mesh, ECS / EKS with weighted target groups, AWS CodeDeploy (canary / linear / all-at-once), CloudWatch Evidently, SageMaker (for ML canary).
- **Azure:** Azure DevOps Pipelines (canary strategy), AKS with Azure Traffic Manager / App Gateway, Azure App Configuration feature flags.
- **Google Cloud:** GKE + Istio (traffic splitting), Cloud Run (traffic splitting between revisions), Firebase Remote Config, Google Cloud Deploy (spinnaker-style canary).
- **Open-source:** Argo Rollouts, Flagger, Spinnaker, Istio, Linkerd.

---

### 2.2.3 — Rolling Deployment

**Definition:**
A release strategy that **gradually updates instances of the application in batches** (or one at a time), until all instances are running the new version. During the rollout, **both old and new versions run simultaneously**, serving the same user base. The end state is 100% on the new version, with no separate "old" environment retained.

**Purpose:**
- **No extra infrastructure** — you reuse your existing fleet, just update it in waves.
- **Gradual rollout** with built-in risk reduction (a single bad batch affects only some users).
- **Continuous deployment** at scale — Kubernetes default, ECS default, Azure VMSS default.

**How it works (step by step):**
1. The current production environment runs N instances of the old version (e.g., 10 servers).
2. A **batch size** is chosen (e.g., 2 instances at a time, or 20% of the fleet).
3. A subset of instances is **taken out of the load balancer** (drained) and updated to the new version.
4. The updated instances are **re-added** to the load balancer, now serving some traffic.
5. Steps 3–4 repeat for the next batch.
6. Eventually, 100% of instances are on the new version.
7. (Optional) Auto-scaling replaces any unhealthy instances automatically.

**Critical detail — order of operations per batch:**
- The "right" rolling update is: **drain → update → health-check → re-accept traffic**.
- A bad implementation: update in place, no drain → users see 502/503 errors as instances are mid-update.

**Two common patterns:**
- **Immutable rolling** — terminate old instance, launch new instance with new version. Safer (clean state per batch).
- **In-place rolling** — update the existing instance (e.g., pull new image, restart container). Cheaper but stateful-data risk.

**Advantages:**
- **No extra infrastructure** — reuses the production fleet. Most cost-efficient.
- **Zero additional cost** vs. running the same fleet anyway.
- **Built into Kubernetes** — the default `Deployment` strategy is rolling update.
- **Built into Azure VMSS** — default upgrade policy.
- **Built into AWS Auto Scaling Groups, ECS, EKS.**
- **Gradual risk** — a bad release affects 10–20% at a time, not 100% all at once.
- **No big-bang switch** — fewer abrupt issues from cold caches, etc.

**Disadvantages:**
- **Both versions run simultaneously** — can cause issues if old and new are incompatible (e.g., different message-format expectations on a shared queue).
- **Rollback is slower** — you have to roll the fleet *backward* through the same batches. Takes time.
- **Stateful session issues** — user requests may bounce between old and new versions.
- **Database schema changes** — both versions must be compatible with the schema during rollout. Common pattern: expand-contract migrations.
- **No full production-like test** — you only test on production traffic, batch by batch.
- **Longer total deployment time** — for large fleets, can take hours.
- **Harder to abort mid-way** — once you start, you have a half-old/half-new fleet until you finish or roll back.

**When to use:**
- **Stateless services** (web frontends, APIs) where both versions can serve the same requests.
- **Microservices in Kubernetes** — the default deployment strategy.
- **Large fleets** where running two full environments (blue-green) is too expensive.
- **Frequent deployments** — rolling is cheap enough to do many times a day.
- **Apps with horizontal scaling** (multiple instances behind a load balancer).
- **Standard, low-risk updates** — bug fixes, minor version bumps.

**When NOT to use:**
- **Single-instance apps** (no fleet to roll) — use blue-green or in-place.
- **Hard schema migrations** that can't be backward-compatible.
- **Apps requiring 100% homogeneous behavior** during the rollout (e.g., some distributed algorithms).
- **Critical big-bang changes** where the cost of any partial state is too high.

**CompTIA cue words:** "incremental," "batches," "one at a time," "both versions run simultaneously," "no extra infrastructure," "default for Kubernetes / VMSS / ASG."

---

### 2.2.4 — In-Place Deployment

**Definition:**
A release strategy that **updates the application on the existing infrastructure** — no new instances, no parallel environment. The application is stopped, updated, and restarted on the *same* servers. If the deployment is not designed for zero-downtime, there is a **maintenance window** where the service is unavailable.

**Purpose:**
- Provide the **simplest, cheapest** deployment strategy.
- Suit apps where brief downtime is acceptable, or where the app is architected for **hot reload** (e.g., reload a config, restart a service in a way that doesn't take down the whole user experience).

**How it works (step by step):**
1. The application (or server) is **stopped or quiesced**.
2. The new code / configuration / image is **installed on the same infrastructure**.
3. The application is **restarted** on the same server.
4. (If a fleet) Repeat for each instance, one at a time, until all are updated.
   - *Note: a rolling in-place update is essentially a hybrid — using the in-place strategy on a fleet of instances.*

**Two flavors:**
- **In-place with downtime** — stop everything, update, restart. User sees an outage.
- **In-place with no downtime** — typically uses a hot-reload mechanism (e.g., `kill -HUP` to reload config, or a service mesh hot-restart). Still uses the same instance.

**Distinguishing feature:**
- The **infrastructure stays the same** — same IP, same hostname, same disk. The new code replaces the old in place.
- Compare to blue-green (new fleet) and rolling (new instances replace old in batches via terminate-and-relaunch).

**Advantages:**
- **Simplest** — no parallel environment to manage.
- **Cheapest** — no extra infrastructure at all.
- **Easiest to understand** — even junior engineers can do it.
- **Best for stateful, single-instance workloads** where launching a new instance would lose local state.
- **No new IP, no DNS change** — useful for hard-coded clients or legacy integrations.

**Disadvantages:**
- **Downtime** — if not designed for hot-reload, the app is down during the update.
- **Hard to rollback** — you overwrote the old code; you may need to redeploy the old version (and hope you have it cached).
- **Risky on production** — a failed in-place update can leave the system in a broken state.
- **No parallel testing** — you're updating the live system.
- **Capacity risk** — during the update, the system is at reduced capacity (one server being updated is not serving traffic).
- **Slow for large fleets** — if you do it one server at a time, you have N updates to perform.
- **Lost in-flight state** — local memory, open files, in-progress requests are lost on restart.

**When to use:**
- **Non-critical apps** where brief downtime is acceptable.
- **Single-instance legacy apps** that can't easily scale horizontally.
- **Maintenance windows** — scheduled downtime the user is informed about.
- **Dev / test environments** — fastest way to push changes.
- **Configuration updates** that don't require a full restart (e.g., nginx reload).
- **Stateful apps** where the state is tied to the host (local DB files, in-memory cache).

**When NOT to use:**
- **Production, customer-facing, always-on services.**
- **Regulated environments** that forbid downtime windows.
- **Multi-instance fleets** where blue-green or rolling would be safer.
- **Apps with high availability SLAs** (99.9%+) — every minute of downtime hurts.

**CompTIA cue words:** "simplest," "cheapest," "downtime," "maintenance window," "same infrastructure," "no parallel environment," "config reload."

**Common real-world examples of in-place:**
- `apt upgrade` on a Linux server.
- `yum update` on RHEL.
- A Windows Server with WSUS pushing patches.
- A legacy on-prem ERP being updated during a Sunday maintenance window.
- A single-instance VM running a custom app, restarted after a code push.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — Blue-Green in the Wild

**AWS Example:**
- **Customer:** Amazon's own retail platform famously uses blue-green at massive scale. New versions are deployed to the "Green" fleet, smoke-tested with synthetic traffic, then 100% of traffic is shifted via Route 53 weighted records. If errors spike, traffic shifts back to "Blue" within seconds.
- **Service mapping:**
  - **AWS Elastic Beanstalk** — supports blue-green via "swap environment URLs."
  - **AWS CodeDeploy** — "blue/green" deployment configuration for EC2, ECS, Lambda.
  - **Amazon ECS** — blue-green via CodeDeploy with two target groups and listener routing.
  - **AWS Lambda** — Lambda aliases with weighted routing (e.g., 0% to new version, 100% to old; flip to 100%/0%).
  - **Amazon Route 53** — DNS-weighted records for blue-green at the global level.
  - **AWS Direct Connect + Global Accelerator** — for blue-green at the network edge.
- **Architecture:**
  ```
  Route 53 (DNS) →  ALB →  Blue Target Group  (current prod, 100%)
                            Green Target Group (new version, 0%,  warming up)
  ```
  After cutover:
  ```
  Route 53 → ALB →  Blue Target Group  (0%, kept warm for rollback)
                       Green Target Group (100%, new prod)
  ```

**Azure Example:**
- **Customer:** Large enterprises deploying .NET apps to **Azure App Service** routinely use **deployment slots** for blue-green. The "staging" slot hosts the new version; the "production" slot hosts the current version. A single "swap" operation in the Azure portal (or via CLI) atomically swaps both slots — traffic cutover is instant.
- **Customer:** Microsoft itself uses blue-green to deploy updates to Office 365 and Azure portal.
- **Service mapping:**
  - **Azure App Service Deployment Slots** — the canonical Azure blue-green feature.
  - **Azure DevOps Pipelines** — supports blue-green via deployment jobs with traffic shifting.
  - **Azure Kubernetes Service (AKS)** with **Azure Traffic Manager** for blue-green at the cluster level.
  - **Azure Front Door** — global blue-green for multi-region traffic.

**Google Cloud Example:**
- **Customer:** Many SaaS companies on **Google App Engine** use App Engine's "versions" feature — split traffic between the current default version and a new candidate version. A 100% shift is blue-green.
- **Customer:** Google itself deploys many services via **GKE** with **Spinnaker** or **Google Cloud Deploy**, which support blue-green pipelines.
- **Service mapping:**
  - **Google App Engine Versions** — split traffic between revisions.
  - **Google Cloud Run** — revisions with traffic split (0%/100% or gradual).
  - **Google Kubernetes Engine (GKE)** with **Anthos Config Management** — multi-cluster blue-green.
  - **Google Cloud Load Balancing** + **Cloud DNS** — for global blue-green.

**Production architecture (typical AWS blue-green):**
1. Pipeline builds new AMI / container image → pushes to ECR.
2. **CodeDeploy** creates a new **Green** Auto Scaling Group, deploys the new image, runs validation.
3. CodeDeploy **shifts traffic** from the Blue ALB target group to the Green ALB target group (10% → 100% over 5 minutes for safety, or instant).
4. After 15–30 minutes of "soak time," the Blue ASG is terminated.
5. If a CloudWatch alarm fires (5xx > 1%), CodeDeploy rolls back to Blue.

---

### 3.2 — Canary in the Wild

**AWS Example:**
- **Customer:** Netflix deploys hundreds of times a day. Each release goes to a "canary" cluster of ~1% of total instances, monitored for errors, latency, and engagement KPIs. If healthy, the release progresses to 10%, 25%, 50%, 100%. If unhealthy, automatic rollback.
- **Service mapping:**
  - **AWS CodeDeploy** — canary / linear / all-at-once for Lambda and ECS.
  - **Amazon ECS** with **AWS App Mesh** — weighted canary at the service-mesh level.
  - **Amazon EKS + Istio** — full canary with traffic splitting.
  - **AWS CloudWatch Evidently** — feature flag + canary with statistical analysis.
  - **AWS Lambda** — alias with weighted traffic (0.05, 0.25, 0.5, 1.0).

**Azure Example:**
- **Customer:** Microsoft's own Azure portal deploys via canary — first 1% of internal users, then external canary, then full rollout.
- **Customer:** SaaS companies using **AKS + Azure Front Door + App Configuration** for canary rollouts.
- **Service mapping:**
  - **Azure DevOps Pipelines** — "canary" deployment strategy for AKS, VMSS, App Service.
  - **AKS + Azure App Gateway** — canary at the ingress.
  - **Azure App Configuration + Feature Manager** — canary / feature flag.
  - **Azure Monitor** — the metrics layer that drives canary decisions.

**Google Cloud Example:**
- **Customer:** Google Search, YouTube, Gmail — all use canary internally (the "Golden" and "Prod" canaries inside Google).
- **Customer:** SaaS apps on **Cloud Run** — canary traffic split between revisions (1% → 100%).
- **Service mapping:**
  - **Cloud Run** — traffic split between revisions.
  - **Google Kubernetes Engine (GKE)** with **Istio** or **Argo Rollouts** — canary at the service-mesh level.
  - **Google Cloud Deploy** — canary / rapid / canary-deploy pipelines.
  - **Firebase Remote Config** — canary at the mobile-client level.
  - **Google Cloud Build + Binary Authorization** — canary with policy gates.

**Production architecture (typical GKE canary with Argo Rollouts):**
```
Old ReplicaSet (stable)     ← 95% traffic
New ReplicaSet (canary)     ← 5% traffic
       ↓
  Prometheus / Datadog metrics (success rate, latency)
       ↓
  Argo Rollouts controller: if healthy, shift 5%→25%→50%→100%
                              if unhealthy, abort and rollback
```

**Real-world canary at Meta / Facebook:**
- "Dark canary" — push code to production servers, but don't route any real traffic yet. Run synthetic tests on the new code on production hardware.
- "1% canary" — 1% of real user traffic hits the new code. Compare to control group.
- "Equal canary" — split 50/50 between canary and control for A/B testing.

---

### 3.3 — Rolling in the Wild

**AWS Example:**
- **Customer:** Most Kubernetes users on **EKS** use rolling updates as the default — Kubernetes' `Deployment` strategy.
- **Customer:** Auto Scaling Groups with `UpdatePolicy: RollingUpdate` — instance refresh replaces instances in batches.
- **Service mapping:**
  - **Amazon EKS / Kubernetes Deployments** — `strategy: RollingUpdate` with `maxSurge` and `maxUnavailable`.
  - **Amazon ECS** — rolling deploy via ECS service update (default).
  - **AWS CodeDeploy** — "rolling" deployment configuration (e.g., 50% at a time).
  - **AWS Auto Scaling Instance Refresh** — rolling replacement of instances in an ASG.

**Azure Example:**
- **Customer:** Any enterprise running **Azure Kubernetes Service (AKS)** with default deployment settings.
- **Customer:** Enterprises using **Azure Virtual Machine Scale Sets (VMSS)** with rolling upgrade policy.
- **Service mapping:**
  - **AKS** — default rolling update in Kubernetes.
  - **Azure VMSS** — "Automatic" or "Rolling" upgrade policy.
  - **Azure App Service** — although slots are more common, basic App Service plans do rolling by default.
  - **Azure Service Fabric** — rolling upgrades for microservices.

**Google Cloud Example:**
- **Customer:** Anyone using **GKE** defaults gets rolling updates.
- **Customer:** **Migrate to Virtual Machines** uses rolling cutover for VM migrations.
- **Service mapping:**
  - **GKE** — default `Deployment` rolling update strategy.
  - **Google Cloud Run** — gradual rollout (between instant and canary).
  - **Google Kubernetes Engine + Argo Rollouts** — advanced rolling with metrics.
  - **Compute Engine MIG (Managed Instance Group)** — rolling update / instance refresh.

**Production architecture (typical Kubernetes rolling):**
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2          # can have 2 extra pods during update
      maxUnavailable: 1    # can have 1 pod unavailable during update
  template:
    spec:
      containers:
        - image: myapp:v2
```

The controller:
1. Creates 2 new pods (maxSurge: 2).
2. Waits for them to be ready.
3. Terminates 1 old pod (maxUnavailable: 1).
4. Repeats until all pods are on the new version.

---

### 3.4 — In-Place in the Wild

**AWS Example:**
- **Customer:** Any enterprise patching **EC2** instances via **AWS Systems Manager Patch Manager** — pushes patches in place.
- **Customer:** Single-instance legacy apps on EC2 where horizontal scaling isn't possible.
- **Service mapping:**
  - **AWS Systems Manager (SSM) Run Command / State Manager** — pushes updates in place.
  - **AWS CodeDeploy** — "in-place" deployment configuration (the third option besides blue/green and rolling).
  - **Amazon EC2 Image Builder** — builds new AMIs, but the update of running instances is in-place.
  - **AWS OpsWorks** — Chef/Puppet-driven in-place configuration updates.

**Azure Example:**
- **Customer:** **Azure VMs** patched via **Azure Update Management** (part of Azure Automation) — in-place patching.
- **Customer:** Legacy Windows Server apps that require in-place updates.
- **Service mapping:**
  - **Azure VMSS with "Manual" upgrade mode** — in-place upgrade (the operator must trigger).
  - **Azure Automation Update Management** — schedules and applies in-place patches.
  - **Azure Arc** — manage in-place updates of on-prem / multi-cloud VMs.
  - **Azure App Service "Local Git" deployment** — basic in-place deploy.

**Google Cloud Example:**
- **Customer:** Patch management of **Compute Engine** instances via **OS Config** + **VM Manager**.
- **Customer:** Single-VM workloads that can't be easily re-imaged.
- **Service mapping:**
  - **Google Compute Engine OS Config** — patch and update in place.
  - **Google Cloud VM Manager** — patch management for VMs.
  - **Cloud Run "Direct VPC"** — code-only updates without redeploying infra (effectively in-place at the code layer).
  - **Google Kubernetes Engine (GKE) Recreate strategy** — a kind of in-place update: kill all pods, then start new ones. Causes downtime but is the simplest "in-place" at the pod level.

**Production architecture (typical in-place patch flow):**
```
SSM / Azure Update Management / OS Config
   ↓
1. Identify target instances.
2. Apply patch to a test instance (or test group).
3. Validate.
4. Schedule maintenance window.
5. Patch remaining instances in groups.
6. Roll back if validation fails.
```

**Real-world example — Black Friday at a retailer:**
- Even large retailers still use **scheduled in-place maintenance** for *non-customer-facing* systems (warehouse management, internal admin tools, analytics).
- A scheduled maintenance window is announced, the system is patched in place, and the team is on standby to roll back if anything breaks.
- This is the canonical in-place scenario for the exam.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Master Comparison: All Four Strategies

| Dimension | **Blue-Green** | **Canary** | **Rolling** | **In-Place** |
|---|---|---|---|---|
| **Definition** | Two parallel environments; switch traffic all at once | Roll out to small % of users, then expand | Update instances in batches one at a time | Update the same instances in place |
| **Infrastructure cost** | 2× during cutover | 1.05–1.10× (canary % capacity) | 1× (or slightly more with maxSurge) | 1× (cheapest) |
| **Downtime** | Zero | Zero | Zero (if implemented correctly) | Often non-zero (maintenance window) |
| **Rollback speed** | Instant (flip the LB) | Fast (reroute 0% to canary) | Slow (must roll back through batches) | Slow (redeploy old version) |
| **Risk to users** | Catastrophic if undetected pre-cutover | Lowest (small % only) | Moderate (batches) | Highest (everyone at once) |
| **Production-like testing** | Full (Green runs on prod-sized infra) | Real production data, but partial | Real traffic, batch by batch | Limited (synthetic only) |
| **Setup complexity** | High (2 envs to manage) | High (traffic splitting, metrics) | Low–Moderate (Kubernetes default) | Lowest |
| **Best for** | Critical, infrequent deploys | High-traffic, frequent deploys | Stateless, frequent deploys | Legacy, dev/test, non-critical |
| **Rollback difficulty** | Trivial | Trivial | Moderate (must roll back fleet) | Hard (must re-deploy old version) |
| **Database migration support** | Hard (shared DB or CDC) | Hard (compat schema required) | Hard (compat schema required) | Easiest (single host) |
| **CompTIA cue words** | "zero downtime," "parallel env," "instant rollback" | "small subset," "gradual," "real user testing" | "incremental," "batches," "no extra infra" | "simplest," "maintenance window," "downtime OK" |

---

### 4.2 — Speed / Cost / Availability / Risk Snapshot

| Strategy | **Speed to release** | **Cost** | **Availability during release** | **Risk to users** |
|---|---|---|---|---|
| Blue-green | ⚡ Instant cutover (after Green is ready) | 💲💲 High (2× infra) | 🟢 100% | 🟡 Catastrophic if missed |
| Canary | 🐢 Hours to days (gradual promotion) | 💲 Low (small extra capacity) | 🟢 100% | 🟢 Lowest (small % only) |
| Rolling | 🐢 Hours (depends on batch size and fleet size) | 💲 Lowest (reuses fleet) | 🟢 100% (with proper drain) | 🟡 Moderate (per-batch) |
| In-place | ⚡ Fast (just deploy) | 💲 Lowest | 🔴 Often < 100% (downtime) | 🔴 Highest (everyone at once) |

---

### 4.3 — Blue-Green vs Canary (Common Confusion)

| Dimension | **Blue-Green** | **Canary** |
|---|---|---|
| **Exposure of new version** | 0% or 100% (atomic) | 1% → 100% (gradual) |
| **Risk of catastrophic bug** | All-or-nothing — entire user base affected at once | Small percentage only |
| **Resource cost** | 100% extra (full parallel env) | 1–10% extra (canary replicas only) |
| **Rollback** | Instant flip | Gradual shift back to 0% |
| **Detection of subtle bugs** | Hard (synthetic tests only) | Easy (real-user metrics) |
| **When to use** | Big-bang, infrequent, critical releases | Frequent, low-risk incremental releases |
| **CompTIA phrasing** | "parallel environments," "swap" | "subset of users," "gradual promotion" |

**Exam tip:** If the question mentions "two full environments" → blue-green. If it mentions "1% of users" or "real-user testing" → canary.

---

### 4.4 — Rolling vs In-Place (The Tricky Distinction)

| Dimension | **Rolling** | **In-Place** |
|---|---|---|
| **Same instances used?** | Old instances terminated; new ones launched (or kept in a "stable" group) | Existing instances updated, kept running |
| **New IP / hostname during rollout?** | Yes (new instances get new IPs) | No (same instance, same IP) |
| **Cost** | Same as steady state (or slightly more during surge) | Lowest possible |
| **Downtime risk** | Zero (with proper drain) | Often non-zero |
| **CompTIA phrasing** | "batches," "incremental," "one at a time," "default in Kubernetes" | "simplest," "same host," "downtime window" |

**Trap:** Some textbooks say "rolling = in-place" because both update existing infrastructure. The strict CompTIA distinction:
- **Rolling** = update the *fleet* in batches (terminate-and-relaunch, or container replace).
- **In-place** = update the *single host* on which the app runs.

If the scenario says "single VM patched with new code" → **in-place**. If it says "10 EC2 instances updated two at a time" → **rolling**.

---

### 4.5 — Decision Matrix (When to Pick Each)

| If you need… | Pick |
|---|---|
| Zero downtime, instant rollback, can afford 2× cost | **Blue-Green** |
| Detect subtle bugs in production, low blast radius | **Canary** |
| Frequent deploys, cost-sensitive, stateless fleet | **Rolling** |
| Single-instance legacy app, brief downtime is OK | **In-Place** |
| Critical financial / healthcare system | **Blue-Green** |
| High-traffic consumer app, deploy 10×/day | **Canary** or **Rolling** |
| Maintenance-windowed legacy system | **In-Place** |
| Kubernetes default | **Rolling** |
| AWS CodeDeploy, ECS, ALB with two target groups | **Blue-Green** |
| AWS Lambda alias weighted traffic | **Canary** (or Blue-Green if 100% flip) |

---

### 4.6 — Deployment Strategy × Cloud Native Service Map

| Service | Default strategy | Can also do |
|---|---|---|
| Kubernetes Deployment | **Rolling** | Recreate (≈in-place), Blue-green (manual) |
| Kubernetes + Argo Rollouts | Configurable | Blue-Green, Canary, Rolling |
| Amazon ECS Service Update | **Rolling** | Blue-Green (via CodeDeploy) |
| AWS CodeDeploy (EC2/ECS/Lambda) | Configurable | In-Place, Blue-Green, Canary, Linear, All-at-Once |
| AWS Lambda alias | N/A (weighted routing) | Blue-Green (0%↔100%), Canary (5%→100%) |
| Azure App Service Deployment Slots | **Blue-Green** (swap) | — |
| Azure VMSS upgrade policy | **Rolling** (automatic) | Manual (= in-place) |
| Google Cloud Run revisions | N/A (traffic split) | Canary (1%→100%), Blue-Green (0%↔100%) |
| Google App Engine versions | N/A (traffic split) | Blue-Green, Canary |
| GKE (vanilla) | **Rolling** | Recreate (in-place) |
| Spinnaker / Argo / Flux | Configurable | All four |

---

## SECTION 5 — EXAM TRAPS

### Trap #1 — "Canary requires two parallel environments like blue-green."
**Wrong.** Canary typically runs alongside the stable version on the *same* infrastructure, splitting traffic by percentage. You might spin up a few extra "canary" instances, but you don't need a full second environment. Blue-green needs a full second environment. Canary needs only a small subset (often 1–10% of capacity).

**Exam phrasing:**
- "Two full production environments, switch all traffic at once" → **Blue-Green**
- "Deploy to 5% of users, monitor, then expand" → **Canary**

---

### Trap #2 — "Rolling = In-Place. They're the same thing."
**Wrong (in the strict CompTIA sense).**
- **Rolling** = update the *fleet* in batches. Old instances are removed, new ones are added. No same-host continuity.
- **In-place** = update the *same* host. Same IP, same disk, just new code on the same infrastructure.

**A rolling update on Kubernetes** typically *terminates* old pods and *creates* new ones — that's rolling, not in-place. A **single EC2 instance patched with SSM** is in-place.

**Right answer logic:**
- Scenario mentions "fleet," "batch," "20% at a time," "no extra infra but multiple instances" → **Rolling**.
- Scenario mentions "single server," "same host," "downtime window" → **In-Place**.

---

### Trap #3 — "Blue-green is always more expensive than canary."
**Sometimes wrong.** Blue-green is more expensive *during the cutover* (2× infra) and *immediately after* (Blue kept warm for rollback). Canary is cheaper at small percentages but can run for hours/days. Over the full lifecycle:
- Short, critical release: blue-green costs more in the moment.
- Long, gradual canary promotion: canary can cost more in total compute-hours if it runs for 24+ hours.

**Exam phrasing:** The default expectation is blue-green = more expensive. If a question says "least expensive *during* the release" → canary or rolling. If "instant rollback is required" → blue-green regardless of cost.

---

### Trap #4 — "Canary replaces blue-green for everything."
**Wrong.** Canary is better for *subtle* issues, blue-green is better for *catastrophic* issues that need to be detected pre-cutover. Many mature orgs use **both**:
- **Blue-green** for the initial cutover (zero-downtime).
- **Canary** *within* blue-green (e.g., 5% canary on Green before shifting 100%).

The exam will give you scenarios where canary is wrong (e.g., low-traffic apps with no statistical power, hard schema migrations, or regulated environments that require pre-prod certification).

---

### Trap #5 — "In-place means the service is always down."
**Not necessarily.** In-place *can* be zero-downtime if the app supports **hot reload** (e.g., nginx `-s reload`, a long-running process that picks up new code on signal). But the default assumption in the exam is that in-place involves a **maintenance window** with downtime.

**Exam phrasing:**
- "Hot reload, no downtime" → in-place (if you can do it).
- "Maintenance window announced, brief outage" → in-place.
- "Always-on, zero downtime" → NOT in-place (use rolling, canary, or blue-green).

---

### Trap #6 — "Rolling update is the same as blue-green if you set the batch size to 100%."
**Wrong.** Setting a rolling update's batch size to 100% is essentially **in-place *for the whole fleet at once*** (Recreate strategy in Kubernetes), which is *not* blue-green.
- **Blue-green** keeps the old environment running during the new environment's life.
- **Rolling 100% batch** kills the old before the new is proven.

A 100%-batch rolling update has the same risk as in-place: no parallel environment for rollback. Blue-green specifically maintains Blue for instant rollback.

---

### Trap #7 — "Canary is only for big companies like Netflix and Google."
**Wrong.** Canary works for any high-traffic app. The defining feature is **real-user testing on a small subset**. Even mid-sized SaaS apps use canary effectively.
- What matters is **traffic volume** — at 1% of 100 users/day, canary is meaningless.
- At 1% of 1M users/day, canary gives you 10,000 real users for metrics. Plenty.

**Exam phrasing:** "High-traffic consumer app, frequent deploys" → canary is fine. "Internal admin tool with 50 users" → canary is overkill; use rolling or in-place.

---

### Trap #8 — "Blue-green is the safest strategy, so always use it."
**Wrong.** Blue-green is the safest *for catastrophic failures*, but it has its own risks:
- 2× cost.
- Database migration challenges.
- Cold-start latency on Green (un-warmed caches).
- Subtle bugs that pre-cutover tests miss become *catastrophic* for 100% of users at once.

The safest strategy is the **right** one for the situation. Canary + good observability often beats blue-green in real-world safety.

---

### Trap #9 — "Rolling update always gives zero downtime."
**Wrong (in implementation).** A poorly-implemented rolling update (e.g., no drain, no health check, terminates instance before new one is ready) causes downtime. The strategy *enables* zero downtime; it doesn't *guarantee* it. The exam's default assumption is that "rolling" = zero downtime *if implemented correctly*.

---

### Trap #10 — "You can use any strategy for any app."
**Wrong.** Schema-breaking database changes cannot use canary or rolling (both versions must run simultaneously with a compatible schema). Stateful single-instance apps cannot easily do blue-green (where do you put the state?). The exam will test these constraints.

**Exam phrasing:** "Major DB schema change required" → blue-green with shared DB and expand-contract migration, OR in-place with maintenance window. NOT canary or rolling without a compat schema.

---

### Trap #11 — "Blue-green equals high availability."
**Confused concept.** Blue-green is a *deployment* strategy, not a *runtime* HA architecture. Your app can still be a single point of failure at runtime even with blue-green deploys. HA is achieved with **multiple instances, load balancing, multi-AZ, multi-region** — separate from the deployment strategy.

---

### Trap #12 — "In-place is the worst strategy."
**Not always.** In-place is the *simplest* and *cheapest*. For non-critical apps, dev environments, or apps that genuinely can't scale horizontally, in-place is the *right* strategy. The exam tests whether you can pick the right strategy for the situation, not whether you always pick the "fanciest" one.

---

## SECTION 6 — PERFORMANCE-BASED QUESTION PREP

> *CompTIA PBQs for 2.2 typically present a scenario with specific constraints and ask you to choose the right deployment strategy — and to defend the choice.*

---

### PBQ 1 — "The Payment Processor"

**Scenario:**
A fintech company processes 50,000 credit-card transactions per minute. Requirements:
- Downtime during a release must be **zero**.
- A bad release must be **rolled back within 30 seconds**.
- The company is willing to pay 2× infrastructure cost during the release window.
- The CTO wants a "clean cutover" with no gradual rollout.

**Your task:** Identify the deployment strategy.

**Step-by-step solution:**

1. **Zero downtime** → eliminates **in-place** (which typically has a maintenance window).
2. **30-second rollback** → eliminates **rolling** (rollback is slow) and **canary** (rollback requires re-routing).
3. **2× infrastructure cost is acceptable** → blue-green is allowed.
4. **"Clean cutover" / no gradual rollout** → eliminates **canary** (which is gradual by definition).
5. The answer: **Blue-Green**.
   - Two identical environments (Blue = current, Green = new).
   - Deploy new version to Green, test, then atomic switch.
   - If 5xx spikes within 30 seconds, flip the load balancer back to Blue.
   - 2× cost is acceptable per the requirement.

**Answer:** **Blue-Green deployment**.

---

### PBQ 2 — "The Mobile Game"

**Scenario:**
A mobile game with 5 million daily active users wants to release a new feature (in-app purchase flow). Requirements:
- The team wants to detect subtle bugs and business regressions (conversion rate drop, crash rate increase).
- The release should affect as few users as possible if buggy.
- The company has 50+ deploys per day.
- Strong monitoring exists (Datadog, Sentry, Mixpanel).

**Your task:** Choose the strategy and design the rollout.

**Step-by-step solution:**

1. **Detect subtle bugs in real production** → **Canary** is the right tool (real-user testing beats synthetic).
2. **Affect as few users as possible** → Canary by definition starts at 1–5%.
3. **Frequent deploys + strong monitoring** → Canary's gradual promotion (5% → 25% → 50% → 100%) fits the cadence.
4. **Rollout design:**
   - Deploy new version to 1% of pods in the cluster.
   - Route 1% of traffic to the new version.
   - Monitor for 1 hour:
     - Crash rate < 0.1%?
     - p95 latency < 200ms?
     - Conversion rate within ±2% of control?
   - If all good → 10% → 30% → 50% → 100%.
   - If anything degrades → automatic rollback, only 1% of users affected.
5. **Implementation:**
   - **GKE + Argo Rollouts** with Prometheus metrics.
   - Or **AWS App Mesh** with weighted target groups.
   - Or **Cloud Run** with traffic split between revisions.

**Answer:** **Canary deployment** (with metrics-driven promotion).

---

### PBQ 3 — "The E-Commerce Platform on Kubernetes"

**Scenario:**
A retailer's e-commerce site runs on Kubernetes with 50 stateless API pods. Requirements:
- The team deploys new versions 3× per day.
- A bad release must not affect 100% of users.
- The team cannot afford to run a second full environment (cost).
- The new version is **compatible with the current database schema** (no migration needed).

**Your task:** Choose the strategy.

**Step-by-step solution:**

1. **Frequent deploys + cost-sensitive + stateless + schema-compatible** → **Rolling deployment** is the natural fit.
2. **"Bad release must not affect 100% of users"** → confirms we want gradual (rolling) over atomic (blue-green).
3. **"Cannot afford a second full environment"** → rules out blue-green.
4. **"Real-user testing" not specified** → canary isn't necessary; standard rolling gives 10-batches-of-5 protection.
5. **Configuration:**
   ```yaml
   strategy:
     type: RollingUpdate
     rollingUpdate:
       maxSurge: 5         # 5 extra pods during update
       maxUnavailable: 0   # never go below current capacity
   ```
6. **Rollback:** If 5xx spikes after a batch, `kubectl rollout undo` rolls the fleet back through the same batches.

**Answer:** **Rolling deployment** (Kubernetes default).

---

### PBQ 4 — "The Legacy CRM"

**Scenario:**
A 20-year-old CRM is a single Windows Server VM with a local SQL Server Express database. Requirements:
- The CRM cannot easily scale horizontally.
- The team has scheduled a **Sunday 2 AM maintenance window** for patches.
- Brief downtime (up to 30 minutes) is acceptable during the window.
- The local SQL Server data must be preserved.

**Your task:** Choose the strategy.

**Step-by-step solution:**

1. **Single VM + local DB** → cannot easily do blue-green (no second host for state).
2. **Maintenance window acceptable** → in-place is allowed.
3. **State preservation** → in-place update (vs. terminate-and-relaunch) keeps the same host, same local DB files.
4. **Procedure:**
   - Notify users of the maintenance window.
   - Take a snapshot of the VM.
   - Stop the CRM service.
   - Apply the patch / deploy new code.
   - Restart the CRM service.
   - Smoke-test.
   - If broken, restore from snapshot.
5. **Why NOT blue-green?** You'd need to clone the VM (including the local DB), apply the patch to the clone, then swap. Doable but expensive and complex for a legacy app.
6. **Why NOT rolling?** Single instance — nothing to "roll" in batches.
7. **Why NOT canary?** Single instance — no small subset possible.

**Answer:** **In-place deployment** during the maintenance window.

---

### PBQ 5 — "The Healthcare Records System"

**Scenario:**
A hospital's electronic health records (EHR) system must be updated. Requirements:
- The system is **HIPAA-regulated** and must be on **FedRAMP-aligned infrastructure**.
- Downtime is acceptable for a **scheduled 4-hour maintenance window** announced in advance.
- The system is **monolithic**, with a tightly coupled database.
- A separate "staging" environment exists but is much smaller than production (can't handle real production load).

**Your task:** Choose the strategy and justify.

**Step-by-step solution:**

1. **Monolithic app + tightly-coupled DB** → blue-green has DB-sync challenges.
2. **Staging is smaller than production** → can't fully production-test in staging.
3. **4-hour maintenance window** → in-place is acceptable per the requirement.
4. **Pre-prod testing on smaller staging + brief in-place maintenance on prod** is the realistic answer.
5. **Procedure:**
   - Full regression testing in staging.
   - Take a database backup / snapshot of production.
   - Announce the maintenance window.
   - Stop the EHR service (in-place).
   - Apply the new code / schema.
   - Restart, smoke-test.
   - If broken, restore from backup (in-place rollback).
6. **Why NOT blue-green?** A full parallel production environment with synced DB is too expensive and complex for a regulated monolithic app. The DB-sync problem alone is a deal-breaker.
7. **Why NOT canary or rolling?** Both require multiple instances of the new code running simultaneously with the old — incompatible with a single monolithic app + tightly-coupled DB.

**Answer:** **In-place deployment** with a scheduled maintenance window.

---

### PBQ 6 — "The Mixed Portfolio"

**Scenario:**
A SaaS company has multiple components. Match each to the right strategy:

| Component | Description |
|---|---|
| **C1** | Stateless web frontend (50 pods in K8s), 20 deploys/day. |
| **C2** | Critical payment API, downtime not tolerated, infrequent deploys, big budget. |
| **C3** | ML inference model, high traffic, must detect accuracy regressions. |
| **C4** | Legacy single-server admin tool, 5 users, weekly deploys, brief downtime OK. |

**Your task:** Match each.

**Answers:**

| Component | Strategy | Why |
|---|---|---|
| **C1** | **Rolling** (K8s default) | Frequent, stateless, cost-sensitive. |
| **C2** | **Blue-Green** | Critical, infrequent, big budget, zero downtime required. |
| **C3** | **Canary** | High traffic + need to detect accuracy regressions on real users. |
| **C4** | **In-Place** | Legacy single host, small, downtime OK. |

---

## SECTION 7 — MEMORY AIDS

### 7.1 — The 4 Restaurants Mnemonic

Reuse the restaurant analogy:

| Strategy | Restaurant version |
|---|---|
| **Blue-Green** | New restaurant next door. Switch everyone at once. |
| **Canary** | Try the new dish on 5% of customers first. If good, expand. |
| **Rolling** | Update one table at a time. |
| **In-Place** | Shut down, replace menu, reopen. |

**Memory hook:** "**B**lue-**G**reen is **B**ig-**G**radual" — wait, no, it's NOT gradual. Better hook:
"**C**anary is **C**autious, **R**olling is **R**obotic, **B**lue-**G**reen is **B**old, **I**n-**P**lace is **I**nstant."

Or, more useful: **"S-P-R-B-C"** ordered by *risk tolerance*:
- **S**implest = **In-Place**
- **P**runy cost = **In-Place** (cheap)
- **R**obotic, batched = **Rolling**
- **B**old, parallel = **Blue-Green**
- **C**autious, gradual = **Canary**

No wait — sorted by *complexity from simplest to most elaborate*:
**In-Place → Rolling → Blue-Green → Canary**

That ordering is your fastest mental model. In-Place is the simplest; Canary is the most sophisticated.

---

### 7.2 — Cost / Complexity Ladder

| Level | Strategy | Cost | Complexity |
|---|---|---|---|
| 1 (bottom) | **In-Place** | 💲 | 🟢 Low |
| 2 | **Rolling** | 💲 | 🟡 Low-Mid |
| 3 | **Blue-Green** | 💲💲 | 🟠 Mid-High |
| 4 (top) | **Canary** | 💲 | 🔴 High (observability required) |

**Memory hook:** *Climbing the ladder* — In-Place is the ground floor, Canary is the penthouse.

---

### 7.3 — Risk Tolerance Decoder

If the scenario says the company has…
- **"High risk tolerance, cost-sensitive, frequent deploys"** → **Rolling**
- **"Zero risk tolerance, big budget, infrequent deploys"** → **Blue-Green**
- **"Mid risk, frequent deploys, real-user testing"** → **Canary**
- **"Low stakes, brief downtime OK"** → **In-Place**

---

### 7.4 — "The 100% Test"

A common exam question: "What percentage of users see the new version during the cutover?"

| Strategy | % at cutover |
|---|---|
| **Blue-Green** | **0% → 100%** (atomic switch) |
| **Canary** | **1–5% → 100%** (gradual) |
| **Rolling** | **10–20% per batch → 100%** |
| **In-Place** | **100%** (everyone affected, possibly with downtime) |

**Memory hook:** The "100% test" instantly decodes most scenarios.

---

### 7.5 — "The Rollback Speed Test"

| Strategy | Rollback speed |
|---|---|
| **Blue-Green** | ⚡ Instant (flip the LB) |
| **Canary** | ⚡ Fast (shift 0% to canary) |
| **Rolling** | 🐢 Slow (roll back through batches) |
| **In-Place** | 🐌 Slowest (redeploy old version) |

**Memory hook:** "Blue-Green and Canary are *runners*; Rolling and In-Place are *crawlers* for rollback."

---

### 7.6 — The Big Story

> *"A startup launches a web app. They start with **in-place** because they're small and the app is simple. As they grow, they move to a multi-instance setup and use **rolling** deploys (Kubernetes default). When they hit enterprise customers who demand zero downtime, they add **blue-green** for major releases. As their traffic explodes and they deploy 50×/day, they layer in **canary** with metrics-driven promotion. They use all four."*

That story is the path most companies follow. It also tells you the natural progression of strategies.

---

### 7.7 — Quick Phrase Decoder

| If the scenario says… | The strategy is… |
|---|---|
| "zero downtime," "atomic cutover," "swap environments" | **Blue-Green** |
| "1% of users," "small subset," "real production testing," "gradual promotion" | **Canary** |
| "incremental," "batches," "one at a time," "no extra infra," "fleet" | **Rolling** |
| "simplest," "single server," "maintenance window," "downtime acceptable" | **In-Place** |
| "parallel environment," "two production" | **Blue-Green** |
| "metrics-driven," "SLO-based promotion," "Prometheus" | **Canary** |
| "Kubernetes default," "Deployment strategy" | **Rolling** |
| "SSM Run Command," "patching," "single host" | **In-Place** |

---

## SECTION 8 — CLOUD PROVIDER MAPPING

### 8.1 — AWS Services for Each Strategy

| Strategy | AWS Service(s) |
|---|---|
| **Blue-Green** | • **AWS CodeDeploy** with "blue/green" deployment config (EC2, ECS, Lambda)<br>• **AWS Elastic Beanstalk** "swap environment URLs"<br>• **Amazon ECS** with two target groups + CodeDeploy<br>• **AWS Lambda** aliases with weighted routing (0%↔100%)<br>• **Amazon Route 53** weighted DNS records<br>• **AWS Global Accelerator** for global blue-green |
| **Canary** | • **AWS CodeDeploy** with "canary" / "linear" config (Lambda: 10% / 10 min → 100%)<br>• **AWS Lambda** alias weighted routing (5% → 25% → 100%)<br>• **Amazon ECS** with **AWS App Mesh** weighted target groups<br>• **Amazon EKS** + **Istio** or **Argo Rollouts**<br>• **AWS CloudWatch Evidently** — feature flag + canary + statistical analysis<br>• **AWS AppConfig** for feature flags<br>• **Amazon SageMaker** — model canary (shadow / A/B / canary) |
| **Rolling** | • **Amazon ECS** default rolling update<br>• **Amazon EKS** `Deployment` strategy<br>• **AWS CodeDeploy** "rolling" config (e.g., 50% at a time)<br>• **AWS Auto Scaling Instance Refresh** — rolling ASG instance replacement |
| **In-Place** | • **AWS Systems Manager (SSM) Run Command / State Manager** — patches in place<br>• **AWS CodeDeploy** "in-place" deployment config (the third option)<br>• **AWS OpsWorks** — Chef/Puppet in-place config<br>• **AWS Systems Manager Patch Manager** — OS patching in place<br>• **EC2 Image Builder** — builds new AMIs (in-place update of running instances) |

### 8.2 — Azure Services for Each Strategy

| Strategy | Azure Service(s) |
|---|---|
| **Blue-Green** | • **Azure App Service Deployment Slots** — the canonical Azure blue-green (slot swap)<br>• **Azure DevOps Pipelines** — deployment jobs with traffic shifting<br>• **AKS** with **Azure Traffic Manager** (priority routing for blue-green)<br>• **Azure Front Door** — global blue-green routing |
| **Canary** | • **Azure DevOps Pipelines** — "canary" deployment strategy<br>• **AKS** with **Azure App Gateway** + traffic splitting<br>• **Azure App Configuration** + **Feature Manager** — feature flag canary<br>• **Azure Container Apps** — revisions with traffic split (canary-style) |
| **Rolling** | • **AKS** — default `Deployment` strategy (rolling update)<br>• **Azure Virtual Machine Scale Sets (VMSS)** — automatic rolling upgrade<br>• **Azure Service Fabric** — rolling upgrades for microservices<br>• **Azure DevOps Pipelines** "rolling" strategy |
| **In-Place** | • **Azure VMSS** with "Manual" upgrade mode<br>• **Azure Automation Update Management** — scheduled in-place patches<br>• **Azure Arc** — in-place management of on-prem / multi-cloud VMs<br>• **Azure App Service "Local Git" deployment** — basic in-place deploy<br>• **Azure VMs** with **Custom Script Extension** — in-place script execution |

### 8.3 — Google Cloud Services for Each Strategy

| Strategy | Google Cloud Service(s) |
|---|---|
| **Blue-Green** | • **Google App Engine Versions** — traffic split 0%/100%<br>• **Google Cloud Run** — revisions with traffic split (instant cutover)<br>• **Google Kubernetes Engine (GKE)** + Spinnaker/Cloud Deploy<br>• **Google Cloud Load Balancing** with backend weights<br>• **Cloud DNS** with weighted records |
| **Canary** | • **Google Cloud Run** — traffic split 1%→100% between revisions<br>• **GKE** + **Istio** (or **Argo Rollouts** for K8s-native canary)<br>• **Google Cloud Deploy** — canary pipelines<br>• **Firebase Remote Config** — canary at the mobile-client level<br>• **Google Cloud Build** + **Binary Authorization** — canary with policy gates<br>• **Vertex AI Model Deployment** — model canary |
| **Rolling** | • **GKE** — default `Deployment` rolling update<br>• **Google Compute Engine Managed Instance Group (MIG)** — rolling instance refresh<br>• **Cloud Run** — gradual rollout (between canary and rolling)<br>• **Google Kubernetes Engine + Argo Rollouts** — advanced rolling with metrics |
| **In-Place** | • **Google Compute Engine OS Config** + **VM Manager** — patch in place<br>• **GKE `Recreate` strategy** — kill all pods, restart with new image (≈in-place at pod level)<br>• **Cloud Run "Direct VPC" updates** — code-only updates (in-place at the code layer)<br>• **Google Cloud VM Manager** — patch management for VMs |

### 8.4 — Open-Source / Vendor-Neutral Tools

| Strategy | Open-Source Tools |
|---|---|
| **Blue-Green** | Spinnaker pipelines, Octopus Deploy, Argo CD with two clusters |
| **Canary** | **Argo Rollouts** (Kubernetes-native), **Flagger** (Istio/App Mesh), **Spinnaker**, **LaunchDarkly** (feature flags) |
| **Rolling** | Kubernetes Deployments (built-in), Helm rollout, Ansible rolling strategy |
| **In-Place** | Ansible, Chef, Puppet, SaltStack, plain SSH + scripts |

### 8.5 — Service Quick-Pick Cheat Sheet

| Need | AWS | Azure | GCP | K8s |
|---|---|---|---|---|
| Blue-Green (full env) | CodeDeploy blue/green, Lambda alias swap, Beanstalk URL swap | App Service Deployment Slots | App Engine versions, Cloud Run revisions | Argo Rollouts, manual two-cluster |
| Canary (subset) | Lambda weighted, CodeDeploy canary, CloudWatch Evidently, SageMaker | DevOps Pipelines canary, AKS + App Gateway, App Config | Cloud Run traffic split, GKE + Istio/Argo, Vertex AI | Argo Rollouts, Flagger, Istio |
| Rolling (batches) | ECS default, EKS Deployment default, ASG instance refresh | AKS default, VMSS auto-upgrade | GKE default, MIG instance refresh | Deployment RollingUpdate |
| In-Place (single host) | SSM Run Command, CodeDeploy in-place, Patch Manager | Automation Update Management, Custom Script Extension, Arc | OS Config, VM Manager, GKE Recreate | StatefulSet manual update, Ansible |

---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 9.1 — Junior Cloud Engineer Questions

**Q1: What's the difference between blue-green, canary, rolling, and in-place deployments?**
**Ideal answer:**
> Blue-green uses two parallel environments — traffic switches all at once from old (Blue) to new (Green), with instant rollback by flipping back. Canary rolls out to a small percentage of users first, then gradually expands, with metrics-driven promotion or rollback. Rolling updates the fleet in batches — old instances are removed and new ones are added gradually, reusing the existing infrastructure. In-place updates the same host in place — simplest and cheapest, but typically involves a maintenance window with downtime.

**Q2: Why would you choose blue-green over canary?**
**Ideal answer:**
> Blue-green is the right choice when you need zero downtime, instant rollback, can afford 2× infrastructure cost, and want a clean atomic cutover. It's also better when you have low traffic (canary needs enough volume for statistical significance) or when you need a full production-sized environment for pre-cutover testing. Canary is the right choice when you want to detect subtle bugs in real production traffic with the lowest possible blast radius.

**Q3: What is "canary deployment" named after?**
**Ideal answer:**
> Coal miners used to bring canaries into mines. The canary is more sensitive to toxic gases than humans — if the canary died, the miners knew to evacuate. In deployment, the "canary" is a small percentage of users exposed to the new version first; if they encounter problems (errors, regressions), the rollout is halted before exposing everyone.

**Q4: Your team deploys 30 times a day. Which strategy do you recommend?**
**Ideal answer:**
> **Canary** (with strong observability) for most releases, and **rolling** for low-risk hotfixes. Canary's metrics-driven promotion fits a high-frequency deploy cadence. You might also use **blue-green** for the most critical release of the day if a canary run can't catch the bug. Avoid in-place (downtime accumulates) and full blue-green (cost accumulates at 30×/day).

---

### 9.2 — Cloud Administrator Questions

**Q5: A scenario says: "We have a single Windows Server 2016 VM running our legacy CRM. We patch it monthly during a Sunday maintenance window. Brief downtime is acceptable. The local SQL Server Express database holds all our data." What deployment strategy is this?**
**Ideal answer:**
> **In-place deployment**. The scenario explicitly says single VM, local state, maintenance window, and acceptable downtime. Blue-green would require cloning the VM including its local DB — complex and not justified. Rolling requires multiple instances. Canary requires a subset of users, not applicable for a single-tenant legacy app. In-place is the textbook fit.

**Q6: How do you implement blue-green in Kubernetes?**
**Ideal answer:**
> Two main approaches:
> 1. **Two Deployments** with the same labels, fronted by a Service. Use a tool like Argo Rollouts or Spinnaker to manage the switch.
> 2. **Argo Rollouts** with a BlueGreen strategy:
>    ```yaml
>    strategy:
>      blueGreen:
>        activeService: my-app-active
>        previewService: my-app-preview
>        autoPromotionEnabled: false  # wait for manual approval
>    ```
>    When the new ReplicaSet is ready, the controller switches the active Service selector to the new RS. Rollback = switch back.

**Q7: How do you implement canary in AWS Lambda?**
**Ideal answer:**
> Use a Lambda **alias** with **weighted routing**. For example:
> - Alias `prod` points to two versions: v1 (current) and v2 (new).
> - Initial weights: 95% v1, 5% v2 (canary).
> - Use **CodeDeploy** with a "canary" deployment config (e.g., `Canary10Percent30Minutes`) to shift weights automatically.
> - Monitor CloudWatch metrics (errors, throttles, duration).
> - If healthy, CodeDeploy shifts to 100% v2.
> - If unhealthy, CodeDeploy shifts back to 0% v2 (rollback).

**Q8: A scenario mentions "the team must update a fleet of 100 EC2 instances two at a time with no extra capacity and no downtime." What strategy?**
**Ideal answer:**
> **Rolling deployment**. Updating "two at a time" is a rolling update with batch size 2. No extra capacity means we're not running a parallel environment (rules out blue-green). "No downtime" requires proper drain + health check (a *correctly-implemented* rolling update). Implementation: AWS Auto Scaling Group with rolling update policy, or ECS service update with `deploymentConfiguration` (e.g., minimumHealthyPercent: 100, maximumPercent: 110).

---

### 9.3 — Cloud Support / Architecture Questions

**Q9: When is in-place deployment the right choice, even with a high-traffic production system?**
**Ideal answer:**
> In-place is the right choice when:
> 1. **The app is stateful and tied to the host** (e.g., a local database, in-memory state that can't be replicated).
> 2. **The deployment is a config-only update** (e.g., nginx reload, log-rotation config change) that doesn't require a restart.
> 3. **The team can't easily run a parallel environment** (single VM legacy app, tight budget, no cloud-orchestration platform).
> 4. **A scheduled maintenance window is acceptable** (e.g., an internal admin tool used Mon–Fri, 9–5, with weekend maintenance).
>
> For *customer-facing, 24/7 high-traffic* systems, in-place is rarely the right choice.

**Q10: How does canary differ from A/B testing?**
**Ideal answer:**
> **Canary** is a *deployment* technique focused on **risk reduction** — release a new version to a small percentage of users to detect errors before full rollout. The decision is binary: promote or rollback.
>
> **A/B testing** is a *product-experimentation* technique focused on **measuring user response** to two variants (often the same version with different features, or different UI/UX) to optimize a business KPI. The decision is statistical: which variant wins on the metric of interest.
>
> Canary and A/B *can* use the same traffic-splitting infrastructure (Istio, App Gateway, Lambda alias), but the *purpose* differs: canary = safety, A/B = learning.

**Q11: How do you handle a database schema change during a canary or rolling deployment?**
**Ideal answer:**
> **Expand-contract migration**:
> 1. **Expand**: Add the new schema elements (new column, new table) in a backward-compatible way. Deploy code that writes both old and new. Old code ignores the new elements.
> 2. **Migrate**: Backfill data into the new schema.
> 3. **Contract**: Once all code is on the new version, remove the old schema elements.
>
> The key principle: **both old and new code must work with the schema during the rollout window**. If the new version requires a new column, the old version must tolerate the new column being there. If the new version removes a column, the old version must tolerate its absence.
>
> If the schema change is fundamentally incompatible (e.g., renaming a primary key column), canary and rolling are unsafe — use blue-green with a shared DB and dual-write, or in-place with a maintenance window.

**Q12: How does Kubernetes' Recreate deployment strategy differ from RollingUpdate?**
**Ideal answer:**
> - **RollingUpdate** (default): gradually replaces pods in batches, maintaining availability throughout. Both old and new pods run simultaneously.
> - **Recreate**: terminates ALL existing pods first, then creates new ones with the new version. Causes downtime, but ensures no two versions run simultaneously (useful for incompatible changes).
>
> In CompTIA terms, **Recreate ≈ in-place** at the pod level (single batch, downtime window). **RollingUpdate = rolling**.

---

### 9.4 — Rapid-Fire Q&A (Drill)

| Question | One-line answer |
|---|---|
| Two parallel environments, atomic cutover? | **Blue-Green** |
| 1% of users, gradual promotion, metrics? | **Canary** |
| Fleet updated 10% at a time? | **Rolling** |
| Single VM patched during a maintenance window? | **In-Place** |
| Kubernetes default? | **Rolling** |
| App Service Deployment Slots? | **Blue-Green** |
| Cloud Run traffic split 1% → 100%? | **Canary** |
| AWS SSM Run Command patching? | **In-Place** |
| Lowest blast radius? | **Canary** |
| Instant rollback? | **Blue-Green** |
| Cheapest? | **In-Place** |
| 2× infrastructure cost? | **Blue-Green** |
| Detect subtle bugs in production? | **Canary** |
| Schema-breaking DB migration? | **In-Place** (or Blue-Green with shared DB) |
| Monolithic stateful legacy app? | **In-Place** |
| High-traffic consumer app, 50 deploys/day? | **Canary** or **Rolling** |
| Critical payment API, infrequent deploys, big budget? | **Blue-Green** |

---

## SECTION 10 — FLASHCARDS

> Use these for spaced-repetition review.

**F1.**
Q: What are the four deployment strategies in CV0-004 objective 2.2?
A: Blue-green, Canary, Rolling, In-place.

**F2.**
Q: Define "blue-green deployment."
A: Two identical production environments side-by-side. Deploy the new version to Green, test it, then switch 100% of traffic from Blue to Green. Rollback = switch back to Blue.

**F3.**
Q: Define "canary deployment."
A: Roll out the new version to a small subset of users (1–10%) first, monitor metrics, then gradually increase the percentage. If unhealthy, the canary is rolled back without affecting the rest of the user base.

**F4.**
Q: Define "rolling deployment."
A: Update the application's instances in batches (e.g., 20% at a time) until all instances are on the new version. Old and new versions run simultaneously during the rollout.

**F5.**
Q: Define "in-place deployment."
A: Update the application on the existing infrastructure — same host, same IP, no parallel environment. Typically involves a maintenance window with brief downtime.

**F6.**
Q: Which strategy has the highest infrastructure cost during a release?
A: **Blue-Green** (2× infrastructure during the cutover).

**F7.**
Q: Which strategy has the lowest blast radius if the new version is buggy?
A: **Canary** (only the small canary percentage is affected).

**F8.**
Q: Which strategy is the Kubernetes default?
A: **Rolling** (Deployment with `strategy: RollingUpdate`).

**F9.**
Q: Which strategy is best for a legacy single-VM app with a maintenance window?
A: **In-Place**.

**F10.**
Q: Which strategy requires two full production environments?
A: **Blue-Green**.

**F11.**
Q: What is the name of the AWS deployment service that supports all four strategies (in-place, blue-green, canary, rolling)?
A: **AWS CodeDeploy** (with the appropriate deployment configuration).

**F12.**
Q: What is the Azure feature for blue-green deployments on App Service?
A: **Deployment Slots** (swap staging ↔ production).

**F13.**
Q: What is the Google Cloud feature for canary on Cloud Run?
A: **Traffic splitting between revisions** (e.g., 1% new, 99% old).

**F14.**
Q: What AWS service is used for in-place patching of EC2 instances?
A: **AWS Systems Manager (SSM) Run Command** or **Patch Manager**.

**F15.**
Q: What is the difference between canary and A/B testing?
A: Canary is a *deployment* technique for risk reduction. A/B testing is an *experimentation* technique for measuring user response to two variants. Same infra, different purpose.

**F16.**
Q: Can you do canary or rolling with a schema-breaking DB migration?
A: No (in general). Both strategies require both old and new code to work with the same schema. Use in-place with a maintenance window, or blue-green with dual-write.

**F17.**
Q: What is the "expand-contract" pattern?
A: A database migration pattern that allows canary/rolling: (1) expand the schema with new elements in a backward-compatible way, (2) migrate data, (3) contract by removing old elements only after all code is migrated.

**F18.**
Q: What does Kubernetes' Recreate strategy approximate in CompTIA terms?
A: **In-Place** (at the pod level — kills all pods, then creates new ones, with downtime).

**F19.**
Q: Which strategy has the slowest rollback?
A: **In-Place** (you must redeploy the old version, no parallel environment to fall back to).

**F20.**
Q: Which strategy has the fastest rollback?
A: **Blue-Green** (flip the load balancer back to Blue — instant).

**F21.**
Q: Name three real-world companies known for canary deployments.
A: Netflix, Google, Amazon, Facebook/Meta, Microsoft. (All run canaries at massive scale.)

**F22.**
Q: How do you implement canary with AWS Lambda aliases?
A: Create an alias with weighted routing: 5% to the new version, 95% to the old. Use CodeDeploy to gradually shift weights. Monitor CloudWatch metrics. Rollback = shift weights to 0% on the new version.

**F23.**
Q: What is the simplest deployment strategy?
A: **In-Place** (just update the existing host).

**F24.**
Q: Which strategy uses the existing fleet without launching new instances?
A: **Rolling** (replaces instances in batches, reusing the same fleet capacity) and **In-Place** (updates the same host).

**F25.**
Q: Which strategy would you recommend for a critical payment API with infrequent deploys and a big budget?
A: **Blue-Green** (zero downtime, instant rollback, 2× cost is acceptable).

**F26.**
Q: Which strategy would you recommend for a high-traffic consumer app with 50 deploys per day and strong observability?
A: **Canary** (gradual promotion with metrics-driven rollback).

**F27.**
Q: Which strategy would you recommend for a stateless API on Kubernetes with 50 pods and 5 deploys per day?
A: **Rolling** (Kubernetes default, cost-effective, gradual).

**F28.**
Q: Which strategy would you recommend for a single-VM legacy CRM with a Sunday maintenance window?
A: **In-Place**.

**F29.**
Q: What is the "100% test" decoder?
A: If the scenario says 0% → 100% atomic switch → Blue-Green. 1–10% → 100% gradual → Canary. 10–20% batches → 100% → Rolling. 100% with downtime → In-Place.

**F30.**
Q: What is "GKE Recreate" in CompTIA terms?
A: In-Place (at the pod level).

**F31.**
Q: What is "App Service Deployment Slots" in CompTIA terms?
A: Blue-Green.

**F32.**
Q: What is "Cloud Run traffic split 1% → 100%" in CompTIA terms?
A: Canary.

**F33.**
Q: What is "SSM Run Command patching" in CompTIA terms?
A: In-Place.

**F34.**
Q: What is "AWS CodeDeploy blue/green config" in CompTIA terms?
A: Blue-Green.

**F35.**
Q: What is "Kubernetes RollingUpdate" in CompTIA terms?
A: Rolling.

**F36.**
Q: Which deployment strategy is the most expensive during the cutover?
A: Blue-Green (2× infrastructure during the cutover window).

**F37.**
Q: Which deployment strategy is the cheapest?
A: In-Place (no extra infrastructure at all).

**F38.**
Q: Which strategy requires the most sophisticated observability?
A: Canary (you need metrics to make promotion/rollback decisions).

**F39.**
Q: What is a "dark canary"?
A: Pushing the new code to production servers but routing 0% of user traffic to it. Run synthetic tests on production hardware. Once greenlit, route a small percentage of real traffic.

**F40.**
Q: Why is blue-green good for regulated environments?
A: The new version can be fully tested in production-like infrastructure (Green) before being exposed to users. The Blue environment provides instant rollback if the regulator-required controls fail.

---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (Q1–Q6)

---

**Q1.** *(Easy)*
A company maintains two identical production environments. When a new version is ready, the load balancer is switched to point all traffic to the new environment. If the new version is broken, traffic is switched back. Which deployment strategy is this?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** Two parallel environments, atomic cutover, instant rollback = blue-green.

---

**Q2.** *(Easy)*
A team deploys a new version to 1% of users first, monitors error rates and latency, and gradually increases the percentage to 5%, 25%, 50%, and 100%. If error rates spike at any point, the rollout is rolled back. Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** B
**Why:** Small subset first, gradual promotion, metrics-driven = canary.

---

**Q3.** *(Easy)*
A team runs 50 stateless API pods in Kubernetes. The default `Deployment` strategy updates pods in batches of 25% at a time, gradually replacing the old version with the new. Which strategy is this?
A. Canary
B. Blue-Green
C. Rolling
D. In-Place

**Correct:** C
**Why:** Batch updates, no parallel environment, default in Kubernetes = rolling.

---

**Q4.** *(Easy)*
A company has a single legacy Windows Server VM running its CRM. The team schedules a Sunday 2 AM maintenance window to apply patches. Brief downtime is acceptable. Which strategy?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** D
**Why:** Single VM, local state, maintenance window = in-place.

---

**Q5.** *(Easy)*
Which deployment strategy REQUIRES the most infrastructure capacity during the cutover (typically 2× the production capacity)?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** Blue-green runs two full production environments simultaneously.

---

**Q6.** *(Easy)*
Which deployment strategy is BEST for detecting subtle, production-only bugs (e.g., a 0.5% conversion drop or a p99 latency regression) in the new version?
A. In-Place
B. Blue-Green
C. Canary
D. In-Place with a maintenance window

**Correct:** C
**Why:** Canary exposes the new version to real users in small percentages; metrics reveal subtle regressions that pre-prod tests miss.

---

### MEDIUM (Q7–Q14)

---

**Q7.** *(Medium)*
A bank processes credit-card transactions and requires zero downtime during releases. The team has a generous infrastructure budget and wants a clean atomic cutover with the ability to roll back within 30 seconds. Which strategy is the BEST fit?
A. Rolling
B. Canary
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** Zero downtime, atomic cutover, instant rollback, big budget = blue-green.

---

**Q8.** *(Medium)*
A SaaS company has 5 million daily active users and deploys 30 times per day. The team uses Prometheus + Datadog + Sentry for monitoring. They want to detect subtle business-metric regressions (e.g., conversion drops) on a small subset of users before full rollout. Which strategy?
A. Rolling
B. Canary
C. Blue-Green
D. In-Place

**Correct:** B
**Why:** High traffic + frequent deploys + observability + need to detect subtle regressions = canary.

---

**Q9.** *(Medium)*
A scenario says: "We deploy 20% of our fleet at a time to the new version. Old instances are terminated; new ones are launched. There is no parallel environment, and we cannot afford 2× infrastructure." Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** C
**Why:** Batches, no parallel env, fleet-based = rolling.

---

**Q10.** *(Medium)*
A team uses **Azure App Service Deployment Slots**. They deploy the new version to a "staging" slot, test it, then "swap" staging with production. Which strategy?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** Deployment slots = Azure's native blue-green (slot swap = atomic cutover).

---

**Q11.** *(Medium)*
A team uses **Cloud Run revisions** with a traffic split of 1% to the new revision and 99% to the old revision. After 1 hour of monitoring, they shift to 5%, then 25%, then 100%. Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** B
**Why:** Gradual traffic split, real-user testing, promotion = canary.

---

**Q12.** *(Medium)*
A scenario says: "We use **AWS Systems Manager Run Command** to push configuration updates to our existing EC2 instances. The instances are not terminated or replaced; the update runs in place." Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** D
**Why:** SSM Run Command updates the same host in place = in-place.

---

**Q13.** *(Medium)*
Which deployment strategy provides the FASTEST rollback if the new version is broken?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** Blue-green flips the load balancer back to Blue — instant (seconds). Canary is also fast (shift 0% to canary), but blue-green is typically considered the fastest "atomic" rollback.

**Note on nuance:** Canary rollback (shift canary weight to 0%) is also near-instant. The exam will usually mark blue-green as the fastest because the cutover is fully reversible with a single LB flip.

---

**Q14.** *(Medium)*
A team needs to deploy a new version but the database migration is fundamentally incompatible (the new version requires a new column that the old version cannot tolerate). Which deployment strategy is the SAFEST in this scenario?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place with a maintenance window

**Correct:** D
**Why:** An incompatible schema means old and new code cannot coexist — rules out canary and rolling. Blue-green with a shared DB also has issues (the DB must be migrated first, breaking the old code). In-place with a maintenance window is the safest: stop the service, migrate the DB, deploy the new code, restart.

---

### HARD (Q15–Q20)

---

**Q15.** *(Hard)*
A company has:
- 10 stateless API pods behind a load balancer.
- A versioned database schema that is fully backward-compatible (old code ignores new columns, new code handles old columns).
- A desire to deploy 5 times per day with minimal cost.
- No parallel environment budget.

Which strategy is the BEST fit?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** C
**Why:** Stateless, schema-compatible, frequent deploys, no parallel-env budget = rolling. Canary would also work, but rolling is cheaper and simpler when the schema is compatible. If they wanted to detect subtle bugs, canary would be a strong second choice.

---

**Q16.** *(Hard)*
A team is using **AWS Lambda** aliases with weighted routing. They start at 5% on the new version and 95% on the old. CodeDeploy with a "canary10Percent30Minutes" config automatically shifts the weights. Which deployment strategy is this?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** B
**Why:** Weighted routing, gradual shift, CodeDeploy canary config = canary.

---

**Q17.** *(Hard)*
A company has 100,000 daily active users, deploys 50 times per day, and requires that no more than 1% of users are affected by a bad release. The team has Datadog, Sentry, and Prometheus. Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** B
**Why:** "No more than 1% affected by a bad release" = small canary percentage. Strong observability supports metrics-driven promotion. High traffic supports statistical significance even at 1%.

---

**Q18.** *(Hard)*
A company wants to deploy a new version to a Kubernetes cluster with zero downtime, no extra infrastructure cost, and the ability to roll back the entire fleet within 5 minutes. The application is stateless. Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** C
**Why:** Zero downtime + no extra cost = not blue-green (cost) and not in-place (downtime). 5-minute rollback for the whole fleet is fine for rolling (`kubectl rollout undo`). Canary would also work but adds complexity not needed here.

---

**Q19.** *(Hard)*
A company has a critical billing API. Requirements:
- Zero downtime.
- Atomic cutover (no gradual rollout).
- Instant rollback (≤ 30 seconds).
- Budget for 2× infrastructure.
- Compliance: the new version must be tested in production-like infrastructure before exposure.

Which strategy?
A. Canary
B. Rolling
C. Blue-Green
D. In-Place

**Correct:** C
**Why:** All four requirements point to blue-green. Canary is gradual (excluded). Rolling is gradual (excluded). In-place has downtime (excluded). Blue-green is the only fit.

---

**Q20.** *(Hard)*
A team deploys 30 times a day using Kubernetes. The new versions are 100% backward-compatible. The team needs the SIMPLEST strategy that still avoids downtime and limits the blast radius. Which strategy?
A. Blue-Green
B. Canary
C. Rolling
D. In-Place

**Correct:** C
**Why:** Simplest + no downtime + blast-radius-limited + schema-compatible + frequent = rolling. Canary is more sophisticated but not the "simplest." Rolling is the natural Kubernetes default for this use case.

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Define blue-green, canary, rolling, in-place | **CRITICAL** | Tested in nearly every 2.2 question. |
| Match strategy to a scenario (the entire PBQ purpose) | **CRITICAL** | The exam's core task. |
| Distinguish rolling from in-place (the subtle trap) | **CRITICAL** | Top trap. |
| Distinguish blue-green from canary (parallel env vs. percentage) | **CRITICAL** | Top trap. |
| "Atomic cutover" / "100% switch" → blue-green | **CRITICAL** | Decoder. |
| "1% of users" / "gradual promotion" → canary | **CRITICAL** | Decoder. |
| "Batches" / "one at a time" → rolling | **CRITICAL** | Decoder. |
| "Single VM" / "maintenance window" → in-place | **CRITICAL** | Decoder. |
| Service mappings: CodeDeploy, App Service Slots, Cloud Run, SSM, K8s | **HIGH** | Service-mapping questions. |
| Database schema migration compatibility with canary/rolling | **HIGH** | Common scenario constraint. |
| Rollback speed of each strategy | **HIGH** | Often-tested trade-off. |
| Cost comparison (in-place < rolling < canary ≤ blue-green) | **HIGH** | Often-tested trade-off. |
| Canary vs A/B testing distinction | **MEDIUM** | Niche but tested. |
| Expand-contract migration pattern | **MEDIUM** | Niche. |
| Kubernetes `Recreate` ≈ in-place | **MEDIUM** | K8s specifics. |
| Blue-green with shared DB and dual-write | **MEDIUM** | Niche. |
| Specific tools (Argo Rollouts, Flagger, Spinnaker) | **LOW** | Background knowledge. |
| Dark canary, equal canary, canary dimensions | **LOW** | Niche. |

**Top 5 things to drill until automatic:**
1. The four strategies and their textbook definitions.
2. The "100% test" — 0%→100% atomic = blue-green; 1%→100% gradual = canary; 10–20% batches = rolling; 100% with downtime = in-place.
3. Rolling ≠ in-place. Rolling = fleet batches; in-place = single host.
4. Schema-breaking migration → in-place (or blue-green with shared DB).
5. The "rollback speed test" — blue-green and canary are fast; rolling and in-place are slow.

---

## SECTION 13 — OBJECTIVE SUMMARY — 1-PAGE CRAM SHEET

> Pin this. Read it the morning of the exam.

### The Four Deployment Strategies (CV0-004 2.2)

| Strategy | One-liner | Tenancy / infra | Rollback | Cost | Best for |
|---|---|---|---|---|---|
| **Blue-Green** | Two parallel envs, atomic cutover | 2× during cutover | ⚡ Instant | 💲💲 | Critical, infrequent, big budget |
| **Canary** | Small % first, gradual promotion | 1.05–1.10× | ⚡ Fast (reroute 0%) | 💲 | High-traffic, frequent, real-user testing |
| **Rolling** | Update fleet in batches | 1× | 🐢 Slow (roll back through batches) | 💲 | Stateless, frequent, no extra capacity |
| **In-Place** | Update the same host | 1× | 🐌 Slowest (redeploy old) | 💲 | Legacy, single VM, brief downtime OK |

### The "100% Test" Decoder

| Scenario says | Strategy |
|---|---|
| "0% → 100% atomic switch," "two parallel envs," "swap" | **Blue-Green** |
| "1–10% of users first," "gradual promotion," "real-user testing" | **Canary** |
| "Batches," "20% at a time," "fleet," "one at a time" | **Rolling** |
| "Single host," "maintenance window," "downtime OK," "config reload" | **In-Place** |

### Critical Distinctions

- **Rolling ≠ In-Place.** Rolling = update *fleet* in batches. In-place = update *single host* on the same infrastructure.
- **Blue-Green ≠ Canary.** Blue-green = full parallel env, atomic cutover. Canary = small subset, gradual promotion.
- **Canary ≠ A/B testing.** Canary is a deployment technique for safety. A/B testing is an experimentation technique for product learning.
- **Schema-breaking migration → NOT canary or rolling.** Old and new must coexist during the rollout, which requires schema compat. Use in-place with maintenance window, or blue-green with shared DB and dual-write.
- **Blue-green is NOT runtime HA.** It's a deployment strategy. Runtime HA needs multi-AZ, multi-instance, etc.

### Acronyms to Know

- **CI/CD** — Continuous Integration / Continuous Delivery (or Deployment).
- **SRE** — Site Reliability Engineering.
- **SLO** — Service Level Objective.
- **K8s** — Kubernetes.
- **VMSS** — Virtual Machine Scale Sets (Azure).
- **ASG** — Auto Scaling Group (AWS).
- **MIG** — Managed Instance Group (GCP).
- **AMI** — Amazon Machine Image.
- **SSM** — AWS Systems Manager.
- **MGN** — AWS Application Migration Service.
- **CDC** — Change Data Capture.
- **B/G** — Blue/Green.

### Service Mappings (Quick Reference)

| Need | AWS | Azure | GCP | K8s |
|---|---|---|---|---|
| Blue-Green | CodeDeploy blue/green, Lambda alias swap, Beanstalk URL swap | App Service Deployment Slots | App Engine versions, Cloud Run revisions | Argo Rollouts blueGreen |
| Canary | CodeDeploy canary config, Lambda alias weighted, CloudWatch Evidently | DevOps canary, AKS + App Gateway, App Config | Cloud Run traffic split, GKE + Istio/Argo | Argo Rollouts canary, Flagger |
| Rolling | ECS default, EKS default, ASG instance refresh | AKS default, VMSS auto-upgrade | GKE default, MIG instance refresh | Deployment RollingUpdate |
| In-Place | SSM Run Command, CodeDeploy in-place, Patch Manager | Automation Update Management, Arc, Custom Script Extension | OS Config, VM Manager | StatefulSet manual, Recreate strategy |

### Exam Traps in 5 Lines

1. Rolling ≠ In-Place.
2. Blue-Green ≠ Canary.
3. Canary ≠ A/B testing.
4. Schema-breaking migration → not canary/rolling.
5. Blue-green is not runtime HA.

### Decision Shortcut

- **Critical + big budget + infrequent** → Blue-Green
- **High traffic + frequent + need subtle-bug detection** → Canary
- **Stateless + frequent + cost-sensitive** → Rolling
- **Legacy + single host + downtime OK** → In-Place

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2026)

### 14.1 — AWS

- **AWS CodeDeploy** continues to be the primary deployment service. It supports all four strategies (in-place, blue-green, canary, linear, all-at-once) for EC2, ECS, and Lambda.
- **AWS Lambda aliases with weighted routing** remain the canonical canary pattern for serverless. SnapStart (2022–2024) reduces cold-start latency — relevant to green-environment cold starts in blue-green.
- **Amazon ECS blue/green via CodeDeploy** is the standard for ECS customers. The pattern is well-documented and widely adopted.
- **Amazon EKS + Argo Rollouts** is increasingly the choice for Kubernetes-native canary and blue-green on AWS.
- **AWS CloudWatch Evidently** (GA 2023) — feature flag + canary + A/B testing with statistical analysis. Integrates with Lambda, ECS, EC2.
- **AWS AppConfig** — feature flag service that pairs with canary for safe rollouts.
- **AWS App Runner** — PaaS that handles blue-green automatically for containerized web apps and APIs.
- **SageMaker Deployment Guardrails** — ML model canary / shadow / all-at-once for ML inference endpoints.

### 14.2 — Microsoft Azure

- **Azure App Service Deployment Slots** remain the canonical blue-green pattern in Azure. New: **"Deployment Slot Swaps with Traffic Routing"** (gradual swap) for slot-style canary.
- **Azure Container Apps** — revisions with traffic split (canary-style). GA 2023, increasingly adopted.
- **AKS** — default rolling, with **Argo Rollouts** as the leading add-on for canary/blue-green.
- **Azure DevOps Pipelines** — all four strategies supported via deployment jobs (runOnce, rolling, canary).
- **Azure App Configuration + Feature Manager** — feature flag canary for .NET / Java apps.
- **Azure Front Door + AKS** — global blue-green with multi-region routing.
- **Microsoft's "Well-Architected Framework"** explicitly recommends canary for high-traffic, blue-green for critical, rolling for stateless — formalized guidance that aligns with CompTIA.

### 14.3 — Google Cloud

- **Google Cloud Run** — traffic split between revisions is the canonical canary/blue-green for serverless containers. Used heavily by Google customers.
- **GKE** — default rolling, with **Argo Rollouts** and **Flagger** for advanced strategies.
- **Google Cloud Deploy** (GA 2022–2023) — managed Spinnaker-style continuous delivery. Supports canary / blue-green pipelines.
- **Spinnaker on GKE** — open-source multi-cloud CD with first-class canary and blue-green.
- **Firebase Remote Config** — canary at the mobile client level (Firebase is GCP).
- **Vertex AI Model Deployment** — canary for ML models.
- **Google Cloud Build** + **Binary Authorization** — canary with policy gates (e.g., only promote to production if signed image passes attestation).

### 14.4 — Kubernetes & Containers

- **Argo Rollouts** has become the de facto standard for advanced deployment strategies in Kubernetes. Supports canary, blue-green, and rolling with metrics integration (Prometheus, Datadog, New Relic, Kayenta).
- **Flagger** (Weaveworks / Flux ecosystem) — progressive delivery operator that automates canary / blue-green via Istio / App Mesh / Linkerd / NGINX / Gloo.
- **Kubernetes `Deployment` strategy** remains RollingUpdate (default). `StatefulSet` uses OrderedReady by default; `DaemonSet` uses OnDelete (manual = in-place).
- **Cluster API (CAPI)** — provisioning Kubernetes clusters consistently; relevant to multi-cluster canary.
- **Gateway API** (2023–2024 GA) — the new standard for ingress, supporting traffic splitting (canary) natively.
- **Service Mesh (Istio, Linkerd, Consul)** — first-class traffic splitting for canary at the L7 layer.
- **KEDA** (Kubernetes Event-Driven Autoscaling) — can be used for canary scaling rules.
- **OpenFeature** — open-standard feature flags; pairs with canary for safe feature rollouts.

### 14.5 — AI / ML Deployment (Cloud+ Relevance)

- **ML model canary** is now a first-class pattern. Tools: **SageMaker Deployment Guardrails** (AWS), **Azure ML Online Endpoints** (traffic split), **Vertex AI Endpoints** (split traffic), **KServe** (Kubernetes-native).
- **Shadow mode / dark canary** — running the new model in parallel on real traffic, but only logging predictions (not serving them). Increasingly common for high-stakes ML.
- **A/B testing at the model level** — split traffic between two model versions, measure business KPI (click-through, conversion). Combines canary (for safety) with A/B (for learning).

### 14.6 — Serverless & Edge

- **Serverless canary** is the norm for Lambda / Azure Functions / Cloud Functions: weighted alias / traffic split between versions or revisions.
- **Edge deploys** (Cloudflare Workers, Fastly Compute@Edge, AWS Lambda@Edge) typically use a canary or rolling pattern at the edge POP.
- **Vercel, Netlify** — front-end canary / blue-green built into the platform via preview deployments and traffic shaping.
- **Cloudflare Load Balancer** — global blue-green with weighted pools.

### 14.7 — Industry Trend Relevant to 2.2

- **Progressive delivery** is the umbrella term for canary + feature flags + automated rollback. The industry is moving from "deploy big release" to "deploy small, measure, promote."
- **Argo Rollouts and Flagger** have largely replaced custom Kubernetes canary scripts.
- **Observability-first rollouts** — every modern canary/blue-green tool requires metrics integration (Prometheus, Datadog, New Relic). The strategy is only as good as the metrics.
- **GitOps** (Argo CD, Flux) drives the *trigger* of rollouts declaratively, but the *strategy* (canary, blue-green, rolling) is still a separate decision.
- **Database migration tools** (Flyway, Liquibase, Prisma Migrate, Atlas) increasingly support expand-contract patterns to enable safer canary/rolling deploys.
- **Service mesh maturity** (Istio ambient, Linkerd) is making canary traffic splitting easier to implement without sidecars.

### 14.8 — Quick "What's New" Summary

| Provider | New (2024–2026) | 2.2 Relevance |
|---|---|---|
| AWS | CodeDeploy + Lambda SnapStart, CloudWatch Evidently, App Runner blue-green, SageMaker Guardrails | All four strategies |
| Azure | Container Apps revisions + traffic split, DevOps canary strategy, App Service gradual slot swap | All four strategies |
| GCP | Cloud Run traffic split, Cloud Deploy pipelines, Vertex AI canary, GKE + Argo | All four strategies |
| Kubernetes | Argo Rollouts (de facto), Flagger, Gateway API traffic split, OpenFeature | All four strategies |
| AI | Model canary, shadow mode, A/B testing at the model level | Canary, Blue-Green |
| Edge | Vercel / Netlify / Cloudflare preview deploys | Canary, Rolling |

---

*End of Objective 2.2 — Deployment Strategies notes.*

*Next in the series: 2.3 — Summarize aspects of cloud migration.*

*Study plan reference: see `Certifications/CompTIA-Cloud+/CompTIA Cloud+ CV0-004 Study Plan.md`.*
