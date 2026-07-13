---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective2.4: Use Code to Deploy and Configure"
aliases: [Objective-2.4-Code-Deploy-Configure, Domain-2.4-Code-Deploy-Configure, IaC, CaC, Drift-Detection, Infrastructure-as-Code]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 2.4
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-2.4, iac, cac, infrastructure-as-code, configuration-as-code, scripting, yaml, json, drift-detection, versioning, repeatability, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.4: Given a Scenario, Use Code to Deploy and Configure Cloud Resources

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 2.0 Deployment Domain = 19% of total exam
> **Objective 2.4 Focus:** Using code (not click-ops) to deploy and configure cloud resources — Infrastructure as Code (IaC), Configuration as Code (CaC), scripting logic (variables, conditionals, operators, data types, functions), testing, documentation, data formats (JSON/YAML), repeatability, drift detection, and versioning.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 2.4
### Objective Name: *Given a scenario, use code to deploy and configure cloud resources.*

**What this objective means (beginner-friendly):**

Imagine you run a café. Instead of manually telling each new barista every step ("put the machine here, set temperature to 92°C..."), you write a **recipe book** (code) that sets up the whole café identically every time. That recipe book is:
- **Infrastructure as Code (IaC)** — the recipe that builds the café (servers, network, storage) from text.
- **Configuration as Code (CaC)** — the recipe that tunes what's inside (software settings, config files).
- **Scripting logic** — the "if this, then that" instructions (variables, conditions, math, functions) that make the recipe flexible.
- **Testing / Documentation / Formats** — checking the recipe works, writing it down clearly, and using a readable format (YAML or JSON).
- **Repeatability / Drift detection / Versioning** — making sure every café built from the recipe is identical, spotting when one drifts from the recipe, and keeping old versions so you can undo.

2.4 is about doing cloud the **"as code" way** instead of clicking around a console. CompTIA wants you to know the vocabulary and when to use each approach.

**Why it matters in the real world:**
- Manual console changes are slow, error-prone, and don't scale. A typo can take down production.
- "As code" makes infrastructure **repeatable, reviewable, auditable, and reversible** — core DevOps/SRE practice.
- Drift (someone clicked a change outside the code) causes "works in prod, broken in code" confusion and compliance gaps.

**Why CompTIA tests it:**
- 2.4 is a **"given a scenario, use code"** objective — expect scenario questions: "Which approach deploys the same environment to 3 regions?" (IaC + repeatability) or "Config changed outside code — what happened?" (drift).
- It sits between 2.3 (migration) and 2.5 (provisioning) — you migrate (2.3), then deploy with code (2.4), then provision the right resources (2.5).
- Terms like **IaC, CaC, drift, JSON vs YAML, variables/conditionals** are classic multiple-choice and PBQ material.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.4.1 — INFRASTRUCTURE AS CODE (IaC)

**Definition:** Managing and provisioning compute, network, storage, and other infrastructure through **machine-readable definition files** (not manual console clicks). The file *is* the infrastructure.

**Purpose:**
- Repeatable, consistent environments (dev = test = prod).
- Version-controlled, peer-reviewed, auditable changes.
- Fast recovery / rebuild (disaster → re-run code).

**How it works:**
1. Write a template (Terraform `.tf`, CloudFormation `.yaml`, Bicep, ARM).
2. Run a plan/validate step (shows what will change).
3. Apply → provider API creates the resources.
4. State file tracks what exists.

**Advantages:** Consistency, speed at scale, rollback via versioning, documentation-as-code, less human error.
**Disadvantages:** Learning curve, state-file management complexity, debugging abstract failures, possible "click first, codify later" lag.
**When to use:** Any environment you'll build more than once (almost everything), multi-region, teams, compliance needs.
**When NOT to use:** One-off throwaway test you'll delete in 5 minutes (console is fine) — but even then, code scales better.

### 2.4.2 — CONFIGURATION AS CODE (CaC)

**Definition:** Managing the **software/OS configuration** of resources (packages, config files, services, settings) through code/declarative files, typically applied *after* IaC builds the resource.

**Purpose:**
- Ensure every server has identical, correct settings.
- Automate post-deploy configuration (no manual SSH tweaks).

**How it works:** Tools like Ansible (playbooks), Chef (recipes), Puppet (manifests), or cloud-native config (AWS Systems Manager, cloud-init) push config to running instances.

**Advantages:** Consistent config, idempotent (safe to re-run), audit trail, faster onboarding.
**Disadvantages:** Tool sprawl, learning curve, ordering/dependency complexity, needs agent or SSH access.
**When to use:** Configuring many servers identically, enforcing baselines, post-provision hardening.
**When NOT to use:** Single ephemeral instance you configure once and discard (unless standardising).

> **IaC vs CaC (key distinction):** IaC builds the *infrastructure* (VM, VPC, bucket). CaC configures what's *inside* (install nginx, set firewall rules, edit config files). They're complementary — IaC first, CaC second.

### 2.4.3 — SCRIPTING LOGIC

Code that drives deployment/config must include programming logic. CompTIA lists five elements:

#### Variables
- **Definition:** Named placeholders that store values (e.g., `region = "ap-southeast-1"`).
- **Purpose:** Reuse values, avoid hardcoding, parameterise per environment.
- **Exam angle:** "Same template, different region/size per env" → use variables/parameters.

#### Conditionals
- **Definition:** Logic that runs code only if a condition is true (`if/else`, `count = var.env == "prod" ? 3 : 1`).
- **Purpose:** Branch behaviour (e.g., enable extra monitoring only in prod).
- **Exam angle:** "Only add the NAT gateway in production" → conditional.

