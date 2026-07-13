# Objective 1.4 — Compare and Contrast Storage Resources and Technologies

## SECTION 1 — Exam Objectives

**CompTIA Cloud+ CV0-004 · Domain 1.0 (Cloud Architecture) · Objective 1.4**

- Compare and contrast the storage resources and technologies available in cloud environments, and match each option to performance, durability, and budget requirements.
- **What the exam expects you to know:**
  - **Tiered storage** — Hot, Warm, Cold, and Archive tiers; access frequency drives both cost and retrieval latency.
  - **Disk types** — SSD vs HDD and the IOPS/latency/cost trade-offs between them.
  - **Storage types** — Object, Block, and File storage, and which workloads each one fits.
  - **Performance implications** — How throughput, IOPS, latency, and concurrency are affected by the storage choice.
  - **Cost implications** — How capacity, request, retrieval, and egress charges vary, and how tiering reduces spend.
- **Exam tip:** Objective 1.4 is heavily scenario-based. Given a business requirement, pick the correct storage type, tier, or disk. Memorize the trade-off tables and classic use-case pairings rather than just the definitions.

---

## SECTION 2 — EXAM KNOWLEDGE

### Tiered Storage (Hot / Warm / Cold / Archive)
- **Definition:** Data is organized into categories by access frequency — Hot (frequent, instant), Warm (infrequent, fast), Cold (rare, minutes delay), Archive (almost never, hours/days to restore).
- **Why it matters:** Not all data deserves expensive fast infrastructure; moving dormant data to cheaper slower tiers cuts spend while keeping hot data performant. Hotter tiers cost more per GB but have low retrieval fees; colder tiers cost less per GB but add retrieval and sometimes minimum-retention penalties.
- **Analogy:** Your wardrobe — everyday clothes (Hot) in the bedroom closet, seasonal (Warm) in the hallway cupboard, formal wear (Cold) in the attic, old documents (Archive) in a remote storage unit.
- **Example:** A video-streaming company keeps its current catalog in Hot, last year's titles in Warm, and decade-old footage in Archive for legal retention.

### Disk Types (SSD / HDD)
- **Definition:** SSD stores data on flash memory with no moving parts, giving very high IOPS and low latency; HDD stores data on spinning platters with a moving head, giving large cheap capacity but higher latency and lower IOPS.
- **Why it matters:** Disk type sets the performance ceiling of block and file storage. Latency-sensitive workloads (databases) need SSDs; throughput/capacity-hungry, cost-sensitive workloads (big data, backups) can use HDDs.
- **Analogy:** An SSD is a bookshelf where you instantly grab any book; an HDD is a library where a clerk walks to the shelf — slower, but far more storage per dollar.
- **Example:** A cloud database uses SSD-backed block volumes (gp3/io2); a batch analytics cluster uses HDD-backed volumes (st1) for large sequential log archives.

### Storage Types (Object / Block / File)
- **Definition:** Object storage manages data as objects (data + metadata + ID) in a flat namespace over HTTP APIs; Block storage presents raw volumes mounted to an OS like a physical disk; File storage exposes a shared hierarchical filesystem over NFS/SMB.
- **Why it matters:** Object scales infinitely for unstructured data and web delivery; Block gives low-latency rewritable volumes for OS and databases; File provides concurrent shared access for many servers and users.
- **Analogy:** Block is a blank notebook you write your own pages in; File is a shared filing cabinet with folders everyone opens; Object is a warehouse of numbered boxes requested by ID at a counter window.
- **Example:** A company stores images in object storage, runs its ERP database on block storage, and uses file storage so multiple app servers share one content library.

### Performance Implications
- **Definition:** How a storage choice affects throughput (MB/s), IOPS (ops/sec), and latency (response time), and how it scales under concurrent load.
- **Why it matters:** The wrong tier or disk can bottleneck the whole app. SSD and Hot deliver millisecond latency; Cold/Archive and HDD add delays. Shared file systems contend under concurrency, while object storage scales horizontally for parallel reads.
- **Analogy:** Choosing storage is like choosing a road — a city expressway (SSD/Hot) moves traffic fast; a rural back road (HDD/Cold) is fine for occasional trips but jams under load.
- **Example:** A high-frequency trading app on HDD sees queries jump from 2 ms to 200 ms, so the team migrates to SSD-backed block storage.

