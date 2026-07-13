## SECTION 1 — Exam Objectives

**Objective 1.3 — Explain cloud networking concepts.** (CompTIA Cloud+ CV0-004, Domain 1: Cloud Architecture)

You must describe how cloud environments are connected, segmented, and protected at the network layer (public reach vs. private isolation). Four knowledge clusters:

- **Public and private connections** — VPN (encrypted internet tunnel) and Dedicated connections (private physical circuit) securely link on-prem/remote networks to the cloud.
- **Virtual Private Cloud (VPC)** — an isolated, software-defined network you build in a provider; covers Peering, Transit Gateway, and Subnets.
- **Routing and switching** — directs and segments traffic: VLAN, SDN, BGP, Static routes, Route tables.
- **Network functions, components, and services** — managed building blocks for app traffic + security: ALB, NLB, Application Gateway, CDN, Firewalls.

Exam focus: *what each concept is, when to use it, and how it differs from alternatives* — many questions are scenario-based.

---

## SECTION 2 — EXAM KNOWLEDGE

### Public and Private Connections to the Cloud

**Virtual Private Network (VPN)**
- **Definition:** Encrypted tunnel over the public internet linking on-prem/remote sites to a cloud VPC.
- **Why it matters:** Cheapest secure hybrid connectivity; ideal for dev/test and low-to-moderate bandwidth.
- **Analogy:** A locked armored car on public streets — visible but unreadable.
- **Example:** AWS Site-to-Site VPN lets office servers privately reach databases in a cloud VPC.

**Dedicated Connections**
- **Definition:** Private physical circuit (fiber) bypassing the internet, linking your site directly to the provider.
- **Why it matters:** Predictable, low-latency, high-bandwidth; immune to internet congestion.
- **Analogy:** A private bridge built only for you, not the shared highway.
- **Example:** AWS Direct Connect (1/10 Gbps) for latency-sensitive trading and compliance.

### Virtual Private Cloud (VPC)

**Virtual Private Cloud (VPC)**
- **Definition:** Logically isolated network you define in a provider (IP ranges, subnets, route tables, gateways).
- **Why it matters:** Private address space + control over segmentation, routing, security — the basis of cloud networking.
- **Analogy:** A rented, fenced plot inside a shared campus; you decide what's built and who enters.
- **Example:** A 10.0.0.0/16 VPC with web servers in public subnets, DBs in private subnets.

**Peering**
- **Definition:** Privately connects two VPCs over the provider's network using private IPs.
- **Why it matters:** Lets separate VPCs (any account/region) talk without the internet or a VPN.
- **Analogy:** A door knocked through the wall between two adjacent apartments.
- **Example:** A production VPC peers with a logging VPC for private-IP log collection.

**Transit Gateway**
- **Definition:** A hub connecting many VPCs and on-prem networks through one gateway.
- **Why it matters:** Replaces unscalable mesh peering with easy hub-and-spoke routing.
- **Analogy:** A central train station where branch lines meet instead of tracks between every town.
- **Example:** An enterprise attaches 20 VPCs and two VPNs to one Transit Gateway.

**Subnets**
- **Definition:** An IP range within a VPC, usually per Availability Zone, separating tiers (public vs private).
- **Why it matters:** Enables tier isolation, AZ fault tolerance, granular routing/firewall rules.
- **Analogy:** Rooms in your plot — the public room faces the street, the private room is in back.
- **Example:** Public subnet hosts a load balancer; private subnet hosts app servers with no internet route.

### Routing and Switching

**Virtual Local Area Network (VLAN)**
- **Definition:** Logical segmentation of a physical network, grouping devices regardless of location.
- **Why it matters:** Segmentation and security without separate physical switches.
- **Analogy:** Painting desks "blue" vs "red" so they use separate intercoms in one building.
- **Example:** Voice on VLAN 100, data on VLAN 200 to prioritize and isolate phones.

