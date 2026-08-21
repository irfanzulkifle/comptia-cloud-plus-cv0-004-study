# Objective 4.5 — Given a scenario, apply security controls in the cloud

> **Domain 4.0 — Security (19% of exam)** · Exam CV0-004 V4 · Objectives doc v5.0
> **Status:** Exam-ready · **Version:** 2.0 (enhanced) · Supersedes `full notes/Objective-4.5-Security-Controls.md`

---

## 0. How to use this note

| Pass | What to read | Time |
|---|---|---|
| **1st (learn)** | Sections 1 → 9 in order | ~60 min |
| **2nd (drill)** | Section 2.1 (control types) + Section 5 (IDS vs IPS) + Section 8 (the firewall family) | ~20 min |
| **3rd (test)** | Section 12 (practice) + Section 13 (PBQ drills) | ~30 min |
| **Exam eve** | Section 14 (60-second recall sheet) only | ~5 min |

> 📌 **4.4 was security *practices*; 4.5 is the specific *controls* that implement them.** Two things carry the marks: classifying a control as **preventive, detective, or corrective**, and the **IDS vs IPS** distinction. The firewall family repeats 1.3 — if that is solid, this section is quick revision.

---

## 1. Official objective coverage

> **4.5 Given a scenario, apply security controls in the cloud.**
> - Endpoint protection
> - Data loss prevention (DLP)
> - Intrusion prevention system / intrusion detection system (IPS/IDS)
> - Distributed denial-of-service (DDoS) protection
> - Identity and access management (IAM) policies
> - **Firewall**
>   - Network access control list (ACL)
>   - Web application firewall (WAF)
>   - Network security group

### 1.1 What the verb tells you

**"Given a scenario, apply"** — an **application** objective. You are given a threat or requirement and asked which control addresses it. Most wrong answers are controls that operate at the **wrong layer** — a firewall offered against SQL injection, or a WAF offered against a volumetric flood.

### 1.2 Coverage checklist

- [ ] I can classify a control as **preventive, detective, or corrective**
- [ ] ★ I know the difference between **IDS** and **IPS** and where each sits in the traffic path
- [ ] I know **signature-based** vs **anomaly-based** detection and what each misses
- [ ] I know what **DLP** inspects and the actions it can take
- [ ] I can name the **three DDoS attack layers** and the mitigation for each
- [ ] I know why **auto-scaling is not a DDoS defence** on its own
- [ ] I know the elements of an **IAM policy** and that **explicit deny wins**
- [ ] I can distinguish **NACL**, **security group**, and **WAF** by layer, scope, and state
- [ ] I know which control stops **SQL injection**, and which cannot
- [ ] I know what **endpoint protection** covers in a cloud context

---

## 2. The core mental model

### 2.1 ★ Control types — the classification that gets tested

```text
   BY FUNCTION
   ┌──────────────┬───────────────────────────────────────────────┐
   │ PREVENTIVE   │ STOPS it happening                            │
   │              │ firewall · security group · NACL · WAF ·      │
   │              │ IAM policy · encryption · MFA · ★ IPS ·       │
   │              │ DLP in blocking mode                          │
   ├──────────────┼───────────────────────────────────────────────┤
   │ DETECTIVE    │ IDENTIFIES that it happened or is happening   │
   │              │ ★ IDS · logging · monitoring · audit trail ·  │
   │              │ DLP in alert mode · vulnerability scanning    │
   ├──────────────┼───────────────────────────────────────────────┤
   │ CORRECTIVE   │ FIXES or RESTORES afterwards                  │
   │              │ backup restore · patching · quarantine ·      │
   │              │ automated remediation · rollback              │
   └──────────────┴───────────────────────────────────────────────┘

   ALSO RECOGNISE
     DETERRENT     discourages the attempt (banners, visible controls)
     COMPENSATING  an alternative when the primary control is not
                   feasible (e.g. WAF rule while awaiting a patch)
     DIRECTIVE     policy and procedure that directs behaviour

   BY NATURE
     TECHNICAL (logical) · ADMINISTRATIVE (policy, training) ·
     PHYSICAL (locks, guards — the provider's in cloud)

   ★ THE PAIR THAT DECIDES QUESTIONS
     IDS = DETECTIVE (watches and alerts)
     IPS = PREVENTIVE (sits inline and blocks)
```

### 2.2 Where each control sits

```text
   ☁ INTERNET
       │
   ┌───▼─────────────────────────────────────────────────────┐
   │ DDoS PROTECTION      absorbs volumetric floods upstream │
   ├─────────────────────────────────────────────────────────┤
   │ WAF                  L7 — inspects HTTP payloads        │
   ├─────────────────────────────────────────────────────────┤
   │ IPS / IDS            L3-L7 — inspects traffic patterns  │
   ├─────────────────────────────────────────────────────────┤
   │ NETWORK ACL          subnet boundary · STATELESS        │
   ├─────────────────────────────────────────────────────────┤
   │ SECURITY GROUP       instance/ENI · STATEFUL            │
   ├─────────────────────────────────────────────────────────┤
   │ ENDPOINT PROTECTION  on the host — anti-malware, EDR    │
   ├─────────────────────────────────────────────────────────┤
   │ IAM POLICIES         who may call which API on what     │
   ├─────────────────────────────────────────────────────────┤
   │ DLP                  watches sensitive DATA leaving     │
   └─────────────────────────────────────────────────────────┘

   ★ Match the control to the LAYER the threat operates at.
     Most wrong answers are the right idea at the wrong layer.
```

---

## 3. Endpoint protection

