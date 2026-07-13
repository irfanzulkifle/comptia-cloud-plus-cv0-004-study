# Objective 3.3 — Backup and Recovery

## SECTION 1 — Exam Objectives

- **Objective 3.3:** Use appropriate backup and recovery methods given a scenario.
- **Backup types:** Full, Incremental, Differential.
- **Backup locations:** On site (fast restore), Off site (disaster protection).
- **Schedule:** How often / when backups run (drives RPO).
- **Retention:** How long copies are kept (compliance / recovery history).
- **Replication:** Continuous copy to another location (geo-redundancy).
- **Encryption:** Protect backups at rest and in transit (KMS / TLS).
- **Testing:** Prove backups work — Recoverability + Integrity.
- **Recovery types:** In-place, Parallel.
- **Recovery options:** Bulk, Granular.
- **Key levers:** RPO (max data loss) and RTO (max downtime) drive every choice.
- **Mnemonic:** Type, Place, Plan (schedule+retention), Protect (encryption+replication), Prove (testing), Restore (type+option).

## SECTION 2 — EXAM KNOWLEDGE

### Backup Types — Full
- **Definition:** Copies every selected file/block, regardless of changes since the last backup.
- **Why it matters:** Simplest and most self-contained — fastest/simplest to restore, no dependency chain.
- **Analogy:** Like photocopying an entire book start to finish; you never need the earlier pages.
- **Example:** Weekly full volume snapshot or full DB dump; restore needs only that one set.

### Backup Types — Incremental
- **Definition:** Copies only data changed since the last backup of ANY type (full or incremental).
- **Why it matters:** Fastest to run and smallest in storage, but restore needs full + every incremental in the chain.
- **Analogy:** Like saving only edited pages since your last save — quick to write, but you need every draft to rebuild.
- **Example:** Full weekly + incremental hourly; one missing link breaks all later restores.

### Backup Types — Differential
- **Definition:** Copies all data changed since the last FULL backup (resets only with a new full).
- **Why it matters:** Larger/slower to back up than incremental, but restore needs only full + latest differential.
- **Analogy:** Like re-saving all edits since the last complete book copy — fewer pieces than incrementals.
- **Example:** Full weekly + differential daily; restores need at most two pieces.

### Backup Locations — On Site and Off Site
- **Definition:** On site = same facility/region as primary; Off site = separate region/account/vault.
- **Why it matters:** On site = fast restore, weak against site loss; Off site = disaster protection, slower / may cost egress.
- **Analogy:** On site is a safe in your house; off site is a safe in another city.
- **Example:** Regional outage → off site (cross-region); fastest file restore → on site.

### Schedule, Retention, Replication
- **Definition:** Schedule = frequency/timing; Retention = how long/many copies; Replication = continuous copy elsewhere.
- **Why it matters:** Schedule drives RPO; retention enables older-point recovery / compliance; replication gives geo-redundancy / RPO≈0.
- **Analogy:** Schedule = how often you save; retention = how many old saves you keep; replication = saving to a second computer.
- **Example:** Daily 7, weekly 4, monthly 12; cross-region async copy for DR.

### Encryption and Testing (Recoverability + Integrity)
- **Definition:** Encryption = protect at rest (KMS/AES-256) and in transit (TLS); Testing = prove backups work.
- **Why it matters:** Losing the key makes encrypted backups useless; an untested backup is not a backup.
- **Analogy:** Encryption locks the safe; testing is opening it to confirm the key works.
- **Example:** KMS-encrypted backup with lost key = worthless; nightly test restores + checksums prove recoverability/integrity.

### Recovery Types and Options
- **Definition:** Types = In-place (original location) or Parallel (separate env); Options = Bulk (whole system) or Granular (single item).
- **Why it matters:** In-place is simple but can clobber prod; parallel validates first; granular minimizes scope / downtime.
- **Analogy:** In-place = repainting the wall in place; parallel = painting a copy first; granular = touching up one spot.
- **Example:** One deleted file → granular; datacenter loss → bulk; validate before cutover → parallel.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Ransomware defense with off-site immutable backups:** A company replicates encrypted, immutable snapshots to a separate AWS account/region daily (off-site + encryption) and keeps 90 days retention. When ransomware encrypts local files, they restore from the clean off-site copy — the on-site, mutable backup was also encrypted by the attacker, proving why off-site immutability matters.
- **Tight RPO for a transactional DB:** Full backup weekly + incremental every 15 minutes (incremental chain for small/fast backups, tight RPO). Monthly a synthetic full resets the chain to limit restore time. Test restores run nightly to confirm recoverability.
- **Accidental file deletion (granular):** A user overwrites a critical spreadsheet. Instead of restoring the whole file server (bulk, disruptive), the team uses item-level granular recovery to pull just that file from last night's backup in minutes.
- **Regional disaster (parallel DR):** A primary region fails. The organization restores the latest backup to a parallel environment in the DR region (parallel recovery), validates the app, then cuts traffic over — avoiding in-place clobbering of uncertain data.
- **Compliance long-term retention:** Financial records are backed up full monthly, retained 7 years in cold off-site storage, encrypted with KMS, with integrity checksums verified quarterly — satisfying audit requirements and proving integrity.

