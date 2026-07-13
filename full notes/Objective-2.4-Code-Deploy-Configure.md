# CompTIA Cloud+ CV0-004 — Domain 2.0 Deployment
## 📘 Objective 2.4: Given a scenario, use code to deploy and configure cloud resources

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Objective 2.4 Focus:** Infrastructure as code (IaC), Configuration as code (CaC), scripting logic (variables, conditionals, operators, data types, functions), testing, documentation, formats (JSON vs YAML), repeatability, drift detection, versioning.

---

## SECTION 1 — Exam Objectives

- **Objective 2.4:** Given a scenario, use code to deploy and configure cloud resources.
- **What it means:** Use *code* — not manual console clicks — to create and configure cloud resources, plus the supporting practices that make code-based deployment reliable (testing, documentation, format choice, repeatability, drift detection, versioning).
- **Sub-topics to know (from official objectives):**
  - **Infrastructure as Code (IaC)** — declare servers, networks, and storage in code files.
  - **Configuration as Code (CaC)** — declare the *settings* applied to resources after they exist.
  - **Scripting logic** — variables, conditionals, operators, data types, functions inside deployment code.
  - **Testing** — validate code before it changes production.
  - **Documentation** — explain what the code does and how to use it.
  - **Formats** — JavaScript Object Notation (JSON) and YAML (Yet Another Markup Language).
  - **Repeatability** — the same code produces the same result every time.
  - **Drift detection** — spot when the live environment stopped matching the code.
  - **Versioning** — track changes to your code over time.
- **Why it matters in the real world:**
  - Manual console clicks do not scale and are not auditable; code is reviewable, repeatable, and recoverable.
  - "Works on my account but not yours" disappears when everyone deploys from the same code.
  - Auditors and incident responders need to know *exactly* what was deployed and when — versioning + documentation provide that.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.4.1 Infrastructure as Code (IaC)
- IaC declares cloud resources (VMs, networks, subnets, load balancers, storage, IAM) in text files instead of manual console work.
- Two schools: **declarative** (state the end-state; the tool computes steps — CloudFormation, Terraform, ARM, GCP Deployment Manager) vs **imperative/procedural** (step-by-step commands — shell scripts, AWS CLI, ordered Ansible).
- IaC gives a single source of truth; one template stands up dev/test/prod; destruction and recreation become trivial.
- Tools to name-drop: Terraform (cloud-agnostic), CloudFormation (AWS-native), ARM/Bicep (Azure), Pulumi (real languages), Ansible (often used for CaC).

### 2.4.2 Configuration as Code (CaC)
- CaC sets the *settings* on top of resources IaC built (web server config, firewall rules, OS tuning, app config file).
- Tools: Ansible, Chef, Puppet, SaltStack push desired config and continuously enforce it.
- "Immutable infrastructure" rebuilds from a new image (IaC) instead of patching live; many shops still use CaC to keep fleets converged to a known-good state.
- Idempotency — same config twice yields the same safe result — is the golden property of good CaC.

### 2.4.3 Scripting Logic — Variables
- Variables avoid hard-coding so the same script works in many contexts (`region = "us-east-1"`, `instance_count = 3`, `environment = "prod"`).
- Sources: file defaults, runtime parameters/inputs, environment variables, or lookups (e.g., an existing VPC ID).
- Swapping input values deploys the identical template to dev/test/prod — core to repeatability.
- Watch scope (global vs local), typing (a string `"3"` is not the number `3`), and interpolation (`"web-${env}-sg"`).

### 2.4.4 Scripting Logic — Conditionals
- Conditionals (`if/else`, `when`, ternary) branch on inputs/environment — "if prod deploy 3, else 1."
- CloudFormation uses `Conditions` + `!If`; Terraform uses `count`/`for_each` with `if`; Ansible uses `when`.
- One flexible template replaces many per-situation copies, reducing maintenance and forgotten edits.
- Answers "deploy X *only if* Y is true" — e.g., attach a public IP only if `public = true`.

### 2.4.5 Scripting Logic — Operators
- Operators: arithmetic (`+ - * / %`), comparison (`== != > < >= <=`), logical (`&& || !`), and string/assignment.
- Used inside conditionals and functions — comparing region strings, combining booleans with `&&`, computing `instance_count + 1`.
- Trap: confusing assignment (`=`) with comparison (`==`/`===`).
- Predict expression results (e.g., `ceil(instance_count / 2)`); AND needs both true, OR needs one, NOT flips a boolean.

