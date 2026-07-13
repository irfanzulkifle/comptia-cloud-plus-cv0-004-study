# CompTIA Cloud+ CV0-004 — Objective 4.6: Monitor Suspicious Activities to Identify Common Attacks

## SECTION 1 — Exam Objectives

**In one breath:** Monitoring suspicious activity means watching telemetry for signs of attack, then naming the attack so you can respond.

The 4.6 objective is to monitor for suspicious activity to identify common attacks. Its exam components are:

- **4.6.1 Event monitoring** — Collect and watch logs, metrics, and network flows for anomalies.
- **4.6.2 Deviation from the baseline** — "Normal" is documented; anything that departs from it is suspect.
- **4.6.3 Unnecessary open ports** — Open ports are attack surface; flag the ones that aren't supposed to be exposed.
- **4.6.4 Attack types** — Recognize the common cloud attacks:
  - **Vulnerability exploitation** — Attacking known weaknesses.
    - **Human error** — Misconfiguration / mistake enabling breach.
    - **Outdated software** — Unpatched CVEs (ties to 4.1/4.4).
  - **Social engineering** — Manipulating people.
    - **Phishing** — Fake message to steal credentials.
  - **Malware** — Malicious code.
    - **Ransomware** — Encrypts data, demands ransom.
  - **DDoS** — Flood to deny service (ties to 4.5.4).
  - **Cryptojacking** — Hijack compute to mine crypto.
  - **Zombie instances** — Forgotten/compromised idle resources.
  - **Metadata** — Abuse of instance metadata (IMDS/credential theft).

**Mental model:** `Collect events (4.6.1) → compare to baseline (4.6.2) → spot open ports (4.6.3) → classify the attack (4.6.4 a–g)`. Monitoring feeds the audit trail (4.3.5) and IDS (4.5.3).

## SECTION 2 — EXAM KNOWLEDGE

### 4.6.1 — Event Monitoring

- Continuous collection, centralization, correlation, and review of telemetry (logs, metrics, network flows, control-plane API events) to detect malicious or anomalous behavior.
- In the cloud, pull from many sources: CloudTrail/CloudWatch Logs (who called which API), VPC Flow Logs (what traffic moved), host/app logs, and IDS/IPS + EDR alerts.
- Ship everything to a SIEM / log-analytics platform — a single signal alone is unreadable, but correlated with a second it becomes an obvious attack.
- Trap: monitoring detects and alerts; it does NOT block. Detection (4.6) is distinct from prevention (4.5).

### 4.6.2 — Deviation from the Baseline

- A baseline is the documented profile of "normal" (typical CPU/memory, traffic volume/direction, login times/locations, API patterns, egress volume).
- A deviation is any statistically/operationally significant departure, treated as suspicious until explained.
- You cannot do anomaly detection without first knowing "boring" — capture metrics over days/weeks to establish the baseline; detect via anomaly-based detection, metric alarms, UEBA.
- Trap: not every spike is an attack. Workload-correlated growth is a baseline shift, not a compromise; attacks show deviation UNCORRELATED with business activity.

### 4.6.3 — Unnecessary Open Ports

- Every listening port is a potential entry point; an "unnecessary" open port has no business reason to be reachable.
- Audit security groups, NACLs, and host firewalls for unexpected open ports (SSH/22, RDP/3389 to 0.0.0.0/0; Redis 6379, Elasticsearch 9200 from the internet).
- An unintended open port is either misconfiguration (human error) or an attacker-opened backdoor / C2 channel.
- Trap: not every open port is bad — 443 on a web server is required. Least-exposure closes UNNEEDED ports, judged against your documented baseline.

### 4.6.4 — Attack Types

- Covers the common cloud attacks you must recognize from symptoms; same mental model — collect → baseline → ports → classify.
- Memorize the signature symptom so you can map scenario → attack on the exam.
- Spans vulnerability exploitation, social engineering, malware, DDoS, cryptojacking, zombie instances, and metadata/IMDS abuse.
- Cheat line: odd login = phishing · encryption = ransomware · CPU spike no traffic = cryptojacking · traffic spike unreachable = DDoS · unexpected port = misconfig · unknown idle instance = zombie · creds via app = metadata/SSRF · old software owned = vuln exploitation · open bucket = human error.

#### Vulnerability Exploitation

