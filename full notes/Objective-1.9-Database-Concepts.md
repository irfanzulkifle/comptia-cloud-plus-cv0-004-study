# Objective 1.9 — Explain the importance of database concepts

## SECTION 1 — Exam Objectives

**Official CompTIA Cloud+ CV0-004 Objective 1.9:** *Explain the importance of database concepts.*

- This objective has you classify a database in two independent ways. You must recognise, describe, and compare each category.
- **Two classification axes (4 sub-objectives):**
  - **Database Types**
    - **Relational** — data in tables of rows and columns, linked by relationships, enforced by a schema.
    - **Non-relational** — data without fixed tables, as documents, key-value pairs, graphs, or wide-column stores ("NoSQL").
  - **Deployment Options**
    - **Self-managed** — the customer installs, configures, patches, backs up, and operates the database on infrastructure they control (e.g. a VM).
    - **Provider-managed** — the CSP runs the database as a managed service: patching, backups, replication, and failover are handled for you.
- **Why it matters:** Cloud+ is vendor-neutral. You will not write SQL, but you will choose the right type or deployment model for a scenario and explain trade-offs (control vs. convenience, cost, scalability). Expect questions like "Which model reduces patching effort?" or "Which type fits a flexible, schema-less catalog?"
- **Beginner tip:** Memorise the 4 sub-objectives as a 2×2 grid. Tables + SQL → Relational. JSON/flexible schema/scale → Non-relational. You install and patch → Self-managed. Provider handles backups/upgrades → Provider-managed.

---

## SECTION 2 — EXAM KNOWLEDGE

- For each sub-objective we give a **Definition**, **Why it matters**, an **Analogy**, and an **Example**.

### 2.1 Relational Databases (Type)

- **Definition:** A relational database organises data into tables (relations) of rows (records) and columns (fields). A fixed schema is defined before data is added. Tables link through primary and foreign keys, enforcing relationships and integrity. Data is manipulated with SQL. ACID properties (Atomicity, Consistency, Isolation, Durability) protect transactions. Examples: MySQL, PostgreSQL, SQL Server, Oracle, MariaDB.
- **Why it matters:** Relational is the default for structured data where accuracy is critical — banking, inventory, payroll, orders. Recognise its traits: tables, rows/columns, schema, SQL, keys, joins, ACID. When a scenario stresses consistency and structured reporting, choose relational.
- **Analogy:** Like a filing cabinet of pre-printed forms. Every form (table) has fixed boxes (columns). A "Customers" form and an "Orders" form cross-reference by customer number (the key), so you never lose who ordered what. The fixed layout keeps everyone's data predictable and reliable.
- **Example:** An online bookstore uses a relational DB. A `Customers` table holds IDs and addresses; `Orders` holds order IDs and customer IDs (foreign key). Joining on customer ID finds every order for a customer. A sale deducts stock and records payment in one ACID transaction, so money and inventory never disagree.

### 2.2 Non-relational Databases (Type)

- **Definition:** A non-relational database (NoSQL) stores data without the rigid table/schema model. Common models: key-value (Redis), document (MongoDB), wide-column (Cassandra), graph (Neo4j). Data is usually schema-less or flexible, so records may hold different fields. It scales out horizontally across servers and often favours availability and partition tolerance (BASE — Basically Available, Soft state, Eventual consistency) over strict ACID.
- **Why it matters:** Non-relational suits big, fast, or unpredictable data: social feeds, IoT streams, varied product catalogs, recommendations. Spot the signals: "flexible schema," "JSON documents," "huge scale," "horizontal scaling." Choose it when structure changes often or single-server scaling hits a wall.
- **Analogy:** Like a shoebox of sticky notes. No fixed form — each note holds whatever you scribble. One note lists a name and favourite colour; another lists a product, price, and photos. You add new note types without redesigning the box, and you spread boxes across a warehouse (scale out) instead of building one giant cabinet.
- **Example:** A mobile game stores profiles in a document DB as JSON. Player A has level/coins/friends; Player B also has an optional "achievements" array. No schema change needed. At launch, the DB adds servers to handle millions of writes per minute — beyond a single relational server.

### 2.3 Self-managed Databases (Deployment)

