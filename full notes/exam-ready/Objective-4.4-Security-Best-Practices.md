# Objective 4.4 — Given a scenario, apply security best practices

> **Domain 4.0 — Security (19% of exam)** · Exam CV0-004 V4 · Objectives doc v5.0
> **Status:** Exam-ready · **Version:** 2.0 (enhanced) · Supersedes `full notes/Objective-4.4-Security-Best-Practices.md`

---

## 0. How to use this note

| Pass | What to read | Time |
|---|---|---|
| **1st (learn)** | Sections 1 → 11 in order | ~70 min |
| **2nd (drill)** | Section 3 (Zero Trust) + Section 7 (secrets) + Section 9 (container security) | ~20 min |
| **3rd (test)** | Section 15 (practice) + Section 16 (PBQ drills) | ~30 min |
| **Exam eve** | Section 17 (60-second recall sheet) only | ~5 min |

> 📌 **Ten sub-topics, and several are synthesis** — patching is 3.4, least privilege is 4.3, container basics are 1.6, storage types are 1.4. The genuinely new material is **Zero Trust, benchmarks/hardening, secrets management, API security, and the privileged-container distinction**. Focus your time there.

---

## 1. Official objective coverage

> **4.4 Given a scenario, apply security best practices.**
> - Zero Trust
> - **Benchmark** — Center for Internet Security (CIS) · Vendor-specific
> - Hardening
> - Patching
> - **Encryption** — Data in transit · Data at rest
> - Secrets management
> - API security
> - Principle of least privilege
> - **Container security** — Privileged · Unprivileged · File access permissions
> - **Storage security** — Object storage · File storage

### 1.1 What the verb tells you

**"Given a scenario, apply"** — an **application** objective. You are shown an insecure configuration or a requirement and asked which practice fixes it. The safest answer is almost always **the more restrictive option that still meets the functional need**.

### 1.2 Coverage checklist

- [ ] I can state the three **Zero Trust** principles and contrast it with perimeter security
- [ ] I know what a **CIS Benchmark** is and how it differs from CIS **Controls**
- [ ] I can list the main **hardening** actions
- [ ] I know the three **states of data** and what protects each
- [ ] I know why **secrets must never live in code, images, or plain environment files**
- [ ] I can name at least five **API security** controls
- [ ] ★ I know what a **privileged container** is and why it is dangerous
- [ ] I know the container **file-permission** practices — non-root user, read-only root filesystem
- [ ] I know the **object storage** and **file storage** security controls
- [ ] I know **least privilege** and **patching** connect to 4.3 and 3.4

---

## 2. The core mental model

### 2.1 Defence in depth

```text
   No single control is trusted to hold. Layer them so a failure
   in one is contained by the next.

   ┌──────────────────────────────────────────────────────────┐
   │ GOVERNANCE   policy · benchmarks · compliance (4.2)      │
   ├──────────────────────────────────────────────────────────┤
   │ IDENTITY     least privilege · MFA · federation (4.3)    │
   ├──────────────────────────────────────────────────────────┤
   │ NETWORK      segmentation · security groups · WAF (1.3)  │
   ├──────────────────────────────────────────────────────────┤
   │ HOST         hardening · patching · endpoint protection  │
   ├──────────────────────────────────────────────────────────┤
   │ APPLICATION  API security · input validation · secrets   │
   ├──────────────────────────────────────────────────────────┤
   │ DATA         encryption at rest and in transit ·         │
   │              classification · storage policy             │
   ├──────────────────────────────────────────────────────────┤
   │ DETECTION    logging · monitoring · audit trail (3.1,4.6)│
   └──────────────────────────────────────────────────────────┘
```

### 2.2 Secure by default

Every practice in this objective is an instance of one rule:

```text
   START CLOSED, OPEN DELIBERATELY.

   ✗ Deploy, then lock down          ✓ Deny by default, grant explicitly
   ✗ Public unless restricted        ✓ Private unless published
   ✗ All capabilities, then trim     ✓ Minimum capabilities, add if needed
   ✗ Long-lived credentials          ✓ Short-lived, rotated, scoped

   ★ On the exam, the more restrictive option that still meets the
     stated requirement is nearly always correct.
```

---

## 3. Zero Trust

| | |
|---|---|
| **Definition** | A security model that removes **implicit trust based on network location**. Every request is authenticated, authorised, and validated regardless of where it originates. |
| **The slogan** | **"Never trust, always verify."** |
| **★ Three principles** | ① **Verify explicitly** — authenticate and authorise every request from all available signals (identity, device, location, behaviour). ② **Use least-privilege access** — just-enough, just-in-time. ③ **Assume breach** — segment, minimise blast radius, log everything, verify end to end |
| **Key mechanisms** | Strong identity and **MFA** · **micro-segmentation** · device health/posture checks · continuous re-verification, not one-time login · encryption everywhere, including internal traffic · comprehensive logging |
| **Exam triggers** | "never trust, always verify", "no implicit trust from being on the internal network", "verify every request", "assume breach", "micro-segmentation" |

```text
   PERIMETER MODEL ("castle and moat")   ZERO TRUST
   ┌──────────────────────────────┐      ┌──────────────────────────────┐
   │        FIREWALL              │      │  every request verified,      │
   │  ┌────────────────────────┐  │      │  wherever it comes from       │
   │  │ INSIDE = TRUSTED       │  │      │                               │
   │  │  hosts talk freely     │  │      │  [svc]⇄verify⇄[svc]           │
   │  │  little internal auth  │  │      │    ⇅              ⇅           │
   │  └────────────────────────┘  │      │  verify          verify       │
   │  OUTSIDE = UNTRUSTED         │      │    ⇅              ⇅           │
   └──────────────────────────────┘      │  [user]        [device]       │
                                         └──────────────────────────────┘
   ⚠ Once inside, an attacker moves       ✓ Lateral movement is blocked —
     LATERALLY almost unopposed             each hop must authenticate
```

