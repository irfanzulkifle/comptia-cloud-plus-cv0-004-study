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

**Question 1.** A database stores data in tables with rows and columns, queried with SQL. Which type?
A. Non-relational
B. Relational
C. Self-managed
D. Provider-managed

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Tables, rows, columns, and SQL define a relational database.
> **Why A is wrong:** Non-relational uses documents, key-value, or graph, not fixed tables.
> **Why C is wrong:** Self-managed is a deployment model, not a data type.
> **Why D is wrong:** Provider-managed is a deployment model, not a data type.

**Question 2.** Player profiles are stored as JSON documents with different fields per profile. Best type?
A. Relational
B. Graph
C. Non-relational
D. Provider-managed

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Flexible JSON documents with varying fields are non-relational, document model.
> **Why A is wrong:** Relational needs a fixed schema; varying fields break it.
> **Why B is wrong:** Graph models relationships, not arbitrary per-record fields.
> **Why D is wrong:** Provider-managed is deployment, not data type.

**Question 3.** Your team installs the engine on a VM, patches, and backs up manually. This is:
A. Provider-managed
B. Relational
C. Non-relational
D. Self-managed

> [!note]- Reveal Answer
> **Correct: D**
> **Why correct:** The customer installs, patches, and backs up the DB, which is self-managed.
> **Why A is wrong:** Provider-managed means the CSP does patching and backups.
> **Why B is wrong:** Relational is a data type, not a deployment model.
> **Why C is wrong:** Non-relational is a data type, not a deployment model.

**Question 4.** A startup wants the provider to handle patching, backups, and failover. Which model?
A. Self-managed
B. Provider-managed
C. Relational
D. Non-relational

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Automatic patching, backups, and failover by the provider is provider-managed.
> **Why A is wrong:** Self-managed puts patching and backups on the customer.
> **Why C is wrong:** Relational is a data type, not deployment.
> **Why D is wrong:** Non-relational is a data type, not deployment.

**Question 5.** Strong consistency and ACID financial transactions best suit which type?
A. Non-relational
B. Relational
C. Key-value
D. Wide-column

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Relational databases provide ACID transactions for financial integrity.
> **Why A is wrong:** Non-relational often favors eventual consistency, not ACID finance.
> **Why C is wrong:** Key-value is a non-relational model, not ACID financial.
> **Why D is wrong:** Wide-column is non-relational, not strong ACID.

**Question 6.** A social network stores highly connected friend relationships. Ideal model?
A. Key-value
B. Document
C. Graph
D. Relational

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Graph databases excel at highly connected relationship data like friend networks.
> **Why A is wrong:** Key-value stores are simple lookups, not relationship traversal.
> **Why B is wrong:** Document stores are for flexible records, not graph edges.
> **Why D is wrong:** Relational can model relations but is weak at deep graph traversal.

**Question 7.** Huge traffic spikes handled by adding servers, not upgrading one, favours:
A. Vertical scaling of relational
B. Horizontal scaling of non-relational
C. Self-managed only
D. Provider-managed only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Non-relational systems scale out horizontally across servers to handle spikes.
> **Why A is wrong:** Vertical scaling of relational upgrades one server, with a ceiling.
> **Why C is wrong:** Self-managed is deployment, not a scaling approach.
> **Why D is wrong:** Provider-managed is deployment, not inherently horizontal scaling.

**Question 8.** Max OS/engine control but highest operational effort describes:
A. Relational + provider-managed
B. Non-relational + provider-managed
C. Self-managed (any type)
D. Provider-managed (any type)

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Self-managed puts install, patch, and backup duties on the customer, the highest effort.
> **Why A is wrong:** Relational plus provider-managed is low-effort.
> **Why B is wrong:** Non-relational plus provider-managed is low-effort.
> **Why D is wrong:** Provider-managed reduces effort, not maximizes it.

**Question 9.** Amazon RDS running MySQL maps to which two sub-objectives?
A. Relational + provider-managed
B. Non-relational + self-managed
C. Relational + self-managed
D. Non-relational + provider-managed

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** RDS MySQL is a relational engine delivered as a provider-managed service.
> **Why B is wrong:** Non-relational plus self-managed would be MongoDB on EC2.
> **Why C is wrong:** RDS manages the engine, so not self-managed.
> **Why D is wrong:** MySQL is relational, not non-relational.

**Question 10.** MongoDB installed and run on EC2 is an example of:
A. Relational + provider-managed
B. Non-relational + self-managed
C. Relational + self-managed
D. Non-relational + provider-managed

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** MongoDB is non-relational and operating it on EC2 is self-managed.
> **Why A is wrong:** It is non-relational, not relational.
> **Why C is wrong:** MongoDB is non-relational, not relational.
> **Why D is wrong:** Running it yourself on EC2 is self-managed, not provider-managed.

