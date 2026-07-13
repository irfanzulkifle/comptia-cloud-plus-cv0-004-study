# Objective 1.10 — Compare and Contrast Methods for Optimizing Workloads Using Cloud Resources

## SECTION 1 — Exam Objectives

**CompTIA Cloud+ CV0-004 · Domain 1.0 (Cloud Architecture) · Objective 1.10**

- Compare and contrast methods for optimizing workloads using cloud resources, and select the right technique (compute, orchestration, workflow, network, storage, managed services) for a given scenario and cost/performance goal.
- **What the exam expects you to know:**
  - **Compute resources** — optimizing Virtual Machines (VMs), Containers, and Serverless functions (right-size, scale, offload).
  - **Orchestration** — automating the deployment, scaling, and management of many compute units together.
  - **Workflow** — chaining tasks/steps into an automated, observable pipeline.
  - **Network** — reducing Latency and increasing Throughput between clients, services, and regions.
  - **Storage** — tuning Input/Output Operations Per Second (IOPS) and Throughput (MB/s) for block and object storage.
  - **Managed services** — offloading operational burden (patching, HA, backups) to the provider.
- **Exam tip:** Objective 1.10 is heavily scenario-based. Given a workload (e.g., a spiky batch job or a low-latency DB), pick the optimization method and the matching cloud feature that delivers it. Think in terms of right-size, scale (up/out/in), cache, distribute, and offload — you are NOT expected to memorize one vendor's console clicks, only the concept.

---

## SECTION 2 — EXAM KNOWLEDGE

### Compute — Virtual Machine (VM)
- **Definition:** A VM is a software-emulated server created by a hypervisor that lets many isolated guest operating systems share one physical host. Optimizing VMs means selecting the correct instance size (vCPU, RAM, network) and scaling strategy for the workload.
- **Why it matters:** VMs are the default "lift-and-shift" target and usually the largest line item on a cloud bill; small mis-sizing wastes money and causes contention.
- **Analogy:** Renting an apartment — you get a private OS but share the building's foundations (hardware). Paying for a 4-bedroom when you live alone is waste.
- **Example:** A web tier averages 18% CPU on an 8-vCPU VM. Right-sizing to 2 vCPU (vertical scaling down) keeps performance identical while cutting compute cost roughly 70%. Vertical scaling changes size of one VM; horizontal scaling adds more VMs behind a load balancer.

### Compute — Container
- **Definition:** A container packages an application and its dependencies into a lightweight, isolated unit that shares the host OS kernel, started in seconds and far denser than VMs. Optimization comes from small images, right-sized CPU/memory limits, and orchestration that packs containers efficiently.
- **Why it matters:** Containers reduce overhead and speed deployment, but without limits a "noisy neighbor" container can starve others.
- **Analogy:** Shipping containers on a cargo ship — standardized boxes that stack tightly and move between trucks, trains, and ships without repacking.
- **Example:** A microservice container requests 256 MB but actually uses 90 MB; tightening the memory limit lets the cluster schedule 3× more pods per node, lowering the node count and bill. Container orchestration (e.g., Kubernetes) auto-places and reschedules containers when nodes fail.

### Compute — Serverless
- **Definition:** Serverless (Function-as-a-Service) runs individual functions triggered by events; the provider allocates compute only while code executes and you pay per invocation/ms, with no always-on server to manage. Optimization = writing short, stateless functions and letting the platform scale to zero.
- **Why it matters:** Ideal for spiky, intermittent, or unpredictable workloads where a 24/7 VM would sit idle and waste money.
- **Analogy:** A taxi you summon only when you ride — you pay for the trip, not for the car parked all day.
- **Example:** A photo-upload app resizes images. Using a serverless function triggered on upload handles 10 requests/month and 10,000/month identically at near-zero cost when idle, whereas a VM would bill 720 hours/month regardless. Cold starts are the main trade-off to know.

