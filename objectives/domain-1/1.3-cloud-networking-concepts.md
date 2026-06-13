# CompTIA Cloud+ CV0-004 — Domain 1.0 Cloud Architecture
## 📘 Objective 1.3: Cloud Networking Concepts

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 1.0 Domain = 23% of total exam
> **Objective 1.3 Focus:** How cloud networks are built — connections in/out, VPCs, subnets, routing, load balancers, CDNs, and firewalls. The networking backbone of every cloud architecture.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 1.3
### Objective Name: *Explain cloud networking concepts.*

**What this objective means (beginner-friendly):**
Imagine a cloud provider's data center is a giant office building. Networking is the **plumbing that connects everything**:
- How your on-prem office connects to the cloud (VPN, dedicated lines).
- How the building is divided into private suites (VPCs, subnets).
- How traffic flows between suites (peering, transit gateway, routing).
- How the building's mailroom sorts incoming requests (load balancers, gateways).
- How to deliver mail faster to remote customers (CDN).
- How the security guard checks IDs (firewalls, security groups).

The exam will give you a scenario — "Company A has 3 VPCs, 5 on-prem branches, needs HA connectivity" — and you must pick the right architecture.

**Why it matters in the real world:**
- Misconfigured security groups cause most cloud breaches (Capital One 2019).
- Wrong network topology (no transit gateway with 50 VPCs = "spaghetti peering") is a top cause of cloud complexity.
- Load balancers and CDNs are how every modern app scales — exam will test the differences.
- BGP is essential for hybrid and multicloud (CompTIA CV0-004 added this).

**Why CompTIA tests it:**
- Cloud Engineer/Architect roles spend 40%+ of their time on networking decisions.
- Networking is a cross-cutting concern — touches every other domain (security, operations, deployment, troubleshooting).
- CompTIA Cloud+ builds on Network+ — this is the cloud-specific networking layer.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Public vs Private Connections to the Cloud

#### 2.1.1 — Virtual Private Network (VPN)

**Definition:**
An **encrypted tunnel** over the public internet that securely connects two networks (site-to-site) or a user to a network (client VPN). Uses IPsec (for site-to-site) or SSL/TLS (for client VPN).

**Purpose:**
- Securely extend an on-prem network to a cloud VPC without leasing dedicated fiber.
- Cost-effective hybrid connectivity.
- Provide remote users with secure access to cloud resources.

**How it works:**
- **Site-to-Site VPN:** An on-prem VPN endpoint (firewall, router, VPN concentrator) negotiates an IPsec tunnel with a cloud VPN endpoint (e.g., AWS Virtual Private Gateway, Azure VPN Gateway, GCP Cloud VPN gateway). All traffic between the two networks is encrypted.
- **Client VPN:** A user installs VPN client software that creates an encrypted tunnel to a cloud VPN endpoint. The user appears to be inside the cloud VPC.
- Encryption: IPsec (AES-256 typically), IKEv2 for key exchange.
- Uses the public internet — bandwidth is shared with everyone else.

**Advantages:**
- Low cost (no dedicated line).
- Quick to set up (minutes, not weeks).
- Works over any internet connection.
- Strong encryption (IPsec).

**Disadvantages:**
- Performance depends on public internet (variable latency, jitter, packet loss).
- No bandwidth SLA from the cloud provider.
- Single VPN = single point of failure (mitigate with redundant tunnels).
- Egress costs still apply (data leaves the cloud, even over VPN).

**When to use:**
- Small to medium hybrid connectivity.
- Branch offices connecting to cloud.
- Remote users needing access to cloud resources.
- Temporary or low-bandwidth needs.

**When NOT to use:**
- Mission-critical, high-bandwidth, low-latency workloads (use dedicated connection).
- Strict SLA on network performance.

**Provider Examples:**
- **AWS:** Site-to-Site VPN, Client VPN.
- **Azure:** VPN Gateway (policy-based or route-based).
- **GCP:** Cloud VPN (HA VPN for redundancy).

---

#### 2.1.2 — Dedicated Connections

**Definition:**
A **physical, private fiber-optic line** that directly connects an on-premises network to a cloud provider's edge location. **Not** over the public internet.

