---
title: CompTIA Cloud+ CV0-004 — Objective 6.1
objective: "6.1 Given a scenario, troubleshoot deployment issues."
domain: "6.0 Troubleshooting"
weight: "Part of Troubleshooting domain"
tags: [Cloud+, CV0-004, Troubleshooting, Deployment, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 6.1
## Troubleshoot Deployment Issues

> **Objective statement:** *"Given a scenario, troubleshoot deployment issues."*
> **Domain:** 6.0 Troubleshooting
> **What it tests:** This is a *scenario/symptom -> cause -> fix* domain. Unlike the knowledge domains, here you're given an outage/deploy failure and must name the root cause (from the list below) and the remediation. Expect PBQ-style "what's wrong and how do you fix it" questions.

---

## SECTION 1 — Exam Objectives


- **Objective 6.1:** Given a scenario, troubleshoot deployment issues — diagnose WHY a rollout failed or a service broke, then name the root cause and the fix.
- **Domain:** 6.0 Troubleshooting — scenario/symptom → cause → fix. Expect PBQ-style "what's wrong and how do you fix it" questions.
- **The 6.1 cause list (memorize these):**
  - 6.1.1 **Incompatibility** — version/dependency/OS mismatch breaks the deploy
  - 6.1.2 **Misconfigurations** — wrong settings (the big bucket)
    - 6.1.2a Resource allocation — wrong CPU/RAM/instance assigned
    - 6.1.2b Permission issues — IAM/role/credential not allowed to deploy
    - 6.1.2c Oversubscription — host/region over-committed (no capacity left)
    - 6.1.2d Sizing issues — instance/storage too small for the workload
  - 6.1.3 **Outdated component definitions** — stale IaC/template/images referencing removed resources
  - 6.1.4 **Deprecation of functionality** — API/feature/instance-type retired by the provider
  - 6.1.5 **Outages** — service unavailable
    - 6.1.5a Full — entire service/region down
    - 6.1.5b Partial — degraded / some instances/AZs affected
  - 6.1.6 **Resource limits** — hard caps hit
    - 6.1.6a API throttling — too many calls → 429 rate-limited
    - 6.1.6b Service quotas — account quota (vCPUs, IPs) exhausted
  - 6.1.7 **Regional service availability** — the service/instance-type isn't offered in that region
- **Symptom → cause quick map:**
  - "access denied" → Permission issues (6.1.2b)
  - "no capacity / insufficient instance" → Oversubscription (6.1.2c) or quota (6.1.6b)
  - "instance type unavailable here" → Regional availability (6.1.7)
  - "API rate exceeded" → Throttling (6.1.6a)
  - "dependency X not found" → Incompatibility (6.1.1) / outdated defs (6.1.3)
  - "feature removed" → Deprecation (6.1.4)
  - "everything down" → Full outage (6.1.5a); "some down" → Partial (6.1.5b)

---

## SECTION 2 — EXAM KNOWLEDGE


### 6.1.1 — INCOMPATIBILITY
- **Definition:** A deployment failure from a version, dependency, or platform mismatch — e.g. Python 3.12 code on a 3.9 base image, or an arm64 image on x86_64 nodes. The pieces are fine alone; they just don't fit each other.
- **Why it matters:** Exam answer for "won't run on base image Y," "GLIBC not found," "architecture mismatch." Distinct from outdated defs (6.1.3) — this is a live clash at runtime. Fix: pin compatible versions, rebuild against matching base, verify architecture.
- **Analogy:** UK three-pin plug in a US socket — both good alone, but built to different standards and won't connect. Use an adapter, don't tear down the wall.
- **Example:** Container built on Python 3.12 crashes on a 3.9 cluster with `ImportError`. Fix: rebuild against the 3.9 base, pin the version, add a CI drift check.

### 6.1.2 — MISCONFIGURATIONS (the big bucket)
- **Definition:** The catch-all for failures from wrong settings — not missing capacity/permissions/features, but a value typed, chosen, or omitted incorrectly. Four flavors: resource allocation, permission issues, oversubscription, sizing.
- **Why it matters:** Umbrella cause on the exam; match the symptom to the sub-flavor (`AccessDenied` = permissions, not oversubscription; OOM = sizing, not IAM). Most common real-world cloud incident.
- **Analogy:** Wrong alarm time — clock works, power works, but 7:00 PM instead of AM misses the meeting. Four knobs you can set badly: wrong time zone (allocation), no key (permissions), overbooked venue (oversubscription), room too small (sizing).
- **Example:** Java service on `t3.micro` (1 GB RAM) gets OOM-killed. Fix: right-size to `t3.large` and set K8s requests/limits.

#### 6.1.2a — Resource allocation
- **Definition:** Wrong amount/type of compute assigned — wrong CPU/RAM, instance family, or requests/limits. Resource exists and is allowed; just mismatched to the task.
- **Why it matters:** Exam answer for "wrong CPU/RAM/instance type → crashes/throttles." Allocation = which resource; sizing = too little. Fix: right-size, set requests/limits.
- **Analogy:** Wrong-sized truck — bicycle for a sofa, or semi for a letter. Road works; wrong vehicle for the load.
- **Example:** Memory-hungry service on `t3.micro` OOM-kills. Fix: `t3.large` + correct requests/limits.

#### 6.1.2b — Permission issues
- **Definition:** Deploying identity (user/role/service account) lacks rights for the action — resource, quota, region all fine, but `AccessDenied` / `403`. Ties to 4.3 (identity & access).
- **Why it matters:** `AccessDenied`/`403` is ALWAYS permissions, never oversubscription/quota/region. Fix: attach least-privilege policy/role. #1 cause of "works on laptop, not pipeline."
- **Analogy:** Wrong key card at a secure door — server room has capacity; you just aren't allowed in. No retry helps; get the right badge.
- **Example:** CI pipeline fails `PutImage` with `AccessDenied`; laptop works. Fix: add `ecr:PutImage` + `ecs:UpdateService` to the task role.

#### 6.1.2c — Oversubscription
- **Definition:** Host/zone/region over-committed — no physical capacity left for that instance type in that AZ right now. AZ-scoped, not a limit you own.
- **Why it matters:** `InsufficientInstanceCapacity` = oversubscription; move AZ / reserve. NOT fixed by raising a quota. Bites during spikes; spread across AZs or pre-reserve.
- **Analogy:** Sold-out theater A, but B and C have seats. Walk to another theater (AZ); the chain isn't refusing you.
- **Example:** `m5.4xlarge` in `us-east-1a` fails capacity; `us-east-1b` launches. Fix: move AZ or use Auto Scaling across AZs; reserve for steady state.

#### 6.1.2d — Sizing issues
- **Definition:** Instance or storage too small for the workload — OOM, no disk, CPU-starved. Wrong magnitude, not wrong type. Distinct from oversubscription and permissions.
- **Why it matters:** Exam answer for "instance too small / OOM / disk full." Fix: larger instance/volume. IAM can't add RAM; a quota bump won't fix a 1 GB box on a 4 GB job.
- **Analogy:** 50 people in a studio apartment — real and allowed in, but physically can't hold the crowd. Not a key (perms) or account (quota) issue; need a bigger room.
- **Example:** 20 GB batch job on a 10 GB disk fails "no space left." Fix: larger volume / elastic storage, re-deploy.

### 6.1.3 — OUTDATED COMPONENT DEFINITIONS
- **Definition:** Stale IaC/template/image pointing at a resource that no longer exists — hardcoded AMI deregistered, template references a retired parameter. Template fine; pointer dead. Distinct from incompatibility and deprecation.
- **Why it matters:** "Terraform fails — AMI gone" or "ghost reference" → outdated defs. Fix: refresh AMIs/templates, re-pull modules. Parameterize (SSM params), run drift checks.
- **Analogy:** Old address book entry — friend moved, letter bounces. Letter (template) correct; destination gone. Update the address.
- **Example:** `terraform apply` fails `InvalidAMIID.NotFound`. Fix: refresh AMI to current ID (SSM param), re-apply.

### 6.1.4 — DEPRECATION OF FUNCTIONALITY
- **Definition:** API/feature/instance type retired by the provider everywhere — `t1.micro` retired, old API off, tier discontinued. Platform exists; that generation gone.
- **Why it matters:** "our type no longer offered" / "API shut off" → deprecation. Fix: migrate to replacement before deadline. Differs from regional availability (exists, just not here).
- **Analogy:** Old road permanently closed by the city — everywhere, forever. Take the new road; old one gone for all.
- **Example:** `m1.small` fails `Unsupported` — retired everywhere. Fix: `t3.micro` + update template.

### 6.1.5 — OUTAGES
- **Definition:** Service/region unavailable because the provider is down, not your config. Two severities: full (entire service/region) and partial (some instances/AZs/endpoints). Remediation differs.
- **Why it matters:** All down + status incident = full; only one AZ = partial. Full → failover/multi-region or wait; partial → drain bad AZ, redeploy healthy. Trap: don't do full multi-region failover for a single-AZ problem.
- **Analogy:** Full = whole city loses power; partial = one broken traffic light. Don't evacuate the city for one light.
- **Example:** All AZs down + region incident → full outage; fail over or wait. One AZ 500s → partial; drain and reroute.

#### 6.1.5a — Full
- **Definition:** Entire service/region down — provider-side incident. No config/permission/capacity change on your side fixes it (problem is upstream).
- **Why it matters:** Uniform "everything fails" + status incident = full outage. Fix: failover to another region/AZ or wait. Don't confuse with regional availability (permanent non-offering).
- **Analogy:** Whole restaurant closed (fire). No table reassignment helps — go to the branch or wait to reopen.
- **Example:** All users down, status shows region incident → full outage (6.1.5a). Fail over or wait; invest in multi-region after.

#### 6.1.5b — Partial
- **Definition:** Degraded — only some instances/AZs/endpoints affected; rest normal. Localized, not global.
- **Why it matters:** "only `us-east-1a` affected" → partial. Fix: drain bad nodes, redeploy healthy AZs. Multi-AZ design absorbs these automatically. Trap: don't over-react with full failover.
- **Analogy:** One broken traffic light, not the whole city. Route around it; don't evacuate.
- **Example:** `ap-southeast-1b` returns 500s, `1a`/`1c` fine → partial (6.1.5b). Drain bad AZ, reroute LB to healthy.

### 6.1.6 — RESOURCE LIMITS
- **Definition:** Hard caps enforced by the provider on what you can do/provision. Two kinds: API throttling (rate — too fast) and service quotas (quantity — too many). Both say stop, for different reasons.
- **Why it matters:** `429` → throttling (backoff+retry); `QuotaExceeded`/`vCPU` → quota (raise). Trap: treating 429 as a quota problem won't help — speed vs quantity.
- **Analogy:** Quota = "max 10 VMs," no 11th without raising the rule. Throttling = "press the button at most 5×/sec" — allowed 11, just too fast.
- **Example:** 429 under heavy parallel CI → throttling; `InstanceLimitExceeded` → quota. Different fixes.

#### 6.1.6a — API throttling
- **Definition:** Rate limit — you call the API faster than allowed, rejected with `HTTP 429`. Service healthy; you're just too chatty. Each account/region/API has burst + steady-state rates.
- **Why it matters:** `429` → throttling; fix with exponential backoff + jitter, retry, batch, honor `Retry-After`. NOT a quota increase, NOT an outage. Trap: raising a vCPU quota to "fix" 429 is wrong mechanism.
- **Analogy:** Clicking a button 100×/sec, system says "one at a time." Allowed more; just can't ask that fast. Slow down.
- **Example:** `DescribeInstances` loop every 200 ms → 429, Terraform times out, instances healthy. Fix: backoff+jitter, batch, SDK retry config. No quota change.

#### 6.1.6b — Service quotas
- **Definition:** Account-wide hard/soft cap on total quantity of a resource — vCPUs per family, VPCs, EIPs, Lambda concurrency. Hit it → `QuotaExceeded`/`InstanceLimitExceeded`/`vCPU limit`. Unlike oversubscription, applies in every AZ (tied to account).
- **Why it matters:** `InstanceLimitExceeded`/`vCPU` → quota; fix by requesting increase (often auto-approved) or freeing resources. Distinct from `InsufficientInstanceCapacity` (move AZ, not raise). Trap: moving AZs won't help a quota error.
- **Analogy:** Account's "max 10 VMs" — have 10, no AZ move/retry makes 11. Ask to raise the rule or delete some.
- **Example:** `c5.9xlarge run-instances` fails vCPU limit at scale. Fix: Service Quotas increase; re-run.

### 6.1.7 — REGIONAL SERVICE AVAILABILITY
- **Definition:** A service/instance type/feature simply isn't offered in the region you chose — permanently. Never available there; not deprecated (everywhere gone), not an outage (temporarily down).
- **Why it matters:** "p3 GPU won't launch in `ap-southeast-1`" → regional availability. Fix: deploy in a supported region or pick another type. Opposite of deprecation. Check the regional catalog first.
- **Analogy:** Phone model not sold in your country — exists, just not stocked there. Import from a country that sells it, or pick another model. Never "recalled."
- **Example:** `p3` in `ap-southeast-1` → "not supported in this region." Fix: deploy in `us-east-1` or choose a local GPU type.

> **Cause cheat:** access denied=permission · no capacity=oversubscription/quota · type missing=region/deprecation · 429=throttling · version clash=incompatibility · stale AMI=outdated defs · all down=full outage · some down=partial.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES


- **3.1 `AccessDenied` on ECR push + `ecs update-service`** → Permission issues (IAM). Pipeline task role had read-only ECR perms, lacking `ecr:PutImage` + `ecs:UpdateService`; laptop worked via admin creds. Fix: grant those actions; re-run. Exam: `AccessDenied` is always IAM, never oversubscription/quota.
- **3.2 `InsufficientInstanceCapacity` in one AZ** → Oversubscription. No physical capacity of that family in that AZ at that moment; `us-east-1b` launches instantly. Fix: another AZ / Auto Scaling / Reserved / Capacity Reservation. Exam: move AZ, NOT raise quota.
- **3.3 `quota exceeded for vCPU`** → Service quota. Account hit soft vCPU cap (e.g. 64 for `c5` in `us-east-1`), same in every AZ. Fix: Service Quotas increase. Exam: vCPU limit = quota, raise it; distinct from `InsufficientInstanceCapacity`.
- **3.4 API `429`** → Throttling. Rate limit exceeded; instances healthy. Fix: exponential backoff + jitter, batch, slow down. Exam: 429 = throttling, NOT quota increase, NOT outage.
- **3.5 Terraform: missing AMI + retired instance type** → Outdated component definitions + Deprecation. AMI deregistered (stale reference) + `m1.small` retired everywhere. Fix: refresh AMI via SSM param, replace with `t3.micro`, pin versions, drift checks. Exam: missing/retired reference = outdated defs/deprecation, never incompatibility/permission.

---


## SECTION 4 — COMPARISON TABLES


### 4.1 Symptom → 6.1 Cause Mapping

| Symptom | Root cause (6.1) | Quick fix direction |
|---|---|---|
| `AccessDenied` / authorization failures | **Permission issues** (IAM) | Fix role/policy |
| `InsufficientInstanceCapacity` (one AZ) | **Oversubscription** | Move AZ / Reserved |
| `429 Too Many Requests` | **API throttling** | Backoff + retry |
| Version / SDK / API mismatch errors | **Incompatibility** | Align versions |
| `Unsupported` / retired type, `NotFound` AMI | **Outdated component defs** / **Deprecation** | Update references |
| Entire region/service down | **Full outage** | Failover / wait |
| Only some AZs / endpoints down | **Partial outage** | Route around AZ |

### 4.2 Oversubscription vs Service quota

| Dimension | Oversubscription | Service quota |
|---|---|---|
| What's limited | Physical capacity **in one AZ** | Account-wide **soft cap** (vCPU, etc.) |
| Error | `InsufficientInstanceCapacity` | `InstanceLimitExceeded` / `vCPU quota` |
| Scope | Single AZ, others work | Same limit in **every** AZ |
| Fix | Move AZ, Reserved Instances, Capacity Reservation | **Raise the quota** (Service Quotas) |
| Who controls it | AWS data-center capacity | Your account limit (request bump) |

### 4.3 API throttling vs Service quota

| Dimension | API throttling | Service quota |
|---|---|---|
| Signal | `429 Too Many Requests` | `LimitExceeded` / quota error |
| Meaning | Too many calls **per second** | Too many resources **total** |
| Fix | Backoff, retry, batch, slow down | Increase the account quota |
| Is the service down? | No — fully available | No — you just hit a cap |

### 4.4 Full vs Partial outage

| Dimension | Full outage | Partial outage |
|---|---|---|
| Blast radius | Entire region/service | Some AZs / some endpoints |
| Symptom | Everything fails uniformly | Works in AZ-b, fails in AZ-a |
| Action | Failover to another region / wait | Reroute to healthy AZ |
| Exam tell | "all down" | "some down, others fine" |

### 4.5 Incompatibility vs Outdated component definitions

| Dimension | Incompatibility | Outdated component defs |
|---|---|---|
| Nature | Version/SDK/API **clash** at runtime | Stale **reference** in template |
| Example | Old SDK calls new API field | Hardcoded AMI ID that's gone |
| Fix | Align/upgrade versions | Refresh the stale reference |

### 4.6 Permission issues vs Regional availability

| Dimension | Permission issues | Regional availability |
|---|---|---|
| Block reason | IAM principal lacks rights | Region **never offered** the feature |
| Error | `AccessDenied` | `UnsupportedOperation` / not in region list |
| Fix | Fix role/policy | Choose a supported region |
| Scope | Your account | Provider-wide per region |

---


## SECTION 5 — AWS PROVIDER MAPPING


AWS-ONLY mapping for objective 6.1. On AWS, deployment troubleshooting is anchored by two native services — AWS CloudFormation (Infrastructure as Code, where drift = a real-world change that no longer matches your template) and AWS Config (continuous configuration compliance and resource change history). Every 6.1 cause below can be seen and fixed through these two lenses, supplemented by the related AWS service that surfaces the symptom.

### CloudFormation — drift detection
A CloudFormation stack drift occurs when the actual resource configuration diverges from the template used to create the stack. This directly exposes several 6.1 causes:
- Outdated component definitions (6.1.3): a template pinned to a now-removed AMI ID / parameter — CloudFormation DetectStackDrift + DescribeStackResourceDrifts flag the resource as MODIFIED/DELETED.
- Misconfigurations (6.1.2): a manually edited security group or instance type shows as drift vs. the template's intended state.
- Deprecation of functionality (6.1.4): a template referencing a retired instance type fails at CREATE/UPDATE with Unsupported; CloudFormation marks the resource failed and surfaces the API error.
- Incompatibility (6.1.1): a runtime/SDK mismatch surfaces as a failed UPDATE or a post-deploy health-check failure rather than a template error — catch it with stack CREATE_FAILED rollback and cfn-lint pre-checks.

### AWS Config — configuration compliance
AWS Config records every configuration change and evaluates resources against rules, giving you the audit trail for the capacity/limits causes:
- Resource limits — quotas (6.1.6b): Config rules + Service Quotas show InstanceLimitExceeded / vCPU ceilings; Config's configuration history proves the account hit its cap.
- Resource limits — API throttling (6.1.6a): CloudTrail (paired with Config) shows ThrottlingException / 429 spikes; Config confirms the resource itself is healthy (not misconfigured).
- Permission issues (6.1.2b): Config + IAM Access Analyzer show a changed/over-broad or missing role binding that blocks the deploy identity.
- Regional availability (6.1.7): Config shows the resource can only be created in supported regions; a launch in an unsupported region fails and is recorded.

### Cause — AWS service table

| 6.1 Cause | Where you SEE it (AWS) | Where you FIX it (AWS) |
|---|---|---|
| Incompatibility (6.1.1) | CloudFormation CREATE_FAILED / cfn-lint; ECS/EKS health checks | Rebuild image against matching base; align SDK/version |
| Misconfiguration (6.1.2) | CloudFormation drift; AWS Config non-compliant rules | Edit template / Config rule remediation |
| Permission (6.1.2b) | IAM Access Analyzer; CloudTrail AccessDenied | Attach correct IAM role/policy (least privilege) |
| Oversubscription (6.1.2c) | EC2 InsufficientInstanceCapacity launch error | Move AZ / Capacity Reservation / RI |
| Sizing (6.1.2d) | CloudWatch OOM / No space left; Config undersized | Right-size instance/volume |
| Outdated defs (6.1.3) | CloudFormation MODIFIED/DELETED drift | Refresh AMI/template references |
| Deprecation (6.1.4) | CloudFormation Unsupported on create/update | Migrate to replacement type/API |
| Outage (6.1.5) | AWS Health Dashboard; CloudWatch alarms | Failover / multi-AZ; wait for recovery |
| Resource limits (6.1.6) | Service Quotas console; CloudTrail 429 | Raise quota / add backoff+retry |
| Regional availability (6.1.7) | Region catalog / unsupported message | Deploy to supported region |

### Cross-cutting reminders
- CloudFormation drift = "reality != template" — your first stop for misconfig / outdated-def / deprecation symptoms.
- AWS Config = "what changed, when, is it compliant" — your audit trail for limits/permissions/regional causes.
- Both feed CloudTrail for the authorization and throttling evidence that pinpoints who/what triggered the failure.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A deployment fails with "AccessDenied" although the resource exists. What is the most likely cause?
   - **A.** Regional capacity exhaustion
   - **B.** Permission/misconfiguration
   - **C.** API throttling
   - **D.** Deprecation

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The resource exists, so capacity/region aren't the issue; `AccessDenied` means the deploying identity lacks the required IAM/role permission (6.1.2b).

**Why A is wrong:** Capacity exhaustion gives `InsufficientInstanceCapacity`, not `AccessDenied`, and the resource does exist here.
**Why C is wrong:** Throttling returns HTTP 429, not an `AccessDenied`/permission error.
**Why D is wrong:** Deprecation surfaces as `Unsupported`/retired-type errors, not an access-denied on an existing resource.

</details>

2. You receive "InsufficientInstanceCapacity" for a specific VM family in one AZ, but your quota is unused. Cause?
   - **A.** Quota exceeded
   - **B.** Throttling
   - **C.** Regional/capacity availability
   - **D.** Outdated definition

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** `InsufficientInstanceCapacity` is oversubscription (6.1.2c) — no physical capacity for that family in that AZ right now; the fix is move AZ / reserve, not raise a quota.

**Why A is wrong:** A quota error would be `InstanceLimitExceeded`/`vCPU` and applies in every AZ; your quota is explicitly unused.
**Why B is wrong:** Throttling is a 429 rate-limit, unrelated to physical capacity in one AZ.
**Why D is wrong:** An outdated definition fails at template apply (e.g. `InvalidAMIID.NotFound`), not with a live capacity error.

</details>

3. A container built on a dev laptop crashes on the cluster with a GLIBC mismatch. Cause?
   - **A.** Oversubscription
   - **B.** Incompatibility
   - **C.** Quota
   - **D.** Partial outage

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A GLIBC/architecture mismatch is a runtime incompatibility (6.1.1) — the image's libraries don't match the host's base/architecture; rebuild against the matching base.

**Why A is wrong:** Oversubscription is "no capacity in this AZ," not a library crash at startup.
**Why C is wrong:** A quota cap blocks provisioning, it doesn't cause a GLIBC crash on a running container.
**Why D is wrong:** A partial outage degrades some AZs/endpoints; it doesn't produce a deterministic GLIBC error.

</details>

4. CI pipelines return HTTP 429 "rate limit exceeded" after adding parallel jobs. Cause?
   - **A.** Quota breach
   - **B.** Throttling
   - **C.** Deprecation
   - **D.** Misconfiguration of subnets

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** HTTP 429 = API throttling (6.1.6a) — you're calling the API faster than allowed after adding parallel load; add backoff+jitter and stagger jobs.

**Why A is wrong:** A quota breach is a quantity cap (`InstanceLimitExceeded`/`vCPU`), not a per-second 429 rate error.
**Why C is wrong:** Deprecation breaks a retired API/feature for everyone, not just under parallel load.
**Why D is wrong:** Subnet misconfiguration causes connectivity/launch failures, not a 429 rate-limit response.

</details>

5. A Terraform apply fails with "QuotaExceeded" for vCPUs. Cause?
   - **A.** Throttling
   - **B.** Regional availability
   - **C.** Quota (resource limit)
   - **D.** Oversubscription

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** `QuotaExceeded` for vCPUs is a service quota (6.1.6b) — an account-wide hard cap hit; request a limit increase or free resources.

**Why A is wrong:** Throttling is a 429 rate error, not a quantity/quota-exceeded message.
**Why B is wrong:** Regional availability is "type not offered in this region," not a vCPU quota error.
**Why D is wrong:** Oversubscription is `InsufficientInstanceCapacity` in one AZ; moving AZs won't fix a quota cap.

</details>

6. A template works in us-east-1 but fails "OperationNotSupportedInRegion" in ap-southeast-1. Cause?
   - **A.** Permissions
   - **B.** Regional availability
   - **C.** Outdated definition
   - **D.** Throttling

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** `OperationNotSupportedInRegion` is regional service availability (6.1.7) — the feature/instance family isn't offered in that region; deploy to a supported region or pick another type.

**Why A is wrong:** A permission error is `AccessDenied`/`403`, not a region-unsupported message.
**Why C is wrong:** An outdated definition references a removed resource (e.g. `InvalidAMIID.NotFound`), not a region-support error.
**Why D is wrong:** Throttling is a 429; it doesn't vary by region the way this error does.

</details>

7. An app launches but cannot reach its database across subnets. Cause?
   - **A.** Insufficient capacity
   - **B.** Misconfiguration (SG/NACL/route)
   - **C.** Deprecation
   - **D.** Throttling

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The app runs but can't reach the DB across subnets — a security-group/NACL/route misconfiguration (6.1.2) isolates the resources; check SG inbound, NACL, and the route table.

**Why A is wrong:** Capacity issues prevent launch/scale, not cross-subnet reachability of a running app.
**Why C is wrong:** Deprecation removes a feature/API everywhere, not just the path to one database.
**Why D is wrong:** Throttling is an API rate limit (429), unrelated to network reachability between instances.

</details>

8. Deployments suddenly fail with "unknown parameter" on an API call that worked last month. Cause?
   - **A.** Partial outage
   - **B.** Deprecation / outdated definition
   - **C.** Quota
   - **D.** Oversubscription

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A parameter that worked last month now being "unknown" points to deprecation/removal of that API field (6.1.4) or an outdated definition still referencing it (6.1.3); update the call/template.

**Why A is wrong:** A partial outage degrades some endpoints, not a specific "unknown parameter" rejection.
**Why C is wrong:** A quota error is a quantity cap, not an "unknown parameter" schema error.
**Why D is wrong:** Oversubscription is capacity-in-an-AZ, unrelated to an API parameter rejection.

</details>

9. Autoscaler cannot add nodes: "no capacity" in the chosen AZ. Cause?
   - **A.** Permission error
   - **B.** Throttling
   - **C.** Regional/capacity availability
   - **D.** Sizing

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** "No capacity" in the chosen AZ is oversubscription (6.1.2c) — the node family has no free physical capacity there; try another AZ/type or use a Capacity Reservation.

**Why A is wrong:** A permission error is `AccessDenied`, not a capacity message.
**Why B is wrong:** Throttling is a 429 rate limit, not "no capacity."
**Why D is wrong:** Sizing is "instance too small for the workload" (OOM), not "can't provision because AZ is full."

</details>

10. A VM experiences high CPU steal and slow disks despite correct sizing. Cause?
   - **A.** Throttling
   - **B.** Oversubscription
   - **C.** Deprecation
   - **D.** Regional availability

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** High CPU steal + slow disks with correct sizing = the host is over-allocated (noisy neighbor), a form of oversubscription (6.1.2c); the physical host is starved, not your config.

**Why A is wrong:** Throttling is an API 429, not CPU steal on a running VM.
**Why C is wrong:** Deprecation removes a feature/type, it doesn't cause runtime CPU steal.
**Why D is wrong:** Regional availability is about whether a type is offered in a region, not runtime host contention.

</details>

11. A Kubernetes Helm chart fails on a newly upgraded cluster with CRD errors. Cause?
   - **A.** Incompatibility
   - **B.** Quota
   - **C.** Partial outage
   - **D.** Throttling

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** CRD/API-version errors after a cluster upgrade are an incompatibility (6.1.1) — the chart targets API versions the new cluster no longer serves; update the chart/CRDs to match.

**Why B is wrong:** A quota error blocks provisioning quantity, not CRD schema validation.
**Why C is wrong:** A partial outage degrades endpoints; it doesn't produce deterministic CRD version errors.
**Why D is wrong:** Throttling is a 429 rate limit, unrelated to CRD schema mismatch.

</details>

12. You cannot launch a GPU instance in your selected region. Cause?
   - **A.** Permissions
   - **B.** Regional availability
   - **C.** Oversubscription
   - **D.** Outdated definition

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** GPU families are limited to specific regions/AZs — this is regional service availability (6.1.7); choose a supported region or a locally available GPU type.

**Why A is wrong:** A permission error is `AccessDenied`, not "type not available in region."
**Why C is wrong:** Oversubscription is `InsufficientInstanceCapacity` in one AZ; here the type is unavailable across the region.
**Why D is wrong:** An outdated definition references a removed resource ID, not a region-offering gap.

</details>

13. Intermittent 5xx errors hit only one microservice while others are fine. Cause?
   - **A.** Full outage
   - **B.** Partial outage of a dependency/AZ
   - **C.** Quota
   - **D.** Sizing

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Only one service/AZ affected while others are fine = a partial outage (6.1.5b) of a dependency or AZ; check health and reroute/fail over that segment.

**Why A is wrong:** A full outage takes down everything uniformly, not just one microservice.
**Why C is wrong:** A quota cap blocks provisioning, not intermittent 5xx on a running service.
**Why D is wrong:** Sizing causes OOM/crashes under load, not intermittent 5xx isolated to one dependency.

</details>

14. A re-deploy fails with "EntityAlreadyExists." Cause?
   - **A.** Throttling
   - **B.** Misconfiguration of idempotency/naming
   - **C.** Deprecation
   - **D.** Regional availability

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** `EntityAlreadyExists` means a resource with that name already exists — an idempotency/naming misconfiguration (6.1.2); fix naming or import the existing resource instead of re-creating.

**Why A is wrong:** Throttling is a 429, not an "already exists" conflict.
**Why C is wrong:** Deprecation retires a type/API, it doesn't reject a duplicate name.
**Why D is wrong:** Regional availability is about offering a type in a region, not duplicate-name conflicts.

</details>

15. Deployments are slow only at month-end peak across teams. Most likely cause?
   - **A.** Deprecation
   - **B.** Throttling tied to shared-account burst limits
   - **C.** Full outage
   - **D.** Incompatibility

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Slowness only at peak across teams = API throttling (6.1.6a) on the shared automation account's burst limits; stagger jobs or request higher limits.

**Why A is wrong:** Deprecation breaks a feature permanently for everyone, not just at month-end peaks.
**Why C is wrong:** A full outage stops deployments entirely, not "slow only at peak."
**Why D is wrong:** Incompatibility is a version clash at runtime, not time-of-month slowness.

</details>

16. A template references a subnet that was deleted last quarter and apply fails. Cause?
   - **A.** Outdated definition / misconfiguration
   - **B.** Throttling
   - **C.** Oversubscription
   - **D.** Regional availability

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** A template pointing at a subnet deleted last quarter is a stale/outdated component definition (6.1.3) (a misconfiguration of references); refresh the IDs and re-validate.

**Why B is wrong:** Throttling is a 429, not a reference to a missing resource.
**Why C is wrong:** Oversubscription is capacity-in-an-AZ, unrelated to a deleted subnet ID.
**Why D is wrong:** Regional availability is about whether a type is offered, not a deleted specific resource.

</details>

17. An instance is under-provisioned and the app crashes under load. Cause?
   - **A.** Sizing misconfiguration
   - **B.** Permissions
   - **C.** Throttling
   - **D.** Deprecation

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Under-provisioned + crash under load = a sizing issue (6.1.2d) — wrong vCPU/RAM for the workload; right-size the instance and set requests/limits.

**Why B is wrong:** Permissions cause `AccessDenied`, not OOM/crash under load.
**Why C is wrong:** Throttling is an API 429, not an instance running out of resources.
**Why D is wrong:** Deprecation removes a type/feature, not "too small for the workload."

</details>

18. A managed service returns 503 only during a provider-incident window for one region. Cause?
   - **A.** Quota
   - **B.** Partial/full outage
   - **C.** Incompatibility
   - **D.** Oversubscription

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A 503 during a provider-incident window for a region is an outage (6.1.5) — provider-side, not your config; check the status page and fail over/wait.

**Why A is wrong:** A quota cap blocks provisioning, not a 503 from a live managed service.
**Why C is wrong:** Incompatibility is a runtime version clash, not a region-wide incident 503.
**Why D is wrong:** Oversubscription is capacity-in-your-AZ, not a provider incident affecting a managed service.

</details>

19. A build succeeds on x86 CI but pods crash on ARM nodes. Cause?
   - **A.** Regional availability
   - **B.** Incompatibility (architecture)
   - **C.** Throttling
   - **D.** Quota

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** x86 build crashing on ARM nodes is an architecture incompatibility (6.1.1) — build multi-arch images and set correct node selectors/affinities.

**Why A is wrong:** Regional availability is about offering a type in a region, not CPU architecture of nodes.
**Why C is wrong:** Throttling is a 429 API rate limit, unrelated to arch mismatch crashes.
**Why D is wrong:** A quota cap blocks provisioning count, not runtime arch crashes.

</details>

20. A deployment uses a long-lived access key that now returns "SignatureDoesNotMatch." Recent policy forced IMDSv2/role use. Cause?
   - **A.** Throttling
   - **B.** Misconfiguration / deprecation of key-based auth path
   - **C.** Oversubscription
   - **D.** Sizing

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A long-lived key rejected after a policy forced IMDSv2/roles is a misconfiguration/deprecation of the key-based auth path (6.1.2/6.1.4); switch to instance-profile/IAM roles.

**Why A is wrong:** Throttling is a 429, not a signature mismatch from a blocked auth method.
**Why C is wrong:** Oversubscription is capacity-in-an-AZ, unrelated to auth signature errors.
**Why D is wrong:** Sizing is instance magnitude (OOM/disk), not authentication failures.

</details>
