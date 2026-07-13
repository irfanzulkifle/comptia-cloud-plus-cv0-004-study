---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 4.2: Compliance and Regulation"
aliases: [Objective-4.2-Compliance-Regulation, Domain-4.2-Compliance-Regulation, Compliance, Regulation, Data-Sovereignty, Data-Ownership, Data-Locality, Data-Classification, Data-Retention, Litigation-Hold, SOC2, PCI-DSS, ISO-27001, CSA]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 4.2
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-4.2, security, compliance, regulation, data-sovereignty, data-ownership, data-locality, data-classification, data-retention, litigation-hold, soc2, pci-dss, iso-27001, csa, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 4.0 Security
## 📘 Objective 4.2: Compare and Contrast Aspects of Compliance and Regulation

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0 (lines 422-435)
> **Objective 4.2 Focus:** Data sovereignty, ownership, locality, classification, retention (litigation/contractual/regulatory) — and industry standards (SOC 2, PCI DSS, ISO 27001, Cloud Security Alliance).

## SECTION 1 — Exam Objectives

- **Objective 4.2:** Compare and contrast aspects of compliance and regulation.
- **Data sovereignty:** *Whose laws* apply — set by where the data is physically stored, not the owner's country.
- **Data ownership:** *Who* owns the data — the customer, not the cloud provider (the provider is just a custodian).
- **Data locality:** *Where* data physically lives — the region or country you choose.
- **Data classification:** *How sensitive* data is (Public / Internal / Confidential / Restricted) — drives how you protect it.
- **Data retention:** *How long* you keep data — including the three special holds: litigation, contractual, regulatory.
- **Industry standards:** External rulebooks to follow — PCI DSS (cards), ISO 27001 (ISMS), SOC 2 (service orgs), CSA (cloud guidance).
- **Goal:** Pick the right cloud config (region, controls, retention) so the correct rule applies and you stay legal and certified.

## SECTION 2 — EXAM KNOWLEDGE

### 4.2.1 — Data Sovereignty
- **Definition:** Data is governed by the laws of the country where it is *physically stored* — not the company's home country.
- **Why it matters:** Storing EU data in the US without safeguards breaks GDPR; fines are enormous.
- **Analogy:** A book in a library follows that library's rules, no matter who owns the book.
- **Example:** EU citizen PII must stay in `eu-central-1` (Frankfurt) so EU law applies.

### 4.2.2 — Data Ownership
- **Definition:** The *customer* owns their data; the cloud provider owns only the infrastructure and acts as a custodian.
- **Why it matters:** Clear ownership means someone is accountable and controls access, encryption, and deletion.
- **Analogy:** Renting a safety-deposit box — the bank owns the box, you own what's inside.
- **Example:** The customer (not AWS/Azure) owns the customer database on a provider's servers.

### 4.2.3 — Data Locality
- **Definition:** The *physical where* — the specific region, country, or data center where data lives.
- **Why it matters:** Drives latency, supports sovereignty, and sets disaster-recovery placement.
- **Analogy:** The locker's street address — separate from the rule that governs it.
- **Example:** "Data must stay in Canada" → pick a Canadian region (`ca-central-1`).

### 4.2.4 — Data Classification
- **Definition:** Labeling data by sensitivity (Public / Internal / Confidential / Restricted) to right-size protection.
- **Why it matters:** Match security effort to risk — protect PII strongly, don't over-spend on public data.
- **Analogy:** Filing cabinets — public = lobby shelf, restricted = locked vault.
- **Example:** A public marketing PDF needs minimal controls; PII/financial data needs encryption + IAM + audit.

### 4.2.5 — Data Retention
- **Definition:** How long data is kept before delete/archive, driven by business, legal, and regulatory needs.
- **Why it matters:** Keep what's required, dispose of the rest — cuts risk and storage cost.
- **Analogy:** A mailbox you are forbidden to empty while a lawsuit is pending.
- **Example:** Litigation hold freezes deletion; a 7-year law overrides a 1-year internal policy.

### 4.2.6 — Industry Standards
- **Definition:** External rulebooks you follow for certain data — PCI DSS, ISO 27001, SOC 2, CSA.
- **Why it matters:** Needed to win enterprise customers and prove your security controls.
- **Analogy:** Different badges proving you meet different safety standards.
- **Example:** PCI DSS for card data; ISO 27001 for an ISMS; SOC 2 report for service orgs; CSA guidance for cloud.