#### Operators
- **Definition:** Symbols that perform operations — arithmetic (`+ - * /`), comparison (`== != > <`), logical (`&& || !`), assignment (`=`).
- **Purpose:** Compute values, compare, combine conditions.
- **Exam angle:** Understanding `==` (equal) vs `=` (assign) or `&&` (and) vs `||` (or) in a snippet.

#### Data Types
- **Definition:** The kind of value a variable holds — string (`"hello"`), number (`42`), boolean (`true/false`), list/array (`[a,b,c]`), map/object (`{key: val}`).
- **Purpose:** Correct typing avoids errors (can't add a string to a number).
- **Exam angle:** "A list of subnet IDs" → array; "on/off flag" → boolean.

#### Functions
- **Definition:** Reusable blocks of logic that take input and return output (e.g., `length()`, `lookup()`, `concat()`).
- **Purpose:** Avoid repetition, transform values, compute derived config.
- **Exam angle:** "Get the third element" or "join tags" → a function call.


---

### 2.4.4 — TESTING

**Definition:** Validating IaC/CaC before and after applying — syntax checks, plan/validate, linting, policy checks, and post-deploy smoke tests.

**Purpose:**
- Catch errors before they reach production (a bad template can delete everything).
- Enforce policy (no public S3, required tags).

**How it works:**
- **Lint/validate:** `terraform validate`, `cfn-lint`, `yamllint`.
- **Plan:** show the diff (`terraform plan`, CloudFormation change set) for human review.
- **Policy-as-code:** Open Policy Agent (OPA), AWS Config rules, Sentinel.
- **Smoke/integration tests:** hit the endpoint, check the config applied.

**Advantages:** Fewer outages, faster safe changes, audit confidence.
**Disadvantages:** Extra pipeline time, test-maintenance burden.
**When to use:** Always before prod apply; in CI/CD gating.
**When NOT to use:** Never skip for prod; only trivial throwaway exemptions.

### 2.4.5 — DOCUMENTATION

**Definition:** Comments in code, README, runbooks, and the code itself serving as living documentation of the environment.

**Purpose:**
- Others (and future-you) understand intent without reverse-engineering.
- Onboarding, audits, incident response.

**How it works:** Inline comments, module docs, a repo README explaining `how to deploy`, parameter tables, architecture diagrams as code (Cloudcraft, diagrams-as-code).

**Advantages:** Knowledge transfer, fewer "what does this do?" incidents.
**Disadvantages:** Docs rot if not maintained alongside code.
**When to use:** Every repo; keep docs with code (single source of truth).
**When NOT to use:** N/A — under-documenting is a debt, not a saving.

### 2.4.6 — FORMATS (JSON vs YAML)

IaC/CaC files are written in structured data formats. CompTIA explicitly lists two:

#### JSON (JavaScript Object Notation)
- **Definition:** Strict text format using `{}`, `[]`, quotes, commas.
- **Pros:** Universal, machine-native (APIs speak JSON), unambiguous.
- **Cons:** No comments, verbose, easy to break with a missing comma, indentation-blind.
- **Exam angle:** "API payload / AWS SDK response" → JSON; "needs comments" → not JSON.

#### YAML (Yet Another Markup Language)
- **Definition:** Indentation-based human-friendly format.
- **Pros:** Readable, supports comments (`#`), used by Kubernetes, CloudFormation YAML, Ansible, GitHub Actions.
- **Cons:** Indentation-sensitive (a wrong space breaks it), less strict.
- **Exam angle:** "Kubernetes manifest / Ansible playbook" → YAML; "human-readable + comments" → YAML.

> **Quick rule:** JSON = machines/APIs, strict. YAML = humans/Config, readable, comments OK.

### 2.4.7 — REPEATABILITY

**Definition:** The ability to run the same code and get the **identical** result every time, in any environment.

**Purpose:** Dev = test = prod; no "snowflake" servers; reliable rebuild.

**How it works:** Parameterised templates + versioned code + immutable inputs → identical output. Idempotency (re-running doesn't duplicate/break).

**Advantages:** Predictable, scalable, disaster-recoverable.
**Disadvantages:** Requires discipline (no manual tweaks post-deploy).
**When to use:** Every environment, especially multi-env/multi-region.
**When NOT to use:** N/A — always desired; the opposite (manual drift) is the failure mode.

### 2.4.8 — DRIFT DETECTION

**Definition:** Detecting when the **live environment no longer matches** the code/template that defined it (because someone clicked a change in the console, or an automated process altered state).

**Purpose:** Keep reality = code; surface unauthorised/compliance-breaking changes.

**How it works:**
- **IaC state drift:** `terraform plan` (shows diff vs state), `terraform refresh`.
- **Config drift:** AWS Config, Azure Policy, GCP Config Validator compare actual vs desired.
- **Alerting:** notify when drift detected; remediate (re-apply code or update code to match reality, then re-apply).

**Advantages:** Compliance, security, predictability, faster root-cause.
**Disadvantages:** Alert noise if benign changes occur; needs a process to fix.
**When to use:** Production, regulated, multi-account estates.
**When NOT to use:** Never in prod — skipping drift checks is a known failure mode.

### 2.4.9 — VERSIONING

**Definition:** Tracking changes to code over time using a VCS (Git) and/or template versions, so any prior state is recoverable.

**Purpose:** Rollback, audit trail, collaboration, blame/attribution, change history.

**How it works:** Commit IaC to Git; branch/PR for changes; tag releases; some tools version the template itself (CloudFormation `AWSTemplateFormatVersion`, module versions, semantic versioning).

**Advantages:** Reversible, reviewable, auditable, collaborative.
**Disadvantages:** Merge conflicts, discipline required.
**When to use:** Always — code lives in Git, never only on a laptop.
**When NOT to use:** N/A — unversioned infra code is a single points of failure.


---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — Infrastructure as Code

**AWS Example:** A fintech writes **Terraform** (provider-agnostic) to build a VPC, subnets, an EKS cluster, and RDS in `ap-southeast-1`. `terraform plan` is reviewed in a PR; `terraform apply` runs in CI only after approval. State is stored in S3 + DynamoDB lock. Same code deploys to a DR region by overriding the `region` variable.

**Azure Example:** A bank uses **Bicep/ARM** templates in a repo. `az deployment sub create` applies them; **Azure Policy** enforces "no public storage." **Azure DevOps** pipeline runs `arm-ttk` lint + what-if before apply. Drift is caught by **Azure Policy** compliance scans.

**GCP Example:** A startup uses **Terraform** + **Google Cloud Deployment Manager** for some services. **Cloud Build** runs `terraform validate` + `gcloud beta terraform` plan; **Config Validator** (OPA) blocks non-compliant config pre-apply.

### 3.2 — Configuration as Code

**AWS Example:** After IaC builds EC2, **AWS Systems Manager (SSM) Run Command / State Manager** and **cloud-init** configure the OS — install nginx, harden sshd, register with SSM. **Ansible** playbooks (CaC) enforce the baseline across 200 nodes idempotently.

**Azure Example:** **Azure Automation State Configuration** (PowerShell DSC) or **Ansible** on Azure VMs keeps config consistent. **VM Applications** bake config into images; **Azure Policy guest config** detects drift from desired state.

**GCP Example:** **OS Config** (guest policies) enforces package/ config state on Compute Engine. **Ansible** via **Google Compute Engine VM Manager** pushes playbooks. Startup scripts (CaC) run cloud-init on boot.

### 3.3 — Scripting Logic in Practice

**Variables:** `env = "prod"` selects instance size via `var.instance_size[var.env]`.
**Conditionals:** `count = var.env == "prod" ? 3 : 1` → 3 AZs in prod, 1 elsewhere.
**Operators:** `${var.count > 2 && var.ha ? "multi-az" : "single"}`.
**Data types:** `tags = { team = "payments", env = "prod" }` (map), `subnets = ["10.0.1.0/24","10.0.2.0/24"]` (list).
**Functions:** `element(var.subnets, 0)` returns first subnet; `lookup(var.sizing, var.env, "default")`.

### 3.4 — Testing / Drift / Versioning in Production

- **Testing:** A media company gates every IaC PR through **GitHub Actions** → `terraform fmt` + `validate` + `tflint` + **Checkov** (policy-as-code, no public buckets). Only passing PRs may apply.
- **Drift:** A retailer runs **AWS Config** rules ("EBS encrypted", "VPC flow logs on"). An engineer manually disables a flow log in console → AWS Config flags **NON_COMPLIANT** → Slack alert → re-apply code.
- **Versioning:** All IaC in **Git** with tagged releases (`v1.4.2`). A bad apply is rolled back by `git revert` + re-apply, or `terraform state`/`cloudformation rollback`.

### 3.5 — Formats

- **JSON:** Lambda event payloads, API Gateway request/response models, DynamoDB item format, AWS SDK responses. Strict, no comments.
- **YAML:** Kubernetes `Deployment.yaml`, Ansible `playbook.yml`, CloudFormation YAML templates, GitHub Actions `.yml`, GitLab CI. Human-readable, comments allowed.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — IaC vs CaC

| | IaC | CaC |
|---|---|---|
| Builds | Infrastructure (VM, VPC, bucket, LB) | Config inside resources (packages, files, services) |
| Runs | Before resource exists | After resource exists |
| Tools | Terraform, CloudFormation, Bicep, ARM, Pulumi | Ansible, Chef, Puppet, SSM, cloud-init, DSC |
| Goal | Consistent environment | Consistent configuration |
| Exam tip | "Build 3 identical VPCs" → IaC | "Same nginx config on 50 servers" → CaC |

### 4.2 — JSON vs YAML

| | JSON | YAML |
|---|---|---|
| Readability | Machine-first, strict | Human-first, indent-based |
| Comments | No | Yes (`#`) |
| Common use | API payloads, SDK responses, Lambda events | K8s, Ansible, CloudFormation YAML, CI |
| Gotcha | One missing comma breaks it | One wrong space breaks it |
| Exam tip | "Needs comments" → NOT JSON → YAML | "API response format" → JSON |

### 4.3 — IaC Tools (provider mapping)

| Approach | AWS | Azure | GCP |
|---|---|---|---|
| Declarative IaC | CloudFormation | ARM / Bicep | Deployment Manager / Terraform |
| Provider-agnostic | Terraform | Terraform | Terraform |
| CaC / config | SSM, cloud-init, Ansible | VM Apps, DSC, Ansible | OS Config, Ansible |
| Policy-as-code | AWS Config, SCP, Checkov | Azure Policy | Config Validator (OPA) |

### 4.4 — Repeatability vs Drift vs Versioning

| Concept | Question it answers | Failure mode | Exam signal |
|---|---|---|---|
| **Repeatability** | Same code → same result? | Snowflake servers | "3 envs must match" → IaC + params |
| **Drift detection** | Does live = code? | Console click changes reality | "Config changed outside code" → drift |
| **Versioning** | Can we roll back? | Unversioned = unrecoverable | "Bad deploy, revert" → Git tag/revert |

### 4.5 — Scripting Logic Elements

| Element | Example | Exam signal |
|---|---|---|
| Variable | `region = "ap-se-1"` | "Reuse value per env" → variable/parameter |
| Conditional | `count = env=="prod" ? 3 : 1` | "Only in prod" → conditional |
| Operator | `==`, `&&`, `+` | "Compare / combine / compute" |
| Data type | string, number, bool, list, map | "List of IDs" → array; "flag" → bool |
| Function | `length()`, `lookup()` | "Derive/transform value" → function |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — IaC vs CaC mix-up.**
Question: "You need the same nginx config on 50 servers."
Correct: **CaC** (Ansible/SSM) — that's config inside resources.
Why wrong: IaC builds the servers; it does't install/tune software. Don't pick Terraform for "configure the OS."

**Trap #2 — "Build 3 identical VPCs" → which approach?**
Correct: **IaC** with variables/parameters (repeatability).
Why wrong: CaC configures what's already built; it can't create the VPC.

**Trap #3 — JSON can have comments.**
Correct: **No** — JSON has no comment syntax. If the requirement is "add comments to the config file," that points to **YAML**.
Why wrong: Picking JSON when comments are needed fails the requirement.

**Trap #4 — YAML indent vs JSON comma.**
Correct: YAML breaks on wrong **indentation**; JSON breaks on wrong **comma/quote**.
Why wrong: The failure mode tells you the format. "One space off and it failed" → YAML.

**Trap #5 — Drift direction.**
Question: "An admin changed a security group in the console. The code no longer matches."
Correct: **Drift** (live ≠ code). Fix by re-applying code OR updating code to match, then re-applying.
Why wrong: It is NOT a versioning problem (versioning is about history/rollback), nor a repeatability problem (that's about building identically).

**Trap #6 — Console change = undo versioning?**
Correct: No. Versioning (Git) tracks code history; it can't stop someone clicking in the console. That's **drift detection's** job.
Why wrong: Confusing "who changed the file" (versioning) with "live ≠ code" (drift).

**Trap #7 — "Only add NAT gateway in prod."**
Correct: **Conditional** (if env == prod).
Why wrong: A variable alone stores the value; the branching logic is the conditional. Don't answer "variable" for a branch.

**Trap #8 — Variable vs Function.**
Question: "Compute the third subnet from a list automatically."
Correct: **Function** (e.g., `element()` / indexing).
Why wrong: A variable only stores; a function transforms/derives. Computing = function.

**Trap #9 — Data type mismatch.**
Question: "A true/false deployment flag" → type?
Correct: **Boolean**.
Why wrong: String ("true") is not a bool; a list is many values; a number is quantitative. Match the type to the value.

**Trap #10 — Skip testing before prod apply.**
Question: "Apply the template straight to production to save time."
Correct: **Wrong** — always plan/validate/test; a bad template can delete or expose everything.
Why wrong: The "save time" phrasing is the trap; real practice gates prod behind plan+policy checks.

**Trap #11 — IaC state as source of truth confusion.**
Question: "What tracks what Terraform actually created?"
Correct: The **state file** (plus the code). Drift = state/real diverge.
Why wrong: The code alone isn't enough; state records current reality.

**Trap #12 — Operator `=` vs `==`.**
Correct: `=` assigns, `==` compares. `&&` = AND, `||` = OR, `!` = NOT.
Why wrong: Reading `if x = 5` as comparison is the classic bug; in most IaC/DSLs `=` is assignment.

**Trap #13 — Repeatability needs idempotency.**
Question: "Re-running the script created duplicate resources."
Correct: The code was **not idempotent** — good IaC/CaC is safe to re-run.
Why wrong: Repeatability implies re-running yields same state, not duplicates.

**Trap #14 — Documentation location.**
Question: "Where should the runbook for the template live?"
Correct: **With the code** (repo README/comments) — single source of truth.
Why wrong: A separate wiki that rots is the anti-pattern; code-adjacent docs stay current.

**Trap #15 — Policy-as-code vs linting.**
Question: "Block any template that creates a public S3 bucket."
Correct: **Policy-as-code** (Checkov/OPA/AWS Config/Sentinel) — enforces rules pre/post-apply.
Why wrong: Linting only checks syntax/style, not security policy. Don't pick "lint" for a security gate.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Repeatable Multi-Region Deploy
**Scenario:** A company needs the same web stack (VPC, 2 subnets, ALB, 3 EC2, RDS) in `ap-southeast-1` (prod) and `ap-southeast-3` (DR). Environments must be identical except instance count and region.
**Question:** Which approach and which scripting elements ensure identical, repeatable deploys?
**Walkthrough:**
1. **IaC** (Terraform/CloudFormation) defines the stack once.
2. **Variables/parameters** hold `region` and `instance_count`; prod passes 3, DR passes 2.
3. **Conditional** adds the DR-only resources only when `env == "dr"` if needed.
4. **Versioning** in Git; **testing** via `plan`/`validate` + policy check before apply.
5. **Repeatability:** same code + different variable values = identical environments.
**Bonus Q:** After deploy, an engineer opens port 22 to 0.0.0.0 in console. What's that? → **Drift**; fix by re-applying code and enabling drift alerts (AWS Config).

### PBQ 2 — Configure 200 Servers Identically
**Scenario:** 200 Linux VMs built by IaC must all run the same nginx + hardened sshd + monitoring agent. Manual SSH is impractical.
**Question:** Which "as code" layer handles this, and how do you guarantee consistency + catch drift?
**Walkthrough:**
1. **CaC** (Ansible playbook / SSM State Manager / DSC) installs and configures post-build.
2. Playbook is **idempotent** — safe to re-run.
3. **Drift detection:** AWS Config / Azure Policy guest config / GCP OS Config compares actual vs desired; alerts on divergence.
4. **Versioning:** playbook in Git; **documentation** in repo README.
**Bonus Q:** Why not IaC for this? → IaC builds the VM; it does't maintain OS-level config over time.

### PBQ 3 — Format & Comment Decision
**Scenario:** Team writes K8s manifests and CI pipelines that humans edit daily; they also integrate a Lambda that receives JSON events from an API Gateway.
**Question:** Which format for each, and why?
**Walkthrough:**
1. **K8s manifests + CI** → **YAML** (human-readable, comments allowed for the team).
2. **Lambda event payload** → **JSON** (API/SDK-native, strict, no comments needed).
3. **Testing:** `yamllint` + `kubeval`; JSON validated by schema in the function.
**Bonus Q:** A teammate adds a `# note` to the Lambda event sample and it breaks. Why? → JSON has no comments; use YAML for annotated samples.

### PBQ 4 — Conditional + Variable Logic
**Scenario:** Template must create 3 AZs and enable a NAT gateway + extra CloudWatch alarms **only in production**, but a single AZ with no NAT in dev.
**Question:** Which scripting elements implement this?
**Walkthrough:**
1. **Variable:** `env` (string), `az_count` (number).
2. **Conditional:** `count = var.env == "prod" ? 3 : 1` for AZs; `count = var.env == "prod" ? 1 : 0` for NAT + extra alarms.
3. **Operator:** `==` compares; `? :` is the ternary (conditional) operator.
4. **Data types:** `env` = string, `az_count` = number, `enable_nat` = boolean.
5. **Function (optional):** `lookup(var.sizing, var.env)` picks sizes per env.
**Bonus Q:** Re-running created duplicate NATs. Why? → code was not **idempotent** (repeatability failure); correct IaC manages count, not creates blindly.

### PBQ 5 — Drift + Versioning Recovery
**Scenario:** A validated IaC apply ran in prod. Two days later someone deleted a subnet in the console "to test." Monitoring broke. You must restore and prevent recurrence.
**Question:** Which two 2.4 concepts apply, and the fix?
**Walkthrough:**
1. **Drift detection:** `terraform plan` / AWS Config shows live ≠ code (missing subnet).
2. **Fix:** re-apply the code (recreates the subnet) — code is the source of truth.
3. **Versioning:** the good state is in Git; if code had also been edited badly, `git revert` to last known-good tag + re-apply.
4. **Prevent recurrence:** enable continuous drift detection/alerting; restrict console perms (least privilege); require changes via PR.
**Bonus Q:** Is this a repeatability problem? → No — repeatability is about building identically; this is **drift** (live diverged from code).


---

## SECTION 7 — MEMORY AIDS

### 7.1 — IaC vs CaC (the core split)
**"IaC builds the house. CaC furnishes the rooms."**
- IaC = walls, roof, plumbing (VPC, VM, bucket).
- CaC = paint, furniture, settings (nginx, sshd, config files).
Order: **IaC first, then CaC.**

### 7.2 — JSON vs YAML
**"JSON = Java Strict Machine. YAML = You Are Mostly Human."**
- JSON: strict, no comments, machines/APIs.
- YAML: indent-based, comments OK, humans/K8s/Ansible.

### 7.3 — The 3 D's: Repeat / Drift / Version
**"Repeat builds it. Drift breaks it. Version saves it."**
- **Repeatability** = same code → same result.
- **Drift detection** = live ≠ code (someone clicked).
- **Versioning** = Git history, rollback.

### 7.4 — Scripting logic ladder
**"V CDO F"** → **V**ariable, **C**onditional, **D**ata type, **O**perator, **F**unction.
- Variable = stores a value
- Conditional = if/else branch
- Data type = what kind of value (string/num/bool/list/map)
- Operator = does math/compare/combine (`+ == &&`)
- Function = transforms/derives (returns something)

### 7.5 — Operator quick ref
- `=` assign · `==` compare
- `&&` AND · `||` OR · `!` NOT
- `+ - * /` math · `> < >= <=` compare

### 7.6 — Data type match game
- "on/off flag" → **boolean**
- "list of subnet IDs" → **array/list**
- "team= payments, env= prod" → **map/object**
- "hello" → **string** · `42` → **number**

### 7.7 — Decision tree (ASCII)
```
Given "use code to deploy/configure":
│
├─ Need to CREATE the infra (VPC/VM/bucket)? ──► IaC (+ variables/conditionals)
├─ Need to CONFIGURE what's inside (OS/software)? ──► CaC (Ansible/SSM/DSC)
├─ "Identical across envs/regions"? ───────────► Repeatability (params + idempotency)
├─ "Console change made live ≠ code"? ────────► Drift detection (re-apply / alert)
├─ "Bad deploy, need to undo"? ──────────────► Versioning (Git revert + re-apply)
└─ "File humans edit daily + need comments"? ──► YAML (not JSON)
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| **IaC (native)** | CloudFormation | ARM / Bicep | Deployment Manager |
| **IaC (agnostic)** | Terraform | Terraform | Terraform |
| **CaC / config mgmt** | SSM, cloud-init, Ansible | VM Applications, DSC, Ansible | OS Config, Ansible |
| **State / drift** | Terraform state, AWS Config | Terraform state, Azure Policy | Terraform state, Config Validator |
| **Policy-as-code** | AWS Config, SCP, Checkov, Sentinel | Azure Policy (guest config) | Config Validator (OPA) |
| **Testing / lint** | cfn-lint, tflint, Checkov | arm-ttk, PSRule, Checkov | gcloud beta terraform, Checkov |
| **Format — API/SDK** | JSON (SDK, Lambda events) | JSON (ARM JSON, SDK) | JSON (API responses) |
| **Format — human config** | YAML (GitHub Actions, K8s on EKS) | YAML (pipelines, K8s on AKS) | YAML (K8s on GKE, Cloud Build) |
| **Versioning** | Git + CodeCommit; CFN versions | Git + Azure Repos; template versions | Git + Cloud Source; Deployment versions |
| **Idempotent apply** | `terraform apply`, CFN (no dup) | `az deployment` (no dup) | `gcloud` / TF (no dup) |
| **Startup config (VM)** | user-data / cloud-init | custom data / cloud-init | startup-script / cloud-init |

**Portability note:** Terraform + YAML (K8s) + Ansible are the cross-cloud constant — same as the 2.3 vendor-lock-in lesson. CloudFormation/ARM/Deployment Manager are provider-specific IaC.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What is IaC and give one tool.**
A: Infrastructure as Code defines infrastructure in machine-readable files instead of console clicks. Example: Terraform (or CloudFormation). It makes environments repeatable and versionable.

**Q2: What is drift, in one sentence?**
A: Drift is when the live environment no longer matches the code/template that defined it — usually because someone changed it outside the code.

**Q3: JSON or YAML for a human-edited config file that needs comments?**
A: YAML — it's human-readable and supports comments; JSON has no comment syntax.

### 🎓 Cloud Administrator Questions

**Q4: IaC vs CaC — when do you reach for each?**
A: IaC (Terraform/CFN) to build infrastructure (VPC, VM, bucket). CaC (Ansible/SSM) to configure what's inside after it exists (packages, config files, services).

**Q5: How do you catch and fix drift in production?**
A: Run drift detection (terraform plan, AWS Config, Azure Policy, GCP Config Validator). On divergence, either re-apply the code (code is source of truth) or, if the console change was intentional, update the code to match — then re-apply. Alert on drift.

**Q6: Why must IaC/CaC be idempotent?**
A: So re-running is safe — it converges to the same state instead of duplicating or breaking resources. That's what makes repeatability real.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: A customer manually fixed prod by clicking in the console and now "code doesn't work." What do you tell them?**
A: That's drift. The fix is to re-apply the code (or update code to reflect the needed change) and to add drift detection + least-privilege console access so it can't silently happen again.

**Q8: Client asks "why not just click in the console, it's faster?"**
A: Console changes don't scale, aren't reviewed/versioned, cause drift, and aren't repeatable or auditable. As-code gives consistency, rollback, and compliance — cheaper over time.

**Q9: How does versioning help a bad deployment?**
A: Because the code is in Git with tagged releases, a bad apply is reverted via `git revert` + re-apply, or by re-applying the last known-good version. Unversioned code has no safe undo.

---

## SECTION 10 — FLASHCARDS

**IaC / CaC**
Q: What does IaC stand for?
A: Infrastructure as Code — define infra in files, not console clicks.

Q: IaC builds what?
A: Infrastructure (VPC, VM, bucket, load balancer).

Q: What does CaC stand for?
A: Configuration as Code — configure what's inside resources.

Q: CaC configures what?
A: OS/software settings (packages, config files, services).

Q: Order of the two?
A: IaC first (build), then CaC (configure).

Q: Example IaC tools?
A: Terraform, CloudFormation, Bicep, ARM, Pulumi.

Q: Example CaC tools?
A: Ansible, Chef, Puppet, SSM, cloud-init, DSC.

**Scripting logic**
Q: What stores a reusable value?
A: A variable / parameter.

Q: What branches behaviour on a condition?
A: A conditional (if/else, ternary).

Q: `==` vs `=`?
A: `==` compares; `=` assigns.

Q: `&&` `||` `!` mean?
A: AND, OR, NOT.

Q: "on/off flag" data type?
A: Boolean.

Q: "list of subnet IDs" data type?
A: List / array.

Q: "team=payments, env=prod" data type?
A: Map / object.

Q: What transforms/derives a value?
A: A function (e.g., length(), lookup()).

**Formats**
Q: JSON supports comments?
A: No — strict, machine-first.

Q: YAML supports comments?
A: Yes (`#`), human-friendly, indent-based.

Q: K8s manifest format?
A: YAML.

Q: Lambda event payload format?
A: JSON.

**Testing / Docs / Repeat / Drift / Version**
Q: What catches errors before prod apply?
A: Testing — validate, plan, lint, policy-as-code.

Q: What enforces "no public bucket" in code?
A: Policy-as-code (Checkov/OPA/AWS Config).

Q: Live config ≠ code is called?
A: Drift.

Q: Tool to detect AWS drift?
A: AWS Config / terraform plan.

Q: Same code → same result is?
A: Repeatability (needs idempotency).

Q: Undo a bad deploy via?
A: Versioning — Git revert + re-apply.

Q: Where should runbooks live?
A: With the code (repo README/comments) — single source of truth.

Q: Re-running created duplicates — what failed?
A: Idempotency / repeatability.

**Traps**
Q: "Same nginx on 50 servers" → IaC or CaC?
A: CaC (configuring inside, not building).

Q: "Build 3 identical VPCs" → IaC or CaC?
A: IaC + variables (repeatability).

Q: Console change broke code match → which concept?
A: Drift (not versioning, not repeatability).

Q: "Add comments to config" → JSON or YAML?
A: YAML (JSON has no comments).


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** Which approach defines infrastructure in machine-readable files instead of console clicks?
A) CaC  B) IaC  C) Drift  D) JSON
**Answer: B) IaC.** Infrastructure as Code.
*Why others wrong:* CaC configures inside; drift is divergence; JSON is a format.