- **Definition:** In self-managed (self-hosted/unmanaged) deployment, the customer owns the full DB lifecycle on cloud infrastructure they provision: installing the engine, configuring it, applying patches/upgrades, managing backups/restores, tuning performance, handling replication and failover. The CSP supplies compute, storage, network; the customer supplies all database administration. A typical setup is a database on a VM/instance.
- **Why it matters:** Self-managed gives maximum control and can lower fees, but places operational burden and misconfiguration risk on the customer. The exam contrasts it with provider-managed: "your team installs and patches the DB" = self-managed. Chosen when you need OS-level access, custom config, or engine versions the provider does not offer as a service.
- **Analogy:** Like renting an empty kitchen to run your restaurant. The landlord (CSP) gives the building, electricity, water (compute/storage/network), but you buy the stove, write the menu, cook, clean, and pass inspections. You control everything, but a broken fridge is your problem.
- **Example:** A company runs PostgreSQL on a cloud VM. Their admin installs it, sets memory limits, schedules pg_dump backups, applies patches, and manually promotes a standby during outage. They save fees and can tweak the kernel, but a missed patch exposed a vulnerability — a risk fully on their team.

### 2.4 Provider-managed Databases (Deployment)

- **Definition:** In a provider-managed (managed service) deployment, the CSP operates the database: provisioning, automated backups, patching/upgrades, monitoring, replication, often automatic failover and scaling. The customer defines the database (engine, size, settings) and connects apps, but routine operations are abstracted. You trade some control for less operational toil.
- **Why it matters:** Provider-managed reduces the staff/expertise needed to run a DB safely, lowering patching/backup errors. Flagged by "provider handles backups and upgrades," "less administrative overhead," "automatic failover." Default when speed-to-market and reliability beat deep low-level control.
- **Analogy:** Like meal-kit delivery with a chef service. You choose what to eat (DB and size), but someone else shops, preps, cooks, cleans. You enjoy the meal with little effort; the trade-off is you cannot rearrange the kitchen.
- **Example:** A startup uses a managed relational service. They pick engine and size; the provider auto-backs up nightly, patches in a maintenance window, replicates to a standby zone, and fails over automatically. Two engineers focus on the app, not DB admin — at a higher per-hour fee and fewer OS tweaks.
- **Cross-cutting note:** Type and deployment are independent. You can have relational+self-managed (PostgreSQL on your VM), relational+provider-managed (managed relational service), non-relational+self-managed (MongoDB on your VM), or non-relational+provider-managed (managed NoSQL service). Practise all four combinations.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Bank core ledger (Relational + Provider-managed):** A Malaysian retail bank runs its ledger on a managed relational service. Regulators require ACID consistency and audit trails, so relational is mandatory. The provider handles backups, encryption, patching, and multi-zone failover, letting a small DBA team meet compliance. Lesson: relational for integrity + provider-managed for compliance.
- **Scenario 2 — E-commerce catalog (Non-relational + Provider-managed):** A growing shop sells millions of items with differing attributes (clothing: size/colour; electronics: specs). A document DB with flexible schema fits; a managed NoSQL service auto-scales during 11.11 sales. The provider replicates for availability under load. Lesson: non-relational for flexible data + provider-managed for elastic scale.
- **Scenario 3 — IoT factory sensors (Non-relational + Self-managed):** A plant streams high-volume sensor data. The team deploys a wide-column NoSQL DB on self-managed VMs inside their own network for data-sovereignty and OS tuning of write throughput. They accept patching/backup burden for control and locality. Lesson: self-managed for isolation and custom tuning.
- **Scenario 4 — Internal payroll (Relational + Self-managed):** A company runs payroll on a self-managed relational DB on a private VM. They need a specific version and full OS control and employ DBAs. They handle their own backups and patching. Lesson: relational for structured finance + self-managed for version/OS control.
- **Scenario 5 — Game leaderboard (Non-relational + Provider-managed):** A studio needs a fast key-value store for real-time leaderboards. They use a managed in-memory NoSQL service that auto-scales with players and provides replication. The studio ships features instead of managing servers. Lesson: non-relational (key-value) for speed + provider-managed for zero ops.

---

## SECTION 4 — COMPARISON TABLES

**Table 1 — Relational vs. Non-relational (Types)**

| Aspect | Relational | Non-relational (NoSQL) |
| --- | --- | --- |
| Data model | Tables, rows, columns | Documents, key-value, graph, wide-column |
| Schema | Fixed / predefined | Flexible / schema-less |
| Query language | SQL | Varies (document queries, APIs) |
| Relationships | Keys + joins | Often embedded / denormalised |
| Consistency | Strong (ACID) | Eventual / BASE (often) |
| Scaling | Scale up (bigger server) | Scale out (more servers) |
| Best for | Structured, transactional data | Flexible, high-volume, fast-changing data |
| Examples | MySQL, PostgreSQL, Oracle | MongoDB, Redis, Cassandra, Neo4j |

