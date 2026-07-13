# Objective 2.1 — Compare and contrast cloud deployment models

## SECTION 1 — Exam Objectives

**Objective Number:** 2.1
**Objective Name:** *Compare and contrast cloud deployment models.*

**Sub-objectives (the four deployment models you must know):**
- Public cloud
- Private cloud (including On premises as the location style)
- Hybrid cloud
- Community cloud

**What this objective means (beginner-friendly):**
- A "deployment model" answers: *Who owns the hardware, where is it located, and who may use it?* — it is NOT the same as a "service model" (IaaS, PaaS, SaaS).
- For every scenario question you get a business situation (cost, privacy law, burst traffic, shared regulation) and must pick the best model.

**Key contrast dimensions:**
- **Tenancy** — single-tenant (one customer) vs multi-tenant (many share hardware).
- **Location** — on premises (you own) vs off premises (provider's DC).
- **Ownership & control** — who buys, patches, secures the servers.
- **Cost** — CapEx (buy upfront) vs OpEx (pay monthly).
- **Scalability** — how fast/far you can grow.
- **Compliance & data sovereignty** — where data physically resides (key for Malaysian PDPA and banking rules).

**Beginner tip:**
- Public = shared + off-prem.
- Private = dedicated (on- or off-prem).
- Community = shared + dedicated-to-a-group.
- Hybrid = private core + public extension.

## SECTION 2 — EXAM KNOWLEDGE

### 2.1.1 — Public Cloud

- **Definition:** A multi-tenant environment owned and operated by a third-party provider (AWS, Azure, Google Cloud, Alibaba Cloud). The provider owns the data centers and sells access over the internet; you provision on demand and pay only for what you use (OpEx, pay-as-you-go).
- **Why it matters:** It is the default for low upfront cost, rapid global scale, and no hardware to manage. The exam pairs "public" with *cost-effective, quick to deploy, no CapEx, elastic, multi-tenant, scalable, internet-facing*. Trade-off: less control and a shared-responsibility security model.
- **Analogy:** Like renting a co-working desk — you share the building and security guards with many others, pay only for the hours you use it, and never own the building.
- **Example:** A Malaysian e-commerce startup launches on AWS using EC2 and S3, paying by traffic. During 12.12 sales it scales 5→200 servers, then back down, spending zero ringgit on physical hardware.

### 2.1.2 — Private Cloud

- **Definition:** Single-tenant, dedicated to ONE organization. Hardware is not shared. It may be **on premises** (org owns the building/servers) or hosted off-prem but dedicated solely to that customer. The org keeps full control of security and data location.
- **Why it matters:** Wins when privacy, compliance, and control dominate. No "noisy neighbor," strongest isolation, predictable performance. Cost is high (CapEx + staff). Exam pairs "private" with *data sovereignty, strict compliance, sensitive data, full control, single-tenant, on premises*.
- **Analogy:** Like owning your own house — you don't share walls or locks, you pay upfront and fix the roof, but you decide exactly who enters.
- **Example:** A Malaysian bank keeps customer records in its own KL data center using VMware, so data never leaves the building and it controls every patch.

### 2.1.3 — Hybrid Cloud

- **Definition:** Connects a private/on-prem environment to a public cloud over a secure network so data and apps move between them. Sensitive workloads stay private; variable workloads burst to public.
- **Why it matters:** "Best of both" and very common. Keep regulated data on-prem, use cheap public capacity for spikes, dev/test, backup, or DR. Exam pairs "hybrid" with *bursting, keep data private + scale public, gradual migration, DR/backup*. Trap: multi-cloud (two public providers) is NOT hybrid.
- **Analogy:** Own a house but rent a hotel room when relatives visit — live private, burst to rented space, then return.
- **Example:** A Malaysian university keeps student records on its private campus cloud, but bursts exam web servers onto AWS during exam season, then decommissions them.

### 2.1.4 — Community Cloud

- **Definition:** Multi-tenant, shared by several orgs with a COMMON concern (same industry, regulation, or mission — e.g., government agencies, hospitals). Infrastructure is jointly owned/governed or run by a third party on their behalf.
- **Why it matters:** Sits between public (anyone) and private (one org). Lets similar orgs share compliant infrastructure and cost while meeting shared rules. Exam pairs "community" with *shared industry, common compliance, shared cost, group with same mission*. Least commonly deployed of the four but appears as the "specific group" option.
- **Analogy:** A gated neighborhood clubhouse shared only by estate residents — not the whole public, not one family.
- **Example:** Several Malaysian state agencies jointly fund an in-country community cloud to share citizen services under the same public-sector rules, splitting cost and governing together.

### 2.1.5 — Multi-Cloud (common pattern)

- **Definition:** Uses services from MORE THAN ONE public provider at once (AWS + Azure + Google). Different from hybrid: multi-cloud = multiple public providers; hybrid = private + public.
- **Why it matters:** Avoids vendor lock-in, enables best-of-breed services, and adds resilience (one provider down, another carries load). Exam contrasts it with hybrid. Pair with *avoid lock-in, best-of-breed, resilience*.
- **Analogy:** Shopping at several supermarkets — rice from one, fruit from another, because each is cheapest for that item.
- **Example:** A Malaysian fintech runs core banking on AWS and analytics on Google BigQuery, splitting providers to avoid lock-in.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Public cloud for a startup (KL e-commerce):** New shop, no server budget, unpredictable traffic. Deploys fully on public cloud (AWS/Alibaba Singapore region). Pays by usage, scales for viral campaigns, shuts down off-season. No CapEx, elastic. Model: **Public**.
- **Private cloud for a regulated bank (on premises, Malaysia):** Must keep ledgers/KYC data in Malaysia under Bank Negara and PDPA. Builds private cloud in its own DC with strict auditing. No external tenant touches hardware. Model: **Private (on premises)**.
- **Hybrid cloud for a university exam system:** Keeps student PII on private campus cloud for residency, bursts public-facing exam app to public cloud during peak weeks, scales back. Model: **Hybrid**.
- **Community cloud for government agencies:** Several state health departments share a community cloud to exchange records under the same Ministry of Health policy, jointly funding and governing it. Model: **Community**.
- **Multi-cloud for resilience:** Enterprise runs production on AWS, warm DR on Azure, AI on Google — no single outage stops business. Pattern: **Multi-cloud**.

## SECTION 4 — COMPARISON TABLES

### Table 1 — Deployment Model Overview

| Model | Tenancy | Location | Ownership | Cost type | Best for |
|-------|---------|----------|-----------|-----------|----------|
| Public | Multi-tenant | Off premises (provider) | Provider | OpEx pay-as-you-go | Low cost, elastic, quick start |
| Private | Single-tenant | On premises or dedicated | One org | CapEx + OpEx | Compliance, control, sensitive data |
| Hybrid | Mixed (private + public) | Both | Org + provider | Mixed | Bursting, DR, gradual migration |
| Community | Multi-tenant (group) | On or off premises | Group of orgs | Shared cost | Common regulation/industry |

### Table 2 — Key Characteristics

| Characteristic | Public | Private | Hybrid | Community |
|----------------|--------|---------|--------|-----------|
| Scalability | Very high | Limited by hardware | High (burst) | Medium |
| Upfront cost | None | High | Medium | Shared/Medium |
| Control | Low | Very high | Medium-High | Medium (group) |
| Isolation | Logical (shared) | Strongest (physical) | Strong core | Within group |
| Management burden | Low | High | High | Shared |
| Compliance ease | Harder | Easiest | Mixed | Good for shared rules |

### Table 3 — Decision Triggers

| Business trigger | Recommended model |
|------------------|-------------------|
| No hardware budget, unpredictable traffic | Public |
| Strict residency/regulated data, own DC | Private (on premises) |
| Sensitive data + scale for spikes | Hybrid |
| Several orgs share regulation/mission | Community |
| Avoid lock-in, best-of-breed | Multi-cloud |
| Gradual migration from DC | Hybrid |

### Table 4 — Public vs Private vs Multi-Cloud

| Factor | Public | Private | Multi-cloud |
|--------|--------|---------|-------------|
| Vendor lock-in | High | None | Low |
| Data residency control | Low | High | Medium |
| Resilience to outage | Single point | Own risk | High |
| Skills needed | Low | High | High |

### Table 5 — On Premises vs Off Premises

| Aspect | On Premises | Off Premises (provider) |
|--------|-------------|-------------------------|
| Hardware owner | You | Provider |
| Physical access | You | Provider |
| Latency | Can be low (local) | Depends on internet |
| DR responsibility | Yours | Provider SLAs |
| Common with | Private | Public / Community |

### Table 6 — Exam Keyword Cheat Sheet

| Keyword | Likely answer |
|---------|---------------|
| "Low cost, no hardware, elastic, multi-tenant" | Public |
| "Sensitive data, full control, own DC" | Private (on premises) |
| "Keep data private BUT scale for spikes" | Hybrid |
| "Group of orgs, common regulation" | Community |
| "Avoid lock-in, two providers" | Multi-cloud |
| "Single-tenant dedicated" | Private |

## SECTION 5 — AWS PROVIDER MAPPING

Objective 2.1 is conceptual, so AWS has no single "deployment model service" — it provides building blocks for each model:

- **Public cloud (AWS itself):** The whole AWS global infra — EC2, S3, VPC. Multi-tenant, pay-as-you-go, off-premises. This IS the public cloud.
- **Private / on-prem on AWS (dedicated, single-tenant):**
  - **AWS Outposts** — run AWS infrastructure *inside your own data center* (on premises).
  - **AWS Dedicated Hosts** — physical servers dedicated to your account only.
  - **AWS Local Zones / Wavelength** — extend AWS to a metro/on-prem-adjacent location for low latency and residency.
- **Hybrid (connect private to public AWS):**
  - **AWS Direct Connect** — dedicated private network link from your DC to AWS.
  - **AWS Site-to-Site VPN** — encrypted tunnel between on-prem and VPC.
  - **AWS Storage Gateway** — bridge on-prem storage with AWS storage.
  - **VMware Cloud on AWS** — consistent environment across both.
- **Community / regulated enclave:** **AWS GovCloud (US)** is a separate AWS partition with stricter compliance — the closest analog to a regulated community enclave. (Malaysian residency is achieved by choosing the right Region, e.g., ap-southeast-1 Singapore.)
- **Multi-cloud enablement:** AWS has no native multi-cloud service, but **Terraform / CloudFormation** (IaC, 2.4) and **AWS Marketplace** help manage across providers.
- **Governance for any model:** **AWS Organizations, Control Tower, IAM** (access), **AWS Config** (compliance auditing) apply regardless of model.

Exam note: "Run AWS compute on your own premises?" → **AWS Outposts** (private/on-prem). "Securely connect your DC to AWS?" → **Direct Connect** or **Site-to-Site VPN** (hybrid).

## SECTION 6 — PRACTICE QUESTIONS

1. A startup has no budget to buy servers and needs to scale quickly for unpredictable traffic. Which deployment model is MOST appropriate?
   A. Private cloud
   B. Community cloud
   C. Public cloud
   D. On-premises data center

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Public cloud needs zero CapEx and offers elastic pay-as-you-go scaling — ideal for a budgetless startup with unpredictable traffic.
> **Why A is wrong:** Private cloud needs upfront hardware purchase (CapEx), which the startup has no budget for.
> **Why B is wrong:** Community cloud is shared by a defined group with common regulation — not a fit for a generic startup.
> **Why D is wrong:** On-premises data center requires buying and running servers — the opposite of "no budget."

2. A bank must keep customer financial data inside its own building to meet national regulation. Which model fits best?
   A. Public cloud
   B. Private cloud (on premises)
   C. Community cloud
   D. Multi-cloud

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Private cloud on premises keeps the bank's data under its own physical control, satisfying national regulation.
> **Why A is wrong:** Public cloud stores data in a shared, provider-operated environment — fails data-residency/control.
> **Why C is wrong:** Community cloud shares infra with other orgs — not suitable for a single bank's regulated data.
> **Why D is wrong:** Multi-cloud still runs on public providers, so the data leaves the building.

3. An organization keeps sensitive data on its own infrastructure but bursts extra web traffic to a provider during sales. This is:
   A. Public cloud
   B. Private cloud
   C. Hybrid cloud
   D. Community cloud

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Hybrid cloud keeps sensitive workloads private while bursting variable traffic to public — exactly the described pattern.
> **Why A is wrong:** Public-only would put the sensitive data in the public cloud, not keep it on own infra.
> **Why B is wrong:** Private-only has no public component to burst into.
> **Why D is wrong:** Community cloud is shared by a group, not a private-plus-public burst model.

4. Several hospitals share infrastructure because they follow the same healthcare regulation. This is a:
   A. Public cloud
   B. Private cloud
   C. Community cloud
   D. Hybrid cloud

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Community cloud is shared by several orgs with a common regulatory/mission need, like hospitals under one healthcare rule.
> **Why A is wrong:** Public cloud is open to anyone, not restricted to a defined group.
> **Why B is wrong:** Private cloud is dedicated to a single org, not shared across hospitals.
> **Why D is wrong:** Hybrid mixes private and public, not a shared group enclave.

5. Using AWS and Azure together to avoid depending on one vendor is called:
   A. Hybrid cloud
   B. Private cloud
   C. Community cloud
   D. Multi-cloud

> [!note]- Reveal Answer
> **Correct: D**
> **Why correct:** Multi-cloud uses multiple public providers (AWS + Azure) to avoid dependence on any single vendor.
> **Why A is wrong:** Hybrid is private + public, not two public providers.
> **Why B is wrong:** Private cloud is a single-tenant deployment model, not a multi-provider pattern.
> **Why C is wrong:** Community cloud is a shared-group deployment model, not multi-provider.

6. Which deployment model is typically multi-tenant and owned by a third party?
   A. Private cloud
   B. Public cloud
   C. On-premises
   D. Community cloud

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Public cloud is multi-tenant and operated by a third-party provider (AWS, Azure, etc.).
> **Why A is wrong:** Private cloud is single-tenant, dedicated to one org.
> **Why C is wrong:** On-premises is owned/operated by the org itself, not a third party.
> **Why D is wrong:** Community cloud is multi-tenant but shared by a defined group, not "typically" the general third-party model.

7. The strongest physical data isolation comes from which model?
   A. Public cloud
   B. Private cloud
   C. Community cloud
   D. Multi-cloud

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Private (single-tenant, dedicated hardware) gives the strongest physical isolation.
> **Why A is wrong:** Public cloud is logically isolated but shares physical hardware (multi-tenant).
> **Why C is wrong:** Community cloud shares hardware within the group — weaker than dedicated private.
> **Why D is wrong:** Multi-cloud spans providers but still uses shared public infra, not dedicated isolation.

8. Which AWS service lets you run AWS compute and storage inside your own data center?
   A. AWS Direct Connect
   B. AWS Outposts
   C. Amazon S3
   D. AWS Lambda

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** AWS Outposts brings AWS compute/storage racks into your own data center (on-prem private).
> **Why A is wrong:** Direct Connect links your DC to AWS; it doesn't run compute on premises.
> **Why C is wrong:** Amazon S3 is object storage in the AWS region, not on-prem.
> **Why D is wrong:** AWS Lambda is a serverless compute service in the AWS cloud, not on-prem.

9. A secure private network link connecting an on-prem data center to AWS is:
   A. AWS Direct Connect
   B. Amazon CloudFront
   C. AWS Outposts
   D. AWS IAM

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** AWS Direct Connect is a dedicated private network link from on-prem to AWS, the classic hybrid connector.
> **Why B is wrong:** CloudFront is a content-delivery (CDN) service, not a private DC link.
> **Why C is wrong:** Outposts runs AWS on premises; it isn't the network link itself.
> **Why D is wrong:** IAM is identity/access management, not networking.

10. Which model has the highest upfront (CapEx) cost?
    A. Public cloud
    B. Private cloud
    C. Community cloud
    D. Multi-tenant public

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Private cloud requires buying and maintaining your own hardware — the highest upfront CapEx.
> **Why A is wrong:** Public cloud has no upfront cost (pay-as-you-go OpEx).
> **Why C is wrong:** Community cloud splits cost among members, lowering each one's CapEx.
> **Why D is wrong:** "Multi-tenant public" is just public cloud — zero upfront cost.

11. A government group jointly funds and governs a cloud only its agencies may use. This is:
    A. Public cloud
    B. Private cloud
    C. Community cloud
    D. Hybrid cloud

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Community cloud is jointly funded/governed by a defined group (the agencies) for their exclusive use.
> **Why A is wrong:** Public cloud is open to all, not restricted to the agencies.
> **Why B is wrong:** Private cloud is one org's, not jointly governed by many agencies.
> **Why D is wrong:** Hybrid is private + public, not a shared-group enclave.

12. Which is NOT a characteristic of public cloud?
    A. Pay-as-you-go
    B. Multi-tenant
    C. Single-tenant dedicated hardware
    D. Elastic scaling

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** "Single-tenant dedicated hardware" describes private cloud; public is multi-tenant, so it is NOT a public characteristic.
> **Why A is wrong:** Pay-as-you-go is a core public-cloud trait.
> **Why B is wrong:** Multi-tenant is exactly how public cloud operates.
> **Why D is wrong:** Elastic scaling is a defining public-cloud benefit.

13. Hybrid cloud is best described as:
    A. Two public providers
    B. One org owning everything
    C. Private plus public connected securely
    D. A group sharing infrastructure

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Hybrid cloud is a private environment securely connected to a public one (e.g., via VPN/Direct Connect).
> **Why A is wrong:** Two public providers is multi-cloud, not hybrid.
> **Why B is wrong:** One org owning everything describes private cloud.
> **Why D is wrong:** A group sharing infrastructure is community cloud.

14. Data sovereignty (keeping data in a specific country) is easiest with:
    A. Public cloud
    B. Private cloud on premises
    C. Community cloud only
    D. Any public region

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Private cloud on premises gives full physical control of where data lives, satisfying data-sovereignty laws.
> **Why A is wrong:** Public cloud stores data in provider regions you don't physically control.
> **Why C is wrong:** Community cloud doesn't guarantee on-prem sovereignty; it can be off-prem.
> **Why D is wrong:** Any public region still places data in a provider's DC, not under your physical control.

15. Which AWS offering is a separate partition for stricter government compliance?
    A. AWS Outposts
    B. AWS GovCloud (US)
    C. Amazon VPC
    D. AWS Direct Connect

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** AWS GovCloud (US) is a separate AWS partition with stricter compliance for government/regulated workloads.
> **Why A is wrong:** Outposts brings AWS on premises; it isn't a compliance partition.
> **Why C is wrong:** Amazon VPC is network isolation, not a compliance enclave.
> **Why D is wrong:** Direct Connect is a network link, not a compliance partition.

16. The main risk of using only one public provider is:
    A. High CapEx
    B. Vendor lock-in
    C. Poor isolation
    D. No elasticity

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Relying on a single public provider creates vendor lock-in — hard to leave or port workloads.
> **Why A is wrong:** Public cloud has no CapEx; that's a benefit, not a risk.
> **Why C is wrong:** Public cloud is multi-tenant but isolation is logical, not the main single-provider risk.
> **Why D is wrong:** Public cloud is highly elastic; lack of elasticity isn't the single-provider risk.

17. A company wants best-of-breed AI from Google and cheap compute from AWS. This is:
    A. Hybrid cloud
    B. Private cloud
    C. Multi-cloud
    D. Community cloud

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Using multiple public providers (Google AI + AWS compute) is multi-cloud, picking best-of-breed per service.
> **Why A is wrong:** Hybrid is private + public, not two public providers.
> **Why B is wrong:** Private cloud is single-tenant, not multi-provider.
> **Why D is wrong:** Community cloud is a shared-group model, not multi-provider.

18. Which service helps bridge on-prem storage with AWS cloud storage in a hybrid setup?
    A. AWS Storage Gateway
    B. Amazon Route 53
    C. AWS Outposts
    D. AWS IAM

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** AWS Storage Gateway bridges on-prem storage with AWS storage (S3/EFS/FSx) in a hybrid setup.
> **Why B is wrong:** Route 53 is DNS/routing, not a storage bridge.
> **Why C is wrong:** Outposts runs AWS on premises; it isn't the storage-bridge service.
> **Why D is wrong:** IAM is access management, not storage.

19. Community cloud is best matched with which keyword?
    A. "Any public user"
    B. "One company only"
    C. "Group with common regulation"
    D. "Burst to public"

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Community cloud matches "a group with common regulation," shared by orgs with the same requirements.
> **Why A is wrong:** "Any public user" is the public-cloud keyword.
> **Why B is wrong:** "One company only" describes private cloud.
> **Why D is wrong:** "Burst to public" describes hybrid cloud.

20. Gradual migration from an on-prem data center to the cloud is best supported by which model?
    A. Public cloud only
    B. Private cloud only
    C. Hybrid cloud
    D. Community cloud

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Hybrid cloud keeps systems linked while migrating step-by-step from on-prem to cloud.
> **Why A is wrong:** Public-only doesn't preserve the on-prem link during a gradual move.
> **Why B is wrong:** Private-only stays on-prem; it doesn't represent the migration path to cloud.
> **Why D is wrong:** Community cloud is about shared groups, not migration sequencing.
