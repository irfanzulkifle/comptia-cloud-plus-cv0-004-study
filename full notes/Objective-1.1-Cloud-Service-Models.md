# Objective 1.1 — Given a scenario, use the appropriate cloud service model

## SECTION 1 — Exam Objectives

- **Objective:** 1.1 — Given a scenario, use the appropriate cloud service model.
- **Sub-objectives:**
  - 1.1.a — Cloud service models: Infrastructure as a Service (IaaS)
  - 1.1.b — Cloud service models: Platform as a Service (PaaS)
  - 1.1.c — Cloud service models: Software as a Service (SaaS)
  - 1.1.d — Cloud service models: Function as a Service (FaaS)
  - 1.1.e — Shared responsibility model
- **The four models as a ladder (who manages what):**
  - IaaS (bottom): provider gives raw VMs; you manage the OS + everything above.
  - PaaS (middle): provider gives a managed platform; you write code + manage data.
  - SaaS (top): provider gives a finished app; you just use it.
  - FaaS (most granular): provider runs single functions on demand; you pay per execution.
- **Shared responsibility model:** the dividing line between "security OF the cloud" (provider) and "security IN the cloud" (customer); shifts by model.
- **Why it matters:** CompTIA gives you a short business story and you must pick the fitting model + know who owns each layer.
- **How CompTIA tests it:** scenario-based — never "define the term," always "which model fits this situation?"

## SECTION 2 — EXAM KNOWLEDGE

### Infrastructure as a Service (IaaS)
- **Definition:** Most basic model. Provider owns physical DC, servers, network, storage, hypervisor. Customer rents virtual compute/storage/network and controls the OS, middleware, runtime, data, apps.
- **Why it matters:** Max flexibility — run almost any workload (legacy apps, DBs, containers) like on own hardware. Easiest lift-and-shift. Exam pick when scenario stresses control, customisation, "we manage our own servers but not the DC."
- **Analogy:** Renting an unfurnished apartment — landlord supplies building + utilities, you bring furniture and decide how to live.
- **Example:** A Malaysian bank runs a regulated core-banking app on EC2: installs its own Linux + DB + app, patches the guest OS; AWS maintains physical servers.

### Platform as a Service (PaaS)
- **Definition:** One level above IaaS. Provider manages infra AND runtime (OS, web server, language runtimes, middleware, often DB). Customer only writes/deploys code + manages data. No OS patching, no server management.
- **Why it matters:** Fast dev, smaller ops team. Exam pick for "focus on code," "managed DB," "rapid deployment," "don't want to patch servers." Trade-off: less control + possible vendor lock-in.
- **Analogy:** Renting a fully equipped commercial kitchen — landlord gives stove/utensils/gas; you bring ingredients + recipes.
- **Example:** A KL e-commerce startup deploys Python to Elastic Beanstalk + managed Postgres; provider handles scaling, patching, LB. Devs ship weekly.

### Software as a Service (SaaS)
- **Definition:** Top of stack. Provider delivers a ready app over the web; customer manages only data + limited config (users/settings). No access to underlying servers. Per-user/month billing.
- **Why it matters:** Least effort, least in-house skill — ideal for non-technical users/SMEs. Exam pick for "just use the software," "browser access," "subscription per user," email/collab/off-the-shelf apps. Downside: minimal customisation, data lives with provider.
- **Analogy:** Eating at a restaurant — chef cooks/serves/cleans; you just eat and pay.
- **Example:** A Penang accounting firm uses Google Workspace + Xero via browser; never sees a server, never patches, pays per user.

### Function as a Service (FaaS)
- **Definition:** Most granular model ("serverless"). Provider runs individual functions on demand, charges only for execution time. No always-on servers. Customer writes stateless functions + wires triggers (HTTP, file upload, DB change). Provider handles OS-down + auto-scaling to zero.
- **Why it matters:** Perfect for event-driven/bursty/sporadic workloads — pay only for use, instant scaling. Exam pick for "event-driven," "pay per execution," "no servers," "bursty." Challenge: stateless + cold-start latency.
- **Analogy:** A taxi you summon only when needed — appears on call, no idle cost between rides.
- **Example:** Food-delivery app deploys a Lambda triggered by S3 upload to make thumbnails; pays fractions of a cent/run, no server to manage.