### 2.4.6 Scripting Logic — Data Types
- Types: string (`"hello"`), number/integer (`42`), boolean (`true`/`false`), list/array (`[a,b,c]`), map/object/dictionary (`{key: value}`), sometimes null.
- Mismatched types are a top failure cause (string where number expected, list where single string required).
- YAML uses indentation + dashes; JSON uses `{}` object and `[]` array.
- IaC is strict: `"3"` (string) will not satisfy a parameter expecting `3` (number).

### 2.4.7 Scripting Logic — Functions
- Functions compute values instead of hard-coding: string (`join`, `split`), numeric (`min`, `max`, `ceil`), collection (`length`, `lookup`, `element`), cloud intrinsics.
- CloudFormation `!Ref`, `!GetAtt`, `!Sub`; Terraform `lookup()`, `element()`.
- User-defined functions (Pulumi, scripting languages) encapsulate repeated logic.
- A function *returns* a value used elsewhere — `!Ref` gets a resource ID, `!GetAtt` fetches an attribute.

### 2.4.8 Testing
- Levels: lint/syntax (`terraform validate`, `cfn-lint`), dry-run/plan (`terraform plan`, CloudFormation change sets), unit, integration/compliance (sandbox + Checkov/tfsec/Sentinel).
- "Test before you apply" mindset — never blindly run code that creates or destroys infrastructure.
- Drift and cost surprises are caught early by planning and policy scanning.
- Safe order: write → lint → plan/validate → review → apply → verify.

### 2.4.9 Documentation
- Explains what the code does, how to run it, what inputs it needs, and what it produces.
- For IaC: READMEs, commented templates, a variables/parameters reference, teardown runbooks.
- Required engineering practice (not busywork): supports onboarding, audits, incident response, disaster recovery.
- Document the *why* (why this size, why this region); self-documenting code + short README beats a 50-page manual.

### 2.4.10 Formats — JSON vs YAML
- **JSON** is strict: braces `{}`, brackets `[]`, double-quoted keys/strings, commas, NO comments, indentation irrelevant — great for machines/APIs.
- **YAML** is human-friendly: indentation for structure, `#` comments, single or double quotes, far easier to review.
- Modern IaC (Terraform, Kubernetes, Ansible, CloudFormation YAML) prefers YAML; many tools also accept JSON.
- Gotcha: YAML indentation sensitivity causes most syntax errors; JSON is comment-free and brace-delimited.

### 2.4.11 Repeatability
- Same code yields the same environment every time with no manual steps — the whole point of IaC.
- Eliminates "snowflake" servers; environments become disposable (destroy/recreate prod-like staging on demand).
- Depends on: parameterizing differences via variables, no hard-coded secrets/IDs, idempotent tools.
- Fix for "inconsistent environments / we forgot how we built it" = codify it for repeatability.

### 2.4.12 Drift Detection
- Drift = gap between what code *says* should exist and what *actually* exists after manual/console/script changes.
- Drift detection compares live state to the code/template and reports differences.
- CloudFormation has built-in Drift Detection; Terraform `terraform plan` + external scanners; Ansible can remediate by re-applying.
- Drift is bad (wrong source of truth, untracked changes, uncertain recovery) — detect routinely and reconcile.

### 2.4.13 Versioning
- Tracks every change to IaC/CaC code over time, almost always with Git (each commit is a version; tag, branch, roll back).
- Gives an audit trail ("who changed the SG and when?"), bad-deploy revert, safe PR collaboration.
- Also version templates (semver) and infrastructure state (remote, locked Terraform state files).
- Links to change management/rollback: if prod breaks, restore the last known-good version from version control.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Terraform standing up a full VPC + EC2 fleet.** One `main.tf` with variables for region and instance count builds identical dev/staging/prod networks across three AWS accounts by swapping inputs — no console clicking, fully repeatable, all in Git.
- **CloudFormation template for a three-tier web app.** An AWS-native team declares VPC, subnets, ALB, Auto Scaling group, and RDS in YAML; wires resources with `!Ref`/`!GetAtt` and previews edits with a Change Set — declarative IaC with built-in drift detection.
- **Ansible playbook hardening 500 Linux servers.** After Terraform builds the VMs (IaC), an Ansible playbook (CaC) installs patches, sets SSH config, enforces firewall rules; idempotency safely converges any drifted server back to desired state.
- **Drift caught by a weekly CloudFormation drift scan.** Someone opened port 22 in the console during an incident and forgot; the drift report flags the mismatch, the team re-applies the template to close it and adds a guardrail against console changes.
- **Git-tagged rollback after a bad deploy.** A committed Terraform change accidentally downsized the database tier; with every change versioned in Git + PR review, on-call reverts to the previous tagged version and re-applies, restoring service in minutes.