**E2.** You must install and configure nginx identically on 50 servers after they are built. This is:
A) IaC  B) CaC  C) Versioning  D) Drift
**Answer: B) CaC.** Configuring inside resources = Configuration as Code.
*Why others wrong:* IaC builds the servers; versioning is history; drift is divergence.

**E3.** Which format supports comments and is indent-based?
A) JSON  B) YAML  C) XML  D) CSV
**Answer: B) YAML.** Human-friendly, comments via `#`.
*Why others wrong:* JSON has no comments; XML/CSV not the listed pair.

**E4.** When the live environment no longer matches the code, this is called:
A) Repeatability  B) Drift  C) Versioning  D) Linting
**Answer: B) Drift.**
*Why others wrong:* Repeatability is sameness; versioning is history; lint checks syntax.

**E5.** Storing a reusable value like `region = "ap-se-1"` uses a:
A) Function  B) Variable  C) Conditional  D) Operator
**Answer: B) Variable.**
*Why others wrong:* Function transforms; conditional branches; operator computes.

### MEDIUM (10)

**M1.** A team needs 3 identical environments (dev/test/prod) from one definition. Which concept delivers this?
A) Drift detection  B) Repeatability  C) Versioning  D) CaC
**Answer: B) Repeatability** (same code + parameters → identical envs).
*Why others wrong:* Drift is divergence; versioning is history; CaC configures inside.

