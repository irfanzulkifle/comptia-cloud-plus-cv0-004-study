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
   Answer: C — Public cloud offers zero CapEx and elastic pay-as-you-go scaling.

2. A bank must keep customer financial data inside its own building to meet national regulation. Which model fits best?
   A. Public cloud
   B. Private cloud (on premises)
   C. Community cloud
   D. Multi-cloud
   Answer: B — Private on-premises keeps data under the org's physical control.

3. An organization keeps sensitive data on its own infrastructure but bursts extra web traffic to a provider during sales. This is:
   A. Public cloud
   B. Private cloud
   C. Hybrid cloud
   D. Community cloud
   Answer: C — Hybrid mixes a private core with public-cloud bursting.

4. Several hospitals share infrastructure because they follow the same healthcare regulation. This is a:
   A. Public cloud
   B. Private cloud
   C. Community cloud
   D. Hybrid cloud
   Answer: C — Community cloud serves a group with common regulatory needs.

5. Using AWS and Azure together to avoid depending on one vendor is called:
   A. Hybrid cloud
   B. Private cloud
   C. Community cloud
   D. Multi-cloud
   Answer: D — Multi-cloud uses multiple public providers to avoid lock-in.

6. Which deployment model is typically multi-tenant and owned by a third party?
   A. Private cloud
   B. Public cloud
   C. On-premises
   D. Community cloud
   Answer: B — Public cloud is multi-tenant, operated by an external provider.

7. The strongest physical data isolation comes from which model?
   A. Public cloud
   B. Private cloud
   C. Community cloud
   D. Multi-cloud
   Answer: B — Private (single-tenant) gives the strongest isolation.

8. Which AWS service lets you run AWS compute and storage inside your own data center?
   A. AWS Direct Connect
   B. AWS Outposts
   C. Amazon S3
   D. AWS Lambda
   Answer: B — AWS Outposts brings AWS infrastructure on premises (private).

9. A secure private network link connecting an on-prem data center to AWS is:
   A. AWS Direct Connect
   B. Amazon CloudFront
   C. AWS Outposts
   D. AWS IAM
   Answer: A — Direct Connect is the dedicated on-prem-to-AWS link for hybrid.

10. Which model has the highest upfront (CapEx) cost?
    A. Public cloud
    B. Private cloud
    C. Community cloud
    D. Multi-tenant public
    Answer: B — Private cloud requires buying and maintaining your own hardware.

11. A government group jointly funds and governs a cloud only its agencies may use. This is:
    A. Public cloud
    B. Private cloud
    C. Community cloud
    D. Hybrid cloud
    Answer: C — Community cloud is shared by a defined group with common goals.

12. Which is NOT a characteristic of public cloud?
    A. Pay-as-you-go
    B. Multi-tenant
    C. Single-tenant dedicated hardware
    D. Elastic scaling
    Answer: C — Single-tenant dedicated hardware describes private, not public.

13. Hybrid cloud is best described as:
    A. Two public providers
    B. One org owning everything
    C. Private plus public connected securely
    D. A group sharing infrastructure
    Answer: C — Hybrid is a private environment linked to a public one.

14. Data sovereignty (keeping data in a specific country) is easiest with:
    A. Public cloud
    B. Private cloud on premises
    C. Community cloud only
    D. Any public region
    Answer: B — On-prem private gives full physical control of data location.

15. Which AWS offering is a separate partition for stricter government compliance?
    A. AWS Outposts
    B. AWS GovCloud (US)
    C. Amazon VPC
    D. AWS Direct Connect
    Answer: B — AWS GovCloud (US) is the regulated/compliance enclave.

16. The main risk of using only one public provider is:
    A. High CapEx
    B. Vendor lock-in
    C. Poor isolation
    D. No elasticity
    Answer: B — Relying on one provider creates vendor lock-in risk.

17. A company wants best-of-breed AI from Google and cheap compute from AWS. This is:
    A. Hybrid cloud
    B. Private cloud
    C. Multi-cloud
    D. Community cloud
    Answer: C — Using multiple public providers is multi-cloud.

18. Which service helps bridge on-prem storage with AWS cloud storage in a hybrid setup?
    A. AWS Storage Gateway
    B. Amazon Route 53
    C. AWS Outposts
    D. AWS IAM
    Answer: A — Storage Gateway connects on-prem storage to AWS storage.

19. Community cloud is best matched with which keyword?
    A. "Any public user"
    B. "One company only"
    C. "Group with common regulation"
    D. "Burst to public"
    Answer: C — Community cloud serves a group sharing common requirements.

20. Gradual migration from an on-prem data center to the cloud is best supported by which model?
    A. Public cloud only
    B. Private cloud only
    C. Hybrid cloud
    D. Community cloud
    Answer: C — Hybrid allows step-by-step migration while keeping systems linked.