| | |
|---|---|
| **Definition** | Security software on **endpoints** — cloud instances, containers, servers, and user devices — that prevents, detects, and responds to malicious activity on the host itself. |
| **Components** | **Anti-malware/antivirus** (signature and behavioural) · **EDR** — endpoint detection and response, recording activity for investigation and enabling remote isolation · **host-based firewall** · **HIDS** — host intrusion detection · **disk encryption** · **application allow-listing** · **device posture checks** |
| **Why in cloud** | Instances are endpoints too. In IaaS the **guest OS is the customer's responsibility** (1.1), so host-level protection is yours to deploy |
| **★ EDR vs traditional AV** | AV matches **known signatures**; **EDR records behaviour**, detects novel attacks, supports threat hunting, and can **isolate a compromised host** |
| **Cloud considerations** | Bake the agent into the **golden image** so every instance starts protected (4.4) · ephemeral and auto-scaled instances need automatic enrolment · containers are usually protected by **image scanning and runtime policy** rather than a traditional AV agent |
| **Exam triggers** | "malware on a server", "detect and respond on the host", "isolate the compromised instance", "agent on every instance", "device posture" |

---

## 4. Data loss prevention (DLP)

| | |
|---|---|
| **Definition** | Controls that **detect and prevent sensitive data leaving** the organisation — whether by accident or deliberate exfiltration. |
| **How it identifies data** | **Pattern matching** (card numbers, national IDs, formats) · **keywords and dictionaries** · **fingerprinting** of known documents · **classification labels** (4.2) · machine learning classifiers |
| **★ Depends on classification** | DLP cannot protect what has not been defined as sensitive — **data classification is the prerequisite** |
| **Exam triggers** | "prevent card numbers being emailed out", "block uploads of confidential files", "detect sensitive data in a public bucket", "stop exfiltration" |

```text
   WHERE DLP OPERATES              ACTIONS IT CAN TAKE

   DATA IN MOTION   email, web    ┌──────────────────────────────┐
                    uploads, API  │ ALERT      notify only       │
                                  │            (DETECTIVE)       │
   DATA AT REST     buckets,      │ BLOCK      stop the transfer │
                    shares, DBs   │            (PREVENTIVE)      │
                                  │ QUARANTINE isolate the file  │
   DATA IN USE      endpoints,    │ ENCRYPT    force protection  │
                    clipboard,    │ REDACT     strip the         │
                    USB, print    │            sensitive parts   │
                                  │ LOG        evidence (4.2)    │
                                  └──────────────────────────────┘

   ★ DLP is DETECTIVE in alert mode and PREVENTIVE in blocking mode —
     the same tool, classified by how it is configured.
```

---

## 5. IDS and IPS

### 5.1 ★ The distinction

```text
   IDS — INTRUSION DETECTION            IPS — INTRUSION PREVENTION
   PASSIVE, OUT OF BAND                 ACTIVE, INLINE

        ┌──────────┐                         ┌──────────┐
        │  TRAFFIC │                         │  TRAFFIC │
        └────┬─────┘                         └────┬─────┘
             │ ──── copy ───► ┌─────┐             ▼
             │  (mirror/tap)  │ IDS │        ┌─────────┐
             ▼                └──┬──┘        │   IPS   │ ← in the path
        ┌──────────┐             │ ALERT     └────┬────┘
        │ DESTINATION            ▼                │ allow / DROP
        └──────────┘         [analyst]            ▼
                                              ┌──────────┐
   ✓ No latency added                         │DESTINATION
   ✓ Cannot break traffic                     └──────────┘
   ✗ ★ CANNOT BLOCK — the attack
     still reaches the target             ✓ ★ BLOCKS the attack
   False positive = NOISE                 ✗ Adds latency; a failure
                                            can interrupt traffic
                                          False positive = ★ LEGITIMATE
                                            TRAFFIC BLOCKED
```

| | **IDS** | **IPS** |
|---|---|---|
| Placement | **Out of band** — sees a copy | **Inline** — in the traffic path |
| Action | **Detect and alert only** | **Detect and block** |
| Control type | **Detective** | **Preventive** |
| Latency impact | None | Some |
| Availability risk | None | A failure or misconfiguration can **interrupt traffic** |
| Cost of a false positive | Noise and alert fatigue | ★ **Legitimate traffic is dropped** |
| Best when | Visibility is needed without risk; tuning phase | Known attacks must be stopped automatically |

> ★ **The deployment pattern:** run new signatures in **IDS (alert-only) mode first** to observe false positives, then promote them to **IPS (blocking) mode** once tuned. That answers "how do we deploy prevention without breaking production?"

### 5.2 Detection methods

| | **Signature-based** | **Anomaly / behaviour-based** |
|---|---|---|
| How | Matches **known** attack patterns | Compares against a **baseline** of normal |
| Catches | Known attacks reliably | **Novel and zero-day** activity |
| ★ Misses | **Zero-days and novel variants** | Slow attacks that look normal |
| False positives | **Low** | **Higher** |
| Requires | Signature updates | A **learned baseline** (see 4.6) |

| Scope variant | Meaning |
|---|---|
| **NIDS/NIPS** | Network-based — monitors traffic on a segment |
| **HIDS/HIPS** | Host-based — monitors a single system's activity, files, and processes |

---

## 6. DDoS protection

| | |
|---|---|
| **Definition** | Defending availability against a **distributed denial-of-service** attack — many compromised sources overwhelming a target so legitimate users cannot be served. |
| **Exam triggers** | "traffic flood from thousands of sources", "service unavailable but not compromised", "SYN flood", "HTTP flood", "volumetric attack" |

### 6.1 The three attack layers and their mitigations

```text
   ① VOLUMETRIC (L3/L4)      "fill the pipe"
      UDP floods, DNS/NTP amplification and reflection
      Measured in Gbps/Tbps
      ► MITIGATE: upstream SCRUBBING service, ANYCAST distribution,
        CDN absorption, provider-scale capacity
        ⚠ You cannot absorb this at your own edge — it must be
          handled UPSTREAM, before it reaches your link

   ② PROTOCOL (L3/L4)        "exhaust the state tables"
      SYN floods, fragmented packets, connection exhaustion
      Measured in packets per second
      ► MITIGATE: SYN cookies, connection rate limits, stateful
        device tuning, load balancer absorption

   ③ APPLICATION (L7)        "make the app do expensive work"
      HTTP floods, slow-loris, repeated costly queries
      Low volume, hard to distinguish from real users
      ► MITIGATE: ★ WAF, rate limiting, bot management, CAPTCHA,
        caching, request cost analysis
```