> ★ **Why it matters in cloud:** there is no meaningful perimeter. Workloads span regions and providers, users work from anywhere, and services call each other over networks you do not own. "Inside the network" stopped being a useful trust signal — which is why identity became the new perimeter (4.3).

---

## 4. Benchmarks and hardening

### 4.1 Benchmarks

| | |
|---|---|
| **Definition** | Published, consensus-developed **configuration baselines** stating how a system should be securely configured. |
| **CIS Benchmarks** | From the **Center for Internet Security** — prescriptive, per-technology guides (OS, cloud provider, database, container platform). Typically **Level 1** (essential, low operational impact) and **Level 2** (defence-in-depth, may affect functionality) |
| **★ CIS Benchmarks vs CIS Controls** | **Benchmarks** = how to configure *a specific technology*. **Controls** = a prioritised list of *organisational security actions*. Different artefacts, same organisation |
| **Vendor-specific** | The provider's or vendor's own hardening guides and well-architected security guidance; also government baselines such as DISA STIGs |
| **How they are used** | As the **measurable definition of "hardened"** — you can scan against a benchmark and produce a compliance score, which is exactly what auditors want (4.2) |
| **Exam triggers** | "industry-standard configuration baseline", "CIS Benchmark", "measure configuration against a standard", "hardening guide" |

### 4.2 Hardening

| | |
|---|---|
| **Definition** | Reducing a system's **attack surface** by removing or disabling everything not required. |
| **Exam triggers** | "reduce the attack surface", "disable unnecessary services", "remove default accounts", "minimal installation", "golden image baseline" |

```text
   ATTACK SURFACE REDUCTION — the standard actions

   ① REMOVE   unnecessary software, packages, sample content
   ② DISABLE  unused services, protocols, and features
   ③ CLOSE    ports that need not listen
   ④ DELETE   default and unused accounts; ★ CHANGE DEFAULT
              CREDENTIALS — the single most exploited weakness
   ⑤ RESTRICT least-privilege file permissions; no world-writable
   ⑥ ENFORCE  secure configuration: strong ciphers, disable legacy
              protocols, enable logging
   ⑦ APPLY    a benchmark, then SCAN to verify (4.1)
   ⑧ BAKE     the result into a GOLDEN IMAGE so every instance
              starts hardened (1.7, 3.4)

   ★ Hardening once into an image beats hardening each server —
     it eliminates configuration drift (2.4).
```

---

## 5. Patching

Covered in depth in **3.4** (as maintenance) and **4.1** (as remediation). The security-practice summary:

| Practice | Note |
|---|---|
| **Automate** where possible | Manual patching does not keep pace |
| **Test in rings** | dev → staging → canary → fleet |
| **Prioritise by risk** | Severity × exposure × exploit availability (4.1) |
| **Prefer immutable replacement** | Patch the golden image and replace instances — no drift |
| **Track EOS** | Software past end of support is **permanently unpatchable** (3.4) |
| **Verify** | Rescan to confirm the patch actually applied fleet-wide |

---

## 6. Encryption

### 6.1 The three states of data

```text
   ┌───────────────────┬──────────────────────────────────────────┐
   │ DATA IN TRANSIT   │ moving across a network                  │
   │                   │ → TLS/HTTPS · IPsec VPN · SSH ·          │
   │                   │   mTLS between services                  │
   │                   │ ⚠ encrypt INTERNAL traffic too           │
   │                   │   (Zero Trust)                            │
   ├───────────────────┼──────────────────────────────────────────┤
   │ DATA AT REST      │ stored on disk or in a service           │
   │                   │ → AES-256 · volume/bucket/database       │
   │                   │   encryption · backups AND snapshots     │
   ├───────────────────┼──────────────────────────────────────────┤
   │ DATA IN USE       │ loaded in memory during processing       │
   │ (adjacent)        │ → confidential computing / enclaves      │
   └───────────────────┴──────────────────────────────────────────┘
```

### 6.2 Key management is the hard part

| Concept | Meaning |
|---|---|
| **KMS** | Managed key service — generates, stores, rotates, and audits keys |
| **Envelope encryption** | Data is encrypted with a **data key**; the data key is encrypted by a **master key**. Rotating the master key does not require re-encrypting all data |
| **Provider-managed vs CMK/BYOK** | Who controls key policy and revocation (see 4.2 — this is also a **sovereignty** lever) |
| **Rotation** | Periodic, automated; old keys retained to decrypt old data |
| **★ Separation of duties** | Whoever administers the data should **not** also control the keys |
| **★ Losing the key** | Loses the data — keys must be backed up and available in the DR region (3.3) |

> ⚠️ **Encryption protects confidentiality, not availability or integrity of access.** It does not stop someone deleting data, and it does not satisfy a **residency** requirement — encrypted data still physically resides somewhere (4.2).

---

## 7. Secrets management

| | |
|---|---|
| **Definition** | Securely storing, distributing, rotating, and auditing **credentials, API keys, tokens, certificates, and connection strings**. |
| **Exam triggers** | "credentials in the repository", "rotate the database password", "inject at runtime", "secret store", "vault" |

```text
   ✗ WHERE SECRETS MUST NOT LIVE          ✓ WHERE THEY SHOULD

   · in source code                       · a managed SECRET STORE /
     ★ git history keeps them FOREVER —     vault, access-controlled
       deleting the line does not help    · injected AT RUNTIME as
   · in container images                    environment variables or
     ★ image layers keep them forever       mounted files
     (1.6)                                · better still: NO SECRET AT
   · in IaC state files                     ALL — use WORKLOAD IDENTITY
     ★ often stored in plaintext (2.4)      so the platform issues
   · in plain config or .env in the repo    short-lived credentials (4.3)
   · in logs, tickets, or chat

   ★ IF A SECRET WAS EVER COMMITTED, IT IS COMPROMISED.
     Removing it is not enough — YOU MUST ROTATE IT.
```

**Practices that get tested:**