### 4.2.7 — Cross-Objective Links
- **2.5 (Provisioning):** Sovereignty/locality decide which region you provision — in-region for compliance.
- **3.3 (Backup/Recovery):** Retention holds drive backup retention, archival, and WORM copies.
- **4.3 / 4.4 / 4.5 (IAM/Controls):** Classification drives access controls and encryption requirements.
- **4.1 (Vulnerability management):** Standards (ISO/PCI) often mandate periodic scanning.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **EU data sovereignty (AWS):** A German company stores citizen PII in `eu-central-1` (Frankfurt) so EU/GDPR law applies; moving it to a US region would need SCCs.
- **PCI DSS scope reduction (Azure):** An e-commerce site uses a payment processor + tokenization so card data never enters its environment, shrinking its PCI burden.
- **Litigation hold (GCP):** After a lawsuit, GCS/BigQuery deletion rules are suspended (WORM) so logs are preserved until the case closes.
- **Regulatory retention (Healthcare):** A clinic keeps patient records 7 years per HIPAA/regulatory minimum, overriding a shorter internal policy.
- **ISO 27001 / SOC 2 (Sales gate):** A B2B SaaS wins enterprise deals by obtaining ISO 27001 and a SOC 2 Type II report.

## SECTION 4 — COMPARISON TABLES

### 4.1 — Sovereignty vs Locality vs Ownership

| Concept | Answers | Driven by |
|---|---|---|
| Data sovereignty | Whose **laws** apply? | Jurisdiction of storage location |
| Data locality | Where **physically** stored? | Region/country chosen |
| Data ownership | Who **owns** the data? | Customer (provider is custodian) |

### 4.2 — Data Classification Tiers

| Tier | Sensitivity | Example | Controls |
|---|---|---|---|
| Public | None | Marketing PDF | Minimal |
| Internal | Low | Internal memos | Basic access |
| Confidential | High | Customer lists | Encryption + IAM |
| Restricted | Highest | PII, financial, IP | Strongest (KMS, IAM, audit) |

### 4.3 — Retention Drivers

| Driver | Source | Effect on retention |
|---|---|---|
| Normal policy | Internal | Scheduled delete/archive |
| Litigation hold | Legal case | **Freeze** deletion |
| Contractual | Contract/MSA | Minimum set by agreement |
| Regulatory | Law (GDPR/HIPAA) | Minimum set by law |

### 4.4 — Industry Standards

| Standard | Scope | For |
|---|---|---|
| SOC 2 | Service-org audit (Trust criteria) | SaaS/cloud providers |
| PCI DSS | Cardholder data security | Anyone handling cards |
| ISO 27001 | ISMS (security mgmt framework) | Orgs seeking cert |
| CSA | Cloud security best practice | Cloud community |

### 4.5 — Compliance → Action Map

| Scenario | Action |
|---|---|
| EU citizen data | In-region (EU) for sovereignty |
| Credit cards | PCI DSS or tokenize to reduce scope |
| Lawsuit pending | Litigation hold (freeze delete) |
| 7-yr legal mandate | Regulatory retention (archive) |
| Restricted/PII | Encrypt + tight IAM + audit |

### 4.6 — GDPR vs HIPAA vs PCI DSS

| Regulation | Applies to | Key requirement | Retention driver |
|---|---|---|---|
| GDPR | EU personal data | Lawful basis, data-subject rights, sovereignty | Regulatory |
| HIPAA | US protected health info | Safeguards, minimum-necessary, audit | Regulatory |
| PCI DSS | Cardholder data | Encrypt, segment, no CVV storage | Industry standard |

## SECTION 5 — AWS PROVIDER MAPPING

AWS-ONLY mapping for compliance and regulation (data residency, audit, governance):

**AWS Artifact** — the self-service portal for **on-demand compliance reports and certifications**. Download AWS's SOC 2, ISO 27001, PCI DSS, and other third-party audit reports and agreements (such as the Business Associate Addendum for HIPAA). This is how you *prove* AWS controls to your auditors and customers.

**AWS Config** — a **configuration compliance and governance** service. Use Config Rules to continuously evaluate whether resources stay in compliance (e.g., "S3 buckets must be in an approved region," "encryption must be enabled"). Config gives you a configuration history and audit trail of changes — directly supporting retention and audit requirements.