---

## SECTION 4 — COMPARISON TABLES

| Concept | Infrastructure as Code (IaC) | Configuration as Code (CaC) |
|---|---|---|
| What it creates | The resources themselves (VMs, networks, buckets) | The settings/state on existing resources |
| Question it answers | "What should I build?" | "How should it be configured?" |
| Typical tools | Terraform, CloudFormation, ARM/Bicep, Pulumi | Ansible, Chef, Puppet, SaltStack |
| Phase | Provisioning (day 0) | Configuration (day 1+) |
| Key property | Repeatable environment creation | Idempotent state enforcement |

| Concept | JSON | YAML |
|---|---|---|
| Syntax style | Braces `{}`, brackets `[]`, commas | Indentation-based, dashes for lists |
| Comments | Not supported | Supported with `#` |
| Human readability | Lower (verbose, strict) | Higher (clean, comment-friendly) |
| Quoting | Double quotes required | Single or double allowed |
| Common use | API payloads, strict machine interchange | Authoring IaC, K8s, Ansible |

| Concept | Declarative IaC | Imperative / Procedural |
|---|---|---|
| What you write | Desired end-state | Step-by-step commands |
| Tool figures out how | Yes (the engine) | No (you do) |
| Examples | Terraform, CloudFormation, ARM | Bash scripts, AWS CLI, ordered playbooks |
| Easier to reason about | Usually, at scale | Easier for simple one-off tasks |

| Concept | Drift Detection | Versioning |
|---|---|---|
| Purpose | Find gaps between code and live state | Track changes to code over time |
| Answers | "Did reality diverge from the blueprint?" | "What changed, when, and by whom?" |
| Tools | CloudFormation Drift, `terraform plan`, scanners | Git, tags, branches, state files |
| Response | Reconcile code to reality or re-apply | Roll back to last known-good version |

| Concept | Testing (pre-apply) | Documentation |
|---|---|---|
| Goal | Catch errors before they hit prod | Explain code so others can use/rebuild it |
| Examples | lint, validate, plan, change set, policy scan | README, commented templates, runbooks |
| Prevents | Bad deploys, cost/security surprises | Knowledge loss, unreproducible builds |

---

## SECTION 5 — AWS PROVIDER MAPPING

**Objective 2.4 → AWS services & features (use code to deploy/configure):**

- **CloudFormation** — AWS-native declarative IaC. Write templates in JSON or YAML; use `!Ref`, `!GetAtt`, `!Sub`, `Conditions` (conditionals), and intrinsic functions. Supports **Change Sets** (testing/preview) and built-in **Drift Detection**.
- **AWS CLI / AWS CDK** — Imperative/procedural deployment. CLI runs ordered commands (scripting logic: variables via `--region`, conditionals in shell); CDK lets you define IaC in real programming languages (functions, data types, operators).
- **AWS SDK (boto3, AWS SDK for Go/Java/JS)** — Programmatic provisioning inside your own scripts/apps; full access to variables, conditionals, operators, functions, and data types.
- **AWS Systems Manager (SSM)** — CaC / configuration management. Use **SSM Documents** (JSON/YAML) and **State Manager** to enforce desired configuration across fleets idempotently; **Run Command** executes scripts.
- **AWS Config** — Complements drift detection by continuously recording resource configuration and evaluating compliance (catches configuration drift).
- **CodePipeline / CodeCommit / CodeBuild** — CI/CD that runs lint → plan → apply and stores versioned IaC in Git (CodeCommit) for review and rollback.
- **Terraform on AWS** (third-party, cloud-agnostic) — Also maps here: `terraform plan` = testing, `terraform apply` = repeatable deploy, state file = versioned source of truth.
- **Format note:** CloudFormation, SSM Documents, and SDK request bodies all accept **JSON** and/or **YAML** — know the JSON-vs-YAML trade-offs from SECTION 2.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which approach describes the *desired end-state* and lets the tool figure out how to achieve it?
   A. Imperative scripting  B. Declarative IaC  C. Manual console build  D. Configuration drift

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Declarative IaC states the desired end-state and the engine computes the steps (Terraform/CloudFormation).
> **Why A is wrong:** Imperative scripting lists step-by-step commands you must order yourself.
> **Why C is wrong:** Manual console build isn't code-based and isn't repeatable.
> **Why D is wrong:** Configuration drift is a divergence problem, not a provisioning approach.