**M2.** "Add a NAT gateway only in production" is implemented with a:
A) Variable  B) Conditional  C) Function  D) Data type
**Answer: B) Conditional** (if env == prod).
*Why others wrong:* Variable stores the env name; function transforms; data type is the value's kind.

**M3.** Which blocks a template that would create a public S3 bucket?
A) yamllint  B) Policy-as-code (Checkov/OPA)  C) Drift detection  D) Versioning
**Answer: B) Policy-as-code.** Enforces security rules pre/post-apply.
*Why others wrong:* Lint checks style/syntax; drift detects live≠code; versioning is history.

**M4.** An engineer adds a `# note` to a Lambda event sample and it fails to parse. Why?
A) YAML forbids comments  B) JSON has no comments  C) Drift  D) Idempotency
**Answer: B) JSON has no comments** — use YAML for annotated samples.
*Why others wrong:* YAML allows comments; drift/idempotency unrelated.

**M5.** Which is the BEST source of truth for what was deployed?
A) Console screenshot  B) The code + state file  C) A teammate's memory  D) The lint output
**Answer: B) The code + state file.**
*Why others wrong:* Screenshot/memory are not authoritative; lint only checks syntax.

**M6.** Re-running a playbook created duplicate users. The failure is:
A) Drift  B) Lack of idempotency  C) Versioning  D) Wrong format
**Answer: B) Lack of idempotency** — good code is safe to re-run.
*Why others wrong:* Drift is live≠code; versioning is history; format unrelated.

