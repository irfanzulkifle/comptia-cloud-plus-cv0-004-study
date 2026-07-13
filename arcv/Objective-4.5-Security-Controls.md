---
title: CompTIA Cloud+ CV0-004 — Objective 4.5
objective: "4.5 Given a scenario, apply security controls in the cloud."
domain: "4.0 Security"
weight: "20% of exam"
tags: [Cloud+, CV0-004, Security, Controls, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 4.5
## Apply Security Controls in the Cloud

> **Objective statement:** *"Given a scenario, apply security controls in the cloud."*
> **Domain:** 4.0 Security (20% of the exam)
> **What it tests:** Whether you can pick and place the right *defensive control* for a given threat. 4.4 was "harden the thing" — 4.5 is "deploy the control that stops the attack" (endpoint, DLP, IDS/IPS, DDoS, IAM policy, firewall/ACL/WAF/NSG). 4.6 then asks you to *watch* for what gets through.

---

## SECTION 1 — OBJECTIVE OVERVIEW

**In one breath:** Security controls = the tools you layer in to **detect, block, and contain** attacks across endpoints, data, network, and identity.

**The 4.5 sub-objectives (verbatim from the official exam objectives):**

| # | Sub-objective | What it means |
|---|---------------|---------------|
| 4.5.1 | **Endpoint protection** | Antivirus/EDR on the workloads (VMs/containers) |
| 4.5.2 | **Data loss prevention (DLP)** | Stop sensitive data leaving the org |
| 4.5.3 | **Intrusion prevention/detection (IPS/IDS)** | Watch network/host for attack signatures |
| 4.5.4 | **Distributed denial-of-service (DDoS) protection** | Absorb/scrub volumetric attacks |
| 4.5.5 | **Identity and access management (IAM) policies** | Enforce who-can-do-what at the policy layer (ties to 4.3) |
| 4.5.6 | **Firewall** | Traffic filtering |
| 4.5.6a | ├ Network access control list (ACL) | Stateless packet filter on subnets |
| 4.5.6b | ├ Web application firewall (WAF) | Layer-7 filter for HTTP/HTTPS apps |
| 4.5.6c | └ Network security group (NSG) | Stateful per-interface/per-subnet rules |

**Mental model — the defense-in-depth stack:**
`Edge (DDoS, WAF) → Network (firewall/NSG/ACL) → Host (endpoint protection, IDS) → Data (DLP) → Identity (IAM policy)`. Each layer catches what the one before missed.

---

## SECTION 2 — EXAM KNOWLEDGE

### 4.5.1 — ENDPOINT PROTECTION

**Definition:** Security software on the workload itself — VMs, containers, servers.

- **Antivirus (AV)** — signature-based malware detection.
- **EDR / XDR** (Endpoint/Extended Detection & Response) — behavioral detection, isolation, automated response.
- Cloud-native: **AWS GuardDuty (host/instance)**, **Azure Defender for Servers / Microsoft Defender**, **GCP Security Agent / Chronicle**.
- **Exam angle:** "A compromised instance is spreading" → endpoint protection / EDR to detect + isolate.

### 4.5.2 — DATA LOSS PREVENTION (DLP)

**Definition:** Policies + tooling that **prevent sensitive data from leaving** the organization (exfiltration) or being mis-shared.

- Inspects content in **email, endpoints, cloud storage, SaaS**.
- Actions: block, quarantine, encrypt, alert, redact.
- Cloud-native: **AWS Macie** (S3 PII discovery + DLP), **Azure Information Protection / Purview DLP**, **Google DLP API**.
- **Exam angle:** "Customer PII being emailed out / uploaded to personal drive" → **DLP** rule to block + alert.

### 4.5.3 — INTRUSION PREVENTION / DETECTION SYSTEM (IPS/IDS)

**Definition:** Network- or host-based inspection for attack patterns.

- **IDS** (detection) — *alerts* on suspicious traffic, does not block.
- **IPS** (prevention) — *actively blocks/drops* malicious traffic inline.
- Detection methods: **signature-based** (known attacks), **anomaly/behavior-based** (deviation from baseline).
- Cloud-native: **AWS GuardDuty** (IDS-style, VPC/account), **Azure Network Watcher / NSG flow logs + IPS**, **GCP IDS**.
- **Exam angle:** "We need to *see* attacks but not break prod" → IDS; "We need to *stop* them automatically" → IPS.

### 4.5.4 — DISTRIBUTED DENIAL-OF-SERVICE (DDoS) PROTECTION

**Definition:** Absorb and scrub **volumetric / amplified** traffic floods so the service stays up.

- **Network-layer (L3/L4):** AWS **Shield** (Standard free / Advanced paid), Azure **DDoS Protection**, GCP **Cloud Armor** (L3/L4 + L7).
- **Application-layer (L7):** **WAF + rate limiting + CDN/edge** (CloudFront, Azure Front Door, Cloud CDN).
- Strategy: **scale out + scrub at edge + ANYCAST**.
- **Exam angle:** "Site is flooded with traffic, going down" → enable DDoS protection (Shield/Advanced) + WAF + CDN.

### 4.5.5 — IDENTITY AND ACCESS MANAGEMENT (IAM) POLICIES

**Definition:** Machine-enforced **who-can-do-what** rules at the identity layer (the policy side of 4.3).

- **Policy types:** identity-based (attached to user/role/group) vs resource-based (attached to the resource, e.g., bucket policy).
- **Least privilege** (4.4.8) enforced *here* — explicit Allow, default Deny.
- Conditions: source IP, MFA-present, time-of-day.
- **Exam angle:** "Restrict a role to only read one bucket from the office IP" → IAM policy with scoped resource + condition (ties 4.3 + 4.4).

### 4.5.6 — FIREWALL

Traffic filtering at different layers:

#### Network access control list (ACL)
- **Stateless** packet filter at the **subnet** level (e.g., AWS NACL).
- Rules evaluated in order; each packet judged on its own (no "remembering" prior packets).
- Good for broad subnet boundaries / quick blocklists.
- **Exam angle:** "Block all inbound from a bad subnet at the network edge" → NACL rule.

#### Web application firewall (WAF)
- **Layer-7** filter for **HTTP/HTTPS** — stops SQLi, XSS, path traversal, bad bots.
- Runs at the edge/CDN (AWS WAF, Azure Front Door WAF, GCP Cloud Armor).
- Rule sets / managed rules (OWASP Top 10).
- **Exam angle:** "Someone is exploiting a web app vulnerability (SQL injection)" → **WAF** with the SQLi rule.

#### Network security group (NSG)
- **Stateful** per-interface / per-subnet rules (e.g., Azure NSG, AWS Security Group).
- "Stateful" = return traffic for an allowed flow is automatically permitted.
- Default-deny, explicit allows; micro-segmentation by attaching to NICs/subnets.
- **Exam angle:** "Only allow 443 from the load balancer to the web tier" → NSG / security group rule (stateful).

> **ACL vs NSG quick diff:** NACL = **stateless**, subnet-level, order-matters. NSG/Security Group = **stateful**, interface/subnet-level, implicit return allowed. WAF = **L7**, app-specific.



## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — DLP blocking PII exfil via email
A Malaysian fintech runs a customer-support app on **AWS**. Agents handle NRIC numbers, bank account details, and credit-card PANs. The company enables **Amazon Macie** to discover PII in S3 and **Microsoft Purview DLP** (or AWS WorkMail / Exchange Online DLP) on outbound email. When an agent tries to mail a spreadsheet of customer NRICs to a personal Gmail account, the **DLP policy** inspects the message body and attachment, matches the **PII content fingerprint** (NRIC regex + PAN Luhn check), and **quarantines** the mail before it leaves the tenant. The security team gets an alert and the incident is logged. Exam angle: DLP is a **content-aware, data-protection** control — it reads what data is leaving, not whether a packet is malicious. It does not stop malware; that is endpoint/AV territory. In **Azure**, the same pattern uses **Microsoft 365 DLP** + **Defender for Cloud Apps**; in **GCP**, **Cloud DLP** scans and **re-identifies/redacts** before storage or egress.

### 3.2 — WAF stopping SQL injection on a web app
A travel-booking web app is hosted on **Azure App Service** behind **Azure Front Door + WAF** (or **AWS WAF** on an ALB/CloudFront). An attacker submits `email=' OR '1'='1' -- ` in the login form. The **WAF** (managed rule group OWASP CRS) inspects the **HTTP request at Layer 7**, flags the classic **SQL injection** signature, and returns **403 blocked** before the request ever reaches the app. Because the WAF operates on **application-layer traffic**, it understands parameters, headers, and cookies. Exam angle: a WAF protects a specific app/API from **Layer 7 attacks** (SQLi, XSS, path traversal). It is NOT the tool for a volumetric **network flood** — that needs DDoS protection. Managed rulesets save you from writing signatures by hand; custom rules handle business logic (block a country, rate-limit a path).

### 3.3 — IDS vs IPS choice during a breach
A SaaS on **GCP** suspects an attacker probing an exposed VM. They deploy **Google Cloud IDS** (a **passive** intrusion detection system) first: it **mirrors** traffic, analyses it, and **alerts** the SOC but does **not** drop anything. This lets analysts confirm the attack is real and tune signatures without risking false positives that **break production**. Once the signature is trusted, they add an **IPS** (inline, often a **Cloud NGFW / third-party inline appliance**) that **actively blocks** the malicious sessions. Exam angle: **IDS = detect + alert only; IPS = detect + block inline.** If the question says "we want to stop the attack in real time," choose **IPS**. If it says "observe without risk," choose **IDS**. IPS inline carries a **latency + false-positive** risk that IDS does not.

### 3.4 — DDoS protection keeping a site up during a flood
During a flash sale, an e-commerce site on **AWS** is hit with a **volumetric UDP/ICMP flood** plus a **Layer 7 HTTP GET flood**. **AWS Shield Standard** (automatic, free) absorbs the basic network-layer volume, while **AWS Shield Advanced + WAF rate-based rules** handle the app-layer flood by throttling abusive IPs. Traffic is fronted by **CloudFront**, which spreads load across edge PoPs so the origin stays reachable. Exam angle: **Shield Standard is always-on and free**; **Shield Advanced is paid**, adds 24/7 DRT support, cost-protection, and L7 mitigation. A **WAF alone cannot stop a multi-Gbps volumetric flood** — you need the DDoS/scrubbing service. DDoS protection covers **both L3/L4 (network) and L7 (application)** floods, not network only.

### 3.5 — NSG/ACL/endpoint layered defense on a 3-tier app
A 3-tier app (web → app → DB) runs on **Azure**. Each tier sits in its own **subnet** guarded by a **Network Security Group (NSG)** — a **stateful** filter, so a web→app request automatically allows the **return** packet without an explicit rule. At the **subnet boundary**, a **Network ACL (NACL)** on AWS (or Azure's subnet NSG) adds a **stateless** allow/deny list evaluated in **numbered order**, blocking broad ranges. On each VM, **endpoint/antivirus protection (Defender for Endpoint / EDR)** catches malware that slipped past the network layers. Exam angle: defense is **layered** — perimeter (firewall/WAF), network (NSG/NACL), host (endpoint). **NSG is stateful** (no return rule needed); **NACL is stateless** (you must allow both directions) and **rule order matters**. Opening `0.0.0.0/0` on a management port is a classic misconfiguration.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — IDS vs IPS
| Aspect | **IDS** (Intrusion Detection) | **IPS** (Intrusion Prevention) |
|---|---|---|
| Placement | **Passive / out-of-band** (mirror port) | **Inline** (traffic passes through it) |
| Action | **Alerts only** — does NOT block | **Blocks/drops** malicious traffic |
| Risk | No false-positive outage risk | Latency + risk of blocking legit traffic |
| Use when | Observe, forensics, tune first | You must stop the attack in real time |
| Cloud example | GCP Cloud IDS, AWS GuardDuty (alerting) | Cloud NGFW inline, Azure Firewall IDPS |

### 4.2 — ACL (stateless) vs NSG (stateful)
| Aspect | **ACL / NACL** (stateless) | **NSG** (stateful) |
|---|---|---|
| State tracking | **No** — evaluates every packet both ways | **Yes** — return traffic auto-allowed |
| Return rule | **Must** explicitly allow both directions | **Not needed** for replies |
| Rule order | **Numbered, processed top-down, first match wins** | Allow/deny evaluated as a set |
| Scope | Subnet / VPC boundary | Subnet or NIC (Azure) |
| Cloud example | AWS NACL | Azure NSG, AWS Security Group |

### 4.3 — WAF vs Network Firewall
| Aspect | **WAF** | **Network Firewall / NGFW** |
|---|---|---|
| Layer | **Layer 7** (HTTP/S, app layer) | **Layer 3/4** (IP, port, protocol) + some L7 |
| Protects | A specific **app/API** | The **network perimeter** broadly |
| Stops | SQLi, XSS, L7 floods, bad bots | Port scans, illegal protocols, IP/geo blocks |
| Volumetric DDoS | **No** (not enough) | Partly, with DDoS service |
| Cloud example | AWS WAF, Azure Front Door WAF | AWS Network Firewall, Azure Firewall |

### 4.4 — Network-layer vs Application-layer DDoS
| Aspect | **Network-layer (L3/L4)** DDoS | **Application-layer (L7)** DDoS |
|---|---|---|
| Target | Bandwidth / connections (UDP, SYN, ICMP) | App resources (HTTP GET floods, API abuse) |
| Volume | Often **very high (Gbps/Tbps)** | Lower volume, harder to detect |
| Mitigation | **Shield/scrubbing**, CDN, anycast | **WAF rate-limiting**, bot management |
| Cost impact | Saturates pipe | Exhausts app/DB compute |
| Cloud example | AWS Shield Standard/Advanced | WAF rate-based rules + Shield Advanced |

### 4.5 — Endpoint protection vs DLP
| Aspect | **Endpoint protection** (EDR/AV) | **DLP** (Data Loss Prevention) |
|---|---|---|
| Goal | Stop **malware / exploits** on the host | Stop **sensitive data leaving** the org |
| Inspects | Files, processes, behavior | **Content** (PII, keywords, fingerprints) |
| Action | Quarantine, kill process, isolate host | Block/encrypt/quarantine data egress |
| Stops malware? | **Yes** | **No** |
| Cloud example | Defender for Endpoint, CrowdStrike | Microsoft 365 DLP, AWS Macie, GCP Cloud DLP |

### 4.6 — Identity-based vs Resource-based IAM policy
| Aspect | **Identity-based policy** | **Resource-based policy** |
|---|---|---|
| Attached to | **User / group / role** (principal) | The **resource** (bucket, KMS key, queue) |
| Answers | "What can **this identity** do?" | "Who can access **this resource**?" |
| Cross-account | Needs role assumption | Can grant **external account** directly |
| Example | IAM user policy, Azure RBAC role | S3 bucket policy, Lambda resource policy |
| Default | **Implicit deny** (nothing allowed unless granted) | **Implicit deny** unless explicitly allowed |

---

## SECTION 5 — EXAM TRAPS

**Trap 1 — "IDS blocks attacks"**
- **Scenario:** The SOC wants to stop an active intrusion automatically.
- **Correct:** IDS only **detects and alerts**; you need an **IPS** to block.
- **Why wrong:** IDS is **passive** — it never drops traffic. Choosing IDS when the question says "block/stop/prevent" is the distractor.

**Trap 2 — "ACL is stateful"**
- **Scenario:** A subnet ACL allows inbound web traffic; the architect assumes replies flow back automatically.
- **Correct:** ACL/NACL is **stateless** — you must allow **both** directions explicitly.
- **Why wrong:** Statefulness belongs to **NSG / Security Groups**, not ACLs.

**Trap 3 — "Use a WAF for network floods"**
- **Scenario:** A 10 Gbps UDP flood hits the perimeter; the engineer points a WAF at it.
- **Correct:** Use **DDoS protection (Shield / scrubbing / CDN)**; a WAF handles **L7 app attacks**, not volumetric floods.
- **Why wrong:** WAF is Layer 7 and cannot absorb multi-Gbps L3/L4 volume.

**Trap 4 — "DLP stops malware"**
- **Scenario:** The question asks which control prevents a virus from executing on a laptop.
- **Correct:** **Endpoint protection / antivirus / EDR** stops malware.
- **Why wrong:** DLP is about **data egress/content** (PII), not executable threats.

**Trap 5 — "NSG needs a return rule"**
- **Scenario:** Web tier sends to app tier; engineer adds an outbound rule for the reply.
- **Correct:** **NSG is stateful** — the return packet is allowed automatically.
- **Why wrong:** Only **stateless** ACLs require explicit return rules.

**Trap 6 — "Opening 0.0.0.0/0 on a security group is fine"**
- **Scenario:** SSH SG rule allows `0.0.0.0/0` "just for convenience."
- **Correct:** This is a **critical misconfiguration** — anyone on the internet can attempt access.
- **Why wrong:** Restrict to known CIDRs / bastion / VPN; open-all is a top exam failure.

**Trap 7 — "IPS breaks prod, so use IDS only when you want to block"**
- **Scenario:** "We only want to block, so we deploy IDS."
- **Correct:** To **block**, deploy **IPS**; IDS never blocks regardless of production fears.
- **Why wrong:** The trap swaps the two. IDS = alert; IPS = block. Worry about IPS false-positives, but it is still the blocking tool.

**Trap 8 — "GuardDuty is a WAF"**
- **Scenario:** The question asks which AWS service filters malicious HTTP requests at the app layer.
- **Correct:** **AWS WAF** filters L7; **GuardDuty** is a **threat-detection / alerting** service (IDS-like).
- **Why wrong:** GuardDuty analyses logs and **alerts** — it does not sit inline or block traffic.

**Trap 9 — "Macie is just a WAF/DLP blocker"**
- **Scenario:** "Use Macie to block PII from leaving."
- **Correct:** **Macie discovers and classifies PII** (DLP discovery); blocking egress needs **DLP policies** (WorkMail/Exchange/M365 DLP).
- **Why wrong:** Macie is **detection/classification**, not an inline block control.

**Trap 10 — "Shield Advanced is free / Standard is paid"**
- **Scenario:** The exam asks which DDoS service is always-on and free.
- **Correct:** **Shield Standard is free & automatic**; **Shield Advanced is paid** (adds DRT, L7, cost protection).
- **Why wrong:** The trap flips the pricing — don't assume "Advanced" is the default free tier.

**Trap 11 — "IAM policy default-allow"**
- **Scenario:** "If no IAM policy explicitly allows an action, AWS allows it by default."
- **Correct:** IAM is **default-deny** — nothing is permitted unless explicitly granted.
- **Why wrong:** Cloud IAM uses **implicit deny**; an explicit Deny always wins over Allow.

**Trap 12 — "NACL rule order doesn't matter"**
- **Scenario:** The engineer adds a broad Allow after a specific Deny, expecting the Allow to apply.
- **Correct:** NACL rules are **evaluated in numeric order; first match wins.**
- **Why wrong:** Order is critical — a lower-numbered rule can override intent.

**Trap 13 — "Endpoint protection is a firewall"**
- **Scenario:** "Deploy endpoint protection on the host to filter all inbound network traffic."
- **Correct:** Endpoint protection targets **malware/process behavior**; a **firewall** filters network flows.
- **Why wrong:** They are different layers — host security vs network traffic control.

**Trap 14 — "DDoS only happens at Layer 3"**
- **Scenario:** "DDoS protection is only needed for network/volumetric attacks."
- **Correct:** DDoS includes **L7 (application-layer)** floods too — mitigate with **WAF + Shield Advanced**.
- **Why wrong:** App-layer floods exhaust compute without saturating the pipe; they are still DDoS.

**Trap 15 — "Resource policy vs identity policy confusion"**
- **Scenario:** A bucket policy grants an external account access; the exam asks what type it is.
- **Correct:** That is a **resource-based policy** (attached to the resource); identity policies attach to the **principal**.
- **Why wrong:** Resource policies can grant **cross-account** access directly; identity policies describe what a user/role can do. Mixing them up fails the question.


## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

Performance-Based Questions on Cloud+ test whether you can actually *apply* a security control, not just recognise its name. Each scenario below gives you a **Scenario**, a step-by-step **Walkthrough** (what the grader wants to see), and a **Bonus question** to stretch your understanding.

### 6.1 — Stop SQL Injection on a Public Web App (WAF + Rule)

**Scenario**
Your company hosts a public e-commerce site on a cloud load balancer. A penetration test flags that the search box is injectable — an attacker can run `' OR '1'='1` and dump the orders table. You must block SQLi at the edge without touching app code before the next release.

**Walkthrough**
1. Deploy a **Web Application Firewall (WAF)** in front of the load balancer (e.g. AWS WAF on the ALB, Azure WAF on Front Door, GCP Cloud Armor).
2. Attach a managed rule group that covers **SQLi** (AWS `AWSManagedRulesSQLiRuleSet`, Azure `SQLI` rule, Cloud Armor `sqli` preset).
3. Set the **action** to *Block* (not just Count/Alert) for SQLi matches.
4. Confirm inspection happens at **Layer 7** on the HTTP request body and query string, not just the URI.
5. Test with a benign payload first, then a known SQLi string, and verify the request is **403-blocked** while legit traffic passes.
6. Enable **logging** (WAF logs / CloudWatch) so you can prove the rule fired.

**Bonus question**
Why is a WAF *not* a substitute for parameterised queries in the application code? *(Answer: WAF is a compensating control at the edge — it can be bypassed with encoding tricks; the real fix is prepared statements in the app. WAF reduces noise and buys time, it doesn't remove the vulnerability.)*

---

### 6.2 — Block PII Exfiltration from an S3 Bucket (Macie/DLP + Bucket Policy)

**Scenario**
An HR bucket holds CSV files with NRIC numbers and salaries. A junior admin accidentally made it public. You must detect sensitive data, stop public access, and prevent future exfil without deleting the bucket.

**Walkthrough**
1. Run **Amazon Macie** (or your provider's DLP scan) over the bucket to classify PII and produce a findings report.
2. Enable **Block Public Access** at the account *and* bucket level — tick all four settings.
3. Write a **bucket policy** with an explicit `Deny` on `s3:GetObject` for `Principal: "*"` unless the request comes from the approved VPC endpoint or IAM role.
4. Add **S3 access logging** + **CloudTrail data events** so any future `GetObject` is auditable.
5. Configure **DLP / Macie alerting** to notify SecOps if any new object scores high on PII.
6. Remove the rogue IAM policy that granted the junior admin `s3:*` and apply **least-privilege** instead.

**Bonus question**
A bucket policy `Allow` and a bucket policy `Deny` both exist for the same action — which wins, and why? *(Answer: explicit `Deny` always wins over `Allow` in AWS IAM/S3 evaluation — that's why the Deny is your safety net against accidental Allows.)*

---

### 6.3 — Choose IDS vs IPS for a Sensitive Production Environment (IDS alert + IPS inline)

**Scenario**
A finance app handles card data. Management asks: "Should we put an **Intrusion Prevention System** inline to block attacks, or an **Intrusion Detection System** that only alerts?" A false block during peak trading could cost millions.

**Walkthrough**
1. Explain the core difference: **IDS = detect & alert** (passive, out-of-band); **IPS = detect & block** (inline, active).
2. For a *sensitive prod* environment with zero tolerance for false positives, start with **IDS** in monitoring mode to baseline traffic and tune signatures — no risk of dropping legit trades.
3. Once signatures are tuned (low false-positive rate), promote to **IPS inline** on the segment handling external traffic, keeping it in **alert-only** on the critical transaction path.
4. Use **IPS** at the network edge / DMZ and **IDS** deep inside the trust zone where blocking is risky.
5. Document the **fail-open vs fail-closed** behaviour: an IPS that fails *closed* during an outage stops all traffic — often unacceptable for finance, so confirm fail-open is configured.

**Bonus question**
Why might you run IDS *and* IPS together rather than picking one? *(Answer: defence in depth — IPS inline blocks known attacks at the perimeter, IDS out-of-band gives full visibility and catches things the IPS let through or mis-classified, without risking an inline block.)*

---

### 6.4 — Mitigate a Combined Layer 3 + Layer 7 DDoS (Shield Advanced + WAF + CDN)

**Scenario**
During a sale, your site is hit by a **volumetric L3/L4 flood** (saturation) *and* a **L7 HTTP-flood / slowloris** attack against the login page. The site is unreachable.

**Walkthrough**
1. Engage the provider's **DDoS protection at the edge**: AWS **Shield Advanced**, Azure **DDoS Network Protection**, GCP **Cloud Armor** with DDoS defence — this scrubs the **L3/L4 volumetric** traffic before it reaches you.
2. Put a **CDN** (CloudFront / Azure Front Door / Cloud CDN) in front so static content is served from edge caches, absorbing L7 request volume away from origin.
3. Apply **WAF rate-based rules** to cap requests per IP on the login path (e.g. >100 req/5min → block) to kill the **L7 flood**.
4. Enable **Shield Advanced automatic application layer mitigation** / health-based detection so scrubbing adapts.
5. Verify the origin stays healthy via health checks; throttle or challenge (CAPTCHA) suspicious bursts.
6. Post-incident: review Shield/WAF logs, tighten rate limits, and open a post-mortem.

**Bonus question**
Why does a CDN alone *not* stop a pure volumetric L3 UDP flood? *(Answer: a CDN only sees HTTP(S) it can proxy; a raw L3/L4 flood saturates pipe/bandwidth before reaching the CDN's HTTP layer, so you need network-layer scrubbing like Shield/Azure DDoS Protection underneath.)*

---

### 6.5 — Lock Down a 3-Tier App (NSG/ACL + Endpoint + IAM Scoped Policy)

**Scenario**
A classic web → app → database 3-tier app. Currently everything is in one flat subnet and the DB is reachable from the internet. Harden it following least privilege and segmentation.

**Walkthrough**
1. Put each tier in its **own subnet**: web tier (public), app tier (private), database tier (private, no public route).
2. Apply **Network Security Groups (NSG)** (Azure) / **security groups + NACLs** (AWS): 
   - Web SG allows **80/443 from anywhere**, nothing else outbound to DB.
   - App SG allows **8080 only from the web SG**, nothing from internet.
   - DB SG allows **3306 only from the app SG**.
3. Use a **NACL (stateless, subnet-level)** as a coarse perimeter deny, and **NSG/SG (stateful)** for tier-to-tier rules.
4. Enable **endpoint protection** (EDR/antimalware) on every VM in all three tiers — patch + detect host malware.
5. Write a **scoped IAM policy**: the app's role can only `rds-db:connect` to *this specific* DB ARN, with **no `*`**, no console access, rotated credentials.
6. Verify with a connectivity test: DB port closed from internet, open only web→app→db path.

**Bonus question**
In AWS, if you allow inbound 3306 in the DB security group *from the app SG* but forget the app's outbound rule, does the connection work? *(Answer: yes — security groups are **stateful**; the return traffic is automatically allowed, so you don't need a matching outbound rule. With a stateless NACL you *would* need both directions.)*

---

## SECTION 7 — MEMORY AIDS

Short hooks to lock the 4.5 sub-objectives into memory. Each maps to a CV0-004 objective number.

### 4.5.3 — IDS-alert-vs-IPS-block
> **"IDS watches, IPS wrestles."**
**IDS** = passive, sits *out-of-band*, only **alerts**. **IPS** = inline, **blocks** in real time. Same detection engine, different action. Exam trick: if the question says "without risk of dropping traffic," pick **IDS**; if it says "must stop the attack automatically," pick **IPS**.

### 4.5.6a — ACL-stateless
> **"ACL = both ways, every time."**
A **Network ACL (NACL)** is **stateless** — you must write *explicit inbound AND outbound* rules; return traffic is not remembered. It evaluates rules in **numbered order**, and it's attached at the **subnet** level (applies to all instances there).

### 4.5.6c — NSG-stateful
> **"NSG = one rule, both directions."**
A **Network Security Group** is **stateful** — allow the inbound and the **return is automatic**. Attached to a **NIC or subnet**. This is the Azure name; in AWS the equivalent "security group" is also stateful. Remember: **stateful = simpler, stateless = explicit**.

### 4.5.6b — WAF-L7
> **"WAF lives at Layer 7."**
**Web Application Firewall** inspects **HTTP/HTTPS** — SQLi, XSS, bad bots, rate limits. It sits *in front of* your app/load balancer. It does **not** do raw port blocking (that's a network firewall/NSG). Exam: "stop SQL injection" → **WAF**; "block port 22 from a CIDR" → **NSG/ACL**.

### 4.5.4 — DDoS-edge-scrub
> **"Scrub at the edge, cache at the edge."**
Mitigate DDoS with **edge scrubbing** (Shield / Azure DDoS Protection / Cloud Armor) for L3/L4, plus a **CDN + WAF rate rules** for L7. Volumetric floods need *network-layer* defence; HTTP floods need *application-layer* defence. Both, not either/or.

### 4.5.2 — DLP-data-egress
> **"DLP = guard the exit."**
**Data Loss Prevention** watches **data egress** — classify (Macie) and stop sensitive data (PII, cards) leaving via email, uploads, or cloud storage. It's about **content**, not ports. Pair with bucket policies / CASB to enforce.

### ASCII Decision Tree — "Which control?"

```
                     WHAT'S THE THREAT?
                              |
        +---------------------+---------------------+
        |                     |                     |
   TRAFFIC FLOOD         WEB EXPLOIT          DATA LEAVING
   (volumetric)         (SQLi/XSS/bot)        (PII/cards out)
        |                     |                     |
   L3/L4 flood?          HTTP attack?          Egress of
        |                     |               sensitive data?
   +----+----+           +----+----+           +----+----+
   |         |           |         |           |         |
  YES       NO          YES       NO          YES       NO
   |         |           |         |           |         |
 SHIELD/   HOST        WAF       IDS/     DLP +     ENDPOINT
 DDoS      MALWARE     (L7)      IPS      bucket      PROTECTION
 ProTECT   below       in        (detect  policy /   (EDR on
 + CDN     |           front     /block   CASB       the VM)
           |           of app    known
           |           |         bad
           |           |         traffic)
           |           |
        HOST MALWARE / UNKNOWN PROCESS ON A VM?
           |
        ENDPOINT PROTECTION (EDR / antimalware / HIDS)
```

Cheat line: **Flood → Shield+DDoS+CDN · Web attack → WAF · Data out → DLP · Weird process → Endpoint/IDS.**

---

## SECTION 8 — CLOUD PROVIDER MAPPING

Use this table to map the generic Cloud+ control to the real product name in each provider. The *concept* is what the exam tests; the *name* is what you'll see in scenario wording.

| Security Control | **AWS** | **Azure** | **GCP** |
|---|---|---|---|
| **Endpoint protection** (EDR/antimalware on VMs) | Amazon Inspector + GuardDuty (agent) / third-party AMI agents | Microsoft Defender for Endpoint / Defender for Cloud (CSPs) | Chronicle / VM Manager + third-party agents |
| **DLP** (data classification & egress) | **Macie** (S3 PII) + S3 bucket policies + AWS DLP/CASB | Microsoft Purview (DLP) + Azure Information Protection | Sensitive Data Protection (DLP) + VPC Service Controls |
| **IDS / IPS** | GuardDuty (IDS-style) + AWS Network Firewall (IPS inline) | Azure Network Watcher / Azure Firewall (IDPS) | Cloud IDS (intrusion detection) + Cloud NAT/Firewall |
| **DDoS protection** | **Shield Standard** (free) / **Shield Advanced** (L3/L4/L7) | Azure **DDoS Protection** (Basic/Standard/Network Pro) | **Cloud Armor** (L3/L4 + L7 DDoS defence) |
| **WAF** (Layer 7) | **AWS WAF** (on ALB/CloudFront/API GW) | **Azure WAF** (on Front Door / App Gateway) | **Cloud Armor** (on LB / CDN) |
| **NSG / ACL** (network segmentation) | **Security Groups** (stateful, per ENI) + **NACLs** (stateless, per subnet) | **NSGs** (stateful, per NIC/subnet) + **ASGs** | **Firewall Rules** + **VPC Firewall** (stateful) + **VPC Service Controls** |
| **IAM policy** (least privilege) | **IAM** policies / roles / permission boundaries | **Azure RBAC** + Entra ID roles | **Cloud IAM** (roles, bindings, org policies) |

### Cross-Provider Reminders (exam traps)

- **Stateful vs stateless is universal:** AWS *Security Groups* and Azure *NSGs* are **stateful**; AWS *NACLs* are **stateless**. If a question mixes them, the return-traffic rule is the giveaway.
- **Explicit Deny always wins** across AWS, Azure, and GCP IAM — your safety-net bucket/IAM policy should use `Deny`.
- **WAF ≠ network firewall:** WAF = L7 HTTP only; NSG/ACL/firewall = ports/IPs. Don't pick WAF to "block port 22."
- **DDoS needs two layers:** edge/network scrubbing (Shield/Azure DDoS/Cloud Armor) for L3/L4 **and** WAF+CDN rate rules for L7. One alone fails the combined attack in 6.4.
- **Least privilege is provider-agnostic:** scope IAM/RBAC to the *specific* resource ARN, use roles not keys, rotate, and avoid `*` — tested in every provider's "lock it down" PBQ.
- **DLP is about content leaving**, not ports — pair it with storage policies (S3 bucket policy / Purview / VPC Service Controls) to actually *enforce* the block.
- **IDS vs IPS decision is the same everywhere:** passive alert (safe for fragile prod) vs inline block (stops attacks, risk of false positives). Run both for defence in depth.


## SECTION 9 — INTERVIEW KNOWLEDGE

### Conceptual Q&A

**Q: What is the difference between an IDS and an IPS?**
**A:** An **IDS (Intrusion Detection System)** is *passive* — it monitors traffic and *alerts* you when it sees something suspicious, but it does not block anything. An **IPS (Intrusion Prevention System)** is *active* — it sits inline in the traffic path and can *drop, reject, or reset* malicious sessions in real time. Think of IDS as a security camera and IPS as a bouncer.

**Q: How does DLP actually stop a data leak?**
**A:** **DLP (Data Loss Prevention)** inspects content and context (file type, keywords, regex patterns, destination) and enforces rules. It can block uploads, encrypt emails, or quarantine files based on *classification* (e.g., "Confidential"). In the cloud it often runs at the **CASB** or **endpoint agent** level.

**Q: Why is endpoint protection harder in the cloud than on-prem?**
**A:** Cloud endpoints are more *ephemeral* (auto-scaled VMs, containers, serverless) and *distributed* across regions/accounts. You cannot rely on a single on-prem appliance, so you use **agent-based EDR/XDR**, **immutable images**, and **configuration baselines** baked into templates.

**Q: What is the relationship between NSG, WAF, and a firewall ACL?**
**A:** They are layers. An **NSG (Network Security Group)** is Azure's *stateful L3/L4* packet filter (allow/deny by IP/port). A **WAF (Web Application Firewall)** is a *Layer 7* filter that understands HTTP and blocks OWASP attacks (SQLi, XSS). A **firewall ACL** is a generic rule list (often stateless) on a perimeter firewall. Defense in depth = use all three.

### Scenario Questions

**Scenario 1 — Detected web exploit:**
Your WAF logs show repeated `SQL injection` attempts against a public web app, and one request returned a `500 error` with a stack trace. What do you do?
- **Step 1:** Confirm the WAF **blocked** the attempts (if in *Detection* vs *Prevention* mode, switch to Prevention).
- **Step 2:** Patch the vulnerable parameter / use **parameterized queries**; never return raw stack traces to users.
- **Step 3:** Rotate any **credentials or tokens** that may have been exposed, and review **access logs** for successful breaches.
- **Step 4:** Enable **rate limiting** and add a custom WAF rule to block the offending IP/signature.
- **Step 5:** Open an **incident ticket**, document, and update the **runbook**.

**Scenario 2 — Controls for a new public app:**
You are launching a new public-facing cloud app. Which controls do you pick and why?
- **WAF** in front of the app (Layer 7 protection, OWASP Top 10).
- **NSG / firewall ACL** to restrict management ports (22/3389) to known IPs only.
- **IAM policy** with **least privilege** + **MFA** for all admin roles; no long-lived keys.
- **Endpoint protection (EDR)** on the backend VMs/containers.
- **DDoS protection** (e.g., cloud provider's managed DDoS service) on the public IP.
- **DLP** on any data store holding PII to prevent accidental exposure.

**Scenario 3 — IAM policy mistake:**
A junior admin attached a policy granting `*` (all actions) on `*` (all resources) to a contractor. What is the risk and fix?
- **Risk:** Over-permissioned identity = trivial **privilege escalation** and blast radius if the account is compromised.
- **Fix:** Immediately detach the wildcard policy, apply a **scoped least-privilege policy**, enable **CloudTrail/audit logging**, and set **permissions boundaries** so it cannot be re-escalated.

### "Explain It Simply" Prompts

- *Endpoint protection* = "Antivirus, but smarter — it watches behavior, not just signatures, and can isolate a hacked laptop instantly."
- *DDoS* = "A thousand kids all calling your phone at once so real customers can't get through; mitigation = filter the noise and absorb the flood."
- *IAM policy* = "A bouncer list that says exactly who can open which door, when, and what they can do inside."
- *WAF* = "A security guard that reads every web request in plain language and throws out the ones trying to trick your website."
- *IDS vs IPS* = "IDS is the alarm; IPS is the alarm that also tackles the intruder."

---

## SECTION 10 — FLASHCARDS

**Q:** What does EDR stand for? **A:** **Endpoint Detection and Response** — agent-based tool that detects, investigates, and responds to endpoint threats.

**Q:** What is XDR? **A:** **Extended Detection and Response** — correlates telemetry across endpoints, network, email, and cloud for broader visibility.

**Q:** What is the main purpose of DLP? **A:** Prevent **sensitive data** from leaving the organization via email, USB, cloud upload, or print.

**Q:** At what OSI layer does a WAF operate? **A:** **Layer 7 (Application layer)** — it understands HTTP/HTTPS and web app logic.

**Q:** At what layer does an NSG filter? **A:** **L3/L4 (Network/Transport)** — by source/destination IP, port, and protocol; stateful.

**Q:** What is the difference between IDS and IPS? **A:** IDS *alerts only* (passive); IPS *blocks* malicious traffic inline (active).

**Q:** Name two OWASP attacks a WAF blocks. **A:** **SQL Injection** and **Cross-Site Scripting (XSS)**.

**Q:** What is a firewall ACL? **A:** An ordered list of **allow/deny rules** (by IP/port/protocol) controlling traffic through a firewall.

**Q:** What is least privilege? **A:** Granting an identity **only the permissions** required for its task, nothing more.

**Q:** What is MFA and why is it important? **A:** **Multi-Factor Authentication** — requires 2+ proof factors; stops most credential-based attacks.

**Q:** What is a permissions boundary? **A:** An AWS feature that sets the **maximum permissions** an IAM identity can receive, preventing escalation.

**Q:** What is a Zero Trust model? **A:** "**Never trust, always verify**" — no implicit trust by network location; verify every request.

**Q:** What is the goal of DDoS mitigation? **A:** Absorb or filter the **volumetric/ protocol/ application flood** so legitimate traffic still flows.

**Q:** What is a volumetric DDoS attack? **A:** Flooding bandwidth with massive traffic (e.g., **UDP/ICMP floods**) to saturate the link.

**Q:** What is a SYN flood? **A:** A **L4 protocol attack** that exhausts server connection-table resources with half-open TCP connections.

**Q:** What cloud service protects against DDoS? **A:** Provider-managed services like **AWS Shield**, **Azure DDoS Protection**, **GCP Cloud Armor**.

**Q:** What is a CASB? **A:** **Cloud Access Security Broker** — enforces policy (incl. DLP) between users and cloud services.

**Q:** What is tokenization in DLP? **A:** Replacing sensitive data with **non-sensitive tokens** so the real value is never exposed.

**Q:** What is a signature-based detection? **A:** Matching traffic/behavior against a **known pattern database** of attacks (fast, needs updates).

**Q:** What is anomaly-based detection? **A:** Detecting deviations from a **baseline** of normal behavior (catches unknowns, more false positives).

**Q:** What is host-based IDS (HIDS)? **A:** An IDS agent on a **single host** monitoring its files, logs, and system calls.

**Q:** What is network-based IDS (NIDS)? **A:** An IDS watching **network traffic** across a segment/sensor (e.g., mirrored port).

**Q:** What is geo-blocking? **A:** A firewall/WAF rule denying traffic from **specific countries/regions** you don't do business with.

**Q:** What is rate limiting? **A:** Restricting **request counts per client/IP** to blunt abuse and application-layer DDoS.

**Q:** What is a security group (AWS)? **A:** A **stateful** virtual firewall at the instance level controlling inbound/outbound traffic.

**Q:** How does an NSG differ from a security group? **A:** NSG is Azure's **stateful** L3/L4 filter applied to NICs/subnets; similar concept, different cloud.

**Q:** What is the principle of defense in depth? **A:** Use **multiple, layered controls** so one failure doesn't breach the system.

**Q:** What is data classification? **A:** Labeling data (Public/Internal/Confidential/Restricted) to drive **handling and DLP rules**.

**Q:** What is an IAM role? **A:** A temporary **identity assumed** by a service/user with defined permissions, not a fixed user.

**Q:** What is a service account? **A:** A non-human identity used by **applications/automation**; should be tightly scoped.

**Q:** What is the risk of long-lived access keys? **A:** If leaked, they grant **persistent access**; prefer roles/temp tokens and rotate regularly.

**Q:** What is egress filtering? **A:** Controlling **outbound** traffic to block exfiltration and command-and-control callbacks.

**Q:** What is a false positive in IDS/IPS? **A:** An **alert on legitimate activity** — too many erode trust and hide real incidents.

**Q:** What is a false negative? **A:** A **missed real attack** — the most dangerous failure for a detection control.

**Q:** What is container image scanning? **A:** Scanning **container images** for vulnerable packages before deployment (endpoint/supply-chain).

**Q:** What is immutable infrastructure? **A:** Rebuilding servers from **golden images** rather than patching live — reduces config drift.

**Q:** What is a WAF rule group / managed rule set? **A:** Prebuilt rules (e.g., **OWASP Core Rule Set**) you attach to block common web attacks quickly.

**Q:** What is lateral movement? **A:** An attacker moving **from one compromised host to others**; micro-segmentation limits it.

**Q:** What is micro-segmentation? **A:** Fine-grained **network isolation** between workloads to contain breaches.

**Q:** What is the shared responsibility model for cloud security? **A:** Provider secures **infrastructure**; you secure **data, identities, OS, and config**.

---

## SECTION 11 — PRACTICE QUESTIONS

1. Which tool is BEST described as an agent that detects, investigates, and automatically responds to threats on a laptop or server?
A. CASB
B. EDR
C. WAF
D. NIDS
Answer: B
Explanation: EDR provides endpoint-level detection and response, unlike network or web-layer tools.

2. A cloud VM shows suspicious behavior but no known malware signature. Which detection method is MOST likely to catch it?
A. Signature-based
B. Anomaly-based
C. ACL rule
D. Geo-block
Answer: B
Explanation: Anomaly-based detection flags deviations from baseline behavior, catching unknown threats.

3. Which control is designed to stop sensitive customer PII from being uploaded to a personal cloud drive?
A. IDS
B. DLP
C. NSG
D. WAF
Answer: B
Explanation: DLP enforces data-handling policy and can block exfiltration to unauthorized destinations.

4. A user emails a spreadsheet containing credit card numbers. DLP should:
A. Encrypt the attachment and deliver
B. Block or quarantine based on classification
C. Allow it because it is internal
D. Rate-limit the email
Answer: B
Explanation: DLP inspects content and blocks/quarantines based on sensitive-data classification.

5. What is the KEY difference between an IDS and an IPS?
A. IDS is cloud-only
B. IPS can actively block traffic inline
C. IDS uses Layer 7 only
D. IPS generates more false positives
Answer: B
Explanation: IPS sits inline and can drop/reject malicious sessions; IDS only alerts.

6. A NIDS sensor placed on a mirrored port sees all segment traffic but takes no action. This is an example of:
A. Prevention mode
B. Passive detection
C. Inline blocking
D. Host isolation
Answer: B
Explanation: A sensor that only monitors/alerts (no blocking) is passive detection.

7. Which is a classic volumetric DDoS attack intended to saturate bandwidth?
A. SQL injection
B. UDP flood
C. XSS
D. Privilege escalation
Answer: B
Explanation: UDP floods generate massive traffic to exhaust bandwidth (volumetric attack).

8. A server's connection table is exhausted by half-open TCP connections. Which attack is this?
A. Application-layer DDoS
B. SYN flood
C. DNS amplification
D. Buffer overflow
Answer: B
Explanation: A SYN flood is a Layer 4 protocol attack that exhausts connection-table resources.

9. Which IAM principle ensures a contractor can only read one specific bucket and nothing else?
A. Shared responsibility
B. Least privilege
C. Defense in depth
D. Micro-segmentation
Answer: B
Explanation: Least privilege grants only the minimum permissions needed for the task.

10. Attaching a policy with `Action: *` and `Resource: *` to an identity is dangerous because it:
A. Enables MFA automatically
B. Grants unrestricted privileges and huge blast radius
C. Improves performance
D. Is required for serverless
Answer: B
Explanation: Wildcard allow-all policies over-permission identities and enable escalation.

11. To prevent an IAM user from ever exceeding a defined permission ceiling even if granted more, use a:
A. Security group
B. Permissions boundary
C. WAF rule
D. NSG
Answer: B
Explanation: A permissions boundary caps the maximum permissions an identity can hold.

12. A firewall ACL is configured to deny inbound TCP/22 from 0.0.0.0/0. What does this accomplish?
A. Blocks all HTTP traffic
B. Blocks SSH from any internet source
C. Enables DDoS protection
D. Encrypts traffic
Answer: B
Explanation: TCP/22 is SSH; denying from any source closes remote shell access from the internet.

13. A public web app is hit with SQL injection and XSS. Which control should be deployed in front of it?
A. NSG
B. WAF
C. DLP
D. EDR
Answer: B
Explanation: A WAF operates at Layer 7 and blocks OWASP web attacks like SQLi and XSS.

14. An Azure NSG is attached to a subnet to allow only HTTPS from the internet and block RDP. The NSG is:
A. Stateless and L7
B. Stateful L3/L4 filter
C. A web application firewall
D. An endpoint agent
Answer: B
Explanation: NSGs are stateful Layer 3/4 filters by IP, port, and protocol.

15. You need to restrict management ports (22/3389) to a known office IP while allowing web traffic. Best combination?
A. WAF + DLP
B. NSG/firewall ACL + security group
C. IDS only
D. EDR + CASB
Answer: B
Explanation: Network-layer allow/deny rules (NSG/ACL/security group) filter by port and source IP.

16. Container images should be scanned for vulnerabilities:
A. After they are already running in production
B. Before deployment from a golden/base image
C. Only annually
D. By the end user
Answer: B
Explanation: Scan images pre-deployment to prevent shipping known vulnerabilities.

17. To reduce configuration drift and speed recovery, an organization rebuilds servers from a known good image rather than patching live. This is:
A. Micro-segmentation
B. Immutable infrastructure
C. Lateral movement
D. Tokenization
Answer: B
Explanation: Immutable infrastructure rebuilds from golden images, eliminating drift.

18. A HIDS alerts that the `/etc/passwd` file changed unexpectedly. This is an example of:
A. Volumetric DDoS
B. Host-based anomaly detection
C. WAF blocking
D. Geo-blocking
Answer: B
Explanation: HIDS monitors host files/system state and flags unauthorized changes.

19. Which mitigation absorbs a large application-layer HTTP flood targeting a login page?
A. SYN cookie
B. Rate limiting + WAF
C. Signature AV
D. NSG only
Answer: B
Explanation: Rate limiting plus a WAF blunts application-layer floods on specific endpoints.

20. Enabling MFA for all admin roles primarily defends against which threat?
A. Volumetric DDoS
B. Credential theft / phishing
C. SQL injection
D. Lateral movement via VLAN
Answer: B
Explanation: MFA adds a second factor, neutralizing most stolen-password/phishing attacks.

---

## SECTION 12 — EXAM PRIORITY

| Rank | Sub-Objective | Exam Likelihood | Why It Shows Up Often |
|------|---------------|-----------------|------------------------|
| 1 | IAM policies (least privilege, MFA, boundaries) | **High** | Identity is the new perimeter; tons of scenario + config questions. |
| 2 | Firewall (ACL / WAF / NSG) | **High** | Layers 3/4 vs 7 distinctions and "which control?" scenarios are exam favorites. |
| 3 | IDS / IPS | **High** | Easy to test detection vs prevention and signature vs anomaly. |
| 4 | DDoS | **Med** | Usually 1-2 questions on attack types and mitigation services. |
| 5 | Endpoint protection (EDR/XDR) | **Med** | Growing topic; tested on agent-based response and immutability. |
| 6 | DLP | **Med** | Often bundled into data-handling / compliance scenarios. |

*Note:* While DDoS/Endpoint/DLP are "Med," they still appear — prioritize IAM and Firewall/IDS first.

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

- **Endpoint protection:** Deploy **EDR/XDR** agents; use **immutable golden images** and scan containers pre-deploy.
- **DLP:** Classify data, then block/exfiltrate control via **endpoint agent or CASB**; watch email, USB, cloud upload.
- **IDS/IPS:** **IDS = alert only (passive)**; **IPS = inline block**; know **signature vs anomaly** detection.
- **DDoS:** Volumetric (UDP flood) / Protocol (SYN flood) / Application (HTTP flood); mitigate with **provider DDoS service + rate limiting + WAF**.
- **IAM policies:** Always apply **least privilege**, enforce **MFA**, use **permissions boundaries** to cap escalation.
- **Firewall (ACL/WAF/NSG):** **NSG/ACL = L3/L4** (IP/port); **WAF = L7** (SQLi/XSS); layer them for **defense in depth**.
- **Cross-cutting:** Remember the **shared responsibility model** — you own identities, data, and config.
- **Golden rule:** When a question asks "which control stops X," match the **layer** (network vs app vs identity vs endpoint).

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024-2025, CV0-004-relevant)

- **2024 — Managed WAF & Edge DDoS services expand:** Major providers (AWS, Azure, Cloudflare) rolled out **managed WAF rule sets** and **auto-tuning** plus always-on **L3/L4/L7 DDoS protection** bundled at the edge, reducing the need for self-managed appliances.
- **2024 — EDR to XDR consolidation accelerates:** Organizations increasingly adopt **XDR platforms** that correlate endpoint, network, email, and cloud telemetry, shifting from standalone AV to unified detection/response.
- **2024 — AI-driven DDoS & bot defense:** A surge in **AI-orchestrated volumetric and application-layer attacks** pushed wider adoption of **behavioral bot management** and ML-based anomaly detection in WAFs.
- **2025 — Zero Trust mandated in cloud baselines:** Frameworks (NIST 800-207 aligned) pushed **identity-centric controls** — continuous verification, **least-privilege IAM**, and **micro-segmentation** — into default cloud hardening guides.
- **2025 — DLP extends to SaaS via CASB/SSPM:** Data-loss prevention increasingly delivered through **CASB and SSPM** to govern data across scattered SaaS apps, not just endpoints.

**Report line:** First line written = `## SECTION9 — INTERVIEW KNOWLEDGE` (exact required opening).
**Flashcard count:** 40 pairs (SECTION10).
**Practice-question count:** 20 MCQs (SECTION11).