2. What is the primary difference between IaC and CaC?
   A. IaC configures OS settings; CaC builds networks  B. IaC provisions resources; CaC configures them
   C. CaC is only for AWS  D. IaC cannot be versioned

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** IaC provisions the resources themselves; CaC applies configuration/state on top of existing resources.
> **Why A is wrong:** Reversed — IaC builds resources, CaC configures them.
> **Why C is wrong:** CaC tools (Ansible/Chef/Puppet) are cloud-agnostic, not AWS-only.
> **Why D is wrong:** IaC is versioned in Git like any code.

3. In a deployment template, `region = "us-east-1"` is an example of a:
   A. Function  B. Conditional  C. Variable  D. Data type

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** It's a variable that parameterizes a value so the same code works in other regions.
> **Why A is wrong:** A function computes a value; this just assigns a literal.
> **Why B is wrong:** A conditional branches logic; this is a plain assignment.
> **Why D is wrong:** It's an instance of a data type (string), not the type definition itself.

4. You want "deploy 3 instances if environment is prod, else 1." Which scripting element handles this?
   A. Operator  B. Conditional  C. Data type  D. Function

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Conditionals (`if/else`, `when`, `!If`) branch on the environment value.
> **Why A is wrong:** Operators compare/compute but don't branch top-level logic.
> **Why C is wrong:** Data types are the shape of values, not branching logic.
> **Why D is wrong:** Functions return computed values; branching is done by conditionals.

5. The expression `instance_count + 1` uses which scripting element?
   A. Arithmetic operator  B. Conditional  C. Variable declaration  D. Function call

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** The `+` is an arithmetic operator adding a buffer instance to the count.
> **Why B is wrong:** There's no branch in `instance_count + 1`.
> **Why C is wrong:** The variable is referenced, not declared, here.
> **Why D is wrong:** This is an operator expression, not a function invocation.

6. Which data type is represented by `[web1, web2, web3]`?
   A. Map  B. String  C. List/array  D. Boolean

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Square brackets denote an ordered list/array of values.
> **Why A is wrong:** A map uses `{key: value}` braces, not brackets.
> **Why B is wrong:** A string is quoted text, not a bracketed sequence.
> **Why D is wrong:** A boolean is `true`/`false`, not a list.

7. In CloudFormation, which intrinsic function returns another resource's ID?
   A. !GetAtt  B. !Ref  C. !Sub  D. !Join

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** `!Ref` references a resource or parameter by name and returns its ID/value.
> **Why A is wrong:** `!GetAtt` returns an attribute of a resource, not its ID.
> **Why C is wrong:** `!Sub` substitutes variables into a string, doesn't return an ID.
> **Why D is wrong:** `!Join` concatenates a list into a string.

8. Before applying a Terraform change to prod, the BEST first step is:
   A. Delete the state file  B. Run `terraform plan`  C. Edit live console  D. Skip review

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** `terraform plan` previews changes (testing) so you catch errors before applying.
> **Why A is wrong:** Deleting state loses the source of truth and breaks tracking.
> **Why C is wrong:** Editing the live console causes drift and isn't a safe pre-apply step.
> **Why D is wrong:** Skipping review risks bad/undetected changes reaching prod.

9. YAML is preferred over JSON for authoring IaC mainly because it:
   A. Is faster at runtime  B. Supports comments and is more readable  C. Requires no indentation  D. Cannot contain lists

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** YAML's `#` comments and indentation make it human-friendly and easier to review.
> **Why A is wrong:** Neither format's authoring syntax affects runtime speed meaningfully.
> **Why C is wrong:** YAML is indentation-sensitive; it requires correct indentation.
> **Why D is wrong:** YAML fully supports lists (with dashes).

10. A misplaced space in a YAML file most commonly causes:
    A. A security breach  B. An indentation/syntax error  C. Drift  D. Version conflict

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** YAML is indentation-sensitive, so a misplaced space breaks parsing (syntax error).
> **Why A is wrong:** A spacing bug is a parse error, not directly a security breach.
> **Why C is wrong:** Drift is live-vs-code divergence, not a file syntax issue.
> **Why D is wrong:** Version conflicts come from Git, not YAML whitespace.