### Shared Responsibility Model
- **Definition:** Security/management duties split between provider and customer.
  - **Provider** = "security OF the cloud": physical DCs, hardware, network, hypervisor/host.
  - **Customer** = "security IN the cloud": data, IAM, guest OS (IaaS), network controls, apps/code.
  - Boundary shifts by model: higher abstraction (SaaS) → more on provider; lower (IaaS) → more on customer.
- **Why it matters:** Single most-tested cloud-security concept + common costly mistake. "The cloud is secure" ≠ "my data is safe." Map per model: IaaS customer patches OS; PaaS provider patches OS but customer secures app+data; SaaS provider secures almost everything, customer manages access + data content.
- **Analogy:** Rented guarded apartment block — management secures perimeter/structure; you lock your own unit + choose who gets a key.
- **Example:** Clinic stores records in a cloud DB but leaves it public — breach is the clinic's fault (access config = customer side), not the provider's.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Uni research lab (IaaS):** Needs custom-configured VMs + HPC filesystem for physics sims. Rents IaaS VMs + block storage, installs own Linux/MPI stack, shuts down when idle. Full control, no CapEx.
- **Scenario 2 — Fintech customer portal (PaaS):** 4 devs, no ops engineer, 3-month launch. Pushes Node.js to a PaaS + managed DB + auth. Provider handles patching/LB/TLS/auto-scaling. Ships fast, accepts some lock-in.
- **Scenario 3 — SME office/accounting (SaaS):** 15-person firm uses Microsoft 365 + Canva + QuickBooks Online. Zero servers, auto-updates, per-user fee. Trades customisation for zero maintenance.
- **Scenario 4 — E-commerce image processing (FaaS):** Thousands of photos/day. Serverless function triggers on upload → validates, resizes, writes metadata. Sub-second runs, auto-scales, pays per execution.
- **Scenario 5 — Misconfig breach (all models):** Clinic leaves a cloud DB open to the internet. Provider infra was secure; breach = clinic's shared-responsibility failure (access config). No model removes the customer's duty to protect data/identities/configs.

---

## SECTION 4 — COMPARISON TABLES

**Table 1 — Responsibility split by service model**

| Layer / Duty | IaaS | PaaS | SaaS | FaaS |
|--------------|------|------|------|------|
| Physical data centre, hardware | Provider | Provider | Provider | Provider |
| Network infrastructure & host | Provider | Provider | Provider | Provider |
| Virtualisation / runtime | Provider | Provider | Provider | Provider |
| Guest operating system | **Customer** | Provider | Provider | Provider |
| Middleware / runtime env | **Customer** | Provider | Provider | Provider |
| Application code | **Customer** | **Customer** | Provider | **Customer** |
| Data & user access | **Customer** | **Customer** | **Customer** | **Customer** |
| Scaling management | Customer (manual/auto) | Provider (automatic) | Provider (automatic) | Provider (automatic, to zero) |

**Table 2 — IaaS vs PaaS vs SaaS vs FaaS at a glance**

| Attribute | IaaS | PaaS | SaaS | FaaS |
|-----------|------|------|------|------|
| What you get | VMs, storage, networks | Dev/runtime platform | Finished app | Single functions |
| What you manage | OS to app | App & data | Data & users | Code only |
| Typical user | Sysadmins, IT ops | Developers | End users | Developers |
| Customisation | Highest | Medium | Lowest | Medium (code) |
| Management effort | Highest | Medium | Lowest | Lowest |
| Billing model | Per resource/hour | Per resource + platform | Per user/month | Per execution/ms |

**Table 3 — When to choose which model (exam decision aid)**

| Scenario clue | Best model |
|---------------|------------|
| "Lift-and-shift," full control, legacy app | IaaS |
| "Focus on code," managed DB, fast dev | PaaS |
| "Just use the software," browser, per-user fee | SaaS |
| "Event-driven," pay per run, bursty | FaaS |
| "We don't want to patch servers" | PaaS or SaaS |
| "We must control the OS and hardening" | IaaS |