| Practice | Why |
|---|---|
| **Centralised secret store** | One audited place, with access control and versioning |
| **Rotation** — automated | Limits the value of a leaked secret |
| **Dynamic/short-lived secrets** | Generated per session and expire — nothing long-lived to steal |
| **Least privilege on the secret itself** | Only the workload that needs it may read it |
| **Audit access** | Who read which secret, when (4.3) |
| **Never log secrets** | Redact at source (3.1) |
| **Scan repositories and images** | Detect committed secrets (4.1) |

---

## 8. API security

APIs are the primary interface to cloud services, so they are the primary attack surface.

| Control | Purpose |
|---|---|
| **Authentication** | Every call identified — tokens or workload identity, **not** shared static keys |
| **Authorization** | Scoped permissions per caller; **least privilege** per endpoint (4.3) |
| **TLS everywhere** | No plaintext API traffic, internal included |
| **★ Input validation** | Validate type, length, format, and range **server-side** — the defence against injection |
| **★ Rate limiting / throttling** | Prevents brute force, scraping, and resource exhaustion; protects downstream services |
| **API gateway** | Central enforcement point for auth, throttling, logging, and routing (1.3) |
| **Schema validation** | Reject malformed payloads before processing |
| **Avoid excessive data exposure** | Return only required fields — do not filter in the client |
| **Versioning and deprecation** | Retire insecure versions deliberately |
| **Logging and monitoring** | Detect abuse patterns (4.6) |
| **CORS configured deliberately** | Do not allow all origins by default |

> ⚠️ **Two frequently tested API failures:** **broken object-level authorization** — the caller is authenticated, but the API never checks the record belongs to them, so changing an ID in the request returns someone else's data; and **no rate limiting**, which turns a login endpoint into a brute-force oracle.

---

## 9. Container security

### 9.1 ★ Privileged vs unprivileged

```text
   UNPRIVILEGED (default, correct)     PRIVILEGED (dangerous)
   ┌──────────────────────────┐        ┌──────────────────────────┐
   │      CONTAINER           │        │      CONTAINER           │
   │  limited capabilities    │        │  ★ NEARLY ALL HOST       │
   │  no host device access   │        │    CAPABILITIES          │
   │  namespaces enforced     │        │  host devices accessible │
   └───────────┬──────────────┘        │  isolation EFFECTIVELY   │
               │ isolated               │  DISABLED                │
   ┌───────────▼──────────────┐        └───────────┬──────────────┘
   │      HOST KERNEL         │                    │ ⚠ direct
   └──────────────────────────┘        ┌───────────▼──────────────┐
                                       │      HOST KERNEL         │
                                       └──────────────────────────┘
   ✓ Container compromise stays        ⚠ CONTAINER COMPROMISE
     inside the container                = HOST COMPROMISE
                                        = every other container on it
```

| | **Unprivileged** | **Privileged** |
|---|---|---|
| Capabilities | Restricted set | **Nearly all host capabilities** |
| Host device access | ❌ No | ✅ Yes |
| Isolation | Enforced | **Effectively removed** |
| Blast radius if compromised | The container | **The host and every container on it** |
| When justified | **Almost always — the default** | Rare system-level tooling only, with compensating controls |

> ★ **`--privileged` is the single most dangerous container flag.** Related and equally serious: **mounting the container runtime socket** into a container effectively grants control of the host, and **hostPath mounts** expose host filesystem paths. If a scenario mentions any of these, that is the vulnerability.

### 9.2 File access permissions and hardening

| Practice | Why |
|---|---|
| **★ Run as a non-root user** | A root process that escapes is root on the host; specify a non-root user in the image |
| **Read-only root filesystem** | Stops an attacker writing tools into the container |
| **Drop capabilities** | Grant only what the workload actually needs |
| **Least-privilege volume permissions** | Mount read-only where possible; correct UID/GID ownership; never world-writable |
| **No sensitive host paths mounted** | Especially not the runtime socket |
| **Minimal base images** | Fewer packages → far fewer CVEs (1.6, 4.1) |
| **Scan images** | Before deployment, and continuously (4.1) |
| **No secrets in images** | They persist in layers forever (Section 7) |
| **Resource limits** | Prevents one container starving the node (1.6) |
| **Namespace and network policy segmentation** | Limits lateral movement — Zero Trust inside the cluster |

---

## 10. Storage security

### 10.1 Object storage

```text
   ★ THE OBJECT STORAGE CHECKLIST

   ① BLOCK PUBLIC ACCESS at the account AND bucket level
      → public buckets are the classic cloud breach
   ② POLICY-BASED ACCESS, least privilege
      → no wildcard principals ("allow everyone")
   ③ ENCRYPTION AT REST, with managed or customer keys
   ④ TLS ENFORCED for access (deny non-TLS requests)
   ⑤ VERSIONING to survive overwrite and deletion
   ⑥ OBJECT LOCK / WORM where immutability is required (1.4, 4.2)
   ⑦ PRE-SIGNED URLS with short expiry for temporary sharing
      → instead of making the object public
   ⑧ ACCESS LOGGING for the audit trail (3.1)
   ⑨ LIFECYCLE POLICIES to expire what should not persist
   ⑩ PRIVATE ENDPOINTS so traffic never traverses the internet (1.3)
```

### 10.2 File storage

| Control | Note |
|---|---|
| **Share-level and file-level permissions** | POSIX modes/ACLs or NTFS ACLs — least privilege, and no anonymous access |
| **Network restriction** | Reachable only from private subnets and specific security groups (1.3) |
| **Encryption in transit** | NFS and SMB must be encrypted — do not assume the network is trusted |
| **Encryption at rest** | On the file system itself |
| **Identity integration** | Map access to directory identities rather than raw UIDs where possible (4.3) |
| **Audit access** | Who read or changed what |

> ⚠️ **The classic difference in exposure:** object storage is reachable **from the internet by default design** (it is an HTTP service), so the primary risk is a **public bucket**. File storage is normally **network-attached inside a VPC**, so the primary risk is **over-broad share permissions** and unencrypted protocol traffic.

---

## 11. Least privilege

Covered in depth in **4.3**. The summary that matters here:

| Principle | Application |
|---|---|
| **Grant only what is needed** | Per user, per role, per workload, per API endpoint, per secret, per bucket |
| **Deny by default** | Nothing permitted unless explicitly granted |
| **Just-in-time elevation** | No standing administrative rights |
| **Periodic access review** | Counters permission creep |
| **Workload identity** | Applications get scoped, short-lived credentials — never static keys |

> ★ **Least privilege is the most frequently correct answer across Domain 4.** When a scenario offers a broader and a narrower permission that both satisfy the requirement, choose the narrower.

---

## 12. Comparison tables

### 12.1 Perimeter vs Zero Trust

| | **Perimeter ("castle and moat")** | **Zero Trust** |
|---|---|---|
| Trust basis | **Network location** | **Verified identity and context, every request** |
| Inside the network | Implicitly trusted | **Not trusted** |
| Lateral movement | Largely unopposed | **Blocked — each hop re-verifies** |
| Verification | Once, at the boundary | **Continuous** |
| Segmentation | Coarse | **Micro-segmentation** |
| Fits cloud | ❌ Poorly — there is no perimeter | ✅ **Designed for it** |

### 12.2 Encryption states

| | **In transit** | **At rest** | **In use** |
|---|---|---|---|
| Protects against | Network interception | Media/storage theft, unauthorised read | Memory inspection |
| Mechanisms | **TLS, IPsec/VPN, SSH, mTLS** | **AES-256**, volume/bucket/database encryption, KMS | Confidential computing/enclaves |
| Also applies to | **Internal service-to-service traffic** | **Backups and snapshots** | — |
| Common gap | Assuming internal traffic is safe | Forgetting backups and snapshots | Rarely required |

### 12.3 Container security posture

| Setting | Insecure | Secure |
|---|---|---|
| Privilege | `--privileged` | **Unprivileged, capabilities dropped** |
| User | root | **Non-root user** |
| Root filesystem | Writable | **Read-only** |
| Runtime socket | Mounted into the container | **Never mounted** |
| Base image | Large, full distro | **Minimal/distroless** |
| Secrets | Baked into the image | **Injected at runtime / workload identity** |
| Volumes | World-writable, host paths | **Least privilege, read-only where possible** |
| Scanning | None | **On push and continuously** |

### 12.4 Scenario → practice

| Scenario | Practice |
|---|---|
| "No implicit trust from being on the corporate network" | **Zero Trust** |
| "Configure servers against an industry-standard baseline" | **CIS Benchmark** |
| "Remove unnecessary services and default accounts" | **Hardening** |
| "Traffic between microservices must be encrypted" | **Encryption in transit (mTLS)** |
| "Protect data if a disk is stolen" | **Encryption at rest** |
| "Database password is in the git repository" | **Secrets management — and rotate it** |
| "Login endpoint is being brute-forced" | **Rate limiting/throttling** |
| "Changing the ID in the URL returns another customer's record" | **Broken object-level authorization — enforce per-object checks** |
| "Container has full host access" | **Remove `--privileged`; run unprivileged** |
| "Container runs as root" | **Specify a non-root user; read-only root filesystem** |
| "Storage bucket is publicly readable" | **Block public access; use pre-signed URLs** |
| "Share must not be reachable from the internet" | **File storage in a private subnet with restricted security groups** |
| "Two permission sets both work" | **Choose the narrower — least privilege** |

---

## 13. Exam traps & distractor patterns

| # | Trap | The truth |
|---|---|---|
| 1 | "Zero Trust means distrusting employees" | It means **no implicit trust from network location** — every request is verified regardless of origin |
| 2 | "Zero Trust is a product you buy" | It is a **model** implemented through identity, MFA, micro-segmentation, and continuous verification |
| 3 | "Internal traffic doesn't need encryption" | Zero Trust and defence in depth require **encrypting internal traffic too** |
| 4 | "Encryption at rest satisfies data residency" | Encrypted data still **physically resides** somewhere. Residency is about location (4.2) |
| 5 | "Encryption protects against deletion" | It protects **confidentiality** only. Deletion needs versioning, object lock, and backups |
| 6 | "Deleting the committed secret fixes it" | ❌ **Git history and image layers keep it forever — you must ROTATE the secret** |
| 7 | "Environment variables are a secure place for secrets" | Better than code, but still exposed in process listings, logs, and dumps. Use a **secret store** or **workload identity** |
| 8 | "Privileged containers are just containers with more permissions" | They **effectively remove isolation** — a compromise becomes a **host compromise** |
| 9 | "Running as root inside a container is contained anyway" | If the process escapes, it is **root on the host**. Run as non-root |
| 10 | "Mounting the container runtime socket is convenient and safe" | It effectively grants **control of the host** |
| 11 | "CIS Benchmarks and CIS Controls are the same" | **Benchmarks** configure a specific technology; **Controls** are prioritised organisational actions |
| 12 | "Hardening each server individually is best practice" | **Bake the hardened baseline into a golden image** — per-server hardening produces drift (2.4, 3.4) |
| 13 | "An authenticated API is a secure API" | Authentication is not authorization. **Per-object authorization** and **input validation** are separate, and both are commonly missing |
| 14 | "Rate limiting is only about cost" | It is a **security control** against brute force, scraping, and resource exhaustion |
| 15 | "Make the object public so the partner can download it" | Use a **pre-signed URL with a short expiry** instead |
| 16 | "Object and file storage have the same exposure profile" | Object storage is internet-reachable by design — the risk is a **public bucket**. File storage sits inside the VPC — the risk is **over-broad share permissions** |

**Disambiguation drill:**

| Pair | The deciding question |
|---|---|
| **Zero Trust vs perimeter** | Is trust based on **location** or on **verified identity per request**? |
| **Benchmark vs hardening** | The **standard** to configure against, or the **act** of reducing attack surface? |
| **CIS Benchmarks vs CIS Controls** | Configure **a technology**, or prioritise **organisational actions**? |
| **In transit vs at rest** | Data **moving**, or data **stored**? |
| **Secret store vs environment variable** | Centrally managed, audited, rotatable — or merely out of the code? |
| **Privileged vs unprivileged** | Does the container retain **host-level capabilities**? |
| **Object vs file storage risk** | **Public exposure** vs **over-broad share permissions** |