**Table 2 — Self-managed vs. Provider-managed (Deployment)**

| Aspect | Self-managed | Provider-managed |
| --- | --- | --- |
| Who installs engine | Customer | Provider |
| Patching/upgrades | Customer | Provider (automatic) |
| Backups | Customer responsibility | Provider (automated) |
| Failover/replication | Customer configures | Provider (often automatic) |
| Control level | High (OS + DB) | Lower (abstracted) |
| Operational effort | High | Low |
| Cost model | Infra only | Infra + service fee |
| Best for | Custom needs, full control | Speed, reliability, small teams |

**Table 3 — Type × Deployment combinations**

| Combination | Example | When to choose |
| --- | --- | --- |
| Relational + Self-managed | PostgreSQL on a VM | Specific version/OS control; have DBAs |
| Relational + Provider-managed | Managed relational service | ACID + low ops burden |
| Non-relational + Self-managed | MongoDB on a VM | Isolation/custom tuning |
| Non-relational + Provider-managed | Managed NoSQL service | Elastic scale + zero ops |

**Table 4 — Scaling approaches**

| Approach | Description | Typical fit |
| --- | --- | --- |
| Vertical (scale up) | Bigger server (CPU/RAM) | Relational, simpler loads |
| Horizontal (scale out) | Add more servers/nodes | Non-relational, big data |
| Read replicas | Copy for read traffic | Both, offload reads |
| Sharding | Split data across nodes | Large datasets |

**Table 5 — Consistency models**

| Model | Meaning | Typical use |
| --- | --- | --- |
| ACID | Atomic, Consistent, Isolated, Durable | Relational, financial |
| BASE | Basically Available, Soft state, Eventual consistency | Non-relational, web-scale |

**Table 6 — Decision quick-reference**

| Scenario clue | Likely answer |
| --- | --- |
| "Tables, SQL, reports, accuracy" | Relational |
| "JSON, flexible fields, huge scale" | Non-relational |
| "We install and patch it" | Self-managed |
| "Provider handles backups/upgrades" | Provider-managed |

---

## SECTION 5 — AWS PROVIDER MAPPING

Maps Objective 1.9's four sub-objectives to **AWS-only** services.

**Type: Relational → AWS**
- **Amazon RDS (Relational Database Service)** — managed relational service supporting multiple engines:
  - **Amazon Aurora** — AWS cloud-native, MySQL/PostgreSQL-compatible relational engine for performance and availability.
  - **MySQL** — open-source relational engine on RDS.
  - **PostgreSQL** — open-source relational engine on RDS.

**Type: Non-relational → AWS**
- **Amazon DynamoDB** — managed key-value and document NoSQL, serverless and auto-scaling.
- **Amazon DocumentDB** — managed document DB (MongoDB-compatible) for JSON.
- **Amazon Neptune** — managed graph DB for connected data (friend networks, fraud).

**Deployment: Self-managed → AWS**
- **Database software on Amazon EC2** (e.g. PostgreSQL, MySQL, MongoDB on an EC2-hosted VM). Customer manages engine, OS patches, backups, replication; AWS supplies compute/storage/network.

**Deployment: Provider-managed → AWS**
- **Amazon RDS** (Aurora, MySQL, PostgreSQL) — provider manages patching, backups, replication, failover.
- **Amazon DynamoDB** — fully provider-managed NoSQL; AWS handles operations, scaling, availability.

**Summary table (AWS only):**

| Sub-objective | AWS service(s) |
| --- | --- |
| Relational | Amazon RDS — Aurora, MySQL, PostgreSQL |
| Non-relational | Amazon DynamoDB, Amazon DocumentDB, Amazon Neptune |
| Self-managed | Database engine on Amazon EC2 (customer-operated) |
| Provider-managed | Amazon RDS / Amazon DynamoDB (AWS-operated) |

**Exam caution:** Cloud+ is vendor-neutral; the exam uses generic terms, not AWS names. Learn the mapping so AWS-named distractors can still be classified by their underlying sub-objective.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A schema-on-write, ACID, table-based store with SQL is:
   - **A.** Key-value store
   - **B.** Relational (RDBMS)
   - **C.** Object store
   - **D.** Graph store

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Relational DBs enforce a schema and ACID transactions via SQL.