**M7.** A "true/false" deployment flag is what data type?
A) String  B) Number  C) Boolean  D) List
**Answer: C) Boolean.**
*Why others wrong:* String is text; number is quantitative; list is many values.

**M8.** After a bad apply, the team restores the last known-good definition. This relies on:
A) Drift detection  B) Versioning  C) Repeatability  D) CaC
**Answer: B) Versioning** (Git revert/tag + re-apply).
*Why others wrong:* Drift detects divergence; repeatability builds same; CaC configures.

**M9.** `=` vs `==` in IaC/DSL logic:
A) Both compare  B) `=` assigns, `==` compares  C) Both assign  D) They are identical
**Answer: B) `=` assigns, `==` compares.**
*Why others wrong:* Mixing them is the classic bug.

**M10.** K8s manifests and Ansible playbooks are written in:
A) JSON  B) YAML  C) XML  D) CSV
**Answer: B) YAML** (human-edited, comments allowed).
*Why others wrong:* JSON is API/SDK-native, no comments.

### HARD (5)

**H1.** A company builds infra with Terraform, but a week later a subnet was deleted in the console and monitoring broke. The MOST complete remediation is:
A) Git revert only  B) Re-apply code + enable drift detection + least-privilege console  C) Switch to JSON  D) Add more variables
**Answer: B) Re-apply code (code is source of truth) + enable drift detection/alerting + restrict console perms.**
*Why others wrong:* Git revert only helps if code itself changed; format/variables don't address drift.