### Orchestration
- **Definition:** Orchestration is the automated arrangement, coordination, and management of many compute resources (VMs, containers, networks) as a single declared system — desired state is specified and the tool continuously enforces it.
- **Why it matters:** Manual scaling and placement does not scale; orchestration delivers self-healing, elastic scaling, and consistent rollouts, directly optimizing utilization and uptime.
- **Analogy:** A traffic control tower that watches every plane and reroutes automatically when one runway closes.
- **Example:** A container orchestrator keeps 5 replicas of a service, detects a crashed pod, and launches a replacement within seconds while shifting load — no human paged at 3 a.m. It also bin-packs workloads to use fewer hosts.

### Workflow
- **Definition:** Workflow (sometimes called orchestration-of-tasks) is the automation of a multi-step business or data process where each step's output feeds the next, with built-in retry, branching, and state tracking.
- **Why it matters:** Manual multi-step jobs are slow and error-prone; a managed workflow engine reduces latency between steps, handles failures, and gives visibility.
- **Analogy:** A factory assembly line — raw material enters, each station transforms it, and a jammed station triggers a defined recovery instead of stopping the whole plant.
- **Example:** Nightly ETL: extract from DB → validate → transform → load to warehouse → notify. A workflow service runs each stage, retries a transient failure twice, and only emails on success, finishing in 12 minutes instead of a fragile 2-hour cron script.

### Network — Latency
- **Definition:** Latency is the time delay for a packet to travel from source to destination, usually measured in milliseconds. Optimizing latency means placing resources closer to users and avoiding unnecessary hops.
- **Why it matters:** High latency degrades UX (slow pages, laggy apps) and can break real-time systems; it is often the cheapest performance win.
- **Analogy:** A letter delivered across town versus across the world — distance and sorting stops add delay.
- **Example:** A Malaysian user hitting a US-East app sees 250 ms round-trips; adding a regional edge cache / CDN or deploying in the nearest region cuts it to under 30 ms. Caching static assets at the edge removes round-trips to origin entirely.

### Network — Throughput
- **Definition:** Throughput is the amount of data successfully moved per unit of time (e.g., Mbps, Gbps). Optimizing throughput means increasing the bandwidth/capacity of links and removing bottlenecks.
- **Why it matters:** High latency hurts responsiveness; low throughput hurts bulk transfer — big backups, video, or data replication stall and miss windows.
- **Analogy:** Latency is how long the first drop of water takes to arrive; throughput is how wide the pipe is once flowing.
- **Example:** A 1 Gbps link saturates during a nightly 2 TB database backup, blowing the maintenance window. Upgrading to a 10 Gbps connection or compressing/parallelizing the transfer finishes inside the window. Right-sizing instance network performance (bandwidth tiers) also matters.

### Storage — IOPS
- **Definition:** IOPS (Input/Output Operations Per Second) measures how many small read/write commands a storage volume can handle each second. Optimizing IOPS matters for random, small, latency-sensitive operations like databases.
- **Why it matters:** A database with too few IOPS develops queueing and slow queries even if total throughput looks fine; provisioned IOPS guarantees performance.
- **Analogy:** A toll booth — IOPS is how many cars per minute can pass; more booths (higher IOPS) clear a queue of commuters faster.
- **Example:** An OLTP database on a default volume hits 3,000 IOPS and queries lag at peak. Moving to a provisioned-IOPS SSD rated at 16,000 IOPS removes the bottleneck and stabilizes response time. Monitor queue depth to confirm IOPS — not throughput — is the limit.

### Storage — Throughput
- **Definition:** Throughput (here, storage) is the total data volume moved per second (MB/s) for large sequential operations like backups, media, or log shipping.
- **Why it matters:** Some workloads are bandwidth-bound, not IOPS-bound; paying for IOPS you don't need wastes money while a throughput-optimized volume is cheaper.
- **Analogy:** Moving furniture by truck — you care about total load per trip (MB/s), not how many individual boxes (IOPS).
- **Example:** A video-rendering job streams 500 MB/s from storage; a general-purpose volume caps at 250 MB/s and the render idles waiting. Switching to a throughput-optimized or larger general-purpose volume raises the cap, cutting render time in half. Match volume type to access pattern.

