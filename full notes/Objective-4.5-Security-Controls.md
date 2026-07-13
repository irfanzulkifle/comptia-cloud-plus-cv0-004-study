## SECTION 1 — Exam Objectives

- **Objective 4.5:** "Given a scenario, apply security controls in the cloud." — a hands-on, scenario-based objective.
- **How the exam tests it:** You get a situation (e.g. a flooded web server, a laptop with confidential files, an internet-exposed storage bucket) and must pick the right control to apply.
- **Six control categories covered:**
  - **Endpoint protection** — securing the devices/VMs/instances that run workloads.
  - **Data Loss Prevention (DLP)** — stopping sensitive data leaving the organisation.
  - **IPS / IDS** — spotting and blocking attackers.
  - **DDoS protection** — keeping services online under attack.
  - **IAM policies** — who can do what, and proving it.
  - **Firewall** — three flavours: Network ACL, WAF, and Network Security Group.
- **Key exam tip:** Learn *what each control does, where it sits (network vs host vs application), and what problem it solves*. Most questions are "which control best solves this scenario" — knowing the difference between, say, an IDS and a firewall earns the point.

---

## SECTION 2 — Exam Knowledge

### 2.1 Endpoint Protection
- **Definition:** Secures individual devices — VMs, cloud instances, containers, and user workstations — that run cloud workloads. Bundles anti-malware/EDR, host firewall, file-integrity monitoring, patch management, and disk/volume encryption.
- **Why it matters:** In the cloud the perimeter isn't a single wall; every VM is a potential entry point. Protecting each endpoint shrinks the attack surface and contains breaches before they spread laterally.
- **Analogy:** Locking every door and window of every house and adding an alarm in each — one compromised yard doesn't let a thief into the other homes.
- **Example:** A fleet of Linux web servers runs an agent that scans for malware, auto-applies patches, blocks all inbound ports except 443, and encrypts disk volumes; on a probe it isolates the host automatically.

### 2.2 Data Loss Prevention (DLP)
- **Definition:** Tools and policies that detect and block sensitive data from leaving the organisation's control. Inspects data at rest, in transit, and in use; classifies by patterns (credit cards, national IDs, medical records) and enforces rules like "block PII uploads."
- **Why it matters:** Cloud makes data easy to share and easy to leak; one misconfigured bucket can expose thousands of records and trigger fines (e.g. PDPA). DLP turns leaks into blocked, auditable events.
- **Analogy:** A customs officer who scans every bag for forbidden items before it leaves the country and also watches the warehouse where the bags are stored.
- **Example:** A DLP engine scans an object bucket, tags files with NRIC numbers confidential, and blocks downloads to unmanaged devices or public links, alerting the security team.

### 2.3 Intrusion Prevention / Detection System (IPS/IDS)
- **Definition:** An IDS monitors traffic for attacks and raises an alert; an IPS does the same but also blocks/drops malicious traffic in real time. Methods: signature-based and anomaly/behaviour-based; placement: network-based or host-based.
- **Why it matters:** Firewalls assume you know the bad guys; IDS/IPS catches what slips through. Exam rule: **IDS = detect and alert only; IPS = detect AND block.**
- **Analogy:** IDS is a camera that records the burglar and sounds an alarm; IPS is the camera plus an automatic door lock that slam-shuts on sight.
- **Example:** A sensor sees repeated SQL-injection attempts on a DB port; in IPS mode it drops the packets and notifies the SOC, whereas IDS mode would only log and alert.

### 2.4 Distributed Denial-of-Service (DDoS) Protection
- **Definition:** Defends services against floods from many sources at once. Three attack types: volumetric (bandwidth), protocol/network-layer (server state), and application-layer (HTTP floods). Defences: scrubbing, rate limiting, anycast, CDNs, AWS Shield.
- **Why it matters:** A successful DDoS takes your business offline. Because attacks are distributed, a single firewall/server can't absorb them — you need scale and edge mitigation.
- **Analogy:** Thousands calling one pizza shop at once so real customers never get through; protection is a smart switchboard that filters fake calls and keeps real lines open.
- **Example:** A 50 Gbps UDP flood hits a store; DDoS protection detects it, routes traffic through scrubbing centres, and keeps shoppers connected during the sale.