### 6.2 ★ Auto-scaling is not a DDoS defence

```text
   Auto-scaling responds to a flood by ADDING CAPACITY.
   The attack is then absorbed — and BILLED TO YOU.

   ┌──────────────────────────────────────────────────────────────┐
   │  "ECONOMIC DENIAL OF SUSTAINABILITY"                          │
   │  The service stays up, but the attacker converts a downtime    │
   │  attack into a COST attack.                                   │
   └──────────────────────────────────────────────────────────────┘

   ★ THE CONTROLS THAT MATTER
     · Set a MAXIMUM on scaling groups (3.2) — a budget ceiling
       and a downstream protection
     · Filter the traffic UPSTREAM so it never reaches compute
     · Rate limit at the edge
     · Budget alerts to detect the cost spike early (1.8, 3.1)
```

### 6.3 The standard defence stack

| Layer | Control |
|---|---|
| **Upstream** | Always-on DDoS protection / scrubbing at the provider edge |
| **Distribution** | **CDN and anycast** spread the load across many locations (1.3) |
| **Application** | **WAF** with rate-based rules; bot management |
| **Architecture** | Keep origins private behind the CDN/load balancer; do not expose origin IPs |
| **Capacity** | Auto-scaling as a shock absorber — **with a maximum** |
| **Detection** | Traffic baselines and alerting (3.1, 4.6) |

---

## 7. IAM policies

Covered in depth in **4.3**. As a *control*, the mechanics that get tested:

```text
   THE ANATOMY OF A POLICY STATEMENT

   ┌─────────────┬────────────────────────────────────────────┐
   │ EFFECT      │ Allow or DENY                              │
   │ PRINCIPAL   │ WHO — user, role, service, account         │
   │ ACTION      │ WHAT operation — read, write, delete       │
   │ RESOURCE    │ ON WHAT — bucket, instance, key, ARN       │
   │ CONDITION   │ WHEN/HOW — source IP, MFA present, time,   │
   │             │ encryption required, tag match             │
   └─────────────┴────────────────────────────────────────────┘

   ★ EVALUATION RULES
     · DENY BY DEFAULT — nothing is permitted unless granted
     · ★ AN EXPLICIT DENY ALWAYS WINS over any allow
     · Permissions are the UNION of all applicable allows,
       minus any explicit deny
```

| Policy type | Attached to | Answers |
|---|---|---|
| **Identity-based** | A user, group, or role | "What may **this identity** do?" |
| **Resource-based** | The resource itself (e.g. a bucket policy) | "**Who** may act on this resource?" — including principals in other accounts |
| **Permission boundary / guardrail** | An identity or an organisation | The **maximum** permissions that can ever be granted |

> ★ **Conditions are the underused control.** Requiring MFA, a source network, or encryption-in-transit as a **condition** on a policy is often the precise answer to "how do we allow this but only under these circumstances."

---

## 8. Firewalls

This trio is the direct application of **1.3** — the contrast is the same, so use this as revision.

### 8.1 Network ACL vs security group

| | **Network ACL** | **Security group** |
|---|---|---|
| Scope | **Subnet** boundary | **Instance / network interface** |
| **State** | ⭐ **Stateless** | ⭐ **Stateful** |
| Return traffic | ❌ **Must be explicitly allowed** (ephemeral ports 1024–65535) | ✅ Automatically allowed |
| Rule types | **Allow and DENY** | **Allow only** (implicit deny) |
| Evaluation | **Numbered, lowest first, first match wins** | All rules evaluated; any match permits |
| Best used for | Coarse subnet guardrails, **explicitly blocking a bad IP** | **The primary least-privilege control** |
| Layer | 3–4 | 3–4 |

> ⚠️ **The stateless trap, restated:** allowing inbound 443 on a NACL is not enough — the reply leaves from a high ephemeral port and needs its own **outbound** allow rule. Symptom: the connection is accepted but nothing comes back.

### 8.2 Web application firewall

| | |
|---|---|
| **Layer** | **7 — HTTP/HTTPS only** |
| **Inspects** | The **content** of requests — URI, headers, parameters, body |
| **Blocks** | **SQL injection, cross-site scripting, path traversal**, OWASP-class attacks, malicious bots |
| **Also does** | **Rate-based rules** (L7 DDoS), geo-blocking, IP reputation, **virtual patching** while awaiting a real fix (4.1) |
| **Cannot** | Inspect non-HTTP protocols, or stop a volumetric L3/L4 flood |

```text
   ★ WHY A FIREWALL CANNOT STOP SQL INJECTION

   The attack arrives on port 443 — which you DELIBERATELY OPENED.
   A network firewall sees a permitted port and allows it.
   Only a WAF looks INSIDE the request and sees the payload.

   FIREWALL/SG/NACL  →  decides on IP, PORT, PROTOCOL, state
   WAF               →  decides on the HTTP PAYLOAD
```

### 8.3 The wider firewall family

| Control | Role |
|---|---|
| **Security group** | Instance-level, stateful, allow-only — the workhorse |
| **Network ACL** | Subnet-level, stateless, supports **deny** |
| **WAF** | L7 application protection |
| **Managed network firewall** | VPC-wide stateful inspection, **IDS/IPS**, egress filtering, domain filtering |
| **NGFW** | Application awareness, IPS, TLS inspection |
| **Host firewall** | On the instance itself — part of endpoint protection |

---

## 9. Comparison tables

### 9.1 ★ Control classification