---

## 14. Keyword → answer trigger table

| If you see… | Apply |
|---|---|
| never trust always verify · no implicit trust from the network · verify every request · assume breach · micro-segmentation | **Zero Trust** |
| industry-standard configuration baseline · CIS · Level 1/Level 2 · vendor hardening guide | **Benchmark** |
| disable unnecessary services · remove default accounts · minimal install · reduce attack surface | **Hardening** |
| bake the baseline into the image so every instance starts secure | **Golden image hardening** |
| TLS · HTTPS · mTLS between services · VPN | **Encryption in transit** |
| AES-256 · encrypted volumes, buckets, databases, **and backups** | **Encryption at rest** |
| rotating master key without re-encrypting all data | **Envelope encryption** |
| credentials in the repository or image | **Secrets management — and ROTATE** |
| generated per session and expires | **Dynamic/short-lived secrets** |
| brute-force attempts against the login endpoint | **Rate limiting / throttling** |
| changing an ID returns another user's record | **Broken object-level authorization** |
| injection through unvalidated input | **Server-side input validation** |
| central enforcement of auth, throttling, and logging for APIs | **API gateway** |
| container has host-level access | **Privileged container — remove it** |
| container process runs as root | **Run as non-root; read-only root filesystem** |
| container runtime socket mounted inside a container | **Host compromise risk** |
| bucket is publicly readable | **Block public access** |
| share a file temporarily with an external party | **Pre-signed URL with short expiry** |
| NFS/SMB share reachable too broadly | **Private subnet + security groups + share permissions** |
| two permission options both satisfy the requirement | **Choose the narrower — least privilege** |

---

## 15. Practice questions

<details>
<summary><b>Q1.</b> Which principle BEST describes Zero Trust?</summary>

A. Trust everything inside the corporate firewall · **B. Never trust, always verify — no implicit trust is granted based on network location; every request is authenticated and authorised** · C. Trust only encrypted traffic · D. Trust devices but not users

**Correct: B.** Zero Trust removes location as a trust signal and verifies explicitly, applies least privilege, and assumes breach.
- **A wrong:** That is the perimeter model Zero Trust replaces.
- **C/D wrong:** Both are partial controls, not the principle.
</details>

<details>
<summary><b>Q2.</b> A developer discovers a database password was committed to the repository six months ago and deletes the line. Is the issue resolved?</summary>

A. Yes, the line is gone · **B. No — the secret remains in git history and must be rotated, then moved to a secret store** · C. Yes, if the repository is private · D. Yes, if the file was encrypted afterwards

**Correct: B.** Git history retains every version; anyone with repository access can recover it. **The credential must be treated as compromised and rotated.**
- **A/D wrong:** Deletion and later encryption do not remove history.
- **C wrong:** Private does not mean secret — everyone with access still sees it.
</details>

<details>
<summary><b>Q3.</b> What is the PRIMARY risk of running a container with the privileged flag?</summary>

A. It consumes more memory · **B. It retains nearly all host capabilities, effectively removing isolation — a container compromise becomes a host compromise affecting every container on that host** · C. It cannot be scanned · D. It prevents scaling

**Correct: B.** Privileged mode defeats the isolation that makes containers safe to co-locate.
- **A/C/D wrong:** None is the security consequence.
</details>

<details>
<summary><b>Q4.</b> An external partner needs to download one file from object storage for 24 hours. What is the MOST secure approach?</summary>

A. Make the bucket publicly readable · B. Email the file · **C. Issue a pre-signed URL with a short expiry** · D. Create a permanent access key for the partner

**Correct: C.** A time-limited signed URL grants access to exactly one object for exactly as long as needed, with no public exposure.
- **A wrong:** Public buckets are the classic cloud breach.
- **B wrong:** Uncontrolled and unauditable.
- **D wrong:** A permanent credential far exceeds the requirement.
</details>

<details>
<summary><b>Q5.</b> A team wants to configure servers against a recognised, measurable security baseline. What should they use?</summary>

**A. CIS Benchmarks for the relevant technology** · B. A CVE list · C. A SOC 2 report · D. An SBOM

**Correct: A.** CIS Benchmarks are prescriptive, consensus-based configuration standards that can be scanned against to produce a compliance score.
- **B wrong:** CVEs list vulnerabilities, not configuration standards (4.1).
- **C wrong:** An attestation report on controls, not a configuration baseline (4.2).
- **D wrong:** An SBOM inventories software components.
</details>

<details>
<summary><b>Q6.</b> An API authenticates every caller correctly, but changing the record ID in a request returns another customer's data. What is the flaw?</summary>

A. Missing TLS · **B. Broken object-level authorization — the API verifies who the caller is but not whether the requested record belongs to them** · C. No rate limiting · D. Weak encryption at rest

**Correct: B.** Authentication is not authorization; each object access must be checked against the caller's entitlement.
- **A/D wrong:** Neither would expose another user's record to an authenticated caller.
- **C wrong:** Rate limiting addresses volume, not entitlement.
</details>

<details>
<summary><b>Q7.</b> Which encryption state protects data if a storage device is physically stolen?</summary>

A. In transit · **B. At rest** · C. In use · D. In flight

**Correct: B.** Encryption at rest protects stored data, and must extend to **backups and snapshots**, which are commonly overlooked.
- **A/D wrong:** Those protect data moving across a network.
- **C wrong:** In-use encryption protects data in memory.
</details>

<details>
<summary><b>Q8.</b> Which hardening action addresses the single most commonly exploited weakness?</summary>

**A. Changing default credentials and removing default accounts** · B. Increasing log verbosity · C. Adding more RAM · D. Enabling compression

**Correct: A.** Unchanged default credentials are the classic entry point, particularly on appliances and IoT devices (1.11).
- **B wrong:** Helpful for detection but not prevention.
- **C/D wrong:** Neither is a security control.
</details>