### Managed Services
- **Definition:** A managed service is a cloud offering where the provider operates the underlying infrastructure, patching, replication, scaling, and backups, while you only configure and use it. Optimizing via managed services means trading some control for reduced operational toil and built-in HA.
- **Why it matters:** Running your own database or message queue means you own failures, patching, and on-call; managed equivalents offload that, improving reliability and speed-to-market.
- **Analogy:** Taking the train (managed) instead of driving and maintaining your own car (self-managed) — someone else handles the engine.
- **Example:** Instead of provisioning VMs and installing PostgreSQL with manual backups, use a managed database: the provider handles failover, patches, and point-in-time recovery, so the team focuses on the app. Trade-off: less OS-level control and possible vendor lock-in.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Right-sizing an oversized VM fleet (Compute/VM):** A Malaysian fintech ran batch reporting on 16× 16-vCPU VMs at 12% average CPU. After analysis they resized to 4× 4-vCPU and used scheduled scaling, cutting the monthly compute bill by ~65% with no report-delay complaints.
- **Container bin-packing during a sale (Compute/Container + Orchestration):** An online fashion retailer containerized its catalog service and let the orchestrator bin-pack pods. During a 12.12 sale, autoscaling grew replicas from 6 to 40 across the same node pool instead of provisioning a separate static cluster, avoiding over-provisioning.
- **Serverless for unpredictable uploads (Compute/Serverless):** A government open-data portal receives file submissions in unpredictable bursts. A serverless function validates and stores each file on arrival, scaling from zero to thousands concurrently during a campaign, with cost near zero between campaigns.
- **CDN edge caching to cut latency (Network/Latency):** A news portal serving Kuala Lumpur users from a single distant region saw 280 ms loads. Adding a content delivery network cached images and articles at regional edge locations, dropping median load time to under 40 ms and reducing origin bandwidth.
- **Provisioned IOPS for a busy database (Storage/IOPS):** A ride-hailing app's booking database suffered slow writes at peak on a default volume. Migrating to a provisioned-IOPS SSD removed query latency spikes, keeping booking confirmation under 100 ms during rush hour.

---

## SECTION 4 — COMPARISON TABLES

| Optimization lever | Best when workload is… | Primary benefit | Key trade-off |
|---|---|---|---|
| Vertical scaling (bigger VM) | Steady, single-instance, stateful | Simple, no app rewrite | Downtime to resize; hard ceiling |
| Horizontal scaling (more VMs) | Stateless, variable demand | Near-unlimited, resilient | Needs load balancer + stateless design |
| Containers | Many small, portable services | Dense, fast start, consistent | Orchestration learning curve |
| Serverless | Spiky, event-driven, short tasks | Scale-to-zero, no idle cost | Cold starts; execution limits |

| Storage metric | Measures | Ideal workload | Tuning action |
|---|---|---|---|
| IOPS | Small random read/writes per sec | Databases, transactions | Provisioned-IOPS SSD; more volumes |
| Throughput (MB/s) | Bulk data moved per sec | Backups, media, logs | Throughput-optimized / larger vol |
| Latency (network) | Round-trip delay (ms) | Interactive, real-time apps | Edge cache / CDN; nearer region |
| Throughput (network) | Data rate (Mbps/Gbps) | Bulk transfer, replication | Larger bandwidth tier; compression |

| Build option | You manage | Provider manages | When to choose |
|---|---|---|---|
| Self-managed on VMs | OS, patches, HA, backups | Hardware, hypervisor | Need full control / niche config |
| Managed service | Config & data | Patching, HA, backups, scaling | Speed, reliability, less ops toil |
| Serverless / PaaS | Just code & events | Nearly everything | Unpredictable or intermittent load |

| Orchestration vs Workflow | Scope | Example tool concept | Optimizes for |
|---|---|---|---|
| Orchestration | Infrastructure/compute placement | Container/VM scheduler | Utilization, self-healing, scaling |
| Workflow | Multi-step task sequencing | Step/pipeline engine | Correct order, retries, visibility |