**AWS Audit Manager** — helps you **collect evidence** continuously to prepare for audits against frameworks (ISO 27001, PCI DSS, GDPR, HIPAA). It maps AWS resource data to control objectives and produces audit-ready reports, reducing the manual effort of proving compliance.

**AWS GovCloud (US)** — a **separate AWS partition** designed to host sensitive US government and regulated workloads. It has its own Regions, account boundaries, and stricter access controls (US persons only), supporting data-residency and sovereignty requirements for regulated US data.

**Regions for data residency** — choose an AWS Region to pin data physically and satisfy **data locality/sovereignty**. Examples: `eu-central-1` (Frankfurt) or `eu-west-1` (Ireland) for EU data; `ca-central-1` (Canada) for Canadian residency; `us-gov-west-1` / `us-gov-east-1` (GovCloud) for US-regulated data. Region selection is the primary control for keeping data within a required jurisdiction.

**Supporting AWS services:** S3 Object Lock (WORM) for litigation/regulatory holds; AWS KMS for encrypting Restricted/PII data; IAM for ownership/access control; S3 Lifecycle + Glacier Vault Lock for retention/archival; AWS Organizations + Control Tower + Service Control Policies (SCPs) to enforce region and compliance guardrails organization-wide.

**Key takeaway:** On AWS, compliance is achieved by (1) downloading attestations from **Artifact**, (2) enforcing and recording posture with **Config** + **Audit Manager**, (3) isolating regulated workloads in **GovCloud**, and (4) pinning data to the correct **Region** for residency/sovereignty.

## SECTION 6 — PRACTICE QUESTIONS

1. A company headquartered in the US stores EU citizen PII. Which concept determines whose privacy laws govern that data?
   - **A.** Data ownership
   - **B.** Data locality
   - **C.** Data sovereignty
   - **D.** Data classification

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Data sovereignty is governed by the laws of the country where the data is physically stored — here, the EU (GDPR).

**Why A is wrong:** Ownership is about *who* owns the data, not which jurisdiction's laws apply.
**Why B is wrong:** Locality is the physical *where*; sovereignty is the legal *whose laws* that follows from it.
**Why D is wrong:** Classification labels sensitivity (Public/Restricted), not the governing legal regime.

</details>

2. Who owns the customer database stored on a cloud provider's servers?
   - **A.** The cloud provider
   - **B.** The customer
   - **C.** The data center operator
   - **D.** The government of the storage region

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The customer owns their data; the cloud provider owns only the infrastructure and acts as a custodian.

**Why A is wrong:** The provider is merely a custodian of the infrastructure, not the data owner.
**Why C is wrong:** The data-center operator is a facilities/custody role, not the data owner.
**Why D is wrong:** The storage region's government sets applicable law (sovereignty), not ownership.

</details>

3. "Data must remain physically within Canada." This requirement is best described as:
   - **A.** Data sovereignty
   - **B.** Data locality
   - **C.** Data ownership
   - **D.** Data classification

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Data locality is the physical *where* — the specific region/country you choose (a Canadian region).

**Why A is wrong:** Sovereignty is the legal consequence; locality is the physical placement requirement itself.
**Why C is wrong:** Ownership is about who owns the data, unrelated to the physical placement rule.
**Why D is wrong:** Classification is sensitivity labeling, not a geographic placement requirement.

</details>

4. Which data classification tier requires the strongest controls such as encryption, tight IAM, and audit?
   - **A.** Public
   - **B.** Internal
   - **C.** Confidential
   - **D.** Restricted

<details>
<summary>Reveal Answer</summary>

**Correct: D**

**Why correct:** Restricted is the highest tier (PII, financial, IP) and needs the strongest controls: KMS, tight IAM, audit.

**Why A is wrong:** Public data needs only minimal controls.
**Why B is wrong:** Internal is low sensitivity with basic access controls only.
**Why C is wrong:** Confidential needs strong controls but less than the Restricted tier.

</details>

5. A lawsuit is filed and your 90-day log-deletion policy would purge relevant records. What should you do?
   - **A.** Delete per policy to save cost
   - **B.** Issue a litigation hold to freeze deletion
   - **C.** Archive for 1 extra year only
   - **D.** Reclassify the logs as public

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A litigation hold freezes deletion of the affected data until the legal case closes.

**Why A is wrong:** Deleting relevant records during litigation is spoliation and illegal.
**Why C is wrong:** Ad-hoc archiving does not formally freeze deletion the way a hold does.
**Why D is wrong:** Reclassifying as public destroys confidentiality and does not preserve evidence.

