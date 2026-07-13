---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 3.3: Backup and Recovery"
aliases: [Objective-3.3-Backup-Recovery, Domain-3.3-Backup-Recovery, Backup, Recovery, RTO, RPO, Full-Backup, Incremental, Differential, Replication, Retention]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 3.3
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-3.3, backup, recovery, rto, rpo, full, incremental, differential, replication, retention, schedule, encryption, on-site, off-site, in-place, parallel, bulk, granular, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 3.0 Operations
## 📘 Objective 3.3: Given a Scenario, Use Appropriate Backup and Recovery Methods

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 3.0 Operations = 17% of total exam
> **Objective 3.3 Focus:** Designing backup and recovery that meets business needs — backup types (full/incremental/differential), schedule, retention, replication, encryption, testing (recoverability/integrity), locations (on-site/off-site), and recovery types/options (in-place/parallel, bulk/granular).

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 3.3
### Objective Name: *Given a scenario, use appropriate backup and recovery methods.*

**What this objective means (beginner-friendly):**

Imagine your laptop. You worry it might break or get stolen, so you copy your files. But HOW you copy matters:
- **Full backup** = copy EVERYTHING (slow, big, but one restore).
- **Incremental** = copy only what changed since the *last backup of any kind* (fast, small, but restore needs the full + every incremental).
- **Differential** = copy only what changed since the *last full* (medium, restore needs full + the latest differential).

Then you decide: *when* (schedule), *how long to keep* (retention), *where* (on-site vs off-site), *copy to another region?* (replication), *encrypt?* (yes), and *do you TEST it actually restores?* (testing — recoverability + integrity). When disaster hits, you recover **in-place** (same place) or **parallel** (new place), and you can restore **bulk** (everything) or **granular** (one file).

3.3 is about choosing the right backup/recovery design for a scenario's RTO/RPO, cost, and compliance. It's the "when it breaks, can we get it back?" objective — central to Operations.

**Why it matters in the real world:**
- Ransomware, accidental deletes, region outages — backups are the last line of defense.
- A backup you never tested is a *hope*, not a recovery plan. Unverified restores fail exactly when needed.
- RTO/RPO are business commitments; backup design must meet them or the company loses money/data.

**Why CompTIA tests it:**
- 3.3 is scenario-heavy: "RPO 5 min" → frequent incremental/replication; "restore one file" → granular; "region down" → off-site/replica.
- The **full/incremental/differential** math and restore chains are classic distractors.
- It ties to 2.3 (migration/repatriation), 2.5 (compliance retention), and 3.1 (monitoring the backups).

---

## SECTION 2 — EXAM KNOWLEDGE

### 3.3.1 — BACKUP TYPES

#### Full Backup
**Definition:** A complete copy of all selected data.
**Advantages:** Fastest, simplest restore (one backup); highest integrity.
**Disadvantages:** Slowest to create; largest storage footprint; high network/IO load.
**When to use:** Periodically as the base (e.g., weekly full), or for small datasets.
**Restore:** Just the full. One step.

#### Incremental Backup
**Definition:** Copies only data changed since the **last backup of any type** (full or incremental).
**Advantages:** Fastest, smallest (only deltas); low load; enables frequent backups (supports tight RPO).
**Disadvantages:** Restore is slowest/most fragile — needs the full + **every** incremental in sequence; one missing incremental breaks the chain.
**When to use:** Frequent backups (daily/hourly) where RPO is tight and storage is constrained.
**Restore:** Full + all incrementals in order.

#### Differential Backup
**Definition:** Copies only data changed since the **last full backup**.
**Advantages:** Restore is simpler/faster than incremental — needs only the full + the **latest** differential. Less fragile (one differential missing, not many).
**Disadvantages:** Grows over the week (each differential includes all changes since the full); larger than incremental; slower than incremental to create.
**When to use:** Balance between full (slow restore chain none) and incremental (fragile chain); common weekly-full + daily-differential.
**Restore:** Full + latest differential.

**The classic comparison (weekly full, daily backups):**

| Day | Full | Incremental (since last ANY) | Differential (since last FULL) |
|---|---|---|---|
| Sun | Full | Full | Full |
| Mon | — | changes since Sun | changes since Sun |
| Tue | — | changes since Mon | changes since Sun (Mon+Tue) |
| Wed | — | changes since Tue | changes since Sun (Mon+Tue+Wed) |
| Restore Wed | Full + Sun/Mon/Tue incrementals | Full + latest differential (Wed) |

> **Memory:** Incremental = chain of many (fragile, small). Differential = always vs full (fewer files to restore, grows in size).


---

### 3.3.2 — SCHEDULE

**Definition:** How often backups run (frequency) and when.
**Purpose:** Meet RPO — the backup frequency defines the maximum data-loss window.
**How it works:** Cron/backup policy (e.g., full Sun 02:00, incremental every 6h). Align with low-traffic windows.
**Considerations:** Tighter RPO → more frequent backups (incremental/replication). Cost vs RPO trade-off.
**Exam angle:** "RPO 1 hour" → backup/replicate at least hourly (or continuous replication).