### 2.5 Identity and Access Management (IAM) Policies
- **Definition:** Rules defining which identities (users, groups, roles, services) can do which actions on which resources. Principles: least privilege, separation of duties. Mechanisms: RBAC, ABAC, MFA, SSO, federation. Policies are JSON: Effect, Action, Resource, Condition.
- **Why it matters:** Most breaches come from over-permissive access, not fancy hacking. IAM is the foundation — get it wrong and everything else is moot.
- **Analogy:** An office key-card system: the cleaner's card opens only the supply closet at night; the CEO's opens the boardroom. A policy = "this card, these doors, these hours."
- **Example:** A developer needs read but never delete on a bucket: policy Allows s3:GetObject and Denies s3:DeleteObject, requires MFA, and uses a rotating role instead of a shared key.

### 2.6 Firewall (NACL, WAF, Network Security Group)
- **Definition:** Filters traffic by rules. Three types: **Network ACL (NACL)** — stateless, subnet-level, numbered rules, return traffic not auto-allowed; **Web Application Firewall (WAF)** — a Layer 7 firewall that blocks SQLi/XSS/OWASP Top 10 at the application layer; **Network Security Group (NSG)** — stateful, instance-level, replies auto-allowed (AWS calls it a Security Group).
- **Why it matters:** Each firewall protects a different layer/scope: NACL = subnet boundary, NSG/Security Group = VM door, WAF = the app. A key discriminator: NACL is stateless, NSG is stateful.
- **Analogy:** NACL = neighbourhood gatehouse checking every car in and out; NSG = apartment door lock that remembers you left; WAF = club bouncer inspecting what each guest says/does, not just the list.
- **Example:** A three-tier app: NACL on the public subnet (80/443 in, ephemeral out), per-VM Security Group (443 from LB only), and a WAF blocking SQLi in front of the app — protecting network, host, and application layers.

---

## SECTION 3 — Real World Cloud Examples

- **Healthcare patient portal (DLP + IAM):** A clinic stores records in object storage; DLP scans uploads for MyKad/medical codes and blocks external shares, while IAM restricts read to the attending doctor's role with MFA on export — satisfying PDPA.
- **Online banking login (WAF + DDoS):** A bank puts its app behind a WAF filtering OWASP Top 10 (credential-stuffing, XSS) and enables DDoS protection so a botnet flood can't take the login page offline.
- **Multi-tenant SaaS on VMs (Endpoint + NSG):** Customer workloads run endpoint protection with EDR and disk encryption; NSGs restrict east-west traffic so a compromised VM can't reach another tenant's database.
- **Retail e-commerce (NACL + IPS/IDS):** A stateless NACL blocks all traffic except required web ports at the subnet edge, and an IPS/IDS sensor detects and blocks exploit attempts and port scans against order-processing servers.
- **Remote workforce (Endpoint + IAM SSO):** Cloud virtual desktops enforce patch compliance before login; IAM provides SSO with federation and conditional access (deny logins from untrusted countries) to cut account-takeover risk.

---

## SECTION 4 — COMPARISON TABLES

### Table 4.1 — Firewall Types: NACL vs WAF vs Network Security Group

| Feature | Network ACL (NACL) | Network Security Group (NSG / Sec Group) | Web Application Firewall (WAF) |
|---|---|---|---|
| OSI layer | Layer 3/4 (network) | Layer 3/4 (network) | Layer 7 (application / HTTP) |
| Stateful? | No — stateless | Yes — stateful | Yes — stateful |
| Scope | Subnet / network boundary | Individual VM / instance / NIC | Web app / API in front of app |
| Rule evaluation | Numbered, first match wins | All rules evaluated, explicit deny wins | Signature/rule-based inspection |
| Protects against | Unwanted IP/port traffic at subnet | Unauthorized host access | SQLi, XSS, OWASP Top 10, bots |
| Return traffic | Must be explicitly allowed | Auto-allowed for established flows | Auto-allowed for established flows |

### Table 4.2 — IDS vs IPS

| Feature | IDS (Detection) | IPS (Prevention) |
|---|---|---|
| Action on attack | Alert / log only | Alert AND block / drop |
| Placement | Out-of-band (mirror port) | In-line (traffic passes through) |
| Risk of blocking good traffic | None | Possible (must tune) |
| Use case | Compliance monitoring, visibility | Active defence, auto-mitigation |
| Performance impact | Low | Higher (in-line inspection) |