## SECTION 4 — COMPARISON TABLES

| Backup type | Backs up | Backup speed/size | Restore needs | Restore speed |
|---|---|---|---|---|
| Full | Everything | Slow / Largest | The full only | Fastest / simplest |
| Incremental | Since last backup (any) | Fastest / Smallest | Full + all incrementals (chain) | Slowest / fragile |
| Differential | Since last full | Medium / Grows | Full + latest differential | Medium / robust |

| Location | Restore speed | Disaster protection | Cost | Use |
|---|---|---|---|---|
| On site | Fast | Weak (site loss) | Low | Operational recovery |
| Off site | Slower | Strong (region/disaster) | Higher (egress) | DR / 3-2-1 |

| Recovery type | Restores to | Risk | Best for |
|---|---|---|---|
| In-place | Original location | Clobbers prod if bad | Simple restore |
| Parallel | Separate env | Low (validate first) | Safe cutover / DR |

| Recovery option | Scope | Downtime | Example |
|---|---|---|---|
| Bulk | Entire system/volume/DB | High if full | Datacenter loss |
| Granular | Single file/row/item | Low | Accidental deletion |

| Concept | Drives | Exam keyword |
|---|---|---|
| Schedule | RPO (data loss) | how often |
| Retention | History / compliance | how long |
| Replication | Geo-redundancy / RPO≈0 | cross-region |
| Encryption | Confidentiality / compliance | KMS, at rest |
| Testing | Trust / recoverability | test restore |

## SECTION 5 — AWS PROVIDER MAPPING

AWS-ONLY mapping for Objective 3.3 (backup and recovery):

- **AWS Backup** — the centralized, policy-driven backup service covering schedule, retention, encryption, and cross-region/off-site replication. You define a **backup plan** with a **cron schedule** (frequency/RPO), a **lifecycle/retention** rule (transition to cold storage and expire), and optional **cross-region / cross-account copy** (off-site replication for DR). AWS Backup supports full and incremental (snapshot-based, block-level deltas) backups for EC2, EBS, RDS, DynamoDB, EFS, and more, with **encryption** handled by KMS. It also provides **backup vault lock** (immutability/WORM) to defend against ransomware and **backup audit/restore testing** reporting to prove recoverability and integrity. This single service maps to Schedule, Retention, Replication, Encryption, and Testing sub-objectives.

- **Snapshots (EBS / RDS / storage snapshots)** — point-in-time **full or incremental** backups. EBS snapshots are incremental at the block level (only changed blocks stored) and can be copied to another region for off-site/DR. They are the mechanism behind most AWS Backup actions and support both bulk (whole volume) and, with file-level tools, granular restore. Snapshots embody the Backup Types and Locations sub-objectives.

- **RDS replication** — provides continuous data protection and recovery options: **Multi-AZ synchronous replication** (automatic failover, near-zero RPO, in-place recovery of the DB), **Read Replicas** (async cross-region copy for off-site + reporting), and **automated backups / PITR** (point-in-time recovery, bulk restore to any second within retention). This maps to Replication, Retention, and Recovery types/options.

Together: AWS Backup = policy/schedule/retention/encryption/replication/testing; Snapshots = incremental/full/off-site; RDS replication = continuous DR recovery.

## SECTION 6 — PRACTICE QUESTIONS

1. Which backup type restores fastest and has no dependency chain?
   - **A.** Incremental
   - **B.** Differential
   - **C.** Full
   - **D.** Synthetic

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** A full backup alone contains everything, so restore needs just that one self-contained set with no chain.

**Why A is wrong:** Incremental restore needs the full plus every link in the chain — slow and fragile.
**Why B is wrong:** Differential still needs the full plus the latest differential, not a single set.
**Why D is wrong:** "Synthetic full" is a backup technique, not a distinct restore-simplest type here.

</details>

2. An incremental backup copies data changed since:
   - **A.** The last full only
   - **B.** The last backup of any type
   - **C.** The last differential only
   - **D.** Seven days ago

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Incremental backs up only what changed since the most recent backup (full or incremental), making each run small.

**Why A is wrong:** "Since last full only" describes a differential, not an incremental.
**Why C is wrong:** It is not limited to the last differential; any prior backup resets the baseline.
**Why D is wrong:** A fixed 7-day window ignores the actual prior backup and is not how incrementals work.

</details>