**Purpose:**
- Provide consistent, low-latency, high-bandwidth hybrid connectivity with predictable performance.
- Reduce egress costs (often cheaper per GB than internet).
- Meet compliance requirements (data doesn't traverse the public internet).

**How it works:**
- A network provider (Equinix, Megaport, Colt, etc.) provisions a cross-connect from a colocation facility to the cloud provider's point of presence.
- The customer signs up with the cloud provider (e.g., AWS Direct Connect, Azure ExpressRoute, GCP Cloud Interconnect).
- Bandwidth options: 1 Gbps, 10 Gbps, 100 Gbps.
- Customer can run BGP over the dedicated connection to dynamically route.

**Advantages:**
- Consistent, predictable network performance.
- Lower latency than VPN (no internet hops).
- Potentially lower data egress cost.
- Can support high bandwidth (10 Gbps, 100 Gbps).
- Doesn't traverse the public internet (compliance benefit).

**Disadvantages:**
- **Expensive** (port fee + per-GB data transfer fee).
- Setup takes weeks/months (cross-connect, fiber provisioning).
- Requires a colocation facility nearby.
- Single point of failure if not redundant (use two connections from different providers).
- Geographic limitations (must be near a provider's edge location).

**When to use:**
- Mission-critical, high-bandwidth hybrid workloads.
- Data center migration to cloud (huge data transfer).
- Latency-sensitive apps (real-time trading, video).
- Compliance (HIPAA, PCI-DSS, FedRAMP) that requires private connectivity.

**When NOT to use:**
- Low bandwidth / low budget.
- Quick, temporary connections (use VPN).
- No nearby colocation facility.

**Provider Examples:**
- **AWS:** Direct Connect (1, 10, 100 Gbps; locations at 100+ colos worldwide).
- **Azure:** ExpressRoute (1, 10, 100 Gbps; via providers like Equinix).
- **GCP:** Cloud Interconnect (Dedicated Interconnect for 10/100 Gbps, Partner Interconnect for 50 Mbps–50 Gbps).

**CompTIA Note:** Many companies combine VPN + Direct Connect for resilience — Direct Connect as primary, VPN as backup.

---

### 2.2 — Virtual Private Cloud (VPC)

**Definition:**
A **logically isolated virtual network** within a cloud provider's infrastructure. You define the IP address range (CIDR block), subnets, route tables, and gateways. Resources in a VPC can communicate with each other as if on the same LAN, isolated from other VPCs.

**Purpose:**
- Provide a private, isolated network environment for your cloud resources.
- Replicate an on-prem network architecture in the cloud.
- Control IP addressing, routing, and security boundaries.

**How it works:**
- Each VPC is defined by a CIDR block (e.g., 10.0.0.0/16 — 65,536 IPs).
- Within a VPC, you create **subnets** (one per AZ typically).
- You attach **gateways**: Internet Gateway (IGW) for public internet access, NAT Gateway for outbound-only, Virtual Private Gateway for VPN, Transit Gateway for VPC-to-VPC.
- You configure **route tables** to control traffic flow.
- You apply **network ACLs (NACLs)** and **security groups** for security.
- VPCs are **region-scoped** (span all AZs in a region).

**Advantages:**
- Complete control over IP space and network topology.
- Logical isolation (your VPC is invisible to other customers).
- Can peer with other VPCs (even across accounts/regions).
- Supports hybrid (via VPN/Direct Connect) and private connectivity.

**Disadvantages:**
- Complexity at scale (many VPCs = management overhead).
- Default VPCs (auto-created) can lead to accidental public exposure.
- Cross-VPC communication needs explicit peering or transit gateway.
- IP exhaustion if CIDR block too small.

**When to use:**
- **Always**, for any workload that needs network isolation.
- Multi-tier apps (web tier in public subnet, DB tier in private subnet).
- Hybrid scenarios (extend on-prem to cloud).
- Multi-tenant SaaS (one VPC per customer, or careful segmentation).

**When NOT to use:**
- For purely serverless workloads that don't need a custom VPC (Lambda can run outside a VPC, but loses VPC access).

**Provider Examples:**
- **AWS:** Amazon VPC.
- **Azure:** Azure Virtual Network (VNet) — closely analogous.
- **GCP:** Google Cloud VPC (global, spans all regions — unique to GCP).

---

### 2.3 — VPC Peering

**Definition:**
A **direct network connection** between two VPCs that allows instances in either VPC to communicate as if they were in the same network. **Non-transitive** — VPC A peered with B, and B peered with C, does NOT mean A can reach C.

**Purpose:**
- Connect VPCs in the same or different accounts, regions, or providers.
- Enable private communication without traversing the public internet.

**How it works:**
- A peering connection is created between two VPCs.
- Each VPC's route table is updated to include routes to the peered VPC's CIDR.
- Traffic flows over the cloud provider's private backbone (not the public internet).
- **Non-transitive:** VPC A → VPC B → VPC C only works if A and C also have a peering connection.

**Advantages:**
- Low latency (private backbone).
- No data transfer through the public internet.
- Simple to set up.
- Cross-account, cross-region support (in most providers).

**Disadvantages:**
- **Not transitive** — large hub-and-spoke topologies become "full mesh" (n × (n-1) / 2 connections), unmanageable.
- No central management or routing policy.
- No multicast support (typically).
- Can be blocked by overlapping CIDR blocks.

**When to use:**
- 2–5 VPCs that need direct communication.
- Cross-account VPC connection (different teams, organizations).
- Cross-region VPC connection (for global apps).

**When NOT to use:**
- Many VPCs needing a hub-and-spoke (use **Transit Gateway**).
- VPCs with overlapping CIDR blocks (CIDR must not overlap).

---

### 2.4 — Transit Gateway

**Definition:**
A **regional network transit hub** that connects VPCs, VPNs, and AWS Direct Connect (or equivalents). Enables transitive routing between all attached networks via a single gateway.

**Purpose:**
- Simplify network topology when you have many VPCs that need to communicate.
- Replace complex peering meshes with a single hub.
- Centralize ingress/egress traffic.

**How it works:**
- Each VPC attaches to the Transit Gateway (one attachment per VPC).
- A route table on the Transit Gateway controls which attachments can reach which.
- Traffic between any two attached VPCs flows through the Transit Gateway.
- Supports transitive routing (unlike peering).
- Can share across accounts (RAM — Resource Access Manager).

**Advantages:**
- **Transitive routing** — VPC A can reach VPC C via the Transit Gateway.
- **Scales to thousands of VPCs.**
- Centralized routing control.
- Cross-account, cross-region support (via peering of Transit Gateways).
- Can attach VPNs and Direct Connect for hybrid.

**Disadvantages:**
- Additional cost (per-GB data processing fee).
- Adds a hop (slight latency increase).
- More complex than direct peering for simple 2-VPC cases.
- Vendor lock-in (proprietary service).

**When to use:**
- 5+ VPCs needing communication.
- Hub-and-spoke architecture (central inspection, central egress).
- Hybrid cloud with multiple VPCs and on-prem.
- Multi-account cloud environments.

**When NOT to use:**
- Only 2 VPCs that need simple connection (peering is cheaper).
- Single-VPC, single-account setups (over-engineering).

**Provider Examples:**
- **AWS:** AWS Transit Gateway.
- **Azure:** Virtual WAN (Azure's hub-and-spoke service).
- **GCP:** Network Connectivity Center (NCC) — GoogleCloud's transit hub.

---

### 2.5 — Subnets

**Definition:**
A **subdivision of a VPC's IP address range** that exists within a single Availability Zone. Resources launched in a subnet are assigned IPs from the subnet's CIDR block.

**Purpose:**
- Group resources by availability requirements, security tier, or function.
- Control routing (public subnets route to IGW, private subnets don't).
- Enforce network segmentation for security.

**How it works:**
- A subnet reserves a CIDR block (e.g., 10.0.1.0/24 = 256 IPs) within a VPC.
- Each subnet is in **one AZ** (not multi-AZ).
- A VPC spans all AZs in a region; subnets are pinned to one AZ each.
- **Public subnet:** Has a route to the Internet Gateway → resources can be reached from the internet (after assigning a public IP).
- **Private subnet:** No route to the Internet Gateway → resources are not directly reachable from the internet.
- **Outbound-only internet access** for private subnets: NAT Gateway (AWS), NAT Gateway (Azure), Cloud NAT (GCP).

**Advantages:**
- Logical separation of workloads (web tier, app tier, DB tier).
- Public/private split for security.
- Pinning to AZ for blast-radius control.
- Granular route table association.

**Disadvantages:**
- Single-AZ failure = subnet is offline (mitigate with multi-AZ deployment).
- IP planning is tricky (hard to resize after creation).
- Too many subnets = management overhead.

**When to use:**
- **Always** for any non-trivial VPC design.
- Multi-tier apps: public subnet for web, private for app, private for DB.
- Segregate by environment: dev, staging, prod in different subnets.
- Compliance (PCI: card data in a separate subnet with stricter rules).

**When NOT to use:**
- Single-purpose VPCs with one resource (overkill).

---

### 2.6 — Routing and Switching

#### 2.6.1 — Route Tables

**Definition:**
A set of rules (routes) that determine where network traffic is directed. Each subnet in a VPC is associated with a route table.

**Purpose:**
- Control traffic flow within a VPC and to/from external networks.
- Implement public vs. private subnet behavior.
- Force traffic through specific gateways (e.g., firewall, NAT, VPN).

**How it works:**
- A route table contains entries: `destination CIDR → target` (e.g., `0.0.0.0/0 → igw-12345`).
- The most specific match wins (longest prefix match).
- Each subnet has exactly one route table association (but a route table can be associated with many subnets).
- The **main route table** is the default for any subnet not explicitly associated.
- **Local route** (e.g., `10.0.0.0/16 → local`) is automatic and intra-VPC.

**Example route table for a public subnet:**
```
10.0.0.0/16   → local          (intra-VPC traffic)
0.0.0.0/0     → igw-12345      (everything else → Internet Gateway)
```

**Example for a private subnet:**
```
10.0.0.0/16   → local
0.0.0.0/0     → nat-12345      (outbound internet via NAT)
10.20.0.0/16  → tgw-12345      (traffic to another VPC via Transit Gateway)
```

**Advantages:**
- Explicit control over traffic paths.
- Enables hub-and-spoke, firewall-in-the-middle designs.
- Route table per subnet = granular policy.

**Disadvantages:**
- Misconfigured routes = downtime or security gaps.
- Many subnets = many route tables to maintain.

**Exam Tip:** A common PBQ is "this app can't reach the internet" — fix the route table (missing 0.0.0.0/0 → IGW for public subnet, or → NAT for private).

---

#### 2.6.2 — Static Routes

**Definition:**
Manually configured routes in a route table. Don't change unless an admin updates them.

**Purpose:**
- Predictable, explicit routing.
- Simple networks with few routes.

**Advantages:**
- Simple, predictable, easy to debug.
- No protocol overhead.

**Disadvantages:**
- Don't adapt to failures (if the target goes down, traffic still goes there).
- Don't scale (hundreds of routes = manual nightmare).
- High operational burden.

**When to use:**
- Small networks, dev environments.
- Specific traffic engineering (force traffic through a specific appliance).

**When NOT to use:**
- Large, dynamic networks (use BGP or other dynamic routing).

---

#### 2.6.3 — Border Gateway Protocol (BGP)

**Definition:**
A **dynamic routing protocol** used to exchange routing information between autonomous systems (AS). The de facto protocol of the internet and the standard for cloud hybrid connectivity.

**Purpose:**
- Automatically learn and advertise routes between networks.
- Adapt to network changes (failover, new paths).
- Multi-cloud and hybrid connectivity.

**How it works:**
- Two BGP peers (e.g., on-prem router and cloud VPN/Direct Connect gateway) establish a TCP session on port 179.
- They exchange routing tables and updates.
- Each peer selects the best path based on BGP attributes (AS path, MED, local preference, etc.).
- Routes are dynamically updated as the network changes.

**Advantages:**
- Dynamic — adapts to failures.
- Scalable (handles thousands of routes).
- Industry standard (works across all providers).
- Enables multi-cloud (BGP peering between cloud and on-prem).

**Disadvantages:**
- Complex to configure.
- Misconfiguration can cause route leaks / hijacks.
- Requires skilled network engineers.

**When to use:**
- Hybrid cloud (on-prem ↔ cloud).
- Multi-cloud connectivity.
- ISP-level routing.

**When NOT to use:**
- Simple intra-VPC routing (use route tables).
- Small networks with few routes.

**CompTIA Angle:** CompTIA added BGP to CV0-004 — expect scenario questions about "using BGP to dynamically route between cloud and on-prem."

---

#### 2.6.4 — Virtual Local Area Network (VLAN)

**Definition:**
A **Layer 2 (data link) network segmentation** technique. VLANs logically separate devices on the same physical switch into different broadcast domains.

**Purpose:**
- Segment a network without separate physical switches.
- Isolate traffic (e.g., finance VLAN, guest VLAN, IoT VLAN).
- Improve security and reduce broadcast storms.

**How it works:**
- Switch ports are tagged with VLAN IDs (1–4094).
- Devices in VLAN 10 cannot directly talk to devices in VLAN 20 without a router.
- VLANs are commonly used in on-prem data centers, and on **cloud provider network appliances** (e.g., virtual firewall, virtual router).

**Advantages:**
- Cost-effective segmentation.
- Reduces broadcast traffic.
- Security isolation.

**Disadvantages:**
- VLAN IDs are limited (4094).
- Misconfiguration (VLAN hopping) can be a security risk.
- Doesn't extend across subnets (Layer 2 boundary).

**When to use:**
- On-prem network segmentation.
- Inside a cloud network virtual appliance (e.g., Cisco CSR in a VPC subnet).
- Tagging traffic in hybrid networks (e.g., 802.1Q trunking over Direct Connect).

**When NOT to use:**
- Pure cloud-native (use security groups + NACLs instead).

---

#### 2.6.5 — Software-Defined Networking (SDN)

**Definition:**
A network architecture where the **control plane** (decides where traffic goes) is **decoupled from the data plane** (forwards traffic) and is implemented in software. The network is programmable via APIs.

**Purpose:**
- Make networks programmable, automated, and centrally managed.
- Enable rapid provisioning and changes via software.
- Decouple network intelligence from physical hardware.

**How it works:**
- A centralized SDN controller (e.g., OpenDaylight, VMware NSX, Azure Virtual WAN, AWS Transit Gateway) defines the network policy.
- Physical or virtual switches/routers enforce the policy.
- Changes are pushed via APIs or a management console.

**Advantages:**
- Programmability (network as code, API-driven).
- Centralized management.
- Faster provisioning.
- Vendor-neutral (OpenFlow).

**Disadvantages:**
- Single point of failure (controller) — must be HA.
- Vendor lock-in (proprietary SDN controllers).
- Complexity in initial setup.

**When to use:**
- Modern cloud networks (all major providers use SDN internally).
- Multi-vendor environments.
- Automation-heavy networks (DevOps).

**When NOT to use:**
- Tiny networks where manual management is fine.

**Provider Examples:**
- **AWS:** VPC is built on SDN (control plane = AWS APIs, data plane = AWS-managed switches).
- **Azure:** Azure Virtual Network Manager (AVNM) is an SDN overlay.
- **GCP:** Andromeda is Google's SDN stack (powers GCP VPC).

---

### 2.7 — Network Functions, Components, and Services

#### 2.7.1 — Application Load Balancer (ALB)

**Definition:**
A **Layer 7 (application layer) load balancer** that routes HTTP/HTTPS (or gRPC, WebSocket) traffic based on content — URL path, host header, query string, HTTP headers.

**Purpose:**
- Distribute web traffic across multiple backend instances.
- Perform SSL/TLS termination.
- Path-based or host-based routing (e.g., `/api` → API servers, `/images` → image servers).
- Layer 7 health checks (HTTP 200 OK).

**How it works:**
- Clients connect to the ALB's DNS name (e.g., `myapp-alb.us-east-1.elb.amazonaws.com`).
- ALB terminates SSL, inspects the request, and routes based on rules.
- Backend targets: EC2 instances, ECS tasks, EKS pods, Lambda functions, IPs.
- Supports sticky sessions, WebSocket, HTTP/2, gRPC.

**Advantages:**
- Intelligent routing (content-based).
- SSL offload (centralized certificate management).
- Auto-scales to handle traffic spikes.
- Integrates with WAF (Web Application Firewall).
- Native integration with containers, Lambda.

**Disadvantages:**
- Layer 7 inspection adds a few ms latency.
- Slightly more expensive than NLB.
- Limited to HTTP/HTTPS/gRPC/WebSocket.

**When to use:**
- Web apps, REST APIs, gRPC services.
- Microservices with multiple paths/hosts.
- Blue-green or canary deployments.
- Any HTTP-based traffic that needs intelligent routing.

**When NOT to use:**
- Pure TCP/UDP traffic (use NLB).
- Extreme low-latency (use NLB).

**Provider Examples:**
- **AWS:** Application Load Balancer (ALB).
- **Azure:** Application Gateway (similar function).
- **GCP:** Application Load Balancer (HTTP(S) Load Balancing).

---

#### 2.7.2 — Network Load Balancer (NLB)

**Definition:**
A **Layer 4 (transport layer) load balancer** that routes TCP, UDP, and TLS traffic based on IP address and port. Passes through the TCP/UDP payload without inspecting it.

**Purpose:**
- Distribute TCP/UDP traffic at extreme scale and low latency.
- Handle millions of requests per second.
- Preserve the source IP (for IP-based filtering).

**How it works:**
- Operates at Layer 4 — doesn't terminate TCP, just forwards packets.
- Hashes source IP + port and selects a target.
- Backend targets: EC2 instances, IPs, ALB (chained).
- Supports static IP addresses, Elastic IPs.

**Advantages:**
- **Very high throughput** (millions of req/s).
- **Ultra-low latency** (no Layer 7 inspection).
- Static IP addresses.
- Preserves client source IP.
- Handles TLS offload (TCP-level) without inspecting HTTP.

**Disadvantages:**
- No content-based routing (URL, header).
- No HTTP-specific features (cookies, sticky sessions at HTTP level).
- More limited in health checks (TCP-level only).

**When to use:**
- Gaming, IoT, real-time apps.
- High-throughput TCP services.
- Microservices using gRPC over TCP.
- Need for static IP (whitelisting).
- Database load balancing (e.g., read replicas).

**When NOT to use:**
- Need path-based or host-based routing (use ALB).
- Need HTTP-aware features.

**Provider Examples:**
- **AWS:** Network Load Balancer (NLB).
- **Azure:** Azure Load Balancer (Layer 4).
- **GCP:** TCP/UDP Load Balancer (Network Load Balancing).

---

#### 2.7.3 — Application Gateway

**Definition (Azure-specific):**
A **Layer 7 load balancer with built-in WAF** (Web Application Firewall). Microsoft's equivalent of AWS ALB + AWS WAF combined.

**Purpose:**
- Web traffic load balancing with built-in application-layer security.
- Path-based routing, multi-site hosting, SSL termination, cookie-based session affinity.
- WAF protection against OWASP Top 10 (SQL injection, XSS, etc.).

**How it works:**
- Similar to ALB — terminates SSL, routes HTTP/HTTPS based on rules.
- Has a built-in WAF in two modes: **detection** (logs) or **prevention** (blocks).
- Backend: VMs, VM Scale Sets, App Service, IPs, AKS.

**Advantages:**
- Single service for LB + WAF.
- Centralized SSL cert management.
- Azure-native (tight integration with other Azure services).

**Disadvantages:**
- Azure-only (the term is specific to Azure).
- WAF adds latency and requires tuning to avoid false positives.

**Provider Examples:**
- **Azure:** Application Gateway.
- **AWS:** AWS ALB + AWS WAF (separate services, similar combined function).
- **GCP:** Cloud Load Balancing + Cloud Armor (WAF).

**CompTIA Note:** When the exam says "Application Gateway," think Azure. When it says "ALB" or "Application Load Balancer," think generic L7 LB.

---

#### 2.7.4 — Content Delivery Network (CDN)

**Definition:**
A **geographically distributed network of servers** that cache content (web pages, images, videos, scripts) close to end users. The user fetches content from the nearest edge location, not from the origin server.

**Purpose:**
- **Reduce latency** by serving content from a location near the user.
- **Reduce origin load** by offloading static content to the CDN.
- **Improve availability** — CDN can absorb traffic spikes and DDoS.
- **Global reach** without global infrastructure.

**How it works:**
- User requests content (e.g., `cdn.example.com/image.jpg`).
- DNS routes the user to the nearest CDN edge location (based on GeoIP or Anycast).
- If the content is cached at that edge → served immediately.
- If not → the edge fetches from the origin, caches it, and serves it to the user (and future users).
- Cache invalidation via TTL or purge API.

**Advantages:**
- Sub-50 ms latency for cached content globally.
- Absorbs traffic spikes (DDoS, viral content).
- Offloads work from the origin.
- Reduces data egress cost (cached at edge, fewer origin fetches).

**Disadvantages:**
- Stale content possible (cache invalidation is tricky).
- Cache misses still go to origin (slower).
- Dynamic content is harder to cache.
- CDN vendor lock-in.

**When to use:**
- Static websites, single-page apps (SPA), image-heavy sites.
- Video streaming.
- Software distribution (game updates, app downloads).
- API caching (for read-heavy public APIs).
- DDoS protection (CDN absorbs the attack).

**When NOT to use:**
- Highly dynamic, personalized content (the cache hit rate would be low).
- Strict data residency (verify CDN edge locations).

**Provider Examples:**
- **AWS:** CloudFront.
- **Azure:** Azure CDN (now part of Front Door) or Azure Front Door.
- **GCP:** Cloud CDN.
- **Third-party:** Cloudflare, Akamai, Fastly.

---

#### 2.7.5 — Firewalls

**Definition:**
A **security device or software** that controls network traffic based on predefined rules. Allows or denies packets based on source/destination IP, port, protocol, or application-layer content.

**Purpose:**
- Enforce network security policy.
- Block unauthorized access.
- Segment networks.
- Protect against attacks (DDoS, SQL injection, etc.).

**Types in Cloud Networking:**

| Type | Layer | Scope | Examples |
|---|---|---|---|
| **Security Group** | Instance/ENI | Stateful, per-instance | AWS Security Group |
| **Network ACL (NACL)** | Subnet | Stateless, per-subnet | AWS NACL |
| **Network Firewall** | VPC | Stateful, traffic inspection | AWS Network Firewall, Azure Firewall |
| **WAF (Web App Firewall)** | L7 (HTTP) | Per-app | AWS WAF, Azure WAF (in App Gateway), Cloud Armor |
| **Cloud-native NGFW** | VPC | Deep packet inspection | Palo Alto VM-Series, Checkpoint, Fortinet |

**How each works:**

**Security Group (SG):**
- **Stateful** — return traffic is automatically allowed.
- Applied at the **instance / ENI** level.
- Rules: allow only (no explicit deny — implicit deny).
- Default: deny all inbound, allow all outbound.
- Most common first line of defense.

**Network ACL (NACL):**
- **Stateless** — return traffic must be explicitly allowed.
- Applied at the **subnet** level.
- Rules: explicit allow/deny, with rule numbers (lower = higher priority).
- Use case: coarse-grained subnet-level filtering, blocking known bad IPs.

**Network Firewall (e.g., AWS Network Firewall, Azure Firewall):**
- **Stateful**, traffic inspection at VPC boundaries.
- Can do deep packet inspection, IDS/IPS, domain filtering.
- Centrally managed, scales with VPC traffic.

**WAF (Web Application Firewall):**
- **Layer 7** (HTTP/HTTPS) — inspects application content.
- Protects against OWASP Top 10: SQL injection, XSS, CSRF, etc.
- Can be attached to ALB, CloudFront, API Gateway, App Gateway.
- Uses rule sets (managed rules, custom rules, rate limiting).

**Advantages:**
- Layered security (defense in depth).
- Cloud-native firewalls = no hardware to manage.
- Auto-scales with traffic.

**Disadvantages:**
- Misconfigured SG = most common cloud breach cause (Capital One 2019).
- WAF requires tuning (false positives = blocked legit traffic).
- NACLs stateless = more rules to manage.

**When to use:**
- **Always** — every cloud workload needs firewall rules.
- Use SGs for instance-level, NACLs for subnet-level, WAF for HTTP, network firewall for VPC edge.

**When NOT to use:**
- Never skip firewalls.

**Provider Examples:**
- **AWS:** Security Groups, NACLs, AWS Network Firewall, AWS WAF.
- **Azure:** NSGs (Network Security Groups), Azure Firewall, Application Gateway WAF, Front Door WAF.
- **GCP:** VPC Firewall Rules, Cloud Armor, Cloud IDS.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — VPN in the Wild

**AWS Example — Site-to-Site VPN for Hybrid Setup:**
- A regional bank runs its core banking on-prem (regulatory requirement) and a public-facing mobile app on AWS.
- They establish a Site-to-Site VPN from their on-prem Cisco ASA firewall to the AWS Virtual Private Gateway.
- Encrypted IPsec tunnel carries mobile app data to on-prem back-office systems.
- They use two tunnels for redundancy (one ISP, one 4G LTE).
- RTO = 4 hours — VPN failover is acceptable for non-critical sync.

**Azure Example — Point-to-Site VPN for Remote Workers:**
- A consulting firm has 200 remote employees who need access to internal Azure VMs.
- They use Azure Point-to-Site VPN with certificate authentication.
- Each user installs the Azure VPN client on their laptop.
- Encrypted tunnel from the laptop to Azure VPN Gateway.
- Cheaper than deploying a dedicated line per user.

**GCP Example — HA VPN for Multi-Region:**
- A SaaS company runs primary in `us-central1` and DR in `us-east1`.
- They use GCP HA VPN (two tunnels, 99.99% SLA) to connect on-prem to GCP.
- BGP over VPN for dynamic route learning.
- Automatic failover if one tunnel drops.

---

### 3.2 — Dedicated Connections in the Wild

**AWS Example — Direct Connect for Big Data Transfer:**
- A media company needs to transfer 500 TB of video archives to S3 monthly.
- They lease a 10 Gbps Direct Connect from their on-prem data center to AWS (via Equinix colo in Los Angeles).
- Transfer is 10x faster than over the public internet.
- Cost: ~$0.03/GB over Direct Connect vs. ~$0.09/GB over internet (varies).

**Azure Example — ExpressRoute for Microsoft 365:**
- A multinational enterprise uses ExpressRoute to connect 50 branch offices to Azure.
- They get predictable performance for Microsoft Teams calls and SharePoint sync.
- Bandwidth: 500 Mbps per site, with QoS for voice traffic.
- Compliance: data doesn't traverse the public internet.

**GCP Example — Partner Interconnect for Mid-Size Business:**
- A financial analytics firm uses GCP Partner Interconnect (50 Mbps–50 Gbps) via Megaport.
- Lower cost than Dedicated Interconnect (10/100 Gbps).
- Sufficient for their analytics workloads.

---

### 3.3 — VPCs and Peering in the Wild

**AWS Example — Cross-Account VPC Peering:**
- A holding company has 3 subsidiaries, each with its own AWS account and VPC.
- VPC peering is established between subsidiary VPCs for inter-company analytics.
- Each account's admin explicitly authorizes the peering request.
- Route tables updated to point to the peering connection.

**Azure Example — Global VNet Peering:**
- A global retailer has VNets in `East US`, `West Europe`, and `Southeast Asia`.
- They use VNet Peering (now also "Global VNet Peering") for cross-region connectivity.
- Latency: ~80 ms between East US and West Europe.
- Used for replicating product catalog data.

**GCP Example — Shared VPC for Multi-Project Setup:**
- A large enterprise uses GCP Shared VPC (now called "Shared VPC" or "Host Project") to centralize networking.
- One host project owns the VPC; multiple service projects attach to it.
- Centralized firewall, route tables, VPN.

---

### 3.4 — Transit Gateway in the Wild

**AWS Example — Hub-and-Spoke for 50 VPCs:**
- A large SaaS company has 50 VPCs (one per business unit).
- Direct peering = 50 × 49 / 2 = 1,225 peering connections (unmanageable).
- They use a Transit Gateway as the hub.
- Each VPC attaches via one Transit Gateway attachment.
- Centralized egress: all outbound traffic goes through a central firewall VPC.

**Azure Example — Virtual WAN for Global Enterprise:**
- A multinational uses Azure Virtual WAN (Microsoft's transit solution).
- Branches, data centers, and VNets all connect through a Virtual WAN hub.
- Built-in VPN, ExpressRoute, and SD-WAN integration.

**GCP Example — Network Connectivity Center:**
- A company uses GCP NCC as a transit hub to connect VPCs, on-prem (via Cloud Interconnect), and partner networks.
- Spokes can be VPCs, hybrid attachments, or partner attachments.
- Centralized policy and routing.

---

### 3.5 — Subnets in the Wild

**AWS Example — Three-Tier Web App:**
- A web app has:
  - **Public subnets** (10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24 in 3 AZs): ALB, NAT Gateway, bastion host.
  - **Private subnets** (10.0.10.0/24, 10.0.11.0/24, 10.0.12.0/24 in 3 AZs): web/app servers.
  - **Database subnets** (10.0.20.0/24, 10.0.21.0/24, 10.0.22.0/24 in 3 AZs): RDS, ElastiCache.
- Each tier has its own route table:
  - Public: 0.0.0.0/0 → IGW.
  - Private: 0.0.0.0/0 → NAT (for outbound updates), DB subnets → local.
  - DB: 10.0.0.0/16 → local, no internet.

**Azure Example — Hub-Spoke with Isolated DB Subnets:**
- A financial services firm uses a hub VNet (shared services) and spoke VNets (per business unit).
- Each spoke has:
  - Frontend subnet (with NSG allowing 80/443).
  - Backend subnet (no internet, NSG allows only frontend).
  - DB subnet (no internet, NSG allows only backend).

**GCP Example — Multi-Region Subnets:**
- A global app uses GCP's auto-mode VPC.
- Custom subnets in `us-central1`, `europe-west1`, `asia-northeast1`.
- Each region's subnets use local IPs (10.x for US, 10.y for EU, 10.z for Asia) to avoid overlap.

---

### 3.6 — Load Balancers in the Wild

**AWS Example — Multi-ALB + NLB Architecture:**
- A media streaming service uses:
  - **NLB** at the edge for TCP traffic (RTMP video ingest from broadcasters). Handles millions of concurrent connections.
  - **ALB** for the viewer API (REST endpoints with path-based routing).
  - **CloudFront** (CDN) in front of static assets (thumbnails, JS, CSS).
- Architecture: User → CloudFront → ALB → App → ALB → NLB → DB.

**Azure Example — Application Gateway + Front Door:**
- A global SaaS uses Azure Front Door for global load balancing and CDN.
- Behind it, in each region, an Application Gateway routes to AKS pods.
- WAF rules in Application Gateway block SQL injection and XSS.

**GCP Example — Global HTTPS Load Balancer + Cloud Armor:**
- A fintech app uses GCP's Global External HTTPS Load Balancer.
- Cloud Armor (WAF) provides DDoS protection and OWASP rule sets.
- Backend: Managed Instance Groups in multiple regions.

---

### 3.7 — CDN in the Wild

**AWS Example — CloudFront for Streaming:**
- Netflix-style streaming service uses CloudFront to cache video at 600+ edge locations.
- Origin: S3 bucket with the master video files.
- CloudFront serves the closest cached copy to each viewer.
- Reduces origin bandwidth by 95%+.

**Azure Example — Azure Front Door for Global Web App:**
- A B2B SaaS uses Azure Front Door for global HTTP load balancing + CDN.
- Static assets cached at edge (CSS, JS, images).
- Dynamic requests routed to nearest region.

**GCP Example — Cloud CDN for Image-Heavy Site:**
- A photo-sharing app uses GCP Cloud CDN in front of GCS buckets.
- Images are cached at 100+ edge locations.
- Origin shield reduces origin load by 80%.

---

### 3.8 — Firewalls in the Wild

**AWS Example — Layered Firewall Architecture:**
- An enterprise uses:
  - **Security Groups** at the instance level (allow only 443 from ALB).
  - **NACLs** at the subnet level (block known malicious IPs).
  - **AWS Network Firewall** at the VPC edge (centralized IDS/IPS).
  - **AWS WAF** in front of CloudFront and ALB (OWASP protection).
  - **Shield Advanced** for DDoS protection.
- Defense in depth — multiple layers, no single point of failure.

**Azure Example — NSG + Azure Firewall:**
- A bank uses:
  - **NSGs** for per-subnet rules.
  - **Azure Firewall** at the hub VNet (centralized egress).
  - **App Gateway WAF** for HTTP-level protection.
  - **DDoS Protection** Standard tier.

**GCP Example — VPC Firewall Rules + Cloud Armor:**
- A media company uses:
  - **VPC firewall rules** (stateful, allow/deny by tag, IP, port).
  - **Cloud Armor** at the global LB (WAF + DDoS).
  - **Cloud IDS** for intrusion detection.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — VPN vs Dedicated Connection

| Attribute | Site-to-Site VPN | Dedicated Connection |
|---|---|---|
| **Medium** | Public internet | Private fiber |
| **Cost** | Low (pay per VPN-hour) | High (port + per-GB) |
| **Bandwidth** | Limited by internet | 1, 10, 100 Gbps available |
| **Latency** | Variable (internet-dependent) | Predictable (low) |
| **Setup time** | Minutes | Weeks–months |
| **Encryption** | IPsec (always) | Optional (often unencrypted private line) |
| **Use case** | Small/medium, low budget | Mission-critical, high bandwidth |
| **AWS** | Site-to-Site VPN | Direct Connect |
| **Azure** | VPN Gateway | ExpressRoute |
| **GCP** | Cloud VPN | Cloud Interconnect |
| **Best practice** | Backup for Direct Connect | Primary for production |
| **Exam tip** | "Cost-effective / quick to set up" | "High bandwidth / low latency / compliance" |

---

### 4.2 — Security Group vs Network ACL

| Attribute | Security Group (SG) | Network ACL (NACL) |
|---|---|---|
| **Layer** | Instance / ENI | Subnet |
| **Stateful?** | Yes (return traffic auto-allowed) | No (must explicitly allow both directions) |
| **Rules** | Allow only | Allow and Deny |
| **Default behavior** | Deny all inbound, allow all outbound | Allow all (default NACL) |
| **Evaluation** | All rules evaluated | Rules evaluated in order (lowest number first) |
| **Use case** | Per-instance rules | Coarse subnet-level filtering, deny known-bad IPs |
| **AWS only?** | AWS concept; Azure has NSG (similar to SG), GCP has firewall rules |
| **Exam tip** | "First line of defense / stateful" | "Stateless / subnet-level / explicit deny" |

---

### 4.3 — ALB vs NLB vs Application Gateway vs Cloud CDN

| Attribute | ALB (L7) | NLB (L4) | Application Gateway (Azure L7) | CDN |
|---|---|---|---|---|
| **OSI Layer** | 7 (HTTP) | 4 (TCP/UDP) | 7 (HTTP) | 7 (HTTP) + caching |
| **Routing** | Content-based (URL, host) | IP + port | Content-based | Geographic (anycast) |
| **SSL offload** | Yes | No (passthrough) | Yes | Yes (at edge) |
| **Use case** | Web apps, APIs, microservices | Gaming, IoT, TCP services | Web apps with WAF | Static content, streaming |
| **Throughput** | Medium | Very high | Medium | Very high (cached) |
| **Latency** | Few ms added | Minimal | Few ms added | Sub-50 ms at edge |
| **WAF integration** | AWS WAF | No | Built-in WAF | Cloud Armor (GCP) / WAF (AWS via CloudFront) |
| **AWS** | ALB | NLB | — | CloudFront |
| **Azure** | — | Azure LB | App Gateway | Azure CDN / Front Door |
| **GCP** | HTTP(S) LB | TCP/UDP LB | — | Cloud CDN |
| **Exam tip** | "Web app / path-based routing" | "Millions of req/sec / static IP" | "Azure + WAF" | "Global / static content" |

---

### 4.4 — VPC Peering vs Transit Gateway

| Attribute | VPC Peering | Transit Gateway |
|---|---|---|
| **Topology** | 1-to-1 (mesh) | Hub-and-spoke |
| **Transitive routing** | No | Yes |
| **Max VPCs** | ~125 peering conns per VPC (practical limit much lower) | 5,000+ attachments |
| **Cost** | Free or low (per-GB data) | Per-GB data processing fee |
| **Use case** | 2–5 VPCs | 5+ VPCs, multi-account |
| **Cross-region** | Yes | Yes (via peering of TGWs) |
| **Cross-account** | Yes | Yes (via RAM) |
| **AWS** | VPC Peering | Transit Gateway |
| **Azure** | VNet Peering | Virtual WAN |
| **GCP** | VPC Peering | Network Connectivity Center |
| **Exam tip** | "2 VPCs / simple" | "Many VPCs / hub-and-spoke / central egress" |

---

### 4.5 — Static Routes vs BGP

| Attribute | Static Routes | BGP |
|---|---|---|
| **Configuration** | Manual | Dynamic, automatic |
| **Adaptation** | None (must update manually) | Adapts to failures |
| **Scalability** | Poor (many routes = manual) | Excellent (handles 100K+ routes) |
| **Use case** | Simple, predictable networks | Hybrid, multi-cloud, ISP |
| **Skill** | Beginner | Advanced |
| **CompTIA** | Mentioned in CV0-004 | Heavily tested in CV0-004 |

---

### 4.6 — Public Subnet vs Private Subnet

| Attribute | Public Subnet | Private Subnet |
|---|---|---|
| **Route to IGW?** | Yes | No |
| **Resources reachable from internet?** | Yes (if public IP) | No |
| **Outbound internet access** | Yes (direct) | Via NAT Gateway |
| **Use case** | ALB, bastion, NAT Gateway | App servers, databases |
| **Route table** | `0.0.0.0/0 → igw-xxx` | `0.0.0.0/0 → nat-xxx` |

---

## SECTION 5 — EXAM TRAPS

### Trap 1: "VPN is secure enough for everything"
- **Trap:** "Use VPN for the bank's cloud connection."
- **Reality:** VPN runs over the public internet — variable performance, no SLA on bandwidth. For mission-critical / regulated / high-bandwidth, use Direct Connect / ExpressRoute. CompTIA tests "compliance requires dedicated line."

### Trap 2: "VPC peering is transitive"
- **Trap:** "VPC A peers with B, B peers with C, so A can reach C."
- **Wrong.** Peering is non-transitive. A must peer with C directly.
- **Fix:** Use Transit Gateway.

### Trap 3: "Security Group blocks return traffic"
- **Trap:** "I allowed inbound 443, but responses don't come back."
- **Wrong.** Security groups are stateful — return traffic is auto-allowed. If responses don't come back, the issue is the response path (e.g., ephemeral ports blocked by NACL).

### Trap 4: "NACL is enough — don't need Security Group"
- **Trap:** "NACL is at the subnet level, more comprehensive."
- **Wrong.** NACLs are stateless — return traffic is NOT auto-allowed. They're a complement to SGs, not a replacement. Defense in depth.

### Trap 5: "ALB can load balance TCP"
- **Trap:** "Use ALB to load balance MySQL on port 3306."
- **Wrong.** ALB is Layer 7 (HTTP/HTTPS/gRPC). For TCP, use NLB.

### Trap 6: "CDN replaces the load balancer"
- **Trap:** "We use CloudFront, so we don't need an ALB."
- **Wrong.** CDN caches static content at the edge. Dynamic requests still go to the origin. ALB (or NLB) is still needed to distribute traffic across origin servers.

### Trap 7: "BGP is only for ISPs"
- **Trap:** "BGP is an internet routing protocol, not used in cloud."
- **Wrong.** BGP is the standard for cloud hybrid connectivity (on-prem ↔ cloud). CompTIA explicitly added BGP to CV0-004.

### Trap 8: "Internet Gateway = NAT Gateway"
- **Trap:** "Use IGW for private subnets to access the internet."
- **Wrong.** IGW is bidirectional (in + out). Private subnets need a **NAT Gateway** for outbound-only. NAT prevents inbound connections from the internet.

### Trap 9: "Public IP = public subnet"
- **Trap:** "A resource in a public subnet must have a public IP."
- **Wrong.** A public subnet has a route to the IGW, but the resource still needs a public IP/Elastic IP to be reachable. A public subnet without public IPs = effectively private.

### Trap 10: "VPN + Direct Connect = no need for both"
- **Trap:** "Use Direct Connect, drop VPN."
- **Wrong.** Best practice is Direct Connect as primary + VPN as backup (cheaper than redundant Direct Connect).

### Trap 11: "Transit Gateway is for transit only"
- **Trap:** "TGW is just for VPC-to-VPC."
- **Wrong.** TGW also supports VPN, Direct Connect, and centralized egress/firewall.

### Trap 12: "BGP = automatic fail-over with VPN"
- **Trap:** "BGP over VPN will fail over in seconds."
- **Reality:** BGP convergence is fast, but the *tunnel* re-establishment can take 30–60 seconds. Plan for this in RTO.

### Trap 13: "WAF = Firewall"
- **Trap:** "I have a WAF, I don't need a network firewall."
- **Wrong.** WAF is Layer 7 (HTTP). Network firewall is Layers 3/4. Both are needed for defense in depth.

### Trap 14: "VLAN in cloud"
- **Trap:** "Set up VLANs in the cloud VPC."
- **Wrong.** VLANs are Layer 2 / on-prem. In the cloud, use subnets + security groups + NACLs. VLANs are only used inside cloud-hosted virtual appliances (e.g., virtual firewalls, virtual routers).

### Trap 15: "CDN for dynamic content"
- **Trap:** "CDN will speed up our personalized homepage."
- **Wrong.** CDN caches content — personalized dynamic content has near-zero cache hit rate. Use CDN for static assets (images, CSS, JS, videos).

---

## SECTION 6 — PERFORMANCE-BASED QUESTION PREP (PBQs)

### PBQ 1 — The Three-Tier Web App in AWS

**Scenario:**
Deploy a three-tier web app in a new AWS VPC. Requirements:
- ALB must be in public subnets.
- Web servers in private subnets.
- Database in isolated private subnets (no internet).
- Multi-AZ for HA.
- CIDR: 10.0.0.0/16.

**Question:** Design the subnets, route tables, and security groups.

**Walkthrough:**

1. **Three AZs for HA:** us-east-1a, 1b, 1c.
2. **Subnet tiers:**
   - Public subnets (one per AZ): 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24.
   - Private web subnets (one per AZ): 10.0.10.0/24, 10.0.11.0/24, 10.0.12.0/24.
   - Private DB subnets (one per AZ): 10.0.20.0/24, 10.0.21.0/24, 10.0.22.0/24.
3. **Gateways:**
   - Internet Gateway attached to VPC.
   - NAT Gateway in each public subnet (HA).
4. **Route tables:**
   - Public: 10.0.0.0/16 → local, 0.0.0.0/0 → igw-xxx.
   - Private web: 10.0.0.0/16 → local, 0.0.0.0/0 → nat-xxx.
   - Private DB: 10.0.0.0/16 → local, no 0.0.0.0/0.
5. **Security groups:**
   - ALB SG: inbound 80/443 from 0.0.0.0/0.
   - Web SG: inbound 80/443 from ALB SG.
   - DB SG: inbound 3306 from Web SG.

**Bonus Q:** How do web servers get OS updates?
- **Answer:** Outbound to internet via NAT Gateway.

---

### PBQ 2 — Multi-Account AWS with Transit Gateway

**Scenario:**
A holding company has 3 AWS accounts (Finance, Marketing, Operations), each with its own VPC. They need:
- Each VPC to reach the others.
- Centralized egress (all internet traffic through a "Security" VPC for inspection).
- A new on-prem data center must connect via Direct Connect.

**Question:** Design the network.

**Walkthrough:**
1. **Create Transit Gateway in a "Network" account.**
2. **Share TGW with other accounts via AWS RAM.**
3. **Each VPC account creates a TGW attachment to its VPC.**
4. **Security VPC has:**
   - TGW attachment.
   - Centralized firewall (3rd party NGFW in HA pair).
   - Egress to internet via IGW + NAT.
5. **TGW route table:**
   - All VPC attachments route 0.0.0.0/0 → Security VPC attachment (centralized egress).
6. **Direct Connect Gateway** in the Network account → VIF → TGW.
7. **On-prem** advertises its CIDRs to the TGW via BGP over Direct Connect.

**Architecture:**
```
[Finance VPC] ─┐
                ├──→ [Transit Gateway] ─→ [Security VPC] ─→ IGW/NAT ─→ Internet
[Marketing VPC]─┤                                    ↑
[Ops VPC] ──────┘                                    │
                                                     │
[On-Prem DC] ─── Direct Connect ──→ [DX Gateway] ────┘
```

---

### PBQ 3 — Global Web App with CDN + WAF

**Scenario:**
A global e-commerce site uses:
- CloudFront in front of static assets (images, JS).
- ALB behind CloudFront for the API.
- S3 for static assets.
- Aurora for the database.
- WAF for SQL injection / XSS protection.

**Question:** Identify each component's role and order of traffic flow.

**Walkthrough:**

1. **User → CloudFront:**
   - User requests `cdn.example.com/image.jpg`.
   - DNS (Route 53) returns a CloudFront edge IP.
2. **CloudFront edge cache:**
   - If image is cached → serve from edge.
   - If not → fetch from origin (S3 bucket), cache, serve.
3. **For API requests (`api.example.com`):**
   - User → CloudFront → ALB → ECS/EKS task.
   - CloudFront forwards to ALB via origin shield.
4. **WAF:**
   - CloudFront has AWS WAF attached. Inspects request, blocks SQLi/XSS.
5. **ALB → App:**
   - ALB routes to ECS task in private subnet.
6. **App → Aurora:**
   - Private subnet → DB subnet via SG rules.
7. **Return path:** App → ALB → CloudFront → user.

**Failure mode:** If WAF is misconfigured, legitimate users may be blocked. Always test in "count" mode first.

---

### PBQ 4 — Hybrid with Direct Connect + VPN Backup

**Scenario:**
A financial firm needs to connect on-prem data center to AWS:
- Primary: 10 Gbps Direct Connect.
- Backup: Site-to-Site VPN (cheaper than redundant DX).
- Failover: automatic.
- Routing: BGP on both links.

**Question:** Design the configuration.

**Walkthrough:**
1. **Direct Connect (primary):**
   - 10 Gbps port at Equinix SV1.
   - Connect to AWS Direct Connect location.
   - Private VIF to a Virtual Private Gateway (VGW).
   - BGP session: on-prem ASN 65000 ↔ AWS ASN.
   - On-prem advertises 192.168.0.0/16.
2. **VPN (backup):**
   - Site-to-Site VPN to the same VGW.
   - BGP session over IPsec tunnel.
   - On-prem also advertises 192.168.0.0/16 over VPN.
3. **Failover via BGP:**
   - DX link has lower MED or higher local preference → preferred.
   - If DX fails → BGP withdraws route → VPN path becomes active.
   - Failover time: ~30–60 seconds (BGP convergence + tunnel re-establishment).
4. **On-prem router:** Two ISPs, one for DX, one for VPN tunnel.

**Bonus Q:** What if both DX and VPN are down?
- **Answer:** Total loss of hybrid connectivity. Cloud workloads that depend on on-prem become unavailable. Plan runbooks.

---

### PBQ 5 — Microservices with Service Mesh

**Scenario:**
A microservices app on AKS (Azure Kubernetes Service) has 20 services that need to talk to each other. The team wants:
- Service-to-service encryption (mTLS).
- Traffic management (canary deployments, retries).
- Observability (distributed tracing).

**Question:** What cloud networking construct meets this?

**Walkthrough:**
1. **Service Mesh:** Istio, Linkerd, or Consul.
2. **In AKS, use the Azure Service Mesh add-on (Istio-based).**
3. **Sidecar proxies** in each pod handle mTLS, retries, tracing.
4. **Ingress gateway** (Istio) handles external traffic + L7 routing.
5. **Cloud networking underneath:** AKS nodes in a private subnet, services communicate over the pod network (overlay, e.g., Cilium).

**Trade-off vs. cloud-native LB:**
- Cloud-native ALB/NLB handles North-South (in/out) traffic.
- Service mesh handles East-West (inter-service) traffic.
- For 20+ services, service mesh is the right answer.

---

### PBQ 6 — CDN Strategy for a Media Company

**Scenario:**
A streaming media company has 1 PB of video files stored in S3. They want sub-2-second video start time globally. Origin bandwidth is 100 Gbps (maxed out during peak).

**Question:** What CDN strategy works?

**Walkthrough:**
1. **CDN:** CloudFront / Cloud CDN / Azure CDN.
2. **Origin shield:** Enable to reduce origin load (one tier of caching between edge and origin).
3. **Cache control headers:** Set `Cache-Control: max-age=31536000` for immutable video files.
4. **Multi-region origin:** Replicate S3 bucket to multiple regions; CloudFront serves from nearest origin on miss.
5. **Signed URLs:** Restrict access to paid content.
6. **Result:** Origin bandwidth drops 80%+. Start time drops to <500 ms globally.

**Anti-pattern to avoid:** Don't use CDN for live streaming ingest (use a separate ingest endpoint, e.g., AWS MediaLive).

---

## SECTION 7 — MEMORY AIDS

### 🎯 VPC Peering vs Transit Gateway

- **Peering** = "**P**rivate 1-to-1" — like a private phone call between 2 VPCs. NOT transitive.
- **Transit Gateway** = "**T**ransit hub" — like a phone switchboard. Many VPCs connect through one hub. Transitive.

**Memory trick:** "**P**eering is **P**oint-to-point. **T**ransit is the **T**elephone exchange."

### 🎯 ALB vs NLB

- **ALB** = "**A**pplication — **A**lways look at the **A**pplication layer" (L7, content-aware).
- **NLB** = "**N**etwork — **N**ever look inside, just forward the **N**etwork packet" (L4, fast).

**Memory trick:** "**A**LB **A**nswers based on URL. **N**LB **N**ever asks what's inside."

### 🎯 Security Group vs NACL

- **Security Group = "**S**mart Guard"** — stateful, knows who's a friend.
- **NACL = "**N**aive **A**ccess **C**ontrol **L**ist"** — stateless, no memory.

**Memory trick:** "**S**G has **S**tate. **N**ACL has **N**o state."

### 🎯 VPN vs Direct Connect

- **VPN = "**V**irtual **P**ipe over the **V**aried public internet"** — fast to set up, variable performance.
- **Direct Connect = "**D**edicated **D**irect **D**ata line"** — physical fiber, predictable, expensive.

**Memory trick:** "**V**PN is the **V**aried highway (sometimes traffic). **D**irect Connect is the **D**edicated rail line (always on time)."

### 🎯 Public Subnet vs Private Subnet

- **Public** = "**P**arty **P**lace" — anyone can come in (if invited).
- **Private** = "**P**rivate **P**ad" — no uninvited guests; if you want to call out, use the NAT.

**Memory trick:** "**P**ublic subnet has a **P**hone line to the **P**ublic (IGW). **P**rivate subnet uses **P**roxy (NAT)."

### 🎯 BGP Mnemonic

- **B**order **G**ateway **P**rotocol = "**B**ig **G**lobal **P**aths" — finds the best path across the internet.
- "**B**GP is **B**ackbone **G**ateway **P**rotocol" — used on the internet backbone and in hybrid cloud.

### 🎯 CDN vs Load Balancer

- **CDN** = "**C**ache **D**eliver **N**etwork" — caches content close to users (read-heavy, static).
- **Load Balancer** = "**L**oad **B**alancer" — distributes requests across servers (dynamic, all traffic).

**Memory trick:** "**C**DN **C**aches at the **C**orner. **L**oad **B**alancer **L**ives near the **L**ogic."

### 🎯 WAF vs Network Firewall

- **WAF = "**W**eb **A**pp **F**irewall"** — protects HTTP (Layer 7).
- **Network Firewall = "**N**etwork **F**irewall"** — protects IP/port (Layer 3/4).

**Memory trick:** "**W**AF watches the **W**eb. **N**etwork firewall watches the **N**etwork."

### 🎯 Route Table Decision

- **0.0.0.0/0 → IGW** = public subnet (everyone goes to the internet).
- **0.0.0.0/0 → NAT** = private subnet (outbound only).
- **0.0.0.0/0 → black hole (no route)** = isolated subnet (no internet).
- **10.0.0.0/16 → local** = default intra-VPC route (auto).

### 🎯 Subnet-to-AZ Pinning

- "**S**ubnet = **S**ingle **A**Z" — each subnet is in exactly **one** AZ.
- "**V**PC = **V**isible across **V**isible all AZs in region" — VPC spans all AZs in a region.

**Memory trick:** "**S**ubnet is **S**ingle. **V**PC is **V**ast (region-wide)."

---

## SECTION 8 — CLOUD PROVIDER MAPPING

### Connections to the Cloud

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **Site-to-Site VPN** | Site-to-Site VPN | VPN Gateway (Site-to-Site) | Cloud VPN (HA VPN) |
| **Client VPN** | Client VPN | Point-to-Site VPN (P2S) | Cloud VPN (Client) |
| **Dedicated Connection** | Direct Connect (1, 10, 100 Gbps) | ExpressRoute (1, 10, 100 Gbps) | Cloud Interconnect (Dedicated 10/100 G, Partner 50M–50G) |
| **Hybrid DNS** | Route 53 Resolver | Azure DNS Private Resolver | Cloud DNS (private zones + forwarding) |

### VPC and Subnets

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **VPC** | Amazon VPC | Virtual Network (VNet) | VPC (global) |
| **Subnet** | Subnet | Subnet | Subnet (regional) |
| **Public IP** | Elastic IP | Public IP Address | Static External IP |
| **Internet Gateway** | Internet Gateway (IGW) | (implicit, via routing) | (implicit, via default route + external IP) |
| **NAT Gateway** | NAT Gateway | NAT Gateway | Cloud NAT |
| **Private connectivity** | PrivateLink | Private Link | Private Service Connect |
| **VPC Peering** | VPC Peering | VNet Peering | VPC Peering |
| **Transit Hub** | Transit Gateway | Virtual WAN | Network Connectivity Center |
| **DHCP/DNS** | DHCP Option Sets, Route 53 Resolver | Azure DNS (default 168.63.129.16) | Cloud DNS |

### Load Balancers and Gateways

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **L4 LB** | Network Load Balancer (NLB) | Azure Load Balancer | TCP/UDP Load Balancer |
| **L7 LB** | Application Load Balancer (ALB) | Application Gateway | HTTP(S) Load Balancer |
| **Global LB** | Route 53 + ALB/NLB | Azure Front Door / Traffic Manager | Global External HTTP(S) LB |
| **API Gateway** | API Gateway | API Management | API Gateway, Apigee |
| **Service Mesh** | App Mesh (EOL) | Azure Service Mesh (Istio) | Anthos Service Mesh |

### CDN

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **CDN** | CloudFront | Azure CDN / Front Door | Cloud CDN |
| **DDoS protection** | Shield (Standard/Advanced) | DDoS Protection (Basic/Standard) | Cloud Armor |
| **Edge compute** | Lambda@Edge, CloudFront Functions | Azure Front Door + Functions | Cloud CDN + Cloud Functions |

### Firewalls

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **Instance-level firewall** | Security Group | Network Security Group (NSG) | VPC firewall rules (network tags) |
| **Subnet-level firewall** | Network ACL (NACL) | NSG (subnet association) | VPC firewall rules (subnet scope) |
| **Centralized firewall** | AWS Network Firewall | Azure Firewall | VPC firewall + Cloud NGFW (3rd party) |
| **WAF** | AWS WAF | Application Gateway WAF / Front Door WAF | Cloud Armor |
| **DDoS** | Shield Standard (free) / Shield Advanced | DDoS Protection Basic (free) / Standard | Cloud Armor (Standard / Managed Protection Plus) |
| **IDS/IPS** | Network Firewall (with Suricata rules) | Azure Firewall Premium / 3rd party (Check Point, Palo Alto) | Cloud IDS (preview → GA) |
| **3rd-party NGFW** | Palo Alto VM-Series, Check Point, Fortinet | Palo Alto, Check Point, Barracuda | Palo Alto, Check Point, Fortinet |

### Routing Protocols

| Concept | AWS | Azure | Google Cloud |
|---|---|---|---|
| **BGP** | DX + VPN support BGP | ExpressRoute supports BGP | Cloud Interconnect supports BGP |
| **Static routes** | Yes (route tables) | Yes (route tables) | Yes (route tables) |
| **SDN** | VPC (SDN-based) | Virtual WAN / AVNM | Andromeda (Google's SDN) |

---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What's a VPC?**
**Ideal answer:** "A Virtual Private Cloud is a logically isolated virtual network in the cloud. You define the IP range, subnets, route tables, and gateways. Resources inside a VPC can communicate as if on the same private LAN. It gives you control over your network topology and is the foundation of cloud networking."

**Q2: What's the difference between a public subnet and a private subnet?**
**Ideal answer:** "A public subnet has a route to the Internet Gateway, so resources with public IPs can be reached from the internet. A private subnet has no route to the IGW — resources aren't directly reachable from the internet. Private subnets use a NAT Gateway for outbound internet access (e.g., to download OS updates)."

**Q3: When would you use ALB vs NLB?**
**Ideal answer:** "ALB (Application Load Balancer) for Layer 7 — web apps, REST APIs, anything that needs URL/host-based routing. NLB (Network Load Balancer) for Layer 4 — extreme performance, TCP/UDP, static IP needed, gaming or IoT. ALB is smarter; NLB is faster."

**Q4: What does a Security Group do?**
**Ideal answer:** "A Security Group acts as a virtual firewall at the instance level. It's stateful, so return traffic is automatically allowed. You define inbound and outbound rules. If a rule allows inbound 443 from 0.0.0.0/0, the response is auto-allowed back out. Default is deny all inbound, allow all outbound."

**Q5: What's the difference between VPN and Direct Connect?**
**Ideal answer:** "VPN is an encrypted tunnel over the public internet. Cheap, quick to set up, but performance depends on the internet. Direct Connect is a private fiber line from a colocation facility to the cloud. Expensive, takes weeks to set up, but provides consistent low latency and high bandwidth. Many companies use Direct Connect as primary with VPN as backup."

**Q6: What is a CDN and why would you use one?**
**Ideal answer:** "A Content Delivery Network is a globally distributed set of edge servers that cache content close to end users. It reduces latency, offloads origin traffic, improves availability, and can help absorb DDoS attacks. You use it for static assets — images, videos, JavaScript, CSS. Dynamic, personalized content doesn't benefit much."

**Q7: What's the difference between VPC Peering and Transit Gateway?**
**Ideal answer:** "VPC Peering is a direct 1-to-1 connection between two VPCs. It's simple but doesn't scale — for 50 VPCs you'd need 1,225 peering connections. Transit Gateway is a hub that connects many VPCs through one attachment. It supports transitive routing (A can reach C through the hub) and is the right choice for 5+ VPCs."

---

### 🎓 Cloud Administrator Questions

**Q1: A web app in a private subnet can't reach the internet to download patches. What's wrong?**
**Ideal answer:** "The most common cause is a missing or wrong route in the private subnet's route table. The private subnet needs a route: 0.0.0.0/0 → NAT Gateway (which lives in a public subnet with a route to the IGW). I'd check:
1. Is there a NAT Gateway in the public subnet?
2. Does the private subnet's route table have 0.0.0.0/0 → NAT GW?
3. Does the NAT Gateway have an Elastic IP?
4. Are security groups allowing outbound (return traffic from patch servers)?
5. Are NACLs allowing outbound ephemeral ports (1024–65535)?"

**Q2: How do you design a multi-region active-active web app?**
**Ideal answer:** "I'd use:
- Route 53 latency-based or geolocation routing to direct users to the nearest region.
- ALB in each region, fronted by a Global Accelerator or Route 53 health check.
- Aurora Global Database or DynamoDB Global Tables for cross-region replication.
- S3 Cross-Region Replication for static assets.
- CloudFront in front for global caching and SSL termination.
- Health checks on each region's ALB; if unhealthy, Route 53 fails over to the healthy region.
- Caveat: cross-region writes are eventually consistent (async). For zero-RPO you'd need Aurora DSQL or Spanner, but that adds write latency."

**Q3: A company has 30 VPCs across multiple accounts. They need centralized egress and firewall inspection. What do you recommend?**
**Ideal answer:** "Transit Gateway as the hub. Each VPC attaches to the TGW. The TGW route table forces all 0.0.0.0/0 traffic to a centralized 'Security' VPC that contains a 3rd-party NGFW (e.g., Palo Alto VM-Series) in HA. The firewall inspects traffic, then forwards to the IGW + NAT. This is the textbook 'egress VPC' or 'security VPC' pattern. Caveats: TGW data-processing fees add up at high volumes; the firewall VPC can be a bottleneck if not sized properly. Monitor with VPC flow logs to TGW."

**Q4: How do you secure a public-facing web app in AWS?**
**Ideal answer:** "Layered defense (defense in depth):
1. **CloudFront + AWS WAF** at the edge — blocks SQL injection, XSS, rate limits, geo-blocking.
2. **Route 53 + health checks** — DNS-based DDoS protection.
3. **Shield Standard** (free, automatic) or **Shield Advanced** (paid) — DDoS protection.
4. **ALB in a public subnet** — only allows 80/443 from CloudFront.
5. **Web servers in a private subnet** — Security Group allows only from ALB SG.
6. **DB in a private subnet** — Security Group allows only from web SG.
7. **AWS Network Firewall** at VPC edge for additional IDS/IPS.
8. **VPC Flow Logs** to S3 for forensics.
9. **AWS GuardDuty** for threat detection.
10. **Secrets Manager / Parameter Store** for credentials — never hard-code."

**Q5: BGP is added to a CV0-004. What scenarios do you expect on the exam?**
**Ideal answer:** "BGP appears in:
- **Hybrid connectivity:** on-prem ↔ cloud via Direct Connect + BGP for dynamic routing.
- **VPN with dynamic routing:** Site-to-Site VPN with BGP for failover between two ISP links.
- **Multi-cloud:** BGP peering between AWS DX and Azure ExpressRoute via a partner (e.g., Equinix).
- **Comparison with static routes:** BGP is dynamic, scales; static is manual, doesn't scale.
The key fact: BGP is the **dynamic routing protocol** used for hybrid and multi-cloud. CompTIA wants you to know that BGP enables automatic failover when one path fails."

**Q6: A company is moving from a single-VPC setup to multi-VPC. They want to share services (e.g., a central AD, a shared logging cluster). What's the best architecture?**
**Ideal answer:** "Three common patterns:
1. **VPC Peering (for 2–3 VPCs):** Simple, low cost, but non-transitive and doesn't scale.
2. **Transit Gateway (for 5+ VPCs):** Hub-and-spoke, transitive, supports centralized services in a 'shared services' VPC attached to the TGW. This is the AWS-recommended pattern for >5 VPCs.
3. **AWS PrivateLink:** Expose a specific service (e.g., an API) to other VPCs without full network peering. Best when you want to share a service but not full network access.
For the described scenario (centralized AD + logging), I'd go with **Transit Gateway + shared services VPC**."

---

### 🎓 Cloud Support / Pre-Sales Questions

**Q1: Customer says 'we want a CDN but we have dynamic content.' What do you say?**
**Ideal answer:** "A traditional CDN caches static content at edge locations — dynamic content has near-zero cache hit rate and won't benefit. But you can:
1. **Cache dynamic fragments** — the user-specific parts of the page aren't cached, but the page shell (header, footer, navigation) is.
2. **Use edge compute** (Lambda@Edge, CloudFront Functions, Cloudflare Workers) to personalize at the edge.
3. **Use origin shield** to reduce origin load.
4. **Consider front-door optimization** (Azure Front Door, AWS Global Accelerator) for dynamic content routing.
A pure CDN isn't the right answer for fully dynamic content. We need a hybrid: CDN for static, dynamic acceleration for dynamic."

**Q2: A customer asks 'should we use VPN or Direct Connect?' How do you decide?**
**Ideal answer:** "Three questions:
1. **Bandwidth:** >1 Gbps sustained → Direct Connect. <1 Gbps → VPN.
2. **Latency tolerance:** Strict SLA / real-time → Direct Connect. Variable is OK → VPN.
3. **Compliance:** Must avoid public internet → Direct Connect.
4. **Time to set up:** Days? → VPN. Months? → DX.
5. **Budget:** Cost-sensitive → VPN. Mission-critical → DX.
Often the answer is **both** — DX as primary, VPN as backup. Failover via BGP."

**Q3: A company is concerned about egress costs from their cloud. What are the strategies?**
**Ideal answer:** "Egress (data leaving the cloud) is a major cloud cost. Strategies:
1. **CDN caching** — cache content at edge, fewer egress requests to origin.
2. **Direct Connect** — sometimes cheaper per-GB than internet egress at high volumes.
3. **Compression** — gzip / brotli at the ALB/CDN.
4. **Keep data in the cloud** — analytics in the same cloud/region as storage.
5. **Cross-region replication** — for compliance, not for performance; only replicate what's needed.
6. **Data lifecycle** — move cold data to cheaper storage tiers.
7. **API design** — pagination, partial responses, GraphQL to avoid over-fetching.
8. **Right-size storage** — delete unused snapshots, logs, old data."

---

## SECTION 10 — FLASHCARDS

```
Q: What is a VPC?
A: A logically isolated virtual network in the cloud, defined by a CIDR block, containing subnets, route tables, and gateways.

Q: What is a public subnet?
A: A subnet with a route to the Internet Gateway; resources with public IPs are reachable from the internet.

Q: What is a private subnet?
A: A subnet with no direct route to the Internet Gateway; resources are not directly reachable from the internet (use NAT for outbound).

Q: What is an Internet Gateway?
A: A gateway that allows bidirectional traffic between a VPC and the public internet (requires public IPs).

Q: What is a NAT Gateway?
A: A managed service that allows private subnet resources to initiate outbound internet traffic (e.g., for OS updates) but blocks unsolicited inbound.

Q: What is VPC Peering?
A: A direct network connection between two VPCs. Non-transitive (A peered with B and B peered with C does not allow A to reach C).

Q: What is a Transit Gateway?
A: A regional network transit hub that connects VPCs, VPNs, and Direct Connect with transitive routing.

Q: When to use Transit Gateway vs VPC Peering?
A: Peering for 2–5 VPCs (simple). Transit Gateway for 5+ VPCs (hub-and-spoke, scales).

Q: What is a Site-to-Site VPN?
A: An encrypted IPsec tunnel over the public internet between on-prem and cloud (or between two clouds).

Q: What is a Dedicated Connection?
A: A private fiber-optic line from a colocation facility to a cloud provider's edge (e.g., AWS Direct Connect, Azure ExpressRoute).

Q: What are the advantages of Direct Connect over VPN?
A: Consistent low latency, high bandwidth (1/10/100 Gbps), predictable performance, doesn't traverse public internet.

Q: What are the disadvantages of Direct Connect vs VPN?
A: Expensive, takes weeks/months to provision, requires a nearby colocation facility.

Q: What is an Application Load Balancer (ALB)?
A: Layer 7 (HTTP/HTTPS) load balancer that routes based on content (URL path, host header, headers).

Q: What is a Network Load Balancer (NLB)?
A: Layer 4 (TCP/UDP) load balancer that forwards packets by IP+port, ultra-low latency, high throughput.

Q: ALB vs NLB — when to use which?
A: ALB for web apps, APIs (L7, content-based). NLB for gaming, IoT, TCP services (L4, extreme perf).

Q: What is a CDN?
A: Content Delivery Network — globally distributed edge servers that cache content close to users.

Q: What is a Security Group?
A: A stateful, instance-level virtual firewall. Return traffic is auto-allowed.

Q: What is a Network ACL (NACL)?
A: A stateless, subnet-level firewall. Return traffic must be explicitly allowed.

Q: Security Group vs NACL — what's the difference?
A: SG is stateful (instance level). NACL is stateless (subnet level).

Q: What is a WAF?
A: Web Application Firewall — Layer 7 firewall that protects against OWASP Top 10 (SQLi, XSS, etc.).

Q: What is BGP?
A: Border Gateway Protocol — dynamic routing protocol used in hybrid cloud, multi-cloud, and the internet.

Q: What is a static route?
A: A manually configured route in a route table; doesn't adapt to failures.

Q: BGP vs static routes?
A: BGP is dynamic and adapts to failures. Static is manual and doesn't scale.

Q: What is a route table?
A: A set of rules determining where network traffic is directed. Each subnet is associated with a route table.

Q: What is a VLAN?
A: Layer 2 network segmentation; on-prem mostly, used inside cloud virtual appliances.

Q: What is SDN?
A: Software-Defined Networking — decouples control plane from data plane, making the network programmable.

Q: What is PrivateLink (AWS) / Private Link (Azure) / Private Service Connect (GCP)?
A: A way to expose a service privately to other VPCs/VNets without peering or public internet.

Q: What is a VPN client?
A: Software on a user device that creates an encrypted tunnel to a cloud VPN endpoint (Point-to-Site VPN).

Q: What is a security VPC / egress VPC?
A: A centralized VPC that all internet-bound traffic from other VPCs routes through (for inspection / firewall).

Q: What is a hub-and-spoke network?
A: An architecture where a central "hub" (e.g., Transit Gateway) connects multiple "spoke" VPCs.

Q: What is CloudFront / Azure CDN / Cloud CDN?
A: The CDN services of AWS, Azure, and GCP.

Q: What is the AWS WAF service?
A: AWS WAF — attach to CloudFront, ALB, API Gateway, or AppSync to protect against OWASP threats.

Q: What is Azure Application Gateway?
A: Azure's Layer 7 load balancer with built-in WAF; combines ALB + WAF in one service.

Q: What is AWS Shield?
A: DDoS protection service. Shield Standard is free; Shield Advanced is paid (with cost protection).

Q: What is the OSI model Layer 4?
A: Transport layer (TCP, UDP). Network Load Balancers operate here.

Q: What is the OSI model Layer 7?
A: Application layer (HTTP, HTTPS, gRPC). Application Load Balancers and WAFs operate here.

Q: What is the default Security Group behavior?
A: Deny all inbound, allow all outbound.

Q: What is the default NACL behavior?
A: Allow all inbound and outbound (default NACL is permissive).

Q: How do you make a private subnet reach the internet?
A: Add a NAT Gateway in a public subnet, then add a route 0.0.0.0/0 → NAT Gateway in the private subnet's route table.
```

---

## SECTION 11 — PRACTICE QUESTIONS

### Q1 (Easy)
What does a private subnet need to reach the internet for outbound-only access?
A) Internet Gateway
B) NAT Gateway
C) VPC Peering
D) Transit Gateway

**Answer: B) NAT Gateway**
**Explanation:** A NAT Gateway allows private subnet resources to initiate outbound internet traffic (e.g., OS updates) without allowing inbound connections. An Internet Gateway would make them reachable.
**Why others are wrong:**
- A) Internet Gateway is bidirectional — would make private subnet publicly accessible.
- C) VPC Peering connects VPCs, not to the internet.
- D) Transit Gateway is a hub for VPCs/VPNs/DX, not internet egress.