<details>
<summary><b>Q9.</b> A login API endpoint is being brute-forced. Which control MOST directly addresses this?</summary>

A. Encryption at rest · **B. Rate limiting/throttling, with account lockout or progressive delay** · C. A larger instance type · D. Read-only root filesystem

**Correct: B.** Rate limiting is a security control against brute force, credential stuffing, scraping, and resource exhaustion.
- **A/D wrong:** Neither affects request volume.
- **C wrong:** Scaling up serves the attack faster.
</details>

<details>
<summary><b>Q10.</b> What is the difference between CIS Benchmarks and CIS Controls?</summary>

A. They are the same · **B. Benchmarks are prescriptive configuration standards for specific technologies; Controls are a prioritised set of organisational security actions** · C. Controls apply only to cloud · D. Benchmarks are legally binding

**Correct: B.** Both come from the Center for Internet Security but serve different purposes.
- **A/C wrong:** They are distinct, and both apply broadly.
- **D wrong:** Neither is law, though they are widely referenced by frameworks.
</details>

<details>
<summary><b>Q11.</b> Which practice ensures every new instance starts in a hardened state without configuration drift?</summary>

A. Hardening each server manually after deployment · **B. Baking the hardened baseline into a golden image and deploying from it** · C. Running a benchmark scan monthly · D. Documenting the hardening steps

**Correct: B.** Immutable deployment from a hardened image guarantees consistency and eliminates drift (2.4, 3.4).
- **A wrong:** Per-server hardening is precisely what causes drift.
- **C/D wrong:** Both are valuable but neither prevents divergence.
</details>

<details>
<summary><b>Q12.</b> Which container configuration is MOST secure?</summary>

A. Privileged, running as root, writable filesystem · B. Unprivileged, running as root, writable filesystem · **C. Unprivileged, running as a non-root user, read-only root filesystem, capabilities dropped** · D. Privileged, non-root user, read-only filesystem

**Correct: C.** Layered least privilege — no host capabilities, no root, nothing writable, minimum capabilities.
- **A/D wrong:** Privileged mode removes isolation regardless of other settings.
- **B wrong:** Root inside the container remains dangerous if the process escapes.
</details>

<details>
<summary><b>Q13.</b> Why should internal service-to-service traffic be encrypted?</summary>

A. It is required for performance · **B. Zero Trust removes implicit trust in the internal network — an attacker with a foothold can otherwise read internal traffic** · C. Only external traffic can be encrypted · D. It reduces bandwidth

**Correct: B.** Assuming the internal network is safe is exactly the assumption Zero Trust rejects; mTLS is the usual mechanism.
- **A/D wrong:** Encryption costs performance and bandwidth rather than saving them.
- **C wrong:** Internal traffic can and should be encrypted.
</details>

<details>
<summary><b>Q14.</b> Which storage risk profile is CORRECT?</summary>

A. File storage is internet-reachable by default; object storage is not · **B. Object storage is internet-reachable by design, so public exposure is the primary risk; file storage sits within the VPC, so over-broad share permissions are the primary risk** · C. Both have identical risk profiles · D. Neither can be exposed publicly

**Correct: B.** Object storage is an HTTP service; file storage is network-attached inside the private network.
- **A wrong:** Reversed.
- **C/D wrong:** The profiles differ and object storage certainly can be exposed.
</details>

<details>
<summary><b>Q15.</b> An application needs a database password at runtime. What is the MOST secure approach?</summary>

A. Hard-code it in the image · B. Store it in a `.env` file in the repository · **C. Retrieve it at runtime from a managed secret store, or eliminate it entirely using workload identity** · D. Pass it as a command-line argument

**Correct: C.** A secret store provides access control, rotation, and audit; workload identity removes the standing secret altogether (4.3).
- **A/B wrong:** Both persist the secret permanently in layers or history.
- **D wrong:** Command lines appear in process listings and logs.
</details>

<details>
<summary><b>Q16.</b> What does envelope encryption achieve?</summary>

A. It encrypts network traffic · **B. Data is encrypted with a data key, which is itself encrypted by a master key — so rotating the master key does not require re-encrypting all the data** · C. It removes the need for key management · D. It guarantees data residency

**Correct: B.** The indirection makes key rotation practical at scale.
- **A wrong:** That is TLS/IPsec.
- **C wrong:** It is a key-management technique, not a replacement.
- **D wrong:** Residency concerns physical location (4.2).
</details>

<details>
<summary><b>Q17.</b> A container mounts the container runtime socket so it can manage other containers. What is the risk?</summary>

**A. It effectively grants control of the host, since the container can start privileged containers and access the host filesystem** · B. It slows the container down · C. It prevents image scanning · D. It breaks networking

**Correct: A.** Runtime socket access is equivalent to host-level control and is a well-known escalation path.
- **B/C/D wrong:** None is the security consequence.
</details>

<details>
<summary><b>Q18.</b> Two IAM policies would both satisfy an application's functional requirement — one grants read access to a single bucket, the other grants full access to all storage. Which should be chosen?</summary>

**A. Read access to the single bucket — least privilege** · B. Full access, to avoid future permission issues · C. Either, they are equivalent · D. Full access with monitoring enabled

**Correct: A.** When two options both work, the **narrower** is correct — the most repeated principle in Domain 4.
- **B/D wrong:** Convenience is not a security justification; monitoring detects but does not prevent.
- **C wrong:** They differ enormously in blast radius.
</details>

<details>
<summary><b>Q19.</b> Which best describes hardening?</summary>

A. Encrypting all data · **B. Reducing the attack surface by removing unnecessary software, disabling unused services and ports, deleting default accounts, and applying a secure baseline** · C. Adding more firewalls · D. Increasing password length only

**Correct: B.** Hardening is systematic attack-surface reduction, usually measured against a benchmark.
- **A/C/D wrong:** Each is one control rather than the practice.
</details>

<details>
<summary><b>Q20.</b> Under Zero Trust, what happens when a service on the internal network calls another internal service?</summary>