</details>

6. Internal retention policy is 1 year, but a regulation mandates 7 years. Which wins?
   - **A.** Internal policy (1 year)
   - **B.** Regulatory requirement (7 years)
   - **C.** Whichever is cheaper
   - **D.** Litigation hold

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Regulatory retention sets a legally binding minimum that overrides an internal policy.

**Why A is wrong:** An internal policy cannot override a law; the 7-year legal minimum wins.
**Why C is wrong:** Cost is not a valid basis to violate a regulatory retention mandate.
**Why D is wrong:** A litigation hold freezes deletion for a case; here the driver is a regulation, not litigation.

</details>

7. A client contract requires you to keep records for 5 years. This is an example of:
   - **A.** Regulatory retention
   - **B.** Litigation hold
   - **C.** Contractual retention
   - **D.** Normal policy

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Contractual retention is driven by an agreement/MSA with the client.

**Why A is wrong:** Regulatory retention comes from a law, not a private contract.
**Why B is wrong:** A litigation hold is triggered by a legal case, not a routine contract term.
**Why D is wrong:** A normal policy is the organization's own internal schedule, not a client requirement.

</details>

8. Which standard audits a service organization's controls against Trust Services Criteria?
   - **A.** PCI DSS
   - **B.** ISO 27001
   - **C.** SOC 2
   - **D.** CSA

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** SOC 2 reports on a service organization's controls against the Trust Services Criteria.

**Why A is wrong:** PCI DSS governs cardholder data security, not service-org Trust criteria.
**Why B is wrong:** ISO 27001 certifies an ISMS framework, not a service-org Trust Services audit.
**Why D is wrong:** CSA publishes cloud best-practice guidance, not a service-org audit standard.

</details>

9. An organization that stores credit-card data must comply with:
   - **A.** ISO 27001
   - **B.** PCI DSS
   - **C.** SOC 2
   - **D.** CSA STAR only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** PCI DSS governs the security of cardholder data for anyone handling cards.

**Why A is wrong:** ISO 27001 is an ISMS framework, not specific to card data.
**Why C is wrong:** SOC 2 is for service-org controls, not a card-data mandate.
**Why D is wrong:** CSA STAR is advisory guidance, not the binding card-data standard.

</details>

10. Which is a certifiable framework for an Information Security Management System?
   - **A.** SOC 2
   - **B.** PCI DSS
   - **C.** ISO 27001
   - **D.** Cloud Security Alliance

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** ISO 27001 is the certifiable framework for an Information Security Management System (ISMS).

**Why A is wrong:** SOC 2 is an audit report, not an ISMS certification framework.
**Why B is wrong:** PCI DSS is a card-data security standard, not an ISMS framework.
**Why D is wrong:** CSA is an advisory body, not a certifiable ISMS standard.

</details>

11. The Cloud Security Alliance (CSA) is best described as:
   - **A.** A government regulation
   - **B.** A nonprofit publishing cloud-security best-practice guidance
   - **C.** A credit-card standard
   - **D.** A service-organization audit

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** CSA is a nonprofit that publishes cloud-security best-practice guidance (e.g., CSA STAR).

**Why A is wrong:** CSA is not a government regulation; it is voluntary guidance.
**Why C is wrong:** Credit-card standards are PCI DSS, not CSA.
**Why D is wrong:** Service-organization audits are SOC 2, not CSA.

</details>

12. To reduce PCI DSS scope, a company should:
   - **A.** Store CVV for faster checkout
   - **B.** Use a payment processor / tokenization so card data leaves its environment
   - **C.** Disable encryption on the card database
   - **D.** Keep card numbers in a spreadsheet

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Using a processor/tokenization keeps card data out of your environment, shrinking PCI scope.

**Why A is wrong:** Storing CVV is prohibited by PCI DSS and *increases* scope.
**Why C is wrong:** Disabling encryption on card data violates PCI and raises scope/risk.
**Why D is wrong:** A plaintext spreadsheet of card numbers is a major PCI violation.

</details>

13. Which AWS service provides on-demand compliance reports such as SOC 2 and ISO 27001?
   - **A.** AWS Config
   - **B.** AWS Artifact
   - **C.** AWS Audit Manager
   - **D.** AWS GovCloud

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS Artifact is the self-service portal for on-demand compliance reports and agreements (SOC 2, ISO 27001, etc.).