3. A differential backup grows because it copies changes since:
   - **A.** The last incremental
   - **B.** The last full
   - **C.** Yesterday only
   - **D.** The last differential

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Differential re-copies everything changed since the last full and only resets when a new full is taken, so it grows over the week.

**Why A is wrong:** It is not measured against the last incremental.
**Why C is wrong:** "Yesterday only" is arbitrary and not the differential definition.
**Why D is wrong:** It does not reset at each differential; only a new full resets it.

</details>

4. To survive a regional natural disaster, backups should be:
   - **A.** On site only
   - **B.** Off site (cross-region/account)
   - **C.** On the same volume
   - **D.** Unencrypted

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Off-site (cross-region/account) copies survive a site/region loss that would destroy on-site backups.

**Why A is wrong:** On-site backups are wiped by the same regional disaster.
**Why C is wrong:** Same volume fails with the primary — no disaster protection at all.
**Why D is wrong:** Encryption protects confidentiality, not geographic survival.

</details>

5. The fastest restore of a single accidentally deleted file uses:
   - **A.** Bulk recovery
   - **B.** Granular recovery
   - **C.** In-place only
   - **D.** Full rebuild

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Granular (item-level) recovery pulls just the one file without restoring the whole system, minimizing scope and downtime.

**Why A is wrong:** Bulk restores the entire system/volume — slow and disruptive for one file.
**Why C is wrong:** In-place is a recovery type (location), not a scope option like granular.
**Why D is wrong:** A full rebuild is the opposite of fast, targeted restore.

</details>

6. Restoring to a separate environment to validate before cutover is:
   - **A.** In-place recovery
   - **B.** Parallel recovery
   - **C.** Bulk recovery
   - **D.** Differential

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Parallel recovery restores to a separate environment so you validate before cutting traffic over, avoiding clobbering prod.

**Why A is wrong:** In-place restores onto the original location, risking bad-data overwrite.
**Why C is wrong:** Bulk is about scope (whole system), not the separate environment.
**Why D is wrong:** Differential is a backup type, not a recovery type.

</details>

7. RPO is primarily controlled by:
   - **A.** Retention length
   - **B.** Backup schedule/frequency
   - **C.** Encryption
   - **D.** Recovery type

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** RPO (max tolerable data loss) is driven by how often you back up — more frequent backups = smaller potential loss.

**Why A is wrong:** Retention governs how far back you can recover, not how much you lose between backups.
**Why C is wrong:** Encryption protects data confidentiality, not data-loss window.
**Why D is wrong:** Recovery type (in-place/parallel) affects cutover safety, not RPO.

</details>

8. A backup encrypted with KMS is useless if you:
   - **A.** Replicate it
   - **B.** Lose the encryption key
   - **C.** Test it
   - **D.** Retain it longer

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Without the KMS key, the ciphertext cannot be decrypted, so the backup cannot be restored — losing the key loses the data.

**Why A is wrong:** Replication copies the data; it does not by itself make it unrecoverable.
**Why C is wrong:** Testing proves recoverability; it does not destroy the backup.
**Why D is wrong:** Longer retention keeps the backup longer; it does not invalidate the key.

</details>

9. Which practice proves a backup actually works?
   - **A.** Encryption
   - **B.** Testing / recoverability (test restores)
   - **C.** Replication
   - **D.** Retention

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Only performing test restores (and verifying the result) confirms the backup is recoverable — "an untested backup is not a backup."

**Why A is wrong:** Encryption protects data but says nothing about whether it restores.
**Why C is wrong:** Replication copies data; a replicated bad backup is still bad.
**Why D is wrong:** Retention keeps copies; it does not validate they are usable.

</details>

10. Integrity verification of a backup uses:
   - **A.** Cooldowns
   - **B.** Checksums / hashes
   - **C.** Load balancers
   - **D.** Schedules

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Checksums/hashes prove the backup is complete and unaltered since capture, confirming integrity.

**Why A is wrong:** Cooldowns are an Auto Scaling concept, unrelated to backup integrity.
**Why C is wrong:** Load balancers distribute traffic, not verify data integrity.
**Why D is wrong:** Schedules control timing, not integrity verification.

</details>

11. Synchronous replication is best described as:
   - **A.** High lag, cross-region
   - **B.** Near-zero data loss, latency-bound
   - **C.** Backup only nightly
   - **D.** Always off-site

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Sync replication waits for the remote write to confirm, giving ~zero data loss but adding latency (distance-bound).

**Why A is wrong:** Sync has low lag for data loss, not high lag; high lag describes async.
**Why C is wrong:** Synchronous replication is continuous, not nightly batch.
**Why D is wrong:** It can be same-region (Multi-AZ) or cross-region; "always off-site" is false.

</details>