- Attacker leverages a known/zero-day weakness in software, config, or design to gain access or execute code.
- Two practical root causes: human error (misconfig walked through) and outdated/unpatched software (known CVE).
- Exam: "old unpatched server owned" → vuln exploitation via outdated software; "open S3 bucket dumped" → via human error.
- Fix differs by root cause: correct config for human error, patch/upgrade for outdated software.

##### Human Error

- Misconfiguration or operational mistake (public bucket, over-permissive IAM, default creds, open port) creating an exposure an attacker walks through without defeating a control.
- When data leaks via misconfig (not defeated software), the answer is human error — NEVER phishing or code exploitation.
- Leading cloud breach cause — more common than clever hacking.
- Prevention: IaC with policy checks, least privilege, mandatory tagging, posture scanning (4.1/4.4).

##### Outdated Software

- Carries known fixable CVEs because never patched/upgraded; the standing condition that ENABLES exploitation (ties to 4.1/4.4).
- Exam: "unpatched CVE on internet-facing software exploited" → vuln exploitation + outdated software (not human error).
- Automated exploit kits hunt unpatched systems within hours of a CVE — patch lag is the biggest self-inflicted risk.
- Detection: vulnerability scanning + version checks; prevention: disciplined patch/lifecycle policy.

#### Social Engineering

- Manipulation of PEOPLE, not systems, to gain access/info; tricks a trusted user into opening the door.
- Phishing is its most common form.
- Bypasses even strong technical controls — a valid password on a lookalike site defeats a patched server.
- Defense: phishing-resistant MFA (FIDO2), conditional access, awareness training. Recognition: weird email + login, MFA-fatigue, impossible travel.

##### Phishing

- Social-engineering via fraudulent messages (email/SMS/vishing) to steal creds, install malware, or click malicious links; spear phishing is the targeted variant.
- Exam: "entered creds on fake login page from email" = phishing (NOT malware — the user handed over the password).
- Top initial-access vector for cloud account compromise; defeats defenses by exploiting trust.
- Mitigations: phishing-resistant MFA, email filtering/DMARC, sign-in risk scoring, user training.

#### Malware

- Umbrella for malicious software (viruses, trojans, worms, spyware, rootkits, ransomware) designed to harm/spy/control; executed on a system.
- Most destructive cloud subtype is ransomware.
- Enters via phishing links, exploit kits, or compromised dependencies; then exfiltrates, persists, spreads laterally.
- Detection: process/file/network telemetry (EDR/AV); prevention: patching, least privilege, EDR (4.5.1). Distinction: malware = code runs; social engineering = person manipulated.

##### Ransomware

- Malware subtype that encrypts (or exfiltrates + threatens to leak) data and demands payment for the key.
- Defining symptom: mass file encryption + ransom notes, often a disk I/O spike as files are rewritten.
- Exam: "files encrypted, ransom note" → ransomware; recovery almost always = restore from clean immutable/offline backups — never pay.
- Mitigation: tested immutable/offline backups (3.3), EDR (4.5.1), least privilege, segmentation. Trap: encryption is the OUTCOME; exploitation is the entry VECTOR.

#### DDoS

- Floods a target from many sources to exhaust bandwidth/connection tables/app resources and make it unreachable; counterpart of 4.5.4.
- Volumetric/amplified floods hit the network; L7 floods hit HTTP.
- Exam: "unreachable, inbound 100x normal from many IPs" → DDoS → Shield + WAF + CDN (4.5.4).
- Signature: massive inbound spike, geographically diverse sources, degraded availability — distinct from cryptojacking and single-source brute force.

#### Cryptojacking

- Attacker hijacks your compute (CPU/GPU) to mine crypto — victim sees high utilization + cost, not a "broken" service.
- Exam: "CPU pegged 100%, app traffic low" → cryptojacking → investigate, patch entry vector, lock down.
- Recognition: unexplained CPU spike, baseline deviation (4.6.2), outbound to mining-pool domains (3333/8080), no business reason.
- Distinct from DDoS (no inbound flood) and zombie (no C2 outbound — just mining). Campaigns target exposed APIs/K8s/leaked keys (e.g., TeamTNT).

#### Zombie Instances

- Compute still running but forgotten/unowned/attacker-provisioned — draining money and expanding attack surface.
- May be a leftover project, an un-torn-down test, or attacker resources (compromised key spinning up miners).
- Exam: "unnamed instances, nobody owns them" → zombie → investigate and decommission.
- Recognition: no owner tag, no recent activity, odd tags, near-zero constant utilization. Ties to 3.4/2.5. A zombie is LOW-utilization — high CPU + mining = cryptojacking.