**Question 11.** A fixed, predefined schema designed before storing data is characteristic of:
A. Non-relational databases
B. Relational databases
C. Self-managed deployment
D. Provider-managed deployment

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Relational databases require a fixed, predefined schema designed before storing data.
> **Why A is wrong:** Non-relational is flexible or schema-less.
> **Why C is wrong:** Self-managed is deployment, unrelated to schema shape.
> **Why D is wrong:** Provider-managed is deployment, unrelated to schema shape.

**Question 12.** Which AWS service is a managed, serverless key-value and document NoSQL DB?
A. Amazon RDS
B. Amazon Neptune
C. Amazon DynamoDB
D. Amazon Aurora

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** DynamoDB is AWS managed serverless key-value and document NoSQL.
> **Why A is wrong:** RDS is relational, not key-value NoSQL.
> **Why B is wrong:** Neptune is graph, not key-value document.
> **Why D is wrong:** Aurora is relational, not NoSQL.

**Question 13.** A company chooses a managed service mainly to reduce:
A. Data consistency
B. Administrative/operational overhead
C. The need for a schema
D. Network bandwidth

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A managed service primarily reduces administrative and operational overhead.
> **Why A is wrong:** Managed services still preserve consistency via ACID options.
> **Why C is wrong:** A schema may still be required for relational managed services.
> **Why D is wrong:** Network bandwidth is unrelated to the management trade-off.

**Question 14.** True statement about self-managed databases:
A. The provider applies all patches.
B. Backups are automatic by the CSP.
C. The customer handles installation and maintenance.
D. They cannot run relational engines.

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** In self-managed models the customer handles installation and maintenance.
> **Why A is wrong:** The provider applies patches only in provider-managed models.
> **Why B is wrong:** Backups are automatic only in provider-managed models.
> **Why D is wrong:** Self-managed can absolutely run relational engines.

**Question 15.** Amazon Aurora is best described as:
A. A non-relational graph database
B. A MySQL/PostgreSQL-compatible relational engine on RDS
C. A self-managed EC2 database
D. A key-value store

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Aurora is a MySQL and PostgreSQL compatible relational engine on RDS.
> **Why A is wrong:** Aurora is relational, not a graph database.
> **Why C is wrong:** Aurora is a managed RDS service, not self-managed EC2.
> **Why D is wrong:** Aurora is relational, not key-value.

**Question 16.** A flexible catalog where items have varying attributes is BEST served by:
A. Relational with fixed schema
B. Non-relational document store
C. Self-managed relational
D. Provider-managed relational

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Varying item attributes suit a flexible-schema non-relational document store.
> **Why A is wrong:** Fixed-schema relational fights varying attributes.
> **Why C is wrong:** Self-managed relational is about ops, not schema flexibility.
> **Why D is wrong:** Provider-managed relational is still fixed-schema.

**Question 17.** Amazon Neptune serves which data model?
A. Relational tables
B. Key-value
C. Graph
D. Document

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Neptune is AWS managed graph database service for connected data.
> **Why A is wrong:** Relational tables are RDS, not Neptune.
> **Why B is wrong:** Key-value is DynamoDB, not Neptune.
> **Why D is wrong:** Document is DocumentDB, not Neptune.

**Question 18.** The usual trade-off of provider-managed vs. self-managed is:
A. Less control over the OS/engine
B. No backups
C. Inability to use SQL
D. Higher data-loss risk

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** Managed services abstract the OS and engine, reducing low-level control.
> **Why B is wrong:** Managed services provide automated backups.
> **Why C is wrong:** SQL remains usable on managed relational services.
> **Why D is wrong:** Managed services lower data-loss risk via backups, not raise it.

**Question 19.** Which deployment keeps the DB in your own network with full OS access for compliance?
A. Provider-managed
B. Self-managed on EC2
C. Serverless NoSQL only
D. Relational only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Self-managed on EC2 gives network isolation and full OS control for compliance.
> **Why A is wrong:** Provider-managed reduces OS control, not increases it.
> **Why C is wrong:** Self-managed is not limited to serverless NoSQL.
> **Why D is wrong:** It is not limited to relational only.

**Question 20.** A "managed NoSQL service" is classified as:
A. Relational + self-managed
B. Non-relational + provider-managed
C. Relational + provider-managed
D. Non-relational + self-managed

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A managed NoSQL service is non-relational and provider-managed.
> **Why A is wrong:** NoSQL is non-relational, not relational.
> **Why C is wrong:** NoSQL is non-relational, not relational.
> **Why D is wrong:** Managed means provider-operated, not self-managed.
