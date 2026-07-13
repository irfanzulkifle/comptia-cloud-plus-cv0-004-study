---
title: CompTIA Cloud+ CV0-004 — Objective 6.2
objective: "6.2 Given a scenario, troubleshoot network issues."
domain: "6.0 Troubleshooting"
weight: "Part of Troubleshooting domain"
tags: [Cloud+, CV0-004, Troubleshooting, Networking, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 6.2
## Troubleshoot Network Issues

> **Objective statement:** *"Given a scenario, troubleshoot network issues."*
> **Domain:** 6.0 Troubleshooting
> **What it tests:** A symptom-to-cause domain for networking. You are given a network failure (nothing pings, DNS fails, traffic is slow, a VLAN is isolated) and must name the root cause from the official list and the fix. Expect PBQ "why can't these two resources talk" questions.

## SECTION 1 — Exam Objectives


- **In one breath:** Network troubleshooting = figuring out WHY two cloud resources can't reach each other, or why traffic is slow/broken, using the official sub-objective list.

- **The 6.2 sub-objectives (verbatim from the official exam objectives):**
  - **6.2.1 — Network service unavailability:** A core network service is down or misbehaving — DHCP, DNS, NTP, NAT, or HTTP (status codes)
  - **6.2.2 — Latency:** High round-trip time / slow responses
  - **6.2.3 — Bandwidth / throughput issues:** Not enough capacity / bottleneck / throttled pipe
  - **6.2.4 — Network device misconfiguration:** Wrong setting on a router/switch/firewall/load balancer/gateway
  - **6.2.5 — Protocol incompatibility:** Two endpoints speak different/conflicting protocols/versions
  - **6.2.6 — Protocol deprecations:** A protocol/version was retired by the provider or standard
  - **6.2.7 — IP addressing issues:** Scope exhaustion (no free IPs) or network overlap (CIDR conflict)
  - **6.2.8 — Routing issues:** Missing routes or misconfigured routes
  - **6.2.9 — Switching issues:** VLAN problems — misconfigured tags, access vs trunk port confusion

- **Mental model — symptom → cause:** `Can't resolve name → DNS (6.2.1) · No IP from DHCP → DHCP (6.2.1) · Clock skew breaks auth → NTP (6.2.1) · Outbound fails, no public IP → NAT (6.2.1) · 503/404 from app → HTTP status (6.2.1) · Slow RTT → latency (6.2.2) · Saturated link → bandwidth (6.2.3) · Firewall blocks → device misconfig (6.2.4) · TLS version mismatch → protocol incompat (6.2.5) · Old SSH/TLS disabled → deprecation (6.2.6) · "no free addresses" → scope exhaustion (6.2.7) · overlapping VPCs → network overlap (6.2.7) · traffic black-holes → missing/misconfig route (6.2.8) · VLAN isolation → misconfigured tag/port (6.2.9).`

---
## SECTION 2 — Exam Knowledge

### 6.2.1 — NETWORK SERVICE UNAVAILABILITY (DHCP, DNS, NTP, NAT, HTTP)
- **Definition:** Core network services that other systems depend on stop answering or become unreachable. DHCP (hands out IP addresses), DNS (name→IP translation), NTP (clock sync), NAT (private→public IP translation), and HTTP (web/service traffic, including status codes like 4xx/5xx). The backend servers may be perfectly healthy — only the supporting service is down.
- **Why it matters:** On the exam, "users can't resolve the hostname / can't get an IP / clocks drift and auth fails / site returns 503" all land here. Fix = check the specific service (DNS resolver, DHCP scope, NTP peers, NAT gateway, ALB health). In real life these cause most "the app is down" incidents that are actually network-layer, not app-layer.
- **Analogy:** A city where the street signs (DNS), address allocator (DHCP), town clocks (NTP), and mail forwarders (NAT) all stop working. The houses exist, but nobody can find or reach them.
- **Example (cloud):** A VM loses its DHCP lease and gets no IP → can't reach anything. Or a misconfigured DNS resolver endpoint → "name resolution failure" on all outbound calls. On AWS: Route 53 resolver failure, expired DHCP options, NAT gateway "cannot allocate," ALB returning 503.

### 6.2.2 — LATENCY
- **Definition:** Delay between request and response, measured as round-trip time (RTT). Caused by physical distance, chatty protocols, overloaded links, or misrouted traffic.
- **Why it matters:** High latency makes apps feel broken (timeouts, retries) even when nothing is "down." Exam: "requests take 3s between region A and B" → co-locate, use CDN/cache (ElastiCache), tune the ALB. Always distinguish latency (slow) from an outage (dead).
- **Analogy:** A slow postal service — the letter arrives, just late. Contrast with an outage where the letter never arrives.
- **Example (cloud):** An app in us-east-1 calling a database in ap-southeast-1 adds 200ms+ RTT per query; multiplied over 100 queries that is a 20s page load. Fix: place the DB near the app, or add a read replica / ElastiCache.

### 6.2.3 — BANDWIDTH / THROUGHPUT ISSUES
- **Definition:** Not enough network capacity (bits/sec) to carry the traffic, causing saturation, packet drops, and slowness. Throughput = the actual achieved rate.
- **Why it matters:** Bulk transfers, backups, and replication saturate links → packet loss and retries that also hurt production. Exam: "nightly backup kills production traffic" → schedule off-peak, increase the tier, parallelize, or use a dedicated link.
- **Analogy:** A single-lane road at rush hour — cars (packets) pile up and some are sent back (drops).
- **Example (cloud):** Cross-AZ replication saturates a 1 Gbps link; VPC Flow Logs show high bytes plus REJECT/drops. Fix: upgrade the instance network tier, add bandwidth, or throttle the backup.

### 6.2.4 — NETWORK DEVICE MISCONFIGURATION
- **Definition:** Wrong settings on network gates — security groups, NACLs, route tables, load balancers, firewall rules. The single most common cause of "can't reach it."
- **Why it matters:** One denied SG rule or a wrong NACL blocks all traffic while the instance runs fine. Exam: "instance can't be reached though it's running" → check SG inbound, NACL, and LB target-group health. Use VPC Reachability Analyzer to name the exact blocker.
- **Analogy:** The wrong PIN on the gate — you are authorized, but the lock still won't open.
- **Example (cloud):** SG missing inbound 443 → HTTPS fails; NACL blocking ephemeral ports → responses dropped; LB target group unhealthy → 502. Fix: open the correct ports, fix the NACL, re-register targets.

### 6.2.5 — PROTOCOL INCOMPATIBILITY
- **Definition:** Two endpoints that cannot agree on a protocol version or cipher — e.g., a client speaks TLS 1.0 but the server requires TLS 1.2, or an SSH version mismatch.
- **Why it matters:** Unlike deprecation (the provider removed it for everyone on a date), incompatibility is two live ends that simply don't share a common protocol. Exam: "handshake fails but both sides are alive" → align the TLS/cipher on both ends.
- **Analogy:** Two people at the same table speaking different languages — both are present, neither understands the other.
- **Example (cloud):** A legacy client only does TLS 1.0; the ALB policy enforces TLS 1.2 → connection refused. Fix: upgrade the client, or (temporarily) relax the server policy to a mutually supported version.

### 6.2.6 — PROTOCOL DEPRECATIONS
- **Definition:** A vendor or standard retires a protocol/version on a set date (e.g., disabling TLS 1.0/1.1, SSLv3, or old ciphers). After the cutoff, anything still using it breaks for everyone.
- **Why it matters:** Announced far ahead, but still causes outages for unpatched clients. Exam: "broke for all old clients on the announced date" → deprecation, not incompatibility. Fix: migrate clients before the date. Closely related to 6.3.1 (cipher-suite deprecation), which is the encryption layer specifically.
- **Analogy:** The post office announces "we no longer accept cursive letters after June 1" — everyone still writing cursive is rejected. (Contrast 6.2.5: two people just speaking different languages, no announced cutoff.)
- **Example (cloud):** A load balancer drops TLS 1.0 + RC4 → legacy POS clients fail the handshake with "no cipher in common." Fix: upgrade the client to TLS 1.2 + AES-GCM.

### 6.2.7 — IP ADDRESSING ISSUES (SCOPE EXHAUSTION & NETWORK OVERLAP)
- **Definition:** Two sub-problems. Scope exhaustion = the DHCP pool or subnet ran out of assignable IPs (no free address to hand out). Network overlap = two VPCs/subnets/CIDRs use the same IP range, so routing is ambiguous or peering/VPN cannot connect.
- **Why it matters:** Exhaustion → new instances/ENIs can't get an IP ("cannot allocate"). Overlap → peering/VPN fails or routes collide. Exam: "new ENIs fail to get an IP" or "peering unreachable due to CIDR clash" → resize the subnet / re-IP / use non-overlapping CIDRs.
- **Analogy:** Exhaustion = the apartment building is full, no free mailboxes. Overlap = two buildings both claim "5 Main St" — the postman doesn't know which to deliver to.
- **Example (cloud):** A /24 subnet with 250 instances plus AWS-reserved + NAT + LB ENIs → no free IP for a new instance. Or VPC A (10.0.0.0/16) peered to VPC B also 10.0.0.0/16 → routes collide. Fix: enlarge the subnet or renumber one VPC.

### 6.2.8 — ROUTING ISSUES (MISSING & MISCONFIGURED ROUTES)
- **Definition:** The route table lacks an entry or has a wrong one, so traffic has no valid path — e.g., no route to the internet gateway (can't reach the internet), no route to NAT, or asymmetric routes.
- **Why it matters:** An instance in a private subnet can't reach the internet because the route to the NAT gateway is missing. Exam: "private instance can't patch/download" → add a route to the NAT GW. Or "can't reach the other subnet" → check route-table association.
- **Analogy:** No road from your street to the highway — you're stuck locally even though the highway exists.
- **Example (cloud):** A private-subnet route table missing 0.0.0.0/0 → NAT GW → instances can't update. Fix: add the route. Reachability Analyzer flags the missing route as the blocker.

### 6.2.9 — SWITCHING ISSUES (VLAN: MISCONFIGURED TAGS, ACCESS VS TRUNK PORTS)
- **Definition:** Layer-2 problems on switches: wrong VLAN tags, a port set as an **access port** when it should be a **trunk port** (or vice versa), or mismatched native VLANs. Traffic lands in the wrong segment or gets dropped.
- **Why it matters:** In the cloud this maps to subnet/ENI placement and SG/NACL segmentation (the "switch" is virtual). Exam: "VM in VLAN 10 can't talk to VLAN 20" → check tags / trunk / access config. Misconfigured tags → traffic dropped or misrouted.
- **Analogy:** Plugging a phone into a "red network" port, but the switch thinks it's "blue" — calls go nowhere.
- **Example (cloud):** An ENI expected on a specific subnet/segment but placed wrong → unreachable from its peers. Or a trunk port stripping tags → VLAN leakage. Fix: correct the tag/port mode (set the right **access port** vs **trunk port** mode) and re-place the ENI.
## SECTION 3 — Real World Cloud Examples


- **3.1 Web app returns "name resolution failure" after a subnet move → DNS service unavailability (6.2.1):** **Symptom:** Instances in a freshly peered subnet can't reach `api.internal.example.com`; curl says "could not resolve host." **Diagnosis:** The subnet's DHCP options set still points at the old VPC's Route 53 resolver endpoint, so DNS queries go nowhere after the move. **Fix:** Update the DHCP options set to the correct Route 53 Resolver IP / endpoint, or point at the new VPC's resolver. **Exam angle:** "can't resolve names but IP ping works" = DNS, not routing.

- **3.2 Latency jumps 200ms after placing DB in another region → Latency (6.2.2):** **Symptom:** App in `us-east-1` calling a database in `ap-southeast-1` now takes seconds per query; users see timeouts. **Diagnosis:** Cross-region RTT multiplied across many queries — slow, not down. **Fix:** Co-locate the DB near the app, add a read replica / ElastiCache, or use a nearer region. **Exam angle:** Distinguish latency (slow) from an outage (dead) — both show as "bad performance."

- **3.3 Nightly backup saturates the link and kills prod traffic → Bandwidth/throughput (6.2.3):** **Symptom:** Every night at 02:00 prod latency spikes and packets drop; VPC Flow Logs show high bytes + REJECT on the backup path. **Diagnosis:** Cross-AZ replication / backup saturates the 1 Gbps link shared with prod. **Fix:** Schedule off-peak, increase the network tier, parallelize, or use a dedicated/separate link. **Exam angle:** "periodic slowness at a fixed time" = bandwidth saturation, not a device fault.

- **3.4 Instance runs but HTTPS just won't connect → Network device misconfiguration (6.2.4):** **Symptom:** EC2 is healthy, web server listening on 443, but no client can reach it. VPC Reachability Analyzer reports the security group as the blocker. **Diagnosis:** SG inbound rule missing 443 (or NACL blocking ephemeral ports). **Fix:** Open 443 in the SG; fix the NACL; re-register the ALB target. **Exam angle:** "server up but unreachable" = SG/NACL, almost always.

- **3.5 Legacy client can't handshake the ALB after a policy change → Protocol incompatibility / deprecation (6.2.5 / 6.2.6):** **Symptom:** After the ALB enforces TLS 1.2, an old client that only speaks TLS 1.0 fails the handshake. **Diagnosis:** If it broke for ALL old clients on an announced date → deprecation (6.2.6); if it's two ends that simply don't share a suite with no announced cutoff → incompatibility (6.2.5). Here it's deprecation. **Fix:** Upgrade the client to TLS 1.2 + AES-GCM. **Exam angle:** "broke on a cutoff date for everyone old" = deprecation, not misconfig.

- **3.6 New ENIs fail to get an IP; peering won't connect → IP addressing: scope exhaustion / overlap (6.2.7):** **Symptom:** A /24 subnet can't launch new instances ("cannot allocate address"); separately, two VPCs that should peer show ambiguous/colliding routes. **Diagnosis:** Exhaustion = DHCP/subnet ran out of free IPs; overlap = both VPCs use 10.0.0.0/16 so routes collide. **Fix:** Enlarge the subnet (or add a second CIDR) for exhaustion; renumber one VPC to a non-overlapping CIDR for overlap. **Exam angle:** "no free IP" vs "routes collide" are two different IP problems.

- **3.7 Private-subnet instances can't download patches → Routing issues (6.2.8):** **Symptom:** Instances in a private subnet can't reach the internet to update, though the app works internally. Reachability Analyzer flags "no route to NAT gateway." **Diagnosis:** The private route table is missing `0.0.0.0/0 → NAT GW`. **Fix:** Add the route to the NAT gateway (or internet gateway for public subnets). **Exam angle:** "internal works, internet doesn't" = missing route, not SG.

- **3.8 VM in VLAN 10 can't talk to VLAN 20 → Switching / VLAN (6.2.9):** **Symptom:** Two workloads that should communicate show no traffic between them; a trunk port is stripping tags. **Diagnosis:** Misconfigured VLAN tag or wrong access-vs-trunk port mode; traffic lands in the wrong segment or is dropped. In the cloud this maps to subnet/ENI placement and SG/NACL segmentation. **Fix:** Correct the tag/port mode (set the right access port vs trunk port mode) and re-place the ENI. **Exam angle:** "same network, wrong segment" = VLAN tag/port misconfig.
## SECTION 4 — COMPARISON TABLES


### 4.1 Symptom → 6.2 Cause Mapping

| Symptom | Root cause (6.2) | Quick fix direction |
|---|---|---|
| 169.254 self-assigned IP | DHCP (6.2.1) | Fix DHCP option set / pool |
| Name fails, IP works | DNS (6.2.1) | Fix resolver / DHCP DNS option |
| Auth fails, clock skew | NTP (6.2.1) | Repoint NTP, sync clocks |
| Private can't reach internet | NAT (6.2.1) / route | Fix NAT gateway + route |
| 4xx / 5xx from app | HTTP status (6.2.1) | Fix client (4xx) or server (5xx) |
| Slow per request, bulk fine | Latency (6.2.2) | Co-locate, cache, tune |
| Big transfer stalls | Bandwidth (6.2.3) | Upgrade link/tier, parallelize |
| Running but unreachable | Device misconfig (6.2.4) | Fix SG/NACL/LB rule |
| Handshake fails | Protocol incompat (6.2.5) | Align TLS/cipher version |
| Broke for all on a date | Deprecation (6.2.6) | Migrate off old protocol |
| No free IPs / CIDR collide | IP addressing (6.2.7) | Resize subnet / re-IP overlap |
| One subnet can't reach net | Routing (6.2.8) | Add/correct route table |
| Wrong/isolated segment | Switching/VLAN (6.2.9) | Fix VLAN tag / access-trunk |

### 4.2 Latency vs Bandwidth

| Dimension | Latency (6.2.2) | Bandwidth (6.2.3) |
|---|---|---|
| Measures | Delay per packet (RTT, ms) | Volume per second (Mbps/Gbps) |
| Symptom | Slow responses, many round trips | Big transfers stall / capped rate |
| Fix | Co-locate, cache, protocol tuning | Bigger pipe, higher tier, parallelize |
| Exam tell | "slow but works" | "transfer never finishes" |

### 4.3 Incompatibility vs Deprecation

| Dimension | Protocol incompat (6.2.5) | Deprecation (6.2.6) |
|---|---|---|
| Nature | Two live ends disagree on version | Provider removed the old protocol |
| Trigger | Any time configs mismatch | A scheduled provider cutoff date |
| Scope | Affected pair | Everyone still on old version |
| Fix | Align protocol on both ends | Migrate to supported version |

### 4.4 Scope exhaustion vs Network overlap

| Dimension | Scope exhaustion (6.2.7) | Network overlap (6.2.7) |
|---|---|---|
| Problem | No free IPs in subnet/pool | Two CIDRs collide |
| Symptom | Can't launch more instances | Peering/VPN fails, ambiguous routes |
| Fix | Add CIDR / larger subnet | Re-address one side non-overlapping |

### 4.5 Missing route vs Misconfigured route

| Dimension | Missing route (6.2.8) | Misconfigured route (6.2.8) |
|---|---|---|
| Problem | No entry for destination | Entry points to wrong target |
| Symptom | Black-hole / unreachable | Traffic sent wrong way |
| Fix | Add route (dest → target) | Correct the target |

### 4.6 Device misconfig vs Switching/VLAN

| Dimension | Device misconfig (6.2.4) | Switching/VLAN (6.2.9) |
|---|---|---|
| Layer | L3/L4 (SG, NACL, firewall, LB) | L2 (VLAN tag, access/trunk) |
| Symptom | Dropped/blocked traffic | Wrong/isolated segment |
| Fix | Open/correct the rule | Fix VLAN tag / port mode |

---

## SECTION 5 — AWS PROVIDER MAPPING


AWS-ONLY mapping for objective 6.2. On AWS, network troubleshooting is anchored by three native services: **VPC Reachability Analyzer** (path analysis that proves whether two resources *can* communicate and why), **VPC Flow Logs** (captures every IP flow so you can see dropped/allowed traffic and bandwidth), and **CloudWatch Logs** (application and service logs that expose DNS, NAT, DHCP, and HTTP-status symptoms). Together they let you see and fix every 6.2 cause.

### VPC Reachability Analyzer — path & routing
Reachability Analyzer runs a static path analysis between a source and destination, reporting `Reachable` or `Not reachable` and the exact component that blocks the path. This directly exposes:
- **Routing issues (6.2.8):** a missing or misconfigured route table entry is flagged as the blocking component (e.g., no route to NAT/internet gateway).
- **Network device misconfiguration (6.2.4):** a security group or NACL rule that denies the flow is shown as the blocker.
- **Switching/VLAN (6.2.9):** subnet/ENI placement issues surface as unreachable paths between expected peers.
- **IP addressing (6.2.7):** overlapping or exhausted CIDRs that make a path ambiguous are revealed by the analysis.

### VPC Flow Logs — traffic visibility
Flow Logs capture `srcaddr`, `dstaddr`, `action` (ACCEPT/REJECT), `packets`, `bytes`, and `tcp-flags` to an S3/CloudWatch destination. This exposes:
- **Bandwidth/throughput (6.2.3):** low `bytes`/`packets` counts and stalled flows reveal a saturated or throttled link.
- **Device misconfig (6.2.4):** `action=REJECT` on the expected port pinpoints the SG/NACL dropping traffic.
- **Latency (6.2.2) inference:** timing gaps between request/response flows indicate delay (pair with CloudWatch metrics).
- **IP addressing (6.2.7):** flows to/from unexpected addresses expose overlap or scope issues.

### CloudWatch Logs — service & application signals
CloudWatch Logs hold DNS resolver logs, NAT gateway flow, DHCP, and application/ALB access logs that surface the Layer-7 and service symptoms:
- **DNS/DHCP/NTP/NAT/HTTP (6.2.1):** resolver failures, failed lease events, clock-skew auth errors, NAT "cannot allocate" errors, and ALB 4xx/5xx access logs.
- **Protocol incompatibility / deprecation (6.2.5/6.2.6):** TLS handshake-failure and cipher-mismatch entries in app/load-balancer logs.
- **Latency (6.2.2):** ALB target response-time metrics and slow request logs.

### Cause → AWS service table

| 6.2 Cause | Where you SEE it (AWS) | Where you FIX it (AWS) |
|---|---|---|
| Service unavailability (6.2.1) | CloudWatch Logs (DNS/NAT/DHCP/ALB 4xx-5xx) | Fix Route 53 / DHCP options / NAT / SG |
| Latency (6.2.2) | CloudWatch metrics, Flow Log timing | Co-locate, ElastiCache, ALB tuning |
| Bandwidth (6.2.3) | Flow Logs bytes, CW network metrics | Upgrade instance tier / link, parallelize |
| Device misconfig (6.2.4) | Reachability Analyzer, Flow Logs REJECT | Edit SG/NACL/LB/route table |
| Protocol incompat (6.2.5) | CloudWatch app/ALB TLS logs | Align TLS/cipher on both ends |
| Deprecation (6.2.6) | Announced cutoff; client failures | Migrate clients to supported protocol |
| IP addressing (6.2.7) | VPC CIDR config, launch errors | Resize subnet / re-IP overlap |
| Routing (6.2.8) | Reachability Analyzer blocker | Add/correct route table entry |
| Switching/VLAN (6.2.9) | Subnet/ENI placement, Analyzer | Fix VLAN tag / access-trunk port |

### Cross-cutting reminders
- **Reachability Analyzer** = "can A reach B, and what blocks it" — your first stop for routing/SG/NACL/overlap questions.
- **Flow Logs** = "what traffic actually flowed and what was dropped" — your evidence for bandwidth and device-misconfig causes.
- **CloudWatch Logs** = "what did the service report" — your source for DNS/NAT/DHCP/HTTP/protocol symptoms.
- Workflow: run Reachability Analyzer (path) → read Flow Logs (drops/volume) → read CloudWatch Logs (service errors).

---

## SECTION 6 — PRACTICE QUESTIONS

1. New instances boot with 169.254.x.x addresses and cannot reach the network, though existing instances work. What is the most likely cause?
   - **A.** Missing route to the internet gateway
   - **B.** DHCP failure (6.2.1)
   - **C.** DNS resolution error
   - **D.** Network overlap

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** 169.254.x.x is an APIPA self-assigned address — the instance never got a DHCP lease, so DHCP service is failing (6.2.1).

**Why A is wrong:** A missing route blocks internet reachability but still leaves a valid private IP from DHCP.
**Why C is wrong:** DNS failure breaks name resolution, not IP assignment — the instance would still have a real IP.
**Why D is wrong:** Network overlap causes peering/VPN routing collisions, not self-assigned APIPA addresses.

</details>

2. `ping 8.8.8.8` succeeds but `ping google.com` fails. What is the cause?
   - **A.** Routing issue
   - **B.** Firewall blocking all traffic
   - **C.** DNS failure (6.2.1)
   - **D.** Bandwidth exhaustion

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** IP connectivity works (8.8.8.8 replies) but name resolution fails — that's a DNS failure (6.2.1), not a path/routing problem.

**Why A is wrong:** A routing issue would also break the raw IP ping to 8.8.8.8.
**Why B is wrong:** A firewall blocking all traffic would stop the successful 8.8.8.8 ping too.
**Why D is wrong:** Bandwidth exhaustion slows/stalls transfers, it doesn't cause "name resolution" failure.

</details>

3. An app server cannot connect to an RDS instance in the same VPC; both are running and connections time out. Most likely cause?
   - **A.** Missing route table entry
   - **B.** Network device misconfiguration — security group (6.2.4)
   - **C.** IP scope exhaustion
   - **D.** Latency

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Both resources run but the connection times out — almost always a dropped SG/NACL/firewall rule (device misconfiguration, 6.2.4); use Reachability Analyzer to name the blocker.

**Why A is wrong:** A missing route would also break reachability to the running app itself, not just the DB connection.
**Why C is wrong:** Scope exhaustion blocks new IP assignment, not a connection between two already-running resources.
**Why D is wrong:** Latency makes things slow, not time out completely between co-located VPC resources.

</details>

4. A web page loads in 3 seconds because the database is in another region, but large file uploads are fast. Cause?
   - **A.** Bandwidth/throughput issue (6.2.3)
   - **B.** Routing issue
   - **C.** Latency (6.2.2)
   - **D.** Protocol incompatibility

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Slow per-request responses (DB in another region adds cross-region RTT) with fine bulk transfer = latency (6.2.2), not a throughput cap.

**Why A is wrong:** Bandwidth/throughput limits would also slow the large file uploads, which are fast here.
**Why B is wrong:** A routing issue would break or black-hole traffic, not just add steady per-request delay.
**Why D is wrong:** Protocol incompatibility fails the handshake entirely, not "slow but works."

</details>

5. A 500 GB nightly sync across a VPN takes 9 hours and stalls. Most likely cause?
   - **A.** Latency (6.2.2)
   - **B.** Bandwidth/throughput (6.2.3)
   - **C.** DNS failure
   - **D.** DHCP failure

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A large transfer that crawls/stalls is a bandwidth/throughput bottleneck (6.2.3) — the pipe is too small for the volume; upgrade tier or schedule off-peak.

**Why A is wrong:** Latency is per-packet delay (slow responses), not a big transfer that never finishes.
**Why C is wrong:** DNS failure breaks name resolution, not a bulk data sync that starts and stalls.
**Why D is wrong:** DHCP failure means no IP at all, not a slow transfer of existing traffic.

</details>

6. After a security policy enforces TLS 1.2 minimum, a legacy agent logging "handshake failure" stops connecting. Cause?
   - **A.** Protocol deprecation (6.2.6)
   - **B.** Protocol incompatibility (6.2.5)
   - **C.** Firewall misconfiguration
   - **D.** Routing issue

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The agent and server now share no common TLS version — a live protocol incompatibility (6.2.5); align the TLS/cipher on both ends.

**Why A is wrong:** Deprecation is a scheduled provider-wide removal affecting everyone; here it's two ends that don't agree on version.
**Why C is wrong:** A firewall misconfig would block the connection, not produce a TLS handshake-version failure.
**Why D is wrong:** A routing issue black-holes traffic, not a TLS version mismatch at the handshake.

</details>

7. On a provider's announced cutoff date, all TLS 1.0 clients fail while TLS 1.2 clients work. Cause?
   - **A.** Protocol incompatibility (6.2.5)
   - **B.** Protocol deprecation (6.2.6)
   - **C.** Device misconfiguration
   - **D.** Latency

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A scheduled provider-wide removal of the old protocol that hits everyone still using it = protocol deprecation (6.2.6); migrate clients to a supported version.

**Why A is wrong:** Incompatibility is two live ends disagreeing with no announced cutoff; this is a provider date affecting all old clients.
**Why C is wrong:** Device misconfiguration is an SG/NACL/firewall rule, not a protocol-version cutoff.
**Why D is wrong:** Latency is delay, not a universal "old clients fail on a date" cutoff.

</details>

8. A subnet can no longer launch instances, returning "insufficient free addresses." Cause?
   - **A.** Network overlap
   - **B.** Missing route
   - **C.** IP scope exhaustion (6.2.7)
   - **D.** VLAN misconfiguration

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** "Insufficient free addresses" means the subnet/DHCP pool is full — IP scope exhaustion (6.2.7); add a CIDR or enlarge the subnet.

**Why A is wrong:** Network overlap causes colliding CIDRs/peering failure, not "no free addresses."
**Why B is wrong:** A missing route blocks pathing, not IP assignment.
**Why D is wrong:** VLAN misconfiguration isolates a segment at L2, not exhausts the IP pool.

</details>

9. A VPC peering fails because both VPCs use 10.0.0.0/16. Cause?
   - **A.** Missing route
   - **B.** Network overlap (6.2.7)
   - **C.** Bandwidth issue
   - **D.** Deprecation

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Two VPCs with the same CIDR make routing ambiguous so peering can't connect — network overlap (6.2.7); re-address one side with a non-overlapping CIDR.

**Why A is wrong:** A missing route breaks an existing path, not the CIDR collision that blocks peering setup.
**Why C is wrong:** Bandwidth is throughput, unrelated to CIDR overlap blocking peering.
**Why D is wrong:** Deprecation retires a protocol/feature, not a CIDR addressing choice.

</details>

10. Private-subnet instances cannot reach the internet, but public-subnet instances can, with identical security groups. Cause?
   - **A.** Security group block
   - **B.** Missing route to NAT gateway (6.2.8)
   - **C.** DNS failure
   - **D.** DHCP failure

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** With identical SGs the only difference is the route table — the private subnet is missing the `0.0.0.0/0 → NAT GW` route, so it has no path to the internet (routing issue 6.2.8).

**Why A is wrong:** SGs are identical between subnets, so an SG block would hit both, not just the private one.
**Why C is wrong:** DNS failure breaks name resolution, not all internet reachability, and would not be specific to the private subnet.
**Why D is wrong:** DHCP failure means no IP at all, not "running but can't reach internet."

</details>

11. Traffic destined for an on-prem network is being sent to the internet instead. Cause?
   - **A.** Missing route
   - **B.** Misconfigured route target (6.2.8)
   - **C.** Protocol deprecation
   - **D.** Scope exhaustion

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A route exists but points to the wrong target (igw instead of the VPN/transit gateway) — a misconfigured route (6.2.8); correct the route target.

**Why A is wrong:** A missing route would black-hole/on-prem traffic, not send it to the internet; here a route is clearly present.
**Why C is wrong:** Protocol deprecation is about retired protocols, not route targets.
**Why D is wrong:** Scope exhaustion is out-of-IPs, unrelated to traffic being misrouted.

</details>

12. A device is on the wrong network segment and can't see its VLAN peers though IP config is correct. Cause?
   - **A.** Routing issue
   - **B.** Switching/VLAN misconfigured tag (6.2.9)
   - **C.** Bandwidth issue
   - **D.** DNS failure

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Correct L3/IP but isolated on the wrong segment = a Layer-2 VLAN tag or access/trunk misconfiguration (6.2.9); fix the tag/port mode.

**Why A is wrong:** A routing issue is L3 pathing; here IP config is correct and the problem is segment placement.
**Why C is wrong:** Bandwidth is throughput, unrelated to VLAN segment isolation.
**Why D is wrong:** DNS failure breaks name resolution, not L2 segment membership.

</details>

13. Authentication to a service fails intermittently with clock-skew errors, though connectivity is fine. Cause?
   - **A.** NTP failure (6.2.1)
   - **B.** DNS failure
   - **C.** Routing issue
   - **D.** Bandwidth issue

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Clock-skew breaking time-based auth = NTP failure (6.2.1) — the hosts' clocks drifted out of sync; repoint NTP and sync.

**Why B is wrong:** DNS failure breaks name resolution, not time-based auth with clock-skew errors.
**Why C is wrong:** A routing issue blocks paths, not authentication; connectivity is fine here.
**Why D is wrong:** Bandwidth is throughput, unrelated to clock skew.

</details>

14. Private instances cannot reach the public internet even though a NAT gateway exists. Most likely?
   - **A.** DNS resolution
   - **B.** Missing/misconfigured route to NAT (6.2.8) or SG/NACL (6.2.4)
   - **C.** IP scope exhaustion
   - **D.** Protocol deprecation

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The NAT exists but traffic still can't egress — either the private route table lacks the NAT route (6.2.8) or a device (SG/NACL, 6.2.4) blocks it; verify both.

**Why A is wrong:** DNS failure would only break name resolution, not all egress to the internet.
**Why C is wrong:** Scope exhaustion is out-of-IPs, not a pathing/device block to an existing NAT.
**Why D is wrong:** Protocol deprecation retires a protocol, not egress routing to a NAT.

</details>

15. An ALB returns 503 Service Unavailable for a backend. This is a signal of which sub-objective?
   - **A.** DHCP (6.2.1)
   - **B.** HTTP status code — service unavailability (6.2.1)
   - **C.** Latency (6.2.2)
   - **D.** Routing (6.2.8)

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A 5xx is an HTTP status-code symptom of network service unavailability (6.2.1) — the backend/service is down or unreachable.

**Why A is wrong:** DHCP is about IP lease assignment, not an HTTP 503 from an ALB.
**Why C is wrong:** Latency is slowness, not a 503 service-unavailable status.
**Why D is wrong:** Routing issues black-hole traffic; a 503 is an HTTP-layer status, not a missing route.

</details>

16. A VPN tunnel shows healthy but no traffic flows between overlapping address spaces. Cause?
   - **A.** Bandwidth
   - **B.** Network overlap (6.2.7)
   - **C.** Protocol incompatibility
   - **D.** DHCP

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The tunnel is up but overlapping CIDRs make the path ambiguous, so traffic can't route — network overlap (6.2.7); re-IP one side.

**Why A is wrong:** Bandwidth is throughput; the tunnel is healthy, the problem is addressing ambiguity.
**Why C is wrong:** Protocol incompatibility fails the handshake/negotiation, not routing between overlapping spaces.
**Why D is wrong:** DHCP is IP assignment, unrelated to overlapping CIDRs blocking a VPN.

</details>

17. Two systems open a TCP connection but the encrypted handshake resets immediately. Cause?
   - **A.** Routing issue
   - **B.** Protocol incompatibility (6.2.5)
   - **C.** Scope exhaustion
   - **D.** VLAN misconfiguration

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The TCP path works (connection opens) but the protocol/cipher negotiation fails and resets — protocol incompatibility (6.2.5); align TLS/cipher on both ends.

**Why A is wrong:** A routing issue would prevent the TCP connection from opening at all.
**Why C is wrong:** Scope exhaustion is out-of-IPs, unrelated to a handshake reset on an open connection.
**Why D is wrong:** VLAN misconfiguration isolates a segment at L2, not a TLS negotiation reset.

</details>

18. A connection to a database times out and traceroute ends at the subnet gateway with nothing beyond. Cause?
   - **A.** Missing/misconfigured route (6.2.8)
   - **B.** DNS failure
   - **C.** DHCP failure
   - **D.** Bandwidth

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Traffic dies at the gateway with no next hop = a missing or misconfigured route (6.2.8); add/correct the route to the destination.

**Why B is wrong:** DNS failure would show as name-resolution errors, not a traceroute dying at the gateway.
**Why C is wrong:** DHCP failure means no IP; here the connection is established then times out at the gateway.
**Why D is wrong:** Bandwidth limits throughput, not a hard "nothing beyond the gateway" stop.

</details>

19. A newly attached ENI can't reach peers in its intended subnet though its IP/subnet is correct. Cause?
   - **A.** Routing
   - **B.** Switching/VLAN issue (6.2.9)
   - **C.** DNS
   - **D.** Deprecation

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Correct L3 (IP/subnet) but isolated from its peers = a Layer-2 VLAN tag/access-trunk misconfiguration (6.2.9) at the ENI's placement.

**Why A is wrong:** Routing is L3 pathing; here the IP/subnet is correct, so it's a segment/VLAN issue.
**Why C is wrong:** DNS failure breaks name resolution, not L2 reachability between correct-IP peers.
**Why D is wrong:** Deprecation retires a protocol/feature, not ENI segment placement.

</details>

20. An application returns 404 Not Found for an API path that exists on another environment. Under 6.2 this is classified as:
   - **A.** DNS failure (6.2.1)
   - **B.** HTTP status code — client error (6.2.1)
   - **C.** Latency
   - **D.** Bandwidth

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A 4xx is an HTTP status-code symptom of network service unavailability (6.2.1) on the client/request side — the requested path/resource isn't served here.

**Why A is wrong:** DNS failure would prevent reaching the host, not return a 404 for a reachable API path.
**Why C is wrong:** Latency is delay, not a 404 client-error status.
**Why D is wrong:** Bandwidth is throughput, unrelated to a 404 response.

</details>