**Software-Defined Network (SDN)**
- **Definition:** Separates the control plane (decisions) from the data plane (forwarding), managed centrally.
- **Why it matters:** Programmable, automated, elastic networks — the model behind cloud VPCs.
- **Analogy:** Replacing manual switches with one app controlling every light.
- **Example:** A VPC uses SDN overlays so you create/delete networks in seconds via API.

**Border Gateway Protocol (BGP)**
- **Definition:** Dynamic routing protocol exchanging routes between autonomous systems and over hybrid links.
- **Why it matters:** Required to advertise on-prem routes over Direct Connect; enables resilient multi-path routing.
- **Analogy:** The postal master directory telling every office reachable regions and best paths.
- **Example:** BGP over Direct Connect advertises your 192.168.0.0/16 into the VPC route table.

**Static Routes**
- **Definition:** A manually configured path telling a router where to send traffic for a destination.
- **Why it matters:** Simple, predictable; good when you control both ends and need no discovery.
- **Analogy:** A handwritten note taped to the router: "Send warehouse mail via Road A."
- **Example:** A static route sends 0.0.0.0/0 to a NAT appliance so private subnets reach the internet.

**Route Tables**
- **Definition:** A set of destination→target rules directing where subnet traffic goes.
- **Why it matters:** The core mechanism controlling east-west and north-south VPC traffic flow.
- **Analogy:** The building directory mapping each destination floor to the right elevator.
- **Example:** A private subnet route table sends 10.0.0.0/16 locally and 0.0.0.0/0 to a NAT gateway.

### Network Functions, Components, and Services

**Application Load Balancer (ALB)**
- **Definition:** Layer 7 (HTTP/HTTPS) balancer routing by URL path, host, or headers.
- **Why it matters:** Smart distribution, TLS termination, microservice routing.
- **Analogy:** A receptionist sending "/images" to photos and "/api" to backend.
- **Example:** Route example.com/shop to store, example.com/blog to CMS by path.

**Network Load Balancer (NLB)**
- **Definition:** Layer 4 (TCP/UDP) balancer handling millions of requests/sec with ultra-low latency.
- **Why it matters:** Extreme performance, non-HTTP protocols, static IPs, client-IP preservation.
- **Analogy:** A high-speed sorter reading only the envelope address, not the letter.
- **Example:** A gaming server or DB proxy fronted by an NLB for raw TCP throughput.

**Application Gateway**
- **Definition:** Provider-managed Layer-7 appliance: load balancing + WAF, SSL offload, URL routing.
- **Why it matters:** Bundles security (WAF) and routing in one managed service.
- **Analogy:** A bouncer + receptionist who checks IDs (WAF) and directs guests.
- **Example:** An ALB fronting a web farm with WAF blocking SQL injection.

**Content Delivery Network (CDN)**
- **Definition:** Globally distributed cache of content (images, video, scripts) served from edge locations.
- **Why it matters:** Reduces latency, offloads origin, improves availability/scale for global users.
- **Analogy:** Local convenience stores stocking popular items so you skip the factory.
- **Example:** CloudFront caches a video near Tokyo and Frankfurt, slashing load times.

