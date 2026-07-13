---
title: CompTIA Cloud+ CV0-004 — Objective 6.3
objective: "6.3 Given a scenario, troubleshoot security issues."
domain: "6.0 Troubleshooting"
weight: "Part of Troubleshooting domain"
tags: [Cloud+, CV0-004, Troubleshooting, Security, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 6.3
## Troubleshoot Security Issues

> **Objective statement:** *"Given a scenario, troubleshoot security issues."*
> **Domain:** 6.0 Troubleshooting
> **What it tests:** A symptom-to-cause domain for security failures. You are given a security incident or control failure (breach, leaked key, vuln, rogue software) and must name the root cause from the official list and the remediation. Expect PBQ "what went wrong and how do you contain it" questions.

---

## SECTION 1 — Exam Objectives


- **In one breath:** Security troubleshooting = figuring out WHY a control failed or a breach happened, using the official sub-objective list, then containing and fixing it.
- **The 6.3 sub-objectives (verbatim from the official exam objectives):**
  - **6.3.1 Cipher suite deprecations** — An encryption cipher/protocol was retired; old clients/endpoints can no longer connect securely.
  - **6.3.2 Authorization issues** — A principal has the wrong level of access.
    - **6.3.2a Privilege escalation** — A user/process gains rights above what they should have.
    - **6.3.2b Unauthorized access** — An identity reaches resources it should not.
  - **6.3.3 Authentication issues** — A principal cannot prove who they are (or proves it via a compromised method).
    - **6.3.3a Leaked credentials** — Password/key/secret was exposed and is being abused.
  - **6.3.4 Software vulnerability issues** — A known flaw (CVE/CVSS) in code/dependency is exploitable.
  - **6.3.5 Unauthorized software** — Unapproved/rogue software running in the environment (shadow IT, malware).
- **Mental model — symptom → cause:** Old client can't connect after cipher change → Cipher deprecation (6.3.1) · User can do more than role allows → Privilege escalation (6.3.2a) · Outside identity reaches data → Unauthorized access (6.3.2b) · Can't log in / token rejected → Auth issue (6.3.3) · Key in public repo abused → Leaked credentials (6.3.3a) · Exploit of known CVE → Software vuln (6.3.4) · Unsanctioned app/malware → Unauthorized software (6.3.5).
- **Note:** 6.3.2/6.3.3 pair naturally — *authentication* proves identity, *authorization* decides what that identity may do. A failure in either leaves the environment exposed.

---

## SECTION 2 — EXAM KNOWLEDGE

### 6.3.1 — CIPHER SUITE DEPRECATIONS
- **Definition:**
  - Provider / load balancer / standard retires a weak encryption algorithm or TLS version
  - Common cuts: SSLv3, TLS 1.0/1.1, RC4, 3DES, SHA-1
  - After the cutoff, any client still negotiating the old suite can't connect
  - About the *encryption* layer (data in transit) — sibling of 6.2.6
- **Why it matters:**
  - Exam signal: "provider disabled TLS 1.0, legacy integration throws handshake failures" → 6.3.1
  - Fix: update clients to TLS 1.2+ with modern ciphers before/at the deadline
  - Announced far ahead but still breaks unpatched integrations (common audit finding)
  - Trap: not protocol incompatibility (6.2.5) — deprecation = provider removed it for *everyone* on a date
- **Analogy:**
  - Bank says "from Monday only pen signatures, no invisible ink" — method gone for all, you must switch
- **Example (cloud):**
  - Load balancer drops TLS 1.0 + RC4 → legacy POS logs "no cipher in common"
  - Fix: upgrade POS to TLS 1.2 + AES-GCM; re-test handshake
  - Signal: "old clients break on a known cutoff" = cipher deprecation

### 6.3.2 — AUTHORIZATION ISSUES (PRIVILEGE ESCALATION & UNAUTHORIZED ACCESS)
- **Definition:**
  - Authorization = what an *authenticated* identity is allowed to do
  - **Privilege escalation (6.3.2a):** gains rights above need — `*` wildcard, container as root, rights-widening exploit
  - **Unauthorized access (6.3.2b):** identity reaches a resource it shouldn't — reads another's S3 bucket, cross-tenant data
  - Both are access-control failures; identity is known but permissions are wrong/abused
- **Why it matters:**
  - Exam: "junior admin deletes prod because policy has `*`" → privilege escalation (6.3.2a)
  - Exam: "outside user reads private bucket via bad bucket policy" → unauthorized access (6.3.2b)
  - Fix both: least-privilege — scope IAM/RBAC, drop wildcards, separate roles per workload
  - Real life: over-broad perms = #1 cloud breach cause (Capital One 2019 = misconfigured role)
  - Trap: don't call escalation "leaked credentials" — it's *excess rights*, not a stolen secret
- **Analogy:**
  - Authorization = keys on your ring. Escalation = handed master key when you needed supply-closet key. Unauthorized access = someone uses a key to enter a room never given
- **Example (cloud):**
  - Dev role `s3:*` on all buckets → deletes/exfiltrates prod = privilege escalation (6.3.2a)
  - Bucket policy `"Principal": "*"` → anonymous reads objects = unauthorized access (6.3.2b)
  - Fix: replace `*` with explicit perms, block-public-access, run IAM Access Analyzer

### 6.3.3 — AUTHENTICATION ISSUES (LEAKED CREDENTIALS)
- **Definition:**
  - Authentication = proving *who you are*
  - Headline sub-case: **leaked credentials (6.3.3a)** — secret exposed (public repo, logs, phishing, hardcoded) and abused
  - Other failures: MFA not enforced, expired/rotated keys, broken SSO/SAML, shared accounts
  - Once a leaked cred is used, attacker is "authenticated" — damage then becomes an *authorization* problem (6.3.2)
- **Why it matters:**
  - Exam: "access key in GitHub repo now makes API calls from unknown IP" → leaked credentials (6.3.3a)
  - Fix: rotate/disable key, revoke sessions, find every use, move to short-lived roles (instance profiles / OIDC)
  - Real life: leaked creds = #1 initial-access vector (CircleCI 2023 began with hardcoded secret)
  - Trap: it *is* unauthorized access, but name root cause = leaked credential (6.3.3a), kill secret first
- **Analogy:**
  - ID badge cloned — door accepts the clone; real problem is the badge copied and still valid. Disable + reissue (rotate the key)
- **Example (cloud):**
  - Long-lived key hardcoded in image, pushed to public registry → `AssumeRole` from foreign IP at 3am
  - Fix: disable/rotate key, revoke assumed-role sessions, scan repos, use IAM roles / OIDC tokens

### 6.3.4 — SOFTWARE VULNERABILITY ISSUES
- **Definition:**
  - Known flaw in code / library / container image / dependency an attacker can exploit
  - Tracked as **CVE** (Common Vulnerabilities and Exposures) + severity **CVSS** score
  - Examples: unpatched Log4j (CVE-2021-44228), outdated OpenSSL, dependency with known RCE
  - Not misconfig or stolen creds — a *defect in the software* that must be patched/upgraded
- **Why it matters:**
  - Exam: "app exploited via CVE-2021-44228, Log4j never patched" → software vulnerability (6.3.4)
  - Fix: patch/upgrade component, rebuild images, add CVE scanning to CI
  - Real life: unpatched CVEs = top path to ransomware/breach; CVSS prioritizes (9.8 > 4.0)
  - Ties to 4.1 (vuln mgmt) + 4.4 (security best practices)
  - Trap: don't call a CVE exploit "unauthorized access" — root cause = vulnerability, fix by patching
- **Analogy:**
  - Broken lock on back door — IAM fine, key not stolen, but lock defective. Fix (patch) the lock, don't reissue keys
- **Example (cloud):**
  - Public service on outdated logging lib exploited for RCE via CVE-2021-44228
  - Fix: upgrade lib, rebuild/redeploy, rotate exposed secrets, enable image/dependency scanning

### 6.3.5 — UNAUTHORIZED SOFTWARE
- **Definition:**
  - Any program/agent/container/workload running that was NOT approved
  - Forms: shadow IT outside change control, dev's personal tool, malware/ransomware
  - Cloud examples: unapproved AMI, container from untrusted registry, crypto-miner from leaked creds
  - Violates baselines, expands attack surface; may carry a vuln (6.3.4) or exfiltrate data
- **Why it matters:**
  - Exam: "unknown process at 100% CPU mining crypto" / "container from unknown registry in prod" → 6.3.5
  - Fix: kill/quarantine, find entry (often leaked creds 6.3.3a or vuln 6.3.4), block untrusted images, alert on drift
  - Real life: compliance violation + leading indicator of compromise
  - Trap: don't treat crypto-mining as "high CPU from legit load" — investigate the *process*
- **Analogy:**
  - Uninvited guest sets up shop in your office — door/locks fine, but someone unapproved operates inside. Evict + lock down
- **Example (cloud):**
  - CloudWatch shows `kdevtmpfsi` (XMRig miner) at 100% CPU, no manifest describes it
  - Fix: terminate/quarantine, revoke abused creds + patch entry vector, block untrusted images, add anomaly alerting
## SECTION 3 — REAL WORLD CLOUD EXAMPLES


- **3.1 Legacy POS can't complete TLS handshake after policy drops TLS 1.0 → Cipher deprecation (6.3.1):** **Symptom:** After a load-balancer security policy disables TLS 1.0 and RC4, older point-of-sale terminals fail the handshake with "no cipher in common" and cannot process payments. **Diagnosis:** **Cipher suite deprecation (6.3.1)** — the weak suite was retired provider-wide on the announced date; only old clients are affected. **Fix:** Upgrade the POS client to negotiate TLS 1.2+ with AES-GCM; enforce strong cipher policy at the load balancer. **Exam angle:** "old clients break on a cutoff date" = cipher deprecation, not local misconfig.

---

- **3.2 Dev role with `s3:*` lets attacker exfiltrate prod bucket → Privilege escalation (6.3.2a):** **Symptom:** A compromised developer account deletes and exfiltrates data from a production S3 bucket, though the dev only needed read on one prefix. Audit shows the role has `s3:*` on `*`. **Diagnosis:** **Privilege escalation (6.3.2a)** — the role granted far more than the job required, turning a low-value compromise into a major breach. **Fix:** Replace `*` with explicit least-privilege actions/resources, run IAM Access Analyzer to find over-share, and separate dev/prod roles. **Exam angle:** "too much access via over-broad policy" = privilege escalation.

---

- **3.3 Hardcoded key in public repo abused from foreign IP → Leaked credentials (6.3.3a):** **Symptom:** At 3 a.m., an AWS access key starts `AssumeRole` calls from an unfamiliar foreign IP. The key was committed to a public GitHub repo inside a container image. **Diagnosis:** **Leaked credentials (6.3.3a)** — an exposed long-lived secret is being actively abused. **Fix:** Disable/rotate the key immediately, revoke assumed-role sessions, scan all repos for secrets, and move to short-lived roles/OIDC. **Exam angle:** "known secret reused by attacker" = leaked credentials, kill the secret first.

---

- **3.4 Internet-facing service exploited via Log4Shell (CVE-2021-44228) → Software vuln (6.3.4):** **Symptom:** A public web service is being used for remote code execution; investigators find an outdated logging library with the known Log4j CVE. No IAM or credential issue is involved. **Diagnosis:** **Software vulnerability (6.3.4)** — a known, unpatched flaw is the entry point. **Fix:** Patch/upgrade the library, rebuild and redeploy images, rotate exposed secrets, and add CVE scanning to CI. **Exam angle:** "known CVE exploited" = software vulnerability, fix by patching.

---

- **3.5 Unknown crypto-miner process on instances → Unauthorized software (6.3.5):** **Symptom:** Several instances spike to 100% CPU from an unrecognized binary (`kdevtmpfsi`) that no deployment manifest defines. **Diagnosis:** **Unauthorized software (6.3.5)** — rogue malware in the environment, typically arriving via leaked creds or a vuln. **Fix:** Terminate/quarantine the instances, revoke the entry-vector credentials, patch the exploited flaw, block untrusted images, and alert on process baseline drift. **Exam angle:** "unexpected program running" = unauthorized software, investigate entry + evict.

---

## SECTION 4 — COMPARISON TABLES


### 4.1 Symptom → 6.3 Cause Mapping

| Symptom | Root cause (6.3) | Quick fix direction |
|---|---|---|
| Old client fails handshake after cutoff | Cipher deprecation (6.3.1) | Upgrade to supported suite |
| Role with `*` deletes prod | Privilege escalation (6.3.2a) | Least-privilege policy |
| Outside identity reads private data | Unauthorized access (6.3.2b) | Revoke/share, block-public |
| Key in repo abused by attacker | Leaked credentials (6.3.3a) | Rotate/disable key, revoke sessions |
| Known CVE exploited (RCE) | Software vulnerability (6.3.4) | Patch/upgrade component |
| Rogue/malware process running | Unauthorized software (6.3.5) | Quarantine, find entry, evict |

### 4.2 Authentication vs Authorization

| Dimension | Authentication (6.3.3) | Authorization (6.3.2) |
|---|---|---|
| Question answered | Who are you? | What may you do? |
| Failure example | Can't log in / leaked creds | Too much access / reached data |
| Fix | Rotate keys, enforce MFA | Least-privilege IAM/RBAC |

### 4.3 Privilege escalation vs Unauthorized access

| Dimension | Privilege escalation (6.3.2a) | Unauthorized access (6.3.2b) |
|---|---|---|
| Nature | Identity has *excess* rights | Identity reaches *forbidden* resource |
| Example | `s3:*` on a dev role | Public bucket readable by all |
| Fix | Scope down the policy | Revoke/share, block access |

### 4.4 Leaked credentials vs Software vulnerability

| Dimension | Leaked credentials (6.3.3a) | Software vuln (6.3.4) |
|---|---|---|
| Entry vector | Stolen/exposed secret | Flaw in code/dependency |
| Example | Key in GitHub repo | Log4Shell CVE |
| Fix | Rotate/disable secret | Patch/upgrade component |

### 4.5 Cipher deprecation vs Protocol incompatibility

| Dimension | Cipher deprecation (6.3.1) | Protocol incompat (6.2.5) |
|---|---|---|
| Scope | Encryption suite removed by provider | Two ends disagree on protocol |
| Trigger | Scheduled provider cutoff | Any config mismatch |
| Fix | Move to supported cipher | Align protocol both ends |

### 4.6 Unauthorized software vs Unauthorized access

| Dimension | Unauthorized software (6.3.5) | Unauthorized access (6.3.2b) |
|---|---|---|
| What's wrong | Rogue/unapproved *program* runs | Identity reaches *resource* |
| Example | Crypto-miner process | External user reads bucket |
| Fix | Quarantine + remove + block | Revoke access / fix policy |

---

## SECTION 5 — AWS PROVIDER MAPPING


AWS-ONLY mapping for objective 6.3. On AWS, security troubleshooting is anchored by three native services: **Amazon GuardDuty** (managed threat detection that surfaces unauthorized activity, crypto-mining, and credential abuse), **IAM Access Analyzer** (finds policies/roles that grant access to external or overly-broad principals), and **AWS CloudTrail** (the audit log of every API call — who did what, from where, with which credential). Together they let you detect, scope, and remediate every 6.3 cause.

### GuardDuty — threat detection
GuardDuty analyzes CloudTrail, VPC Flow Logs, and DNS logs using threat intelligence and ML to raise findings for:
- **Leaked credentials (6.3.3a):** `CredentialAccess:IAMUser/AnomalousBehavior` / `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration` — a key used from an unusual IP/region.
- **Unauthorized access (6.3.2b):** `UnauthorizedAccess:IAMUser/ConsoleLoginSuccess`/`MaliciousIP` findings when an identity reaches resources from a known-bad source.
- **Unauthorized software (6.3.5):** `CryptoCurrency:EC2/...Miner` findings flagging compute hijacked for mining — the signature of rogue software.
- **Privilege escalation (6.3.2a):** `PrivilegeEscalation:IAMUser/...` findings (e.g., `PassRole` + `CreateInstanceProfile`) indicating rights widening.

### IAM Access Analyzer — over-permission & external access
Access Analyzer evaluates IAM policies, bucket policies, KMS, and roles to find:
- **Unauthorized access (6.3.2b):** a finding that a resource (S3 bucket, role, KMS key) is shared with an external account or public principal — exactly the "reached what they shouldn't" case.
- **Privilege escalation (6.3.2a):** identifies roles/policies granting `*` or risky permissions that let a principal widen its own rights.
- **Cipher/transport posture (6.3.1):** complements by validating resource policies don't allow weak/legacy access paths.

### CloudTrail — the who/what/when audit
CloudTrail records every API call, giving the forensic backbone for:
- **Leaked credentials (6.3.3a):** trace which access key (`aws.accessKeyId`) made calls, from which source IP, to scope the blast radius and revoke.
- **Privilege escalation / unauthorized access (6.3.2):** see `AssumeRole`, `AttachUserPolicy`, `PutBucketPolicy` events that changed permissions.
- **Authentication issues (6.3.3):** `ConsoleLogin` with `MFAUsed=false` or failed-auth spikes reveal weak auth paths.
- **Unauthorized software (6.3.5):** `RunInstances`/`RegisterTaskDefinition` from unexpected principals shows how rogue workloads appeared.

### Cause → AWS service table

| 6.3 Cause | Where you SEE it (AWS) | Where you FIX it (AWS) |
|---|---|---|
| Cipher deprecation (6.3.1) | ELB/CloudFront policy logs; client handshake failures | Enforce TLS 1.2+ policy; upgrade clients |
| Privilege escalation (6.3.2a) | GuardDuty PrivEsc findings; Access Analyzer `*` | Scope IAM/RBAC to least privilege |
| Unauthorized access (6.3.2b) | Access Analyzer external-share finding; GuardDuty | Revoke share, block-public-access |
| Leaked credentials (6.3.3a) | GuardDuty + CloudTrail key/IP anomaly | Rotate/disable key, revoke sessions |
| Software vuln (6.3.4) | Inspector CVE findings; exploit alerts | Patch/upgrade; rebuild images |
| Unauthorized software (6.3.5) | GuardDuty CryptoMiner; unexpected RunInstances | Quarantine, revoke entry, block images |

### Cross-cutting reminders
- **GuardDuty** = "what malicious activity is happening" — your detection engine for creds, escalation, unauthorized access, and malware.
- **IAM Access Analyzer** = "who has more access than they should, including outsiders" — your over-permission scanner.
- **CloudTrail** = "exactly who did what, when, from where" — your forensic source for every 6.3 cause.
- Workflow on an incident: GuardDuty alerts → CloudTrail scopes the key/principal → Access Analyzer finds the over-broad grant → remediate (rotate, scope, revoke, patch).

---

## SECTION 6 — PRACTICE QUESTIONS


1. After a provider disables TLS 1.0, a legacy client fails the handshake with "no cipher in common." What is the cause?
   A. Protocol incompatibility (6.2.5)
   B. Cipher suite deprecation (6.3.1)
   C. Authentication failure
   D. Routing issue
   Answer: B — The weak cipher was retired provider-wide on a schedule; upgrade to a supported suite.

2. A developer's role has `s3:*` on all buckets, and a compromise leads to prod data loss. Which cause?
   A. Unauthorized access (6.3.2b)
   B. Privilege escalation (6.3.2a)
   C. Leaked credentials (6.3.3a)
   D. Software vulnerability (6.3.4)
   Answer: B — An over-permissive (`*`) policy granted excess rights; scope to least privilege.

3. A public S3 bucket policy with `"Principal": "*"` lets anonymous users read sensitive objects. Cause?
   A. Privilege escalation (6.3.2a)
   B. Unauthorized access (6.3.2b)
   C. Cipher deprecation (6.3.1)
   D. Unauthorized software (6.3.5)
   Answer: B — An external/unauthenticated principal reaches forbidden data; fix the policy and block public access.

4. An AWS access key committed to a public repo starts API calls from a foreign IP at 3 a.m. Cause?
   A. Software vulnerability (6.3.4)
   B. Leaked credentials (6.3.3a)
   C. Unauthorized access (6.3.2b)
   D. Privilege escalation (6.3.2a)
   Answer: B — An exposed secret is being abused; rotate/disable it and revoke sessions immediately.

5. An internet-facing service is exploited for RCE via CVE-2021-44228 (Log4j). Cause?
   A. Leaked credentials (6.3.3a)
   B. Software vulnerability (6.3.4)
   C. Unauthorized access (6.3.2b)
   D. Cipher deprecation (6.3.1)
   Answer: B — A known, unpatched flaw; patch/upgrade the component and rebuild images.

6. Instances spike to 100% CPU from an unknown `kdevtmpfsi` binary not in any manifest. Cause?
   A. Software vulnerability (6.3.4)
   B. Unauthorized software (6.3.5)
   C. Leaked credentials (6.3.3a)
   D. Privilege escalation (6.3.2a)
   Answer: B — Rogue crypto-miner malware; quarantine, find the entry vector, and evict.

7. A user can perform actions far above their job role because their policy uses wildcards. This is:
   A. Unauthorized access (6.3.2b)
   B. Privilege escalation (6.3.2a)
   C. Leaked credentials (6.3.3a)
   D. Authentication issue (6.3.3)
   Answer: B — Excess rights via over-broad policy = privilege escalation; apply least privilege.

8. GuardDuty raises `CredentialAccess:IAMUser/AnomalousBehavior` from an unusual IP. This indicates:
   A. Software vulnerability (6.3.4)
   B. Leaked credentials (6.3.3a)
   C. Cipher deprecation (6.3.1)
   D. Unauthorized software (6.3.5)
   Answer: B — A key used anomalously signals leaked/abused credentials; rotate and investigate.

9. IAM Access Analyzer shows an S3 bucket shared with an external account. This is:
   A. Privilege escalation (6.3.2a)
   B. Unauthorized access (6.3.2b)
   C. Leaked credentials (6.3.3a)
   D. Unauthorized software (6.3.5)
   Answer: B — External sharing is unintended access; revoke the cross-account grant.

10. A container from an untrusted registry is running in production without approval. Cause?
    A. Unauthorized software (6.3.5)
    B. Software vulnerability (6.3.4)
    C. Leaked credentials (6.3.3a)
    D. Cipher deprecation (6.3.1)
    Answer: A — An unapproved image/workload; block untrusted registries and remove it.

11. An attacker assumes a role then attaches `AdministratorAccess` to themselves. Which cause?
    A. Unauthorized access (6.3.2b)
    B. Privilege escalation (6.3.2a)
    C. Leaked credentials (6.3.3a)
    D. Software vulnerability (6.3.4)
    Answer: B — Widening own rights via role/policy manipulation = privilege escalation.

12. CloudTrail shows `ConsoleLogin` with `MFAUsed=false` repeatedly succeeding for an admin. Concern?
    A. Cipher deprecation (6.3.1)
    B. Authentication issue (6.3.3) — MFA not enforced
    C. Unauthorized software (6.3.5)
    D. Software vulnerability (6.3.4)
    Answer: B — Missing MFA is an authentication weakness; enforce MFA on all privileged users.

13. A dependency with a known CVSS 9.8 RCE is pulled into the build and exploited. Cause?
    A. Unauthorized software (6.3.5)
    B. Software vulnerability (6.3.4)
    C. Leaked credentials (6.3.3a)
    D. Unauthorized access (6.3.2b)
    Answer: B — A known vulnerable dependency; patch/upgrade and scan in CI.

14. After disabling RC4, a partner's integration stops connecting securely. Cause?
    A. Cipher suite deprecation (6.3.1)
    B. Protocol incompatibility (6.2.5)
    C. Authorization issue (6.3.2)
    D. Leaked credentials (6.3.3a)
    Answer: A — The weak cipher was deprecated; the partner must negotiate a supported suite.

15. A compromised key is used to launch dozens of unapproved instances. The instance creation is:
    A. Software vulnerability (6.3.4)
    B. Unauthorized software (6.3.5) enabled via leaked creds (6.3.3a)
    C. Cipher deprecation (6.3.1)
    D. Privilege escalation (6.3.2a)
    Answer: B — Rogue workloads = unauthorized software; the entry was the leaked key (6.3.3a).

16. An S3 bucket policy allows `s3:GetObject` to `"Principal": "*"`. This is best described as:
    A. Privilege escalation (6.3.2a)
    B. Unauthorized access (6.3.2b)
    C. Leaked credentials (6.3.3a)
    D. Cipher deprecation (6.3.1)
    Answer: B — Public read of private data is unauthorized access; remove the wildcard principal.

17. Inspector flags a critical CVE in the base AMI of running instances. Under 6.3 this is:
    A. Leaked credentials (6.3.3a)
    B. Software vulnerability (6.3.4)
    C. Unauthorized software (6.3.5)
    D. Unauthorized access (6.3.2b)
    Answer: B — A vulnerable image/OS component; patch and redeploy from a fixed AMI.

18. A user shares their password and an outsider logs in as them. The root cause to name is:
    A. Privilege escalation (6.3.2a)
    B. Leaked credentials (6.3.3a)
    C. Software vulnerability (6.3.4)
    D. Unauthorized software (6.3.5)
    Answer: B — A shared/exposed password is a leaked credential; reset it and enforce MFA.

19. GuardDuty raises `CryptoCurrency:EC2/...Miner`. The appropriate 6.3 classification is:
    A. Software vulnerability (6.3.4)
    B. Unauthorized software (6.3.5)
    C. Leaked credentials (6.3.3a)
    D. Unauthorized access (6.3.2b)
    Answer: B — Detected crypto-mining is rogue/unauthorized software; quarantine and find the entry.

20. A role can `iam:PassRole` to a privileged instance profile, enabling a user to gain admin. This is:
    A. Unauthorized access (6.3.2b)
    B. Privilege escalation (6.3.2a)
    C. Cipher deprecation (6.3.1)
    D. Unauthorized software (6.3.5)
    Answer: B — Using a permission to widen rights = privilege escalation; restrict risky IAM actions.
