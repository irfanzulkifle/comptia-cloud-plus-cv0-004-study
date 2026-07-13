# CompTIA Cloud+ CV0-004 — Domain 1.0 Cloud Architecture
## 📘 Objective 1.1: Cloud Service Models & Shared Responsibility

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 1.0 Domain = 23% of total exam
> **Objective 1.1 Focus:** Selecting the correct cloud service model for a given scenario, plus understanding who is responsible for what in the cloud.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 1.1
### Objective Name: *Given a scenario, use the appropriate cloud service model.*

**What this objective means (beginner-friendly):**
Imagine you're renting space for a business. You can rent:
1. **Just the building** and bring your own furniture, electricity, internet (that's like IaaS).
2. **A building with utilities, desks, and a receptionist** but you bring your own software (PaaS).
3. **A fully furnished office** where you just walk in and start working (SaaS).
4. **Pay-per-use meeting rooms** that only exist when you book them (FaaS).

The exam will give you a scenario — a company description with requirements — and ask you to pick the right model. You also need to know who (you or the cloud provider) is responsible for security, patching, OS, network, data, etc. That's the **shared responsibility model**.

**Why it matters in the real world:**
- Wrong model = overspending (over-provisioning) or under-support (can't customize).
- Misunderstanding shared responsibility = data breaches (e.g., customer thinks AWS patches their OS — they don't for EC2 IaaS).
- Cloud architects must match workload to model: legacy lift-and-shift = IaaS, microservices = PaaS/Containers, event-driven = FaaS.

**Why CompTIA tests it:**
- It is the **#1 foundational decision** in any cloud engagement.
- 1.1 appears in almost every scenario-based question (PBQs and multiple-choice) across the entire exam.
- Misconceptions about shared responsibility cause real-world breaches — CompTIA validates you understand it.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Infrastructure as a Service (IaaS)

**Definition:**
The cloud provider supplies the **fundamental compute, storage, and networking building blocks** (virtualized hardware). You, the customer, install and manage the OS, middleware, runtime, data, and applications.

**Purpose:**
Gives you maximum control over the infrastructure without buying physical hardware. You rent virtual machines, virtual networks, and raw storage on demand.

**How it works:**
- Provider runs a hypervisor on physical servers in their data center.
- Provider carves out VMs (virtual machines) and assigns vCPU, RAM, virtual disks, virtual NICs to you.
- You get admin/root access to the VM, just like a bare-metal server.
- You install the OS (Windows Server, Linux), patch it, secure it, and deploy your apps.

**Advantages:**
- Highest level of control and customization.
- Lift-and-shift friendly — move existing on-prem workloads without redesign.
- Pay only for what you provision (per-second or per-hour billing).
- Supports any OS, any software, any configuration.

**Disadvantages:**
- You manage the most "stack" — more operational overhead.
- You're responsible for OS patching, security hardening, high availability, backups (unless you build them yourself).
- Slower to deploy than PaaS (you provision + install + configure).

**When to use:**
- Lift-and-shift migrations of legacy apps that need specific OS/dependency versions.
- Workloads requiring custom OS configuration (specific kernel modules, drivers, legacy software).
- Dev/test environments needing full control.
- Regulatory scenarios where you must control the entire stack.

**When NOT to use:**
- Stateless web APIs that could run serverless cheaper.
- Simple websites that PaaS or SaaS handles out-of-the-box.
- Short-lived event processing (FaaS is better fit).
- Teams without sysadmin capacity to patch and secure VMs.

---

### 2.2 — Platform as a Service (PaaS)

**Definition:**
The cloud provider supplies a **managed platform** — runtime, middleware, OS, and often the underlying infrastructure — so you can focus on deploying your **application code and data** only.

**Purpose:**
Removes the "undifferentiated heavy lifting" of managing servers, runtimes, and middleware. Faster to develop, deploy, and scale.

**How it works:**
- Provider provisions and maintains the host OS, runtime (e.g., Node.js, Java, Python, .NET), middleware (web server, application server), and sometimes the database engine.
- You push your application code (zip, container, or git push) and config.
- Provider handles scaling, patching, high availability of the platform.
- You consume the platform through a portal, CLI, or API.

**Advantages:**
- Dramatically faster time-to-market (no OS patching, runtime install).
- Built-in scalability, load balancing, and high availability.
- Reduced ops overhead — fewer sysadmins needed for routine tasks.
- Cost-effective for variable workloads (auto-scaling, pay per use).

**Disadvantages:**
- Less control over the underlying stack (can't install custom kernel drivers).
- Possible "vendor lock-in" — code may use provider-specific APIs.
- Costs can balloon with high sustained traffic vs. reserved IaaS.

**When to use:**
- Web apps, REST APIs, microservices.
- Continuous integration / continuous deployment (CI/CD) pipelines.
- Database hosting (managed Postgres, MySQL, SQL).
- Mobile app backends.

**When NOT to use:**
- Apps requiring specific OS customization (use IaaS).
- Strict data residency requiring physical hardware control.
- Legacy monoliths tightly coupled to a specific OS/middleware.
- Extremely cost-sensitive, predictable workloads where long-term reserved IaaS is cheaper.

---

### 2.3 — Software as a Service (SaaS)

**Definition:**
The cloud provider delivers a **complete, ready-to-use application** over the internet. You consume it; the provider manages everything underneath.

**Purpose:**
Eliminates the need to install, maintain, or even think about infrastructure and platform. You just log in and use the software.

**How it works:**
- Provider develops, secures, hosts, patches, and updates the application.
- Users access it via a web browser, mobile app, or API.
- Subscription or usage-based billing.
- Examples: Gmail, Microsoft 365, Salesforce, Dropbox, Slack, Zoom.

**Advantages:**
- Zero infrastructure to manage.
- Always on the latest version (provider updates automatically).
- Predictable subscription cost.
- Accessible from anywhere, any device.

**Disadvantages:**
- Minimal customization (you're stuck with the provider's feature set).
- Data resides with the provider — must trust their security, compliance, and uptime.
- Integration challenges with on-prem systems.
- Subscription costs compound over years.

**When to use:**
- Standard business functions (email, CRM, HR, collaboration, file storage).
- Companies that want to avoid capital expenditure on software licenses + hardware.
- Distributed teams needing anywhere access.

**When NOT to use:**
- Processes needing deep customization that no SaaS supports.
- Workloads with strict data sovereignty requirements that exclude public SaaS.
- Scenarios where total cost of ownership over 5+ years exceeds building it yourself.
- Highly regulated industries (healthcare, finance) where audit access is limited.

---

### 2.4 — Function as a Service (FaaS) / Serverless

**Definition:**
A cloud-native execution model where you upload **discrete functions** (small pieces of code) that run **only when triggered by an event**. The provider handles all servers, scaling, and idle time.

**Purpose:**
Event-driven, sub-second billing, infinite scale without capacity planning. You write code; the provider runs it on demand.

**How it works:**
- You write a function (e.g., Python, Node.js, Go) with a handler.
- You define a trigger (HTTP request, queue message, file upload, schedule, database change).
- Provider runs the function in an ephemeral container — usually starting in milliseconds.
- You pay only for the compute time consumed (rounded up to the nearest millisecond or 100 ms).
- Provider auto-scales from zero to thousands of concurrent executions.

**Advantages:**
- No servers to manage, ever.
- Pay only when code runs (zero cost when idle).
- Built-in high availability and fault tolerance.
- Auto-scales horizontally with no configuration.

**Disadvantages:**
- Cold-start latency (first request may take longer).
- Execution time limits (commonly 5–15 min per invocation).
- Stateless — state must live in external services (DB, cache).
- Vendor lock-in (function code often uses provider-specific event sources).
- Debugging and observability harder than traditional apps.

**When to use:**
- Event-driven workloads (image resize on upload, webhook handlers, IoT telemetry).
- Sporadic or unpredictable traffic patterns.
- Glue code between services (REST → DB write, queue → notification).
- Scheduled tasks (cron replacement).

**When NOT to use:**
- Long-running processes (over the timeout limit).
- Latency-sensitive apps that can't tolerate cold starts.
- Apps requiring persistent local state.
- Workloads with sustained high CPU usage (cheaper on IaaS/PaaS).

---

### 2.5 — Shared Responsibility Model

**Definition:**
A **security and operations framework** that defines which security tasks are the cloud **provider's** responsibility and which are the **customer's**, depending on the service model.

**Purpose:**
Removes ambiguity. Without it, gaps appear — and attackers love gaps.

**How it works:**
The deeper into the stack the provider goes (toward SaaS), the **less** you manage. The shallower (toward IaaS), the **more** you manage.

**Provider is always responsible for:**
- Security **of** the cloud (physical data centers, hardware, network infrastructure, host OS of the platform layer for PaaS/SaaS, hypervisor, global networking).

**Customer is always responsible for:**
- Security **in** the cloud (their data, identity/access management, which varies by model).

**Responsibility matrix (simplified):**

| Layer | On-Prem | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Data | You | **You** | You | You |
| Application | You | **You** | You | Provider |
| Runtime / Middleware | You | **You** | Provider | Provider |
| OS | You | **You** | Provider | Provider |
| Virtualization | You | Provider | Provider | Provider |
| Servers | You | Provider | Provider | Provider |
| Storage | You | Provider | Provider | Provider |
| Networking | You | Provider | Provider | Provider |
| Physical DC | You | Provider | Provider | Provider |

**Advantages:**
- Clear ownership prevents gaps.
- Provider invests billions in physical/global security you can't replicate.
- Customer retains control over their own data and access policies.

**Disadvantages:**
- Easy to misread — many customers assume provider handles more than it does (e.g., "AWS patches my EC2 OS" — false).
- Varies by service (e.g., RDS = provider patches DB engine; EC2 = you patch OS).
- Compliance audits still require customer to prove their controls.

**When to use (always):** In every cloud engagement, no exceptions.

**When NOT to use:** Never — misunderstanding it leads to breaches.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — IaaS in the Wild

**AWS Example — EC2 (Elastic Compute Cloud):**
- A bank migrating its 15-year-old core banking system from on-prem to AWS uses EC2 to replicate the existing VMware environment.
- They choose EC2 over RDS or Lambda because the legacy app requires a specific Windows Server 2008 configuration with custom COM+ components.
- AWS manages the physical server, hypervisor, and physical network. The bank's sysadmins RDP into the EC2 instance, manage Active Directory, install antivirus, and patch Windows themselves.
- This is the textbook IaaS pattern: lift-and-shift with maximum control.

**Azure Example — Azure Virtual Machines:**
- A healthcare company hosts an electronic medical records (EMR) application on Azure VMs.
- The app is HIPAA-regulated and must run on a hardened Linux distro with specific FIPS-compliant modules — only IaaS allows that level of OS control.
- They use Azure Disk Storage (Premium SSD) attached to the VMs and Azure Virtual Network for their private subnets.

**Google Cloud Example — Compute Engine:**
- A research lab runs genomics simulations on GCP Compute Engine with custom GPU drivers.
- GCP manages the host hardware, but the lab's HPC team installs their own NVIDIA CUDA toolkit version and tunes the OS kernel for MPI workloads.
- They use Preemptible VMs (now Spot VMs) to slash costs since the simulations are fault-tolerant.

---

### 3.2 — PaaS in the Wild

**AWS Example — Elastic Beanstalk / RDS / Lambda (managed):**
- A startup builds a Node.js REST API.
- They deploy to **Elastic Beanstalk** (PaaS) — they push their code via `git push`, and AWS provisions the EC2, load balancer, auto-scaling, and monitoring.
- Their database sits on **RDS (Relational Database Service)** — AWS handles the MySQL engine patching, backups, and Multi-AZ failover.
- The team's two developers never log into a server. They focus on application code.

**Azure Example — App Service + Azure SQL Database:**
- A retailer runs its e-commerce checkout API on **Azure App Service** (PaaS).
- The platform handles TLS termination, auto-scaling to handle Black Friday traffic spikes, and OS/runtime patching.
- Their transactional data lives in **Azure SQL Database** (managed PaaS database) with automatic backups and point-in-time restore.
- They pay only for the App Service Plan tier + DTU/vCore consumption.

**Google Cloud Example — App Engine + Cloud SQL:**
- A media company runs a content publishing platform on **Google App Engine** (PaaS) standard environment.
- It auto-scales from zero to thousands of instances based on traffic.
- Their content metadata is in **Cloud SQL** (PostgreSQL), which GCP manages (patching, backups, replicas).
- Developers only `gcloud app deploy` and never touch the OS.

---

### 3.3 — SaaS in the Wild

**AWS Example — Amazon Chime / Amazon WorkMail:**
- A company uses Amazon Chime (SaaS) for video conferencing instead of building their own WebRTC stack.
- AWS manages the servers, codecs, recording, scaling — the company just pays per user per month.

**Azure Example — Microsoft 365 (formerly Office 365):**
- An enterprise standardizes on **Microsoft 365** for email (Exchange Online), documents (SharePoint Online), and chat (Teams).
- Microsoft manages all servers, security patching, anti-malware, and uptime SLAs.
- IT team's job is mostly licensing, identity (Entra ID / Azure AD), and policy configuration.

**Google Cloud Example — Google Workspace:**
- A startup uses Gmail, Google Drive, Docs, Sheets, Meet, and Calendar from **Google Workspace**.
- Google manages the entire stack, including spam filtering, anti-malware, and global infrastructure.
- The startup never provisions a server.

---

### 3.4 — FaaS in the Wild

**AWS Example — Lambda + S3 + DynamoDB:**
- A photo-sharing app lets users upload images. When a new file lands in **S3**, an **S3 event trigger** fires an **AWS Lambda** function that:
  1. Generates three thumbnail sizes.
  2. Stores metadata in **DynamoDB**.
  3. Calls a machine learning API to detect inappropriate content.
- The function runs for ~200 ms per image. When no one uploads, the function costs $0.
- No EC2 instance is ever provisioned.

**Azure Example — Azure Functions + Cosmos DB + Event Grid:**
- A logistics company ingests IoT sensor data from delivery trucks.
- Truck GPS pings hit **Event Grid**, which triggers an **Azure Function** to validate, transform, and write to **Cosmos DB**.
- During low-traffic hours, zero compute is consumed; during the 8 AM dispatch spike, hundreds of function instances run in parallel — all managed by Azure.

**Google Cloud Example — Cloud Functions + Firestore + Pub/Sub:**
- A fintech app uses **Pub/Sub** to queue user transactions.
- A **Cloud Function** (Node.js) subscribes to the topic, validates the transaction, and writes to **Firestore**.
- Another **Cloud Function** triggers on Firestore writes to send a push notification via FCM.
- Everything is event-driven, auto-scaled, and pay-per-execution.

---

### 3.5 — Shared Responsibility in Action

**Real-world breach lesson — Capital One (2019):**
- A misconfigured AWS Web Application Firewall (WAF) on an EC2 instance allowed an ex-AWS employee to exploit an SSRF vulnerability and exfiltrate 100M+ customer records.
- The shared responsibility model says: AWS is responsible for the **security OF the cloud** (the WAF as a service, the underlying infrastructure). Capital One was responsible for the **security IN the cloud** (configuring the WAF correctly, IAM policies, patching the application).
- CompTIA tests this scenario frequently.

**Microsoft's published shared responsibility matrix** explicitly shows:
- For **Azure Virtual Machines (IaaS)**: customer manages OS, app, data; Microsoft manages physical host, network, host OS.
- For **Azure SQL Database (PaaS)**: customer manages data and access; Microsoft manages OS, SQL engine, backups.
- For **Microsoft 365 (SaaS)**: customer manages data, devices, accounts; Microsoft manages apps, OS, infrastructure.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — IaaS vs PaaS vs SaaS vs FaaS

| Attribute | IaaS | PaaS | SaaS | FaaS |
|---|---|---|---|---|
| **Definition** | Raw VMs, storage, network | Managed runtime + middleware | Ready-to-use application | Event-driven code snippets |
| **You manage** | OS, middleware, runtime, app, data | App, data | Data + access only | Function code only |
| **Provider manages** | Hypervisor, hardware, DC | OS, runtime, middleware, hypervisor, hardware, DC | Everything except your data | Everything (serverless) |
| **Control** | Highest | Medium | Lowest | Lowest (code only) |
| **Speed to deploy** | Minutes–hours | Minutes | Seconds (sign up) | Seconds (deploy function) |
| **Cost model** | Per-hour/second VM | Per-app or per-resource | Per-user/month subscription | Per-execution + GB-second |
| **Scalability** | Manual or auto-scaling groups | Built-in auto-scale | Automatic (provider) | Automatic, scales to zero |
| **Skill required** | Sysadmin / DevOps | Developer | End user | Developer |
| **Customization** | Maximum | Moderate | Minimal | Code-level only |
| **Vendor lock-in** | Low (uses standard VMs) | Medium | High | High |
| **Best use case** | Lift-and-shift, legacy apps | Web apps, APIs, CI/CD | Email, CRM, collaboration | Event handlers, glue code, sporadic workloads |
| **Examples (AWS/Azure/GCP)** | EC2 / Azure VMs / Compute Engine | Elastic Beanstalk, App Service, App Engine | Microsoft 365, Google Workspace | Lambda, Azure Functions, Cloud Functions |
| **Exam tip** | "Lift-and-shift" or "custom OS" | "Developer pushes code, no server mgmt" | "Just use the app" | "Event-driven", "sub-second billing", "scales to zero" |

---

### 4.2 — Shared Responsibility by Service Model

| Responsibility | On-Prem | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| **Data** | You | **You** | You | You |
| **Application / Code** | You | **You** | You | Provider |
| **Runtime / Middleware** | You | **You** | Provider | Provider |
| **Operating System** | You | **You** | Provider | Provider |
| **Virtualization Layer** | You | Provider | Provider | Provider |
| **Physical Servers** | You | Provider | Provider | Provider |
| **Storage Infrastructure** | You | Provider | Provider | Provider |
| **Network Infrastructure** | You | Provider | Provider | Provider |
| **Physical Data Center** | You | Provider | Provider | Provider |
| **Identity & Access Mgmt** | You | **You** | **You** | **You** (always) |
| **Patching the OS** | You | **You** | Provider | Provider |

> **Exam shorthand:** "You always own your **data** and **IAM/identity** — regardless of model."

---

### 4.3 — FaaS vs PaaS

| Attribute | FaaS | PaaS |
|---|---|---|
| **Unit of deploy** | Function | Application |
| **Execution model** | Event-triggered, ephemeral | Long-running service |
| **Cold starts** | Yes (possible) | No (pre-warmed) |
| **Max execution time** | 5–15 min (provider limit) | Unlimited |
| **State** | Stateless by design | Can be stateful |
| **Cost when idle** | $0 | Pay for provisioned tier |
| **Scaling** | Auto, per-request | Auto, by tier |
| **Best for** | Event handlers, glue | Web apps, APIs |

---

## SECTION 5 — EXAM TRAPS

### Trap 1: "Just pick SaaS for everything"
- **Question framing:** "Company wants to eliminate all infrastructure management and just use the software."
- **Trap:** Candidate picks SaaS. But if the scenario says *"the company needs to install custom security agents on the OS"* or *"the company must run a specific legacy application"* — that's IaaS, not SaaS.
- **Rule:** If the scenario mentions **OS-level control**, **custom kernel modules**, or **lift-and-shift**, it's **IaaS** — even if "fewer servers" sounds appealing.

### Trap 2: "FaaS is the cheapest — always pick it"
- **Question framing:** "Which is most cost-effective for a steady-state 24/7 workload with 10,000 req/sec?"
- **Trap:** FaaS has per-execution + GB-second pricing. For sustained, high-volume traffic, **IaaS** (with reserved instances) or **PaaS** is cheaper.
- **Rule:** FaaS = **sporadic or event-driven** traffic. Sustained high traffic = IaaS/PaaS.

### Trap 3: "Provider patches everything"
- **Question framing:** "Who is responsible for patching the OS on an EC2 instance?"
- **Wrong answer:** "AWS."
- **Correct answer:** "The customer (you)." EC2 = IaaS, you own the OS.
- **Rule:** The only time the provider patches the OS is **PaaS or SaaS**. IaaS = you patch.

### Trap 4: "PaaS is always the best balance"
- **Question framing:** "A company wants zero infrastructure management AND complete data ownership AND to install custom security agents on every server."
- **Trap:** If you need custom agents at the OS level, it's **IaaS**, not PaaS. PaaS abstracts the OS — you can't install kernel-level agents.
- **Rule:** OS-level customization = **IaaS**.

### Trap 5: "Multicloud means SaaS"
- **Question framing:** "A company wants to use multiple cloud providers to avoid vendor lock-in."
- **Wrong answer:** SaaS (because SaaS is "easy to switch").
- **Correct answer:** **IaaS** is the model with the least vendor lock-in (standard VMs, standard APIs). SaaS often has the *most* lock-in (proprietary data formats, integrations).

### Trap 6: Confusing "shared" with "split 50/50"
- **Exam wording:** *"Responsibility is shared between the provider and the customer."*
- **Trap:** Candidates think it's a 50/50 split. It's not — it varies by service. EC2 OS patching = 100% customer. AWS data center security = 100% AWS. There's no "shared" OS.

### Trap 7: Serverless ≠ no servers
- **Trap:** FaaS is called "serverless" but servers absolutely exist — the provider just manages them. CompTIA expects you to know this nuance.

### Trap 8: "Edge computing" misconception
- **Question framing:** "Company wants to run a single function on a smart camera to filter noise before sending to the cloud."
- **Trap:** Picking FaaS. But if the function must run **on the device** (no reliable uplink), it's **edge computing**, not FaaS. FaaS still runs in the provider's data center.

---

## SECTION 6 — PERFORMANCE-BASED QUESTION PREP (PBQs)

### PBQ 1 — The Lift-and-Shift Bank

**Scenario:**
A regional bank is migrating its 20-year-old core banking system to the cloud. The system runs on **Windows Server 2008 R2** with custom COM+ components and a 3rd-party encryption HSM driver that requires kernel-level access. The bank's compliance team requires full control over the OS, including the ability to install custom antivirus and to perform OS-level hardening audits.

**Question:** Which cloud service model is most appropriate?

**Walkthrough:**
1. **Custom COM+ components** → app needs specific Windows configuration.
2. **HSM driver with kernel-level access** → requires OS-level control (kernel modules).
3. **Custom antivirus + OS hardening audits** → sysadmins must access the OS directly.
4. **Decision:** ❌ PaaS (can't install custom OS drivers). ❌ SaaS (no OS access). ❌ FaaS (no OS, no long-running state). ✅ **IaaS** — gives the bank full VM + OS control.

**Bonus Q:** Who patches the OS?
- **Answer:** The bank does (shared responsibility in IaaS).

---

### PBQ 2 — The Photo-Sharing Startup

**Scenario:**
A photo-sharing startup wants to generate three thumbnail sizes for every uploaded image and run a content-moderation ML model on the result. Traffic is **spiky** (zero at 3 AM, 50K req/min at 8 PM). The CTO wants zero server management and the lowest possible cost when idle.

**Question:** Which model and what AWS services?

**Walkthrough:**
1. **Event-driven** (image upload triggers processing) → event model.
2. **Spiky traffic with zero cost when idle** → must scale to zero.
3. **Zero server management** → no IaaS.
4. **Decision:** ✅ **FaaS** (AWS Lambda triggered by S3 events).
5. **Why not PaaS?** PaaS (Elastic Beanstalk) would keep the app running 24/7, costing money even when idle.
6. **Why not always-on containers?** Same reason — idle cost.

**Bonus Q:** Where does the thumbnail metadata live?
- **Answer:** DynamoDB (NoSQL) — PaaS-style managed service called from the Lambda function.

---

### PBQ 3 — The Enterprise Email Migration

**Scenario:**
A 5,000-employee enterprise is replacing its on-prem Microsoft Exchange 2010 with cloud-based email. They want users to access mail from any device, IT wants zero server management, and security requires data-loss prevention (DLP) and multi-factor authentication.

**Question:** Which model?

**Walkthrough:**
1. **Email** → standard business app.
2. **Any device, any location** → web-based access.
3. **Zero server management** → don't want Exchange admins.
4. **DLP + MFA** → provider must support both natively.
5. **Decision:** ✅ **SaaS** (Microsoft 365 / Exchange Online, or Google Workspace).
6. **Why not PaaS hosting their own Exchange on VMs?** Because that would be IaaS (you'd manage the OS) — defeating "zero server management."

**Bonus Q:** Who manages email data residency?
- **Answer:** Customer (you) decides via tenant region selection, but Microsoft operates the infrastructure.

---

### PBQ 4 — The Regulated Workload

**Scenario:**
A healthcare app must run on a FIPS 140-2 validated OS. The team needs to install custom FIPS modules, apply OS hardening per HIPAA, and run nightly vulnerability scans at the OS level.

**Question:** Which model?

**Walkthrough:**
1. **FIPS 140-2 validated OS** → you need to control and validate the OS.
2. **OS hardening + vulnerability scans** → requires OS-level access.
3. **Custom modules** → PaaS won't allow it.
4. **Decision:** ✅ **IaaS** (e.g., AWS EC2 with a hardened AMI, or Azure VMs).

---

### PBQ 5 — The API Backend for a Mobile App

**Scenario:**
A mobile gaming company needs a scalable backend for in-game leaderboards and player profiles. Traffic varies 100x between weekdays and weekends. The two-person dev team has no sysadmin.

**Question:** Which model + services?

**Walkthrough:**
1. **Variable traffic (100x swing)** → auto-scaling needed.
2. **Two-person dev team, no sysadmin** → no OS management.
3. **Leaderboards** → fast key-value lookups (NoSQL).
4. **Decision:** ✅ **PaaS** for the API (e.g., Azure App Service, AWS Elastic Beanstalk, Google App Engine Flexible).
5. **Database:** ✅ **Provider-managed NoSQL** (DynamoDB / Cosmos DB / Firestore) — also PaaS.
6. **Why not IaaS?** Two-person team can't patch OS at scale.
7. **Why not FaaS?** Sustained, high-volume API traffic is more cost-effective on PaaS.

---

## SECTION 7 — MEMORY AIDS

### 🎯 Service Model Mnemonic: **"I Pee Sock, Friend"**

- **I**aaS = **I**nfrastructure (you bring the most; provider gives the least abstraction)
- **P**aaS = **P**latform (you bring the app, provider brings the platform)
- **S**aaS = **S**oftware (you bring the data, provider brings everything)
- **F**aaS = **F**unctions (you bring a function, provider brings ephemeral compute)

### 🎯 Shared Responsibility Mnemonic: **"You Always Own DATA and IAM"**

- **D**ata → yours, always.
- **I**AM → yours, always.
- **A**nything else = depends on the model.

### 🎯 Pizza Analogy (a teaching classic)

- **On-Prem** = you make pizza at home from scratch (you buy ingredients, you cook, you clean up).
- **IaaS** = you use a kitchen that's already set up with an oven (provider gives you the kitchen; you make the pizza).
- **PaaS** = a kitchen where the dough is already made, sauce is ready, toppings laid out — you just assemble and bake (provider gives you the platform).
- **SaaS** = you call Pizza Hut and they deliver a finished pizza (you just eat).
- **FaaS** = you order a single slice that exists only when you want it (per-use, ephemeral).

### 🎯 FaaS Trigger Words
Spot these in questions:
- "Event-driven"
- "Triggered by [S3, queue, HTTP]"
- "Scales to zero"
- "Sub-second billing"
- "Runs only when X happens"
- "Cold start" (mention of this is a tell)

### 🎯 Shared Responsibility Visual

```
           ┌─────────────────────────────┐
YOU OWN →  │ Data  │ IAM │ (varies)      │
           ├─────────────────────────────┤
PROVIDER → │ OS, Runtime, Hypervisor,   │ ← varies by model
           │ Servers, Storage, Network, │
           │ Physical DC                 │
           └─────────────────────────────┘
```

### 🎯 Quick Decision Tree

```
Q: Do you need to control the OS?
├── YES → IaaS
└── NO  → Q: Do you deploy a whole app or just functions?
         ├── Functions only → FaaS
         ├── Whole app      → Q: Do you want to manage runtime/middleware?
                             ├── NO  → PaaS
                             └── YES → IaaS (re-think)
         └── You just use it → SaaS
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

### IaaS Services

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **Virtual machines** | EC2 (Elastic Compute Cloud) | Azure Virtual Machines | Compute Engine |
| **Raw block storage** | EBS (Elastic Block Store) | Azure Disk Storage | Persistent Disk |
| **Object storage** | S3 (Simple Storage Service) | Blob Storage | Cloud Storage |
| **Virtual network** | VPC (Virtual Private Cloud) | Azure Virtual Network (VNet) | VPC |
| **DNS** | Route 53 | Azure DNS | Cloud DNS |
| **Load balancer (L4)** | Network Load Balancer | Azure Load Balancer | TCP/UDP Load Balancing |
| **Load balancer (L7)** | Application Load Balancer | Application Gateway | HTTP(S) Load Balancing |
| **Relational DB (IaaS-style)** | EC2-hosted RDS-self or Aurora on EC2 | SQL Server on Azure VMs | SQL Server on Compute Engine |

### PaaS Services

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **App hosting (managed)** | Elastic Beanstalk, App Runner | App Service, Spring Apps | App Engine |
| **Managed relational DB** | RDS, Aurora | Azure SQL Database, Database for MySQL/PostgreSQL | Cloud SQL, AlloyDB |
| **Managed NoSQL** | DynamoDB | Cosmos DB | Firestore, Bigtable |
| **Managed cache** | ElastiCache (Redis/Memcached) | Azure Cache for Redis | Memorystore |
| **Message queue** | SQS, SNS | Service Bus, Event Grid | Pub/Sub |
| **Container PaaS** | ECS, EKS | Container Apps, AKS | Cloud Run, GKE Autopilot |
| **CI/CD PaaS** | CodeBuild, CodeDeploy | Azure DevOps, GitHub Actions | Cloud Build, Cloud Deploy |

### SaaS Examples (Off-the-Shelf Apps)

| Function | AWS | Azure | Google Cloud |
|---|---|---|---|
| **Email + Collab** | Amazon WorkMail (limited) | Microsoft 365 | Google Workspace |
| **CRM** | (3rd party on AWS) | Dynamics 365 (Microsoft) | (3rd party on GCP) |
| **Storage/Sharing** | — | OneDrive, SharePoint | Google Drive |
| **Video Conferencing** | Amazon Chime | Microsoft Teams | Google Meet |
| **BI / Analytics** | Amazon QuickSight | Power BI | Looker |
| **Security/IdP** | (3rd party) | Microsoft Entra ID | Cloud Identity |
| **Low-code Apps** | — | Power Apps | AppSheet |

### FaaS / Serverless Services

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **Function compute** | AWS Lambda | Azure Functions | Cloud Functions |
| **Serverless containers** | AWS Fargate | Azure Container Apps | Cloud Run |
| **Event bus** | EventBridge | Event Grid | Eventarc |
| **Serverless API gateway** | API Gateway | API Management | API Gateway, Cloud Endpoints |
| **Workflow orchestration** | Step Functions | Logic Apps | Workflows |
| **Serverless DB** | Aurora Serverless, DynamoDB | Cosmos DB Serverless | Firestore, AlloyDB Serverless |

### Shared Responsibility — Provider Documentation Links
- **AWS:** Shared Responsibility Model whitepaper
- **Azure:** Microsoft Services Shared Responsibility (has 6 customer-managed categories)
- **GCP:** Google Cloud Shared Responsibility Model (GCP matrix)

---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What's the difference between IaaS, PaaS, and SaaS?**
**Ideal answer:** "IaaS gives you virtualized hardware — VMs, storage, networks — and you manage the OS, middleware, and app. PaaS gives you a managed platform — you push code and the provider handles the runtime, OS, and scaling. SaaS is a finished application you just use, like Gmail or Microsoft 365. The more you move up the stack toward SaaS, the less you manage and the less control you have."

**Q2: Who patches the OS in AWS EC2?**
**Ideal answer:** "The customer does. EC2 is IaaS, and shared responsibility says the customer owns the OS, middleware, runtime, app, and data. AWS owns the physical host, hypervisor, and underlying infrastructure."

**Q3: When would you choose EC2 over Lambda?**
**Ideal answer:** "When I need long-running processes, a custom OS environment, full control of the runtime, or steady high-throughput traffic where reserved capacity is more cost-effective than per-execution billing. Lambda is best for short, event-driven bursts."

**Q4: What is FaaS in one sentence?**
**Ideal answer:** "Function as a Service — you upload small pieces of code that run only when triggered by an event, with the provider managing all servers and billing per millisecond of execution time."

**Q5: Name one example of SaaS in the AWS ecosystem.**
**Ideal answer:** "Amazon Chime for video conferencing, Amazon WorkMail for email, or Amazon QuickSight for BI. (Note: most of AWS's SaaS-style offerings are 3rd-party products hosted on AWS Marketplace.)"

---

### 🎓 Cloud Administrator Questions

**Q1: A team complains that their app is slow on PaaS. Walk me through how you'd diagnose whether it's the app, the platform, or the underlying infra.**
**Ideal answer:** "I'd check the platform's built-in monitoring first — Azure App Service metrics, AWS Elastic Beanstalk health, or GCP App Engine logs. Look at HTTP queue time (PaaS queue), instance count, and response time. If queue time is high but CPU is low, the platform is bottlenecked and I need to scale out. If CPU is high, it's the app. Then I'd check downstream dependencies (DB, cache) using APM tools like Application Insights, X-Ray, or Cloud Trace. Finally, I'd confirm the underlying infra (VMs, network) isn't saturated by checking the provider's health dashboard."

**Q2: How do you decide between moving an on-prem app to EC2 vs Elastic Beanstalk?**
**Ideal answer:** "If the app needs a custom OS, kernel modules, or a specific runtime version that the platform doesn't support, EC2. If the app is a standard web service that runs on a supported runtime (Node, Java, Python, .NET, etc.) and we want zero OS management, Elastic Beanstalk. Beanstalk can also be lifted into a Docker container, which gives some flexibility. If we need even less management, we'd consider App Runner or Fargate (serverless containers)."

**Q3: A security officer asks, 'In our AWS environment, what does AWS secure and what do we secure?' How do you answer?**
**Ideal answer:** "AWS secures the cloud — physical data centers, hardware, host infrastructure, networking, and the services themselves (e.g., the RDS engine, the Lambda runtime). We secure in the cloud — our data, IAM users and policies, OS patching (for EC2), security groups, NACLs, application code, and network configuration. The exact split depends on the service: for EC2, we own OS + above; for RDS, AWS owns the DB engine; for Lambda, AWS owns the runtime entirely."

**Q4: A company wants to use multiple cloud providers. What service model reduces lock-in the most, and why?**
**Ideal answer:** "IaaS — because VMs and standard APIs (Linux, Windows, Kubernetes, Terraform) are portable across providers. PaaS ties you to provider-specific services (e.g., DynamoDB, Cosmos DB, Bigtable have different APIs). SaaS is the worst for lock-in because your data, business logic, and integrations all live in a proprietary system."

**Q5: When would you recommend FaaS even though cold starts are a concern?**
**Ideal answer:** "When the workload is event-driven, cost-sensitive during idle, and can tolerate 100–500 ms of latency on first invocation. For example, image processing, file conversion, webhook receivers, and async ETL. To mitigate cold starts, you can use provisioned concurrency (Lambda), keep the function warm with a scheduled ping (anti-pattern but practical), or use a serverless container platform like Cloud Run or Azure Container Apps which warm up faster than pure FaaS."

---

### 🎓 Cloud Support / Pre-Sales Questions

**Q1: Customer says 'We want to move to serverless to save money.' What do you ask next?**
**Ideal answer:** "I ask about their traffic patterns — is it steady or spiky? What does their app look like — is it a monolith or microservices? What are their latency SLAs — can they tolerate 100–500 ms cold starts? What are their compliance requirements — can the provider's runtime meet them? 'Serverless' can mean FaaS (Lambda, Functions) or serverless containers (Fargate, Cloud Run, Container Apps) — the right answer depends on the workload."

**Q2: A prospect says 'PaaS is always more expensive than IaaS.' How do you respond?**
**Ideal answer:** "Not always. You need to factor in total cost of ownership: IaaS has hidden costs in sysadmin time, patching, HA setup, monitoring agents, and backup management. PaaS bundles these in. For a 3-person dev team, PaaS is almost always cheaper when you include labor. For a 30-person ops team running predictable workloads, reserved IaaS can win on raw compute cost. The break-even depends on workload type and team size."

**Q3: How do you explain shared responsibility to a non-technical executive?**
**Ideal answer:** "I use the cloud-vacation-rental analogy. When you stay in a hotel, the hotel secures the building, the pool, and the lobby. You lock your room door and put valuables in the room safe. The hotel isn't responsible if you leave your wallet on the beach. The cloud works the same way — AWS secures the data center, the servers, and the network. You secure your data, your access credentials, and your configurations. A breach usually happens because the customer's part of the responsibility wasn't met."

---

## SECTION 10 — FLASHCARDS

```
Q: What does IaaS stand for and what does the provider give you?
A: Infrastructure as a Service — provider gives VMs, storage, and networking; you manage OS, runtime, middleware, app, and data.

Q: What does PaaS stand for and what does the provider give you?
A: Platform as a Service — provider gives a managed platform (OS, runtime, middleware, infrastructure); you manage app and data.

Q: What does SaaS stand for?
A: Software as a Service — ready-to-use application over the internet (Gmail, Microsoft 365, Salesforce).

Q: What does FaaS stand for and how is it different from PaaS?
A: Function as a Service — you deploy single functions, not whole apps. Runs only when triggered by an event. Scales to zero. Bills per execution.

Q: In IaaS, who patches the OS?
A: The customer (you).

Q: In PaaS, who patches the OS?
A: The provider.

Q: In SaaS, who manages the application code?
A: The provider.

Q: Who always owns the data, regardless of service model?
A: The customer.

Q: Who always owns IAM (identity and access management)?
A: The customer.

Q: Name an AWS IaaS service.
A: EC2 (Elastic Compute Cloud).

Q: Name an Azure IaaS service.
A: Azure Virtual Machines.

Q: Name a GCP IaaS service.
A: Compute Engine.

Q: Name an AWS PaaS service for app hosting.
A: Elastic Beanstalk, App Runner.

Q: Name an Azure PaaS service for app hosting.
A: App Service.

Q: Name a GCP PaaS service for app hosting.
A: App Engine.

Q: Name an AWS FaaS service.
A: AWS Lambda.

Q: Name an Azure FaaS service.
A: Azure Functions.

Q: Name a GCP FaaS service.
A: Cloud Functions.

Q: Which model gives the customer the most control?
A: IaaS.

Q: Which model gives the customer the least operational overhead?
A: SaaS.

Q: Which model is most cost-effective when the workload is idle?
A: FaaS (scales to zero, no idle cost).

Q: What's a "cold start" in FaaS?
A: The latency when a function is invoked for the first time after being idle, as the provider spins up a new execution environment.

Q: What's the maximum execution time commonly allowed in FaaS?
A: Typically 5–15 minutes (provider-specific).

Q: A company has a legacy app that needs Windows Server 2008. Which model?
A: IaaS — you need OS-level control.

Q: A company wants to send notifications whenever a new file is uploaded. Which model?
A: FaaS — event-driven, scales to zero.

Q: A company wants email for 5,000 users with no server management. Which model?
A: SaaS — Microsoft 365 or Google Workspace.

Q: A company wants to deploy a Node.js REST API and focus only on code. Which model?
A: PaaS — Elastic Beanstalk / App Service / App Engine.

Q: What's the shared responsibility model in one sentence?
A: Provider is responsible for security OF the cloud; customer is responsible for security IN the cloud.

Q: In shared responsibility, who secures the data center?
A: The cloud provider (always).

Q: In shared responsibility, who secures the customer's data?
A: The customer (always).

Q: In shared responsibility, who applies security groups (firewall rules) in AWS?
A: The customer (always — even for IaaS, PaaS, and SaaS).

Q: What does "lift-and-shift" mean in cloud migration?
A: Moving an application from on-prem to cloud with minimal redesign — typically IaaS (EC2 / Azure VM / Compute Engine).

Q: What's an example of vendor lock-in?
A: Building an app tightly on AWS Lambda + DynamoDB + API Gateway; switching to Azure or GCP requires significant code rewrite.

Q: Which model is most portable across cloud providers?
A: IaaS (with standard VMs, Linux, Kubernetes on top).

Q: What is a "serverless" misconception?
A: That no servers run your code. Servers do — the provider just manages them.

Q: What is a hypervisor?
A: Software that creates and runs virtual machines on a physical host (provider manages this in IaaS).

Q: What's a "managed service" in PaaS?
A: A service where the provider handles the infrastructure, OS, runtime, patching, and scaling — you consume it via API or portal.

Q: What does "scales to zero" mean?
A: The service can drop to zero running instances (and zero cost) when there's no load — typical of FaaS.
```

---

## SECTION 11 — PRACTICE QUESTIONS

### Q1 (Easy)
A startup wants to send a one-time welcome email the moment a new user signs up. They want zero server management. Which model is best?
A) IaaS
B) PaaS
C) SaaS
D) FaaS

**Answer: D) FaaS**
**Explanation:** Event-driven (sign-up triggers email), zero idle cost, no server management. Lambda/Function/Cloud Function triggered by an API or queue event is a textbook FaaS scenario.
**Why others are wrong:**
- A) IaaS — overkill, requires VM and OS management.
- B) PaaS — possible but inefficient for a single event handler.
- C) SaaS — sending transactional email from a SaaS product doesn't fit; SaaS is for end-user apps, not custom backends.

---

### Q2 (Easy)
Who is responsible for patching the operating system on an AWS EC2 instance?
A) AWS
B) The customer
C) Both share patching duties
D) The application vendor

**Answer: B) The customer**
**Explanation:** EC2 = IaaS, and the shared responsibility model assigns the OS to the customer. AWS secures the host, the hypervisor, and the physical infrastructure — not your guest OS.
**Why others are wrong:**
- A) AWS would only patch the OS in PaaS/SaaS offerings like RDS, Lambda, or Microsoft 365.
- C) There's no "shared" OS responsibility — it's 100% one or the other.
- D) Application vendors supply app patches, not OS patches.

---

### Q3 (Easy)
A company wants to use Microsoft 365 for email. Which service model?
A) IaaS
B) PaaS
C) SaaS
D) FaaS

**Answer: C) SaaS**
**Explanation:** Microsoft 365 is a complete, ready-to-use cloud application. Provider manages everything except your data and access.
**Why others are wrong:**
- A) IaaS would mean you run the email server on a VM (like Exchange on EC2) — they don't want that.
- B) PaaS would mean you deploy a custom app to a platform — but Microsoft 365 is a finished product.
- D) FaaS is for event-driven functions, not full office productivity suites.

---

### Q4 (Medium)
A developer pushes code to AWS Elastic Beanstalk. What does AWS manage on her behalf?
A) Application code
B) OS, runtime, and infrastructure
C) Customer data
D) Security groups

**Answer: B) OS, runtime, and infrastructure**
**Explanation:** Elastic Beanstalk is PaaS. AWS provisions the EC2 instances, installs the OS and runtime (e.g., Node.js), and handles auto-scaling and load balancing. You manage the code and data.
**Why others are wrong:**
- A) The developer manages application code.
- C) The customer always owns their data.
- D) The customer (developer) configures security groups in Beanstalk.

---

### Q5 (Medium)
Which scenario is the best fit for FaaS?
A) A web app with 1,000 steady concurrent users
B) An event-driven image-resize pipeline
C) A monolithic legacy ERP system
D) A long-running batch job that takes 8 hours

**Answer: B) An event-driven image-resize pipeline**
**Explanation:** Event-driven, short execution, scales to zero — FaaS is purpose-built for this.
**Why others are wrong:**
- A) Sustained high traffic is more cost-effective on PaaS or IaaS.
- C) Legacy ERP = IaaS (lift-and-shift with custom OS).
- D) 8 hours exceeds typical FaaS execution limits (5–15 min).

---

### Q6 (Medium)
A bank needs to run an HSM driver that requires kernel-level access. Which model?
A) IaaS
B) PaaS
C) SaaS
D) FaaS

**Answer: A) IaaS**
**Explanation:** Kernel-level access requires OS control, which only IaaS provides.
**Why others are wrong:**
- B) PaaS abstracts the OS — you can't install kernel modules.
- C) SaaS gives you no OS access at all.
- D) FaaS runs in an ephemeral sandbox — no custom OS.

---

### Q7 (Medium)
In which model does the cloud provider have the LEAST responsibility?
A) IaaS
B) PaaS
C) SaaS
D) On-Premises

**Answer: A) IaaS**
**Explanation:** The provider has the least responsibility in IaaS — they only manage the hypervisor, hardware, and physical data center. Everything else (OS up) is the customer's job.
**Why others are wrong:**
- B) PaaS adds OS, runtime, middleware to provider's plate.
- C) SaaS gives the provider the most responsibility.
- D) On-prem has the provider with zero responsibility (you do it all).

---

### Q8 (Medium)
A company wants to use Google App Engine to deploy a Python web app. What is one thing the customer is STILL responsible for?
A) Patching the Python runtime
B) Configuring auto-scaling
C) The application code
D) Updating the OS

**Answer: C) The application code**
**Explanation:** In PaaS, you push code and the provider handles the platform. You're responsible for what your code does.
**Why others are wrong:**
- A) The provider manages the Python runtime.
- B) App Engine handles auto-scaling automatically.
- D) The provider updates the OS.

---

### Q9 (Medium)
Which of the following is a clear indicator that a workload should run on FaaS, NOT PaaS?
A) The app needs a relational database.
B) The workload runs only in response to events and spends 95% of the day idle.
C) The app has a web UI.
D) The app needs a load balancer.

**Answer: B) The workload runs only in response to events and spends 95% of the day idle.**
**Explanation:** Spiky + idle = FaaS (scales to zero = no idle cost).
**Why others are wrong:**
- A) FaaS can call a relational DB; it's not exclusive to PaaS.
- C) Both FaaS and PaaS can serve web UIs (FaaS via API Gateway).
- D) API Gateway + Lambda is a serverless load-balancing pattern.

---

### Q10 (Hard)
An enterprise wants full data sovereignty and the ability to revoke the cloud provider's access to encrypted data at any time. The workload is a custom Java app that must run on a specific JDK version. Which model?
A) IaaS with customer-managed encryption keys
B) PaaS with provider-managed keys
C) SaaS with provider-managed keys
D) FaaS with provider-managed keys

**Answer: A) IaaS with customer-managed encryption keys**
**Explanation:**
- Custom JDK = IaaS (need OS/runtime control).
- Data sovereignty + key revocation = customer-managed keys (e.g., AWS KMS with customer-managed CMK, Azure Key Vault HSM, GCP CMEK).
- Provider-managed keys don't give the customer revocation control.
**Why others are wrong:**
- B) PaaS won't allow custom JDK versions on most platforms.
- C) SaaS gives no OS control and uses provider keys.
- D) FaaS has no JDK customization.

---

### Q11 (Hard)
A SaaS provider advertises "we take care of everything." A customer believes this means the provider handles ALL security. Which CompTIA-tested concept is the customer misunderstanding?
A) Pay-as-you-go billing
B) Shared responsibility model
C) Auto-scaling
D) Multi-tenancy

**Answer: B) Shared responsibility model**
**Explanation:** The customer always owns data, IAM, and access policy — even in SaaS. "We take care of everything" refers to the infrastructure, OS, and application — not your data and accounts.
**Why others are wrong:**
- A) Billing model is unrelated to security ownership.
- C) Auto-scaling is a capability, not a security model.
- D) Multi-tenancy is about how customers share infrastructure, not about security ownership.

---

### Q12 (Hard)
A company uses AWS Lambda to process messages from an SQS queue. Each message takes 30 seconds to process. They notice occasional processing failures. What is the FIRST thing they should verify?
A) Lambda's maximum execution time limit
B) The IAM role attached to the Lambda
C) The size of the SQS message
D) The Lambda function's memory allocation

**Answer: A) Lambda's maximum execution time limit**
**Explanation:** Lambda's max execution time is 15 minutes (configurable, but capped). 30 seconds is fine, but if the queue has long messages with retries, execution can balloon. Always confirm the timeout setting on the function itself first.
**Why others are wrong:**
- B) IAM is important but unrelated to timeout-driven failure.
- C) Message size affects SQS, not Lambda execution time directly.
- D) Memory affects CPU and concurrency, not timeout.

---

### Q13 (Hard)
A startup uses both AWS (for compute) and Azure (for ML services). They want to use a consistent identity provider across both. Which architectural choice is most aligned with shared responsibility best practices?
A) Create separate IAM users in AWS and Azure AD users in Azure
B) Use a third-party IdP (e.g., Okta) federated to both AWS and Azure
C) Hard-code credentials in application config files
D) Share root account credentials between the two cloud teams

**Answer: B) Use a third-party IdP federated to both AWS and Azure**
**Explanation:** Federation via SAML/OIDC lets the customer own identity centrally. This satisfies shared responsibility (customer owns IAM) while reducing management overhead.
**Why others are wrong:**
- A) Duplicates identity management — not a best practice.
- C) Hard-coding credentials is a security anti-pattern.
- D) Sharing root credentials violates the principle of least privilege.

---

### Q14 (Hard)
A team wants to run a 24/7 video transcoding pipeline that consistently uses 80% of 16 vCPUs. Which model is the LEAST cost-effective?
A) Reserved EC2 instance
B) Spot EC2 instance
C) AWS Lambda
D) Savings Plan EC2

**Answer: C) AWS Lambda**
**Explanation:** Sustained 80% CPU on 16 vCPUs means continuous, predictable compute. Lambda's per-execution + GB-second pricing makes this extremely expensive. Spot/Savings/Reserved are all cheaper.
**Why others are wrong:**
- A) Reserved is excellent for steady-state workloads.
- B) Spot is cheaper still (but can be interrupted — risky for 24/7 critical work).
- D) Savings Plans offer discount for committed use.

---

### Q15 (Hard)
A SaaS app on Microsoft 365 suffers a breach because a former employee still has an active account. According to shared responsibility, who failed?
A) Microsoft
B) The customer
C) Both — Microsoft should have detected the inactive account
D) Neither — SaaS is fully provider-managed

**Answer: B) The customer**
**Explanation:** Identity lifecycle (provisioning, de-provisioning) is the customer's responsibility. Microsoft provides the platform, but the customer must manage their own user accounts.
**Why others are wrong:**
- A) Microsoft has no insight into which employees should have access.
- C) Microsoft does not manage customer-side identity lifecycle.
- D) SaaS is *not* fully provider-managed — data and IAM remain the customer's.

---

### Q16 (Medium)
Which of the following BEST describes "scale to zero"?
A) IaaS auto-scaling removing the largest instance
B) FaaS having no running instances and zero cost when idle
C) SaaS deleting customer data
D) Shutting down a PaaS app to save money

**Answer: B) FaaS having no running instances and zero cost when idle**
**Explanation:** "Scale to zero" is a defining FaaS characteristic.
**Why others are wrong:**
- A) IaaS auto-scaling doesn't usually scale to zero (usually has a minimum).
- C) SaaS providers don't delete customer data based on load.
- D) Shutting down a PaaS app is a manual action, not a "scale to zero" property.

---

### Q17 (Medium)
A company wants to develop a custom mobile app's backend. They have 1 backend developer and no sysadmins. The backend will handle user auth, push notifications, and profile storage. Which model is most appropriate?
A) IaaS
B) PaaS
C) SaaS
D) FaaS (for everything)

**Answer: B) PaaS** (or a mix of PaaS + FaaS, but PaaS is the core)
**Explanation:** A single dev without ops skills needs managed services — App Service / Elastic Beanstalk / App Engine. FaaS could handle some glue code, but the auth/profile storage pieces benefit from PaaS (managed DB, identity).
**Why others are wrong:**
- A) IaaS requires sysadmin work.
- C) SaaS is for end-user apps, not custom backends.
- D) Pure FaaS is hard to use for sustained user auth flows and complex state.

---

### Q18 (Hard)
A company wants to use cloud but must comply with export-control laws that prohibit data from leaving a specific country. Which model gives them the MOST control over data location?
A) SaaS in any region
B) IaaS in a region within that country
C) PaaS in any region
D) FaaS in any region

**Answer: B) IaaS in a region within that country**
**Explanation:** While PaaS and SaaS can be regional, IaaS gives the most direct control over data location (you choose the region, you can encrypt with customer-managed keys, you can verify the physical location).
**Why others are wrong:**
- A, C, D) Provider regions are not always within the specific country required by law, and provider-managed services may replicate data across regions.

---

### Q19 (Medium)
Which of the following is NOT a shared responsibility in any cloud model?
A) Data
B) IAM
C) Application code
D) Physical data center security

**Answer: C) Application code** — never the provider's responsibility, except in SaaS where the provider owns the app code they wrote.
**Wait — correction:** Application code is the customer's responsibility in IaaS and PaaS, and the provider's in SaaS (because the SaaS provider wrote the app). The question is asking which is *not* shared — meaning fully owned by one party. "Physical data center security" is fully owned by the provider. Application code is fully owned by the customer in IaaS/PaaS. IAM and data are always the customer's. So the answer is C) Application code is never *shared* — it's always 100% one party.
**Why others are wrong:**
- A) Always customer.
- B) Always customer.
- D) Always provider.

---

### Q20 (Hard)
A company wants to ensure that even the cloud provider's employees cannot access their data. They use provider-managed encryption but rely on the provider's KMS. What is the security gap?
A) No gap — provider-managed KMS is fully secure
B) Provider employees with KMS access could decrypt the data
C) The data is automatically replicated globally
D) The provider cannot read encrypted data regardless of key access

**Answer: B) Provider employees with KMS access could decrypt the data**
**Explanation:** Provider-managed KMS means the provider controls the keys — and in some cases, their personnel may have technical access. To prevent this, the customer must use **customer-managed keys (CMK/CMEK)** with envelope encryption, sometimes stored in a Hardware Security Module (HSM) that the provider cannot access.
**Why others are wrong:**
- A) Provider-managed KMS has trust assumptions.
- C) Replication is a separate concern.
- D) Whoever holds the key can decrypt — this is fundamental cryptography.

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| IaaS / PaaS / SaaS definitions | 🔴 **CRITICAL** | Tested in nearly every scenario question. |
| FaaS characteristics & use cases | 🔴 **CRITICAL** | CompTIA added FaaS to CV0-004 — heavy focus. |
| Shared responsibility model (full matrix) | 🔴 **CRITICAL** | Top exam topic across all 5 domains. |
| Who patches the OS in each model | 🔴 **CRITICAL** | Most-asked specific trap. |
| Who owns data and IAM | 🔴 **CRITICAL** | Universal rule, tested in every domain. |
| Real-world examples (AWS Lambda, EC2, etc.) | 🟠 **HIGH** | Scenarios mention specific services. |
| FaaS vs PaaS trade-offs | 🟠 **HIGH** | Common PBQ decision point. |
| Cost model differences (per-hour vs per-execution) | 🟠 **HIGH** | Tested in cost-related questions. |
| Vendor lock-in by model | 🟡 **MEDIUM** | Occasional trap, usually in security or cost. |
| Serverless ≠ no servers | 🟡 **MEDIUM** | Conceptual question, lower frequency. |
| Specific Azure/GCP service names | 🟡 **MEDIUM** | You don't need all, but recognize common ones. |
| Cold start latency | 🟡 **MEDIUM** | Performance scenario question. |
| Lambda execution time limits | 🟢 **LOW** | Niche detail; rarely tested directly. |
| Pizza analogy (in real life) | 🟢 **LOW** | Teaching tool, not on exam. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-Page Cram Sheet)

### 📋 Cram Sheet: 1.1 Cloud Service Models & Shared Responsibility

**Service Models (stack coverage, from most to least customer-managed):**
1. **IaaS** = VMs, storage, networks. You manage: OS, middleware, runtime, app, data.
2. **PaaS** = Managed platform. You manage: app, data.
3. **SaaS** = Ready-to-use software. You manage: data + access.
4. **FaaS** = Event-driven functions. You manage: function code + data.

**Decision Shortcuts:**
- "Custom OS / kernel / driver / lift-and-shift" → **IaaS**
- "Push code, no server management" → **PaaS**
- "Just use the software" → **SaaS**
- "Triggered by event, scales to zero, sub-second billing" → **FaaS**

**Shared Responsibility Cheat Codes:**
- 🔑 **Customer ALWAYS owns:** Data, IAM, identity.
- 🏢 **Provider ALWAYS owns:** Physical data center, hardware, host infrastructure.
- ⚙️ **OS:** Customer in IaaS, Provider in PaaS/SaaS.
- 🗄️ **Application:** Customer in IaaS/PaaS, Provider in SaaS.
- 🌐 **Network config (security groups, NACLs):** Always customer.

**FaaS Gotchas:**
- Cold start: first invoke after idle = 100–500 ms latency.
- Max execution: 5–15 min (provider-specific).
- Stateless: state must live in DB/cache/storage.
- Idle cost: $0.
- Don't use for: sustained high-traffic, long jobs, persistent local state.

**Top Exam Triggers:**
- "Lift-and-shift" → IaaS
- "OS-level control" / "custom kernel" → IaaS
- "PaaS but need custom security agents" → actually IaaS
- "Steady high traffic, cost-sensitive" → IaaS reserved, not FaaS
- "Event-driven" / "spiky and idle" → FaaS
- "OS patching in EC2" → Customer
- "Email for 5,000 users" → SaaS

**Acronyms to Know Cold:**
- **IaaS** = Infrastructure as a Service
- **PaaS** = Platform as a Service
- **SaaS** = Software as a Service
- **FaaS** = Function as a Service
- **IAM** = Identity and Access Management
- **AMI** = Amazon Machine Image (EC2 template)
- **RTO/RPO** = Recovery Time / Recovery Point Objective (preview for 1.2)

---

## SECTION 14 — LATEST INDUSTRY UPDATES (CV0-004 Relevant)

### AWS Updates (2024–2025)
- **Lambda:** Now supports **Provisioned Concurrency** for sub-10 ms cold starts; **SnapStart** for Java/.NET; **Response Streaming** for HTTP responses up to 20 MB. Function URLs simplified FaaS deployment.
- **App Runner:** Continued growth as the "PaaS-lite" — auto-scales from containers without managing clusters.
- **EC2:** Graviton4 (ARM) instances offer 30–40% better price-performance; M7i, R8g families standard.
- **Shared Responsibility:** AWS published updated shared responsibility docs in 2023 emphasizing "security of the cloud" vs "security in the cloud" remains the same; new services like Lambda have shifted more responsibility to AWS.

### Azure Updates (2024–2025)
- **Azure Functions:** Flex Consumption plan (2024) — faster cold starts, per-function scaling, VNet integration. Replaces the older Consumption plan in many new deployments.
- **Azure Container Apps:** Emerged as the "modern Azure PaaS for microservices" — based on Kubernetes but fully managed.
- **Azure VMs:** New D-series v6, HX-series for HPC.
- **Microsoft 365:** Copilot integration (2024) — AI features inside SaaS apps. Exam-relevant: AI is now embedded in SaaS, but customers still own their data when configuring Copilot.

### Google Cloud Updates (2024–2025)
- **Cloud Run:** Serverless containers with sub-second cold starts; supports GPU (2024) for AI workloads.
- **Cloud Functions:** 2nd gen (2024) built on Cloud Run — better concurrency, longer timeouts.
- **GKE Autopilot:** PaaS-like Kubernetes — Google manages the control plane and nodes, customer only manages pods.
- **Compute Engine:** Spot VMs replaced Preemptible VMs; C3, C3a (AMD) families standard.

### Containers / Kubernetes
- **Kubernetes is the de facto orchestration standard.** EKS (AWS), AKS (Azure), GKE (GCP) are managed Kubernetes services — all PaaS.
- **Serverless containers (AWS Fargate, Azure Container Apps, Google Cloud Run)** are the fastest-growing PaaS category — they sit between FaaS and PaaS.
- **Exam tip:** Know the difference between **containers as a service (CaaS)** like EKS/Fargate and **PaaS for apps** like Elastic Beanstalk. CaaS is still your container to manage; PaaS abstracts more.

### AI Services (Cloud+ Adjacent — appearing more in CV0-004 v5.0)
- **AWS:** Bedrock (managed foundation models), SageMaker (ML PaaS), Q for Developers.
- **Azure:** Azure OpenAI Service, Azure ML, Copilot Studio.
- **Google:** Vertex AI (unified ML platform), Gemini API.
- **All three are PaaS** — the customer brings their data and the application logic, the provider manages the model serving infrastructure.

### Serverless Trend
- "Serverless" is no longer just FaaS — it now includes serverless containers (Fargate, Container Apps, Cloud Run), serverless databases (Aurora Serverless, Cosmos DB Serverless, AlloyDB Serverless), and serverless Kafka (MSK Serverless).
- **Exam angle:** "Serverless = provider manages all infrastructure, scales to zero (or near-zero), bills per consumption." Match this description to the right service.

---

## ✅ END OF OBJECTIVE 1.1 NOTES

**Next step:** Once you've mastered 1.1, ping me for **1.2 — Service Availability (Regions, AZs, Cloud Bursting, Edge, RTO/RPO, Hot/Warm/Cold Sites, Multicloud Tenancy)** — that's the next big exam weight in Domain 1.0.

Study tip from Cikgu Danial: *"Don't just read — close this file, take a blank sheet of paper, and write down IaaS/PaaS/SaaS/FaaS in your own words. Then sketch the shared responsibility matrix from memory. If you can do that, you own this objective."* 💪