#### Metadata (IMDS Abuse)

- Exploits the Instance Metadata Service (esp. IMDSv1) to steal an instance's IAM creds and act as that role; link-local endpoint 169.254.169.254.
- Reached via SSRF bug / open proxy in a vulnerable app; grab temp creds and escalate.
- Exam: "app with SSRF assumes instance role, enumerates account" → metadata/IMDS abuse → enable IMDSv2 + fix SSRF.
- Mitigation: enforce IMDSv2 (token-required, hop limit 1), block metadata from app code, patch SSRF. Trap: instance creds used from the instance itself = expected; exfiltration findings fire when used from outside AWS.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 Spotting Cryptojacking via CPU Baseline Deviation

- A SaaS fleet normally at 15–25% CPU overnight sat pinned at 95–100% with negligible traffic; process telemetry showed an unfamiliar long-running process with outbound connections to a mining-pool domain → cryptojacking. Response: isolate via restrictive SG, snapshot for forensics, rotate creds, kill process, rebuild from clean AMI. Prevention: CPU baseline alarms, egress filtering, patch the entry vector.

### 3.2 Catching Phishing-Led Credential Theft via Event Monitoring

- A finance employee clicked a helpdesk-impersonating email and entered creds on a lookalike portal; CloudTrail then showed a successful login from a no-staff country plus an impossible-travel alert and rogue MFA enrollment → phishing detected at the identity layer via event monitoring. Response: disable account, revoke sessions/tokens, remove rogue MFA, force password reset. Prevention: phishing-resistant MFA, conditional access, training, sign-in risk scoring.

### 3.3 Identifying a Zombie Instance Draining Cost

- A customer's bill crept up with no new deployments; Cost Explorer by tag showed an untagged EC2 instance in an unused region running 24/7 for months — near-zero egress, no connections, minimal constant CPU, no logs → zombie (waste/hygiene, not attack). Response: confirm ownership, snapshot, stop/delete. Prevention: mandatory tagging, idle-resource detection, TTL on test resources, cost-anomaly reviews.

### 3.4 Detecting Metadata/SSRF Credential Theft

- A public web app with an SSRF flaw forced the server to call 169.254.169.254; IMDSv1 (no token) returned the instance role's temp creds, and GuardDuty raised InstanceCredentialExfiltration when those creds were used from an external IP; CloudTrail corroborated unfamiliar API calls → metadata/SSRF abuse. Response: revoke role sessions, rotate creds, patch SSRF, audit role access. Prevention: enforce IMDSv2, least-privilege roles, monitor cred use from unexpected locations.

### 3.5 Recognizing a DDoS via Traffic Deviation + Open-Port Misconfig

- An API at a few thousand req/min suddenly saw inbound traffic spike to millions of pps from thousands of distributed IPs with collapsed availability; a security group had left management + app ports open to 0.0.0.0/0 → distributed denial-of-service amplified by open-port misconfig. Response: enable/tune Shield, rate-limit + WAF, tighten SG to required ports/ranges, scale out or CDN. Prevention: least-exposure policy, always-on DDoS mitigation, volumetric traffic-baseline alarms.

## SECTION 4 — COMPARISON TABLES

### 4.1 Baseline-Normal vs Deviation-Suspicious Signals

| Signal | Baseline (Normal) | Deviation (Suspicious) |
|--------|-------------------|--------------------------|
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
|---------|--------------------------|
| CPU spike + no traffic increase | Cryptojacking |
| Traffic spike + service unreachable | DDoS |
| Files encrypted + ransom note | Ransomware |
| Odd login (new geo, impossible travel) | Credential theft / phishing |
| Unexpected open port | Misconfiguration (exposed surface) |
| Unknown idle instance, rising bill | Zombie instance (waste) |
| Credentials stolen via the application | Phishing / SSRF metadata abuse |
| Old/unpatched software compromised | Vulnerability exploitation (outdated) |

### 4.3 Vulnerability Exploitation: Human Error vs Outdated Software

| Aspect | Human Error | Outdated Software |
|--------|-------------|-------------------|
| Root cause | Misconfiguration, mistake, oversight | Unpatched known vulnerability (CVE) |
| Example | S3 bucket left public, port open to all | Exploited old library / unpatched OS |
| Who introduces it | Operator/admin action | Failure to patch/maintain |
| Detection | Config/posture scanning, audits | Vulnerability scanning, version checks |
| Fix | Correct the configuration | Apply patch / upgrade version |
| Prevention | IaC guardrails, least privilege, review | Patch management, lifecycle policy |