### Table 4.3 — DDoS Attack Types

| Attack type | What it targets | Example | Typical defence |
|---|---|---|---|
| Volumetric | Bandwidth / saturation | UDP / ICMP flood, amplification | Scrubbing, anycast, CDN, Shield |
| Protocol / Network | Server state / resources | SYN flood, fragmented packets | Rate limiting, stateful inspection |
| Application-layer | The app itself | HTTP GET flood, API abuse | WAF, rate limiting, bot management |

### Table 4.4 — Endpoint Protection Components

| Component | Purpose | Example control |
|---|---|---|
| Anti-malware / EDR | Detect & respond to threats on host | Agent scans, behavioural EDR |
| Host firewall | Filter traffic on the device | Block all ports except needed |
| Patch management | Close known vulnerabilities | Automated OS updates |
| Disk encryption | Protect data at rest if stolen | BitLocker / LUKS / volume encrypt |

### Table 4.5 — IAM Concepts

| Concept | Meaning | Exam relevance |
|---|---|---|
| Least privilege | Grant only required permissions | Core principle, very common |
| RBAC | Access by role/group membership | Standard model |
| ABAC | Access by attributes/tags/conditions | Fine-grained control |
| MFA | Second factor required | Strongly recommended |
| SSO / Federation | One login across systems | Reduces credential sprawl |
| Deny precedence | Explicit Deny beats Allow | Policy evaluation rule |

---

## SECTION 5 — AWS PROVIDER MAPPING

This section maps each Objective 4.5 control category to the AWS service(s) that implement it. Use this only when the question is explicitly AWS-flavoured; the exam is vendor-neutral overall, but knowing the AWS names helps when a scenario mentions them.

| Objective 4.5 control | AWS service(s) | Notes |
|---|---|---|
| Endpoint protection | **Amazon GuardDuty** (threat detection on hosts/workloads) and **EBS malware scan** (scan EBS volumes / snapshots for malware) | GuardDuty also covers runtime and finding malware; EBS malware scanning inspects attached/attached snapshot volumes for trojans and viruses. |
| DLP (Data Loss Prevention) | **Amazon Macie** | Macie uses machine learning and pattern matching to discover, classify, and protect sensitive data (PII, credentials) in S3. |
| IPS / IDS | **Amazon GuardDuty** (IDS-style threat detection) and **AWS Network Firewall** (inline IPS/IDS at the VPC edge) | GuardDuty detects and alerts; Network Firewall can block with IDS/IPS rule groups in-line. |
| DDoS protection | **AWS Shield** (Standard included, Advanced for L3/L4/L7 mitigation) plus **Amazon CloudFront** / **Route 53** for edge absorption | Shield Standard auto-protects against common L3/L4 attacks at no cost. |
| IAM policies | **AWS IAM** (users, groups, roles, policies, permission boundaries, ABAC) | JSON policies define Effect/Action/Resource/Condition; MFA, SSO via IAM Identity Center. |
| Firewall — Network ACL | **VPC Network ACL (NACL)** | Stateless, subnet-level, numbered rules, separate inbound/outbound. |
| Firewall — WAF | **AWS WAF** | Layer 7, deployed on CloudFront / ALB / API Gateway / AppSync; managed rules for OWASP Top 10. |
| Firewall — Network Security Group | **AWS Security Group** (the AWS equivalent of a Network Security Group) | Stateful, instance/ENI-level, allow rules only (implicit deny). |

Quick memory aid: **M**acie = **D**ata, **G**uardDuty = **E**ndpoint + **I**DS, **S**hield = **D**DoS, **IAM** = policies, **NACL / Security Group / WAF** = the three firewalls, **Network Firewall** = inline IPS/IDS.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A cloud VM shows signs of a rootkit and unusual outbound connections. Which control is primarily responsible for detecting and responding on that host?
   A. Network ACL
   B. Endpoint protection
   C. WAF
   D. DLP
   **Answer: B — Endpoint protection (EDR/anti-malware) runs on the host.**

2. Your company wants to block customer NRIC numbers from being uploaded to a public link. Which control should you deploy?
   A. IPS
   B. DLP
   C. Network Security Group
   D. WAF
   **Answer: B — DLP classifies and blocks sensitive data egress.**