| Scaling type | Direction | Trigger | Example |
|---|---|---|---|
| Scale up/out | Add capacity | High CPU / demand | Add replicas or vCPU |
| Scale in/down | Remove capacity | Low utilization | Shrink to save cost |
| Scale to zero | Stop billing | No traffic | Serverless idle periods |

## SECTION 5 — AWS PROVIDER MAPPING

This section maps the vendor-neutral 1.10 sub-objectives to the AWS-specific services you may see on the exam when a question uses AWS terminology. AWS-only mapping:

- **Compute — Virtual Machine →** Amazon EC2 (Elastic Compute Cloud) for the VM instances, with AWS Auto Scaling Groups to scale the fleet horizontally based on CPU, schedule, or custom metrics.
- **Compute — Container →** Amazon ECS (Elastic Container Service) and Amazon EKS (Elastic Kubernetes Service); both support AWS Fargate as a serverless compute engine for containers (no nodes to manage).
- **Compute — Serverless →** AWS Lambda, which runs functions on event triggers and scales to zero, billed per invocation and duration.
- **Orchestration →** Amazon ECS and Amazon EKS handle container orchestration (placement, scaling, self-healing); AWS Step Functions can orchestrate multi-service workflows as well.
- **Workflow →** AWS Step Functions, a managed state-machine service for sequencing tasks with branching, retries, and observability across Lambda, ECS, and other services.
- **Network — Latency →** Amazon CloudFront (global CDN caching content at edge locations) and AWS Global Accelerator (routes traffic to the nearest healthy regional endpoint over the AWS backbone) to reduce round-trip delay.
- **Storage — IOPS →** Amazon EBS (Elastic Block Store), specifically the io2 (and io1) Provisioned IOPS SSD volume types for guaranteed, high, low-latency IOPS suited to databases.
- **Storage — Throughput →** Amazon EBS gp3 / gp2 general-purpose SSDs (with adjustable throughput) and Amazon S3 (object storage) for high-throughput, bulk, and sequential data access.
- **Managed services →** Any AWS managed offering (e.g., Amazon RDS, Amazon DynamoDB, Amazon SQS, Amazon ElastiCache) where AWS operates patching, replication, backups, and high availability on your behalf.

## SECTION 6 — PRACTICE QUESTIONS

1. A workload runs steadily at 20% CPU on an 8-vCPU VM. The cheapest optimization is to:
   - **A.** Add a second VM behind a load balancer
   - **B.** Right-size to a smaller VM (vertical scale down)
   - **C.** Convert to containers immediately
   - **D.** Switch to provisioned IOPS storage

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Vertical scaling down to a smaller VM removes wasted capacity without an architectural change.

**Why A is wrong:** Adding a second VM increases cost and is horizontal scaling, not cheapest.
**Why C is wrong:** Containers are a rewrite, not the cheapest first step for steady load.
**Why D is wrong:** Provisioned IOPS addresses storage, not CPU over-provisioning.

</details>

2. An unpredictable, event-driven task runs only a few times daily. The best compute choice is:
   - **A.** Always-on large VM
   - **B.** Reserved container cluster
   - **C.** Serverless function
   - **D.** Manual bare-metal

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Serverless scales to zero and bills only on execution, ideal for rare events.

**Why A is wrong:** An always-on VM bills 720 hours regardless of idle time.
**Why B is wrong:** A reserved cluster is expensive for few daily runs.
**Why D is wrong:** Bare metal is costly and not event-driven.

</details>

3. Which metric best indicates a database volume bottleneck from many small random writes?
   - **A.** Network throughput
   - **B.** IOPS
   - **C.** Latency of the CDN
   - **D.** Container count

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** IOPS measures small random read and write operations per second, the DB bottleneck signal.

**Why A is wrong:** Network throughput is bulk data rate, not small random writes.
**Why C is wrong:** CDN latency is content delivery, not a storage volume metric.
**Why D is wrong:** Container count is orchestration, not a storage metric.

</details>

4. A user in Kuala Lumpur sees 280 ms page loads from a distant region. Best fix:
   - **A.** Provisioned IOPS
   - **B.** Larger VM
   - **C.** Edge cache / CDN
   - **D.** More containers

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** A CDN or edge cache serves content from nearby edges, cutting network latency.