### 3.3.3 — RETENTION

**Definition:** How long backups are kept before deletion/archival.
**Purpose:** Balance recovery window (need old backups) vs cost and compliance.
**How it works:** GFS (Grandfather-Father-Son) schemes — daily (son) → weekly (father) → monthly/yearly (grandfather); archive to cheap tier for long-term; legal hold may extend.
**Considerations:** Compliance may mandate years (ties to 2.5 compliance + 3.1 retention). Too short = can't recover old; too long = cost.
**Exam angle:** "Keep 1 year for audit" → retention policy + archive tier; "7-day retention" fails a 1-year audit need.

### 3.3.4 — REPLICATION

**Definition:** Continuously copying data to another location (often another region/account) in near-real-time.
**Purpose:** Meet tight RPO/RTO; survive a region/zone failure (DR).
**How it works:** Synchronous (zero/low RPO, but latency cost) or asynchronous (some RPO, lower latency); DB replicas, storage cross-region replication, block replication.
**Considerations:** RPO dictates sync vs async. Replication ≠ backup (a corrupted write replicates too — you still need point-in-time/backups).
**Exam angle:** "Region failure, RPO near zero" → synchronous cross-region replication; "ransomware" → replication alone insufficient, need isolated backups.

### 3.3.5 — ENCRYPTION

**Definition:** Encrypting backups at rest and in transit.
**Purpose:** Protect data confidentiality; meet compliance (ties to 2.5).
**How it works:** KMS-managed keys (SSE), customer-managed keys, TLS in transit; encrypt before leaving on-site.
**Considerations:** Key management is critical — lose the key, lose the backup. Separate key access from backup access (least privilege).
**Exam angle:** "Backups contain PII" → encrypt at rest + in transit; "key stored with backup" → bad (compromise both).

### 3.3.6 — BACKUP LOCATIONS

#### On-site
**Definition:** Backup stored in the same facility / local network as the source.
**Advantages:** Fast backup/restore (no internet), cheap, low latency.
**Disadvantages:** Destroyed by the same disaster (fire/flood/theft) as production; limited DR value.
**When to use:** Fast local restore; first tier before off-site copy.

#### Off-site
**Definition:** Backup stored in a separate physical location / cloud region / account.
**Advantages:** Survives local disaster; enables DR; meets geographic separation.
**Disadvantages:** Slower transfer (network), possible cost, must be encrypted in transit.
**When to use:** Required for real DR; follow the 3-2-1 rule (3 copies, 2 media, 1 off-site).

> **3-2-1 rule (industry standard, exam-relevant):** Keep **3** copies of data, on **2** different media, with **1** copy **off-site**.

### 3.3.7 — TESTING

Backups are worthless unverified. Testing has two sub-concepts:

#### Recoverability
**Definition:** Confirming a backup can actually be restored (the restore works end-to-end).
**Purpose:** Avoid "backup succeeded but restore fails" — the worst surprise.
**How it works:** Periodic restore drills to a test environment; document RTO actually achieved.
**Exam angle:** "We have nightly backups" is NOT enough — must TEST recovery (recoverability).

#### Integrity
**Definition:** Confirming the backup data is complete and uncorrupted (not bit-rotted, not partial).
**Purpose:** Detect silent corruption, failed backups, or tampering.
**How it works:** Checksums/hashes, backup job success verification, immutable/locked copies (WORM) to prevent tampering (ransomware).
**Exam angle:** "How do you know the backup isn't corrupted?" → integrity checks (hash/checksum) + verify job status.


---

### 3.3.8 — RECOVERY TYPES

#### In-place Recovery
**Definition:** Restore data back into the **original** location/system (overwrite or replace in place).
**Purpose:** Bring the existing system back; simplest when the system itself is fine.
**Considerations:** Risk of overwriting good data with bad if not careful; may require downtime; the failed system must still be usable.
**When to use:** The instance/environment is intact; you just need the data back.
**Exam angle:** "Restore the corrupted database to the same server" → in-place.

#### Parallel Recovery
**Definition:** Restore to a **new/separate** location (new instance, new region, different account) alongside or instead of the original.
**Purpose:** Avoid further disrupting production; test before cutover; survive when the original site is destroyed.
**Considerations:** More steps (re-point, validate, cut over); useful for DR and clean recovery.
**When to use:** Original site down/destroyed, or you want to validate before replacing production.
**Exam angle:** "Region is down, restore elsewhere" → parallel (DR) recovery.

### 3.3.9 — RECOVERY OPTIONS

#### Bulk Recovery
**Definition:** Restore a large amount at once — entire system, volume, or dataset.
**Purpose:** Full rebuild after major loss; DR.
**Considerations:** Slower, higher RTO; often the whole-environment restore.
**When to use:** Total loss, migration, or full-environment rebuild.
**Exam angle:** "Entire VM lost" → bulk recovery.