| Control | Preventive | Detective | Corrective |
|---|:--:|:--:|:--:|
| Security group / NACL / firewall | ✅ | | |
| **WAF** | ✅ | | |
| **IPS** | ✅ | | |
| **IDS** | | ✅ | |
| IAM policy | ✅ | | |
| Encryption | ✅ | | |
| MFA | ✅ | | |
| DDoS protection | ✅ | | |
| **DLP** | ✅ *(block mode)* | ✅ *(alert mode)* | |
| Endpoint anti-malware | ✅ | ✅ | ✅ *(quarantine/clean)* |
| Logging / audit trail | | ✅ | |
| Vulnerability scanning | | ✅ | |
| Backup restore | | | ✅ |
| Patching | ✅ | | ✅ |

### 9.2 Threat → control

| Threat / requirement | Control |
|---|---|
| SQL injection against a web app | **WAF** |
| Volumetric flood saturating the link | **Upstream DDoS scrubbing / CDN** |
| HTTP flood from many IPs | **WAF rate-based rules + bot management** |
| Malware executing on an instance | **Endpoint protection / EDR** |
| Need to isolate a compromised host | **EDR isolation** |
| Card numbers being emailed externally | **DLP (block mode)** |
| Sensitive files found in a public bucket | **DLP scanning at rest + block public access** |
| Detect suspicious traffic without risking availability | **IDS** |
| Automatically stop known attack traffic | **IPS** |
| Block one malicious source IP for a whole subnet | **Network ACL (deny rule)** |
| Restrict which instances may reach the database | **Security group** |
| Allow an action only when MFA is present | **IAM policy condition** |
| Prevent any role ever exceeding certain permissions | **Permission boundary / guardrail** |
| Grant another account access to a bucket | **Resource-based policy** |
| Temporary protection while awaiting a patch | **WAF virtual patching (compensating control)** |

### 9.3 IDS vs IPS vs WAF vs firewall

| | **Firewall / SG / NACL** | **IDS** | **IPS** | **WAF** |
|---|---|---|---|---|
| Layer | 3–4 | 3–7 | 3–7 | **7 only** |
| Decides on | IP, port, protocol, state | Traffic patterns | Traffic patterns | **HTTP payload** |
| Placement | Inline | **Out of band** | **Inline** | Inline (in front of the app) |
| Action | Allow/deny | **Alert only** | **Alert and block** | Alert and block |
| Type | Preventive | **Detective** | **Preventive** | Preventive |
| Stops SQL injection | ❌ | Detects | Can block | ✅ **Yes** |
| Stops a volumetric flood | ❌ | ❌ | Limited | ❌ |

---

## 10. Exam traps & distractor patterns

| # | Trap | The truth |
|---|---|---|
| 1 | "IDS blocks attacks" | ❌ **IDS detects and alerts only.** **IPS** blocks. IDS is detective, IPS is preventive |
| 2 | "IPS is always better than IDS" | IPS sits **inline** — it adds latency, can interrupt traffic if it fails, and a **false positive blocks legitimate users**. Deploy in alert mode first, then tune |
| 3 | "A firewall stops SQL injection" | The attack arrives on the **port you deliberately opened**. Only a **WAF** inspects the payload |
| 4 | "A WAF protects against volumetric DDoS" | A WAF is **L7 only**. Volumetric floods must be absorbed **upstream** |
| 5 | "Auto-scaling defends against DDoS" | It absorbs the flood and **bills you for it** — *economic denial of sustainability*. **Set a maximum** and filter upstream |
| 6 | "Security groups and NACLs are interchangeable" | **SG = instance, stateful, allow-only. NACL = subnet, stateless, allow+deny, numbered** |
| 7 | "Allowing inbound 443 on a NACL is sufficient" | **Stateless** — you must also allow the **outbound ephemeral port range** for the reply |
| 8 | "DLP works without data classification" | DLP can only protect what has been **defined as sensitive** — classification is the prerequisite (4.2) |
| 9 | "DLP is purely preventive" | **Alert mode is detective; block mode is preventive** — the same tool, classified by configuration |
| 10 | "Signature-based detection catches zero-days" | It matches **known** patterns only. **Anomaly-based** detection catches novel activity, at the cost of more false positives |
| 11 | "Antivirus and EDR are the same" | AV matches signatures; **EDR records behaviour**, detects novel attacks, and can **isolate the host** |
| 12 | "Endpoint protection is unnecessary in cloud" | In IaaS the **guest OS is yours** (1.1) — instances are endpoints |
| 13 | "An explicit allow overrides a deny" | ❌ **An explicit deny always wins** |
| 14 | "IAM policies only attach to users" | **Resource-based policies** attach to the resource and can grant access to other accounts |
| 15 | "More controls always means better security" | Controls must sit at the **right layer** for the threat; misplaced ones add cost and noise without reducing risk |

**Disambiguation drill:**

| Pair | The deciding question |
|---|---|
| **IDS vs IPS** | Does it sit **inline** and **block**, or watch a copy and **alert**? |
| **Preventive vs detective** | Does it **stop** it, or **tell you about it**? |
| **Firewall vs WAF** | Decides on **port/IP**, or on the **HTTP payload**? |
| **Security group vs NACL** | **Instance + stateful + allow-only**, or **subnet + stateless + deny available**? |
| **Signature vs anomaly** | Matching **known patterns**, or deviation from a **baseline**? |
| **AV vs EDR** | Signature matching, or **behavioural recording and response**? |
| **Volumetric vs application DDoS** | Filling the **pipe**, or making the **app work hard**? |
| **Identity vs resource policy** | Attached to **who**, or attached to **what**? |

---

## 11. Keyword → answer trigger table