**Why A is wrong:** Provisioned IOPS helps storage, not geographic latency.
**Why B is wrong:** A larger VM does not move content closer to users.
**Why D is wrong:** More containers do not reduce round-trip distance.

</details>

5. Container orchestration primarily optimizes for:
   - **A.** Single-VM performance
   - **B.** Placement, scaling, and self-healing
   - **C.** Database IOPS
   - **D.** Network throughput

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Orchestration automates placement, scaling, and self-healing of containers.

**Why A is wrong:** It manages fleets, not single-VM performance.
**Why C is wrong:** Database IOPS is storage tuning, not orchestration.
**Why D is wrong:** Network throughput is separate from container orchestration.

</details>

6. A nightly 2 TB backup misses its window on a 1 Gbps link. Primary fix:
   - **A.** Increase network throughput / bandwidth
   - **B.** Reduce IOPS
   - **C.** Use serverless
   - **D.** Add a CDN

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Throughput or bandwidth limits bulk transfer speed, so increase the link rate.

**Why B is wrong:** Reducing IOPS is wrong; it is a throughput-bound bulk job.
**Why C is wrong:** Serverless does not speed a 2 TB backup transfer.
**Why D is wrong:** A CDN is for content delivery, not backups.

</details>

7. Which is a managed service benefit?
   - **A.** You patch the OS yourself
   - **B.** Provider handles HA, patching, backups
   - **C.** Full bare-metal control
   - **D.** No vendor involved

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Managed services offload operations such as HA, patching, and backups to the provider.

**Why A is wrong:** With managed services the provider patches, not you.
**Why C is wrong:** Managed services trade away bare-metal control.
**Why D is wrong:** A vendor, the CSP, is involved.

</details>

8. Vertical scaling means:
   - **A.** Adding more VMs
   - **B.** Changing the size of one VM
   - **C.** Using containers
   - **D.** Removing all capacity

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Vertical scaling changes the size of a single instance up or down.

**Why A is wrong:** Adding more VMs is horizontal scaling.
**Why C is wrong:** Containers are a packaging model, not vertical scaling.
**Why D is wrong:** Removing all capacity is not scaling, it is shutdown.

</details>

9. A workflow engine is best for:
   - **A.** Running one long VM
   - **B.** Sequencing multi-step tasks with retries
   - **C.** Caching static files
   - **D.** Measuring IOPS

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Workflow engines sequence multi-step tasks with branching, retries, and state tracking.

**Why A is wrong:** A long VM is not a workflow; it has no step chaining.
**Why C is wrong:** Caching static files is a CDN or cache, not a workflow.
**Why D is wrong:** Measuring IOPS is storage, not workflow.

</details>

10. For high, guaranteed IOPS on AWS, choose:
   - **A.** EBS gp3
   - **B.** EBS io2 Provisioned IOPS
   - **C.** S3
   - **D.** CloudFront

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EBS io2 Provisioned IOPS gives guaranteed, high, low-latency IOPS for databases.

**Why A is wrong:** gp3 has adjustable but not guaranteed peak IOPS like io2.
**Why C is wrong:** S3 is object storage for throughput, not guaranteed IOPS.
**Why D is wrong:** CloudFront is a CDN, not a block volume.

</details>

11. Serverless cold starts refer to:
   - **A.** A VM rebooting
   - **B.** Initial delay when a function initializes after idle
   - **C.** Network latency
   - **D.** Disk IOPS drop

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A cold start is the initial delay while a function initializes after idle.

**Why A is wrong:** A VM reboot is not a serverless cold start.
**Why C is wrong:** Network latency is separate from function init.
**Why D is wrong:** Disk IOPS is storage, not function init.

</details>

12. Horizontal scaling requires the app to be:
   - **A.** Stateful on one host
   - **B.** Stateless / share-nothing behind a load balancer
   - **C.** Single-threaded
   - **D.** Container-only

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Horizontal scaling adds instances, so state must be stateless or shared, not on one host.