**Why A is wrong:** Key-value is schemaless.
**Why C is wrong:** Object store is S3.
**Why D is wrong:** Graph models relationships, not classic tables.

</details>

2. A flexible-schema, single-digit-millisecond, serverless NoSQL table keyed by partition+sort is:
   - **A.** RDS MySQL
   - **B.** DynamoDB
   - **C.** Aurora
   - **D.** Redshift

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** DynamoDB is a managed NoSQL key-value/document store with single-digit-ms latency.

**Why A is wrong:** RDS MySQL is relational.
**Why C is wrong:** Aurora is relational.
**Why D is wrong:** Redshift is warehouse.

</details>

3. A managed MySQL/PostgreSQL-compatible relational engine with up to 15 read replicas and Multi-AZ is:
   - **A.** DynamoDB
   - **B.** Aurora
   - **C.** Neptune
   - **D.** DocumentDB

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Aurora is a high-performance managed relational engine (MySQL/PostgreSQL compatible).

**Why A is wrong:** DynamoDB is NoSQL.
**Why C is wrong:** Neptune is graph.
**Why D is wrong:** DocumentDB is MongoDB-compatible.

</details>

4. A graph database for highly connected data (social, fraud) is:
   - **A.** DynamoDB
   - **B.** Neptune
   - **C.** RDS
   - **D.** Redshift

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Neptune stores nodes/edges for relationship-heavy queries.

**Why A is wrong:** DynamoDB is key-value/doc.
**Why C is wrong:** RDS is relational tables.
**Why D is wrong:** Redshift is warehouse.

</details>

5. A MongoDB-compatible managed document database is:
   - **A.** DocumentDB
   - **B.** DynamoDB
   - **C.** Aurora
   - **D.** ElastiCache

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** DocumentDB is a managed MongoDB-compatible document store.

**Why B is wrong:** DynamoDB is its own API.
**Why C is wrong:** Aurora is relational.
**Why D is wrong:** ElastiCache is in-memory cache.

</details>

6. A columnar, petabyte-scale data warehouse for analytics (OLAP) is:
   - **A.** RDS
   - **B.** Redshift
   - **C.** DynamoDB
   - **D.** Neptune

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Redshift is a column-oriented warehouse built for large analytical queries.

**Why A is wrong:** RDS is OLTP.
**Why C is wrong:** DynamoDB is OLTP NoSQL.
**Why D is wrong:** Neptune is graph.

</details>

7. In-memory cache (Redis/Memcached) is used to:
   - **A.** Replace the primary database permanently
   - **B.** Reduce latency and offload read load from the DB
   - **C.** Store cold archives
   - **D.** Encrypt data

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Caches sit in front of the DB to serve hot reads fast and reduce backend load.

**Why A is wrong:** Cache is not the system of record.
**Why C is wrong:** Archives go to Glacier.
**Why D is wrong:** Caches may encrypt but that is not the purpose.

</details>

8. ACID stands for Atomicity, Consistency, Isolation, Durability — these guarantee:
   - **A.** Maximum scale only
   - **B.** Reliable, all-or-nothing transactions
   - **C.** No schema
   - **D.** Eventual consistency

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** ACID ensures transactions complete fully or roll back, with consistent isolated durable state.

**Why A is wrong:** Scale is a separate concern.
**Why C is wrong:** Relational DBs are schemaful.
**Why D is wrong:** ACID is strong consistency, not eventual.

</details>

9. Eventual consistency (vs strong) means:
   - **A.** Writes never succeed
   - **B.** Reads may briefly return stale data until replicas converge
   - **C.** No durability
   - **D.** Schema is required

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Eventual-consistent stores may serve stale reads for a short window before all replicas sync.

**Why A is wrong:** Writes do succeed.
**Why C is wrong:** Durability is separate.
**Why D is wrong:** Often schemaless.

</details>

10. A NoSQL document store is a good fit when:
   - **A.** You need complex multi-row ACID joins
   - **B.** Data shape varies and you need horizontal scale + flexible schema
   - **C.** You need strict SQL reports
   - **D.** You need a warehouse

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Document stores scale out and accept varying structures without migrations.

**Why A is wrong:** Joins are weak in NoSQL.
**Why C is wrong:** SQL reporting favors relational.
**Why D is wrong:** Warehouse is Redshift.

</details>

