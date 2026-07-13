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

**Q1.** A cloud engineer needs to store daily application logs that are accessed less than once a month but must be retrievable within minutes. Which storage tier is the BEST fit?
A. Hot
B. Warm
C. Cold
D. Archive
**Answer:** C — Cold tier balances low cost with minute-level retrieval, fitting rare-but-fast access.

**Q2.** Which storage type presents raw volumes that an operating system mounts like a physical disk?
A. Object storage
B. Block storage
C. File storage
D. Archive storage
**Answer:** B — Block storage exposes raw volumes mounted and formatted by the OS.

**Q3.** A company must let 12 Linux app servers read and write the same shared folder concurrently. Which storage type should they choose?
A. Object storage
B. Block storage
C. File storage
D. Cold storage
**Answer:** C — File storage provides a shared hierarchical filesystem over NFS/SMB.

**Q4.** Which disk type delivers the lowest latency and highest IOPS for a transactional database?
A. HDD
B. SSD
C. Tape
D. Optical
**Answer:** B — SSDs use flash with no moving parts, giving sub-ms latency and high IOPS.

**Q5.** In AWS, which service provides object storage?
A. EBS
B. EFS
C. S3
D. FSx
**Answer:** C — Amazon S3 is AWS's object storage service.

**Q6.** Which AWS EBS volume type is a provisioned-IOPS SSD suited for mission-critical databases?
A. gp3
B. st1
C. io2
D. sc1
**Answer:** C — io2 is provisioned-IOPS SSD for the highest performance and durability.

**Q7.** A hospital must retain medical records for 10 years and rarely retrieves them. Which tier minimizes capacity cost?
A. Hot
B. Warm
C. Cold
D. Archive
**Answer:** D — Archive has the lowest per-GB cost for almost-never-accessed data.

**Q8.** What is the main performance drawback of HDD compared with SSD?
A. Lower capacity
B. Higher latency and lower IOPS
C. No durability
D. Higher cost per GB
**Answer:** B — HDDs have moving parts, causing higher latency and lower IOPS.

**Q9.** Which AWS S3 tier is designed for infrequently accessed data with a lower storage cost but a retrieval fee?
A. S3 Standard
B. S3 Standard-IA
C. S3 Glacier Deep Archive
D. S3 Intelligent-Tiering (base)
**Answer:** B — S3 Standard-IA is the Warm tier: cheaper capacity, per-GB retrieval fee.

**Q10.** Object storage is BEST described as:
A. A mounted filesystem with folders
B. Raw blocks addressed by LBA
C. Data + metadata + ID in a flat namespace accessed via API
D. A magnetic tape library
**Answer:** C — Objects are stored with metadata and a unique ID, retrieved via HTTP API.

**Q11.** Which cost driver applies to ALL S3 tiers when data leaves the cloud to the internet?
A. Retrieval fee
B. Early deletion penalty
C. Egress (data transfer out) fee
D. Capacity fee only
**Answer:** C — Egress/data-transfer-out fees apply regardless of storage tier.

**Q12.** A big-data job performs large sequential reads of rarely changed logs. Which disk is most cost-effective?
A. io2 SSD
B. gp3 SSD
C. st1 HDD
D. sc1 only
**Answer:** C — st1 throughput-optimized HDD is cheap and sequential-read friendly.

**Q13.** What is a key risk of placing frequently accessed data in an Archive tier?
A. It cannot be deleted
B. High retrieval fees and minimum-retention penalties erase savings
C. It loses durability
D. It becomes object storage automatically
**Answer:** B — Archive adds retrieval fees and minimum stay; frequent access makes it costly.

**Q14.** Which AWS offering provides managed Windows-file-share (SMB) file storage?
A. EFS
B. FSx
C. EBS
D. S3
**Answer:** B — Amazon FSx (e.g., FSx for Windows File Server) provides SMB file storage.

**Q15.** Hot storage is characterized by:
A. Hours-long retrieval and lowest cost
B. Frequent access, millisecond retrieval, highest capacity cost
C. Never accessed, compliance only
D. Sequential HDD backing only
**Answer:** B — Hot = frequent access, ms retrieval, highest per-GB cost, no retrieval fee.

**Q16.** A video-streaming service keeps its current catalog for instant playback. Which combination fits?
A. Object + Archive
B. Block + HDD
C. Object + Hot
D. File + Cold
**Answer:** C — Object storage with Hot tier gives scalable, instant web delivery.

**Q17.** Which performance metric measures completed read/write operations per second?
A. Throughput
B. Latency
C. IOPS
D. Durability
**Answer:** C — IOPS (input/output operations per second) counts operations per second.

**Q18.** Glacier Instant Retrieval is the AWS mapping for which Objective 1.4 tier?
A. Hot
B. Warm
C. Cold
D. Archive
**Answer:** C — Glacier Instant Retrieval is the Cold tier (fast retrieval, archive pricing).

**Q19.** Why might an architect choose gp3 over io2 for a general-purpose workload?
A. gp3 offers higher maximum IOPS than io2
B. gp3 is cheaper and decouples IOPS/throughput from size for most needs
C. io2 cannot attach to EC2
D. gp3 is HDD-based
**Answer:** B — gp3 is general-purpose SSD, cheaper, with independently scalable performance.

**Q20.** Which statement about File storage is TRUE?
A. It is only accessible by one server at a time
B. It exposes a shared hierarchical namespace over network protocols
C. It is the cheapest tier for backups
D. It uses HTTP APIs exclusively
**Answer:** B — File storage shares a folder tree over NFS/SMB to multiple clients.
