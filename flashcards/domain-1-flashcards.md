# Domain 1 Flashcards

> Markdown flashcards in `Q:` / `A:` format. Anki-importable via the [Anki Markdown importer](https://ankiweb.net/shared/info/2055492159) plugin.

---

## Card 1 — Service Models

**Q:** Name the four cloud service models from most customer-managed to least.
**A:** IaaS → PaaS → SaaS → FaaS. (FaaS has the most provider management of the four for infrastructure, but customer still owns the function code.)

---

## Card 2 — Shared Responsibility (OS ownership)

**Q:** In which service model does the **customer** own the OS?
**A:** **IaaS.** In PaaS, SaaS, and FaaS, the provider owns the OS.

---

## Card 3 — Always Customer

**Q:** What two responsibilities does the customer **always** own, in every model?
**A:** **Data** and **Identity / IAM.**

---

## Card 4 — Region vs AZ

**Q:** What is the difference between a Region and an Availability Zone?
**A:** A **Region** is a geographic area (e.g., `us-east-1`); an **Availability Zone (AZ)** is one or more discrete data centers inside a region, connected by low-latency links. AZs share a region but have independent power, cooling, and networking.

---

## Card 5 — RTO

**Q:** What does RTO stand for, and what does it measure?
**A:** **Recovery Time Objective** — the maximum acceptable duration of a service outage before recovery. *"How fast must we be back up?"*

---

## Card 6 — RPO

**Q:** What does RPO stand for, and what does it measure?
**A:** **Recovery Point Objective** — the maximum acceptable amount of data loss, measured in time. *"How much data can we afford to lose?"*

---

## Card 7 — DR Site Selection

**Q:** Match the DR site type to its cost and RTO: hot, warm, cold.
**A:** **Hot** = $$$$ / RTO minutes, RPO seconds. **Warm** = $$ / RTO hours, RPO minutes. **Cold** = $ / RTO 24h+, RPO hours.

---

## Card 8 — Pilot Light

**Q:** What is a "pilot light" DR strategy?
**A:** A minimal version of the production environment is always running in the DR region (the "pilot light"); if the primary fails, capacity is rapidly scaled up using IaC templates and pre-replicated data. Cheaper than hot, faster than warm.

---

## Card 9 — Cloud Bursting

**Q:** What is **cloud bursting**?
**A:** A hybrid pattern where an application runs in a private cloud / on-prem during normal load and "bursts" into a public cloud when capacity is exceeded.

---

## Card 10 — Edge Location

**Q:** What is an edge location, and what is it for?
**A:** A CDN point-of-presence geographically closer to end users. Used to cache static and dynamic content, reduce latency, and offload origin servers.

---

## Card 11 — Networking Trio

**Q:** Differentiate Security Group, NACL, and Route Table.
**A:**

- **Security Group** — instance-level, **stateful**, default deny inbound.
- **NACL** — subnet-level, **stateless**, evaluated rule-by-rule with explicit allow/deny.
- **Route Table** — controls where traffic leaving a subnet is forwarded (IGW, NAT GW, transit GW, peered VPC).

---

## Card 12 — Load Balancer Layers

**Q:** What's the difference between L4 and L7 load balancing?
**A:** **L4** (Transport) makes decisions on IP/port/TCP — fast, content-agnostic (NLB, Azure Load Balancer). **L7** (Application) inspects HTTP headers, paths, cookies — smarter routing, TLS termination (ALB, Azure App Gateway, Cloud Load Balancing HTTP(S)).

---

## Card 13 — Storage Types

**Q:** Compare block, file, and object storage.
**A:**

- **Block** — raw volumes mounted to a single instance. Best for databases. (EBS, Azure Disk)
- **File** — shared filesystem via NFS/SMB. Multi-instance, hierarchical. (EFS, Azure Files)
- **Object** — flat namespace, HTTP API, virtually unlimited scale. (S3, Azure Blob, Cloud Storage)

---

## Card 14 — RAID Levels

**Q:** What are the trade-offs of RAID 0, 1, 5, 6, 10?
**A:**

- **RAID 0** — striping, fastest, **no redundancy**.
- **RAID 1** — mirroring, redundant, 50% capacity.
- **RAID 5** — striping + single parity, good read, slow write.
- **RAID 6** — striping + double parity, can tolerate 2 disk failures.
- **RAID 10** — stripe of mirrors, best performance + redundancy, 50% capacity.

---

## Card 15 — Cloud-Native vs Traditional

**Q:** Name three characteristics of a cloud-native application.
**A:** **Stateless**, **horizontally scalable**, **declaratively managed** (e.g., via K8s manifests). Also: loosely coupled, API-driven, event-driven, observable, resilient.

---

## Card 16 — Container vs VM

**Q:** What is the key architectural difference between a container and a VM?
**A:** A **VM** virtualizes the **hardware** (each VM has its own kernel, OS). A **container** virtualizes the **OS** (containers share the host kernel; only the app + user-space libs are isolated).

---

## Card 17 — Kubernetes Pod

**Q:** What is a Kubernetes Pod?
**A:** The smallest deployable unit in Kubernetes: one or more co-scheduled containers sharing a network namespace and storage volumes. Pods are ephemeral; workloads are managed by Deployments/StatefulSets.

---

## Card 18 — Hypervisor Types

**Q:** Compare Type 1 and Type 2 hypervisors.
**A:** **Type 1** (bare-metal) runs directly on hardware — ESXi, Hyper-V, KVM, Xen. Production-grade. **Type 2** (hosted) runs as an app on a host OS — VirtualBox, VMware Workstation. Dev/test only.

---

## Card 19 — Live Migration

**Q:** What is live migration?
**A:** Moving a running VM from one physical host to another **with no downtime**, by streaming memory and CPU state over a high-speed network. Used for maintenance, load balancing, hardware refresh.

---

## Card 20 — Pricing Models

**Q:** Name three cloud pricing models and their typical use.
**A:**

- **On-Demand** — pay per second/hour, no commit. Dev/test, unpredictable workloads.
- **Reserved** — 1- or 3-year commit, ~40–60% discount. Steady-state production.
- **Spot / Preemptible** — up to 90% discount, can be reclaimed. Fault-tolerant batch.

---

## Card 21 — TCO

**Q:** What does TCO stand for and what should it include?
**A:** **Total Cost of Ownership** — includes compute, storage, network egress, licensing, support contracts, ops staff time, training, and **exit / migration cost** when comparing cloud to on-prem.

---

## Card 22 — OLTP vs OLAP

**Q:** Differentiate OLTP and OLAP databases.
**A:** **OLTP** — high-volume short transactions, normalized schema, row-store (e.g., MySQL, PostgreSQL, Aurora). **OLAP** — analytical queries over large data, denormalized star/snowflake schema, columnar store (e.g., BigQuery, Redshift, Synapse).

---

## Card 23 — ACID vs BASE

**Q:** When would you choose BASE over ACID?
**A:** When you need **massive scale and availability** and can tolerate eventual consistency (e.g., social feeds, IoT telemetry, session caches). ACID for financial / inventory / order data; BASE for non-critical, high-throughput workloads.

---

## Card 24 — Sharding vs Replication

**Q:** Compare sharding and replication.
**A:** **Replication** copies the same data across multiple nodes (read scale + HA). **Sharding** splits data across nodes by key (write scale). They are often combined: sharded primary + replicated secondaries per shard.

---

## Card 25 — Caching Patterns

**Q:** Name three cache-aside patterns and when each is used.
**A:** **Read-through** (app reads from cache, cache reads from DB on miss) — common default. **Write-through** (writes go to cache + DB synchronously) — read-heavy with strong consistency. **Write-behind** (writes async to DB) — high write throughput, eventual consistency.

---

> Add new cards as you study. Keep them atomic — one fact per card.