### Cost Implications
- **Definition:** How storage is billed — per-GB capacity, per-request, retrieval/early-deletion fees, and egress (transfer-out) charges, plus whether the tier penalizes frequent access.
- **Why it matters:** The cheapest per-GB tier is not always cheapest overall. Cold/Archive add retrieval and minimum-duration costs that hurt if accessed often; egress fees can dominate multi-region/hybrid setups; tiering and lifecycle policies align cost with real access.
- **Analogy:** Like a parking garage — street parking (Hot) is pricey per hour, the lot (Warm) moderate, the distant park-and-ride (Cold/Archive) cheap but with a shuttle fee and minimum stay each time you leave.
- **Example:** A team puts daily backups in Archive to save capacity, but monthly restore tests trigger retrieval fees that erase the savings — Cold via a lifecycle policy would have been cheaper.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **E-commerce product catalog (Object + Hot/Warm tiering):** An online retailer stores millions of product images and videos in object storage; new and trending items stay in Hot for instant loads, last season's catalog auto-transitions to Warm after 60 days via a lifecycle rule, keeping experience fast while cutting cost on dormant assets.
- **Mission-critical database (Block + SSD):** A finance SaaS runs PostgreSQL on SSD-backed block storage (provisioned IOPS); single-digit-millisecond latency and high IOPS are mandatory, and HDD would break SLAs.
- **Media archive and compliance (Object + Cold/Archive):** A hospital keeps 7 years of medical imaging — active cases Hot, closed cases older than 1 year move to Cold, and records past 5 years go to Archive; retrieval is rare and scheduled, so deep-discount capacity cost outweighs occasional retrieval fees.
- **Shared content library (File storage):** A video studio has multiple render servers reading/writing the same project files concurrently via a managed NFS/SMB file system, so all nodes see one consistent folder tree without syncing object blobs.
- **Big-data analytics on a budget (Block HDD + lifecycle):** An IoT platform ingests terabytes of sensor logs daily; recent logs on SSD block volumes for live dashboards, older logs copied to HDD-backed block and then to object Cold for historical ML training, balancing hot-query performance against cheap long-tail retention.

---

## SECTION 4 — COMPARISON TABLES

### Table 1 — Storage Type Comparison (Object vs Block vs File)

| Attribute | Object Storage | Block Storage | File Storage |
|---|---|---|---|
| Data model | Objects with metadata + ID | Fixed-size raw blocks/volumes | Hierarchical files/folders |
| Access method | HTTP/REST API | Mounted volume / iSCSI / NVMe | NFS / SMB network share |
| Best for | Unstructured data, backups, web assets | OS disks, databases | Shared concurrent access |
| Scalability | Near-infinite, flat namespace | Bounded per volume | Bounded, can be large |
| Mutability | Rewrite whole object | Random read/write | Random read/write |
| Latency | Higher (API overhead) | Lowest | Low–moderate |
| Example use | Images, videos, logs | DB, VM boot disk | Shared home dirs |

### Table 2 — Tiered Storage Comparison (Hot / Warm / Cold / Archive)

| Tier | Access frequency | Retrieval time | Cost (capacity) | Retrieval fee | Typical use |
|---|---|---|---|---|---|
| Hot | Frequent | Milliseconds | Highest | None/low | Active DB, live assets |
| Warm | Infrequent | Seconds | Medium | Low | Older records |
| Cold | Rare | Minutes | Low | Moderate | Backups, archives |
| Archive | Almost never | Hours/days | Lowest | High + min retention | Compliance, deep backup |

### Table 3 — Disk Type Comparison (SSD vs HDD)

| Attribute | SSD (Solid-State) | HDD (Hard Disk) |
|---|---|---|
| Mechanism | Flash memory, no moving parts | Spinning platters + head |
| Latency | Very low (sub-ms to few ms) | Higher (several ms+) |
| IOPS | High (thousands–hundreds of K) | Low (hundreds) |
| Throughput | High | Moderate, good for sequential |
| Cost per GB | Higher | Lower |
| Best for | Databases, OS, low-latency | Bulk, backups, cold data |

### Table 4 — Performance vs Cost Trade-off (Access Pattern → Choice)