### 4.4 Social Engineering vs Malware

| Aspect | Social Engineering | Malware |
|--------|--------------------|---------|
| Core mechanism | Manipulating a person | Malicious code execution |
| Example | Phishing email, pretexting, vishing | Ransomware, trojan, miner binary |
| Entry point | Human trust/deception | Software/system exploitation |
| Credential loss | Victim hands them over | Stolen by running code |
| Primary defense | Awareness training, MFA, verification | EDR/AV, patching, least privilege |
| Detection focus | Auth events, impossible travel | Process/file/network telemetry |

### 4.5 Ransomware Mitigation vs Cryptojacking Response

| Aspect | Ransomware Mitigation | Cryptojacking Response |
|--------|------------------------|-------------------------|
| Goal of attacker | Encrypt data, extort ransom | Steal compute to mine crypto |
| Key symptom | Encrypted files, ransom note | Sustained max CPU, mining egress |
| Immediate action | Isolate, restore from backup | Isolate, kill process, rebuild AMI |
| Core controls | Backups, EDR, least privilege | CPU baseline alarms, egress filtering |
| Recovery | Restore clean backups | Terminate miner, rotate credentials |
| Prevention | Offline/immutable backups, patching | Patch entry vector, block pools |

### 4.6 Metadata IMDSv1 vs IMDSv2

| Aspect | IMDSv1 | IMDSv2 |
|--------|--------|--------|
| Request model | Simple request/response | Session-oriented, token required |
| SSRF resistance | Weak — easy metadata hop | Strong — blocks simple SSRF |
| Token | None | PUT to get token, then use it |
| Hop limit | Not enforced by default | Configurable (recommend 1) |
| Credential theft risk | High via SSRF | Greatly reduced |
| Recommendation | Deprecate/disable | Enforce as required |

## SECTION 5 — AWS PROVIDER MAPPING

This objective maps to AWS-only services for detecting/monitoring the suspicious activities in 4.6. The four services are:

| 4.6 Monitoring Need | AWS Service | How it detects the activity |
|---------------------|-------------|------------------------------|
| Attack/intrusion & credential-theft detection (4.6.1, 4.6.4a–g) | **GuardDuty** | A managed threat-detection service that analyzes CloudTrail, VPC Flow Logs, and DNS logs to flag cryptojacking, instance-credential exfiltration (metadata abuse), reconnaissance, and unusual API calls. |
| Event monitoring & metric/alarm detection (4.6.1, 4.6.2, 4.6.4d/e) | **CloudWatch** | Collects logs/metrics, builds baselines, and fires alarms on deviation (e.g., CPU pegged for cryptojacking, traffic spike for DDoS) via CloudWatch Logs, Metrics, and Alarms. |
| Configuration & open-port / drift detection (4.6.3, 4.6.4f) | **Config** | Continuously records resource configuration and evaluates rules — flagging unnecessary open ports (unintended 22/3389/0.0.0.0/0), zombie/orphaned instances, and baseline drift. |
| Network traffic visibility (4.6.3, 4.6.4e/g) | **VPC Flow Logs** | Captures IP traffic to/from ENIs, exposing unexpected open ports, mining-pool egress (cryptojacking), and metadata-endpoint (169.254.169.254) calls indicating SSRF/IMDS abuse. |

These compose: VPC Flow Logs feed GuardDuty and CloudWatch; Config catches posture drift; together they satisfy event monitoring, baseline deviation, and open-port detection for every 4.6 attack type.

## SECTION 6 — PRACTICE QUESTIONS

1. The continuous collection and correlation of logs, metrics, and API events into a SIEM is called:
   A. Hardening
   B. Event monitoring
   C. Patching
   D. Encryption
   Answer: B — event monitoring centralizes telemetry for detection.

2. A server normally uses 20% CPU but is pinned at 100% with no traffic rise. This is a:
   A. Baseline deviation
   B. DDoS
   C. Normal growth
   D. Patch event
   Answer: A — sustained CPU with no matching workload is a deviation.

3. Management port 3389 open to 0.0.0.0/0 is best described as:
   A. Required for web traffic
   B. An unnecessary open port
   C. A DDoS attack
   D. Encryption at rest
   Answer: B — an unneeded, internet-exposed port is unnecessary attack surface.