3. A sensor only logs and alerts when it sees a port scan but does not block it. What is this?
   A. IPS
   B. Firewall
   C. IDS
   D. WAF
   **Answer: C — IDS detects and alerts only; IPS would block.**

4. An attacker floods your web app with 60 Gbps of UDP traffic from many sources. Which control mitigates this?
   A. DLP
   B. DDoS protection
   C. Endpoint protection
   D. IAM policy
   **Answer: B — DDoS protection scrubs volumetric floods.**

5. Which IAM principle means giving a user only the permissions they need?
   A. Separation of duties
   B. Least privilege
   C. Federation
   D. ABAC
   **Answer: B — Least privilege minimizes access scope.**

6. A subnet-level filter that is stateless and uses numbered rules is a:
   A. Security Group
   B. WAF
   C. Network ACL
   D. NSG
   **Answer: C — NACL is stateless and subnet-scoped with numbered rules.**

7. Which firewall inspects HTTP requests to block SQL injection?
   A. Network ACL
   B. WAF
   C. Security Group
   D. NACL
   **Answer: B — WAF operates at Layer 7 and blocks SQLi/XSS.**

8. An AWS Security Group is best described as:
   A. Stateless subnet filter
   B. Stateful instance-level filter
   C. Layer 7 app filter
   D. Data classifier
   **Answer: B — Security Groups are stateful and attach to instances/NICs.**

9. You must prove a developer can read but never delete a bucket. What do you use?
   A. DLP policy
   B. IAM policy with Allow GetObject and Deny DeleteObject
   C. NACL
   D. WAF rule
   **Answer: B — IAM policy defines precise allow/deny actions.**

10. In AWS, which service discovers and classifies sensitive data in S3?
    A. GuardDuty
    B. Macie
    C. Shield
    D. Inspector
    **Answer: B — Macie is the AWS DLP/classification service.**

11. Which service provides in-line IPS/IDS filtering at the VPC edge in AWS?
    A. Security Group
    B. AWS Network Firewall
    C. Macie
    D. CloudFront
    **Answer: B — Network Firewall runs IDS/IPS rule groups in-line.**

12. GuardDuty in AWS is primarily used for:
    A. Data classification (DLP)
    B. Threat detection (endpoint/IDS-style)
    C. DDoS mitigation
    D. IAM policy enforcement
    **Answer: B — GuardDuty is threat detection; Macie is DLP, Shield is DDoS.**

13. The OWASP Top 10 protections are most associated with which control?
    A. NACL
    B. NSG
    C. WAF
    D. Endpoint protection
    **Answer: C — WAF defends against OWASP Top 10 web attacks.**

14. An application-layer DDoS (HTTP flood) is best mitigated by:
    A. NACL rate limiting alone
    B. WAF / bot management + DDoS protection
    C. Disk encryption
    D. IAM federation
    **Answer: B — App-layer floods need WAF/bot controls plus DDoS protection.**

15. Explicit Deny in an IAM policy:
    A. Is overridden by Allow
    B. Always wins over Allow
    C. Only applies to groups
    D. Requires MFA
    **Answer: B — Explicit Deny takes precedence over any Allow.**

16. Full-disk encryption on a cloud instance protects data:
    A. In transit
    B. At rest
    C. In use by an app
    D. Shared externally
    **Answer: B — Disk encryption protects data at rest if the volume is stolen.**

17. A host-based firewall is a component of:
    A. DLP
    B. Endpoint protection
    C. WAF
    D. NACL
    **Answer: B — Host firewall is part of endpoint protection.**

18. Which is a difference between NACL and Security Group?
    A. Both are stateful
    B. NACL is stateless; Security Group is stateful
    C. Both are Layer 7
    D. NACL attaches to instances
    **Answer: B — NACL stateless, Security Group stateful.**

19. Multi-factor authentication strengthens which objective area?
    A. DLP
    B. IAM policies
    C. WAF
    D. DDoS
    **Answer: B — MFA is an IAM authentication control.**

20. EBS malware scanning in AWS supports which 4.5 control?
    A. DLP
    B. Endpoint protection
    C. DDoS
    D. WAF
    **Answer: B — Scanning EBS volumes for malware is endpoint protection.**