---

### Q2 (Easy)
What is the OSI layer of an Application Load Balancer?
A) Layer 2
B) Layer 4
C) Layer 7
D) Layer 3

**Answer: C) Layer 7**
**Explanation:** ALB is a Layer 7 (application layer) load balancer — it inspects HTTP content (URL, headers, host) to route.
**Why others are wrong:**
- A) Layer 2 is the data link layer (switches, VLANs).
- B) Layer 4 is the transport layer (TCP/UDP) — that's NLB.
- D) Layer 3 is the network layer (IP routing).

---

### Q3 (Easy)
What does "non-transitive" mean for VPC Peering?
A) Peering connections are automatically deleted.
B) If A peers with B and B peers with C, A cannot reach C through B.
C) Peering only works in one direction.
D) Peering requires a transit gateway.

**Answer: B) If A peers with B and B peers with C, A cannot reach C through B.**
**Explanation:** VPC Peering is non-transitive. Each VPC must explicitly peer with every other VPC it needs to reach.
**Why others are wrong:**
- A) Peering connections are persistent until deleted.
- C) Peering is bidirectional.
- D) Transit Gateway is an alternative, not a requirement.

---

### Q4 (Easy)
What is the AWS service for a dedicated private network connection from on-prem to AWS?
A) Site-to-Site VPN
B) Direct Connect
C) Transit Gateway
D) VPC Peering