| If you see… | Apply |
|---|---|
| malware on an instance · isolate the compromised host · behavioural detection on the host | **Endpoint protection / EDR** |
| stop card numbers leaving by email · detect sensitive data in a bucket · prevent exfiltration | **DLP** |
| detect suspicious traffic without affecting availability · alert only · out of band | **IDS** |
| automatically block known attack traffic · inline inspection | **IPS** |
| run in alert mode first, then enable blocking | **IDS→IPS tuning pattern** |
| catches only known attacks | **Signature-based** |
| deviation from normal baseline · catches novel attacks · more false positives | **Anomaly-based** |
| flood from thousands of sources saturating bandwidth | **Volumetric DDoS → upstream scrubbing/CDN** |
| SYN flood · connection table exhaustion | **Protocol DDoS** |
| HTTP flood · slow-loris · expensive repeated queries | **Application DDoS → WAF + rate limiting** |
| service stayed up but the bill exploded during the attack | **Economic denial of sustainability — set a scaling maximum** |
| SQL injection · XSS · OWASP · inspect the HTTP request body | **WAF** |
| block one bad IP for an entire subnet | **NACL deny rule** |
| control which instances may reach the database | **Security group** |
| connection accepted but no response returns | **Stateless NACL missing outbound ephemeral rule** |
| allow only when MFA is present, or only from this network | **IAM policy condition** |
| grant a different account access to this bucket | **Resource-based policy** |
| cap the maximum permissions any role can hold | **Permission boundary / guardrail** |
| temporary protection until the patch ships | **WAF virtual patching (compensating control)** |

---

## 12. Practice questions

<details>
<summary><b>Q1.</b> What is the PRIMARY difference between an IDS and an IPS?</summary>

A. IDS is host-based; IPS is network-based · **B. An IDS is passive and out of band, detecting and alerting only; an IPS sits inline and can block the traffic** · C. IDS inspects HTTP; IPS inspects TCP · D. They are the same

**Correct: B.** The distinction is placement and action — which also makes IDS a **detective** control and IPS a **preventive** one.
- **A wrong:** Both come in network (NIDS/NIPS) and host (HIDS/HIPS) forms.
- **C wrong:** Both can inspect across layers; HTTP payload inspection is a WAF speciality.
- **D wrong:** The difference is the core of this objective.
</details>

<details>
<summary><b>Q2.</b> A web application is being attacked with SQL injection over HTTPS. Which control stops it?</summary>

A. Network ACL · B. Security group · **C. Web application firewall** · D. IDS

**Correct: C — WAF.** The request arrives on port 443, which is deliberately open; only a Layer 7 control inspects the payload and recognises the injection pattern.
- **A/B wrong:** Both decide on IP, port, and protocol — they see a permitted port.
- **D wrong:** An IDS would **detect** it but cannot block.
</details>

<details>
<summary><b>Q3.</b> An organisation is hit by a 400 Gbps UDP amplification flood. Which mitigation is appropriate?</summary>

**A. Upstream DDoS scrubbing at the provider edge, with CDN and anycast distribution** · B. A WAF rule · C. Increasing the instance size · D. Host firewall rules

**Correct: A.** Volumetric attacks must be absorbed **before** they reach your link — you cannot filter a saturated pipe at your own edge.
- **B wrong:** A WAF is L7 and would itself be overwhelmed.
- **C/D wrong:** Neither addresses bandwidth saturation.
</details>

<details>
<summary><b>Q4.</b> During a DDoS attack an auto-scaling group expands to its limit and the service stays online, but the monthly bill triples. What is this called and how is it controlled?</summary>

**A. Economic denial of sustainability — set a maximum on the scaling group, filter traffic upstream, and use rate limiting and budget alerts** · B. Vertical scaling failure · C. A protocol attack · D. Normal elastic behaviour requiring no action

**Correct: A.** The attacker converts a downtime attack into a cost attack. Maximum capacity is both a budget and a security control (3.2, 1.8).
- **B/C wrong:** Neither describes the outcome.
- **D wrong:** Unbounded scaling under attack is a real financial exposure.
</details>

<details>
<summary><b>Q5.</b> Which control classification applies to an IDS?</summary>

A. Preventive · **B. Detective** · C. Corrective · D. Deterrent

**Correct: B — detective.** It identifies and alerts on activity but takes no blocking action; an **IPS** is the preventive equivalent.
- **A wrong:** That is the IPS.
- **C wrong:** Corrective controls restore after an incident.
- **D wrong:** Deterrents discourage attempts.
</details>

<details>
<summary><b>Q6.</b> A NACL allows inbound TCP 443, but clients receive no response. What is missing?</summary>

**A. An outbound rule permitting the ephemeral port range, because NACLs are stateless** · B. An inbound rule for port 80 · C. A security group deny rule · D. A WAF

**Correct: A.** NACLs do not track connection state, so the reply — sourced from a high ephemeral port — needs its own outbound allow (1.3).
- **B/C/D wrong:** None explains the missing return traffic.
</details>

<details>
<summary><b>Q7.</b> Which control prevents employees emailing files containing credit card numbers to external addresses?</summary>

A. IDS · **B. DLP in blocking mode** · C. Security group · D. Endpoint encryption

**Correct: B — DLP.** Pattern matching identifies card-number formats in data **in motion** and blocks the transfer.
- **A wrong:** An IDS watches network intrusions, not data content policy.
- **C wrong:** Security groups filter by IP and port.
- **D wrong:** Encryption protects stored data; it does not prevent sending it.
</details>

<details>
<summary><b>Q8.</b> A security team wants to deploy new IPS signatures without risking legitimate traffic being dropped. What approach should they take?</summary>

**A. Run the signatures in detection (alert-only) mode first, tune out false positives, then enable blocking** · B. Deploy directly in blocking mode for maximum protection · C. Disable the IPS entirely · D. Apply the signatures only to internal traffic

**Correct: A.** An IPS false positive blocks real users, so observe first and promote to blocking once tuned — the standard deployment pattern.
- **B wrong:** That is exactly the risk being avoided.
- **C/D wrong:** Neither achieves protection.
</details>

<details>
<summary><b>Q9.</b> Which detection method can identify a previously unknown zero-day attack?</summary>

A. Signature-based · **B. Anomaly/behaviour-based** · C. Port scanning · D. Allow-listing signatures