**Table 4 — Shared responsibility: provider vs customer**

| Provider responsible (security OF cloud) | Customer responsible (security IN cloud) |
|------------------------------------------|------------------------------------------|
| Physical security of data centres | Customer data and encryption keys |
| Hardware, storage, network gear | Guest OS patches (IaaS) and configurations |
| Hypervisor and host infrastructure | Identity, access, and user management |
| Service availability and redundancy | Network traffic rules (security groups, firewalls) |
| Managed-service patching (PaaS/SaaS/FaaS) | Application code and its vulnerabilities |

**Table 5 — Abstraction ladder (who does more work)**

| Model | Provider effort | Customer effort | Control |
|-------|-----------------|-----------------|---------|
| IaaS | Low | High | High |
| PaaS | Medium | Medium | Medium |
| FaaS | High | Low | Low (code only) |
| SaaS | Highest | Lowest | Lowest |

**Table 6 — Common exam examples per model**

| Model | Typical examples |
|-------|------------------|
| IaaS | AWS EC2, Azure VMs, Google Compute Engine, bare-metal cloud |
| PaaS | AWS Elastic Beanstalk, Heroku, Google App Engine, Azure App Service |
| SaaS | Google Workspace, Microsoft 365, Salesforce, Zoom, Dropbox |
| FaaS | AWS Lambda, Azure Functions, Google Cloud Functions |

---

## SECTION 5 — AWS PROVIDER MAPPING

This section maps each Objective 1.1 concept to a concrete, real AWS service or model. AWS is used here as the reference provider because its shared responsibility model is the clearest published example and matches the exam's vendor-neutral logic.

| Objective 1.1 concept | Real AWS service / model | Notes |
|-----------------------|--------------------------|-------|
| IaaS | **Amazon EC2** (Elastic Compute Cloud) + **Amazon EBS** + **Amazon VPC** | You get virtual servers, block storage, and isolated networks; you manage the guest OS, patches, and apps. |
| PaaS | **AWS Elastic Beanstalk** + **AWS RDS** (managed database) | You upload code; AWS provisions, patches, and scales the platform and runtime. |
| SaaS | **Amazon WorkSpaces** (virtual desktop), **AWS Managed Microsoft AD**, plus third-party SaaS on AWS Marketplace | Finished applications delivered over the network; AWS runs the stack. |
| FaaS | **AWS Lambda** | Run code on event triggers, pay per millisecond, no servers to manage. |
| Shared responsibility | **AWS Shared Responsibility Model** | AWS secures "the cloud" (infrastructure); the customer secures "in the cloud" (data, OS for IaaS, IAM, apps). |

Additional AWS mappings that reinforce the model boundaries:

- **IaaS supporting services:** Amazon S3 (object storage, customer configures bucket policy), Amazon VPC (customer designs subnets, route tables, security groups).
- **PaaS supporting services:** AWS Lambda (also FaaS), Amazon ECS/EKS with Fargate (managed container platform — PaaS-leaning), AWS App Runner.
- **SaaS examples on AWS:** Amazon Chime, Amazon Connect (cloud contact centre), and countless ISV apps via AWS Marketplace.
- **Shared responsibility nuance:** For Lambda/FaaS and SaaS, AWS patches the underlying OS and runtime; the customer is responsible only for code (FaaS) or data and user access (SaaS). For EC2/IaaS, the customer must patch the guest OS — a frequent exam trap.

Key exam takeaway: the same provider (AWS) offers all four models, and the shared responsibility line moves upward as you move up the stack. Knowing EC2=IaaS, Elastic Beanstalk=PaaS, WorkSpaces=SaaS, Lambda=FaaS, and the AWS Shared Responsibility Model as the umbrella is enough to answer every AWS-flavoured 1.1 question.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A company wants to run its own custom Linux servers in the cloud but does not want to buy physical hardware. Which model fits best?
   A. SaaS
   B. PaaS
   C. IaaS
   D. FaaS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** IaaS gives the customer control of the OS and applications while the provider supplies the virtualised hardware.
> **Why A is wrong:** SaaS is a finished app you just use, with no custom OS control.
> **Why B is wrong:** PaaS manages the runtime; you do not control the underlying OS.
> **Why D is wrong:** FaaS runs single functions on demand, not full custom servers.