| Workload pattern | Recommended type | Recommended tier/disk | Rationale |
|---|---|---|---|
| Low-latency DB writes | Block | SSD (Hot) | Needs IOPS + low latency |
| Infrequent large file reads | Object | Cold | Cheap capacity, tolerant delay |
| Many servers share files | File | SSD or HDD | Concurrent shared access |
| Long-term compliance hold | Object | Archive | Lowest capacity cost |
| High-throughput sequential logs | Block | HDD (st1) | Cheap, sequential friendly |

### Table 5 — Cost Driver Comparison

| Cost driver | Hot / SSD | Warm | Cold | Archive |
|---|---|---|---|---|
| Capacity $/GB | High | Medium | Low | Lowest |
| Retrieval fee | None | Low | Yes | Yes + min stay |
| Request fee | Yes | Yes | Yes | Yes |
| Egress fee | Always applies | Always applies | Always applies | Always applies |
| Early deletion penalty | No | Sometimes | Yes | Yes |

---

## SECTION 5 — AWS PROVIDER MAPPING

This section maps every Objective 1.4 concept to its AWS-specific service. Use these pairings directly when an AWS-flavored exam question appears.

| Objective 1.4 Concept | AWS Service / Offering |
|---|---|
| Object storage | **Amazon S3** (Simple Storage Service) |
| Block storage | **Amazon EBS** (Elastic Block Store) |
| File storage | **Amazon EFS** (Elastic File System) and **Amazon FSx** (FSx for Windows/Lustre/NetApp) |
| Hot tier | **S3 Standard** (11 nines durability, ms retrieval, no retrieval fee) |
| Warm tier | **S3 Standard-IA** (Infrequent Access; lower capacity cost, retrieval fee per GB) |
| Cold tier | **S3 Glacier Instant Retrieval** (ms–sec retrieval, archive-grade pricing) |
| Archive tier | **S3 Glacier Deep Archive** (lowest cost, hours retrieval, 90–180 day min) |
| SSD disk (general) | **EBS gp3** (general-purpose SSD, baseline 3,000 IOPS) |
| SSD disk (high perf) | **EBS io2** (provisioned IOPS SSD, up to 64,000 IOPS, mission-critical) |
| HDD disk (throughput) | **EBS st1** (throughput-optimized HDD, big sequential) |
| HDD disk (cold) | **EBS sc1** (cold HDD, lowest cost, infrequent) |

**Quick AWS reminders for the exam:**
- S3 is object storage; you access it over HTTP with a globally unique bucket name and a flat key namespace.
- EBS volumes are block storage attached to a single EC2 instance in one Availability Zone (except io2 Block Express multi-attach patterns).
- EFS is elastic, multi-AZ, shared POSIX file storage; FSx covers Windows (SMB) and high-performance (Lustre) file needs.
- Glacier Instant Retrieval fits "cold but needs fast access"; Glacier Deep Archive fits "rarely touched compliance."
- gp3 lets you scale IOPS and throughput independently of size; io2 is for the highest durability/IOPS needs.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which storage class is best for frequently accessed, low-latency object data with 11 nines durability?
   - **A.** S3 Glacier Deep Archive
   - **B.** S3 Standard
   - **C.** S3 Glacier Flexible Retrieval
   - **D.** EBS Cold HDD

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** S3 Standard is the general-purpose, low-latency, durable object store for hot data.

**Why A is wrong:** Glacier Deep Archive is for rare cold access.
**Why C is wrong:** Glacier Flexible is archival, not low-latency.
**Why D is wrong:** EBS Cold HDD is block storage, not object.

</details>

2. You need block storage attached to a single EC2 instance for a database with sustained high IOPS. Best choice?
   - **A.** S3 Standard
   - **B.** EBS gp3
   - **C.** EBS io2 (provisioned IOPS)
   - **D.** EFS Standard

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** io2 delivers provisioned IOPS for demanding, latency-sensitive databases.

**Why A is wrong:** S3 is object, not block, and not attachable.
**Why B is wrong:** gp3 is general-purpose, lower ceiling than io2.
**Why D is wrong:** EFS is file storage shared across instances.

</details>

3. A shared file system accessed concurrently by hundreds of Linux instances using NFS is needed. Which service?
   - **A.** EBS volume
   - **B.** S3 bucket
   - **C.** EFS (Elastic File System)
   - **D.** Instance store

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** EFS is a managed NFS file system that many instances mount concurrently.