**Correct: B.** Baseline-deviation detection can flag novel behaviour, though it produces more false positives than signature matching.
- **A wrong:** Signatures match **known** patterns and by definition miss zero-days.
- **C/D wrong:** Neither is a detection methodology for novel attacks.
</details>

<details>
<summary><b>Q10.</b> An IAM policy grants a role read access to a bucket, and a separate policy explicitly denies access to that bucket. What is the result?</summary>

A. Access is allowed, as allow is more specific · **B. Access is denied — an explicit deny always overrides any allow** · C. Access alternates · D. The policies cancel and default to allow

**Correct: B.** Evaluation is deny-by-default, permissions are the union of allows, and **any explicit deny wins**.
- **A/C/D wrong:** None reflects the evaluation logic.
</details>

<details>
<summary><b>Q11.</b> Which control would you use to block a single malicious source IP address for an entire subnet?</summary>

A. Security group · **B. Network ACL deny rule** · C. WAF only · D. IAM policy

**Correct: B.** NACLs operate at the subnet boundary and support **explicit deny** rules; security groups are allow-only.
- **A wrong:** Security groups cannot express a deny.
- **C wrong:** A WAF handles HTTP, not all subnet traffic.
- **D wrong:** IAM governs API actions, not network traffic.
</details>

<details>
<summary><b>Q12.</b> What advantage does EDR provide over traditional signature-based antivirus?</summary>

**A. It records endpoint behaviour, detects novel attacks, supports investigation, and can isolate a compromised host** · B. It uses less CPU · C. It replaces the need for patching · D. It works only on physical servers

**Correct: A.** EDR shifts from matching known files to observing behaviour and enabling response.
- **B wrong:** EDR is typically heavier.
- **C wrong:** Patching remains essential (3.4, 4.1).
- **D wrong:** EDR is widely deployed on cloud instances.
</details>

<details>
<summary><b>Q13.</b> Which control is required before DLP can be effective?</summary>

A. A WAF · **B. Data classification, so the tool knows what counts as sensitive** · C. An IPS · D. Auto-scaling

**Correct: B.** DLP acts on defined categories — patterns, labels, or fingerprints. Without classification (4.2) there is nothing to enforce.
- **A/C/D wrong:** None is a prerequisite for DLP.
</details>

<details>
<summary><b>Q14.</b> A slow-loris attack holds many connections open with partial HTTP requests. Which category is this, and what mitigates it?</summary>

**A. Application-layer (L7) DDoS — mitigated by a WAF, connection timeouts, and rate limiting** · B. Volumetric — mitigated by more bandwidth · C. Protocol — mitigated by encryption · D. Not an attack

**Correct: A.** Slow-loris is low-volume and application-focused, exhausting server connection capacity rather than bandwidth.
- **B wrong:** It consumes little bandwidth, so capacity does not help.
- **C wrong:** Encryption is irrelevant to connection exhaustion.
- **D wrong:** It is a well-known availability attack.
</details>

<details>
<summary><b>Q15.</b> Which pairing of scope and behaviour is CORRECT?</summary>

A. Security group — subnet level, stateless · **B. Security group — instance level, stateful, allow rules only** · C. NACL — instance level, stateful · D. NACL — allow rules only

**Correct: B.** Security groups attach to instances/interfaces, track state, and support allow rules only.
- **A/C wrong:** The scopes and state are reversed.
- **D wrong:** NACLs support both allow **and deny**.
</details>

<details>
<summary><b>Q16.</b> An organisation must grant a partner's separate cloud account read access to one storage bucket. Which policy type is appropriate?</summary>

A. Identity-based policy in the partner account only · **B. A resource-based policy attached to the bucket, naming the external principal** · C. A permission boundary · D. A NACL rule

**Correct: B.** Resource-based policies attach to the resource and can grant access to principals in other accounts.
- **A wrong:** The resource owner must also grant access.
- **C wrong:** Boundaries cap maximum permissions rather than granting them.
- **D wrong:** NACLs control network traffic, not API authorisation.
</details>

<details>
<summary><b>Q17.</b> Which control type does a backup restore represent?</summary>

A. Preventive · B. Detective · **C. Corrective** · D. Deterrent

**Correct: C — corrective.** It restores state after an incident has occurred (3.3).
- **A/B/D wrong:** Backups neither prevent nor detect an incident.
</details>

<details>
<summary><b>Q18.</b> A vulnerability has no vendor patch. Which control provides temporary protection?</summary>

**A. A WAF rule blocking the exploit pattern — virtual patching, a compensating control** · B. Disabling logging · C. Increasing the scaling maximum · D. Rotating credentials

**Correct: A.** Virtual patching at the WAF blocks exploitation until a real fix ships — the standard zero-day mitigation (4.1).
- **B wrong:** That removes detection capability.
- **C/D wrong:** Neither addresses the vulnerability.
</details>

<details>
<summary><b>Q19.</b> Which requirement is BEST met with an IAM policy condition?</summary>

A. Blocking a malicious IP at the subnet · **B. Allowing an action only when MFA is present and the request comes from the corporate network** · C. Detecting malware on a host · D. Absorbing a volumetric flood

**Correct: B.** Conditions add contextual constraints — MFA presence, source network, time, encryption in transit, tag match.
- **A wrong:** That is a NACL.
- **C wrong:** That is endpoint protection.
- **D wrong:** That is DDoS protection.
</details>

<details>
<summary><b>Q20.</b> Why are cloud instances considered endpoints requiring protection?</summary>

**A. In IaaS the guest OS is the customer's responsibility, so instances need host-level anti-malware, EDR, and host firewalls** · B. Because providers require it contractually · C. Because containers cannot be scanned · D. They are not — cloud instances are protected by the provider

**Correct: A.** The shared responsibility model (1.1) puts the guest OS and everything above it on the customer.
- **B wrong:** It is a security necessity, not a contractual one.
- **C wrong:** Container images are scanned (4.1).
- **D wrong:** The provider secures the hypervisor and below.
</details>