#### Granular Recovery
**Definition:** Restore a small, specific item — one file, one email, one table/row.
**Purpose:** Fix a single mistake (accidental delete) without a full restore.
**Considerations:** Needs backup that supports item-level restore; faster RTO for the specific item.
**When to use:** Accidental deletion, corrupted single object.
**Exam angle:** "User deleted one file" → granular recovery (not full restore).

### 3.3.10 — THE RTO / RPO ANCHOR (ties it all together)

Every 3.3 decision maps to two business metrics:
- **RPO (Recovery Point Objective):** max acceptable **data loss** (how old the backup can be). Drives **schedule + replication** frequency. RPO 5 min → continuous replication / frequent incremental.
- **RTO (Recovery Time Objective):** max acceptable **downtime** to restore. Drives **recovery type/option + location + testing**. Tight RTO → parallel/off-site replica, pre-staged.

| Need | Drives |
|---|---|
| Tight RPO | Frequent incremental / synchronous replication |
| Tight RTO | Off-site replica, parallel recovery, tested restore |
| One file restore | Granular recovery |
| Whole-system loss | Bulk recovery / in-place or parallel |
| Region failure | Off-site / cross-region replica + parallel |
| Audit/legal | Long retention + immutable (WORM) |

> **Replication is NOT a backup.** A corrupted or ransomware-encrypted write replicates to the replica. You still need point-in-time backups + immutable copies for true recovery.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — AWS Backup Design
- **Types:** EBS snapshots (incremental), RDS automated backups (full + incremental transaction logs for PITR).
- **Schedule:** Daily full snapshot + continuous transaction logs (RPO minutes).
- **Retention:** 35-day operational + annual archive to Glacier (compliance).
- **Replication:** Cross-region backup (CRR) to a DR region; RPO near-zero via Multi-AZ + async replica.
- **Encryption:** KMS-encrypted snapshots; keys separate from backup role.
- **Location:** On-site EBS snap + off-site (another region/account) → 3-2-1.
- **Testing:** Periodic restore to a test account (recoverability); checksum verification (integrity).
- **Recovery:** Single-file from S3 versioning (granular); full instance restore (bulk, in-place or parallel to new AZ).

### 3.2 — Azure Backup Design
- **Types:** Azure Backup (full + incremental, forever-incremental); SQL/Azure VM backup.
- **Schedule:** Policy-based (e.g., daily + hourly log backup).
- **Retention:** Daily/weekly/monthly/yearly (GFS) in Recovery Vault; long-term archive tier.
- **Replication:** Geo-redundant storage (GRS) for the vault; ASR for VM replication (DR).
- **Encryption:** Encryption at rest (platform/CMK); in transit TLS.
- **Location:** Local vault + geo-redundant (off-site).
- **Testing:** Restore to a separate resource group (recoverability); integrity via backup job reports.
- **Recovery:** Item-level (granular) or full VM (bulk); parallel to new region via ASR.

### 3.3 — GCP Backup Design
- **Types:** Persistent Disk snapshots (incremental), Cloud SQL automated backups (PITR).
- **Schedule:** Snapshot schedules (hourly/daily); SQL binlog PITR.
- **Retention:** Snapshot retention policies; archive to Archive class; long-term via export.
- **Replication:** Snapshots copied to another region; Cloud SQL cross-region replica.
- **Encryption:** CMEK (Cloud KMS); in transit TLS.
- **Location:** Multi-region snapshot copies (off-site).
- **Testing:** Restore to a temp project (recoverability); hash verification (integrity).
- **Recovery:** File from snapshot (granular); full disk/instance (bulk, parallel to new zone).

### 3.4 — Ransomware Scenario (why replication ≠ backup)
A company replicates its file share cross-region. Ransomware encrypts files; the encryption replicates. They restore from **immutable, offline/isolated backups** (WORM, separate account, MFA-deleted-protected) — granular restore of affected folders. Lesson: need isolated, immutable, tested backups beyond replication.

### 3.5 — Accidental Deletion (granular)
A user deletes one S3 object. Versioning + lifecycle keep prior versions; restore that single object (granular) in seconds — no bulk restore, no downtime.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Backup Types

| Type | Backs up | Restore needs | Restore speed | Backup speed | Storage | Fragility |
|---|---|---|---|---|---|---|
| **Full** | Everything | Just full | Fastest | Slowest | Largest | None |
| **Incremental** | Since last backup (any) | Full + ALL incrementals | Slowest | Fastest | Smallest | High (one missing = break) |
| **Differential** | Since last FULL | Full + latest differential | Medium | Medium | Grows over week | Lower (one missing) |

### 4.2 — Recovery Types vs Options

| Concept | Meaning | Use when |
|---|---|---|
| In-place | Restore to original location | System intact, just need data back |
| Parallel | Restore to new/separate location | Original down, or validate before cutover (DR) |
| Bulk | Restore whole system/dataset | Total loss / full rebuild |
| Granular | Restore one item (file/row) | Accidental single delete |

### 4.3 — On-site vs Off-site