**Answer: B) Direct Connect**
**Explanation:** Direct Connect is AWS's dedicated connection service. Site-to-Site VPN is the encrypted internet alternative.
**Why others are wrong:**
- A) Site-to-Site VPN runs over the public internet.
- C) Transit Gateway is a hub for VPCs/VPNs/DX.
- D) VPC Peering connects VPCs to each other, not to on-prem.

---

### Q5 (Medium)
A company has 30 VPCs that need to communicate. What should they use?
A) VPC Peering for all
B) Transit Gateway
C) Internet Gateway
D) Direct Connect

**Answer: B) Transit Gateway**
**Explanation:** With 30 VPCs, full-mesh peering would require 30 × 29 / 2 = 435 connections — unmanageable. Transit Gateway is the hub-and-spoke solution.
**Why others are wrong:**
- A) 435 peering connections is unmanageable.
- C) Internet Gateway connects VPCs to the internet, not VPC to VPC.
- D) Direct Connect is for on-prem to cloud, not VPC to VPC.

---

### Q6 (Medium)
A Security Group allows inbound 443 from 0.0.0.0/0. The instance is not receiving responses. What could be wrong?
A) Security Groups are stateless.
B) The instance is in a private subnet with no NAT.
C) A NACL is blocking the return traffic.
D) The Security Group does not allow outbound.

