# CompTIA Cloud+ CV0-004 — Objective 4.4: Apply Security Best Practices

## SECTION 1 — Exam Objectives

**In one breath:** Security best practices are the standard techniques used to shrink the attack surface and protect data across every layer of a cloud deployment — from the network trust model down to container privileges and storage buckets.

The 4.4 sub-objectives (verbatim from the official exam objectives, lines 463-477):

- **4.4.1 Zero Trust** — "Never trust, always verify"; no implicit trust by network location
- **4.4.2 Benchmark** — Hardening baselines: CIS (vendor-neutral) + vendor-specific (AWS/Azure/GCP)
- **4.4.3 Hardening** — Removing attack surface from OS/config
- **4.4.4 Patching** — Applying security updates on a cadence
- **4.4.5 Encryption** — Protecting data from exposure: in transit (TLS/VPN) and at rest (volumes/objects)
- **4.4.6 Secrets management** — Vaulting keys/passwords, not in code
- **4.4.7 API security** — Locking down programmatic endpoints
- **4.4.8 Principle of least privilege** — Minimum permissions, just-in-time
- **4.4.9 Container security** — Privileged vs unprivileged + file access permissions
- **4.4.10 Storage security** — Locking down object + file storage

**Mental model:** `Zero Trust (philosophy) → Benchmarks (the checklist) → Hardening+Patching (the OS) → Encryption+Secrets (the data) → API+Least-Priv+Containers+Storage (the workload)`. Every item is a way to shrink the attack surface and protect the data.

## SECTION 2 — EXAM KNOWLEDGE

### 4.4.1 — Zero Trust

- Model: "never trust, always verify" — no request is trusted simply because it came from inside the network
- Core ideas: assume breach, verify explicitly, enforce least-privilege + JIT access, apply micro-segmentation, perform continuous evaluation
- Contrast: replaces the castle-and-moat / perimeter model where everything behind the firewall was trusted
- Implement with: identity-aware proxies, MFA, conditional access, micro-segmentation, and just-in-time admin rather than a flat internal network

### 4.4.2 — Benchmark

- A benchmark is a hardening baseline — a standardized checklist applied consistently instead of from memory
- CIS (vendor-neutral): CIS Benchmarks, the prioritized CIS Controls, and CIS Hardened Images
- Vendor-specific: AWS Well-Architected Security Pillar / FSBP, Azure MCSB, GCP Security Foundations + CIS GCP via Security Command Center
- Use CIS as the universal starting point, then layer the provider's benchmark on top — they are complementary

### 4.4.3 — Hardening

- Reduce attack surface: disable unused services/ports/protocols; remove or disable default and unused accounts
- Enforce secure configuration: no default passwords, strong ciphers; disable root SSH, enable host firewall, remove unneeded packages
- A new over-permissioned server exposing extra ports → harden it (close ports, disable services, remove defaults), not merely "install patches"
- Hardening (removes attack surface) is related but distinct from patching (closes known vulnerabilities in what remains)

### 4.4.4 — Patching

- Apply vendor security updates on a regular, scheduled cadence with emergency patches for critical issues
- Test-first process: validate in a non-production account, stage the change, then roll to production — never blindly patch instantly
- Automation: AWS SSM Patch Manager, Azure Automation Update Management, GCP OS patch management
- "A vulnerable package was flagged but we can't break production" → patch on a tested cadence (test first, then roll out)

### 4.4.5 — Encryption

- In transit: encrypt moving data with TLS/SSL, VPN/IPsec, and in-service mTLS to stop eavesdropping and MITM
- At rest: protect stored volumes/objects with provider KMS (AWS KMS/Azure Key Vault/GCP KMS), S3 SSE, encrypted RDS/EBS
- "Traffic between services is sniffable" → enforce TLS; "disk stolen / bucket dumped" → enable at-rest encryption
- Use both: at-rest alone won't stop wire interception, and transit alone won't protect a stolen snapshot

### 4.4.6 — Secrets Management

- Store credentials (API keys, DB passwords, certs, tokens) in a dedicated vault, not in code/config/images
- Tools: AWS Secrets Manager / Parameter Store (SecureString), Azure Key Vault, GCP Secret Manager
- Practices: rotate on a schedule, prefer short-lived credentials, just-in-time retrieval, runtime injection (env var / mounted secret)
- Hardcoded secret in app/Dockerfile/config → move to secrets management and rotate; pair with pre-commit scanning (gitleaks)

### 4.4.7 — API Security

- AuthN/AuthZ on every call (OAuth 2.0, API keys, signed requests) — no anonymous endpoints
- Rate limiting and quotas to stop abuse, brute force, and surprise cost blowouts
- Input validation plus a WAF to block injection/malformed payloads; API gateway as the central auth/throttle/log point
- "A third party is hammering our API" / "we leak data through our API" → add auth, rate limiting, gateway, validation