| | On-site | Off-site |
|---|---|---|
| Speed | Fast (local) | Slower (network) |
| Disaster survival | Destroyed by same disaster | Survives local disaster |
| DR value | Low alone | Required for DR |
| Rule | First tier | 3-2-1: 1 copy off-site |

### 4.4 — Replication vs Backup

| | Replication | Backup |
|---|---|---|
| Purpose | Low RPO / DR (near-real-time copy) | Point-in-time recovery, history |
| Corruption handling | Replicates bad writes | PIT/immutable copies survive |
| Ransomware | Vulnerable | Isolated/immutable needed |
| Verdict | Not a substitute for backup | Needed alongside |

### 4.5 — RTO/RPO Driver Map

| Requirement | Drives |
|---|---|
| Tight RPO | Frequent incremental / sync replication |
| Tight RTO | Off-site replica, parallel, tested restore |
| Single file | Granular recovery |
| Whole loss | Bulk / in-place or parallel |
| Region down | Off-site + parallel recovery |
| Audit/legal | Long retention + WORM immutable |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — Incremental restore chain.**
Question: "Restore Tuesday from Sun full + daily incrementals."
Correct: Need the **full + EVERY incremental** (Sun, Mon, Tue) in sequence.
Why wrong: Missing one incremental breaks the restore. Incremental = fragile chain.

**Trap #2 — Differential restore is simpler.**
Question: "Restore Wed with a weekly full + differentials."
Correct: Need only the **full + the latest differential** (Wed) — not all.
Why wrong: Differential is always vs the last full; one file, not a chain.

**Trap #3 — Replication = backup.**
Question: "We replicate cross-region, so we're backed up."
Correct: **No** — replication copies bad/encrypted writes too; need point-in-time + immutable backups.
Why wrong: Ransomware/corruption replicates; replication alone isn't recovery.

**Trap #4 — Untested backup = safe.**
Question: "Nightly backups run successfully; we're covered."
Correct: **No** — must TEST recovery (recoverability). A successful backup can still fail to restore.
Why wrong: "Backup succeeded" ≠ "restore works."

**Trap #5 — RPO drives frequency, not RTO.**
Question: "RPO 5 minutes — how often back up?"
Correct: At least every 5 min / continuous replication. RPO = max data loss → drives schedule/replication.
Why wrong: Confusing RPO (data loss) with RTO (downtime).

**Trap #6 — RTO drives recovery speed/location.**
Question: "RTO 1 hour — how to recover fast?"
Correct: Off-site replica + parallel recovery + tested restore. RTO = max downtime → drives recovery design.
Why wrong: RTO is time-to-recover, not data-loss window.

**Trap #7 — On-site only for DR.**
Question: "Local backups in the same building — enough for DR?"
Correct: **No** — same disaster destroys both. Need off-site (3-2-1).
Why wrong: On-site survives only local failures, not site loss.

**Trap #8 — Granular vs Bulk for one file.**
Question: "User deleted one file."
Correct: **Granular** restore — not a full bulk restore.
Why wrong: Bulk is for whole-system loss; overkill + slower for one item.

**Trap #9 — In-place when site is destroyed.**
Question: "Entire region down; restore in-place there."
Correct: **Parallel** recovery to a different region/account — original site is gone.
Why wrong: In-place needs the original location usable.

**Trap #10 — Encryption key with the backup.**
Question: "Store the decryption key in the same backup bucket."
Correct: **Bad** — compromise of bucket yields both data and key. Separate key access (KMS, least privilege).
Why wrong: Key separation is basic backup security.

**Trap #11 — Retention too short for compliance.**
Question: "Audit needs 7 years; keep 30 days."
Correct: **Fails compliance** — extend retention / archive (ties to 2.5/3.1).
Why wrong: Short retention violates mandated hold.

**Trap #12 — Differential grows but restore is simple.**
Question: "Differential is small like incremental?"
Correct: **No** — differential grows (all changes since full); it's restore-simpler, not backup-smaller.
Why wrong: Its advantage is restore chain, not size.

**Trap #13 — Integrity vs Recoverability.**
Question: "How do you know the backup isn't corrupted?"
Correct: **Integrity** checks (hash/checksum, job verification), not recoverability (which is about restore working).
Why wrong: Recoverability = can restore; integrity = data is complete/uncorrupted. Both needed, different checks.

**Trap #14 — Sync vs async replication for RPO.**
Question: "RPO near zero across regions."
Correct: **Synchronous** (low/zero RPO) — but higher latency cost. Async has some RPO.
Why wrong: Async replicates with lag; not "near zero" RPO.

**Trap #15 — No immutable copy vs ransomware.**
Question: "Protect backups from ransomware."
Correct: **Immutable / WORM / isolated (separate account, MFA-delete)** copies — replication alone is vulnerable.
Why wrong: Mutable replicas get encrypted too.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Tight RPO + RTO E-commerce
**Scenario:** RPO 5 min, RTO 1 hr, PII data, multi-region. Design backup/recovery.
**Walkthrough:**
1. **Types:** Full + frequent incremental (or DB transaction-log PITR) for RPO 5 min.
2. **Schedule:** Continuous/log backup; full daily.
3. **Replication:** Async cross-region replica (DR) + Multi-AZ.
4. **Retention:** 35-day + annual archive (compliance).
5. **Encryption:** KMS at rest + TLS; key role separate.
6. **Location:** On-site snap + off-site region (3-2-1).
7. **Testing:** Restore drill (recoverability) + checksum (integrity).
8. **Recovery:** Parallel to DR region for region failure (RTO 1 hr).
**Bonus Q:** Ransomware hits — replication alone enough? → No, need immutable/WORM isolated backup.