**H2.** Which combination best guarantees prod = test = dev?
A) CaC + YAML  B) IaC + variables + idempotency  C) JSON + functions  D) Drift + versioning
**Answer: B) IaC + variables (parameterise per env) + idempotency (safe re-run) = repeatability.**
*Why others wrong:* CaC configures inside; JSON/functions alone don't enforce sameness; drift/version are separate controls.

**H3.** You must create 3 AZs in prod but 1 in dev, enable a NAT only in prod, and size instances per environment from a lookup table. Which elements are required?
A) Variables + conditionals + functions  B) JSON + YAML  C) Drift + versioning  D) CaC only
**Answer: A) Variables (env, sizes) + conditionals (AZ/NAT by env) + functions (lookup sizing).**
*Why others wrong:* Formats don't branch; drift/version are ops controls; CaC configures, doesn't branch infra.

**H4.** A security audit finds resources drifted from policy. The control that should have caught it BEFORE apply was:
A) Linting  B) Policy-as-code  C) Versioning  D) Documentation
**Answer: B) Policy-as-code** (Checkov/OPA/AWS Config) enforces rules pre/post-apply.
*Why others wrong:* Lint checks syntax/style; versioning is history; docs don't enforce.

**H5.** Best practice for the runbook that explains how to deploy a template:
A) A separate wiki page  B) With the code (repo README/comments)  C) Only in the console  D) In a ticket
**Answer: B) With the code** — single source of truth that stays current.
*Why others wrong:* Separate wiki rots; console/ticket aren't the living doc.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| IaC vs CaC distinction | 🔴 CRITICAL | Most-tested split; "build vs configure" |
| Drift detection | 🔴 CRITICAL | "Live ≠ code" classic scenario |
| Repeatability / idempotency | 🔴 CRITICAL | "Identical across envs" |
| Versioning (Git rollback) | 🟠 HIGH | "Bad deploy, revert" |
| JSON vs YAML | 🟠 HIGH | Comments/format trap |
| Variables / Conditionals | 🟠 HIGH | "parameterise / only in prod" |
| Testing / policy-as-code | 🟠 HIGH | "block public bucket / validate first" |
| Operators / data types | 🟡 MEDIUM | `==` vs `=`, bool/list/map |
| Functions | 🟡 MEDIUM | derive/transform values |
| Documentation (with code) | 🟢 LOW | Single-source-of-truth principle |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**IaC vs CaC**
- IaC = build infra (VPC, VM, bucket) — Terraform / CloudFormation / Bicep / ARM.
- CaC = configure inside (nginx, sshd, config files) — Ansible / SSM / DSC / cloud-init.
- Order: **IaC first, then CaC.**