**Why A is wrong:** Stateful single-host fights horizontal scaling.
**Why C is wrong:** Single-threaded is unrelated to scaling-out design.
**Why D is wrong:** Containers are not required for horizontal scaling.

</details>

13. Throughput-optimized storage suits:
   - **A.** Small random DB writes
   - **B.** Large sequential backups and media
   - **C.** Edge caching
   - **D.** Serverless code

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Bulk sequential transfers such as backups and media are throughput-bound, not IOPS-bound.

**Why A is wrong:** Small random writes are IOPS-bound, not throughput.
**Why C is wrong:** Edge caching is network latency, not storage type.
**Why D is wrong:** Serverless code is compute, not storage class.

</details>

14. AWS Fargate is best described as:
   - **A.** A virtual machine
   - **B.** Serverless compute for containers
   - **C.** A database
   - **D.** A CDN

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Fargate runs containers without you managing the underlying nodes, serverless compute.

**Why A is wrong:** Fargate is not a VM; it is container compute.
**Why C is wrong:** Fargate is not a database.
**Why D is wrong:** Fargate is not a CDN.

</details>

15. To reduce round-trip delay between regions, use:
   - **A.** EBS io2
   - **B.** Global Accelerator / CloudFront
   - **C.** Auto Scaling
   - **D.** S3

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Global Accelerator and CloudFront route over the optimized AWS backbone or edge.

**Why A is wrong:** EBS io2 is a storage volume, not inter-region routing.
**Why C is wrong:** Auto Scaling adjusts capacity, not round-trip delay.
**Why D is wrong:** S3 is storage, not a latency-reduction network service.

</details>

16. Bin-packing in orchestration:
   - **A.** Wastes nodes
   - **B.** Places containers densely to use fewer hosts
   - **C.** Increases IOPS
   - **D.** Reduces network latency

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Bin-packing places containers densely to use fewer hosts, lowering node count and cost.

**Why A is wrong:** Bin-packing reduces, not wastes, nodes.
**Why C is wrong:** Bin-packing affects placement, not IOPS.
**Why D is wrong:** It reduces latency indirectly but is about density.

</details>

17. The main trade-off of managed services is:
   - **A.** Higher IOPS only
   - **B.** Less low-level control / possible lock-in
   - **C.** No high availability
   - **D.** Manual patching

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** The trade-off of managed services is less low-level control and possible vendor lock-in.

**Why A is wrong:** Managed services also help IOPS indirectly via options.
**Why C is wrong:** Managed services provide HA, not remove it.
**Why D is wrong:** Managed services patch automatically, not manually.

</details>

18. Autoscaling helps optimize by:
   - **A.** Keeping fixed capacity always
   - **B.** Matching capacity to demand, saving cost at low load
   - **C.** Removing all VMs
   - **D.** Increasing latency

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Autoscaling matches capacity to demand, saving cost when load is low.

**Why A is wrong:** It changes capacity with demand, not fixed always-on.
**Why C is wrong:** It does not remove all VMs; it scales to demand.
**Why D is wrong:** It reduces, not increases, latency under load.

</details>

19. A spiky batch job best maps to:
   - **A.** Always-on VM
   - **B.** Serverless or autoscaled containers
   - **C.** Provisioned IOPS only
   - **D.** Single region static VM

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Elastic serverless or autoscaled containers handle bursts without idle waste.

**Why A is wrong:** An always-on VM sits idle and wastes money between bursts.
**Why C is wrong:** Provisioned IOPS is storage, not burst compute.
**Why D is wrong:** A single static VM cannot absorb spikes without waste.

</details>

20. Caching at the edge primarily improves:
   - **A.** IOPS
   - **B.** Network latency for repeated content
   - **C.** Storage throughput
   - **D.** Container density

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Edge caches serve repeated content close to users, cutting network latency.

**Why A is wrong:** IOPS is a storage metric, not edge caching.
**Why C is wrong:** Storage throughput is separate from edge caching.
**Why D is wrong:** Container density is orchestration, not edge caching.

</details>