<details>
<summary><b>Q21.</b> Which combination BEST defends a public web application against DDoS?</summary>

**A. Upstream scrubbing, CDN and anycast distribution, WAF rate-based rules, a private origin, and a scaling maximum** · B. A larger instance type alone · C. A host firewall alone · D. Unlimited auto-scaling

**Correct: A.** Layered defence across volumetric, protocol, and application layers, with a cost ceiling.
- **B/C wrong:** Neither addresses distributed floods.
- **D wrong:** That produces economic denial of sustainability.
</details>

<details>
<summary><b>Q22.</b> DLP is configured to log matches without stopping transfers. How is it classified?</summary>

A. Preventive · **B. Detective** · C. Corrective · D. Deterrent

**Correct: B.** In alert-only mode DLP identifies but does not stop the action; in blocking mode the same tool is **preventive**.
- **A wrong:** That describes blocking mode.
- **C/D wrong:** Neither applies.
</details>

<details>
<summary><b>Q23.</b> Which control operates at Layer 7 and inspects request bodies?</summary>

A. Network ACL · B. Security group · **C. WAF** · D. NAT gateway

**Correct: C.** Only the WAF parses HTTP and evaluates URI, headers, parameters, and body content.
- **A/B wrong:** Both are L3–L4 controls.
- **D wrong:** NAT provides outbound internet access.
</details>

<details>
<summary><b>Q24.</b> Which statement about anomaly-based detection is CORRECT?</summary>

A. It has fewer false positives than signature-based · **B. It compares activity against a learned baseline, so it can detect novel attacks but generates more false positives** · C. It cannot detect insider activity · D. It requires no tuning

**Correct: B.** The trade-off is coverage of the unknown against noise — which is why baselines must be established and tuned (4.6).
- **A wrong:** Signature-based produces fewer false positives.
- **C wrong:** Behavioural deviation is well suited to insider detection.
- **D wrong:** It requires significant tuning.
</details>

<details>
<summary><b>Q25.</b> A control is chosen that operates at the wrong layer for the threat. What is the likely outcome?</summary>

A. The threat is still stopped · **B. The threat is not mitigated, and the organisation pays for a control that adds cost and noise without reducing risk** · C. Performance improves · D. Compliance is automatically satisfied

**Correct: B.** Matching the control to the layer at which the threat operates is the core skill this objective tests — a WAF against a volumetric flood, or a firewall against SQL injection, protects nothing.
- **A/C/D wrong:** None follows from a misplaced control.
</details>

---

## 13. PBQ-style drills

### Drill A — Classify the control

| # | Control | Preventive / detective / corrective? |
|---|---|---|
| 1 | IDS | |
| 2 | IPS | |
| 3 | Audit logging | |
| 4 | Backup restore | |
| 5 | WAF | |
| 6 | DLP in alert mode | |
| 7 | DLP in blocking mode | |
| 8 | MFA | |

<details><summary>Answers</summary>

1 → **Detective** · 2 → **Preventive** · 3 → **Detective** · 4 → **Corrective** · 5 → **Preventive** · 6 → **Detective** · 7 → **Preventive** · 8 → **Preventive**
</details>

### Drill B — Threat to control

| # | Threat | Control? |
|---|---|---|
| 1 | Cross-site scripting in a form field | |
| 2 | 300 Gbps UDP flood | |
| 3 | Ransomware executing on an instance | |
| 4 | Employee uploading a customer list to personal storage | |
| 5 | Repeated brute force against a login endpoint | |
| 6 | Need visibility of suspicious traffic without blocking risk | |
| 7 | One IP scanning an entire subnet | |

<details><summary>Answers</summary>

1 → **WAF** · 2 → **Upstream DDoS scrubbing / CDN / anycast** · 3 → **Endpoint protection / EDR** (plus backups to recover — 3.3) · 4 → **DLP** · 5 → **WAF rate-based rules** (and IAM lockout) · 6 → **IDS** · 7 → **NACL deny rule**
</details>

### Drill C — Firewall family

| # | Requirement | SG / NACL / WAF? |
|---|---|---|
| 1 | Explicitly deny one IP range across a subnet | |
| 2 | Allow only the app tier to reach the database instance | |
| 3 | Block SQL injection payloads | |
| 4 | Return traffic must be allowed automatically | |
| 5 | Rules evaluated in numbered order, first match wins | |
| 6 | Geo-block requests from specific countries at L7 | |

<details><summary>Answers</summary>

1 → **NACL** (only NACLs support deny) · 2 → **Security group** · 3 → **WAF** · 4 → **Security group** (stateful) · 5 → **NACL** · 6 → **WAF**
</details>

### Drill D — DDoS layer and mitigation

| # | Attack | Layer + mitigation? |
|---|---|---|
| 1 | DNS amplification saturating the uplink | |
| 2 | SYN flood exhausting the connection table | |
| 3 | HTTP GET flood against a search endpoint | |
| 4 | Slow-loris holding connections open | |
| 5 | Service stayed up but costs tripled | |

<details><summary>Answers</summary>

1 → **Volumetric** → upstream scrubbing, anycast, CDN
2 → **Protocol** → SYN cookies, connection rate limits, load balancer absorption
3 → **Application (L7)** → WAF rate-based rules, caching, bot management
4 → **Application (L7)** → connection timeouts, WAF, rate limiting
5 → **Economic denial of sustainability** → scaling maximum, upstream filtering, budget alerts
</details>

---

## 14. 60-second recall sheet