**Scripting logic**
- Variable = stores a value (region, size)
- Conditional = branch (if env=="prod")
- Operator = `=` assign, `==` compare, `&&` AND, `||` OR, `!` NOT, `+ - * /`
- Data types = string, number, boolean, list/array, map/object
- Function = transforms/derives (length(), lookup())

**Formats**
- JSON = strict, machine/API, NO comments → Lambda events, SDK responses
- YAML = indent-based, human, comments OK → K8s, Ansible, CI, CFN YAML

**The 3 D's**
- Repeatability = same code → same result (needs idempotency)
- Drift = live ≠ code (console click) → re-apply / alert
- Versioning = Git history → revert + re-apply

**Testing / Docs**
- Validate → plan → policy-as-code (Checkov/OPA/AWS Config) → apply
- Docs live WITH code (repo README/comments)

**Exam triggers → answer**
- "Same nginx on 50 servers" → CaC
- "Build 3 identical VPCs" → IaC + variables (repeatability)
- "Only in prod" → conditional
- "Add comments to config" → YAML (not JSON)
- "Console change broke code match" → drift
- "Bad deploy, undo" → versioning (Git revert)
- "Block public bucket" → policy-as-code
- "Re-run made duplicates" → not idempotent

**Acronyms**
- IaC = Infrastructure as Code · CaC = Configuration as Code
- DSL = Domain-Specific Language · API = Application Programming Interface
- JSON = JavaScript Object Notation · YAML = Yet Another Markup Language
- CI/CD = Continuous Integration / Continuous Delivery
- OPA = Open Policy Agent · SSM = Systems Manager (AWS)
- DSC = Desired State Configuration · PR = Pull Request
- SDK = Software Development Kit · ALB = Application Load Balancer