11. "Running the same template always creates the same environment with no manual steps" describes:
    A. Drift  B. Repeatability  C. Versioning  D. Documentation

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Repeatability means identical output from the same code every time, with no manual steps.
> **Why A is wrong:** Drift is the opposite — divergence from the template.
> **Why C is wrong:** Versioning tracks code changes over time, not environment sameness.
> **Why D is wrong:** Documentation explains the code; it doesn't guarantee identical output.

12. A security group was opened in the console and the template no longer matches reality. This is:
    A. Repeatability  B. Drift  C. Idempotency  D. Linting

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Manual console changes that diverge from the code are configuration drift.
> **Why A is wrong:** Repeatability is about code producing the same result, not divergence.
> **Why C is wrong:** Idempotency is safe re-application, not divergence.
> **Why D is wrong:** Linting checks syntax, not live-vs-code mismatch.

13. Which AWS feature directly detects drift in CloudFormation stacks?
    A. AWS Config  B. CloudFormation Drift Detection  C. IAM  D. S3 Versioning

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CloudFormation has built-in Drift Detection that compares stack resources to the template.
> **Why A is wrong:** AWS Config records config continuously but isn't the stack drift tool itself.
> **Why C is wrong:** IAM is access management, unrelated to drift detection.
> **Why D is wrong:** S3 Versioning tracks object versions, not infra drift.

14. Storing IaC in Git with tagged releases supports which practice?
    A. Drift detection  B. Versioning  C. Conditionals  D. JSON formatting

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Git tags/commits give versioning — an audit trail and safe rollback to known-good code.
> **Why A is wrong:** Drift detection is a separate live-vs-code comparison, not Git tagging.
> **Why C is wrong:** Conditionals are template logic, not a Git practice.
> **Why D is wrong:** Format choice is independent of version control.

15. An Ansible playbook that can be safely re-run without harmful side effects demonstrates:
    A. Drift  B. Idempotency  C. Variables  D. YAML errors

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Idempotent CaC converges state safely on every run — same result, no harm.
> **Why A is wrong:** Drift is divergence, not safe re-runs.
> **Why C is wrong:** Variables enable reuse but don't define safe re-runs.
> **Why D is wrong:** YAML errors would break the run, the opposite of safe execution.

16. In JSON, which statement is true?
    A. Comments are allowed with `#`  B. Keys need no quotes  C. No comments; braces delimit  D. Indentation defines structure

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** JSON is brace-delimited (`{}`/`[]`) and does not support comments; indentation is cosmetic.
> **Why A is wrong:** JSON has no comment syntax (unlike YAML's `#`).
> **Why B is wrong:** JSON keys must be double-quoted.
> **Why D is wrong:** Indentation is irrelevant in JSON; braces/brackets define structure.

17. Using `when: public == true` in Ansible is an example of a:
    A. Variable  B. Conditional  C. Function  D. Data type

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** `when` is a conditional that gates whether a task executes.
> **Why A is wrong:** The `public` is the variable; `when` is the gate using it.
> **Why C is wrong:** It's a condition, not a function call computing a value.
> **Why D is wrong:** `true` is a boolean data type, but the construct is a conditional.

18. Which tool is BEST described as Configuration as Code for fleets of servers?
    A. Terraform  B. Ansible  C. CloudFormation  D. S3

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Ansible is a classic CaC/config-management tool that enforces desired state across fleets.
> **Why A is wrong:** Terraform is primarily IaC (provisioning), not config management.
> **Why C is wrong:** CloudFormation is IaC (provisioning), not fleet config management.
> **Why D is wrong:** S3 is object storage, not a config tool.

19. After a bad deploy, reverting to the previous tagged code version is an example of:
    A. Documentation  B. Rollback via versioning  C. Drift  D. Testing

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Versioning (Git tags/commits) enables safe rollback to a last known-good state.
> **Why A is wrong:** Documentation explains code; it isn't rollback.
> **Why C is wrong:** Drift is live-vs-code divergence, not a revert action.
> **Why D is wrong:** Testing validates before apply, not rollback after.

20. SSM Documents used by State Manager to enforce server config are written in:
    A. Only XML  B. JSON or YAML  C. Only Python  D. Binary

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** SSM Documents are authored in JSON or YAML — flexible, human-readable formats.
> **Why A is wrong:** SSM Documents are not XML-only.
> **Why C is wrong:** They are JSON/YAML, not Python-only.
> **Why D is wrong:** They are text (JSON/YAML), not binary.
