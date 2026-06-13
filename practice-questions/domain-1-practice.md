# Domain 1 Practice Questions

> Self-authored scenario-based questions derived from the [Domain 1 notes](../objectives/domain-1/). These are **not** real exam items. Use them to test your own understanding.

---

## Q1 — Service Model Selection

A company wants to migrate a legacy on-prem Java application that depends on a specific JDK version and a custom JDBC driver. They want minimal code changes and the ability to install custom OS packages.

**Which service model fits best?**

- A. SaaS
- B. PaaS
- C. IaaS
- D. FaaS

<details><summary>Answer</summary>
**C. IaaS.** Legacy apps needing custom OS-level configuration and full control (custom JDK, OS packages) belong on IaaS. PaaS abstracts the OS, removing the ability to install custom packages.
</details>

---

## Q2 — Shared Responsibility

In **AWS EC2 (IaaS)**, who is responsible for patching the guest operating system?

- A. AWS
- B. The customer
- C. Both share the responsibility
- D. Neither — EC2 uses an immutable OS

<details><summary>Answer</summary>
**B. The customer.** In IaaS, the customer owns the OS, including patching. AWS patches the host hypervisor and physical infrastructure, but the EC2 guest OS is yours.
</details>

---

## Q3 — DR Site Selection

A payments processor must restore service within 5 minutes of an outage and can lose at most 30 seconds of transaction data.

**Which DR strategy is appropriate?**

- A. Cold site
- B. Warm site
- C. Pilot light
- D. Hot site / active-active

<details><summary>Answer</summary>
**D. Hot site / active-active.** Sub-5-minute RTO and sub-minute RPO require synchronous replication and active-active (or at minimum, hot standby with automated failover).
</details>

---

## Q4 — RTO vs RPO

**RTO** measures ___; **RPO** measures ___.

- A. Data loss tolerance; downtime tolerance
- B. Downtime tolerance; data loss tolerance
- C. Recovery cost; recovery time
- D. Backup frequency; restore speed

<details><summary>Answer</summary>
**B. Downtime tolerance; data loss tolerance.** RTO = "How long can we be down?" RPO = "How much data can we lose?"
</details>

---

## Q5 — Networking

You need to allow an instance in a private subnet to download OS updates from the internet, but the instance must **not** be reachable from the internet.

**What do you use?**

- A. Internet Gateway
- B. NAT Gateway
- C. Security Group
- D. Direct Connect

<details><summary>Answer</summary>
**B. NAT Gateway.** A NAT Gateway lets private-subnet instances initiate outbound traffic (e.g., updates) while blocking unsolicited inbound traffic. An IGW would expose the instance to inbound; an SG is a firewall, not a routing component.
</details>

---

## Q6 — Storage

Which storage type is best for a large media library served via HTTP with no filesystem mount?

- A. Block
- B. File
- C. Object
- D. Archive (with retrieval delay)

<details><summary>Answer</summary>
**C. Object storage** (S3 / Azure Blob / GCS). Object stores expose a flat HTTP-based API ideal for media, with virtually unlimited scale and tiered lifecycle options.
</details>

---

## Q7 — Containerization

Which Kubernetes object provides a **stable network endpoint** (virtual IP + DNS name) for a set of pods?

- A. Deployment
- B. Pod
- C. Service
- D. ConfigMap

<details><summary>Answer</summary>
**C. Service.** Deployments manage pod replicas; Services expose them via a stable ClusterIP and DNS name.
</details>

---

## Q8 — Cost Optimization

A batch processing job tolerates interruption and can be restarted. **Which pricing model offers the largest discount?**

- A. On-Demand
- B. Reserved Instances
- C. Savings Plans
- D. Spot / Preemptible Instances

<details><summary>Answer</summary>
**D. Spot / Preemptible.** Up to ~90% off On-Demand, but the cloud provider can reclaim the instance with ~30 s notice. Ideal for fault-tolerant batch workloads.
</details>

---

## Q9 — Database

You need a globally distributed database with single-digit-millisecond reads and writes, and you can tolerate eventual consistency.

**Which is the best fit?**

- A. Relational OLTP (e.g., Aurora)
- B. OLAP columnar (e.g., Redshift)
- C. Multi-region NoSQL with eventual consistency (e.g., DynamoDB Global Tables, Cosmos DB)
- D. In-memory cache (e.g., ElastiCache)

<details><summary>Answer</summary>
**C. Multi-region NoSQL with eventual consistency.** The use case explicitly allows BASE semantics in exchange for global low-latency.
</details>

---

## Q10 — Scenario Synthesis

A startup's web app has steady traffic 9-to-5, spiky evenings, and occasional batch jobs at night. They want minimum ops overhead.

**Recommend a deployment model.**

- A. Three reserved EC2 instances behind an ALB
- B. AWS Lambda for the API, Fargate for any long-running worker, S3 + CloudFront for static assets
- C. Single EC2 instance running the whole stack
- D. On-prem servers with cloud backup

<details><summary>Answer</summary>
**B. Serverless-first.** The workload is spiky and idle-friendly; Lambda and Fargate scale to zero and remove patching. Static assets on S3 + CloudFront are cheapest and most resilient.
</details>

---

> Want more? Generate your own questions by converting each "Top Exam Triggers" bullet in the notes into a Q.