A. The call is trusted because both are internal · **B. The call must still be authenticated, authorised, and encrypted — internal location grants no trust** · C. Only the first call of a session is verified · D. Internal calls bypass logging

**Correct: B.** Continuous verification of every request, regardless of origin, is the defining behaviour.
- **A/C wrong:** Both reintroduce implicit trust.
- **D wrong:** Zero Trust increases logging.
</details>

<details>
<summary><b>Q21.</b> Which combination secures an object storage bucket holding regulated data?</summary>

**A. Block public access, least-privilege policies, encryption at rest, enforce TLS, versioning, object lock, access logging, and a private endpoint** · B. Encryption at rest only · C. A strong bucket name · D. Public read with a complex object key

**Correct: A.** Layered controls — access, encryption, integrity, immutability, and audit.
- **B wrong:** Encryption alone does not prevent unauthorised access or deletion.
- **C/D wrong:** Obscurity is not a control; anyone with the URL can read a public object.
</details>

<details>
<summary><b>Q22.</b> Why should a container image specify a non-root user?</summary>

**A. If the process escapes the container, it would otherwise run as root on the host** · B. Root containers cannot be scanned · C. It reduces image size · D. It is required for networking

**Correct: A.** Running as non-root limits the impact of a container escape and is standard hardening.
- **B/C/D wrong:** None follows from the user setting.
</details>

<details>
<summary><b>Q23.</b> Which control provides central enforcement of authentication, authorisation, throttling, and logging for many APIs?</summary>

A. A NAT gateway · **B. An API gateway** · C. A bastion host · D. A load balancer alone

**Correct: B.** The API gateway is the single enforcement and observability point in front of backend services (1.3).
- **A wrong:** NAT provides outbound internet access.
- **C wrong:** A bastion secures administrative host access (4.3).
- **D wrong:** A plain load balancer distributes traffic without policy enforcement.
</details>

<details>
<summary><b>Q24.</b> Which statement about encryption is CORRECT?</summary>

A. Encryption at rest prevents accidental deletion · B. Encrypting data satisfies data-residency requirements · **C. Encryption protects confidentiality; deletion protection requires versioning, object lock, or backups** · D. Internal traffic does not require encryption

**Correct: C.** Encryption addresses confidentiality only — the other properties need different controls (1.4, 3.3, 4.2).
- **A wrong:** Encrypted data deletes just as easily.
- **B wrong:** Encrypted data still physically resides somewhere.
- **D wrong:** Zero Trust requires encrypting internal traffic.
</details>

<details>
<summary><b>Q25.</b> A scenario offers two configurations that both meet the functional requirement, one more permissive than the other. Which is correct on the exam?</summary>

**A. The more restrictive option** · B. The more permissive option, for flexibility · C. Whichever is cheaper · D. Whichever is faster to configure

**Correct: A.** Secure-by-default and least privilege mean the narrower option wins whenever it still satisfies the requirement.
- **B/C/D wrong:** None is a security justification, and each conflicts with the principle the objective is testing.
</details>

---

## 16. PBQ-style drills

### Drill A — Name the practice

| # | Requirement | Practice? |
|---|---|---|
| 1 | Verify every request regardless of network location | |
| 2 | Configure hosts against a recognised measurable standard | |
| 3 | Remove unused services, ports, and default accounts | |
| 4 | Protect data if a disk is stolen | |
| 5 | Protect data crossing the network between services | |
| 6 | Stop credentials appearing in the repository | |
| 7 | Prevent brute force against an endpoint | |
| 8 | Prevent a container compromise reaching the host | |

<details><summary>Answers</summary>

1 → **Zero Trust** · 2 → **CIS Benchmark** · 3 → **Hardening** · 4 → **Encryption at rest** · 5 → **Encryption in transit (mTLS)** · 6 → **Secrets management** · 7 → **Rate limiting** · 8 → **Unprivileged container, non-root, dropped capabilities**
</details>

### Drill B — Fix the insecure configuration

| # | Current state | Fix? |
|---|---|---|
| 1 | Container runs with `--privileged` | |
| 2 | Bucket set to public read for partner access | |
| 3 | API key hard-coded in the application image | |
| 4 | NFS share reachable from any subnet | |
| 5 | Internal microservice traffic sent in plaintext | |
| 6 | Container process runs as root with a writable filesystem | |
| 7 | Servers hardened individually after deployment | |

<details><summary>Answers</summary>

1 → **Run unprivileged**, drop capabilities, add compensating controls only if genuinely required
2 → **Block public access**; issue a **pre-signed URL** with short expiry
3 → **Secret store or workload identity** — and **rotate** the key, since it persists in image layers
4 → Restrict to **private subnets and specific security groups**; tighten share permissions; enable **encryption in transit**
5 → **mTLS** between services
6 → **Non-root user + read-only root filesystem**
7 → **Bake the baseline into a golden image** and deploy from it
</details>

### Drill C — Which encryption state?

| # | Scenario | State? |
|---|---|---|
| 1 | Database files on an encrypted volume | |
| 2 | HTTPS between browser and load balancer | |
| 3 | Backup snapshots in object storage | |
| 4 | mTLS between two microservices | |
| 5 | Data being processed inside an enclave | |

<details><summary>Answers</summary>

1 → **At rest** · 2 → **In transit** · 3 → **At rest** (commonly forgotten) · 4 → **In transit** · 5 → **In use**
</details>

### Drill D — Zero Trust or perimeter?

For each statement, say whether it reflects Zero Trust or the perimeter model.

| # | Statement |
|---|---|
| 1 | "Once on the VPN, users can reach any internal system" |
| 2 | "Every service call presents a token and is authorised" |
| 3 | "The firewall is our primary defence" |
| 4 | "Device health is checked at every access request" |
| 5 | "Internal traffic is unencrypted because it never leaves our network" |

<details><summary>Answers</summary>

1 → **Perimeter** (implicit trust after entry) · 2 → **Zero Trust** · 3 → **Perimeter** · 4 → **Zero Trust** · 5 → **Perimeter** — and the assumption Zero Trust explicitly rejects
</details>