**Answer: C) A NACL is blocking the return traffic.**
**Explanation:** Security Groups are stateful — return traffic is auto-allowed regardless of outbound rules. NACLs are stateless — they require explicit return rules. If the NACL allows inbound 443 but doesn't allow ephemeral outbound (1024–65535), the response is blocked.
**Why others are wrong:**
- A) Security Groups are stateful.
- B) The instance is receiving inbound, so it's not a "no route" problem.
- D) Security Groups stateful = outbound doesn't need explicit rule for response.

---

### Q7 (Medium)
A company has compliance requirements that data NOT traverse the public internet. Which connection type?
A) Site-to-Site VPN
B) Direct Connect / ExpressRoute
C) Public API endpoints
D) CDN

**Answer: B) Direct Connect / ExpressRoute**
**Explanation:** Dedicated connections provide private fiber that doesn't touch the public internet. VPN runs over the public internet (even if encrypted).
**Why others are wrong:**
- A) VPN runs over the public internet.
- C) Public APIs are internet-facing.
- D) CDN serves content over the public internet (cached at edge).

---

### Q8 (Medium)
A web app uses ALB in front of EC2 instances. The ALB should:
A) Only allow 443 from the public internet, route to instances in private subnets.
B) Be in a private subnet.
C) Only allow 80 from internal networks.
D) Use NLB instead.