12. The 3-2-1 rule's "1 off-site" protects against:
   - **A.** Ransomware keys
   - **B.** Site-level disaster
   - **C.** Slow restores
   - **D.** Encryption cost

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** One off-site copy survives a localized catastrophe (fire, flood, region outage) that destroys on-site media.

**Why A is wrong:** Off-site alone does not defeat ransomware encryption of the copy unless it is also immutable.
**Why C is wrong:** Off-site copies are often slower to restore, not a protection against slowness.
**Why D is wrong:** Encryption cost is unrelated to the off-site copy's disaster purpose.

</details>

13. Storing backups for 7 years for audits is governed by:
   - **A.** Schedule
   - **B.** Retention
   - **C.** Replication
   - **D.** Granular recovery

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Retention defines how long copies are kept, which is exactly the compliance/history window (7 years) question.

**Why A is wrong:** Schedule governs frequency/RPO, not how long you keep copies.
**Why C is wrong:** Replication is about location/geo-redundancy, not duration.
**Why D is wrong:** Granular recovery is a restore scope, not a retention duration.

</details>

14. On AWS, a centralized policy with cron schedule + lifecycle is:
   - **A.** Snapshot only
   - **B.** AWS Backup plan
   - **C.** RDS replica
   - **D.** KMS key

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** An AWS Backup plan defines the cron schedule, retention/lifecycle, encryption, and cross-region copy in one policy.

**Why A is wrong:** A snapshot is a single point-in-time artifact, not a scheduled policy framework.
**Why C is wrong:** An RDS replica is continuous replication, not a scheduled backup plan.
**Why D is wrong:** A KMS key encrypts; it does not schedule or retain backups.

</details>

15. EBS snapshots are incremental at the:
   - **A.** File level only
   - **B.** Block level
   - **C.** Region level
   - **D.** User level

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** EBS snapshots store only changed blocks since the prior snapshot — block-level incremental, efficient in size.

**Why A is wrong:** EBS snapshots are block-level, not file-level (that is file-based backup tools).
**Why C is wrong:** "Region level" is meaningless for how incrementals are computed.
**Why D is wrong:** Incremental behavior is not per-user; it is per-volume block changes.

</details>

16. RDS Multi-AZ provides which recovery characteristic?
   - **A.** Manual only
   - **B.** Synchronous failover, near-zero RPO
   - **C.** Off-site only
   - **D.** Granular file restore

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Multi-AZ uses synchronous standby replication enabling automatic failover with near-zero RPO and in-place recovery.

**Why A is wrong:** Failover is automatic, not manual-only.
**Why C is wrong:** Multi-AZ is same-region (different AZ), not off-site; cross-region is Read Replicas.
**Why D is wrong:** Multi-AZ is whole-DB failover, not item-level file restore.

</details>

17. Immutability / vault lock defends backups against:
   - **A.** Slow queries
   - **B.** Ransomware tampering
   - **C.** Encryption
   - **D.** Schedule drift

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** WORM/immutable vault lock prevents deletion or alteration, so attackers cannot encrypt or delete the clean backup.

**Why A is wrong:** Immutability does not affect query speed.
**Why C is wrong:** Encryption is a separate control; immutability is about write-protection.
**Why D is wrong:** Schedule drift is an operational issue, not what immutability addresses.

</details>

18. Bulk recovery is most appropriate for:
   - **A.** One deleted email
   - **B.** Entire datacenter loss
   - **C.** A single row
   - **D.** A config typo

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Bulk recovery restores whole systems/volumes/DBs, which is what a datacenter loss scenario requires.

**Why A is wrong:** One deleted email is a granular/item-level restore.
**Why C is wrong:** A single row is granular recovery, not bulk.
**Why D is wrong:** A config typo is a tiny change, better fixed granularly or via IaC, not a bulk restore.

</details>

19. A long incremental chain is risky because:
   - **A.** Backups are too big
   - **B.** One corrupt link breaks later restores
   - **C.** Restore is too fast
   - **D.** It needs a full every hour

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Restore needs every link in the incremental chain, so any single corrupt/missing link invalidates all later restores.

**Why A is wrong:** Incrementals are the smallest/fastest backups, not too big.
**Why C is wrong:** Restore is actually the slowest/fragile with long chains, not too fast.
**Why D is wrong:** You do not need a full every hour; the risk is chain dependency, not frequency.

</details>

20. Restoring in-place is riskier than parallel because it can:
   - **A.** Cost less
   - **B.** Clobber production data if the restore is bad
   - **C.** Encrypt the backup
   - **D.** Skip validation

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** In-place overlays live data with no isolation, so a bad restore can overwrite and destroy production.

**Why A is wrong:** Cost is not the risk differentiator; clobbering data is.
**Why C is wrong:** Restore does not encrypt the backup; that is a separate control.
**Why D is wrong:** Both can skip validation, but the danger unique to in-place is overwriting prod.

</details>