4. An attacker uses a known CVE in outdated software to gain a shell. This is:
   A. Human error
   B. Vulnerability exploitation (outdated software)
   C. Phishing
   D. Ransomware
   Answer: B — exploiting an unpatched CVE is vulnerability exploitation via outdated software.

5. A public S3 bucket is misconfigured and dumped. The root cause is:
   A. Phishing
   B. Human error (misconfiguration)
   C. Cryptojacking
   D. DDoS
   Answer: B — data leaked via a misconfiguration, not deception or code.

6. A user enters credentials on a fake portal after an email. This is:
   A. Malware
   B. Phishing (social engineering)
   C. Vulnerability exploitation
   D. Zombie instance
   Answer: B — credentials handed over under deception = phishing.

7. Files are mass-encrypted with a ransom note. The attack type is:
   A. DDoS
   B. Ransomware (malware)
   C. Cryptojacking
   D. Metadata abuse
   Answer: B — mass encryption plus extortion is ransomware.

8. A site is unreachable with inbound traffic 100x normal from many IPs. This is:
   A. Cryptojacking
   B. DDoS
   C. Zombie instance
   D. Phishing
   Answer: B — distributed volumetric flood causing unavailability is DDoS.

9. CPU is pegged at 100% but app traffic is low. This points to:
   A. DDoS
   B. Cryptojacking
   C. Ransomware
   D. Human error
   Answer: B — high CPU with no business traffic is cryptojacking.

10. An untagged VM runs 24/7 with no owner and a rising bill. This is a:
    A. Zombie instance
    B. DDoS victim
    C. Ransomware host
    D. Phishing target
    Answer: A — abandoned, unowned, idle compute is a zombie instance.

11. An app with SSRF reads 169.254.169.254 and steals the instance role's creds. This is:
    A. Phishing
    B. Metadata / IMDS abuse
    C. Zombie instance
    D. DDoS
    Answer: B — SSRF to the metadata endpoint stealing IAM creds is metadata abuse.

12. The mitigation that blocks simple SSRF metadata hops is:
    A. IMDSv1
    B. IMDSv2 (token-required, hop limit 1)
    C. Disabling CloudTrail
    D. Opening port 22
    Answer: B — IMDSv2's token/session model stops simple SSRF.

13. A legitimate, gradual, workload-correlated traffic increase is:
    A. An attack
    B. A baseline shift (normal growth)
    C. Cryptojacking
    D. Phishing
    Answer: B — correlated, explainable growth is normal, not an attack.

14. A single IP brute-forcing a login is BEST classified as:
    A. DDoS
    B. Brute force (credential attack)
    C. Ransomware
    D. Zombie
    Answer: B — single-source password guessing is brute force, not distributed DDoS.

15. Which AWS service flags cryptojacking and instance-credential exfiltration?
    A. CloudTrail
    B. GuardDuty
    C. IAM
    D. KMS
    Answer: B — GuardDuty analyzes logs to detect these threats.

16. Which AWS service builds CPU/baseline alarms to catch cryptojacking/DDoS?
    A. VPC Flow Logs
    B. CloudWatch
    C. Macie
    D. Secrets Manager
    Answer: B — CloudWatch metrics/alarms detect baseline deviation.

17. Which AWS service flags unnecessary open ports and orphaned instances?
    A. Config
    B. Shield
    C. Macie
    D. KMS
    Answer: A — Config evaluates resource config and drift rules.

18. Which AWS service exposes network traffic to find mining egress and SSRF calls?
    A. VPC Flow Logs
    B. CloudTrail
    C. IAM
    D. GuardDuty alone
    Answer: A — VPC Flow Logs capture the ENI traffic detail.

19. Monitoring that surfaces a suspicious sign-in but does NOT block it demonstrates that:
    A. Monitoring prevents attacks
    B. Monitoring detects/alerts; blocking needs inline controls
    C. SIEM is a firewall
    D. IDS is the same as IPS
    Answer: B — detection is not prevention; blocking needs IPS/conditional access.

20. Port 443 open on a public web server should be:
    A. Closed as unnecessary
    B. Kept (it is required for the service)
    C. Opened to 0.0.0.0/0 on 22 instead
    D. Encrypted at rest
    Answer: B — 443 is required; least-exposure closes only unneeded ports.