**Why A is wrong:** EBS attaches to one instance at a time.
**Why B is wrong:** S3 is object, not a mountable file system.
**Why D is wrong:** Instance store is ephemeral local disk.

</details>

4. Archive data must be kept for 7+ years at the lowest cost, retrieval within 12 hours is acceptable. Choose:
   - **A.** S3 Standard
   - **B.** S3 Glacier Deep Archive
   - **C.** EBS gp3
   - **D.** EFS

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Glacier Deep Archive is the lowest-cost class for long-term retention with hours-long retrieval.

**Why A is wrong:** Standard is costly for cold data.
**Why C is wrong:** EBS is block and pricier per GB.
**Why D is wrong:** EFS is for active shared files.

</details>

5. S3 Standard-IA is appropriate when:
   - **A.** Data is accessed multiple times per second
   - **B.** Data is infrequently accessed but needs rapid retrieval when needed
   - **C.** Data must never be retrieved
   - **D.** Data is only accessed via Glacier

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Standard-IA lowers storage cost for infrequently accessed data while keeping millisecond retrieval.

**Why A is wrong:** That is Standard, not IA.
**Why C is wrong:** Archive classes fit never-accessed better.
**Why D is wrong:** IA is not behind Glacier.

</details>

6. What does S3's '11 nines' (99.999999999%) durability refer to?
   - **A.** Uptime of the S3 service
   - **B.** Probability of NOT losing an object over a year
   - **C.** Encryption strength
   - **D.** Maximum file size

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Durability is the design probability that stored objects are not lost.

**Why A is wrong:** That is availability, not durability.
**Why C is wrong:** Encryption is separate.
**Why D is wrong:** File size is unrelated.

</details>

7. An EBS volume's data persists after the attached EC2 instance is stopped. TRUE because:
   - **A.** EBS is network-attached block storage independent of instance lifecycle
   - **B.** Instance store persists
   - **C.** S3 auto-copies it
   - **D.** It is always encrypted

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** EBS is durable network block storage that survives stop/start and even termination if not set to delete.

**Why B is wrong:** Instance store is ephemeral, not EBS.
**Why C is wrong:** S3 is separate.
**Why D is wrong:** Encryption is optional, not the reason for persistence.

</details>

8. You want to auto-tier objects from Standard to IA after 30 days of no access. Use:
   - **A.** Lifecycle policy
   - **B.** Bucket policy
   - **C.** CORS configuration
   - **D.** Replication rule

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** S3 Lifecycle rules transition objects between storage classes based on age/access.

**Why B is wrong:** Bucket policy controls access, not tiering.
**Why C is wrong:** CORS controls cross-origin web access.
**Why D is wrong:** Replication copies data to another bucket.

</details>

9. A workload needs temporary, very high-IOPS block storage that is lost when the instance stops. Which?
   - **A.** EBS io2
   - **B.** Instance store (ephemeral)
   - **C.** EFS
   - **D.** S3

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Instance store is physically attached, very fast, and ephemeral — data is lost on stop.

**Why A is wrong:** EBS persists.
**Why C is wrong:** EFS persists and is shared.
**Why D is wrong:** S3 persists.

</details>

10. EBS st1 (Throughput Optimized HDD) is best for:
   - **A.** Boot volumes
   - **B.** Big data / throughput-heavy sequential workloads
   - **C.** Small random IOPS databases
   - **D.** Cold archive

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** st1 is low-cost HDD optimized for large, sequential throughput (e.g., big data, logs).

**Why A is wrong:** Boot volumes need SSD (gp3).
**Why C is wrong:** Random IOPS want SSD.
**Why D is wrong:** Archive is Glacier.

</details>

11. Which storage is object-based and accessed via a REST/HTTPS API (not mounted as a file system)?
   - **A.** EFS
   - **B.** EBS
   - **C.** S3
   - **D.** Instance store

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** S3 is object storage reached over HTTP API; you do not mount it like a disk.

**Why A is wrong:** EFS is a mountable file system.
**Why B is wrong:** EBS is a block device.
**Why D is wrong:** Instance store is local block.

</details>

