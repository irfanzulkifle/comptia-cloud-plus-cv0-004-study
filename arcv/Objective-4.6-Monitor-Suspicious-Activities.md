---
title: CompTIA Cloud+ CV0-004 — Objective 4.6
objective: "4.6 Given a scenario, monitor suspicious activities to identify common attacks."
domain: "4.0 Security"
weight: "20% of exam"
tags: [Cloud+, CV0-004, Security, Monitoring, Incident, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 4.6
## Monitor Suspicious Activities to Identify Common Attacks

> **Objective statement:** *"Given a scenario, monitor suspicious activities to identify common attacks."*
> **Domain:** 4.0 Security (20% of the exam)
> **What it tests:** The detect-and-recognize half of defense. 4.4 = harden, 4.5 = deploy controls, 4.6 = **watch the logs/metrics and recognize the attack that's happening** (ties to 3.1 observability). You must connect a *symptom* (odd event, baseline deviation, open port) to an *attack type* (exploit, phishing, ransomware, DDoS, cryptojacking, zombie instance, metadata abuse).

---

## SECTION 1 — OBJECTIVE OVERVIEW

**In one breath:** Monitoring suspicious activity = watching telemetry for signs of attack, then naming the attack so you can respond.

**The 4.6 sub-objectives (verbatim from the official exam objectives):**

| # | Sub-objective | What it means |
|---|---------------|---------------|
| 4.6.1 | **Event monitoring** | Collect + watch logs/metrics/flows for anomalies |
| 4.6.2 | **Deviation from the baseline** | "Normal" is known; anything off it is suspect |
| 4.6.3 | **Unnecessary open ports** | Open ports = attack surface; flag the unexpected ones |
| 4.6.4 | **Attack types** | Recognize the common cloud attacks |
| 4.6.4a | ├ Vulnerability exploitation | Attacking known weaknesses |
| 4.6.4a1 | ├─ Human error | Misconfig / mistake enabling breach |
| 4.6.4a2 | └─ Outdated software | Unpatched CVEs (ties to 4.1/4.4) |
| 4.6.4b | ├ Social engineering | Manipulating people |
| 4.6.4b1 | └─ Phishing | Fake message to steal creds |
| 4.6.4c | ├ Malware | Malicious code |
| 4.6.4c1 | └─ Ransomware | Encrypts data, demands ransom |
| 4.6.4d | ├ DDoS | Flood to deny service (ties to 4.5.4) |
| 4.6.4e | ├ Cryptojacking | Hijack compute to mine crypto |
| 4.6.4f | ├ Zombie instances | Forgotten/compromised idle resources |
| 4.6.4g | └ Metadata | Abuse of instance metadata (IMDS/credential theft) |

**Mental model:** `Collect events (4.6.1) → compare to baseline (4.6.2) → spot open ports (4.6.3) → classify the attack (4.6.4 a–g)`. Monitoring feeds the audit trail (4.3.5) and IDS (4.5.3).

---

## SECTION 2 — EXAM KNOWLEDGE

### 4.6.1 — EVENT MONITORING

**Definition:** Continuously collecting and reviewing telemetry to detect malicious or anomalous behavior.

- **Sources:** CloudTrail / Activity Log / Cloud Audit Logs (control plane), VPC flow logs, host logs, app logs, IDS/IPS alerts (4.5.3), EDR (4.5.1).
- **Centralization:** ship everything to a SIEM / log analytics (Splunk, Sentinel, CloudWatch/Log Analytics, Chronicle) for correlation.
- **Key practice:** alert on *high-signal* events — failed logins spikes, new IAM keys, public bucket made, unusual geo/IP, privilege changes.
- **Exam angle:** "We can't see what's happening across accounts" → centralize event monitoring into a SIEM + enable all audit logs.

### 4.6.2 — DEVIATION FROM THE BASELINE

**Definition:** Establish what *normal* looks like (baseline), then treat any significant departure as suspicious.

- **Baseline =** typical CPU, traffic, login times, API call patterns, data egress volume.
- **Deviation examples:** traffic spike at 3am, a normally-quiet instance suddenly calling out to a foreign IP, egress 100x normal (exfil / cryptojacking).
- **How:** anomaly-based detection (4.5.3), metric alarms (3.1), UEBA.
- **Exam angle:** "CPU and egress on one box jumped overnight" → baseline deviation → investigate (cryptojacking/exfil likely).

### 4.6.3 — UNNECESSARY OPEN PORTS

**Definition:** Every listening port is an entry point; ports that shouldn't be open are a red flag.

- **Scan/audit** security groups / NSGs / NACLs / host firewalls for unexpected open ports (22/3389 to 0.0.0.0/0, 6379 Redis, 9200 ES).
- **Why suspicious:** an open port you didn't intend = misconfig (human error, 4.6.4a1) or attacker-opened backdoor.
- **Fix:** least-exposure (4.4), close unused, restrict source, security-group audits.
- **Exam angle:** "Instance has 22 and 3389 open to the world" → unnecessary open port → close/lock down (4.4 + 4.5.6).

### 4.6.4 — ATTACK TYPES

#### Vulnerability exploitation
Attacking a known weakness. Two root causes:
- **Human error** — misconfiguration (public bucket, weak policy, open port) that an attacker walks through.
- **Outdated software** — unpatched CVEs the attacker exploits (ties to 4.1 scanning + 4.4 patching).
- **Recognize:** exploit attempts in IDS/IPS (4.5.3), unexpected vuln scan hits, known-CVE exploit patterns.
- **Exam angle:** "Old unpatched web server gets owned" → vulnerability exploitation via **outdated software**; "open S3 bucket dumped" → via **human error**.

#### Social engineering
Manipulating people (not systems) to gain access.
- **Phishing** — fake email/msg that tricks a user into revealing credentials or clicking a malicious link (leads to credential theft → the attacker uses them).
- **Recognize:** login from a user who "just got a weird email," MFA fatigue, impossible-travel logins.
- **Exam angle:** "User got an email, entered creds on a fake portal, account compromised" → **phishing** (social engineering).

#### Malware
Malicious code executed on a system.
- **Ransomware** — encrypts the victim's data, demands payment for the key. **Mitigation:** backups (3.3) + EDR (4.5.1) + least privilege.
- **Recognize:** mass file renames/encryption activity, ransom note, spike in disk I/O, disabled backups.
- **Exam angle:** "Files suddenly encrypted, ransom note appears" → **ransomware** (malware) → restore from backup (3.3).

#### DDoS
Volumetric/ amplified flood to deny service (same as 4.5.4 attacker side).
- **Recognize:** traffic spike far above baseline (4.6.2), latency, edge saturation.
- **Exam angle:** "Site unreachable, inbound traffic 100x normal" → **DDoS** → DDoS protection + WAF + CDN (4.5.4).

#### Cryptojacking
Attacker hijacks your compute to mine cryptocurrency — stealthy, profits off your bill.
- **Recognize:** unexplained CPU spike, baseline deviation (4.6.2), outbound to mining pools, no business reason for the load.
- **Exam angle:** "CPU pegged at 100% but app traffic is low" → **cryptojacking** → investigate instance, patch, lock down.

#### Zombie instances
Forgotten/orphaned compute still running (or spun up by an attacker) that drains money and expands attack surface.
- **Recognize:** instances with no owner, no recent legit activity, odd tags, idle but billing, or unknown instances in an account.
- **Ties to 3.4 lifecycle** (decommission) + cost (2.5).
- **Exam angle:** "Unnamed instances running, nobody owns them" → **zombie instances** → investigate + decommission.

#### Metadata
Abuse of the **instance metadata service (IMDS)** — especially IMDSv1 — to steal **IAM credentials** assigned to the instance, then act as that role.
- **Attack:** a SSRF bug or open proxy lets an attacker hit `169.254.169.254`, grab the role's temp creds, and escalate.
- **Mitigation:** enforce **IMDSv2** (token-required), block metadata access from app code where unneeded, patch SSRF.
- **Exam angle:** "App with SSRF suddenly assumes the instance role and enumerates the account" → **metadata service abuse** → enable IMDSv2 + fix SSRF.

> **Spot-the-attack cheat:** odd login = phishing/social eng · file encryption = ransomware · CPU spike, no traffic = cryptojacking · traffic spike, unreachable = DDoS · unexpected open port = misconfig/backdoor · unknown idle instance = zombie · creds stolen via app = metadata/SSRF · old software owned = vuln exploitation.



## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 Spotting Cryptojacking via CPU Baseline Deviation
A mid-sized SaaS company runs a fleet of AWS EC2 instances that normally sit at 15–25% CPU during business hours and near-idle overnight. One morning the CloudWatch dashboard shows a cluster of instances pinned at 95–100% CPU continuously, including through the night when application traffic is negligible. Critically, the elevated compute does not correlate with any rise in legitimate request volume from the Application Load Balancer — a classic deviation from the established baseline. The security team pulls process-level telemetry via the CloudWatch agent and finds an unfamiliar long-running process consuming nearly all cores, with outbound connections to a known mining pool domain. This is cryptojacking: an attacker has compromised the instances (often via an exposed API, leaked key, or vulnerable dependency) and is stealing compute to mine cryptocurrency. The tell is the combination of sustained maximal CPU with zero matching business workload. Response: isolate the instances with a restrictive security group, snapshot for forensics, rotate any credentials, terminate the malicious process, and rebuild from a clean AMI. Prevention: baseline alarms on CPU that fire on deviation rather than absolute value, egress filtering to block mining pools, and patching the entry vector.

### 3.2 Catching Phishing-Led Credential Theft via Event Monitoring
An Azure-hosted enterprise uses Microsoft Entra ID sign-in logs and Azure Monitor to watch authentication events. A finance employee receives a convincing email impersonating the IT helpdesk, clicks the link, and enters their credentials on a lookalike portal — a textbook phishing attack. Hours later, Entra sign-in logs record a successful login for that account from an IP geolocated in a country where the company has no staff, followed within minutes by an "impossible travel" alert because the same account had signed in domestically 40 minutes earlier. Event monitoring also flags the attacker enrolling a new MFA device and attempting to access SharePoint finance folders. Because the organization monitors authentication events rather than only network packets, the anomaly surfaces at the identity layer where the compromise actually happened. Response: disable the account, revoke active sessions and tokens, remove the rogue MFA registration, and force a password reset. The root cause is phishing (social engineering), not a software flaw — the credentials were handed over voluntarily under deception. Prevention: phishing-resistant MFA (FIDO2), conditional access policies, user awareness training, and continuous sign-in risk scoring.

### 3.3 Identifying a Zombie Instance Draining Cost
A GCP customer notices their monthly bill creeping upward without any new deployments. Using Cloud Billing reports broken down by project and label, the FinOps team spots a Compute Engine VM in an unused region running 24/7 for months. Investigation shows the instance was spun up for a one-off migration test, never tagged, and never torn down — a "zombie" instance: still billed, still running, but serving no purpose and owned by no active team. Monitoring reveals near-zero network egress, no active connections, minimal but constant CPU, and no logs from any application. Unlike cryptojacking, the resource utilization is low and there is no malicious process — the waste comes from abandonment, not attack. The danger is dual: unnecessary cost and an unpatched, unmonitored attack surface that could later be exploited. Response: confirm ownership through tags and audit logs, snapshot if needed, then stop and delete the instance. Prevention: mandatory resource labeling, automated idle-resource detection, TTL policies on test resources, and regular cost-anomaly reviews. The key distinction on an exam: a zombie instance is a waste/hygiene problem, whereas high sustained CPU with mining traffic is cryptojacking.

### 3.4 Detecting Metadata/SSRF Credential Theft
A public-facing web application on AWS EC2 has a server-side request forgery (SSRF) vulnerability in an image-fetch feature. An attacker crafts a request forcing the server to call the instance metadata endpoint at 169.254.169.254 and, because the instance still uses IMDSv1, retrieves the temporary IAM role credentials without any token requirement. GuardDuty raises an "UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration" finding when those instance-scoped credentials are suddenly used from an external IP outside AWS — a strong signal that role credentials meant to stay on the instance have been stolen. CloudTrail corroborates API calls (S3 listing, new key creation) originating from an unfamiliar source. The attack path is metadata abuse via SSRF, distinct from normal IAM usage where credentials are used from the instance itself. Response: revoke the role's sessions, rotate credentials, patch the SSRF flaw, and audit what the stolen role could access. Prevention: enforce IMDSv2 (token-required, session-oriented, blocks simple SSRF hops), set a hop limit of 1, apply least-privilege IAM roles, and monitor for credential use from unexpected locations.

### 3.5 Recognizing a DDoS via Traffic Deviation + Open-Port Misconfig
An Azure-hosted API normally handles a few thousand requests per minute with predictable regional traffic. Azure Monitor and DDoS Protection metrics suddenly show inbound traffic spiking to millions of packets per second from thousands of distributed source IPs, and legitimate users report the service is unreachable — availability has collapsed. Investigation reveals a network security group left a management port (and the app port) wide open to 0.0.0.0/0, giving attackers a large reachable surface to flood. The signature is a massive deviation in inbound traffic volume combined with degraded or zero service availability — distributed sources distinguish it from a single-source brute force. This is a distributed denial-of-service attack, amplified by an open-port misconfiguration, not cryptojacking (CPU-for-mining) or data theft. Response: enable/tune Azure DDoS Protection, apply rate limiting and WAF rules, tighten NSG rules to expose only required ports to required ranges, and scale or use a CDN to absorb load. Prevention: least-exposure firewall policy, always-on DDoS mitigation, and traffic-baseline alarms that fire on volumetric deviation.

## SECTION 4 — COMPARISON TABLES

### 4.1 Baseline-Normal vs Deviation-Suspicious Signals
| Signal | Baseline (Normal) | Deviation (Suspicious) |
|---|---|---|
| CPU utilization | 15–30%, rises/falls with traffic | Sustained ~100% with no matching workload |
| Inbound traffic | Predictable volume from known regions | Sudden massive spike from many IPs |
| Logins | Expected users, expected geos/times | Impossible travel, new geo, odd hours |
| Egress destinations | Known APIs/services | Mining pools, unknown external IPs |
| Running processes | Known application binaries | Unfamiliar long-running process |
| Credential use | From the instance/user itself | Instance creds used from external IP |
| Cost | Flat, matches deployments | Creeping up with no new deployments |
| Open ports | Only required ports, scoped ranges | Management/app ports open to 0.0.0.0/0 |

### 4.2 Symptom → Attack-Type Mapping
| Symptom | Most Likely Attack/Issue |
|---|---|
| CPU spike + no traffic increase | Cryptojacking |
| Traffic spike + service unreachable | DDoS |
| Files encrypted + ransom note | Ransomware |
| Odd login (new geo, impossible travel) | Credential theft / phishing |
| Unexpected open port | Misconfiguration (exposed attack surface) |
| Unknown idle instance, rising bill | Zombie instance (waste) |
| Credentials stolen via the application | Phishing / SSRF metadata abuse |
| Old/unpatched software compromised | Vulnerability exploitation (outdated software) |

### 4.3 Vulnerability Exploitation: Human Error vs Outdated Software
| Aspect | Human Error | Outdated Software |
|---|---|---|
| Root cause | Misconfiguration, mistake, oversight | Unpatched known vulnerability (CVE) |
| Example | S3 bucket left public, port open to all | Exploited old library / unpatched OS |
| Who introduces it | Operator/admin action | Failure to patch/maintain |
| Detection | Config/posture scanning, audits | Vulnerability scanning, version checks |
| Fix | Correct the configuration | Apply patch / upgrade version |
| Prevention | IaC guardrails, least privilege, review | Patch management, lifecycle policy |

### 4.4 Social Engineering vs Malware
| Aspect | Social Engineering | Malware |
|---|---|---|
| Core mechanism | Manipulating a person | Malicious code execution |
| Example | Phishing email, pretexting, vishing | Ransomware, trojan, miner binary |
| Entry point | Human trust/deception | Software/system exploitation |
| Credential loss | Victim hands them over | Stolen by running code |
| Primary defense | Awareness training, MFA, verification | EDR/AV, patching, least privilege |
| Detection focus | Auth events, impossible travel | Process/file/network telemetry |

### 4.5 Ransomware Mitigation vs Cryptojacking Response
| Aspect | Ransomware Mitigation | Cryptojacking Response |
|---|---|---|
| Goal of attacker | Encrypt data, extort ransom | Steal compute to mine crypto |
| Key symptom | Encrypted files, ransom note | Sustained max CPU, mining egress |
| Immediate action | Isolate, restore from backup | Isolate, kill process, rebuild AMI |
| Core controls | Backups, EDR, least privilege | CPU baseline alarms, egress filtering |
| Recovery | Restore clean backups | Terminate miner, rotate credentials |
| Prevention | Offline/immutable backups, patching | Patch entry vector, block pools |

### 4.6 Metadata IMDSv1 vs IMDSv2
| Aspect | IMDSv1 | IMDSv2 |
|---|---|---|
| Request model | Simple request/response | Session-oriented, token required |
| SSRF resistance | Weak — easy metadata hop | Strong — blocks simple SSRF |
| Token | None | PUT to get token, then use it |
| Hop limit | Not enforced by default | Configurable (recommend 1) |
| Credential theft risk | High via SSRF | Greatly reduced |
| Recommendation | Deprecate/disable | Enforce as required |

## SECTION 5 — EXAM TRAPS

**Trap 1**
- Scenario: An instance shows sustained 100% CPU overnight with no rise in request traffic; a candidate labels it a DDoS.
- Correct: Cryptojacking.
- Why wrong: DDoS is defined by a flood of inbound traffic causing unavailability. Here traffic is flat and CPU is consumed for mining — a compute-theft signature, not a traffic-volume attack.

**Trap 2**
- Scenario: Files are encrypted with a ransom note; a candidate calls it "vulnerability exploitation."
- Correct: Ransomware.
- Why wrong: Vulnerability exploitation names the entry vector, not the outcome. The observed impact — encryption plus extortion — is the defining characteristic of ransomware.

**Trap 3**
- Scenario: An S3 bucket exposed publicly leaks data; a candidate picks "phishing."
- Correct: Human error (misconfiguration).
- Why wrong: No one was deceived. The root cause is an operator leaving a resource misconfigured, which is human error, not social engineering.

**Trap 4**
- Scenario: A scan finds a management port open to 0.0.0.0/0; a candidate immediately calls it a DDoS.
- Correct: Misconfiguration (exposed attack surface).
- Why wrong: An open port is a vulnerability/exposure, not an attack in progress. DDoS requires an actual volumetric traffic flood; the open port only enables risk.

**Trap 5**
- Scenario: CPU and traffic have doubled over three months in a steady, workload-correlated way; a candidate flags an attack.
- Correct: Normal growth (baseline shift), not an attack.
- Why wrong: The rise correlates with legitimate workload and is gradual — that is organic growth. Attacks show deviation uncorrelated with business activity.

**Trap 6**
- Scenario: An idle, untagged VM runs for months with near-zero utilization and rising cost; a candidate calls it cryptojacking.
- Correct: Zombie instance (waste/hygiene).
- Why wrong: Cryptojacking shows high sustained CPU and mining traffic. A zombie instance is low-utilization and abandoned — a cost problem, not active compute theft.

**Trap 7**
- Scenario: Instance IAM role credentials are used to call S3 from the instance itself during normal operation; a candidate flags metadata abuse.
- Correct: Normal IAM usage.
- Why wrong: Credentials used from the instance they belong to is expected. Metadata abuse is indicated when instance credentials appear from an external IP, not local legitimate use.

**Trap 8**
- Scenario: A single source IP hammers the login endpoint trying many passwords; a candidate calls it DDoS.
- Correct: Brute force (credential attack).
- Why wrong: DDoS is distributed and aims at availability. A single-source, targeted password-guessing pattern is brute force, focused on authentication, not flooding.

**Trap 9**
- Scenario: A user is tricked by a phone call into revealing an MFA code; a candidate labels it malware.
- Correct: Social engineering (vishing).
- Why wrong: No malicious code executed. The compromise came through human manipulation (delivery via deception), which is social engineering, not malware.

**Trap 10**
- Scenario: An unpatched web library with a known CVE is exploited; a candidate blames "human error."
- Correct: Outdated software (vulnerability exploitation).
- Why wrong: The root cause is failure to patch a known vulnerability. Human error covers misconfigurations/mistakes, not the exploitation of unmaintained software.

**Trap 11**
- Scenario: A brief, expected traffic spike during a marketing launch; a candidate treats every spike as an attack.
- Correct: Legitimate spike (no malicious deviation).
- Why wrong: Spikes must be judged against baseline and context. A planned, explainable, workload-correlated spike is normal — not all spikes are attacks.

**Trap 12**
- Scenario: SSRF pulls instance credentials because IMDSv1 is in use; a candidate says nothing could have prevented it.
- Correct: Enforce IMDSv2 (with hop limit 1).
- Why wrong: IMDSv2's token-required, session model blocks simple SSRF metadata hops. Missing this mitigation is the key oversight.

**Trap 13**
- Scenario: Event monitoring surfaces a suspicious sign-in; a candidate assumes monitoring automatically blocked the attack like an IDS/IPS.
- Correct: Event monitoring detects/alerts; it does not inherently block.
- Why wrong: Monitoring provides visibility and alerts. Blocking requires inline enforcement (IPS, conditional access) — conflating detection with prevention is the trap.

**Trap 14**
- Scenario: A firewall audit lists port 443 open on a web server; a candidate flags it as an "unnecessary" open port to close.
- Correct: 443 is a required port for the web service — keep it.
- Why wrong: Least-exposure means closing unneeded ports, not required ones. Removing a needed port breaks the service; necessity must be assessed per role.

**Trap 15**
- Scenario: Credentials were stolen after a user entered them on a fake portal; a candidate attributes it to malware.
- Correct: Phishing (social engineering).
- Why wrong: No code stole the credentials — the user was deceived into submitting them. That is phishing, not malware-based theft.


## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

> Exam tip: every Cloud+ PBQ in objective 4.6 gives you a **symptom** and asks you to map it to the **correct attack type**, then pick the **correct response**. Don't fix blindly — name the attack first, then respond.

### 6.1 — CPU Pegged, Traffic Low → Cryptojacking

**Scenario**
CloudWatch alarms fire: an EC2 instance in `prod-app` shows **CPU at 98% for 40 minutes**, but the **application request rate is flat / near zero**. Billing shows a sudden jump in compute cost. No deploy, no cron job you recognise.

**Walkthrough**
1. **Identify the anomaly** — high CPU with *no* matching business traffic is the classic fingerprint. Legit load scales with requests; this doesn't.
2. **Name the attack: Cryptojacking** — an attacker (often via an exposed service, leaked creds, or a vulnerable dependency) is running a **coin-miner** on your compute, stealing CPU cycles.
3. **Confirm** — check `top`/`htop` for unknown processes (e.g. `xmrig`), odd outbound connections to mining pools on ports 3333/8080, and a spike in network egress to unknown IPs.
4. **Respond (isolate → eradicate → recover)**
   - **Isolate:** put the instance in a **security group that blocks all outbound** (or detach it from the network) to stop the miner calling home.
   - **Preserve evidence:** snapshot the volume / grab a memory dump *before* you kill it (for IR).
   - **Eradicate:** terminate the instance and **rebuild from a clean image** (don't just "clean" it — you can't trust a compromised host).
   - **Root-cause:** find the entry point (open port? stolen key? vulnerable image) and close it. Rotate any credentials on that host.
   - **Cost control:** set a **billing alarm** so a future spike pages you fast.

**Bonus question**
Your manager asks to just kill the miner process and keep the server. Why is that the wrong call?
*Answer: a compromised host can't be trusted — the attacker may have installed a **persistence** mechanism (cron, systemd, SSH key) that survives a process kill. Rebuild from a clean image.*

### 6.2 — Login From Two Countries in 10 Minutes → Phishing / Credential Theft

**Scenario**
Your SIEM shows a single user `a.salleh` authenticated from **Kuala Lumpur at 09:02** and from **Lagos at 09:08** — impossible travel. The account then started pulling sensitive objects from storage.

**Walkthrough**
1. **Identify the anomaly** — **impossible travel**: two logins from geographically impossible locations within minutes.
2. **Name the attack: Phishing / Credential Theft** — the user's password (or session token) was likely captured via a phishing page or reused on a breached site; the attacker is now **session hijacking / account takeover**.
3. **Confirm** — check the login IPs, user-agent, and MFA status. Two distinct geolocations + a new device fingerprint = stolen creds.
4. **Respond**
   - **Contain:** **disable / suspend the account** and **revoke active sessions & refresh tokens** immediately.
   - **Force reset:** reset the password **and** invalidate all sessions; **re-enroll MFA** (assume the old MFA is compromised too).
   - **Scope the blast:** audit what the attacker accessed (files, roles, keys) and **rotate any secrets** they could have reached.
   - **User education:** report the phishing source to the user; add the phishing domain to email filters.
   - **Prevent:** enforce **MFA**, **conditional access / geo-blocking**, and **impossible-travel detection** in your IdP.

**Bonus question**
The attacker used a valid session token, not the password. Does resetting the password stop them?
*Answer: No — **revoke the session/refresh tokens** too. A live token bypasses password checks; password reset alone leaves the session valid.*

### 6.3 — Unknown Instances, No Owner → Zombie Instances

**Scenario**
A cost review finds **12 running VM instances** in the account that **no team claims**. They've been up 200+ days, have no tags/owner, and low-but-nonzero CPU. They weren't in any approved project list.

**Walkthrough**
1. **Identify the anomaly** — running resources with **no owner tag, no business justification, and no recent legitimate activity** = **zombie (orphaned) instances**.
2. **Name the issue: Zombie / orphaned cloud resources** — often leftovers from terminated projects, or **attacker-provisioned resources** (e.g. a compromised key used to spin up mining boxes).
3. **Confirm** — cross-reference against the **CMDB / approved inventory**. Check billing, **VPC/network placement**, public IPs, and outbound traffic. If they're talking to the internet with no owner, treat as **possible compromise**.
4. **Respond (decommission safely)**
   - **Tag & quarantine:** apply a `review` tag and move to an isolated network segment / apply a deny-all SG.
   - **Notify:** alert the instance's (absent) owner list and the account owner; allow a grace period.
   - **Snapshot for evidence** if any look suspicious (possible attacker boxes).
   - **Decommission:** stop → snapshot → terminate; remove associated disks, public IPs, and IAM roles.
   - **Prevent recurrence:** enforce **mandatory tagging policies** (AWS Tag Policies / Azure Policy), **budget alerts**, and **unused-resource sweeps** on a schedule.

**Bonus question**
Before terminating, one "zombie" shows steady outbound traffic to an unknown IP. What do you do differently?
*Answer: treat it as a **potential compromise** — isolate first, **preserve a snapshot/forensic image**, and loop in IR before deletion so you don't destroy evidence.*

### 6.4 — App With SSRF Now Assumes Instance Role → Metadata Abuse (IMDSv2)

**Scenario**
A web app had an **SSRF vulnerability**. Logs now show the app fetching `http://169.254.169.254/latest/meta-data/iam/security-credentials/<role>` and the role's temp keys appearing in external requests. The app is now **assuming the instance role** beyond its intended scope.

**Walkthrough**
1. **Identify the anomaly** — an app reaching the **instance metadata endpoint (169.254.169.254)** and exfiltrating **IAM credentials** is **metadata abuse** via SSRF.
2. **Name the attack: Metadata / IMDS Abuse (SSRF → credential theft)** — the attacker chained an **SSRF** bug to read the **instance profile's temporary keys**, effectively escalating to whatever IAM role the instance holds.
3. **Confirm** — CloudTrail shows API calls from the instance's role originating from outside your network, or the app's logs contain `169.254.169.254` requests.
4. **Respond**
   - **Rotate:** **immediately deactivate/rotate the instance role's access keys / revoke the session** so the stolen creds die.
   - **Patch the SSRF:** sanitise user-supplied URLs, block internal IP ranges, validate redirects.
   - **Enable IMDSv2 (the key fix):** set the instance metadata service to **IMDSv2 (required, token-based)** so a simple GET (SSRF) can't read credentials without a session token.
     - AWS CLI: `aws ec2 modify-instance-metadata-options --instance-id <id> --http-tokens required --http-endpoint enabled`
   - **Least privilege:** tighten the instance role to **only the permissions the app truly needs**.
   - **Network defence:** consider a **proxy / metadata firewall** and restrict metadata access at the subnet level where supported.

**Bonus question**
Why doesn't IMDSv1 protect against this, and what exactly does IMDSv2 add?
*Answer: IMDSv1 needs **no token** — any process that can reach the metadata IP (including via SSRF) can read creds. **IMDSv2 requires a PUT to get a session token first**, which an SSRF that only does simple GETs typically can't perform, blocking the theft.*

### 6.5 — Traffic 100× Baseline, Site Down → DDoS

**Scenario**
At 14:00 the load balancer shows **inbound traffic ~100× the normal baseline**. The site is **unreachable / 5xx-ing**. Source IPs are **highly distributed** across many countries; no single IP dominates.

**Walkthrough**
1. **Identify the anomaly** — a **massive, sudden, distributed spike** in traffic with **geographically spread sources** and **service degradation** = **DDoS (Distributed Denial of Service)**.
2. **Name the attack: DDoS** — typically **Layer 3/4 (volumetric / SYN flood)** or **Layer 7 (HTTP flood / app-layer)** depending on the signature. Distributed sources rule out a single bad client.
3. **Confirm** — check LB/edge metrics: request rate, bandwidth, connection counts, and **top-talkers**. Distributed low-and-slow = L7; huge bandwidth from many IPs = volumetric.
4. **Respond (enable protection + mitigate)**
   - **Enable cloud DDoS protection** — turn on the provider's managed scrubbing (e.g. **AWS Shield**, **Azure DDoS Protection**, **GCP Cloud Armor**).
   - **Absorb / filter at the edge:** engage **CDN / WAF**; enable **rate-limiting** and **geo-fencing** where appropriate.
   - **Scale out:** add capacity / auto-scale so legitimate users survive.
   - **Blackhole / scrub:** for volumetric attacks, route through the provider's **scrubbing centre**; drop spoofed/odd packets.
   - **Post-incident:** keep protection enabled, tune **baseline alarms** so the next event pages earlier.

**Bonus question**
The traffic is all valid-looking HTTP GETs to your login page from thousands of IPs. Which DDoS type is this and why is a WAF/rate-limit better than just "more bandwidth"?
*Answer: **Layer 7 (application-layer) HTTP flood**. More bandwidth doesn't help because each request is "legitimate" at the packet level — you need **L7 filtering / WAF / rate-limiting / bot detection** to separate real users from the flood.*

---

## SECTION 7 — MEMORY AIDS

> Quick-recall pegs for objective 4.6. Each maps a **symptom → attack**. Bold = the term the exam wants.

### 4.6.2 — Baseline Deviation
- **Baseline = normal.** Anything that **deviates** (CPU, traffic, logins, cost) is your first signal.
- Peg: *"If it's weird vs. yesterday, it's a signal — not noise."*
- Set **alarms on deviation**, not just on absolute thresholds. A 5% CPU box jumping to 60% is an alert even if 60% "looks fine" on a big box.

### 4.6.3 — Open-Port Red-Flag
- **Every open port = attack surface.** An open port with **no business reason** is a red flag.
- Peg: *"Open port + no owner = trouble."*
- Audit regularly: **unused ports closed**, only required services exposed, **no RDP/SSH (22/3389) open to 0.0.0.0/0**. Public admin ports are the #1 initial-access mistake.

### 4.6.4a — Vulnerability Exploit (human-error vs outdated)
- **Two root causes:**
  - **Human error** — misconfig (open bucket, weak IAM, default creds). Fix = **config review / guardrails**.
  - **Outdated** — unpatched software / known CVE. Fix = **patch / upgrade**.
- Peg: *"Exploit = either you mis-set it (human) or you didn't patch it (outdated)."*

### 4.6.4b — Phishing
- **Symptom:** unexpected login, **impossible travel**, credential prompts, weird links.
- **Attack:** steals creds/sessions → **account takeover**.
- Peg: *"Phish = they steal the key, not the door."* Defence: **MFA + user training + email filtering**.

### 4.6.4c — Ransomware (backup!)
- **Symptom:** files encrypting, **extensions changing**, ransom note, mass file renames.
- **Attack:** encrypts your data, demands payment.
- Peg: *"RANSOMWARE → BACKUP!"* The recovery answer is **always restore from clean, offline/immutable backups** — never pay. Isolate infected hosts first.

### 4.6.4e — Cryptojacking (CPU-no-traffic)
- **Symptom:** **high CPU but low/no business traffic** + surprise compute bill.
- **Attack:** attacker runs a **miner** on your compute.
- Peg: *"CPU up, traffic flat = miner."* Isolate outbound, snapshot, rebuild clean, rotate creds.

### 4.6.4g — Metadata / IMDSv2
- **Symptom:** app hits `169.254.169.254`, IAM creds leak, SSRF present.
- **Attack:** **metadata abuse** → instance-role credential theft.
- Peg: *"SSRF + 169.254.169.254 = IMDSv2 NOW."* Enable **IMDSv2 (token-required)**, least-privilege the role, fix the SSRF.

### ASCII — Symptom → Attack Decision Tree

```
                         SUSPICIOUS ACTIVITY OBSERVED
                                    |
                -------------------------------------------------
                |                |                |             |
         CPU HIGH?        LOGIN ANOMALY?     TRAFFIC SPIKE?   OPEN PORT /
                |                |                |           UNKNOWN RESOURCE?
    ------------+---      -------+------     -----+------        |
    |               |      |            |     |          |       |
low traffic     normal   2 countries  new   100x base   site    no owner /
    |          traffic   in 10 min   device  distributed down   no business
    |               |      |            |     |          |       |
 CRYPTO-        (OK /    PHISHING /  (phish  DDoS        DDoS   ZOMBIE /
 JACKING        benign)  CRED THEFT  or      (volumetric (app-  ORPHAN
 (4.6.4e)                   (4.6.4b)  session            layer)  (decom-
    |                                 hijack)   |          |     mission)
    |                                          |          |       |
 isolate,                                   enable     WAF +    tag,
 snapshot,                                SHIELD/     rate-     quarantine,
 rebuild,                                 ARMOR +    limit,    snapshot,
 rotate                                    WAF       scale     terminate
                                              |          |
                                          (6.5)     (6.5 L7)
    |
 (6.1)

 APP HITS 169.254.169.254 + SSRF?
    |
 METADATA ABUSE / IMDS  -> enable IMDSv2, rotate role, fix SSRF  (4.6.4g / 6.4)

 UNPATCHED / MISCONFIG EXPLOITED?
    |
 VULN EXPLOIT -> patch (outdated) OR fix config (human error)  (4.6.4a)

 FILES ENCRYPTING / RANSOM NOTE?
    |
 RANSOMWARE -> ISOLATE, RESTORE FROM BACKUP (never pay)  (4.6.4c)
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

> Map each 4.6 control to the **AWS / Azure / GCP** tool so you can answer "which service does X?" on the exam.

| Control / Capability | AWS | Azure | GCP |
|---|---|---|---|
| **Event monitoring** (collect logs & events) | **CloudTrail** (API), **CloudWatch Logs**, **VPC Flow Logs** | **Azure Monitor** (Activity + Diagnostics logs), **NSG Flow Logs** | **Cloud Audit Logs** (Admin/Data/System), **VPC Flow Logs** |
| **Baseline / alarms** (deviation detection) | **CloudWatch Metrics + Alarms**, **CloudWatch Anomaly Detection** | **Azure Monitor Metrics + Alerts**, **Log Analytics** | **Cloud Monitoring** (Metrics, Alerting, Anomaly detection) |
| **Open-port audit** (attack-surface review) | **Security Groups** + **AWS Config** + **Inspector** + **Trusted Advisor** | **NSGs** + **Azure Policy** + **Defender for Cloud** | **VPC Firewall Rules** + **Security Command Center** |
| **Cryptojacking detection** (CPU-no-traffic) | CloudWatch (CPU vs request-rate), **GuardDuty** (crypto-mining findings) | Azure Monitor + **Defender for Cloud** (crypto-mining) | Cloud Monitoring + **SCC** (suspicious compute) |
| **Phishing / credential-theft detection** | **GuardDuty** (credential access), **IAM Access Analyzer**, CloudTrail impossible-travel | **Entra ID Identity Protection** (impossible travel, risky sign-ins) | **SCC** + **Context-Aware Access**, Audit Logs |
| **Ransomware detection** | GuardDuty (data exfil/encrypt), **Macie**, CloudWatch file-change alarms | Defender for Cloud + **MDE**, backup alerts | SCC + **Backup** anomaly, Cloud Monitoring |
| **Metadata / IMDS protection** | **IMDSv2** (`modify-instance-metadata-options --http-tokens required`); block 169.254.169.254 at firewall | **Azure IMDS** hardening; **Managed Identity** (no creds in metadata); NSG/service-tag limits | **GCE metadata** (`disable-legacy-endpoints`); **OS Login** / service accounts; block `metadata.google.internal` |
| **DDoS protection** | **AWS Shield** (Standard/Advanced) + **WAF** + **Route 53** | **Azure DDoS Protection** (Standard) + **WAF / Front Door** | **Cloud Armor** + **Cloud CDN** + **VPC edge** |
| **Zombie / orphaned resource discovery** | **AWS Config** + **Cost Explorer** + **Resource Groups/Tags** | **Azure Resource Graph** + **Cost Management** + Policies | **Asset Inventory (SCC)** + **Billing Reports** |

### Cross-Provider Reminders (provider-agnostic, exam-safe)
- **Baseline first, always.** You can't spot deviation without a baseline — monitor *normal* before you can flag *abnormal* on any platform.
- **Tag everything / enforce ownership.** Mandatory tagging + org policies catch **zombie instances** everywhere (AWS Tag Policies, Azure Policy, GCP Org Policies).
- **Least privilege the identity.** Whether it's an **IAM role, Managed Identity, or service account**, scope it to what's needed — limits blast radius of **metadata abuse** and **credential theft**.
- **MFA + impossible-travel detection** is the universal answer to **phishing / credential theft** across all three clouds.
- **Backups are the ransomware answer** — immutable/offline backups (AWS Backup, Azure Backup, GCP Backup) beat paying the ransom on every platform.
- **IMDS/metadata hardening is non-negotiable** — require token-based metadata (IMDSv2 / disable legacy endpoints) so **SSRF can't steal instance creds**.
- **Enable managed DDoS protection + WAF at the edge** on all providers; volumetric needs scrubbing, L7 needs rate-limiting/WAF.
- **Close unused ports** and never expose admin ports (22/3389) to the world — consistent across AWS SG/NACL, Azure NSG, GCP Firewall.


## SECTION 9 — INTERVIEW KNOWLEDGE

### Conceptual Q&A

**Q: What is event monitoring in a cloud security context, and why does it matter for objective 4.6?**
A: Event monitoring is the continuous collection, correlation, and analysis of logs, metrics, and telemetry (from workloads, control planes, network flows, and identity systems) to detect abnormal or malicious activity. It matters because most cloud attacks are invisible without instrumentation — you cannot troubleshoot or contain what you cannot see. Effective monitoring feeds a SIEM/observability pipeline that turns raw events into actionable alerts.

**Q: What is a baseline, and what does "baseline deviation" tell you?**
A: A baseline is a documented profile of "normal" — typical CPU/memory/network utilization, expected login patterns, standard open ports, normal API call rates, and usual data-egress volumes. A baseline deviation is any statistically or operationally significant departure from that norm. Deviations are the primary early-warning signal for compromise: a sudden CPU spike, off-hours logins, new listening ports, or a surge in outbound traffic all indicate something changed and warrants investigation.

**Q: Why are open ports a security concern, and how do you evaluate them?**
A: Every open (listening) port is a potential entry point. Unexpected or unnecessary open ports expand the attack surface and often indicate misconfiguration, shadow services, or an implanted backdoor/C2 channel. You evaluate them by comparing discovered ports (via scans like nmap, cloud security-group audits, or netstat/ss) against the documented baseline of ports the service is *supposed* to expose, then closing or firewalling anything unexpected.

**Q: Distinguish vulnerability exploitation, human error, and outdated/unpatched systems as root causes.**
A: Vulnerability exploitation is an attacker leveraging a known or zero-day flaw (e.g., unpatched CVE) to gain access or execute code. Human error is misconfiguration or mistake (public S3 bucket, exposed credential, over-permissive IAM). Outdated/unpatched systems are the standing condition that *enables* exploitation — they are the fuel; the exploit is the match. Most breaches chain these: an outdated system with a known CVE exploited because patching was neglected (human/process error).

**Q: What is the difference between malware and ransomware?**
A: Malware is the umbrella term for any malicious software (viruses, trojans, worms, spyware, rootkits). Ransomware is a specific malware subtype that encrypts (or exfiltrates and threatens to leak) data and demands payment for restoration. Ransomware symptoms are distinctive: mass file-rename with odd extensions, ransom notes, and a sharp spike in file I/O.

**Q: What is cryptojacking and how does it differ from a zombie/botnet host?**
A: Cryptojacking is the unauthorized use of your compute to mine cryptocurrency — the attacker profits from your CPU/GPU cycles and cloud bill. A zombie is a compromised host conscripted into a botnet, taking commands from a C2 server to perform attacks (often DDoS or spam). Cryptojacking's signature is sustained near-100% CPU with mining-pool connections; a zombie's signature is beaconing to C2 and participation in coordinated outbound attacks.

**Q: What are metadata/IMDS attacks?**
A: Cloud instances expose an Instance Metadata Service (IMDS) at a link-local address (169.254.169.254) that returns credentials, config, and identity tokens. Attackers abuse it — typically via SSRF against a vulnerable app — to pull temporary IAM credentials and pivot. IMDSv2 mitigates this by requiring a session token (PUT request) and hop-limit enforcement, defeating simple SSRF-based GET retrieval.

**Q: What is a DDoS attack and what does it look like in monitoring?**
A: A Distributed Denial of Service floods a target with traffic from many sources to exhaust bandwidth, connections, or application resources. In monitoring it appears as a massive baseline deviation: sudden inbound traffic spike, connection-table saturation, latency/timeouts, and often geographically diverse source IPs.

### Scenario Questions

**Scenario 1 — Symptom to attack:** "A production VM's CPU has sat at 98% for three days, cloud spend jumped, and outbound connections to `pool.minexmr.com:3333` appear in flow logs. No legitimate batch job is running." *Name the attack and first response.*
A: Cryptojacking. First response: isolate the instance (security-group lockdown), snapshot for forensics, kill the miner process, rotate any credentials on the host, then find the initial access vector (often an exposed API, unpatched CVE, or leaked key).

**Scenario 2 — Symptom to attack:** "Users report files renamed to `.locky`, a `README_DECRYPT.txt` appears in every directory, and disk I/O spiked overnight." *Name the attack and immediate priority.*
A: Ransomware. Immediate priority: isolate to stop lateral encryption, do NOT pay reflexively, identify strain, and restore from known-good immutable/offline backups after confirming the intrusion vector is closed.

**Scenario 3 — Design a monitoring baseline:** "You inherit a new three-tier web app with no monitoring. Design a security monitoring baseline."
A: (1) Inventory assets and expected open ports per tier (443 on LB, app-port internal only, DB port internal only). (2) Capture normal metrics over 2–4 weeks: CPU/mem/network per tier, request rate, error rate, DB query volume, egress volume. (3) Establish identity baselines: normal login times/geos, MFA usage, API call patterns. (4) Enable and centralize logs (flow logs, control-plane/audit logs, app logs) into a SIEM. (5) Define deviation thresholds and alerts (CPU sustained >X%, new listening ports, off-hours admin logins, egress >Y). (6) Enforce IMDSv2 and alert on 169.254.169.254 access anomalies. (7) Review and tune thresholds monthly.

**Scenario 4 — Symptom to attack:** "An internal server suddenly opens port 6667 (IRC), beacons to an unfamiliar external IP every 60 seconds, and participates in bursts of outbound SYN floods toward a third party." *Name the attack.*
A: The host is a zombie in a botnet — the IRC/beaconing is C2, and the outbound SYN floods indicate it's being used as a DDoS bot.

### "Explain It Simply" Prompts

- **Baseline deviation:** "It's like knowing your house's normal electric bill. If it suddenly triples, you don't need to see the thief — the number tells you something's plugged in that shouldn't be."
- **IMDS attack:** "The metadata service is a valet who hands out your car keys to anyone who asks nicely. IMDSv2 makes the valet demand a password token first."
- **Cryptojacking:** "Someone secretly running their space heater on your electricity — your bill soars and your machine runs hot, but nothing looks 'broken.'"
- **Zombie/botnet:** "Your computer got hypnotized and now takes orders from a stranger, joining a mob to attack someone else."
- **Phishing/social engineering:** "Instead of picking the lock, the attacker knocks and convinces you to open the door yourself."

## SECTION 10 — FLASHCARDS

**Q:** What is event monitoring? **A:** Continuous collection, correlation, and analysis of logs/metrics/telemetry to detect abnormal or malicious activity.

**Q:** What is a security baseline? **A:** A documented profile of normal behavior (utilization, logins, ports, API rates, egress) used as a reference for detecting anomalies.

**Q:** Define baseline deviation. **A:** A significant departure from established normal behavior, serving as an early indicator of compromise or misconfiguration.

**Q:** Why are open ports a risk? **A:** Each listening port expands the attack surface and may indicate a shadow service, misconfiguration, or backdoor/C2 channel.

**Q:** What tool audits open ports on a host? **A:** nmap (external scan) or netstat/ss (local); in cloud, also security-group/NSG rule audits.

**Q:** What is the SIEM's role in event monitoring? **A:** It centralizes, correlates, and alerts on events from many sources to turn raw logs into actionable detections.

**Q:** What does "observability" add beyond monitoring? **A:** Logs, metrics, and traces together let you understand *why* a system behaves abnormally, not just *that* it does.

**Q:** Name three data sources for a cloud monitoring baseline. **A:** VPC/flow logs, control-plane/audit logs, and application/host logs (plus identity logs).

**Q:** What is vulnerability exploitation? **A:** An attacker leveraging a known or zero-day flaw to gain access or execute code.

**Q:** What is human error as an attack root cause? **A:** Misconfiguration or mistake (public buckets, exposed keys, over-permissive IAM) that creates an exposure.

**Q:** Why are outdated/unpatched systems dangerous? **A:** They carry known exploitable CVEs, making them prime targets for automated exploitation.

**Q:** What chains outdated systems and exploitation? **A:** An unpatched known CVE is the fuel; the exploit is the trigger — patch management breaks the chain.

**Q:** Define social engineering. **A:** Manipulating people into revealing information or performing actions that compromise security.

**Q:** What is phishing? **A:** A social-engineering attack using fraudulent messages (email/SMS) to steal credentials or deliver malware.

**Q:** What is spear phishing? **A:** A targeted phishing attack tailored to a specific individual or role using personalized details.

**Q:** Symptom: employee entered credentials on a fake login page. What attack? **A:** Phishing (credential-harvesting social engineering).

**Q:** What is malware? **A:** Any malicious software — viruses, trojans, worms, spyware, rootkits, ransomware.

**Q:** What is ransomware? **A:** Malware that encrypts or exfiltrates data and demands payment for restoration or non-disclosure.

**Q:** Ransomware symptoms? **A:** Mass file renames with odd extensions, ransom notes, and a spike in disk I/O.

**Q:** Best defense against ransomware data loss? **A:** Immutable/offline, tested backups plus network segmentation to limit spread.

**Q:** What is a DDoS attack? **A:** A distributed flood of traffic from many sources overwhelming a target's bandwidth, connections, or app resources.

**Q:** DDoS monitoring signature? **A:** Sudden inbound traffic spike, connection saturation, latency/timeouts, geographically diverse sources.

**Q:** Primary cloud DDoS mitigation? **A:** Managed DDoS protection/scrubbing, CDN/WAF, rate limiting, and autoscaling absorption.

**Q:** What is cryptojacking? **A:** Unauthorized use of your compute to mine cryptocurrency, profiting from your cycles and cloud bill.

**Q:** Cryptojacking symptoms? **A:** Sustained near-100% CPU/GPU, elevated cloud spend, and connections to mining pools.

**Q:** What is a zombie host? **A:** A compromised machine conscripted into a botnet that executes commands from a C2 server.

**Q:** What is a botnet? **A:** A network of zombie hosts controlled by an attacker, often used for DDoS, spam, or brute-forcing.

**Q:** Zombie/botnet monitoring signature? **A:** Regular beaconing to C2, unexpected open ports (e.g., IRC), and coordinated outbound attacks.

**Q:** What is the Instance Metadata Service (IMDS)? **A:** A link-local endpoint (169.254.169.254) returning instance config, identity, and temporary credentials.

**Q:** How is IMDS abused? **A:** Via SSRF, an attacker forces a vulnerable app to fetch IAM credentials from the metadata endpoint and pivots.

**Q:** How does IMDSv2 defend against metadata attacks? **A:** It requires a session token (PUT) and enforces a hop limit, defeating simple SSRF GET retrieval.

**Q:** What address should you alert on for metadata abuse? **A:** 169.254.169.254 (unexpected access from application contexts).

**Q:** What is metadata (as an attack surface) beyond IMDS? **A:** Exposed configuration, tags, user-data scripts, and secrets that leak sensitive info if accessible.

**Q:** Symptom: SSRF in app followed by AWS API calls from a new IP using instance-role creds. What attack? **A:** IMDS/metadata credential theft via SSRF.

**Q:** Symptom: new listening port appears with no change ticket. What technique detects it? **A:** Open-port monitoring / baseline deviation on listening ports.

**Q:** Symptom: admin login at 3 a.m. from a foreign country. What detects it? **A:** Baseline deviation on identity/login patterns.

**Q:** Symptom: outbound egress volume 20x normal at night. Likely category? **A:** Data exfiltration — a baseline deviation warranting investigation.

**Q:** First action when a host shows cryptojacking indicators? **A:** Isolate, snapshot for forensics, kill the miner, rotate credentials, find the entry vector.

**Q:** What is the difference between a zombie and cryptojacking? **A:** Zombie = takes C2 orders (often for DDoS); cryptojacking = steals compute to mine coin.

**Q:** Why review baselines periodically? **A:** Normal behavior drifts as apps evolve; stale thresholds cause false positives and missed detections.

## SECTION 11 — PRACTICE QUESTIONS

1. A cloud VM shows sustained 99% CPU for days, a rising bill, and outbound connections to a known mining pool. What attack is this?
   A. Ransomware
   B. Cryptojacking
   C. DDoS
   D. Phishing
   Answer: B
   Explanation: Sustained max CPU plus mining-pool traffic and cost spikes are the classic cryptojacking signature.

2. Files across a server are renamed with a `.crypt` extension and a `README_DECRYPT.txt` appears in every folder. What attack?
   A. Cryptojacking
   B. Zombie/botnet
   C. Ransomware
   D. Metadata theft
   Answer: C
   Explanation: Mass renaming and ransom notes are hallmark ransomware indicators.

3. An application vulnerable to SSRF is used to retrieve temporary credentials from 169.254.169.254. What attack category is this?
   A. Phishing
   B. IMDS/metadata credential theft
   C. DDoS
   D. Human error only
   Answer: B
   Explanation: SSRF against the instance metadata endpoint to steal IAM creds is a metadata/IMDS attack.

4. Which control most directly mitigates SSRF-based metadata credential theft?
   A. Enabling IMDSv2 with hop-limit enforcement
   B. Increasing instance size
   C. Rotating SSH keys weekly
   D. Enabling verbose logging
   Answer: A
   Explanation: IMDSv2 requires a session token and enforces hop limits, blocking simple SSRF GET retrieval.

5. Monitoring shows a new listening port (6667) opened on an internal server with no change ticket, plus periodic beaconing to an external IP. What attack?
   A. Ransomware
   B. Zombie/botnet host
   C. Cryptojacking
   D. Phishing
   Answer: B
   Explanation: An unexpected IRC-style port and regular beaconing indicate C2 — the host is a botnet zombie.

6. Inbound traffic suddenly spikes 50x from thousands of diverse IPs, saturating connections and causing timeouts. What attack?
   A. DDoS
   B. Ransomware
   C. Metadata theft
   D. Cryptojacking
   Answer: A
   Explanation: A distributed traffic flood exhausting resources is a DDoS attack.

7. An employee enters corporate credentials into a spoofed login page linked from an email. What attack?
   A. DDoS
   B. Phishing (social engineering)
   C. Cryptojacking
   D. Zombie
   Answer: B
   Explanation: Fraudulent messages tricking users into revealing credentials is phishing.

8. A public storage bucket exposed customer data due to a misconfigured ACL. What root cause?
   A. Vulnerability exploitation
   B. Human error/misconfiguration
   C. DDoS
   D. Ransomware
   Answer: B
   Explanation: A misconfigured ACL is human error, not exploitation of a software flaw.

9. Attackers exploited an unpatched CVE on an internet-facing server running end-of-life software. Which two categories apply best?
   A. Phishing and DDoS
   B. Vulnerability exploitation enabled by outdated/unpatched systems
   C. Cryptojacking and metadata
   D. Zombie and ransomware
   Answer: B
   Explanation: The unpatched EOL system (outdated) provided the exploitable flaw (vulnerability exploitation).

10. Which is the best early-warning signal that a workload has been compromised, before you know the specific attack?
    A. A vendor advisory email
    B. A significant baseline deviation (CPU, ports, logins, or egress)
    C. A monthly cost report
    D. A successful backup completion
    Answer: B
    Explanation: Deviations from normal are the primary early indicator regardless of the eventual attack type.

11. An admin account logs in at 3:00 a.m. from a country the company doesn't operate in, then creates new IAM users. What technique surfaces this?
    A. Open-port scanning
    B. Baseline deviation on identity/login patterns
    C. Ransomware sandboxing
    D. DDoS scrubbing
    Answer: B
    Explanation: Off-hours, out-of-geo logins are identity baseline deviations indicating account compromise.

12. Outbound data egress jumps to 20x the normal nightly volume to an unfamiliar external endpoint. What should you suspect?
    A. Normal autoscaling
    B. Data exfiltration flagged by baseline deviation
    C. IMDSv2 enforcement
    D. A routine backup
    Answer: B
    Explanation: A large egress deviation is a strong exfiltration indicator warranting investigation.

13. Which tool set is most appropriate to inventory and validate open ports against a documented baseline?
    A. nmap plus security-group/NSG audit
    B. A password manager
    C. A CDN
    D. A transcoding service
    Answer: A
    Explanation: Port scanning plus cloud firewall-rule review reveals unexpected listening services.

14. A worm spreads laterally, installing spyware that logs keystrokes across many hosts. What umbrella term applies?
    A. Phishing
    B. Malware
    C. DDoS
    D. Baseline deviation
    Answer: B
    Explanation: Worms and spyware are types of malware — the umbrella category for malicious software.

15. Which practice best limits ransomware's ability to destroy your data?
    A. Paying the ransom promptly
    B. Immutable/offline tested backups plus segmentation
    C. Increasing CPU quota
    D. Disabling all logging
    Answer: B
    Explanation: Recoverable, tamper-proof backups and segmentation neutralize ransomware's leverage and spread.

16. A finance user receives a highly personalized email impersonating the CFO requesting an urgent wire. What attack?
    A. Spear phishing
    B. Cryptojacking
    C. Zombie
    D. Metadata theft
    Answer: A
    Explanation: A targeted, personalized social-engineering message is spear phishing.

17. Flow logs show an application server repeatedly requesting 169.254.169.254 shortly after user-supplied URLs are processed. What is the concern?
    A. Normal health checks
    B. SSRF being used to reach the metadata service
    C. A CDN cache miss
    D. DNS propagation delay
    Answer: B
    Explanation: App-context requests to the metadata IP after processing user URLs indicate SSRF-driven metadata access.

18. You are building a new monitoring baseline. Which step comes first?
    A. Set alert thresholds arbitrarily
    B. Inventory assets and capture normal metrics over a representative period
    C. Buy a bigger SIEM license
    D. Disable historical logs
    Answer: B
    Explanation: You cannot define deviation without first establishing documented "normal."

19. A host beacons to a C2 server every 60 seconds and joins coordinated outbound SYN floods against a third party. What role is the host playing?
    A. A ransomware victim
    B. A zombie participating in a botnet DDoS
    C. A metadata proxy
    D. A phishing relay only
    Answer: B
    Explanation: C2 beaconing plus coordinated outbound floods means the host is a DDoS botnet zombie.

20. Which combination best distinguishes cryptojacking from a zombie host in monitoring?
    A. Cryptojacking = mining-pool traffic + max CPU; zombie = C2 beaconing + coordinated outbound attacks
    B. Both show identical signatures and cannot be told apart
    C. Cryptojacking = ransom notes; zombie = encrypted files
    D. Cryptojacking = inbound flood; zombie = high CPU only
    Answer: A
    Explanation: Cryptojacking is defined by mining connections and sustained CPU; zombies are defined by C2 control and participation in attacks.

## SECTION 12 — EXAM PRIORITY

| Rank | 4.6 Sub-objective | Exam Likelihood | Why |
|------|-------------------|-----------------|-----|
| 1 | Symptom → attack-type identification (malware/ransomware, DDoS, cryptojacking, zombie) | High | The exam loves scenario stems that describe symptoms and ask you to name the attack; highest question density. |
| 2 | Baseline deviation as detection technique | High | Central troubleshooting concept — nearly every monitoring scenario hinges on spotting departure from normal. |
| 3 | Metadata/IMDS attacks and IMDSv2 mitigation | High | A distinctly cloud-native attack CompTIA emphasizes; IMDSv2 is a frequently tested control. |
| 4 | Event monitoring / SIEM & observability | Medium | Foundational context; tested as "what tool/source" and "how to detect" rather than deep configuration. |
| 5 | Open ports (attack surface & auditing) | Medium | Common as a supporting detail in scenarios and as a baseline-deviation trigger. |
| 6 | Root causes: vuln-exploitation, human error, outdated/unpatched | Medium | Tested as classification questions distinguishing cause categories. |
| 7 | Social engineering / phishing | Low-Medium | Present but less cloud-specific; usually one straightforward recognition question. |

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

- **Event monitoring:** Centralize logs/metrics/traces into a SIEM; you can't troubleshoot what you don't collect. Sources = flow logs, control-plane/audit logs, host/app logs, identity logs.
- **Baseline deviation:** Establish "normal" first (metrics, ports, logins, egress), then alert on departures — the #1 early compromise signal *before* you know the attack.
- **Open ports:** Every listening port = attack surface; audit with nmap + security-group review; unexpected ports = shadow service or backdoor/C2.
- **Root causes:** Vulnerability exploitation (flaw abused) vs. human error (misconfig/exposed keys) vs. outdated/unpatched (the standing fuel that enables exploitation).
- **Social engineering/phishing:** Attacker convinces the user to open the door; spear phishing = targeted/personalized.
- **Malware/ransomware:** Malware = umbrella; ransomware = encrypt/exfiltrate + ransom note; defend with immutable/offline tested backups + segmentation.
- **DDoS:** Distributed flood exhausting resources; mitigate with scrubbing/WAF/CDN/rate limiting/autoscaling.
- **Cryptojacking:** Steals your compute to mine coin; signature = sustained max CPU + mining-pool traffic + rising bill.
- **Zombie/botnet:** Compromised host under C2; beaconing + unexpected ports + coordinated outbound attacks.
- **Metadata/IMDS:** SSRF → 169.254.169.254 → steal IAM creds → pivot; enforce **IMDSv2** (session token + hop limit) and alert on metadata-IP access.

**Symptom → Attack mini-map:**
- Sustained 100% CPU + mining pool + high bill → **Cryptojacking**
- Files renamed + ransom note + I/O spike → **Ransomware**
- Traffic flood + connection saturation + diverse IPs → **DDoS**
- C2 beaconing + unexpected port + coordinated outbound attack → **Zombie/botnet**
- App requests to 169.254.169.254 after SSRF → **Metadata/IMDS theft**
- Fake login page + harvested creds → **Phishing/social engineering**
- New listening port, no ticket → **Baseline deviation → open-port review**
- Off-hours/out-of-geo admin login → **Baseline deviation (identity)**
- 20x nightly egress → **Baseline deviation → data exfiltration**
- Exploited EOL/unpatched CVE → **Vulnerability exploitation + outdated system**

## SECTION 14 — LATEST INDUSTRY UPDATES (2024-2025, CV0-004-relevant)

- **2024 — IMDSv2 enforced by default (AWS):** AWS made IMDSv2 the default for newly launched instances and expanded controls/AMIs that require it, materially reducing SSRF-based metadata credential theft. Expect the exam to favor IMDSv2 as the correct metadata mitigation.
- **2024–2025 — Ransomware surge and shift to exfiltration/double-extortion:** Industry threat reports (e.g., Verizon DBIR 2024, Sophos State of Ransomware 2024) documented continued high ransomware volume and a pivot toward data-theft extortion over pure encryption, reinforcing immutable/offline backups plus egress monitoring as core defenses.
- **2024–2025 — Cloud cryptojacking campaigns intensify:** Campaigns from groups such as TeamTNT and others targeted exposed cloud APIs, Kubernetes, and leaked credentials to deploy miners, making sustained-CPU + cost-anomaly detection a mainstream baseline-deviation use case.
- **2024–2025 — SIEM/observability convergence and AI-assisted detection:** The market moved toward unified security-observability platforms (logs+metrics+traces) with anomaly/ML-driven baselining, aligning directly with 4.6's event-monitoring and baseline-deviation emphasis.
- **2025 — Cloud threat reports highlight identity + misconfiguration as top vectors:** Reports (e.g., CrowdStrike/Mandiant/Google Cloud) stressed stolen credentials, over-permissive IAM, and human-error misconfiguration as leading cloud breach causes, validating identity baseline monitoring and least privilege.

---

**Report:**
- First line of file: `## SECTION9 — INTERVIEW KNOWLEDGE`
- Flashcard count (SECTION10): 40
- Practice-question count (SECTION11): 20