2. Developers want to deploy code without managing operating systems or servers. Which model should they choose?
   A. IaaS
   B. PaaS
   C. SaaS
   D. FaaS (only if event-driven)

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** PaaS provides a managed runtime so developers focus on code, not server patching.
> **Why A is wrong:** IaaS makes the customer manage the OS and servers.
> **Why C is wrong:** SaaS is a ready app, not a development platform.
> **Why D is wrong:** FaaS is event-driven functions, not the general build-and-deploy answer.

3. A user accesses email and documents through a browser for a monthly per-user fee. This is an example of:
   A. IaaS
   B. PaaS
   C. SaaS
   D. FaaS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** SaaS delivers a finished application consumed via browser on a subscription basis.
> **Why A is wrong:** IaaS is raw VMs, not a browser app.
> **Why B is wrong:** PaaS is a build platform, not a finished app.
> **Why D is wrong:** FaaS runs functions, not a full application.

4. Which model charges only for the time code actually executes?
   A. IaaS
   B. PaaS
   C. SaaS
   D. FaaS

> [!note]- Reveal Answer
> **Correct: D**
> **Why correct:** FaaS (serverless) bills per execution and scales to zero when idle.
> **Why A is wrong:** IaaS bills for running resources by the hour.
> **Why B is wrong:** PaaS bills for the platform/runtime, not per execution.
> **Why C is wrong:** SaaS bills per user per month.

5. Under the shared responsibility model, who is responsible for patching the guest operating system on an IaaS virtual machine?
   A. The cloud provider
   B. The customer
   C. A third-party regulator
   D. No one

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** In IaaS the customer manages the guest OS, while the provider secures the host.
> **Why A is wrong:** The provider secures the host, not the guest OS.
> **Why C is wrong:** Regulators do not patch your virtual machines.
> **Why D is wrong:** Someone must patch; it is the customer's responsibility.

6. In a SaaS arrangement, who is primarily responsible for securing the application code?
   A. The customer
   B. The provider
   C. The internet service provider
   D. Shared equally

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** With SaaS the provider manages the full stack including application code.
> **Why A is wrong:** The customer only manages data and users in SaaS.
> **Why C is wrong:** The ISP has no role in application security.
> **Why D is wrong:** It is not shared equally; the provider owns the stack.

7. A retailer resizes uploaded images automatically whenever a file arrives, with no servers running between uploads. This is:
   A. IaaS
   B. PaaS
   C. FaaS
   D. SaaS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Event-triggered, stateless, pay-per-run image processing is a classic FaaS use case.
> **Why A is wrong:** IaaS would need always-on servers.
> **Why B is wrong:** PaaS is not per-file event-triggered by default.
> **Why D is wrong:** SaaS is a finished app, not a custom processor.

8. Which characteristic is MOST associated with IaaS?
   A. Zero customer management
   B. Maximum customer control over the stack
   C. Per-user subscription billing
   D. Finished application delivered

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** IaaS offers the highest control because the customer manages from the OS upward.
> **Why A is wrong:** Zero management describes SaaS, not IaaS.
> **Why C is wrong:** Per-user subscription is SaaS billing.
> **Why D is wrong:** A finished application is SaaS.

9. Under shared responsibility, "security OF the cloud" refers to:
   A. The customer's data
   B. The provider's physical and infrastructure layer
   C. The customer's passwords
   D. Application logic

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** The provider secures the data centres, hardware, and host infrastructure.
> **Why A is wrong:** Customer data is the customer's 'security IN the cloud' duty.
> **Why C is wrong:** Passwords are the customer's responsibility.
> **Why D is wrong:** Application logic is the customer's (or SaaS provider's) area.

10. A startup wants to build an app fast with a managed database and no ops team. Best choice:
    A. IaaS
    B. PaaS
    C. SaaS
    D. FaaS

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** PaaS pairs a managed runtime and database with developer-focused deployment.
> **Why A is wrong:** IaaS needs an ops team for the OS.
> **Why C is wrong:** SaaS is off-the-shelf, not a custom app build.
> **Why D is wrong:** FaaS is event-driven functions, not a typical app build.