11. Sharding a database is done to:
   - **A.** Combine all data on one node
   - **B.** Partition data across nodes to scale writes/store horizontally
   - **C.** Delete data
   - **D.** Encrypt it

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Sharding splits data by key across nodes, spreading load beyond one machine's limit.

**Why A is wrong:** Opposite of sharding.
**Why C is wrong:** Not deletion.
**Why D is wrong:** Not encryption.

</details>

12. A read replica primarily provides:
   - **A.** Write scaling
   - **B.** Read scaling and a standby for failover
   - **C.** Schema changes
   - **D.** Encryption keys

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Replicas serve read traffic and can be promoted on failure.

**Why A is wrong:** Replicas do not scale writes.
**Why C is wrong:** Schema changes go to primary.
**Why D is wrong:** KMS manages keys.

</details>

13. OLTP vs OLAP: OLTP is for:
   - **A.** Long analytical scans
   - **B.** Short transactional app queries (insert/update/select)
   - **C.** Data warehousing
   - **D.** Batch reporting

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** OLTP handles day-to-day transactional workloads; OLAP handles analytics.

**Why A is wrong:** That is OLAP.
**Why C is wrong:** Warehouse is OLAP.
**Why D is wrong:** Reporting is OLAP.

</details>

14. You need to migrate an on-prem MySQL to AWS with minimal change to app SQL. Best:
   - **A.** DynamoDB
   - **B.** RDS for MySQL / Aurora MySQL
   - **C.** Redshift
   - **D.** Neptune

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** RDS/Aurora MySQL are drop-in managed MySQL, so app SQL barely changes.

**Why A is wrong:** DynamoDB needs a data model rewrite.
**Why C is wrong:** Redshift is warehouse, not OLTP.
**Why D is wrong:** Neptune is graph.

</details>

15. A managed relational DB where AWS handles patching, backups, and Multi-AZ failover is:
   - **A.** MySQL on a self-patched EC2
   - **B.** RDS
   - **C.** DynamoDB
   - **D.** S3

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** RDS offloads maintenance, backups, and failover — the managed relational service.

**Why A is wrong:** Self-patched EC2 is not managed.
**Why C is wrong:** DynamoDB is NoSQL.
**Why D is wrong:** S3 is object storage.

</details>

16. Time-series or wide-column stores (e.g., Cassandra-style) suit:
   - **A.** Bank transactions
   - **B.** High-volume timestamped/IoT writes with simple key access
   - **C.** Ad-hoc SQL joins
   - **D.** Graph traversal

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Wide-column/time-series stores ingest massive timestamped streams efficiently.

**Why A is wrong:** Transactions favor relational.
**Why C is wrong:** Ad-hoc joins favor SQL.
**Why D is wrong:** Graph favors Neptune.

</details>

17. Choosing SQL vs NoSQL should be based on:
   - **A.** Which is newer
   - **B.** Query patterns, consistency needs, and scale model
   - **C.** Random choice
   - **D.** Only cost

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Fit the model to access patterns, consistency requirements, and scaling needs.

**Why A is wrong:** Novelty is not a criterion.
**Why C is wrong:** Not random.
**Why D is wrong:** Cost is one factor, not the only one.

</details>

18. A database connection pool is used to:
   - **A.** Avoid the overhead of opening a new connection per query
   - **B.** Encrypt the disk
   - **C.** Replace indexes
   - **D.** Shard automatically

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Pools reuse connections, cutting latency and resource churn from frequent connect/disconnect.

**Why B is wrong:** Encryption is separate.
**Why C is wrong:** Indexes are still needed.
**Why D is wrong:** Sharding is manual/architectural.

</details>

19. The PRIMARY key in a relational table:
   - **A.** Can be duplicated
   - **B.** Uniquely identifies each row and cannot be null
   - **C.** Is optional
   - **D.** Stores the schema

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A primary key uniquely and non-nullly identifies a row.

**Why A is wrong:** Must be unique.
**Why C is wrong:** It is required for identity.
**Why D is wrong:** It is data, not schema metadata.

</details>

20. Aurora Global Database is primarily used for:
   - **A.** Single-AZ testing
   - **B.** Cross-Region disaster recovery and low-latency global reads
   - **C.** Graph queries
   - **D.** Data warehousing

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Aurora Global Database replicates to other regions for fast DR failover and local reads.

**Why A is wrong:** It is a multi-region feature, not single-AZ.
**Why C is wrong:** Graph is Neptune.
**Why D is wrong:** Warehouse is Redshift.

</details>