**Answer: A) Only allow 443 from the public internet, route to instances in private subnets.**
**Explanation:** ALB in public subnet terminates SSL from internet. Instances in private subnets are not directly exposed. Best practice.
**Why others are wrong:**
- B) ALB must be in a public subnet to receive traffic.
- C) Web app serves public users on 80/443.
- D) NLB is L4, no content routing. Use ALB for HTTP.

---

### Q9 (Medium)
Which load balancer type preserves the client's source IP?
A) ALB
B) NLB
C) Both
D) Neither

**Answer: B) NLB**
**Explanation:** NLB preserves the source IP by default (L4 passthrough). ALB replaces the source IP with its own (you can get the original via X-Forwarded-For header).
**Why others are wrong:**
- A) ALB uses X-Forwarded-For, not the actual source IP.
- C) Only NLB preserves it natively.
- D) NLB does preserve it.

---

### Q10 (Hard)
A company needs:
- 5 Gbps sustained connectivity to AWS
- Lowest possible latency
- Compliance: no internet traversal
- Backup: cheaper link for failover

What do you recommend?
A) Two Site-to-Site VPNs (one ISP, one 4G)
B) Direct Connect primary, Site-to-Site VPN backup
C) Single Direct Connect
D) Transit Gateway with two VPCs

**Answer: B) Direct Connect primary, Site-to-Site VPN backup**
**Explanation:** DX meets the 5 Gbps, low latency, and compliance requirements. VPN is a cheaper backup. Failover via BGP.
**Why others are wrong:**
- A) VPN can't sustain 5 Gbps reliably (internet-dependent).
- C) Single DX = single point of failure.
- D) Transit Gateway with two VPCs is intra-cloud, doesn't address on-prem connectivity.