**Firewalls**
- **Definition:** Traffic filters by port/IP/protocol: Security Groups (instance, stateful), NACLs (subnet, stateless), Network Firewall (managed, deep inspection).
- **Why it matters:** Enforce least-privilege access and block unauthorized/malicious traffic.
- **Analogy:** A border checkpoint inspecting every passport and cargo before entry.
- **Example:** SG allows 443 anywhere, 22 only from admin IP; NACL adds subnet-wide deny rules.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Hybrid E-commerce (VPN + ALB):** On-prem inventory DB + cloud web tier linked by Site-to-Site VPN; an ALB spreads shopper traffic across public-subnet web instances in two AZs with TLS termination. *Takeaway:* VPN = secure hybrid reach; ALB = resilient L7 distribution.
- **Low-Latency Trading (Direct Connect + Transit Gateway):** 10 Gbps Direct Connect with BGP advertises routes; VPC + on-prem link attach to a Transit Gateway also linking a DR VPC. *Takeaway:* Dedicated beats VPN for latency/bandwidth; TGW scales hub-and-spoke.
- **Global Streaming (CDN + NLB):** CloudFront caches popular streams at the edge; an NLB fronts the transcode fleet for raw L4 TCP performance and static IPs. *Takeaway:* CDN cuts global latency; NLB handles high-throughput non-HTTP tiers.
- **Multi-Account Isolation (VPC Peering + Firewalls):** Dev/staging/prod VPCs per account; prod peers with a shared-services VPC; SGs + NACLs + managed Network Firewall enforce least privilege. *Takeaway:* Peering connects isolated VPCs privately; layered firewalls enforce least privilege.
- **SDN Auto-Scaling Web Farm (Route Tables + Static Routes):** SDN VPC spins subnets on demand; public subnets route to an internet gateway, private subnets use a static route to a NAT gateway; route tables block inbound internet to back-end subnets. *Takeaway:* SDN = programmatic networks; route tables + static routes define what reaches the internet.

---

## SECTION 4 — COMPARISON TABLES

| Feature | VPN (Site-to-Site) | Dedicated Connection (Direct Connect) |
|---|---|---|
| Underlying path | Public internet (encrypted tunnel) | Private physical circuit |
| Cost | Low / pay for data | High (port + cross-connect) |
| Bandwidth | Moderate (up to ~1.25 Gbps typical) | High (1/10/100 Gbps) |
| Latency | Variable (internet dependent) | Predictable / low |
| Setup time | Fast (software config) | Weeks (fiber install) |
| Best for | Dev/test, low budget, hybrid start | Prod, latency-sensitive, big data |

| Feature | Application Load Balancer (ALB) | Network Load Balancer (NLB) |
|---|---|---|
| OSI layer | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP) |
| Routing basis | URL path, host, headers | IP / port / protocol |
| Performance | High | Very high / millions pps |
| Client IP | Via X-Forwarded-For | Preserved natively |
| TLS termination | Yes | Yes (offload) |
| Best for | Web apps, microservices | Gaming, databases, raw TCP |

| Feature | VPC Peering | Transit Gateway |
|---|---|---|
| Topology | Point-to-point (mesh) | Hub-and-spoke |
| Scalability | Poor at scale (n*(n-1)/2 links) | Excellent (many attachments) |
| Cross-region | Supported | Supported |
| Central inspection | No (direct) | Yes (via hub routing) |
| Best for | Few VPCs | Many VPCs / large orgs |

| Feature | Traditional VLAN | Software-Defined Network (SDN) |
|---|---|---|
| Control | Per-switch config | Centralized controller |
| Provisioning | Manual / CLI | API / automated |
| Scope | Single physical site | Cloud-wide / overlay |
| Elasticity | Low | High (on demand) |
| Best for | On-prem segmentation | Cloud VPCs, dynamic nets |

| Feature | Security Group | NACL | Network Firewall |
|---|---|---|---|
| Scope | Instance / ENI | Subnet | VPC (managed) |
| Stateful? | Yes (stateful) | No (stateless) | Yes (stateful) |
| Rule direction | Inbound + outbound | Inbound + outbound | Inbound + outbound |
| Inspection | Basic (port/IP) | Basic (port/IP) | Deep (L3-L7, IDS) |
| Default | Allow all out, deny in | Allow all (editable) | Deny by default |

| Feature | Static Route | BGP (Dynamic) |
|---|---|---|
| Configuration | Manual entries | Automatic exchange |
| Adaptation | None (fixed) | Self-healing / reconverge |
| Overhead | Minimal | Higher (protocol) |
| Use case | Simple, controlled paths | Hybrid / multi-path / large |
| Failure response | Manual fix | Auto reroute |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-only mapping of generic Cloud+ networking terms to concrete AWS services:

| Generic Term (Exam) | AWS Service / Construct |
|---|---|
| VPC | Amazon VPC (Virtual Private Cloud) |
| VPN | AWS Site-to-Site VPN / AWS Client VPN |
| Dedicated (connection) | AWS Direct Connect |
| Peering | VPC Peering |
| Transit gateway | AWS Transit Gateway |
| Subnets | VPC Subnets |
| Application load balancer | Application Load Balancer (ALB) |
| Network load balancer | Network Load Balancer (NLB) |
| Application gateway | Application Load Balancer (ALB) — the AWS L7 load balancer and HTTP router (AWS has no separate "App Gateway" product) |
| CDN | Amazon CloudFront |
| Firewalls | Security Groups / Network ACLs (NACL) / AWS Network Firewall |
| SDN | Amazon VPC (SDN-driven overlay network) |
| BGP | AWS Direct Connect / AWS Transit Gateway (dynamic routing via BGP) |
| Route tables | VPC Route Tables |

---

## SECTION 6 — PRACTICE QUESTIONS

1. A company needs a private, low-latency, dedicated connection from its on-prem data center to its VPC that avoids the public internet. Which is the BEST choice?
   - **A.** Internet Gateway
   - **B.** NAT gateway
   - **C.** AWS Direct Connect
   - **D.** Elastic IP

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Direct Connect provides a dedicated physical link into AWS that bypasses the public internet, lowering latency and jitter.

**Why A is wrong:** IGW is for internet-bound traffic, not private.
**Why B is wrong:** NAT gateway still uses the internet path for egress.
**Why D is wrong:** EIP is just a static public IP, not a private link.

</details>

2. Two VPCs in the same region must privately route to each other without traversing the internet. What connects them?
   - **A.** VPC peering
   - **B.** Internet Gateway
   - **C.** Transit gateway only
   - **D.** NAT instance

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** VPC peering creates a direct private network route between two VPCs; no internet involved.

**Why B is wrong:** IGW exposes traffic to the internet.
**Why C is wrong:** Transit Gateway works but peering is the direct two-VPC answer.
**Why D is wrong:** NAT instance is for egress, not VPC-to-VPC routing.

</details>

3. You must centrally connect many VPCs and on-prem networks through a single hub. Which service fits?
   - **A.** VPC peering mesh
   - **B.** Transit Gateway
   - **C.** Elastic Load Balancer
   - **D.** Route 53

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Transit Gateway is a hub-and-spoke router that scales to many VPCs and on-prem links.

**Why A is wrong:** Peering mesh grows O(n^2) and is unmanageable at scale.
**Why C is wrong:** ELB distributes app traffic, not network routing.
**Why D is wrong:** Route 53 is DNS, not a network hub.

</details>

4. An application needs to distribute incoming HTTP/HTTPS traffic across many healthy EC2 instances. Which do you use?
   - **A.** Network Load Balancer
   - **B.** Application Load Balancer
   - **C.** NAT gateway
   - **D.** Transit Gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** ALB operates at L7 and is designed for HTTP/HTTPS distribution with host/path routing.

**Why A is wrong:** NLB is L4 (TCP/UDP), not HTTP-aware routing.
**Why C is wrong:** NAT gateway is for outbound internet, not LB.
**Why D is wrong:** Transit Gateway routes networks, not app traffic.

</details>

5. A stateless TCP/UDP load balancer that preserves the client source IP and handles millions of requests per second is needed. Which type?
   - **A.** Application Load Balancer
   - **B.** Network Load Balancer
   - **C.** Classic Load Balancer
   - **D.** API Gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** NLB is L4, extremely high throughput, and preserves the client IP at the network layer.

**Why A is wrong:** ALB is L7 and terminates/rewrites the connection.
**Why C is wrong:** Classic is legacy and lower scale.
**Why D is wrong:** API Gateway is for APIs, not raw TCP/UDP LB.

</details>

6. You want to filter traffic at the subnet boundary with rules for inbound and outbound, evaluated in numeric order. Which do you deploy?
   - **A.** Security group
   - **B.** Network ACL
   - **C.** AWS Shield
   - **D.** IAM policy

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** NACLs are stateless, subnet-level, and processed in rule-number order for both directions.