11. Which is a typical IaaS example?
    A. Google Workspace
    B. AWS Lambda
    C. Amazon EC2
    D. Salesforce

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** EC2 provides virtual machines, the defining IaaS building block.
> **Why A is wrong:** Google Workspace is SaaS.
> **Why B is wrong:** Lambda is FaaS.
> **Why D is wrong:** Salesforce is SaaS.

12. Which is a typical PaaS example?
    A. Microsoft 365
    B. AWS Elastic Beanstalk
    C. Amazon S3
    D. AWS Lambda

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Elastic Beanstalk manages the platform so developers deploy code directly.
> **Why A is wrong:** Microsoft 365 is SaaS.
> **Why C is wrong:** S3 is object storage (IaaS-level).
> **Why D is wrong:** Lambda is FaaS.

13. Which is a typical SaaS example?
    A. Azure Virtual Machines
    B. Google App Engine
    C. Salesforce
    D. AWS Lambda

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Salesforce is a ready-to-use application delivered over the web.
> **Why A is wrong:** Azure VMs are IaaS.
> **Why B is wrong:** App Engine is PaaS.
> **Why D is wrong:** Lambda is FaaS.

14. Which is a typical FaaS example?
    A. Amazon EC2
    B. Heroku
    C. AWS Lambda
    D. Dropbox

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Lambda runs individual functions on demand, the essence of FaaS.
> **Why A is wrong:** EC2 is IaaS.
> **Why B is wrong:** Heroku is PaaS.
> **Why D is wrong:** Dropbox is SaaS.

15. As you move UP the service-model stack (IaaS→PaaS→SaaS), the customer's management effort generally:
    A. Increases
    B. Decreases
    C. Stays the same
    D. Disappears entirely for IaaS

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Higher abstraction means the provider takes on more management.
> **Why A is wrong:** Effort decreases, not increases.
> **Why C is wrong:** It does change across the stack.
> **Why D is wrong:** Management is highest at IaaS, not absent.

16. A company leaves a cloud storage bucket publicly readable and suffers a leak. Whose responsibility is the misconfiguration?
    A. The provider's, always
    B. The customer's, because access config is their duty
    C. The government's
    D. Neither

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Configuring data access is the customer's side of shared responsibility.
> **Why A is wrong:** The provider supplies secure infra, not access config.
> **Why C is wrong:** Government is not responsible for your bucket config.
> **Why D is wrong:** It is someone's (the customer's) responsibility.

17. For FaaS, which of the following is the customer still responsible for?
    A. The runtime environment
    B. The application code and its triggers
    C. The physical servers
    D. The operating system

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** The customer writes the function code; the provider manages everything below.
> **Why A is wrong:** The runtime is managed by the provider.
> **Why C is wrong:** Physical servers are the provider's.
> **Why D is wrong:** The OS is the provider's in FaaS.

18. The BEST match for "lift-and-shift migration of a legacy app with OS control" is:
    A. SaaS
    B. PaaS
    C. IaaS
    D. FaaS

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Lift-and-shift with full OS control maps directly to IaaS.
> **Why A is wrong:** SaaS gives no OS control.
> **Why B is wrong:** PaaS hides the OS.
> **Why D is wrong:** FaaS has no persistent OS.

19. Under shared responsibility for PaaS, the provider patches the OS but the customer must still secure:
    A. The physical data centre
    B. The application and its data
    C. The hypervisor
    D. The network backbone

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** In PaaS the customer owns app-level security and data, not the platform.
> **Why A is wrong:** The data centre is the provider's.
> **Why C is wrong:** The hypervisor is the provider's.
> **Why D is wrong:** The network backbone is the provider's.

20. What does the AWS Shared Responsibility Model describe?
    A. How AWS splits costs with customers
    B. The division of security duties between AWS and the customer
    C. Which regions AWS operates in
    D. How to cancel an AWS subscription

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** It defines what AWS secures versus what the customer must secure.
> **Why A is wrong:** It is about security duties, not a cost split.
> **Why C is wrong:** It does not describe AWS regions.
> **Why D is wrong:** It is not about subscription cancellation.