---

### Q11 (Hard)
A company is moving from a single VPC to 15 VPCs across 3 accounts. They need shared services (AD, logging) and centralized egress. What's the BEST architecture?
A) 15 VPC peerings between all VPCs
B) Transit Gateway with a shared services VPC
C) 3 separate Transit Gateways, one per account
D) Direct Connect to all 3 accounts

**Answer: B) Transit Gateway with a shared services VPC**
**Explanation:** TGW connects all 15 VPCs through one hub. A shared services VPC contains AD + logging, accessible to all spokes. Centralized egress goes through this VPC too.
**Why others are wrong:**
- A) 15 VPCs = 105 peerings, unmanageable.
- C) 3 separate TGWs = no inter-account communication.
- D) DX is for on-prem, not VPC-to-VPC.

---

### Q12 (Hard)
A company uses BGP over a Direct Connect link to AWS. The link goes down. The on-prem router should:
A) Stop advertising routes until the link is back.
B) Continue advertising the same routes.
C) Switch to a backup VPN via BGP.
D) Wait for the AWS side to fail over.

**Answer: C) Switch to a backup VPN via BGP.**
**Explanation:** With DX + VPN, both advertise the same routes via BGP. DX has a higher preference (lower MED or higher local pref). When DX goes down, BGP withdraws the route, and VPN becomes the active path automatically.
**Why others are wrong:**
- A) Stopping routes would mean total loss of connectivity.
- B) Continuing to advertise the same routes doesn't help.
- D) AWS doesn't initiate failover for on-prem links.

---

### Q13 (Hard)
A web app uses CloudFront + S3 + ALB + EC2. Where should AWS WAF be attached?
A) On the EC2 instances
B) On the ALB only
C) On CloudFront and ALB
D) WAF is not needed in this architecture

**Answer: C) On CloudFront and ALB**
**Explanation:** WAF can be attached to CloudFront (for edge protection) and ALB (for origin protection). Defense in depth.
**Why others are wrong:**
- A) WAF is not typically attached to EC2 directly (you'd use a host-based WAF or 3rd-party agent).
- B) WAF only on ALB leaves CloudFront-to-ALB traffic unprotected.
- D) WAF is needed for OWASP protection.

---

### Q14 (Hard)
What is the difference between Security Group and AWS Network Firewall?
A) Security Group is L7, Network Firewall is L4.
B) Security Group is instance-level, Network Firewall is VPC-wide.
C) They are the same.
D) Network Firewall is a 3rd-party product.

**Answer: B) Security Group is instance-level, Network Firewall is VPC-wide.**
**Explanation:** Security Group operates at the ENI/instance level. AWS Network Firewall operates at the VPC edge, inspecting all traffic entering/leaving the VPC.
**Why others are wrong:**
- A) Both are stateful. Security Group is L4 (with optional L7 via WAF integration).
- C) They are different services.
- D) Network Firewall is an AWS-managed service.

---

### Q15 (Hard)
A company has ALB in front of microservices. They want path-based routing. How does ALB route `/api/*` vs `/images/*`?
A) ALB is L4, can't do path routing.
B) Use ALB listener rules with path patterns.
C) Use NLB instead.
D) Use Route 53.

**Answer: B) Use ALB listener rules with path patterns.**
**Explanation:** ALB supports listener rules with conditions: path patterns, host headers, HTTP headers, query strings, source IP. Each rule has actions (forward to target group, redirect, fixed response).
**Why others are wrong:**
- A) ALB is L7, supports path routing.
- C) NLB is L4, doesn't do path routing.
- D) Route 53 is DNS, doesn't do path routing.

---

### Q16 (Hard)
A company wants to use a CDN to reduce origin bandwidth. They serve 10 TB of video per day, with a 95% cache hit rate. How much origin bandwidth do they save?
A) 0 GB — CDN doesn't save origin bandwidth.
B) 500 GB (5% goes to origin).
C) 9.5 TB (95% cached at edge).
D) 10 TB (CDN serves everything).

**Answer: C) 9.5 TB (95% cached at edge)**
**Explanation:** With 95% cache hit rate, 95% of requests are served from edge, only 5% go to origin. Origin saves 9.5 TB.
**Why others are wrong:**
- A) CDN does save origin bandwidth.
- B) 500 GB is the origin's traffic, not the savings.
- D) CDN serves what's cached; misses go to origin.

---

### Q17 (Hard)
A company uses Azure. They need an L7 load balancer with WAF. Which service?
A) Azure Load Balancer
B) Application Gateway
C) Front Door
D) Traffic Manager

**Answer: B) Application Gateway**
**Explanation:** Application Gateway is Azure's Layer 7 LB with built-in WAF. Azure Load Balancer is L4. Front Door is global LB. Traffic Manager is DNS-based.
**Why others are wrong:**
- A) Azure Load Balancer is L4, no WAF.
- C) Front Door is global LB but more for multi-region.
- D) Traffic Manager is DNS-based, not full LB.

---

### Q18 (Hard)
A developer needs to expose a service running in their VPC to other VPCs in the company, but NOT to the internet. What should they use?
A) Internet Gateway
B) VPC Peering
C) PrivateLink / Private Service Connect
D) ALB with public IP

**Answer: C) PrivateLink / Private Service Connect**
**Explanation:** PrivateLink (AWS) / Private Link (Azure) / Private Service Connect (GCP) exposes a service privately to other VPCs/VNets without public IPs or peering. Best for service sharing without full network access.
**Why others are wrong:**
- A) IGW would expose to internet.
- B) Peering gives full network access, not just the service.
- D) ALB with public IP = internet-exposed.

---

### Q19 (Medium)
A company's app is in a private subnet. They need to download OS updates. What do they configure?
A) Internet Gateway in the private subnet
B) NAT Gateway in a public subnet + route in private subnet's route table
C) VPC Peering
D) Security Group

