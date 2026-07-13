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

**Q1.** Which service connects an on-premises data center to a VPC over an encrypted tunnel on the public internet?
A. AWS Direct Connect
B. AWS Site-to-Site VPN
C. VPC Peering
D. Transit Gateway
**Answer:** B — A Site-to-Site VPN uses an encrypted internet tunnel for hybrid connectivity.

**Q2.** A company needs predictable, low-latency, 10 Gbps connectivity that bypasses the internet. Which should they choose?
A. Client VPN
B. VPC Peering
C. AWS Direct Connect
D. CloudFront
**Answer:** C — Direct Connect is a dedicated physical circuit with predictable performance.

**Q3.** Which VPC component lets two VPCs in different accounts communicate using private IPs?
A. Internet Gateway
B. VPC Peering
C. NAT Gateway
D. Security Group
**Answer:** B — VPC Peering privately connects VPCs across accounts/regions via private IPs.

**Q4.** For managing 25 VPCs and two on-prem links with central routing, which is the best fit?
A. VPC Peering mesh
B. Transit Gateway
C. Static routes only
D. NACLs
**Answer:** B — Transit Gateway uses hub-and-spoke, scaling far better than peering meshes.

**Q5.** A public subnet is best described as:
A. A subnet with no route table
B. A subnet that has a route to an internet gateway
C. A subnet that cannot reach the internet
D. A subnet limited to one instance
**Answer:** B — Public subnets route 0.0.0.0/0 to an internet gateway.

**Q6.** Which Layer 7 service routes based on URL path and terminates TLS?
A. Network Load Balancer
B. Application Load Balancer
C. NAT Gateway
D. CDN
**Answer:** B — ALB operates at Layer 7 with path/host routing and TLS termination.

**Q7.** An NLB is preferred over an ALB when you need:
A. URL-based routing
B. Millions of TCP connections with low latency and client IP preservation
C. WAF integration
D. Header rewriting
**Answer:** B — NLB is Layer 4, high throughput, preserves client IP, ultra-low latency.

**Q8.** Which managed service both load balances HTTP traffic and provides a Web Application Firewall?
A. Application Gateway
B. Network Load Balancer
C. Transit Gateway
D. Route Table
**Answer:** A — An Application Gateway bundles Layer-7 load balancing with WAF.

**Q9.** A global CDN primarily improves:
A. Database write speed
B. Latency and offload for users far from origin
C. VLAN segmentation
D. BGP convergence
**Answer:** B — CDNs cache content at edge locations near users, reducing latency.

**Q10.** Which firewall is stateful and applied at the instance (ENI) level?
A. NACL
B. Security Group
C. Route Table
D. Transit Gateway
**Answer:** B — Security Groups are stateful and attached to instances/ENIs.

**Q11.** A subnet-level, stateless firewall that evaluates inbound and outbound rules separately is a:
A. Security Group
B. Network ACL (NACL)
C. NAT Gateway
D. Internet Gateway
**Answer:** B — NACLs are stateless and operate at the subnet boundary.

**Q12.** Which protocol is used to advertise on-prem routes over Direct Connect dynamically?
A. OSPF
B. Static only
C. BGP
D. VLAN tagging
**Answer:** C — BGP exchanges routes between autonomous systems over Direct Connect.

**Q13.** SDN is best described as:
A. Physical switch stacking
B. Separating control plane from data plane, centrally managed by software
C. A type of firewall
D. A routing protocol
**Answer:** B — SDN decouples control/data planes and is software-controlled (basis of VPCs).

**Q14.** A VLAN is used to:
A. Replace BGP
B. Logically segment a network regardless of physical location
C. Terminate TLS
D. Cache content at the edge
**Answer:** B — VLANs logically segment broadcast domains independent of physical layout.

**Q15.** A static route is best when:
A. You need automatic failover across many paths
B. The path is simple, controlled, and predictable
C. You must use BGP
D. You want dynamic discovery
**Answer:** B — Static routes are manual, simple, and predictable for known paths.

**Q16.** What does a route table control?
A. Firewall rules per instance
B. Where traffic from a subnet is directed based on destination
C. CDN edge placement
D. VLAN IDs
**Answer:** B — Route tables map destinations to targets for subnet traffic flow.

**Q17.** A private subnet typically sends outbound internet traffic through a:
A. Internet Gateway
B. NAT Gateway (via static route)
C. Transit Gateway only
D. Direct Connect
**Answer:** B — Private subnets use a NAT Gateway (often via a static/default route) for outbound.

**Q18.** Which AWS service maps to "Dedicated connection" in the exam objectives?
A. VPN
B. Direct Connect
C. CloudFront
D. ALB
**Answer:** B — Dedicated connections map to AWS Direct Connect.

**Q19.** Mapping the generic "Application gateway" to AWS yields:
A. Network Firewall
B. Application Load Balancer (ALB)
C. Transit Gateway
D. VPC Peering
**Answer:** B — AWS has no separate App Gateway; it maps to ALB.

**Q20.** For global content delivery (CDN) on AWS, which service is used?
A. CloudFront
B. Direct Connect
C. Security Group
D. Route Table
**Answer:** A — Amazon CloudFront is the AWS CDN.