**Traps recap**
1. IaC builds, CaC configures. 2. YAML=comments, JSON=no comments. 3. Drift=live≠code (not versioning). 4. Versioning=history (can't stop console clicks). 5. "Only in prod"=conditional (not variable). 6. Compute value=function (not variable). 7. true/false=boolean. 8. Always test before prod. 9. State file tracks reality. 10. `=` assign vs `==` compare. 11. Repeatability needs idempotency. 12. Docs with code. 13. Policy-as-code ≠ lint. 14. K8s/CI = YAML. 15. Lambda events = JSON.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **Terraform / OpenTofu:** HashiCorp's license change (2023) spawned **OpenTofu** (Linux Foundation, MPL) — same HCL, open fork. Both remain the dominant provider-agnostic IaC; exam still treats "Terraform" as the canonical cross-cloud IaC.
- **CloudFormation:** AWS added `cfn-lint` maturity, `hooks` for policy guardrails pre-apply, and `CloudFormation Git sync` (template-in-repo auto-deploy). Bicep (Azure) compiles to ARM and is now the recommended Azure IaC over raw ARM JSON.
- **Policy-as-code mainstream:** **Open Policy Agent (OPA)** + **Checkov** + **Snyk IaC** + **AWS Config** rules are standard pre-apply gates in CI — directly the 2.4 "Testing" concept.
- **Drift detection as a service:** Terraform Cloud/Enterprise, AWS Config, Azure Policy "guest configuration," and GCP Config Validator now continuously flag drift — the 2.4 drift concept is production-real, not academic.
- **GitOps:** **Argo CD / Flux** make Git the source of truth for K8s — versioning + repeatability + drift (auto-reconcile) in one loop. Ties IaC + CaC + versioning together.
- **AI-assisted IaC:** Amazon Q / GitHub Copilot / cloud copilots now generate and review IaC — but human `plan`/`validate`/policy review stays mandatory (testing concept).
- **Modules & reuse:** Public module registries (Terraform Registry, Azure Verified Modules, GCP modules) push "write once, compose everywhere" — repeatability via versioned modules.
- **Supply-chain security:** **SLSA** + signed modules + provenance for IaC artifacts — documentation/versioning now part of compliance, not just convenience.

*End of Objective 2.4 notes.*