**Why A is wrong:** SGs are instance-level and stateful.
**Why C is wrong:** Shield is DDoS protection, not subnet filtering.
**Why D is wrong:** IAM controls API/identity, not packets.

</details>

7. A stateful, instance-level firewall that automatically allows return traffic for allowed connections is:
   - **A.** Network ACL
   - **B.** Security group
   - **C.** Route table
   - **D.** Internet Gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Security groups are stateful and attached to instances/ENIs; return traffic is allowed automatically.

**Why A is wrong:** NACLs are stateless, requiring explicit return rules.
**Why C is wrong:** Route tables direct traffic, not filter it.
**Why D is wrong:** IGW is an internet entry point, not a firewall.

</details>

8. A subnet's route table sends 0.0.0.0/0 to an internet gateway. The instances cannot reach the internet. The likely cause is:
   - **A.** Missing NAT gateway
   - **B.** No public IP or IGW association on the VPC
   - **C.** Security group too open
   - **D.** Transit Gateway misconfig

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** For internet access via IGW, instances need a public IP and the VPC must be attached to the IGW.

**Why A is wrong:** NAT is for private subnets, not the IGW path.
**Why C is wrong:** An open SG would not block egress.
**Why D is wrong:** Transit Gateway is unrelated to basic IGW egress.

</details>

9. A private subnet needs to download patches from the internet but must not be reachable from outside. Which component enables this?
   - **A.** Internet Gateway
   - **B.** NAT gateway
   - **C.** VPC peering
   - **D.** Direct Connect

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A NAT gateway in a public subnet lets private instances initiate outbound traffic without inbound exposure.

**Why A is wrong:** IGW would make them publicly reachable.
**Why C is wrong:** Peering is VPC-to-VPC only.
**Why D is wrong:** Direct Connect is a private link, not patch egress.

</details>

10. Which DNS service provides private name resolution inside a VPC?
   - **A.** Route 53 public hosted zone
   - **B.** Route 53 private hosted zone
   - **C.** Elastic IP
   - **D.** NAT gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A Route 53 private hosted zone resolves names only within associated VPCs.

**Why A is wrong:** Public zones resolve over the internet.
**Why C is wrong:** EIP is an IP address, not DNS.
**Why D is wrong:** NAT gateway is for egress, not name resolution.

</details>

11. A VPC CIDR is 10.0.0.0/16 but you need to add more addresses. What is TRUE about VPC CIDR size?
   - **A.** You can shrink it anytime
   - **B.** It is fixed at creation and cannot be changed
   - **C.** You can add secondary CIDR blocks
   - **D.** It must be /24 minimum

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** You can associate additional CIDR blocks (secondary) to expand a VPC without recreating it.

**Why A is wrong:** Primary CIDR cannot be shrunk.
**Why B is wrong:** You can add secondary CIDRs, so not fully fixed.
**Why D is wrong:** VPC minimum is /16, not /24.

</details>

12. BGP is used in AWS networking primarily for:
   - **A.** Encrypting VPC traffic
   - **B.** Advertising and exchanging routes over VPN/Direct Connect
   - **C.** DNS resolution
   - **D.** Load balancing

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** BGP exchanges dynamic route advertisements between your network and AWS over VPN or Direct Connect.

**Why A is wrong:** Encryption is IPsec, not BGP.
**Why C is wrong:** DNS is Route 53.
**Why D is wrong:** BGP is routing, not LB.

</details>

13. An SDN (software-defined networking) characteristic most relevant to cloud is:
   - **A.** Fixed hardware-only forwarding
   - **B.** Control plane separated from data plane, programmable via API
   - **C.** No virtual networks
   - **D.** Manual cable patching

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** SDN decouples the control plane from forwarding and lets you program the network via APIs.

**Why A is wrong:** That describes traditional networking.
**Why C is wrong:** Cloud SDN is heavily virtualized.
**Why D is wrong:** Manual patching contradicts SDN.