### 4.4.8 — Principle of Least Privilege

- Grant only the permissions necessary to do the job — nothing more, and for no longer than needed
- Implement with RBAC + group-based permissions, avoid standing admin, use JIT elevation that expires for break-glass
- Directly supports Zero Trust and IAM authorization; "full admin but only reads one bucket" → scope the role down
- Shared standing admin credentials are always wrong: they remove accountability and create a huge blast radius

### 4.4.9 — Container Security

- Privileged (`--privileged`) gives near-root host access and bypasses isolation — a major risk, used only when truly required
- Unprivileged: non-root UID, dropped Linux capabilities, read-only root filesystem, no `--privileged` flag — stronger isolation
- File access: restrictive perms inside the container and on mounted volumes; mount secrets read-only with minimum scope
- Secure pattern: unprivileged + non-root + dropped caps + read-only FS; on K8s enforce PSS (restricted) + runAsNonRoot + SecurityContext

### 4.4.10 — Storage Security

- Object storage (S3/Blob/GCS): Block Public Access, least-privilege bucket policy/IAM, SSE, versioning, access logging
- Prefer scoped, short-TTL presigned/signed URLs instead of permanent public links
- File storage (EFS/Azure Files/FSx): restrictive mount permissions, encryption in transit + at rest, private endpoints/subnets
- "S3 bucket exposed PII" → block public access, lock policy, SSE, audit logs; "private by default" is not "secure by default"

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 Hardening a Public Web Server Fleet (AWS EC2 / Auto Scaling)

- Malaysian e-commerce web tier behind an ALB/Auto Scaling: CIS-hardened base AMI (disable root SSH, remove defaults, SELinux/AppArmor), least-privilege SG (443/80 only, 22 blocked, SSM Session Manager access), staged canary patching validated in non-prod then rolled on a window, TLS 1.2+ at ALB + backend mTLS, EBS KMS with rotated CMK, WAF + CloudWatch alarms — lesson: hardening = CIS baseline + least privilege + removing defaults + patching discipline + encryption both ways, not just "install patches."

### 3.2 Locking Down an S3 Bucket Holding PII (AWS S3 / Azure Blob / GCP)

- Payroll export with NRIC (PII) lands in a bucket: explicit deny public-access policy + account-level Block Public Access, SSE-KMS with CMK for auditability, least-privilege bucket policy (only HR app role gets s3:GetObject), versioning + MFA Delete + Object Lock, access logging + CloudTrail data events, lifecycle to Glacier + cross-region replication, forced TLS via policy — lesson: "private by default" is not "secure by default"; a default-allow policy is the classic trap.

### 3.3 Securing a Containerized Microservice (Privileged to Unprivileged)

- Microservice shipped `--privileged` "for convenience" breaks isolation (escape to host): move to unprivileged with cap_drop ALL / add only NET_BIND_SERVICE, non-root UID, read-only root FS, seccomp/AppArmor, on EKS/AKS/GKE enable PSS (restricted) + runAsNonRoot + SecurityContext, secrets from vault not image, image scanning before deploy, read-only /tmp mounts, network policies — lesson: privileged containers are almost never correct; "for convenience" is a red flag.

### 3.4 Implementing Zero Trust for an Internal Admin Tool

- Internal billing dashboard assumed "internal subnet = trusted" and a stolen WiFi laptop reached it directly: front with an identity-aware proxy (GCP IAP / Azure App Proxy / AWS IAM Identity Center), require MFA + device compliance + JIT elevation (no standing admin), micro-segment the tool's subnet, grant least-privilege billing role, log and rate-limit sessions — lesson: network location is never a trust signal; "it's internal, so it's safe" is the trap.

### 3.5 Secrets Management for a CI/CD Pipeline

- Production DB password hardcoded in a Dockerfile and committed to Git leaked via repo/image/history: move to a managed vault (Secrets Manager / Key Vault / Secret Manager), CI runner assumes an IAM role via OIDC (no static keys), fetches the secret at runtime and injects as an env var (never to disk), rotates every 30 days, scans images so no secret slips into a layer, gitleaks pre-commit — lesson: secrets belong in a vault, fetched via short-lived credentials, never in code/images/static keys.

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
| Source | Center for Internet Security (community) | AWS / Azure / GCP, OS vendors |
| Scope | Cross-platform OS/service baselines | Tailored to one platform's features |
| Goal | Neutral hardening baseline | Optimized for that cloud's controls |
| Example | CIS Ubuntu 22.04 L1/L2 | AWS Foundations Benchmark, Azure Security Baseline |
| Use | Universal starting point | Layered on top of CIS |