12. A bucket policy is used to:
   - **A.** Define storage class transitions
   - **B.** Control who can access the bucket/objects and what actions
   - **C.** Encrypt objects at rest
   - **D.** Set retrieval time

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Bucket policies are resource-based IAM-like JSON that grant/deny access to the bucket.

**Why A is wrong:** That is lifecycle.
**Why C is wrong:** Encryption is SSE config.
**Why D is wrong:** Retrieval time is a class property.

</details>

13. You must ensure objects are encrypted at rest in S3. The SIMPLEST always-on option is:
   - **A.** SSE-S3 (AES-256 managed by S3)
   - **B.** Client-side only
   - **C.** No encryption
   - **D.** TLS only

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** SSE-S3 transparently encrypts every object with a key managed by S3.

**Why B is wrong:** Client-side is possible but more work.
**Why C is wrong:** No encryption fails compliance.
**Why D is wrong:** TLS is in-transit, not at rest.

</details>

14. Multi-part upload in S3 is recommended for files larger than:
   - **A.** 5 MB
   - **B.** 100 MB
   - **C.** 5 GB
   - **D.** 1 TB

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AWS recommends multipart upload for objects over 100 MB to improve throughput and recovery.

**Why A is wrong:** Too small a threshold.
**Why C is wrong:** 5 GB is the single-part max, not the recommendation threshold.
**Why D is wrong:** Far above.

</details>

15. A company needs cross-region copies of a bucket for disaster recovery. Implement with:
   - **A.** S3 Cross-Region Replication (CRR)
   - **B.** Lifecycle expiry
   - **C.** Bucket policy
   - **D.** CORS

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** CRR asynchronously copies objects to a bucket in another region for DR.

**Why B is wrong:** Lifecycle expires, does not replicate.
**Why C is wrong:** Policy controls access.
**Why D is wrong:** CORS is web cross-origin.

</details>

16. EFS is described as 'elastic' because:
   - **A.** It is block storage
   - **B.** Capacity grows/shrinks automatically as files are added/removed
   - **C.** It is single-instance only
   - **D.** It requires fixed provisioning

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EFS scales storage up and down automatically with usage, no pre-provisioning.

**Why A is wrong:** It is file, not block.
**Why C is wrong:** It is multi-attach.
**Why D is wrong:** No fixed provisioning is the point.

</details>

17. EBS sc1 (Cold HDD) is the right choice for:
   - **A.** Production databases
   - **B.** Cold, infrequent-access, throughput workloads at lowest HDD cost
   - **C.** Boot volumes
   - **D.** High IOPS transactions

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** sc1 is the cheapest HDD for cold, sequentially-accessed data.

**Why A is wrong:** Databases want SSD.
**Why C is wrong:** Boot wants gp3.
**Why D is wrong:** High IOPS wants io2.

</details>

18. Object lock in S3 provides:
   - **A.** Faster retrieval
   - **B.** WORM (write once, read many) immutability to prevent deletion
   - **C.** Lifecycle deletion
   - **D.** Cross-region copy

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Object Lock enforces WORM so objects cannot be deleted/modified for a retention period.

**Why A is wrong:** It does not speed retrieval.
**Why C is wrong:** It prevents deletion, opposite of lifecycle expiry.
**Why D is wrong:** Replication is separate.

</details>

19. A database needs block storage that can be attached to one instance but snapshot-backed for recovery. Best is:
   - **A.** S3
   - **B.** EBS with snapshots
   - **C.** EFS
   - **D.** Instance store

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EBS gives block storage; EBS snapshots to S3 provide point-in-time recovery.

**Why A is wrong:** S3 is object, not a DB volume.
**Why C is wrong:** EFS is shared file, not a single-instance DB volume.
**Why D is wrong:** Instance store has no snapshots.

</details>

20. A single file system that must be available across multiple AZs automatically with no single-AZ failure is:
   - **A.** EBS volume
   - **B.** EFS with Multi-AZ
   - **C.** S3 bucket
   - **D.** Instance store

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EFS is a regional, Multi-AZ file system so it survives an AZ failure; EBS is single-AZ.

**Why A is wrong:** EBS lives in one AZ.
**Why C is wrong:** S3 is object, not a mountable shared FS with AZ semantics.
**Why D is wrong:** Instance store is single-host ephemeral.

</details>