</details>

14. A VPN connection between on-prem and AWS is established but routes flap. The most likely cause is:
   - **A.** BGP keepalive/ASN mismatch
   - **B.** Security group blocking
   - **C.** S3 misconfig
   - **D.** IAM role

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** VPNs using BGP flap when ASN or keepalive timers mismatch, breaking route advertisement.

**Why B is wrong:** SGs affect instance traffic, not tunnel establishment.
**Why C is wrong:** S3 is unrelated to VPN routing.
**Why D is wrong:** IAM does not govern VPN routes.

</details>

15. You need to inspect all traffic between subnets with L3-L7 filtering, logging, and a managed firewall. Which service?
   - **A.** Network ACL
   - **B.** AWS Network Firewall
   - **C.** Security group
   - **D.** Route 53

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS Network Firewall is a managed, stateful L3-L7 firewall for VPC traffic inspection.

**Why A is wrong:** NACL is stateless L3/L4 only.
**Why C is wrong:** SG is instance-level, not central inspection.
**Why D is wrong:** Route 53 is DNS.

</details>

16. Two subnets in different VPCs need name resolution without exposing DNS to the internet. Best approach:
   - **A.** Public hosted zones
   - **B.** Private hosted zones with VPC association
   - **C.** NAT gateway
   - **D.** Transit Gateway routing only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Private hosted zones associated to each VPC give internal resolution without public exposure.

**Why A is wrong:** Public zones are internet-resolvable.
**Why C is wrong:** NAT does not do DNS.
**Why D is wrong:** TGW routes but does not resolve names.

</details>

17. A connection from on-prem uses IPsec but you notice throughput caps around 1.25 Gbps per tunnel. This is a known limit of:
   - **A.** Direct Connect
   - **B.** Site-to-Site VPN over a single tunnel
   - **C.** Transit Gateway
   - **D.** NAT gateway

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A single AWS Site-to-Site VPN tunnel is capped near 1.25 Gbps; use multiple tunnels or DX for more.

**Why A is wrong:** DX has much higher bandwidth.
**Why C is wrong:** TGW itself is not the 1.25 Gbps cap source.
**Why D is wrong:** NAT gateway has different limits.

</details>

18. An instance in a public subnet has a public IP but still cannot be reached from the internet. Most likely:
   - **A.** NACL allows all
   - **B.** Security group has no inbound rule for the port
   - **C.** Route table missing IGW
   - **D.** Both B and C

<details>
<summary>Reveal Answer</summary>

**Correct: D**

**Why correct:** No inbound SG rule AND/OR missing IGW route both block inbound; both are common causes.

**Why A is wrong:** Open NACL would help, not block.
**Why B is wrong:** Only partial — missing IGW route is also a cause.
**Why C is wrong:** Only partial — missing SG rule is also a cause.

</details>

19. You must log every accepted and rejected packet at the VPC boundary for audit. Which provides this?
   - **A.** VPC Flow Logs
   - **B.** CloudTrail
   - **C.** Security group
   - **D.** NAT gateway

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** VPC Flow Logs capture IP traffic metadata (accepted/rejected) for monitoring and forensics.

**Why B is wrong:** CloudTrail logs API calls, not packet flows.
**Why C is wrong:** SG is a filter, not a log source.
**Why D is wrong:** NAT gateway forwards, does not log flows.

</details>

20. A hub-and-spoke design where each VPC peers only to a central VPC (not to each other) is implemented with:
   - **A.** Full mesh peering
   - **B.** Transit Gateway or centralized peering via a hub
   - **C.** Direct Connect only
   - **D.** NAT instances

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Transit Gateway (or a hub VPC) gives hub-and-spoke so spokes talk only through the center.

**Why A is wrong:** Full mesh connects every VPC to every other.
**Why C is wrong:** DX is a link type, not topology.
**Why D is wrong:** NAT instances are egress, not topology.

</details>