---

## 17. 60-second recall sheet

```text
╔══════════════════════════════════════════════════════════════════════╗
║  4.4 — SECURITY BEST PRACTICES                                       ║
║  ★ RULE: the MORE RESTRICTIVE option that still meets the            ║
║    requirement is nearly always the right answer.                    ║
╠══════════════════════════════════════════════════════════════════════╣
║  ZERO TRUST  "NEVER TRUST, ALWAYS VERIFY" — no implicit trust from   ║
║   NETWORK LOCATION.  ① VERIFY EXPLICITLY ② LEAST PRIVILEGE           ║
║   ③ ASSUME BREACH · micro-segmentation · continuous re-verification  ║
║   · encrypt INTERNAL traffic too · vs PERIMETER (trusted inside →    ║
║   lateral movement unopposed).  Identity is the new perimeter.       ║
╠══════════════════════════════════════════════════════════════════════╣
║  BENCHMARK  CIS Benchmarks = prescriptive CONFIG baselines per       ║
║   technology (L1 essential / L2 defence-in-depth) · vendor guides    ║
║   ⚠ CIS BENCHMARKS (configure a technology) ≠ CIS CONTROLS           ║
║     (prioritised organisational actions)                             ║
║  HARDENING  remove software · disable services/ports · ★ CHANGE      ║
║   DEFAULT CREDENTIALS · least-privilege file perms · apply benchmark ║
║   → then SCAN · ★ BAKE INTO A GOLDEN IMAGE (no drift)               ║
║  PATCHING  see 3.4/4.1 · automate · rings · immutable replacement    ║
╠══════════════════════════════════════════════════════════════════════╣
║  ENCRYPTION  IN TRANSIT (TLS/mTLS/IPsec/SSH) · AT REST (AES-256,     ║
║   KMS — ⚠ INCLUDE BACKUPS AND SNAPSHOTS) · IN USE (enclaves)        ║
║   ENVELOPE ENCRYPTION: data key encrypted by master key → rotate     ║
║   the master without re-encrypting everything                        ║
║   ⚠ Encryption ≠ residency · ≠ deletion protection · LOSE THE KEY   ║
║     = LOSE THE DATA                                                  ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ SECRETS  NEVER in code · images · IaC state · .env in repo · logs ║
║   → git history and image LAYERS KEEP THEM FOREVER                   ║
║   ★ IF IT WAS EVER COMMITTED, IT IS COMPROMISED → ROTATE IT          ║
║   ✓ secret store · injected at runtime · rotation · dynamic/         ║
║     short-lived · best of all: WORKLOAD IDENTITY (no secret at all)  ║
╠══════════════════════════════════════════════════════════════════════╣
║  API SECURITY  authN + authZ per call · TLS · ★ SERVER-SIDE INPUT    ║
║   VALIDATION · ★ RATE LIMITING/THROTTLING · API GATEWAY · schema     ║
║   validation · minimal data exposure · versioning · logging · CORS   ║
║   ⚠ BROKEN OBJECT-LEVEL AUTHZ: authenticated, but changing an ID     ║
║     returns someone else's record                                    ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ CONTAINER  PRIVILEGED = nearly all HOST capabilities → isolation  ║
║    EFFECTIVELY REMOVED → container compromise = HOST compromise      ║
║    UNPRIVILEGED = the default and correct choice                     ║
║    ⚠ Equally dangerous: MOUNTING THE RUNTIME SOCKET, hostPath mounts ║
║    FILE PERMS: ★ NON-ROOT USER · READ-ONLY ROOT FS · drop            ║
║    capabilities · read-only volumes · minimal base image · scan ·    ║
║    NO secrets in images · resource limits                            ║
╠══════════════════════════════════════════════════════════════════════╣
║  STORAGE  OBJECT (internet-reachable BY DESIGN → risk = PUBLIC       ║
║    BUCKET): block public access · least-privilege policy, no         ║
║    wildcards · encrypt · enforce TLS · versioning · OBJECT LOCK ·    ║
║    ★ PRE-SIGNED URL with expiry instead of making it public ·        ║
║    access logging · private endpoint                                 ║
║   FILE (inside the VPC → risk = OVER-BROAD SHARE PERMISSIONS):       ║
║    share + file ACLs · private subnets/security groups · encrypt     ║
║    NFS/SMB in transit · directory-based identity · audit             ║
║  LEAST PRIVILEGE (4.3) — the most repeated correct answer            ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 18. Cross-references

| Related objective | Connection |
|---|---|
| **1.3 Cloud networking** | Micro-segmentation, security groups, WAF, private endpoints, API gateway |
| **1.4 Storage** | Object vs file storage; object lock/WORM; encryption at rest |
| **1.6 Containerization** | Container fundamentals; minimal base images; secrets never in layers |
| **1.7 Virtualization** | Golden images are where hardening is baked in |
| **2.4 Code to deploy** | Secrets must not be in code or IaC state; policy-as-code enforces baselines |
| **3.4 Resource life cycle** | Patching in depth; immutable replacement; EOS software is unpatchable |
| **4.1 Vulnerability management** | Scanning verifies hardening; misconfigurations are findings |
| **4.2 Compliance** | Benchmarks provide measurable evidence; encryption and key custody support sovereignty |
| **4.3 IAM** | Least privilege, MFA, and workload identity — the identity half of Zero Trust |
| **4.5 Security controls** | The specific technical controls (WAF, DLP, IDS/IPS, endpoint) that implement these practices |
| **4.6 Monitor suspicious activities** | Detecting failures of these practices — exposed ports, exploited software, cryptojacking |

> 🔑 **Carry this forward:** every practice here reduces to **start closed and open deliberately**. When a scenario offers a permissive and a restrictive option that both work, the restrictive one is the answer.

---

*Source of truth: CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0. CIS Benchmarks and the OWASP-style API weaknesses are industry references included as supporting context. Product names are illustrative; the exam is vendor-neutral.*