### PBQ 2 — Restore One File
**Scenario:** User deletes a single critical file; production must stay up.
**Walkthrough:**
1. **Option:** **Granular** restore of that one object (e.g., S3 versioning / file-level restore).
2. No bulk restore, no downtime.
3. Verify integrity (hash) post-restore.
**Bonus Q:** Full restore instead? → Overkill; bulk disrupts and is slower.

### PBQ 3 — Region Down (Parallel DR)
**Scenario:** Entire primary region offline; RTO 4 hr.
**Walkthrough:**
1. **Recovery type:** **Parallel** — restore to DR region (not in-place, original gone).
2. Use off-site replica + backup; re-point DNS/LB.
3. Validate (recoverability) before cutover.
**Bonus Q:** In-place recovery possible? → No, site destroyed; parallel only.

### PBQ 4 — Backup Type Math
**Scenario:** Weekly full Sunday; daily backups the rest of the week. Restore needed for Thursday.
**Walkthrough:**
- **If incremental:** Full (Sun) + incrementals Mon, Tue, Wed, Thu (all four) in order.
- **If differential:** Full (Sun) + differential (Thu, which contains Mon–Thu changes).
- Differential = fewer files to restore, but each is larger.
**Bonus Q:** One incremental (Tue) missing → incremental restore fails; differential still works with Thu diff.

### PBQ 5 — Compliance Retention + Ransomware
**Scenario:** 7-year audit retention; recent ransomware attempt; data must stay in-region.
**Walkthrough:**
1. **Retention:** GFS + archive tier for 7 years; immutable (WORM) for legal hold.
2. **Encryption:** In-region KMS; key separation.
3. **Ransomware defense:** Isolated, immutable, MFA-delete-protected backups + replication separately.
4. **Testing:** Periodic restore verifies recoverability.
**Bonus Q:** Replicate only → safe from ransomware? → No, encryption replicates; need immutable backup.


---

## SECTION 7 — MEMORY AIDS

### 7.1 — The three backup types (core of 3.3)
**"Full = everything. Incremental = since last ANY. Differential = since last FULL."**
- Full: one restore step.
- Incremental: chain (full + all) — fragile, small.
- Differential: full + latest diff — fewer files, grows in size.

### 7.2 — Restore chain story
**Incremental:** "Collect every ticket from the start." Miss one → can't finish.
**Differential:** "Just the latest summary since the baseline." One file.

### 7.3 — RTO vs RPO (don't mix)
**RPO = Point (data loss) — how much you can lose.**
**RTO = Time (downtime) — how long to recover.**
- RPO tight → back up often (incremental/replication).
- RTO tight → recover fast (off-site replica, parallel, tested).

### 7.4 — 3-2-1 rule
**"3 copies, 2 media, 1 off-site."** The bedrock of backup design.

### 7.5 — Replication ≠ backup
**"A mirror reflects the crack too."** Replication copies corruption/ransomware. You need point-in-time + immutable backups.

### 7.6 — Recovery decision
- Original fine → **In-place** (restore here).
- Original gone → **Parallel** (restore elsewhere / DR).
- One file → **Granular**.
- Whole system → **Bulk**.

### 7.7 — Testing two faces
- **Recoverability** = "can we actually restore?" (do the drill).
- **Integrity** = "is the backup complete/uncorrupted?" (hash/checksum).

