# Domain 1 Cheatsheet — Cloud Architecture & Design

> One-page density reference. For full context, open the linked objective file.

## 1.1 — Cloud Service Models & Shared Responsibility

| Model | You manage | Provider manages | Example |
|---|---|---|---|
| **IaaS** | OS, middleware, runtime, app, data | Hardware, hypervisor, network | AWS EC2, Azure VM |
| **PaaS** | App, data | OS, runtime, middleware, infra | AWS Elastic Beanstalk, Azure App Service |
| **SaaS** | Data, identity, access | Everything else | Microsoft 365, Salesforce |
| **FaaS** | Function code, triggers | Infra + scaling + idle | AWS Lambda, Azure Functions |

**Shared responsibility rule of thumb:**

> Customer **always** owns: data + IAM. Provider **always** owns: physical DC + hardware + host infra. OS ownership flips at the IaaS/PaaS line.

🔗 Full notes: [1.1](../objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md)

---

## 1.2 — Service Availability

| Concept | Definition |
|---|---|
| **Region** | Geographic area containing 2+ AZs. Independent failure domain. |
| **Availability Zone (AZ)** | One or more discrete data centers within a region, low-latency links. |
| **Edge location** | CDN / point-of-presence for caching, closest to users. |
| **RTO** | Recovery Time Objective — how fast must service be restored. |
| **RPO** | Recovery Point Objective — how much data loss is acceptable. |

**DR site selection:**

| Site | Cost | RTO | RPO | Use when |
|---|---|---|---|---|
| **Hot** | $$$$ | Minutes | Seconds | Mission-critical, near-zero loss tolerated |
| **Warm** | $$ | Hours | Minutes | Important but tolerable downtime |
| **Cold** | $ | 24+ h | Hours | Archival, low-priority workloads |
| **Pilot light** | $$$ | Tens of min | Minutes | Core services always on, scale on demand |

🔗 Full notes: [1.2](../objectives/domain-1/1.2-service-availability-concepts.md)

---

## 1.3 — Cloud Networking (key terms)

- **VPC / VNet** — isolated virtual network.
- **Subnet** — CIDR range within a VPC; public or private.
- **Route table** — controls traffic leaving a subnet.
- **Internet Gateway (IGW)** — public ingress/egress for a VPC.
- **NAT Gateway** — outbound-only internet for private subnets.
- **Security Group** — instance-level, stateful firewall.
- **NACL** — subnet-level, stateless firewall.
- **Load Balancer** — L4 (TCP/UDP) or L7 (HTTP/HTTPS).
- **VPN** — encrypted tunnel over public internet.
- **Direct Connect / ExpressRoute** — private dedicated line.
- **CDN** — caches content at edge locations.
- **DNS** — name resolution (Route 53, Azure DNS, Cloud DNS).

🔗 Full notes: [1.3](../objectives/domain-1/1.3-cloud-networking-concepts.md)

---

## 1.4 — Storage Types

| Type | Access | Examples | Use case |
|---|---|---|---|
| **Block** | Raw volumes, mounted | EBS, Azure Disk, Persistent Disk | Databases, boot disks |
| **File** | Shared filesystem, NFS/SMB | EFS, Azure Files, Filestore | Lift-and-shift, content mgmt |
| **Object** | HTTP API, flat namespace | S3, Azure Blob, Cloud Storage | Backups, media, data lakes |
| **Archive** | Cold, retrieval delay | S3 Glacier, Azure Archive | Compliance, long-term |

🔗 Full notes: [1.4](../objectives/domain-1/1.4-storage-resources-and-technologies.md)

---

## 1.5 — Cloud-Native Design (the 12-factor hits)

- **Codebase** — one repo, many deploys.
- **Dependencies** — explicit, isolated.
- **Config** — in environment, not code.
- **Backing services** — treat as attached resources.
- **Build, release, run** — strict separation.
- **Processes** — stateless, share-nothing.
- **Port binding** — self-contained services.
- **Concurrency** — scale out via process model.
- **Disposability** — fast startup, graceful shutdown.
- **Dev/prod parity** — keep environments similar.
- **Logs** — treat as event streams.
- **Admin processes** — run as one-off tasks.

🔗 Full notes: [1.5](../objectives/domain-1/1.5-cloud-native-design-concepts.md)

---

## 1.6 — Containerization

- **Image** = read-only template with app + dependencies.
- **Container** = running instance of an image.
- **Pod** = smallest K8s deployable unit (1+ co-scheduled containers).
- **Deployment** = declarative desired state for pods.
- **Service** = stable network endpoint for a set of pods.
- **Namespace** = cluster-scoped partition.
- **Serverless containers** = Fargate, Azure Container Apps, Cloud Run.

🔗 Full notes: [1.6](../objectives/domain-1/1.6-containerization-concepts.md)

---

## 1.7 — Virtualization

- **Type 1 hypervisor** — bare metal (ESXi, Hyper-V, KVM).
- **Type 2 hypervisor** — hosted on OS (Workstation, VirtualBox).
- **vCPU** = thread of a physical core.
- **Oversubscription** = ratio of vCPU to pCPU > 1 (e.g., 4:1).
- **Live migration** = move running VM with no downtime.
- **NUMA** = non-uniform memory access; affects VM placement.

🔗 Full notes: [1.7](../objectives/domain-1/1.7-virtualization-concepts.md)

---

## 1.8 — Cost Considerations

- **CapEx** vs **OpEx** — up-front hardware vs pay-as-you-go.
- **TCO** = 3-yr cost comparison including ops, licensing, exit cost.
- **On-Demand** = highest cost, no commitment.
- **Reserved** = 1- or 3-yr commit, ~40–60% discount.
- **Spot / Preemptible** = cheapest, can be reclaimed.
- **Tagging** = cost allocation, chargeback/showback.
- **FinOps** = ongoing cost optimization practice.

🔗 Full notes: [1.8](../objectives/domain-1/1.8-cost-considerations.md)

---

## 1.9 — Database Concepts

| Concept | Meaning |
|---|---|
| **OLTP** | High-volume transactions, normalized. |
| **OLAP** | Analytical queries, denormalized, columnar. |
| **ACID** | Atomicity, Consistency, Isolation, Durability (RDBMS). |
| **BASE** | Basically Available, Soft state, Eventually consistent (NoSQL). |
| **Replication** | Sync vs async; leader-follower vs multi-leader. |
| **Sharding** | Horizontal partition across nodes. |
| **Caching** | Redis, Memcached; read-through, write-through, write-behind. |
| **Connection pooling** | Reuse DB connections to avoid overhead. |

🔗 Full notes: [1.9](../objectives/domain-1/1.9-database-concepts.md)