**Why A is wrong:** Config evaluates configuration compliance, but does not deliver attestation reports.
**Why C is wrong:** Audit Manager helps collect audit evidence, not download third-party reports.
**Why D is wrong:** GovCloud is an isolated partition for regulated workloads, not a report portal.

</details>

14. Which AWS service continuously evaluates resource configuration against compliance rules?
   - **A.** AWS Config
   - **B.** AWS Artifact
   - **C.** AWS IAM
   - **D.** AWS KMS

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** AWS Config continuously evaluates resources against Config Rules and records configuration history/audit trail.

**Why B is wrong:** Artifact delivers reports; it does not evaluate live resource configuration.
**Why C is wrong:** IAM controls identity/access, not configuration-compliance evaluation.
**Why D is wrong:** KMS manages encryption keys, not compliance configuration rules.

</details>

15. An S3 bucket must preserve records immutably to satisfy a legal hold. Which feature supports this?
   - **A.** S3 Lifecycle
   - **B.** S3 Object Lock (WORM)
   - **C.** S3 Transfer Acceleration
   - **D.** S3 Versioning only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** S3 Object Lock provides WORM (write-once-read-many) immutability needed for legal/litigation holds.

**Why A is wrong:** Lifecycle transitions/expire objects; it does not make them immutable.
**Why C is wrong:** Transfer Acceleration speeds uploads; it has nothing to do with immutability.
**Why D is wrong:** Versioning alone does not prevent deletion/overwrite; Object Lock is required for WORM.

</details>

16. A US government-regulated workload with strict access controls is best placed in:
   - **A.** A standard commercial Region
   - **B.** AWS GovCloud (US)
   - **C.** An EU Region
   - **D.** A Canadian Region

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS GovCloud (US) is a separate partition with stricter access controls for US government/regulated workloads.

**Why A is wrong:** Standard commercial Regions lack the US-persons-only isolation GovCloud provides.
**Why C is wrong:** An EU Region would subject the data to EU law, wrong for US-regulated workloads.
**Why D is wrong:** A Canadian Region applies Canadian residency, not US government isolation.

</details>

17. To satisfy EU data sovereignty, you should store EU PII in:
   - **A.** us-east-1
   - **B.** eu-central-1 (Frankfurt)
   - **C.** ap-southeast-1
   - **D.** Any Region with encryption

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Storing in an EU Region (e.g., eu-central-1) keeps the data under EU/GDPR law.

**Why A is wrong:** us-east-1 places data under US jurisdiction, violating EU sovereignty without safeguards.
**Why C is wrong:** ap-southeast-1 is an Asia-Pacific Region, not EU law.
**Why D is wrong:** Encryption does not change the storage jurisdiction that determines applicable law.

</details>

18. Data classification primarily helps you:
   - **A.** Decide who owns the data
   - **B.** Match security effort to data sensitivity and reduce waste
   - **C.** Determine the storage Region
   - **D.** Replace retention policies

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Classification labels sensitivity so you match security effort to risk and avoid over/under-protecting.

**Why A is wrong:** Ownership is a separate concept (customer owns data), not set by classification.
**Why C is wrong:** Region selection follows sovereignty/locality, not classification directly.
**Why D is wrong:** Classification informs controls; it does not replace retention requirements.

</details>

19. Under a litigation hold, normal deletion policies:
   - **A.** Continue as scheduled
   - **B.** Are frozen for the affected data
   - **C.** Are shortened
   - **D.** Are converted to regulatory retention

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A litigation hold overrides and freezes deletion for the affected data until the case closes.

**Why A is wrong:** Continuing scheduled deletion would destroy evidence and violate the hold.
**Why C is wrong:** The hold lengthens preservation; it does not shorten retention.
**Why D is wrong:** The hold is case-driven, not a conversion to a regulatory minimum.

</details>

20. Which combination best supports proving AWS compliance to an auditor?
   - **A.** Artifact + Config + Audit Manager
   - **B.** EC2 + S3 only
   - **C.** Route 53 + CloudFront
   - **D.** Lambda + DynamoDB

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Artifact supplies attestations, Config records posture, and Audit Manager collects evidence — together proving compliance.

**Why B is wrong:** EC2 + S3 are compute/storage, not compliance-proof tooling.
**Why C is wrong:** Route 53 + CloudFront are DNS/CDN, unrelated to audit evidence.
**Why D is wrong:** Lambda + DynamoDB are compute/data services, not compliance evidence sources.

</details>