### 4.3 Encryption In-Transit vs At-Rest

| Dimension | Encryption In-Transit | Encryption At-Rest |
|-----------|------------------------|--------------------|
| Protects | Data moving over the network | Data stored on disk/volume |
| Mechanism | TLS 1.2+/mTLS, IPsec | KMS, SSE, LUKS, EBS/disk encryption |
| Threat | Interception, MITM | Theft of disk, backup, snapshot |
| Enforcement | Enforce HTTPS / aws:SecureTransport | Enable KMS/CMK on volumes & buckets |
| Exam note | Often forgotten — plaintext transit | Easy to enable, but not enough alone |

### 4.4 Privileged vs Unprivileged Containers

| Dimension | Privileged Container | Unprivileged Container |
|-----------|----------------------|------------------------|
| Host access | Full device/host access | Isolated, no host devices |
| Capabilities | All Linux caps | Dropped; add only needed |
| User | Often root | Non-root UID |
| Risk | Container escape to host compromise | Blast radius contained |
| Use case | Rare (e.g., some CNI plugins) | Default for app workloads |

### 4.5 Object Storage vs File Storage Security

| Dimension | Object Storage (S3/Blob/GCS) | File Storage (EFS/FSx/NFS) |
|-----------|------------------------------|----------------------------|
| Access model | Bucket policies, IAM, pre-signed URLs | POSIX perms, export policies, mount |
| Public exposure | Accidental public bucket (high risk) | Usually network-restricted mount |
| Encryption | SSE-KMS, enforced TLS | KMS/at-rest, in-transit NFSv4.1+ |
| Trap | Default-allow / misconfig policy | World-readable mount (777) |
| Audit | Access logs + CloudTrail data events | Filesystem audit logs |

### 4.6 Secrets in Code vs Secrets Management

| Dimension | Secrets in Code / Image | Secrets Management (Vault) |
|-----------|--------------------------|----------------------------|
| Storage | Hardcoded, committed to Git | Managed vault (Secrets Manager/Key Vault) |
| Lifetime | Long-lived, rarely rotated | Short-lived, auto-rotated |
| Exposure | Leaks via repo/image/history | Scoped IAM, fetched at runtime |
| Rotation | Manual, painful | Automated |
| Exam answer | Always wrong | Always the correct choice |

## SECTION 5 — AWS PROVIDER MAPPING

This objective is AWS-only for the mapping. The five AWS services that operationalize 4.4 security best practices are:

| 4.4 Best Practice | AWS Service | How it applies the practice |
|-------------------|-------------|------------------------------|
| Identity & access control (4.4.8 least privilege, 4.4.7 API security) | **IAM** | IAM roles, policies, and permission boundaries enforce least privilege; IAM policies on APIs and roles for service-to-service calls lock down programmatic access. |
| Encryption at rest & key management (4.4.5) | **KMS** | AWS Key Management Service creates and controls customer-managed keys (CMK) used for SSE on S3, EBS, RDS, and Secrets Manager — the backbone of encryption at rest. |
| DDoS / network protection (4.4.1 Zero Trust edge, 4.4.3 hardening) | **Shield** | AWS Shield (Standard/Advanced) provides managed DDoS protection at the edge, defending the internet-facing surface that hardening and Zero Trust aim to shrink. |
| Sensitive-data discovery (4.4.6 secrets, 4.4.10 storage security) | **Macie** | Amazon Macie uses ML to discover and protect sensitive data (PII, credentials) in S3, surfacing exposed buckets and secrets that violate storage-security best practices. |
| Secrets management (4.4.6) | **Secrets Manager** | AWS Secrets Manager stores, rotates, and retrieves database credentials, API keys, and tokens at runtime, eliminating hardcoded secrets in code and images. |

Note: KMS also underpins Secrets Manager encryption, and IAM governs who can use both — so these services compose to deliver the 4.4 objectives end to end.

## SECTION 6 — PRACTICE QUESTIONS

1. Which principle states "never trust, always verify" and refuses to trust a request just because it came from the internal network?
   A. Defense in depth
   B. Zero Trust
   C. Hashing
   D. Salting
   Answer: B — Zero Trust denies implicit trust by network location.

2. A company wants a standardized, vendor-neutral hardening checklist for its Linux servers. Which should it apply?
   A. A vendor-specific benchmark only
   B. CIS Benchmark
   C. A marketing whitepaper
   D. The default OS image with no changes
   Answer: B — CIS Benchmarks are the neutral, consensus hardening baselines.