### 7.8 — Decision tree (ASCII)
```
Given a backup/recovery scenario:
│
├─ RPO tight (min) ? ─────────────► Frequent incremental / sync replication
├─ RTO tight (hr) ? ─────────────► Off-site replica + parallel + tested restore
├─ One file deleted ? ────────────► Granular recovery
├─ Whole system lost ? ───────────► Bulk (in-place or parallel)
├─ Original site down ? ──────────► Parallel (DR) recovery
├─ Audit/legal hold ? ────────────► Long retention + WORM immutable
├─ Ransomware risk ? ─────────────► Immutable / isolated / MFA-delete backups
└─ How to prove it works ? ───────► TEST (recoverability + integrity)
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| Snapshot / backup | EBS snapshots, AWS Backup | Azure Backup, VM snapshots | Persistent Disk snapshots |
| DB PITR | RDS automated backups + logs | Azure SQL PITR | Cloud SQL PITR |
| Full/incremental | Snapshots are incremental; AMIs full | Azure Backup (forever-incremental) | Snapshots incremental |
| Schedule | Backup plans / cron | Backup policies | Snapshot schedules |
| Retention | Backup vault + Glacier archive | Recovery Vault GFS + archive | Snapshot retention + archive |
| Replication | Cross-region backup (CRR) | GRS / geo-redundant, ASR | Cross-region snapshot copy |
| Encryption | KMS (SSE) | CMK / platform encryption | CMEK (Cloud KMS) |
| Off-site | Another region/account | Geo-redundant vault | Multi-region copies |
| Testing (recoverability) | Restore to test account | Restore to test RG | Restore to temp project |
| Immutable / WORM | Object Lock (S3), Vault Lock | Immutable blob / soft-delete | Bucket lock / retention |
| Granular restore | S3 versioning, file-level | Item-level restore | File from snapshot |
| Integrity | Checksums, job verification | Backup reports | Hash verification |

**Cross-cloud constant:** Snapshots are incremental; PITR via transaction logs; cross-region copy for DR; KMS encryption; 3-2-1 + immutable copies for ransomware. The *concept* is identical across providers.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What's the difference between incremental and differential backups?**
A: Incremental backs up changes since the *last backup of any type* (restore needs full + all incrementals). Differential backs up changes since the *last full* (restore needs full + latest differential).

**Q2: What is RPO?**
A: Recovery Point Objective — the maximum acceptable data loss (how old a backup can be). It drives backup frequency and replication.

**Q3: What is RTO?**
A: Recovery Time Objective — the maximum acceptable downtime to restore service. It drives recovery design (off-site replica, parallel, tested restore).

### 🎓 Cloud Administrator Questions

**Q4: A company replicates cross-region. Are they safe from ransomware?**
A: No — replication copies encrypted/corrupted writes. They need immutable, isolated backups (WORM, separate account, MFA-delete) plus point-in-time copies.

**Q5: How do you prove backups actually work?**
A: Test recovery (recoverability) via periodic restore drills, and verify integrity (checksums/hashes, job success) — "backup succeeded" is not "restore works."

**Q6: One user deleted a single file; production must stay up. Approach?**
A: Granular restore of that one item (versioning/file-level) — no bulk restore, no downtime.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: Client wants RPO 5 min and RTO 1 hr. Your design?**
A: Frequent incremental / continuous transaction-log backups (RPO 5 min), cross-region replica + parallel recovery to DR region with a tested restore (RTO 1 hr), encrypted, retained per compliance, following 3-2-1.

**Q8: "We keep backups in the same server room." Good enough?**
A: For local failure yes, but not DR — a fire/flood destroys both. Need an off-site copy (3-2-1: 1 off-site).

**Q9: Why separate the encryption key from the backup?**
A: If both are compromised together (same bucket/account), the backup is useless. Key separation (KMS, least privilege) keeps data protected even if storage leaks.

---

## SECTION 10 — FLASHCARDS

**Backup types**
Q: Backs up everything?
A: Full backup.

Q: Since last backup of ANY type?
A: Incremental.

Q: Since last FULL only?
A: Differential.

Q: Incremental restore needs?
A: Full + ALL incrementals (chain).

Q: Differential restore needs?
A: Full + latest differential.

**RTO / RPO**
Q: Max data loss allowed?
A: RPO (Recovery Point Objective).

Q: Max downtime allowed?
A: RTO (Recovery Time Objective).

Q: RPO tight → ?
A: Frequent backup / replication.

Q: RTO tight → ?
A: Off-site replica + parallel + tested restore.

**Locations / Replication**
Q: 3-2-1 rule?
A: 3 copies, 2 media, 1 off-site.

Q: Same building backups enough for DR?
A: No — need off-site.

Q: Replication = backup?
A: No — replicates corruption/ransomware.

Q: Survive region failure?
A: Off-site / cross-region replica + parallel recovery.

**Recovery types / options**
Q: Restore to original location?
A: In-place.

Q: Restore to new/separate location (DR)?
A: Parallel.

Q: Restore one file?
A: Granular.

Q: Restore whole system?
A: Bulk.

**Testing / Encryption**
Q: Confirm restore actually works?
A: Recoverability (test drill).

Q: Confirm backup not corrupted?
A: Integrity (hash/checksum).

Q: Protect backups from ransomware?
A: Immutable / WORM / isolated / MFA-delete.

Q: Where to keep decryption key?
A: Separate from backup (KMS, least privilege).

**Provider**
Q: AWS snapshot service?
A: EBS snapshots (incremental).

Q: Azure backup service?
A: Azure Backup / Recovery Vault.

Q: GCP snapshot service?
A: Persistent Disk snapshots.

Q: Immutable S3?
A: S3 Object Lock (WORM).


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** A backup that copies all selected data is:
A) Incremental  B) Full  C) Differential  D) Replication
**Answer: B) Full.**
*Why others wrong:* Incremental/differential copy only changes; replication is a copy method.

**E2.** RPO stands for:
A) Recovery Time Objective  B) Recovery Point Objective  C) Restore Point Only  D) Region Point Object
**Answer: B) Recovery Point Objective** (max data loss).
*Why others wrong:* RTO is time; others are invented.

**E3.** Restoring a single deleted file is:
A) Bulk  B) Granular  C) Parallel  D) In-place
**Answer: B) Granular.**
*Why others wrong:* Bulk is whole-system; parallel/in-place are locations.

**E4.** The 3-2-1 backup rule requires at least one copy:
A) In the cloud  B) Off-site  C) On tape  D) Encrypted
**Answer: B) Off-site.**
*Why others wrong:* 3-2-1 = 3 copies, 2 media, 1 off-site.

**E5.** Confirming a backup can actually be restored is called:
A) Integrity  B) Recoverability  C) Replication  D) Retention
**Answer: B) Recoverability.**
*Why others wrong:* Integrity is corruption-check; replication/retention unrelated.

### MEDIUM (10)

**M1.** Weekly full Sun; daily incrementals. Restore needed for Thursday. What's required?
A) Full + Thu incremental only  B) Full + Sun/Mon/Tue/Wed/Thu incrementals  C) Full + Wed differential  D) Full only
**Answer: B) Full + all incrementals (Sun–Thu) in sequence.**
*Why others wrong:* Incremental needs the whole chain; differential isn't used here.

**M2.** Weekly full Sun; daily differentials. Restore Thursday. What's required?
A) Full + all incrementals  B) Full + Thu differential  C) Full only  D) Full + Mon differential
**Answer: B) Full + latest (Thu) differential.**
*Why others wrong:* Differential is vs the last full; only the latest needed.

**M3.** RPO 5 minutes. Best approach:
A) Weekly full only  B) Continuous replication / frequent incremental  C) Annual backup  D) Manual copy
**Answer: B) Continuous replication / frequent incremental.**
*Why others wrong:* Others violate the tight RPO.

**M4.** RTO 1 hour for region failure. Best recovery:
A) In-place same region  B) Parallel to DR region + tested restore  C) Manual copy  D) On-site only
**Answer: B) Parallel to DR region + tested restore.**
*Why others wrong:* In-place needs original; on-site won't survive region loss.

**M5.** "We replicate cross-region, so ransomware is covered." Correct response:
A) True  B) False — replication copies encryption; need immutable backups  C) True if async  D) True if sync
**Answer: B) False** — replication copies bad writes; need immutable/isolated backups.
*Why others wrong:* Sync/async still replicate corruption.

**M6.** User deleted one file; prod must stay up. Best:
A) Bulk restore  B) Granular restore  C) Full rebuild  D) In-place full
**Answer: B) Granular restore.**
*Why others wrong:* Bulk/full is overkill and disruptive.

**M7.** Entire region offline. Recovery type:
A) In-place  B) Parallel  C) Granular  D) Replication
**Answer: B) Parallel** (restore to a different location/DR region).
*Why others wrong:* In-place needs the original site usable.

**M8.** Audit needs 7-year retention; current is 30 days. Fix:
A) Keep 30 days  B) Extend retention / archive (WORM)  C) Replicate  D) Encrypt
**Answer: B) Extend retention / archive (immutable).**
*Why others wrong:* Others don't meet the hold.

**M9.** How do you verify a backup isn't corrupted?
A) Recoverability  B) Integrity (hash/checksum)  C) Replication  D) Schedule
**Answer: B) Integrity** (hash/checksum verification).
*Why others wrong:* Recoverability is about restore working; others unrelated.

**M10.** Store decryption key in the same backup bucket?
A) Yes  B) No — separate key access (KMS, least privilege)  C) Only if encrypted  D) Only if on-site
**Answer: B) No** — separate the key from the backup.
*Why others wrong:* Co-locating yields both on compromise.

### HARD (5)

**H1.** RPO near-zero across regions, but latency-sensitive app. Best replication:
A) Async only  B) Synchronous (low/zero RPO, higher latency)  C) No replication  D) Manual
**Answer: B) Synchronous** — meets near-zero RPO; accepts latency cost.
*Why others wrong:* Async has RPO lag; none/manual fail RPO.

**H2.** A backup chain uses incrementals; the Tuesday incremental is corrupted/missing. Effect:
A) Only Tuesday lost  B) All restores after Monday fail (chain broken)  C) Full still works alone  D) Differential saves it
**Answer: B) Restores needing Tuesday onward fail** — incremental chain is broken.
*Why others wrong:* Full alone lacks later changes; differential isn't in this chain.

**H3.** Design for PII, in-region, 7-yr audit, ransomware-resistant, RTO 4hr. Most complete:
A) On-site full only  B) Immutable off-site + cross-region replica + tested restore + KMS + retention  C) Async replication only  D) Weekly full, no encryption
**Answer: B)** — covers retention (audit), immutability (ransomware), off-site/replica (RTO/DR), encryption (PII), testing.
*Why others wrong:* A fails DR/ransomware; C fails immutability; D fails compliance/security.

**H4.** Difference between recoverability and integrity:
A) Same thing  B) Recoverability = restore works; Integrity = data complete/uncorrupted  C) Integrity = restore works  D) Both mean encrypted
**Answer: B)** — recoverability is "can we restore," integrity is "is the data intact."
*Why others wrong:* Confusing the two misses that a restore can succeed yet return corrupted data.

**H5.** Best defense so a ransomware attack can't destroy backups:
A) More frequent replication  B) Immutable/WORM + isolated account + MFA-delete protection  C) On-site only  D) Faster schedule
**Answer: B) Immutable/isolated/MFA-delete backups** — replication alone is vulnerable.
*Why others wrong:* More replication still copies encryption; on-site isn't isolated.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Full vs Incremental vs Differential | 🔴 CRITICAL | Restore-chain math is a classic |
| RTO vs RPO distinction | 🔴 CRITICAL | Drives all design choices |
| Replication ≠ backup | 🔴 CRITICAL | Ransomware/corruption trap |
| Testing (recoverability + integrity) | 🟠 HIGH | "Backup succeeded" ≠ safe |
| In-place vs Parallel recovery | 🟠 HIGH | Site-down → parallel |
| Granular vs Bulk recovery | 🟠 HIGH | One file vs whole system |
| On-site vs Off-site / 3-2-1 | 🟠 HIGH | DR survival |
| Retention (compliance) | 🟠 HIGH | Audit/legal mandates |
| Encryption (key separation) | 🟡 MEDIUM | Security of backups |
| Immutable / WORM (ransomware) | 🟡 MEDIUM | Modern must-have |
| Schedule frequency | 🟢 LOW | Implied by RPO |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**Backup types**
- **Full:** everything; restore = full only; slow to make, fast to restore.
- **Incremental:** since last backup ANY; restore = full + ALL incrementals (chain, fragile, small).
- **Differential:** since last FULL; restore = full + latest differential (fewer files, grows in size).

**RTO / RPO**
- RPO = max data loss → drives schedule + replication frequency.
- RTO = max downtime → drives recovery type/location + testing.

**Locations & replication**
- On-site = fast, dies with the building. Off-site = survives disaster (3-2-1: 1 off-site).
- Replication = low RPO/DR, but copies corruption/ransomware → NOT a backup.

**Recovery**
- In-place = original location (system intact). Parallel = new/separate (DR, site down).
- Granular = one item. Bulk = whole system.

**Testing**
- Recoverability = restore actually works (drill). Integrity = data complete/uncorrupted (hash).

**Security**
- Encrypt at rest + in transit; separate key from backup (KMS, least privilege).
- Immutable/WORM/isolated/MFA-delete protects against ransomware.

**Acronyms**
- RTO = Recovery Time Objective · RPO = Recovery Point Objective
- PITR = Point-In-Time Recovery · GFS = Grandfather-Father-Son (retention)
- WORM = Write Once Read Many (immutable) · KMS = Key Management Service
- CMK = Customer-Managed Key · CMEK = Customer-Managed Encryption Key
- DR = Disaster Recovery · PII = Personally Identifiable Information
- 3-2-1 = 3 copies, 2 media, 1 off-site · MFA = Multi-Factor Authentication

**Traps recap**
1. Incremental restore = full + ALL incrementals. 2. Differential restore = full + latest diff. 3. Replication ≠ backup. 4. "Backup succeeded" ≠ recoverable (test it). 5. RPO drives frequency; RTO drives recovery speed. 6. On-site alone fails DR. 7. One file → granular. 8. Site down → parallel. 9. Key with backup = bad. 10. Short retention fails compliance. 11. Differential grows but restores simpler. 12. Integrity ≠ recoverability. 13. Near-zero RPO → sync replication. 14. Ransomware → immutable/isolated backups. 15. Off-site + tested restore = real DR.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **Immutable backups are now baseline:** AWS S3 Object Lock / Backup Vault Lock, Azure Immutable Blob / Backup soft-delete + MFA-delete, GCP Bucket Lock — WORM is the ransomware answer.
- **Continuous data protection (CDP):** Near-real-time replication + PITR (RDS/Cloud SQL/Azure) meet tight RPO without heavy incremental chains.
- **Cross-region backup/replication GA everywhere:** AWS Backup CRR, Azure GRS/ASR, GCP cross-region snapshot copy — standard for DR (parallel recovery).
- **Backup as a ransomware control:** Isolated/air-gapped backup accounts, MFA-delete, and "logically air-gapped" vaults are now exam-relevant best practice.
- **Managed PITR databases:** Aurora/Cloud SQL/Pretty much all managed DBs offer second-level PITR — makes incremental-log backup the default for tight RPO.
- **Backup testing automation:** Scheduled restore-validation jobs (recoverability) + checksum verification (integrity) are built into AWS Backup Audit Manager / Azure Backup reports / GCP.
- **FinOps on retention:** Lifecycle policies tier old backups to archive (Glacier/Archive class) to cut cost while keeping compliance holds — ties 3.3 to 2.5 cost.
- **3-2-1 evolves to 3-2-1-1-0:** add 1 offline/immutable copy and 0 errors in backup verification — the modern hardening of the classic rule.

*End of Objective 3.3 notes.*