```text
╔══════════════════════════════════════════════════════════════════════╗
║  4.5 — SECURITY CONTROLS  (4.4 = practices · 4.5 = the CONTROLS)     ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ CONTROL TYPES                                                     ║
║   PREVENTIVE  stops it — firewall/SG/NACL · WAF · ★IPS · IAM ·       ║
║               encryption · MFA · DDoS protection · DLP (block)       ║
║   DETECTIVE   spots it — ★IDS · logging · monitoring · audit trail · ║
║               vuln scanning · DLP (alert)                            ║
║   CORRECTIVE  fixes it — backup restore · patching · quarantine ·    ║
║               auto-remediation · rollback                            ║
║   also: DETERRENT · COMPENSATING (WAF virtual patch) · DIRECTIVE     ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★★ IDS vs IPS — THE #1 PAIR                                         ║
║   IDS  PASSIVE, OUT OF BAND (sees a copy) · ALERTS ONLY · DETECTIVE  ║
║        no latency · false positive = NOISE                           ║
║   IPS  ACTIVE, INLINE · BLOCKS · PREVENTIVE                          ║
║        adds latency · false positive = ★ LEGITIMATE TRAFFIC BLOCKED  ║
║   ★ Deploy new signatures in ALERT mode first, tune, THEN block      ║
║   SIGNATURE = known attacks, few FPs, ⚠ MISSES ZERO-DAYS             ║
║   ANOMALY   = baseline deviation, CATCHES NOVEL, ⚠ more FPs          ║
║   NIDS/NIPS = network · HIDS/HIPS = host                             ║
╠══════════════════════════════════════════════════════════════════════╣
║  ENDPOINT  anti-malware · ★EDR (records BEHAVIOUR, detects novel,    ║
║   ISOLATES the host) · host firewall · disk encryption               ║
║   ⚠ In IaaS the GUEST OS IS YOURS → instances ARE endpoints          ║
║   Bake the agent into the GOLDEN IMAGE                               ║
║  DLP  prevents SENSITIVE DATA LEAVING · in MOTION/at REST/in USE     ║
║   actions: alert · BLOCK · quarantine · encrypt · redact             ║
║   ⚠ REQUIRES DATA CLASSIFICATION FIRST (4.2)                         ║
║   alert mode = DETECTIVE · block mode = PREVENTIVE                   ║
╠══════════════════════════════════════════════════════════════════════╣
║  DDoS — THREE LAYERS                                                 ║
║   VOLUMETRIC (L3/4) fill the pipe · UDP/DNS/NTP amplification        ║
║     ► UPSTREAM SCRUBBING · ANYCAST · CDN (cannot filter at your edge)║
║   PROTOCOL (L3/4)   SYN flood, exhaust state tables                  ║
║     ► SYN cookies · connection rate limits · LB absorption           ║
║   APPLICATION (L7)  HTTP flood, slow-loris, expensive queries        ║
║     ► WAF rate-based rules · bot management · caching · CAPTCHA      ║
║   ★ AUTO-SCALING IS NOT A DEFENCE — it absorbs AND BILLS YOU =       ║
║     "ECONOMIC DENIAL OF SUSTAINABILITY" → SET A MAXIMUM (3.2)        ║
╠══════════════════════════════════════════════════════════════════════╣
║  IAM POLICY  EFFECT · PRINCIPAL · ACTION · RESOURCE · CONDITION      ║
║   DENY BY DEFAULT · ★ EXPLICIT DENY ALWAYS WINS                      ║
║   IDENTITY-based (attached to who) vs RESOURCE-based (attached to    ║
║   what — grants cross-account) · PERMISSION BOUNDARY caps the max    ║
║   ★ CONDITIONS: require MFA / source IP / encryption / tag match     ║
╠══════════════════════════════════════════════════════════════════════╣
║  FIREWALL FAMILY (see 1.3)                                           ║
║   SECURITY GROUP  INSTANCE · ★STATEFUL · ALLOW ONLY · the workhorse  ║
║   NETWORK ACL     SUBNET · ★STATELESS · ALLOW **AND DENY** ·         ║
║                   numbered, lowest first                             ║
║                   ⚠ must allow OUTBOUND EPHEMERAL for replies        ║
║   WAF             ★L7 ONLY · inspects the HTTP PAYLOAD → SQLi, XSS,  ║
║                   OWASP, bots, rate-based rules, virtual patching    ║
║   ★ A FIREWALL CANNOT STOP SQL INJECTION — it arrives on the port    ║
║     you deliberately opened. Only the WAF looks inside.              ║
║   ★ MATCH THE CONTROL TO THE LAYER THE THREAT OPERATES AT.           ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 15. Cross-references

| Related objective | Connection |
|---|---|
| **1.1 Service models** | The guest OS is the customer's in IaaS — which is why instances need endpoint protection |
| **1.3 Cloud networking** | **Security groups, NACLs, and WAF** are taught there in depth; CDN and load balancers underpin DDoS defence |
| **1.8 Cost considerations** | Scaling maximums and budget alerts limit **economic denial of sustainability** |
| **3.1 Observability** | IDS alerts, DLP events, and firewall logs all feed monitoring; alert fatigue is the shared failure mode |
| **3.2 Scaling** | A **maximum capacity** is a security control during a flood, not only a budget setting |
| **3.3 Backup and recovery** | The corrective control after ransomware or destructive attack |
| **4.1 Vulnerability management** | WAF **virtual patching** is the compensating control for an unpatched vulnerability |
| **4.2 Compliance** | **Data classification is the prerequisite for DLP**; control evidence supports audit |
| **4.3 IAM** | Policy structure, explicit deny, least privilege, conditions |
| **4.4 Security best practices** | The **practices**; this objective supplies the **controls** that implement them |
| **4.6 Monitor suspicious activities** | IDS, baselines, and anomaly detection are the inputs to identifying attacks |
| **6.3 Security troubleshooting** | Blocked legitimate traffic, stateless NACL return-path faults, and false positives |

> 🔑 **Carry this forward:** identify the **layer** the threat operates at, then choose the control that sees that layer — and remember **IDS alerts, IPS blocks**.

---

*Source of truth: CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0. Control-type taxonomy (preventive/detective/corrective) is standard security terminology included as organising context. Product names are illustrative; the exam is vendor-neutral.*
