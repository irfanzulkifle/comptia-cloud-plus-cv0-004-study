---
title: CompTIA Cloud+ CV0-004 — Objective 4.4
objective: "4.4 Given a scenario, apply security best practices."
domain: "4.0 Security"
weight: "20% of exam"
tags: [Cloud+, CV0-004, Security, IAM-adjacent, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 4.4
## Apply Security Best Practices

> **Objective statement:** *"Given a scenario, apply security best practices."*
> **Domain:** 4.0 Security (20% of the exam)
> **What it tests:** Whether you can take a real workload and harden it end-to-end — from the network model (Zero Trust) down to container privileges and bucket policies. This is the **practical hardening** objective: the "how do I make this secure" counterpart to 4.1 (find the holes), 4.2 (stay compliant), 4.3 (who gets in), and 4.5/4.6 (controls + detection).

---

## SECTION 1 — OBJECTIVE OVERVIEW

**In one breath:** Security best practices = the collection of standard techniques used to reduce attack surface and protect data across every layer of a cloud deployment.

**The 4.4 sub-objectives (verbatim from the official exam objectives):**

| # | Sub-objective | What it means |
|---|---------------|---------------|
| 4.4.1 | **Zero Trust** | "Never trust, always verify" — no implicit trust by network location |
| 4.4.2 | **Benchmark** | Hardening baselines — CIS + vendor-specific |
| 4.4.2a | ├ Center for Internet Security (CIS) | CIS Benchmarks / CIS Controls |
| 4.4.2b | └ Vendor-specific | AWS / Azure / GCP security benchmarks |
| 4.4.3 | **Hardening** | Removing attack surface from OS/config |
| 4.4.4 | **Patching** | Applying security updates on a cadence |
| 4.4.5 | **Encryption** | Protecting data from exposure |
| 4.4.5a | ├ Data in transit | TLS / VPN between endpoints |
| 4.4.5b | └ Data at rest | Encrypt stored volumes / objects |
| 4.4.6 | **Secrets management** | Vaulting keys/passwords, not in code |
| 4.4.7 | **API security** | Locking down programmatic endpoints |
| 4.4.8 | **Principle of least privilege** | Minimum permissions, just-in-time |
| 4.4.9 | **Container security** | Privileged vs unprivileged + file perms |
| 4.4.9a | ├ Privileged | `--privileged` containers = risk |
| 4.4.9b | ├ Unprivileged | drop caps, non-root, read-only FS |
| 4.4.9c | └ File access permissions | restrictive perms on mounts/secrets |
| 4.4.10 | **Storage security** | Locking down object + file storage |
| 4.4.10a | ├ Object storage | buckets: block public, policies, SSE |
| 4.4.10b | └ File storage | shares: restrictive mount, encryption |

**Mental model:** Everything below is a *layer* of the same "make it smaller and locked" idea:
`Zero Trust (philosophy) → Benchmarks (the checklist) → Hardening+Patching (the OS) → Encryption+Secrets (the data) → API+Least-Priv+Containers+Storage (the workload)`.

---

## SECTION 2 — EXAM KNOWLEDGE

### 4.4.1 — ZERO TRUST

**Core idea:** "Never trust, always verify." No request is trusted just because it came from "inside" the network.

- **Assume breach** — design as if the attacker is already inside.
- **Verify explicitly** — authenticate + authorize **every** request, every time, regardless of source IP or network zone.
- **Least-privilege access** — grant only what's needed, for as long as needed (just-in-time).
- **Micro-segmentation** — break the network into tiny zones so a compromise can't roam.
- **Continuous evaluation** — re-check identity/device posture, not just at login.

**Exam contrast:** Old "castle-and-moat" / perimeter security = trust everything inside the firewall. Zero Trust = trust nothing by location (kills the "but it's on the internal subnet" trap).

### 4.4.2 — BENCHMARK

Hardening **baselines** — the starting checklist so you don't harden from memory.

#### Center for Internet Security (CIS)
- **CIS Benchmarks** — vendor-neutral, consensus hardening guides for OSes, cloud, containers (e.g., CIS Amazon Linux 2 Benchmark).
- **CIS Controls** — prioritized safeguard list (inventory, config, access control…).
- **CIS Hardened Images** — pre-hardened VM images you can launch instead of hardening by hand.

#### Vendor-specific
- **AWS:** Well-Architected Framework — Security Pillar; AWS Security Hub / Foundational Security Best Practices (FSBP) standard.
- **Azure:** Microsoft Cloud Security Benchmark (MCSB); Azure Security Benchmark policy initiatives.
- **GCP:** Security Foundations Guide; CIS GCP Benchmark via Security Command Center.

**Exam angle:** "We need a standardized hardening baseline" → apply the relevant **CIS Benchmark** (or the provider's security benchmark).

### 4.4.3 — HARDENING

**Definition:** Reducing attack surface by removing what you don't need and tightening what remains.

- Disable unused services, ports, and protocols.
- Remove/disable default accounts; rename/disable default admin where possible.
- Enforce secure configuration (no default passwords, strong ciphers).
- OS/instance hardening: disable root SSH login, enable firewall, remove unneeded packages.
- Apply benchmarks (4.4.2) as the baseline.

**Exam angle:** "New server is over-permissioned and exposes extra ports" → **harden** it (close ports, disable services, remove defaults).

### 4.4.4 — PATCHING

**Definition:** Applying vendor security updates to close known vulnerabilities (links to 4.1 scanning and 3.4 lifecycle).

- **Cadence:** regular, scheduled (e.g., monthly patch window); emergency patches for crits.
- **Process:** test in non-prod → stage → prod; automate with patch management (SSM Patch Manager, Azure Automation, GCP OS patch management).
- **Why:** unpatched systems = #1 breach vector (ties back to 4.1).

**Exam angle:** "Vulnerable package flagged, prod can't break" → patch on a **tested cadence** (test first, then roll).

### 4.4.5 — ENCRYPTION

#### Data in transit
- Encrypt data **moving between points** — TLS/SSL for web/API, VPN/IPsec for network tunnels, in-service encryption (e.g., TLS between microservices).
- Prevents eavesdropping / man-in-the-middle.
- **Exam angle:** "Traffic between services is sniffable" → enforce **TLS / encryption in transit**.

#### Data at rest
- Encrypt data **stored** — EBS/disk volumes, database files, object blobs.
- Use provider KMS (AWS KMS, Azure Key Vault, GCP KMS) with CMK/BYOK; S3 SSE; encrypted RDS.
- **Exam angle:** "Disk stolen / bucket dumped" → **encryption at rest** with managed keys.

### 4.4.6 — SECRETS MANAGEMENT

**Definition:** Store credentials (API keys, DB passwords, certs, tokens) in a dedicated **vault**, never in code, config files, or images.

- Tools: AWS Secrets Manager / Parameter Store, Azure Key Vault, GCP Secret Manager.
- **Rotation** — auto-rotate on a schedule; short-lived creds.
- Inject at runtime (env vars / mounted secret), not baked into the image.
- **Exam angle:** "Password is hardcoded in the app" → move it to **secrets management** + rotate.

### 4.4.7 — API SECURITY

**Definition:** Locking down the programmatic endpoints (ties to 4.3 programmatic access).

- **AuthN/AuthZ** on every call — OAuth 2.0 / API keys / signed requests; no anonymous endpoints.
- **Rate limiting + quotas** — stop abuse / brute force / cost blowout.
- **Input validation + WAF** — block injection / malformed payloads.
- **API gateway** — central point for auth, throttling, logging.
- **Least privilege** on the called resource.
- **Exam angle:** "Third party is hammering our API / we leak data via it" → add auth + rate limiting + gateway + validation.

### 4.4.8 — PRINCIPLE OF LEAST PRIVILEGE

**Definition:** Grant only the permissions **necessary** to do the job — nothing more, for no longer than needed.

- Implement via RBAC (4.3.4) + group-based; **no standing admin**.
- **Just-in-time (JIT)** elevation for break-glass admin.
- Ties directly to Zero Trust (4.4.1) and 4.3 authorization.
- **Exam angle:** "Service has full admin but only reads one bucket" → apply **least privilege** (scope the role down).

### 4.4.9 — CONTAINER SECURITY

#### Privileged
- A **privileged container** (`--privileged`) gets near-root access to the host — bypasses isolation. **Major risk; avoid** unless a specific device needs it.
- **Exam angle:** "Container needs host device access" → only then consider privileged; otherwise **don't**.

#### Unprivileged
- Run containers **without** host privileges: non-root user, **drop Linux capabilities**, **read-only root filesystem**, no `--privileged`.
- Stronger isolation; limits blast radius if compromised.
- **Exam angle:** "Harden this container deployment" → run **unprivileged**, non-root, drop caps, read-only FS.

#### File access permissions
- Apply **restrictive permissions** to files inside the container and on mounted volumes.
- Don't write secrets to writable layers; mount secrets read-only; limit volume mount scope.
- **Exam angle:** "Container can read other tenants' files" → tighten **file access permissions** / volume mounts.

### 4.4.10 — STORAGE SECURITY

#### Object storage
- **Block all public access** by default (S3 Block Public Access / Azure blob firewall).
- Bucket/container **policies** + IAM least privilege; **encryption at rest** (SSE); **versioning** + access **logging**.
- Use **scoped presigned/signed URLs** (short TTL) instead of permanent public links.
- **Exam angle:** "S3 bucket exposed PII" → block public access + lock policy + enable SSE + audit logs.

#### File storage
- File shares (EFS / Azure Files / FSx): **restrictive mount permissions**, encrypt in transit + at rest, network-isolate (private endpoints / mount targets in private subnets).
- **Exam angle:** "File share readable by everyone" → lock down mount permissions + private networking.

> **Umbrella principle:** every 4.4 item is a way to **shrink the attack surface and protect the data**. If a question describes "too much access / too open / unpatched / plaintext / hardcoded secret," the fix is the matching best practice above.




## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 Hardening a Public Web Server Fleet (AWS EC2 / Auto Scaling)

A Malaysian e-commerce client runs a public web tier across an **Auto Scaling group** of EC2 instances behind an **Application Load Balancer (ALB)**. Best practice starts with a **CIS Benchmark**-hardened base AMI (disable root SSH, remove default accounts, enable **SELinux/AppArmor**). The launch template applies the hardening automatically so every new node is compliant from boot — no manual drift.

The **security group** is least-privilege: only 443 (and 80 for redirect) open to `0.0.0.0/0`, with 22 blocked entirely; admins reach hosts via **SSM Session Manager** (no open SSH port at all). **Patch management** runs on a staged cadence: canary instances get updates first, validated in a non-prod account, then rolled to prod on a maintenance window — never "patch prod instantly."

TLS 1.2+ terminates at the ALB with **ACM** certs; backend traffic uses mTLS. **Encryption at rest** via EBS KMS (CMK, rotated). A **WAF** in front blocks common OWASP attacks and enforces rate limits. Logs stream to **CloudWatch** with alarms on failed-auth spikes.

**Exam angle:** "Hardening" = baseline config (CIS) + least privilege + removing defaults + patching discipline + encryption both ways. The distractor is treating hardening as "just install patches."

### 3.2 Locking Down an S3-Style Object Bucket with PII (AWS S3 / Azure Blob / GCP)

A payroll export containing NRIC numbers (PII) lands in an object bucket. **Definition:** Object storage buckets are private by default, but misconfig is the #1 breach cause. We set an **explicit deny** public-access policy and enable **Block Public Access** at account level so no future policy can accidentally expose it.

**Encryption at rest** uses SSE-KMS with a customer-managed key (audit who decrypted). **Bucket policies** follow least privilege — only the HR app role gets `s3:GetObject`. **Versioning** + **MFA Delete** protect against overwrite/deletion. **Object Lock** (WORM) satisfies retention. **Access logging** + **CloudTrail data events** record every object read for the audit trail.

Lifecycle rules move cold PII to **Glacier** after 90 days; cross-region replication copies to a DR bucket with the same lockdown. **TLS** is forced via the policy (`aws:SecureTransport = false` → deny) so transit is encrypted.

**Exam angle:** "Private by default" ≠ "secure by default." You must still disable public access, enforce TLS, and use KMS. A **default-allow bucket policy** is the classic trap.

### 3.3 Securing a Containerized Microservice (Privileged → Unprivileged)

The team's microservice was shipped as a **privileged container** (`--privileged`) "for convenience" to read host devices. That breaks isolation — a compromise escapes to the host. Hardening moves it to an **unprivileged container**: drop all Linux capabilities, add only those strictly needed (`cap_drop: ALL`, `cap_add: NET_BIND_SERVICE`), run as a **non-root UID**, set `read_only` root filesystem, and use **seccomp** + **AppArmor** profiles.

On **EKS/AKS/GKE**, enable **Pod Security Standards** (restricted), set `runAsNonRoot`, and use **SecurityContext**. Secrets come from the cluster secret store (or **Secrets Manager**), not baked into the image. Image scanning (**ECR scanning / Trivy**) blocks CVEs before deploy; **read-only mounts** for `/tmp`. Network policy restricts pod-to-pod traffic to the minimum.

**Exam angle:** Privileged containers are almost never correct on the exam. "For convenience" = wrong. **Unprivileged + non-root + dropped caps + read-only FS** is the secure pattern.

### 3.4 Implementing Zero Trust for an Internal Admin Tool

The internal admin console (billing dashboard) sat on the corporate network, assumed "internal subnet = trusted." A stolen laptop on WiFi reached it directly. **Definition:** **Zero Trust** = "never trust, always verify" — every request is authenticated, authorized, and encrypted regardless of network location.

We front the tool with an **identity-aware proxy** (GCP IAP / Azure App Proxy / AWS IAM Identity Center). Access requires **MFA**, device compliance check, and **just-in-time (JIT)** elevation — no standing admin rights. **Microsegmentation** isolates the tool's subnet; **least privilege** grants only the billing-role permission set. All sessions logged and **rate limited**.

**Exam angle:** The whole point of Zero Trust is that **network location is not a trust signal**. "It's internal, so it's safe" is the trap. Internal ≠ trusted.

### 3.5 Secrets Management for a CI/CD Pipeline

The build pipeline originally had the production DB password **hardcoded** in a Dockerfile and committed to Git — anyone with repo access (or a leaked image) got prod. Best practice moves secrets to a **managed vault**: **AWS Secrets Manager / Parameter Store (SecureString)**, **Azure Key Vault**, or **GCP Secret Manager**.

The CI runner assumes an **IAM role** (OIDC federation — no static keys) and fetches the secret at runtime, injects it as an **environment variable** (never written to disk), and the secret is **rotated** automatically every 30 days. **Least privilege** scoping means the build role can only read the one secret it needs. Images are scanned so no secret slips into a layer; **`.gitignore`** + pre-commit secret scanning (**gitleaks**) catch accidental commits.

**Exam angle:** Secrets belong in a vault, fetched at runtime via short-lived credentials — **not** in code, images, or long-lived static keys.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 Zero Trust vs Perimeter (Castle-and-Moat)

| Dimension | Perimeter / Castle-and-Moat | Zero Trust |
|-----------|------------------------------|------------|
| Trust model | Trust inside the network | Never trust, always verify |
| Network location | Internal = trusted | No implicit trust by location |
| Auth | Once at the edge | Continuous, per-request |
| Scope | Protect the border | Protect every resource |
| Breach impact | Lateral movement easy | Contained by microsegmentation |
| Example | VPN then full internal access | Identity-aware proxy + MFA + JIT |

### 4.2 CIS Benchmarks vs Vendor-Specific Benchmarks

| Dimension | CIS Benchmarks | Vendor-Specific Benchmarks |
|-----------|----------------|----------------------------|
| Source | Center for Internet Security (community) | AWS/Azure/GCP, OS vendors |
| Scope | Cross-platform, OS/service baselines | Tailored to one platform's features |
| Goal | Neutral hardening baseline | Optimized for that cloud's controls |
| Example | CIS Ubuntu 22.04 L1/L2 | AWS Foundations Benchmark, Azure Security Baseline |
| Use | Universal starting point | Layered on top of CIS |

### 4.3 Encryption In-Transit vs At-Rest

| Dimension | Encryption In-Transit | Encryption At-Rest |
|-----------|------------------------|--------------------|
| Protects | Data moving over the network | Data stored on disk/volume |
| Mechanism | TLS 1.2+/mTLS, IPsec | KMS, SSE, LUKS, EBS/disk encryption |
| Threat | Interception, MITM | Theft of disk, backup, snapshot |
| Enforcement | Enforce HTTPS / `aws:SecureTransport` | Enable KMS/CMK on volumes & buckets |
| Exam note | Often forgotten — plaintext transit | Easy to enable, but not enough alone |

### 4.4 Privileged vs Unprivileged Containers

| Dimension | Privileged Container | Unprivileged Container |
|-----------|----------------------|------------------------|
| Host access | Full device/host access | Isolated, no host devices |
| Capabilities | All Linux caps | Dropped; add only needed |
| User | Often root | Non-root UID |
| Risk | Container escape → host compromise | Blast radius contained |
| Use case | Rare (e.g., some CNI plugins) | Default for app workloads |

### 4.5 Object Storage vs File Storage Security

| Dimension | Object Storage (S3/Blob/GCS) | File Storage (EFS/FSx/NFS) |
|-----------|------------------------------|----------------------------|
| Access model | Bucket policies, IAM, pre-signed URLs | POSIX perms, export policies, mount |
| Public exposure | Accidental public bucket (high risk) | Usually network-restricted mount |
| Encryption | SSE-KMS, enforced TLS | KMS/at-rest, in-transit NFSv4.1+ |
| Trap | Default-allow / misconfig policy | World-readable mount (`777`) |
| Audit | Access logs + CloudTrail data events | Filesystem audit logs |

### 4.6 Secrets in Code vs Secrets Management

| Dimension | Secrets in Code / Image | Secrets Management (Vault) |
|-----------|--------------------------|----------------------------|
| Storage | Hardcoded, committed to Git | Managed vault (Secrets Manager/Key Vault) |
| Lifetime | Long-lived, rarely rotated | Short-lived, auto-rotated |
| Exposure | Leaks via repo/image/repo history | Scoped IAM, fetched at runtime |
| Rotation | Manual, painful | Automated |
| Exam answer | Always wrong | Always the correct choice |

---

## SECTION 5 — EXAM TRAPS

### Trap 1 — "Internal subnet = trusted"
- **Scenario:** An admin tool is placed on the internal network with no extra auth because "it's behind the firewall."
- **Correct:** Treat it as untrusted — apply Zero Trust (MFA, identity-aware proxy, JIT, least privilege).
- **Why wrong:** Zero Trust says network location is not a trust signal. Internal ≠ safe; a single compromised laptop gives full access.

### Trap 2 — Default-allow bucket policy
- **Scenario:** An S3 bucket policy uses `Effect: Allow` on `*` with no explicit conditions, relying on "private by default."
- **Correct:** Use explicit deny for public access + Block Public Access + least-privilege bucket policy.
- **Why wrong:** "Private by default" doesn't survive misconfig or future policy edits. Default-allow + broad principals = data breach.

### Trap 3 — Privileged container for convenience
- **Scenario:** A container runs `--privileged` so it can read a host device without extra config.
- **Correct:** Run unprivileged, drop all caps, add only needed, run as non-root, read-only FS.
- **Why wrong:** Privileged breaks isolation; a compromise escapes to the host. Convenience is never worth the blast radius.

### Trap 4 — Hardcode secret in image
- **Scenario:** The DB password is baked into the Dockerfile `ENV` so the app "just works."
- **Correct:** Fetch from a vault (Secrets Manager/Key Vault) at runtime via a scoped role.
- **Why wrong:** The secret lands in the image layer and Git history — anyone pulling the image gets prod.

### Trap 5 — API with no rate limit
- **Scenario:** A public API has no throttling because "we trust our clients."
- **Correct:** Enforce rate limits / quotas (API Gateway, WAF) and input validation.
- **Why wrong:** No rate limit = easy DoS and credential-stuffing. API security requires throttling + validation.

### Trap 6 — Patch everything in prod instantly with no test
- **Scenario:** Apply all OS/security patches to production immediately on release to "stay secure."
- **Correct:** Stage → test in non-prod → canary → roll out on a maintenance window.
- **Why wrong:** Unvalidated patches cause outages. Patching discipline = staged rollout, not blind prod updates.

### Trap 7 — Encryption only at rest, transit plaintext
- **Scenario:** Volumes are KMS-encrypted but the API serves data over plain HTTP.
- **Correct:** Encrypt both — TLS in transit AND KMS at rest.
- **Why wrong:** At-rest alone doesn't stop interception. Plaintext transit exposes data on the wire.

### Trap 8 — Root SSH login enabled
- **Scenario:** The hardened server keeps `PermitRootLogin yes` for "easy admin."
- **Correct:** Disable root SSH; use non-root + sudo / SSM Session Manager; key-only auth.
- **Why wrong:** Root SSH is a top brute-force target and violates least privilege. CIS benchmarks disable it.

### Trap 9 — Shared standing admin creds
- **Scenario:** The team shares one admin account/password for the cloud console.
- **Correct:** Per-user identities, least privilege, JIT elevation, MFA.
- **Why wrong:** Shared standing creds = no accountability, no revocation granularity, huge blast radius. Use least privilege + JIT.

### Trap 10 — Unencrypted volume
- **Scenario:** An EBS/data disk is left unencrypted "because it's internal."
- **Correct:** Enable encryption at rest (KMS CMK) on all volumes, snapshots, and backups.
- **Why wrong:** A stolen snapshot/backup leaks data. At-rest encryption is baseline and nearly free.

### Trap 11 — Open security group 0.0.0.0/0
- **Scenario:** A security group allows `0.0.0.0/0` on port 22 "just for now."
- **Correct:** Least privilege — restrict to admin CIDR or use SSM; block 22 publicly.
- **Why wrong:** Wide-open SG = internet-exposed attack surface. Never expose management ports to the world.

### Trap 12 — No WAF / validation on API
- **Scenario:** The API sits directly on the internet with no WAF and trusts all input.
- **Correct:** Put a WAF in front, validate/sanitize input, enforce schema.
- **Why wrong:** No WAF/validation = OWASP injection, XSS, and L7 DoS. API security needs both.

### Trap 13 — File storage mounted world-readable
- **Scenario:** An EFS/NFS share is mounted with `777` permissions "so all apps can read it."
- **Correct:** POSIX perms + export policies limit to specific hosts/IDs; not world-readable.
- **Why wrong:** World-readable mounts leak data across workloads and violate least privilege.

### Trap 14 — Default accounts left enabled
- **Scenario:** The base image keeps `guest`/`admin` default accounts enabled.
- **Correct:** Remove/disable all default and unused accounts during hardening (CIS).
- **Why wrong:** Default accounts are known credentials — an easy foothold. Hardening removes them.

### Trap 15 — Long-lived static keys vs rotation
- **Scenario:** A CI pipeline uses one static IAM access key that never rotates.
- **Correct:** Use OIDC/role-based short-lived creds; if keys needed, rotate automatically (≤90 days).
- **Why wrong:** Long-lived static keys are a permanent leak risk. Rotation + short-lived creds limit exposure.


## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

These are the hands-on, drag-and-drop / config tasks you'll likely face on the CV0-004 exam. For each PBQ, work the **Scenario** like a real ticket, follow the **Walkthrough** checklist, then test yourself with the **Bonus question**.

### 6.1 — Harden a New Internet-Facing VM

**Scenario**
A brand-new Linux VM is deployed in a public subnet to host a small web app. The security team flags it as "live but unhardened." You must lock it down before traffic is allowed in.

**Walkthrough**
1. **Apply the CIS benchmark** — start from a **CIS-hardened image** or run the **CIS Benchmark** / Lynis scan; fix the high-severity findings (file permissions, service configs).
2. **Disable root SSH** — set `PermitRootLogin no` in `/etc/ssh/sshd_config`, then restart `sshd`. Use key-based auth only.
3. **Create a non-root admin** with `sudo` and a strong password; lock the account if not used.
4. **Configure the host firewall** — default-deny with **ufw** / **firewalld** / cloud **security group**: allow only `22` (or better, a bastion/`22` from VPN), `80/443`; drop everything else.
5. **Patch the OS** — `apt update && apt upgrade` / `yum update`; enable **automatic security updates**.
6. **Disable unused services** and remove default accounts.
7. **Verify** with an external port scan (`nmap`) — only intended ports should be open.

**Bonus question**
*Your firewall allows 22 from 0.0.0.0/0 but root login is already disabled. Is that enough for exam "hardened"?* → **No** — restrict SSH source (security group / firewall rule to admin CIDR or bastion) and prefer key-only auth. Disabling root is necessary but not sufficient.

### 6.2 — Secure an Exposed S3 Bucket Holding PII

**Scenario**
An S3 bucket named `company-pii-backup` is publicly readable and contains customer PII. A breach is imminent. Lock it down and prove it's safe.

**Walkthrough**
1. **Block all public access** — enable **Block Public Access** at bucket *and* account level.
2. **Remove the public bucket policy / ACL** — delete any `Principal: "*"` `GetObject` statements and set ACL to **private**.
3. **Enforce encryption at rest (SSE)** — enable **SSE-S3** (or **SSE-KMS** for audit/key control) on the bucket; turn on **default encryption**.
4. **Enable Versioning** — protects against accidental/malicious overwrite or delete.
5. **Enable logging & access tracking** — **S3 server access logs** + **CloudTrail data events** to a separate, restricted log bucket.
6. **Add a bucket policy deny** for unencrypted (`aws:SecureTransport false`) and non-KMS requests if required.
7. **Verify** — confirm anonymous `curl` of an object returns `AccessDenied`.

**Bonus question**
*Bucket policy says private but account-level Block Public Access is OFF. Can the bucket still leak?* → **Yes** — account-level settings override; if someone later adds a public ACL it'll work. Keep **account-level Block Public Access ON**.

### 6.3 — Convert a Privileged Container to Unprivileged

**Scenario**
A container runs with `--privileged` (full host access) because "it needs to mount stuff." Convert it to the **least-privilege** model without breaking the app.

**Walkthrough**
1. **Drop `--privileged`** and remove `--cap-add=ALL`.
2. **Run as non-root** — set `USER` in the Dockerfile / `runAsNonRoot: true` in Kubernetes; map a non-zero UID.
3. **Drop Linux capabilities** — keep only what's required (e.g. `NET_BIND_SERVICE` if binding port 80); use `--cap-drop=ALL` then add back the minimum.
4. **Read-only root filesystem** — `--read-only` (K8s `readOnlyRootFilesystem: true`); mount writable paths as `tmpfs` or specific volumes.
5. **Restrict volume permissions** — mount only needed paths, set `:ro` read-only where possible, and tighten host directory ownership/perms.
6. **Add seccomp/AppArmor** default profiles; set `no-new-privileges`.
7. **Verify** — `docker inspect` shows no `Privileged: true`, capabilities minimal, and the app still starts.

**Bonus question**
*You dropped --privileged but the app still needs to read /proc/sys/net. Is mounting that path as rw the right fix?* → **No** — that reopens the host. Use a targeted capability or a read-only, scoped sysctl; avoid mounting host `/proc`/`/sys` writable.

### 6.4 — Implement Zero Trust for an Internal Admin Panel

**Scenario**
An internal admin panel currently trusts anyone on the corporate network ("we're behind the firewall"). Apply **Zero Trust** so no request is implicitly trusted.

**Walkthrough**
1. **Verify every request** — require authn + authz on *each* call; no "once on the network, you're in."
2. **Enforce identity & device posture** — MFA, device compliance check, per-session tokens.
3. **Micro-segmentation** — place the panel in its own segment / **security group**; only the proxy/identity-aware gateway can reach it, not the general LAN.
4. **Least-privilege access** — admins get **just-in-time (JIT)**, scoped roles, not standing admin.
5. **Encrypt everything** — TLS in transit; assume the network is hostile.
6. **Continuous monitoring** — log every admin action; alert on anomalies.
7. **Verify** — a device *on the LAN but unauthenticated* must be denied, not silently allowed.

**Bonus question**
*Is putting the panel behind a VPN enough to call it Zero Trust?* → **No** — VPN gives network-level access (implicit trust once connected). Zero Trust means **per-request verification**, micro-seg, and no implicit trust even inside.

### 6.5 — Move a Hardcoded DB Password to a Secrets Vault + Enforce API Auth & Rate-Limit

**Scenario**
The app's DB password is hardcoded in `config.py` and committed to git. The public API has no auth and no throttling. Fix both.

**Walkthrough**
1. **Remove the secret from code** — delete the literal password; rotate it immediately (it's compromised).
2. **Store in a secrets manager** — **AWS Secrets Manager / Parameter Store**, **Azure Key Vault**, or **GCP Secret Manager**. Grant the app's role least-privilege read access only.
3. **Inject at runtime** — app fetches the secret via IAM-role auth at startup; never write it to disk or logs.
4. **Enable API authentication** — require API keys / OAuth2 / JWT on every endpoint.
5. **Add rate-limiting / throttling** — WAF or API gateway quota per client to stop abuse.
6. **Audit & scrub history** — check git history; rotate the secret again if it was ever pushed.
7. **Verify** — code contains no plaintext secret; API rejects unauthenticated calls and 429s past the limit.

**Bonus question**
*You moved the password to the vault but still print it in debug logs. Is the risk closed?* → **No** — logs leak it. Use secret **redaction** in logging and restrict log access.

---

## SECTION 7 — MEMORY AIDS

Quick recall hooks for exam day. Memorize the bold one-liners.

### 4.4.1 — Zero Trust (one-liner)
> **"Never trust, always verify — every request, every time, from anywhere."**
No implicit trust based on network location. Identity + device + context per request.

### 4.4.2 — CIS vs Vendor Benchmark
> **CIS = independent community hardening baseline; Vendor benchmark = the cloud provider's own hardening guide (e.g. AWS/Azure/GCP).**
Use **CIS** for OS/image baselines; use the **vendor** benchmark for provider-specific services. Exam loves "start from a CIS-hardened image."

### 4.4.5 — Transit vs Rest Distinction
> **In-transit = moving data (TLS/SSL, VPN, IPsec); At-rest = stored data (SSE, disk encryption, KMS).**
If data is *flying* → encrypt the **channel**. If data is *sitting* → encrypt the **storage**. Don't mix them up.

### 4.4.9 — Privileged Red-Flag
> **`--privileged`, `cap_add: ALL`, running as root, writable root FS, host /proc or /sys mounts = RED FLAG.**
Unprivileged = non-root + dropped caps + read-only FS + scoped volumes.

### 4.4.6 — Secrets Never in Code
> **Secrets belong in a vault, never in source, config files, or environment literals committed to git.**
Use **Secrets Manager / Key Vault / Secret Manager** + IAM-role injection + rotation.

### 4.4.8 — Least-Privilege / JIT
> **Give the minimum permission needed, and only for as long as needed (Just-In-Time).**
Standing admin = bad; scoped + time-boxed = good. Pair with **micro-segmentation**.

### ASCII Decision Tree — "Which Fix?"

```
                 WHAT'S WRONG WITH IT?
                          |
        +-----------------+-----------------+-----------------+------------------+
        |                 |                 |                 |                  |
     TOO OPEN         TOO MUCH ACCESS    PLAINTEXT         HARDCODED           |
   (open port /      (privileged /      (no encryption /  (password in        |
    public bucket)    standing admin)    clear data)        source code)       |
        |                 |                 |                 |                  |
        v                 v                 v                 v                  v
   Firewall /        Least-privilege +   Encryption:        Secrets vault +    |
   Block Public      JIT access +        transit (TLS) +    rotate + inject    |
   Access +          micro-seg           at-rest (SSE/KMS)   at runtime        |
   security group                                            (no git literal)  |
        |                 |                 |                 |                  |
        +-----------------+-----------------+-----------------+------------------+
                          v
                  VERIFY: re-scan / re-test the control actually blocks
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

Map each **4.4 security best practice** to the tool on each major cloud. The *concept* is provider-agnostic; only the *product name* changes — that's what the exam tests.

| Best Practice | AWS | Azure | GCP |
|---|---|---|---|
| **Zero Trust** (identity/device verify, micro-seg) | IAM + AWS Verified Access / PrivateLink + Security Groups | Entra ID (Conditional Access) + NSGs / Azure Firewall + Private Link | IAM + BeyondCorp Enterprise / VPC Service Controls |
| **CIS Benchmark** (hardened baseline) | CIS Amazon Linux 2 AMI / Inspector | CIS Azure marketplace image / Defender for Cloud | CIS GCE image / Security Command Center |
| **Secrets Management** | **Secrets Manager** / Parameter Store (SecureString) | **Key Vault** (Secrets) | **Secret Manager** |
| **Encryption / KMS** (at-rest + keys) | **KMS** (SSE-S3, SSE-KMS) | **Key Vault** (Keys) | **Cloud KMS** (CSM) |
| **Container Security** (unprivileged, caps, read-only FS) | ECS/EKS securityContext + IAM Roles for Tasks + Inspector | AKS securityContext + Defender for Containers | GKE securityContext + Artifact Registry scanning |
| **Object Storage Security** (block public, SSE, versioning, logs) | S3 Block Public Access + SSE + Versioning + Access Logs/CloudTrail | Blob Storage (private, SAS, encryption) + Diagnostic logs | Cloud Storage (Uniform access, SSE, Versioning, Audit logs) |

### Cross-Provider Reminders (apply on ANY cloud)
- **Encryption is two-sided:** always name **both** transit (TLS) and at-rest (SSE/KMS) — missing one is an exam trap.
- **Block Public Access beats bucket policies:** account/project-level blocks override per-resource settings — turn them ON for sensitive data.
- **Least privilege is universal:** scope IAM/role permissions to exactly what's needed; prefer **JIT** over standing access.
- **Secrets never live in code or images:** pull from the native vault at runtime using a service identity (instance/profile/Workload Identity).
- **CIS + vendor benchmarks stack:** CIS for the OS/image, vendor benchmark for the managed service.
- **Zero Trust = per-request verification**, not "behind a firewall/VPN." Micro-segment so the resource isn't reachable by default.
- **Containers:** non-root + drop capabilities + read-only root FS + scoped volumes = the safe pattern everywhere.
- **Log & verify:** enable audit logs on the KMS, storage, and IAM layers so you can *prove* the control works.


## SECTION 9 — INTERVIEW KNOWLEDGE

### Conceptual Q&A

**Q: What is the core idea behind Zero Trust, and why does it matter in cloud?**
A: Zero Trust assumes **no implicit trust** based on network location. Every request is authenticated, authorized, and encrypted regardless of whether it originates inside or outside the perimeter. In cloud, the perimeter is blurred (multi-tenant, remote, hybrid), so "verify explicitly" replaces "trust but verify."

**Q: How is a security benchmark different from a hardening guide?**
A: A **benchmark** (e.g., CIS Benchmark) is a published, versioned standard with scored settings and rationale. **Hardening** is the act of applying those (or vendor) recommendations to reduce the attack surface — disabling unneeded services, closing ports, removing default accounts. Benchmark = the spec; hardening = the implementation.

**Q: Why encrypt data at rest AND in transit? Aren't they the same risk?**
A: No. **Encryption in transit** (TLS) protects data moving between client and service, or service to service, from interception. **Encryption at rest** (AES-256 on disk/object storage) protects data sitting in storage from theft of the physical media or a storage-layer breach. You need both because the threat at each stage is different.

**Q: What is the difference between least privilege and separation of duties?**
A: **Least privilege** limits each identity to only the permissions needed for its task. **Separation of duties** splits a sensitive operation across multiple people/roles so no single identity can complete it alone (e.g., one requests, another approves). Least privilege is about scope; separation of duties is about splitting power.

### Scenario Questions

**Scenario 1 — Leaked hardcoded API key**
A developer committed a cloud storage **access key** directly into a public GitHub repo. What do you do?
1. **Revoke/rotate** the leaked key immediately in the cloud console — the old key is now compromised.
2. **Remove the secret** from the repo history (git filter-repo / BFG) and force-push; assume the plaintext is public.
3. **Move the secret** to a managed secrets store (AWS Secrets Manager, Azure Key Vault, HashiCorp Vault) and inject it at runtime via environment variables or mounts.
4. **Add guardrails**: pre-commit secret scanners (gitleaks), repo secret-scanning alerts, and CI policy to block commits containing high-entropy strings.
5. **Audit** access logs for any use of the key before rotation to check for abuse.

**Scenario 2 — Secure a container CI/CD pipeline**
A team builds Docker images in CI and deploys them to a Kubernetes cluster. How do you harden it?
1. **Scan images** in the pipeline (Trivy/Grype) and fail the build on critical CVEs — shift security left.
2. **Use minimal base images** (distroless/alpine) and pin digests, not floating tags.
3. **Sign images** (cosign/Notary) and enforce **image signature verification** at the admission controller.
4. **Run containers as non-root / unprivileged**; drop Linux capabilities; set `read_only_root_filesystem: true`.
5. **Inject secrets** from the cluster secrets manager, never bake them into the image layer.
6. **Least-privilege service accounts** and network policies restricting pod-to-pod traffic.

**Scenario 3 — Over-privileged IAM role**
You find an automation role granted `*` (full admin) "just to make it work." How do you remediate?
1. Review CloudTrail/audit logs to see what the role actually calls.
2. Replace the wildcard policy with a **least-privilege policy** listing only those actions/resources.
3. Add **conditions** (MFA, source IP, resource tags) and prefer **role assumption** over long-lived keys.
4. Enable **access analyzer** to flag externally shared resources and unused permissions, then trim.

### "Explain It Simply" Prompts

- *Explain Zero Trust to a non-technical manager:* "We no longer assume anyone inside our network is safe. Every person and device must prove who they are and be checked every time they ask for something — like a passport check at every door, not just the front gate."
- *Explain encryption to a friend:* "Encryption scrambles your data with a key. Without the key it's gibberish. In transit it protects data while it travels; at rest it protects data while it's parked on a disk."
- *Explain least privilege:* "Give people the smallest set of keys they need to do their job — no master key for the whole building if they only clean one office."
- *Explain container privilege:* "A privileged container is like giving a process the building master key and the ability to rewire the building. An unprivileged one gets only the one room it needs."

---

## SECTION 10 — FLASHCARDS

1. **Zero Trust** Q: What is the central principle of Zero Trust? A: Never trust, always verify — authenticate and authorize every request regardless of network location.

2. **Zero Trust Architecture** Q: What are the three core principles (NIST 800-207)? A: Continuous verification, least-privilege access, and assume breach.

3. **Implicit Trust** Q: What does Zero Trust remove? A: Implicit trust based on network position (inside vs. outside the perimeter).

4. **CIS Benchmarks** Q: What are CIS Benchmarks? A: Vendor-neutral, consensus-based hardening standards with scored configuration settings for OS, cloud, and apps.

5. **CIS Controls v8** Q: What are the CIS Critical Security Controls v8? A: A prioritized set of 18 safeguard families (inventory, data protection, access control, etc.) for defense-in-depth.

6. **Vendor Benchmark** Q: Give an example of a vendor-specific benchmark. A: AWS Foundations Benchmark, Azure Security Benchmark, or GCP Security Foundation Guide.

7. **Hardening** Q: Define system hardening. A: Reducing the attack surface by disabling unneeded services, removing default accounts, closing ports, and applying secure configs.

8. **Baseline Image** Q: Why use a hardened baseline image? A: Ensures every new VM/container starts from a known-secure configuration, not vendor defaults.

9. **Patching** Q: What is the goal of patching? A: Remediate known vulnerabilities by applying vendor updates before they are exploited.

10. **Patch Window** Q: What is a maintenance/patch window? A: A scheduled, low-impact time to apply updates with minimal disruption and a rollback plan.

11. **Patch Management** Q: Name the patch lifecycle steps. A: Inventory → assess/prioritize (CVSS) → test → deploy → verify → document.

12. **Encryption in Transit** Q: What protocol primarily protects data in transit? A: TLS (1.2/1.3); also IPsec for network tunnels.

13. **Encryption at Rest** A: Q: What protects data at rest? A: AES-256 (or equivalent) disk/object encryption, often with customer-managed keys (CMK).

14. **Key Management** Q: What is a KMS used for? A: Securely generating, storing, rotating, and controlling access to encryption keys.

15. **Bring Your Own Key (BYOK)** Q: What is BYOK? A: Customer supplies their own encryption keys to the cloud provider's KMS for greater control/control.

16. **Secrets Management** Q: What belongs in a secrets manager? A: API keys, DB passwords, tokens, certificates — never hardcoded in code or images.

17. **Secret Rotation** Q: Why rotate secrets regularly? A: Limits the blast radius if a secret leaks; short-lived credentials reduce exposure.

18. **API Security** Q: Name three API security controls. A: Authentication (OAuth2/API keys), rate limiting, input validation, and schema enforcement.

19. **API Gateway** Q: What does an API gateway provide for security? A: Centralized auth, throttling, WAF, logging, and request validation at the edge.

20. **Least Privilege** Q: Define least privilege. A: Grant each identity only the permissions required for its specific task — nothing more.

21. **RBAC** Q: What is Role-Based Access Control? A: Permissions assigned to roles, and users/groups inherit roles — easier least-privilege management.

22. **ABAC** Q: What is Attribute-Based Access Control? A: Access decisions based on attributes (user, resource, environment, time) — more granular than RBAC.

23. **Privileged Container** Q: Why avoid privileged containers? A: `--privileged` grants near-host-root capabilities, letting the container escape isolation and compromise the host.

24. **Unprivileged Container** Q: What is the secure default for containers? A: Run as non-root, drop capabilities, use a read-only root filesystem, and limit syscalls (seccomp).

25. **Container Capabilities** Q: What are Linux capabilities in containers? A: Fine-grained privileges (e.g., NET_ADMIN) you grant instead of full root; drop all unneeded ones.

26. **Root Filesystem Read-Only** Q: Why set read_only_root_filesystem? A: Prevents an attacker from writing malicious files to the container's filesystem at runtime.

27. **Object Storage Security** Q: How do you secure object storage (e.g., S3)? A: Block public access, use bucket policies/IAM, enable encryption, enable versioning and access logging.

28. **Bucket Policy** Q: What controls S3 bucket access? A: Bucket policies (resource-based), IAM policies (identity-based), and ACLs (legacy).

29. **Object Versioning** Q: Why enable versioning on object storage? A: Protects against accidental deletion/overwrite and ransomware by keeping prior versions.

30. **File Storage Security** Q: How secure cloud file storage (EFS/FSx)? A: Use NFS/TLS, security groups, POSIX/file permissions, and encrypt at rest with KMS.

31. **Encryption Keys — CMK vs Data Key** Q: What is the difference? A: CMK encrypts data keys; data keys encrypt the actual data — envelope encryption.

32. **TLS Termination** Q: What is TLS termination? A: Decrypting TLS at a load balancer/proxy; re-encrypt (end-to-end) if traffic crosses a trust boundary internally.

33. **MFA** Q: Why require MFA for privileged access? A: Adds a second factor, defeating stolen passwords/credentials in a Zero Trust model.

34. **Service Account** Q: How should machine identities be secured? A: Least-privilege roles, short-lived tokens, no long-lived static keys, scoped to workload.

35. **Network Segmentation** Q: How does segmentation support 4.4? A: Limits lateral movement; micro-segments/security groups enforce least-privilege network access.

36. **Immutable Infrastructure** Q: What is it and why secure? A: Servers/containers are never modified in place — rebuild from hardened images, reducing config drift.

37. **Audit Logging** Q: Why enable cloud audit logs (CloudTrail/Activity Log)? A: Provides an immutable record of who did what — essential for detection and forensics.

38. **CIS Hardening Level** Q: What are CIS Level 1 vs Level 2? A: Level 1 = practical, low-risk; Level 2 = deeper, may impact functionality for high-security needs.

39. **Vulnerability Scanning** Q: Where should scanning happen? A: In CI (images/code) and continuously on running assets (VMs, containers, dependencies).

40. **Shared Responsibility** Q: What does the cloud shared responsibility model say about 4.4? A: Provider secures the infrastructure; the customer is responsible for securing config, data, IAM, and workloads.

---

## SECTION 11 — PRACTICE QUESTIONS

1. **Which principle requires verifying every access request regardless of whether it originates inside or outside the corporate network?**
A. Defense in depth
B. Zero Trust
C. Kerberos
D. VLAN isolation
Answer: B — Zero Trust assumes no implicit trust by network location and verifies every request explicitly.

2. **A systems administrator wants a vendor-neutral, scored checklist to harden a new Linux deployment. Which should they use?**
A. NIST 800-53
B. CIS Benchmark
C. PCI DSS
D. SOC 2
Answer: B — CIS Benchmarks provide consensus-based, scored hardening settings for specific platforms.

3. **What is the primary purpose of applying security patches on a regular schedule?**
A. Improve application performance
B. Remediate known vulnerabilities before exploitation
C. Renew TLS certificates
D. Comply with data-residency laws
Answer: B — Patching closes known security holes identified by vendors and CVEs.

4. **Which encryption type protects data as it travels between a client and a cloud API over the internet?**
A. Encryption at rest
B. Disk encryption
C. Encryption in transit (TLS)
D. Homomorphic encryption
Answer: C — TLS encrypts data in transit, protecting it from interception on the wire.

5. **A database volume is encrypted using AES-256 on the cloud provider's storage. This is an example of:**
A. Encryption in transit
B. Encryption at rest
C. Tokenization
D. Hashing
Answer: B — Encrypting stored data on disk/volume is encryption at rest.

6. **An application stores its database password directly in source code that was pushed to a public repo. The BEST immediate remediation is to:**
A. Delete the repo
B. Revoke/rotate the credential and move it to a secrets manager
C. Rename the variable
D. Add a .gitignore entry
Answer: B — Rotate the exposed secret immediately and inject it at runtime from a secrets store.

7. **Which control best limits an API to legitimate callers and prevents abuse?**
A. Enabling verbose error messages
B. API authentication plus rate limiting
C. Disabling TLS
D. Allowing CORS from any origin
Answer: B — Auth identifies callers; rate limiting prevents brute force and DoS abuse.

8. **The security principle of granting a user only the permissions needed for their job is called:**
A. Separation of duties
B. Least privilege
C. Mandatory access control
D. Single sign-on
Answer: B — Least privilege minimizes the permissions (and blast radius) of each identity.

9. **A container is started with the `--privileged` flag. What is the main risk?**
A. It uses more CPU
B. It can escape isolation and compromise the host
C. It cannot access the network
D. Images build slower
Answer: B — Privileged containers gain host-level capabilities and can break out of isolation.

10. **To reduce container attack surface, a secure default configuration should:**
A. Run as root with a writable root filesystem
B. Run as non-root, drop capabilities, and use a read-only root filesystem
C. Mount the Docker socket
D. Disable seccomp
Answer: B — Non-root, dropped capabilities, and read-only root FS are core container-hardening defaults.

11. **In Kubernetes, the safest way to provide a database password to a pod is to:**
A. Bake it into the image
B. Store it in a ConfigMap in plaintext
C. Mount it from a secrets manager as a Secret
D. Hardcode it in the deployment YAML
Answer: C — Secrets (sourced from a secrets manager) keep credentials out of images and YAML.

12. **Which object-storage setting most directly prevents accidental public exposure of sensitive data?**
A. Lifecycle rules
B. Block Public Access
C. Cross-region replication
D. Storage class tiering
Answer: B — Block Public Access globally denies public bucket/object exposure regardless of other settings.

13. **Enabling versioning on an object-storage bucket primarily helps with:**
A. Reducing storage cost
B. Protecting against accidental deletion/overwrite and ransomware
C. Speeding up reads
D. Enabling static website hosting
Answer: B — Versioning retains prior copies, aiding recovery from deletion or encryption-based attacks.

14. **For cloud file storage (e.g., NFS-based), securing access includes all EXCEPT:**
A. Security groups / network rules
B. POSIX file permissions
C. Encryption at rest
D. Disabling all authentication
Answer: D — Disabling authentication weakens file-storage security; the others are standard controls.

15. **What does envelope encryption (CMK + data key) accomplish?**
A. Compresses data
B. Lets a master key encrypt data keys, which then encrypt the data — scalable key management
C. Removes the need for TLS
D. Backs up keys offsite
Answer: B — Envelope encryption uses a CMK to protect data keys, improving performance and control.

16. **Which access-control model uses attributes like time, location, and resource tags to make decisions?**
A. RBAC
B. ABAC
C. DAC
D. MAC
Answer: B — ABAC evaluates dynamic attributes for fine-grained authorization.

17. **A cloud account role was given `*` permissions "to avoid errors." The remediation is to:**
A. Keep it but add MFA
B. Replace with a least-privilege policy based on actual usage
C. Delete the account
D. Share the role widely
Answer: B — Scope the role to only the actions/resources actually used (least privilege).

18. **Continuous vulnerability scanning in a CI pipeline is intended to:**
A. Replace the need for runtime monitoring
B. Detect and block vulnerable images/code before deployment
C. Generate billing reports
D. Auto-scale workloads
Answer: B — Shifting scanning left catches flaws before they reach production.

19. **Under the shared responsibility model, which of the following is the CUSTOMER's responsibility?**
A. Physical security of the data center
B. Hypervisor patching
C. Securing IAM configuration and workload hardening
D. Underlying network hardware
Answer: C — Customers secure configurations, IAM, data, and workloads; providers secure the infrastructure.

20. **Which CIS Benchmark level is practical for most systems with low risk of impact?**
A. Level 0
B. Level 1
C. Level 2
D. Level 3
Answer: B — CIS Level 1 is the practical, low-risk profile suitable for most deployments.

---

## SECTION 12 — EXAM PRIORITY

| Rank | Sub-Objective | Likelihood | Why it's tested |
|------|---------------|------------|-----------------|
| 1 | Encryption (in-transit + at-rest) | High | Foundational, appears in many PBQ/scenario items; TLS vs AES distinction is a classic. |
| 2 | Least Privilege / IAM | High | Core to almost every cloud security scenario; RBAC/ABAC and over-privilege remediation. |
| 3 | Hardening & Benchmarks (CIS/vendor) | High | Directly named in 4.4; CIS Levels and baseline images are common distractors. |
| 4 | Secrets Management | High | Frequent real-world failure (hardcoded keys); rotation and secrets stores are testable. |
| 5 | Zero Trust | Med-High | Modern mandate; "never trust, always verify" phrasing appears across scenarios. |
| 6 | Patching | Med | Patch lifecycle and windows are straightforward remember/apply items. |
| 7 | Container Security (priv/unpriv/perms) | Med | Growing exam weight; privileged escape and read-only root FS are favorites. |
| 8 | API Security | Med | Auth, rate limiting, gateways — often paired with secrets/least privilege. |
| 9 | Storage Security (object/file) | Med | Bucket public-access blocks, versioning, file perms — practical and scenario-friendly. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

- **Zero Trust** — Never trust, always verify; no implicit trust by network location; continuous verification + least privilege + assume breach.
- **Benchmark (CIS + vendor)** — CIS Benchmarks = scored, vendor-neutral hardening specs; vendor benchmarks (AWS/Azure/GCP Foundations); Level 1 practical, Level 2 strict.
- **Hardening** — Reduce attack surface: disable services, remove defaults, close ports, baseline images, immutable infra.
- **Patching** — Inventory → assess (CVSS) → test → deploy → verify → document; use maintenance windows + rollback.
- **Encryption in transit** — TLS 1.2/1.3 (and IPsec); protects data moving between endpoints.
- **Encryption at rest** — AES-256 on disk/objects; use KMS + customer-managed keys (CMK/BYOK); envelope encryption.
- **Secrets management** — Store keys/passwords/tokens in a secrets manager; rotate; never hardcode; inject at runtime.
- **API security** — Authenticate (OAuth2/keys), rate limit, validate input, use API gateway + WAF + logging.
- **Least privilege** — Grant only needed permissions; use RBAC/ABAC; review with Access Analyzer; avoid wildcards.
- **Container security** — Run unprivileged/non-root, drop capabilities, read-only root FS, seccomp; avoid `--privileged`.
- **Storage — object** — Block Public Access, bucket/IAM policies, encryption, versioning, access logging (e.g., S3).
- **Storage — file** — Security groups, POSIX perms, TLS/NFS encryption, KMS at-rest (e.g., EFS/FSx).
- **Shared responsibility** — Provider = infrastructure; customer = config, IAM, data, workload hardening.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024-2025, CV0-004-relevant)

- **CIS Controls v8.1 (2024)** — Refined implementation groups and cloud/identity guidance; emphasizes asset inventory and data protection aligned with modern cloud workloads. Relevance: benchmark/hardening exam items now reflect v8 framing.

- **Zero Trust mandates & NIST SP 800-207 adoption (2024-2025)** — Continued federal and enterprise push for Zero Trust Architecture; micro-segmentation and continuous verification become default expectations. Relevance: Zero Trust is no longer "nice to have" on the exam.

- **Secrets-management shift to short-lived, dynamic credentials (2024-2025)** — Industry best practice moved from static long-lived keys toward just-in-time/rotating secrets (Vault dynamic secrets, cloud short-lived tokens). Relevance: strongly supports 4.4 secrets + least-privilege questions.

- **Container image signing & supply-chain security (Sigstore/cosign mainstream, 2024)** — Signed, verified images enforced at admission controllers became standard CI/CD practice post-SBOM push. Relevance: reinforces container-security and CI-pipeline hardening scenarios.

- **Cloud provider benchmark revisions (2024-2025)** — AWS/Azure/GCP Foundations Benchmarks updated for new services, default-encryption and public-access-block defaults; providers now enable encryption-at-rest and Block Public Access by default in many regions. Relevance: at-rest encryption and object-storage hardening are more "default-on" than before.