**Answer: B) NAT Gateway in a public subnet + route in private subnet's route table**
**Explanation:** NAT Gateway in a public subnet (with IGW access). Private subnet's route table has 0.0.0.0/0 → NAT GW. The private subnet's resources can now make outbound connections to the internet.
**Why others are wrong:**
- A) You can't put an IGW in a subnet — it's attached to the VPC.
- C) Peering is VPC-to-VPC.
- D) Security Group is a firewall, doesn't enable internet.

---

### Q20 (Hard)
A company has Direct Connect + Site-to-Site VPN. Both use BGP. The DX goes down. How long does failover take?
A) 0 seconds (instant)
B) 30–60 seconds (BGP convergence + tunnel establishment)
C) 5 minutes (DNS TTL)
D) 1 hour

**Answer: B) 30–60 seconds**
**Explanation:** BGP convergence is fast (sub-second), but the VPN tunnel may need to re-establish (re-key IKE, set up ESP) — typically 30–60 seconds total.
**Why others are wrong:**
- A) Tunnel re-establishment takes time.
- C) DNS TTL doesn't apply to BGP failover.
- D) Far too long.

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| VPC, subnet, route table, IGW, NAT GW basics | 🔴 **CRITICAL** | Foundation of all cloud networking questions. |
| Security Group vs NACL | 🔴 **CRITICAL** | Tested in every networking PBQ. |
| ALB vs NLB | 🔴 **CRITICAL** | Direct scenario questions. |
| VPN vs Direct Connect | 🔴 **CRITICAL** | Direct comparison + scenario questions. |
| VPC Peering vs Transit Gateway | 🔴 **CRITICAL** | Architecture decision points. |
| Public vs private subnet design | 🔴 **CRITICAL** | Three-tier architecture PBQs. |
| WAF role and placement | 🟠 **HIGH** | Security + networking. |
| CDN use cases and limits | 🟠 **HIGH** | Architecture decisions. |
| BGP vs static routes | 🟠 **HIGH** | CompTIA added BGP to CV0-004. |
| Firewall types (SG, NACL, Network FW, WAF) | 🟠 **HIGH** | Common conflation. |
| Hub-and-spoke / central egress pattern | 🟠 **HIGH** | Modern architecture pattern. |
| Subnet route table entries (0.0.0.0/0 → IGW vs NAT) | 🟠 **HIGH** | PBQ troubleshooting. |
| BGP failover in hybrid | 🟡 **MEDIUM** | Niche but tested. |
| Application Gateway (Azure) specifics | 🟡 **MEDIUM** | Azure-specific. |
| SDN concept | 🟡 **MEDIUM** | Conceptual. |
| VLAN in cloud (limited use) | 🟡 **MEDIUM** | Often misunderstood. |
| PrivateLink concept | 🟡 **MEDIUM** | Modern service sharing pattern. |
| Cloud-native DDoS protection (Shield, etc.) | 🟡 **MEDIUM** | Security. |
| Service mesh in microservices | 🟢 **LOW** | Advanced. |
| Specific Azure/GCP service names (less common) | 🟢 **LOW** | Recognition. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-Page Cram Sheet)

### 📋 Cram Sheet: 1.3 Cloud Networking Concepts

**VPC Building Blocks:**

- **VPC** = isolated virtual network. CIDR block, region-wide.
- **Subnet** = VPC subdivision, **one AZ** per subnet.
- **Public subnet** = route to IGW → internet-reachable (if public IP).
- **Private subnet** = no IGW route, uses **NAT GW** for outbound.
- **Route Table** = rules for traffic. `0.0.0.0/0 → IGW` (public) or `0.0.0.0/0 → NAT` (private).
- **Internet Gateway** = bidirectional internet access.
- **NAT Gateway** = outbound-only from private subnets.

**VPC-to-VPC Connectivity:**

- **VPC Peering** = 1-to-1, **non-transitive**. Use for 2–5 VPCs.
- **Transit Gateway** = hub-and-spoke, **transitive**. Use for 5+ VPCs, central egress.

**Connection to Cloud:**

- **Site-to-Site VPN** = encrypted IPsec over internet. Cheap, fast setup.
- **Direct Connect / ExpressRoute / Cloud Interconnect** = private fiber. Expensive, takes weeks, high bandwidth, low latency, no internet traversal.
- **Best practice:** DX as primary + VPN as backup (failover via BGP).

**Routing:**

- **Static routes** = manual, simple, no failover.
- **BGP** = dynamic, scales, automatic failover. Used in hybrid / multi-cloud.
- **Route tables** = subnet-level traffic control.

**Load Balancers:**

- **ALB (L7)** = HTTP-aware, path/host routing, WAF integration. For web apps / APIs.
- **NLB (L4)** = TCP/UDP passthrough, ultra-fast, static IP, preserves source IP. For gaming / IoT / TCP services.
- **Application Gateway (Azure)** = ALB + WAF combined.
- **CDN** = cache at edge for static content; not for dynamic.

**Firewalls:**

- **Security Group (SG)** = stateful, instance-level. **Always used.**
- **NACL** = stateless, subnet-level. **Defense in depth.**
- **Network Firewall** = VPC-edge, IDS/IPS. Centralized.
- **WAF** = L7, OWASP protection. HTTP-aware.

**SDN / VLAN:**

- **SDN** = software-defined networking, programmable, all major clouds use it.
- **VLAN** = Layer 2 segmentation, mostly on-prem. Limited use in cloud.

**Top Exam Triggers:**

- "Lift-and-shift / custom OS" → IaaS (1.1)
- "RPO = 0, RTO = 5 min" → Hot site, sync replication (1.2)
- "Public subnet needs IGW route" → 0.0.0.0/0 → igw-xxx
- "Private subnet outbound only" → NAT Gateway
- "5+ VPCs need to talk" → Transit Gateway
- "2 VPCs only" → VPC Peering
- "Dedicated line, compliance" → Direct Connect
- "Cheap, quick hybrid" → VPN
- "L7 routing, path-based" → ALB
- "TCP, millions of req/s, static IP" → NLB
- "Web attacks / OWASP" → WAF
- "Stateful vs stateless firewall" → SG (stateful) vs NACL (stateless)
- "Instance can't reach internet" → Check NAT + route table
- "BGP failover hybrid" → DX primary, VPN backup

**Acronyms to Know Cold:**

- **VPC / VNet** = Virtual Private Cloud / Network
- **IGW** = Internet Gateway
- **NAT GW** = NAT Gateway
- **TGW** = Transit Gateway
- **NACL** = Network ACL
- **SG** = Security Group
- **ALB / NLB** = Application / Network Load Balancer
- **WAF** = Web Application Firewall
- **BGP** = Border Gateway Protocol
- **SDN** = Software-Defined Networking
- **VLAN** = Virtual LAN
- **VPN** = Virtual Private Network
- **DX** = Direct Connect
- **ER** = ExpressRoute
- **CDN** = Content Delivery Network
- **CIDR** = Classless Inter-Domain Routing
- **IGP / EGP** = Interior / Exterior Gateway Protocol
- **HA** = High Availability
- **PoP** = Point of Presence
- **MEC** = Multi-access Edge Computing

---

## SECTION 14 — LATEST INDUSTRY UPDATES (CV0-004 Relevant)

### AWS Updates (2024–2025)
- **VPC Lattice** (GA 2024) — Application-layer networking across VPCs, accounts, and on-prem. Service-to-service auth, traffic management, observability. Exam-relevant as a modern alternative to TGW for L7 service-to-service.
- **AWS Verified Access** (GA 2023) — Zero-trust VPN alternative. Replaces Client VPN for many use cases.
- **Route 53 zonal shift** (2024) — Shift traffic away from a single AZ within seconds. New DR pattern.
- **Network Load Balancer with Zonal Isolation** (2024) — Contain blast radius per AZ.
- **AWS Direct Connect + MACsec** — Encryption on the DX link itself.
- **CloudFront VPC origins** (GA 2024) — CloudFront can now pull from private VPC origins, removing the need for public ALBs.
- **Security Groups referencing other Security Groups** — Refactored to support cross-VPC SG references (2023).

### Azure Updates (2024–2025)
- **Azure Virtual Network Manager (AVNM)** (GA 2024) — Centralized management of VNets, subnets, security admin rules across subscriptions.
- **Azure Cross-Region Load Balancer** (public preview 2024) — Active-active across regions natively.
- **Azure Front Door Standard/Premium** — Replaces classic Azure CDN for many use cases; integrated WAF.
- **Azure Private Endpoint** enhancements — Now with PrivateLink across regions.
- **Azure Bastion** — Managed jumpbox, replacing the need for self-managed bastion hosts in public subnets.
- **ExpressRoute Metro** (2024) — Two ExpressRoute circuits to the same metro for HA.

### Google Cloud Updates (2024–2025)
- **GCP VPC remains global** (unique) — subnets are regional, VPC spans regions.
- **Cloud Armor Standard + Managed Protection Plus** (2024) — Adaptive protection, DDoS, WAF.
- **Cloud IDS** (GA 2023) — Network threat detection at VPC boundaries.
- **Network Connectivity Center** — Now supports more spoke types.
- **Cloud NAT enhancements** — Per-tag IP allocation, more granular control.
- **Cloud Load Balancing** — Added support for Envoy-based advanced traffic management.

### Service Mesh
- **Istio** remains the leading service mesh, now with **Ambient Mesh** (2024) — sidecar-less mode, lighter weight.
- **Linkerd** (CNCF graduated) — Lighter alternative to Istio.
- **Consul** by HashiCorp.
- **AWS App Mesh** reached end-of-life (2024) — pushed users to VPC Lattice.
- **Azure Service Mesh** — Istio-based, now in Azure Kubernetes Service.

### CDN / Edge
- **Cloudflare** continues to push into edge compute (Workers, Durable Objects, R2).
- **Fastly** is a strong CDN + edge compute player.
- **AWS Lambda@Edge** is being merged into **CloudFront Functions** (2024) for simpler use cases.
- **CloudFront KeyValueStore** (2023) — Store config at the edge for CloudFront Functions.

### AI Networking
- **High-bandwidth networking for AI workloads** is now a major focus — NVIDIA Spectrum-X, RoCE, InfiniBand.
- **AWS** is launching **UltraClusters** with EFA (Elastic Fabric Adapter) for multi-node training.
- **Azure** has **InfiniBand-enabled ND H100 v5** VMs.
- **GCP** has **A3 VMs** with NVIDIA H100 + high-bandwidth networking.
- **CompTIA angle:** AI workloads drive the need for high-bandwidth, low-latency networking — same concepts as HPC, just newer.

### BGP / Hybrid
- **AWS Direct Connect + BGP communities** — More granular route filtering.
- **BGP routing in Azure Virtual WAN** — Hub-and-spoke with dynamic routing.
- **GCP** supports **BGP** for Cloud Interconnect and Cloud VPN.
- **EVPN (Ethernet VPN)** is gaining traction in cloud networks (data center extension).

### Security / Firewalls
- **Zero-trust networking** continues to grow (AWS Verified Access, Azure Zero Trust, BeyondCorp).
- **Cloud-native NGFW** vendors (Palo Alto VM-Series, Check Point, Fortinet) integrate deeper with cloud networking.
- **eBPF-based security** (Cilium Tetragon, Falco) is the next-generation cloud-native security tool.

### Industry Trends
- **Network as Code** — VPCs, subnets, route tables, security groups all defined in Terraform / CloudFormation / Bicep.
- **Multi-cloud networking** — Companies like Aviatrix, Alkira, HashiCorp Consul are building multi-cloud networking overlays.
- **Edge networking** — 5G MEC continues to grow; AWS Wavelength, Azure Edge Zones expanding.

---

## ✅ END OF OBJECTIVE 1.3 NOTES

**Coverage Summary:**
- ✅ Public/private connections (VPN, Dedicated)
- ✅ VPC (Peering, Transit Gateway)
- ✅ Subnets (public/private, route tables)
- ✅ Routing & switching (VLAN, SDN, BGP, static routes, route tables)
- ✅ Network functions (ALB, NLB, Application Gateway, CDN, Firewalls)

**Next step:** Ping me for **1.4 — Storage Resources and Technologies** (tiered storage hot/warm/cold/archive, SSD vs HDD, object vs block vs file, performance/cost implications) — shorter objective but storage questions are heavily tested.

Study tip from Cikgu Danial: *"For 1.3, the most exam-relevant pattern is the three-tier VPC design: public ALB subnets, private app subnets, isolated DB subnets. If you can draw that on a whiteboard with route tables, security groups, and a NAT gateway, you can answer 80% of 1.3 questions. Practice it."* 💪