3. A new internet-facing server exposes extra services and unused ports. What is the best first step?
   A. Install antivirus
   B. Harden it (close ports, disable services, remove defaults)
   C. Increase the instance size
   D. Add a second NIC
   Answer: B — hardening removes attack surface; patching alone is not enough.

4. A critical CVE is announced. Production cannot break. What is the correct patching approach?
   A. Patch production immediately, no testing
   B. Ignore it until next year
   C. Test in non-prod, then stage and roll out on a schedule
   D. Only patch non-critical systems
   Answer: C — staged, tested patching balances security with stability.

5. Traffic between two microservices is being sniffed on the network. Which control fixes this?
   A. Encryption at rest
   B. Encryption in transit (TLS/mTLS)
   C. Object versioning
   D. Rate limiting
   Answer: B — encrypt data moving between endpoints with TLS/mTLS.

6. A stolen EBS snapshot exposes customer data. Which best practice was missing?
   A. Encryption at rest
   B. Encryption in transit
   C. Container isolation
   D. Micro-segmentation
   Answer: A — at-rest encryption protects stored volumes and snapshots.

7. A developer hardcoded the production DB password in a Dockerfile committed to Git. The fix is to:
   A. Rotate and move it to a secrets vault
   B. Base64-encode it
   C. Put it in a config file
   D. Share it in chat
   Answer: A — secrets belong in a vault (Secrets Manager/Key Vault), fetched at runtime.

8. A public API is being hammered and leaking data. Which two controls should be added?
   A. AuthN/AuthZ and rate limiting
   B. More CPU only
   C. A bigger subnet
   D. Disabling logs
   Answer: A — API security requires authentication and throttling.

9. A service has full admin but only needs to read one bucket. The best fix is:
   A. Give it more permissions
   B. Apply least privilege (scope the role down)
   C. Share the admin creds
   D. Remove all permissions
   Answer: B — least privilege grants only what is needed.

10. A container runs `--privileged` "for convenience." On the exam this is:
    A. The recommended default
    B. A red flag — prefer unprivileged
    C. Required for all apps
    D. More secure than non-root
    Answer: B — privileged breaks isolation; use unprivileged containers.

11. The secure container pattern is:
    A. Root user, read-write FS, all caps
    B. Non-root, dropped caps, read-only FS
    C. Privileged, host /proc mounted
    D. No SecurityContext
    Answer: B — unprivileged + non-root + dropped caps + read-only FS.

12. An S3 bucket holding PII is found publicly readable. The correct response is:
    A. Leave it; S3 is private by default
    B. Block public access, lock policy, enable SSE, enable logs
    C. Delete the data only
    D. Add a larger instance
    Answer: B — disable public access, enforce TLS/SSE, and audit.

13. A file share (EFS) is mounted world-readable (777). The fix is:
    A. Keep it open for all apps
    B. Restrictive mount permissions + private networking
    C. Encrypt only in transit
    D. Disable encryption
    Answer: B — lock mount permissions and use private subnets/endpoints.

14. Which is a vendor-specific benchmark example?
    A. CIS Ubuntu Benchmark
    B. AWS Well-Architected Security Pillar / FSBP
    C. NIST CSF (general)
    D. OWASP Top 10
    Answer: B — vendor-specific benchmarks like AWS FSBP tailor to one provider.

15. Tightening file access permissions inside a container primarily protects against:
    A. DDoS
    B. One tenant reading another tenant's files
    C. Over-provisioning
    D. Slow builds
    Answer: B — restrictive file/volume perms limit cross-tenant exposure.

16. To force encrypted transit to an S3 bucket, you deny requests where:
    A. SourceIp is internal
    B. aws:SecureTransport is false
    C. UserAgent is curl
    D. Principal is *
    Answer: B — deny when aws:SecureTransport is false to require TLS.

17. Customer-managed encryption keys in AWS are managed by:
    A. IAM
    B. KMS
    C. CloudTrail
    D. Route 53
    Answer: B — KMS issues and controls the CMKs used for at-rest encryption.

18. Placing a WAF in front of an API primarily helps with:
    A. OWASP injection, XSS, and L7 abuse
    B. Encrypting disks
    C. Rotating secrets
    D. Container scheduling
    Answer: A — a WAF blocks injection/XSS and enforces L7 controls.

19. The most secure way for a CI runner to access a secret is:
    A. A long-lived static IAM key in the repo
    B. Assume a role via OIDC and fetch at runtime
    C. Hardcode in the Dockerfile
    D. Email it to the team
    Answer: B — short-lived, role-based creds avoid static-key leakage.

20. "Internal subnet = trusted" violates which model?
    A. Perimeter / castle-and-moat
    B. Zero Trust
    C. Hashing
    D. RAID
    Answer: B — Zero Trust says network location is never a trust signal.
