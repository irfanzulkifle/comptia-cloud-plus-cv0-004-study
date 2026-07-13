---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 3.4: Resource Lifecycle"
aliases: [Objective-3.4-Resource-Lifecycle, Domain-3.4-Resource-Lifecycle, Lifecycle, Patches, Updates, Major, Minor, Testing, Ephemeral, Persistent, Decommissioning, End-of-Life, End-of-Support]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 3.4
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-3.4, lifecycle, patches, updates, major, minor, testing, ephemeral, persistent, decommissioning, end-of-life, end-of-support, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 3.0 Operations
## 📘 Objective 3.4: Given a Scenario, Manage the Life Cycle of Cloud Resources

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 3.0 Operations = 17% of total exam
> **Objective 3.4 Focus:** Managing a cloud resource from birth to retirement — **patches** and **updates** (major/minor), **testing** changes before production, handling **data** (ephemeral vs persistent), and **decommissioning** at end-of-life / end-of-support.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 3.4
### Objective Name: *Given a scenario, manage the life cycle of cloud resources.*

**What this objective means (beginner-friendly):**

Every cloud resource — a VM, a database, a container, a load balancer — has a life story:
1. **Born** (provisioned, per 2.5).
2. **Maintained** — you apply **patches** (small security/fix updates) and **updates** (**minor** = small feature/version bumps, **major** = big version jumps that may break things).
3. **Tested** — before you push a change to production, you try it in a test environment so you don't crash everything.
4. **Data handled** — the resource may hold **ephemeral** data (temporary, gone when the resource dies) or **persistent** data (lives on, must be protected/migrated).
5. **Retired** — when it's **end-of-life** (vendor stops selling/supporting) or **end-of-support** (no more security fixes), you **decommission** it safely — backing up data, shutting down, cleaning up, revoking access.

3.4 is the "take care of it the whole time it exists, then put it down properly" objective. It's Operations maturity — not just launching things (2.5) but keeping them healthy and retiring them cleanly.

**Why it matters in the real world:**
- Unpatched systems get breached (the #1 attack vector). Patch management is core security hygiene.
- Major updates can break apps — testing prevents outages.
- Deleting a resource with persistent data = data loss disaster. Lifecycle discipline prevents that.
- End-of-support software is a liability (no security fixes) — you must plan migration before the deadline.

**Why CompTIA tests it:**
- 3.4 is scenario-heavy: "major version upgrade" → test first, plan rollback; "temp compute" → ephemeral data; "vendor dropped support" → decommission/migrate.
- The **ephemeral vs persistent** distinction drives backup and decommissioning decisions.
- It ties to 2.3 (re-architect/retire in migrations), 2.5 (provisioning), 3.1 (monitor patch health), 3.3 (backup before decommission).

---

## SECTION 2 — EXAM KNOWLEDGE

### 3.4.1 — PATCHES

**Definition:** Small, frequent updates that fix bugs or close security vulnerabilities — usually within the same version (e.g., 4.2.1 → 4.2.2).
**Purpose:** Keep systems secure and stable without changing features/behavior.
**How it works:** OS package updates, security patches, hotfixes; often automated via patch management (SSM, Azure Automation, GCP OS patch).
**Considerations:** Still test (a bad patch can break things); schedule (maintenance windows); roll out in waves (canary) to limit blast radius.
**Exam angle:** "Critical CVE released" → patch promptly (after test); "patch everything at once" → risky (wave/canary better).

### 3.4.2 — UPDATES (and versioning)

#### Minor Updates
**Definition:** Small version bumps that add features or fixes but stay backward-compatible (e.g., 4.2 → 4.3).
**Purpose:** Improve functionality without breaking existing behavior.
**Risk:** Low–medium; usually safe but still test.
**When to use:** Routine improvement; low-risk adoption.

#### Major Updates
**Definition:** Big version jumps that may introduce breaking changes (e.g., 4.x → 5.0, or a new DB engine major version).
**Purpose:** Significant new capability, but may break APIs/configs/app compatibility.
**Risk:** HIGH — can break the application; requires planning, testing, and a rollback plan.
**When to use:** When you need the new major features or must move off an end-of-life version; never blindly.
**Exam angle:** "Upgrade to the next major version" → test in staging FIRST, have rollback, communicate downtime.

### 3.4.3 — PATCH vs UPDATE vs MAJOR (the spectrum)

| | Patch | Minor update | Major update |
|---|---|---|---|
| Scope | Bug/security fix | Small feature/fix | Big version jump |
| Compatibility | Same version | Backward-compatible | May break |
| Risk | Low (but test) | Low–medium | High |
| Frequency | Frequent | Periodic | Rare |
| Rollback | Usually easy | Usually easy | Needs plan |
| Exam cue | "CVE fix" | "version 4.2→4.3" | "upgrade to v5" |


---

### 3.4.4 — TESTING

**Definition:** Validating a patch/update/change in a non-production environment before it reaches users.
**Purpose:** Catch breakage, performance regressions, and compatibility issues *before* they hit production (ties to 2.4 IaC/CaC testing, and 3.1/3.3 verification).
**How it works:**
- **Staging / pre-prod:** mirror of production to test the change.
- **Canary:** roll out to a small subset first; watch (3.1) before full rollout.
- **Rollback plan:** documented way to revert if the change fails (images/snapshots/previous version).
- **CI/CD gates:** automated tests must pass before deploy (2.4).
**Considerations:** Skipping testing = outage risk; major updates especially need it.
**Exam angle:** "Major upgrade" → test in staging + canary + rollback; "deploy straight to prod" → wrong.

### 3.4.5 — DATA: EPHEMERAL vs PERSISTENT

#### Ephemeral Data
**Definition:** Temporary data that lives only as long as the resource/instance — lost when the resource stops or is terminated.
**Examples:** RAM, instance-store (temp) disks, container scratch space, /tmp, session cache on a stateless node.
**Lifecycle implication:** No backup needed (by design); safe to destroy the resource. Stateless horizontal scaling (3.2) relies on ephemeral compute.
**Exam angle:** "Temp compute, data can vanish" → ephemeral; "delete the VM, no data loss concern" → ephemeral data.

#### Persistent Data
**Definition:** Data that must survive beyond the resource's lifecycle — stored on durable storage (block volumes, databases, object storage).
**Examples:** Database files, user uploads (object storage), attached block volumes, file shares.
**Lifecycle implication:** MUST be backed up (3.3) before any change/delete; must be migrated (not lost) on decommission; encrypted (2.5).
**Exam angle:** "User data must not be lost on VM termination" → persistent (use durable volume/DB); "decommission the DB server" → migrate/backup the persistent data first.

> **The key split:** Ephemeral = compute/scratch (safe to lose). Persistent = the truth (must protect, migrate, and not destroy on decommission).


---

### 3.4.6 — DECOMMISSIONING

**Definition:** Safely retiring a cloud resource at the end of its useful/ supported life — removing it without losing data, leaking access, or leaving cost/security debt.
**Purpose:** Avoid data loss, avoid orphaned resources (cost + attack surface), and meet compliance (data disposal).

#### End of Life (EOL)
**Definition:** The vendor stops selling, developing, or supporting the product/version entirely. No new features, and eventually no fixes.
**Implication:** Plan migration to a supported version/product before EOL; running EOL software is a growing risk.
**Exam angle:** "Vendor announced EOL for v4 next year" → plan migration now; don't stay on EOL.

#### End of Support (EOS)
**Definition:** The vendor stops providing security patches/support for that version — even if the product exists, *this version* gets no more fixes.
**Implication:** Running past EOS = unpatched vulnerabilities, compliance failure. Must upgrade or migrate before EOS.
**Exam angle:** "Version hits end-of-support next month" → upgrade/migrate before the date; staying = security + compliance risk.

> **EOL vs EOS:** EOL = product/version retired by vendor (no dev at all). EOS = this version no longer gets support/fixes (security gap). Both trigger decommission/migration planning.

#### Safe Decommissioning Steps
1. **Inventory & confirm** the resource is truly unused (monitor 3.1; check dependencies).
2. **Back up persistent data** (3.3) and verify (recoverability + integrity) before any delete.
3. **Migrate** required data/workloads to the replacement (or archive per retention 3.3).
4. **Drain/redirect** traffic (update DNS/LB, remove from scaling group 3.2).
5. **Stop/terminate** the resource in a maintenance window.
6. **Revoke access** — disable IAM roles, keys, service accounts tied to it.
7. **Clean up** — delete attached volumes/snapshots per retention policy, release IPs, remove from IaC (2.4).
8. **Verify** — confirm no lingering cost (billing) or security exposure; document the retirement.

> **Golden rule:** Persistent data is migrated/backed up BEFORE decommission. Ephemeral data needs no rescue. Deleting a resource with persistent data = disaster.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — Patching at Scale (AWS SSM)
- **Patch:** AWS Systems Manager Patch Manager applies OS security patches on a schedule; instances tagged by env.
- **Testing:** Patch a canary group first; CloudWatch (3.1) watches health before fleet-wide.
- **Data:** Stateless web tier (ephemeral) — safe to recycle; DB (persistent) patched in maintenance window with snapshot first (3.3).

### 3.2 — Major DB Engine Upgrade (Azure)
- **Update (major):** PostgreSQL 11 → 15 major version.
- **Testing:** Clone to staging; run app test suite; check extension compatibility.
- **Rollback:** Pre-upgrade snapshot + point-in-time (3.3) if it breaks.
- **Data:** Persistent (the DB) — backed up before; upgrade in-app maintenance window.

### 3.3 — Ephemeral Compute (GCP)
- **Pattern:** Batch workers on ephemeral instances / containers (scratch only).
- **Lifecycle:** Spin up, process, terminate — no persistent data loss (by design).
- **Decommission:** Just stop; nothing to migrate; cost drops to zero.

### 3.4 — End-of-Support Migration
- **Scenario:** App on a runtime hitting EOS in 60 days.
- **Action:** Plan migration to supported runtime; test in staging; cut over via blue-green (2.2); decommission old environment (revoke access, clean IaC).
- **Why:** Past EOS = no security patches = compliance + breach risk.

### 3.5 — Safe Decommission of a Retired Service
- **Scenario:** Legacy API being shut down.
- **Action:** Confirm no traffic (3.1); back up any persistent logs/data (3.3, retention); migrate consumers; drain; terminate; revoke service account; remove Terraform (2.4); verify no orphaned cost.


---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Patch / Update / Major

| | Patch | Minor update | Major update |
|---|---|---|---|
| Scope | Bug/security fix | Small feature/fix | Big version jump |
| Compatibility | Same version | Backward-compatible | May break |
| Risk | Low (test anyway) | Low–medium | High |
| Rollback | Easy | Easy | Needs plan |
| Exam cue | CVE fix | v4.2→4.3 | upgrade to v5 |

### 4.2 — Ephemeral vs Persistent

| | Ephemeral | Persistent |
|---|---|---|
| Lifespan | Dies with resource | Survives resource |
| Examples | RAM, instance-store, /tmp, container scratch | DB, block volume, object storage, file share |
| Backup needed? | No (by design) | Yes (3.3) |
| On decommission | Nothing to rescue | Migrate/backup first |
| Scaling role | Stateless horizontal (3.2) | The stateful truth |

### 4.3 — EOL vs EOS

| | End of Life (EOL) | End of Support (EOS) |
|---|---|---|
| Meaning | Product/version retired by vendor | This version gets no more fixes |
| Dev stops? | Yes | Version unsupported |
| Security patches? | No | No (after EOS date) |
| Action | Migrate to supported product | Upgrade/migrate before EOS |
| Risk if ignored | Growing, unsupported | Unpatched vulns + compliance fail |

### 4.4 — Decommissioning Checklist

| Step | Why |
|---|---|
| Confirm unused (monitor) | Don't kill something live |
| Backup persistent data | No data loss |
| Migrate/archive | Preserve required data |
| Drain traffic | No broken users |
| Stop/terminate | End the resource |
| Revoke access | No orphaned creds (security) |
| Clean up (volumes/IaC) | No orphaned cost/exposure |
| Verify + document | Confirm clean retirement |

### 4.5 — Testing Approaches

| Approach | Use for |
|---|---|
| Staging / pre-prod | Full pre-prod validation |
| Canary | Limit blast radius on rollout |
| Rollback plan | Revert if change fails |
| CI/CD gates | Automated tests before deploy |


---

## SECTION 5 — EXAM TRAPS

**Trap #1 — Major update straight to prod.**
Question: "Upgrade to the next major version; deploy to production immediately."
Correct: **Test in staging + canary + rollback plan first.** Major versions can break apps.
Why wrong: Blind major upgrade = outage risk.

**Trap #2 — Skipping patch testing entirely.**
Question: "Patches are safe; deploy to all at once, no test."
Correct: Patches are low-risk but **still test/canary**; wave rollout limits blast radius.
Why wrong: Even small patches have broken production (e.g., bad kernel patch).

**Trap #3 — Ephemeral = persistent.**
Question: "Terminate the VM; the data on its instance-store survives."
Correct: **No** — instance-store / ephemeral data dies with the instance. Only persistent (block volume/DB/object) survives.
Why wrong: Confusing compute scratch with durable storage.

**Trap #4 — Deleting persistent data on decommission.**
Question: "Shut down the DB server; nothing to save."
Correct: **Wrong** — persistent data must be backed up (3.3) / migrated BEFORE decommission.
Why wrong: Deleting = permanent data loss.

**Trap #5 — EOL vs EOS mix-up.**
Question: "Version hit end-of-support; still gets security patches?"
Correct: **No** — EOS means no more fixes for that version. EOL means the product/version is retired entirely.
Why wrong: Both are unsupported; "still patched" is false for EOS.

**Trap #6 — Staying past EOS/EOL.**
Question: "Keep running EOL software; it works fine."
Correct: **Risk** — no security patches (breach) + compliance failure. Plan migration.
Why wrong: "Works" ignores the security/compliance gap.

**Trap #7 — No rollback plan for major change.**
Question: "Major upgrade; if it fails we'll figure it out."
Correct: Need a **rollback plan** (snapshot/PITR/previous image) before the change.
Why wrong: Without rollback, a failed upgrade = prolonged outage.

**Trap #8 — Orphaned access after decommission.**
Question: "Terminated the server; done."
Correct: Must **revoke IAM/keys/service accounts** — orphaned creds are a security hole + cost.
Why wrong: Leaving credentials = attack surface.

**Trap #9 — Forgetting to clean up IaC/volumes.**
Question: "Deleted the instance; that's the whole resource."
Correct: Also remove attached volumes (per retention), release IPs, delete from IaC (2.4) — else orphaned cost.
Why wrong: Orphaned resources keep billing.

**Trap #10 — Minor update assumed breaking.**
Question: "Minor version bump (4.2→4.3) will break everything; avoid it."
Correct: Minor updates are **backward-compatible**, low risk — but still test.
Why wrong: Over-fearing minors causes version stagnation (then forced major/EOL jump).

**Trap #11 — Patch = feature upgrade.**
Question: "Apply the patch to get the new major features."
Correct: Patches are **fixes/security**, not features. Features come from updates (minor/major).
Why wrong: Patch scope is bug/security, not new capability.

**Trap #12 — No canary / wave on fleet patch.**
Question: "Patch 1000 servers simultaneously."
Correct: **Wave/canary** (patch a subset, watch 3.1, then proceed) limits blast radius.
Why wrong: A bad patch on all = total outage.

**Trap #13 — Decommission without confirming unused.**
Question: "Delete the resource nobody mentioned using."
Correct: **Confirm unused first** (monitor traffic/dependencies) — could be live.
Why wrong: Blind deletion = outage.

**Trap #14 — Persistent data on ephemeral tier.**
Question: "Store the user DB on instance-store for speed."
Correct: **Bad** — instance-store is ephemeral; a reboot/termination loses the DB. Use persistent block/DB.
Why wrong: Durability mismatch.

**Trap #15 — Retention ignored on decommission.**
Question: "Delete everything including logs needed for 7-yr audit."
Correct: Archive per **retention** (3.3) before deleting; compliance may require the data to live on.
Why wrong: Destroying mandated records = compliance violation.


---

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

### PBQ 1 — Major Version Upgrade (test-first)
**Scenario:** Upgrade app runtime 4.x → 5.0 (major). Must avoid outage.
**Walkthrough:**
1. Snapshot/backup the persistent data first (3.3).
2. Clone to **staging**; run full test suite; check compatibility.
3. **Canary** rollout (blue-green, 2.2) to a subset; watch metrics (3.1).
4. Keep **rollback plan** (previous image/snapshot) ready.
5. Full rollout after canary is healthy.
**Bonus Q:** Deploy straight to prod? → No — major = breaking risk.

### PBQ 2 — Critical CVE Patch (wave/canary)
**Scenario:** Critical OS vulnerability; 500 servers.
**Walkthrough:**
1. **Patch** promptly (security) but via **wave/canary** — patch a canary group, watch health (3.1).
2. Roll out in waves; keep rollback (previous AMI) if issues.
3. Verify patch applied (compliance scan).
**Bonus Q:** Patch all at once? → No — blast radius if bad patch.

### PBQ 3 — Ephemeral vs Persistent Placement
**Scenario:** Stateless API + a user database. Where does each live?
**Walkthrough:**
1. API on **ephemeral** compute (autoscale, 3.2) — safe to recycle, no state.
2. DB on **persistent** storage (block volume/managed DB) — survives, backed up (3.3).
3. Never put durable data on instance-store.
**Bonus Q:** Terminate an API node → data loss? → No (ephemeral by design). Terminate DB node → data loss? → Only if not persistent/backed up.

### PBQ 4 — End-of-Support Migration
**Scenario:** Runtime hits EOS in 30 days; compliance requires supported software.
**Walkthrough:**
1. Plan migration to supported version; test in staging.
2. Cut over via blue-green (2.2); keep rollback.
3. Decommission old env: backup logs (retention 3.3), revoke access, clean IaC (2.4), verify no orphaned cost.
**Bonus Q:** Stay past EOS? → No — unpatched + compliance fail.

### PBQ 5 — Safe Decommission
**Scenario:** Retire a legacy service; it has a DB and attached volumes.
**Walkthrough:**
1. Confirm unused (monitor 3.1; check dependencies).
2. **Backup + verify** the persistent DB (3.3 recoverability/integrity).
3. Migrate required data / archive per retention.
4. Drain traffic; terminate instances.
5. Revoke IAM/keys/service account; delete volumes per retention; remove from IaC.
6. Verify no lingering billing/security exposure; document.
**Bonus Q:** Delete first, worry later? → No — persistent data lost; access orphaned.


---

## SECTION 7 — MEMORY AIDS

### 7.1 — The change spectrum
**"Patch = fix. Minor = small add. Major = big jump (may break)."**
- Patch: same version, security/bug.
- Minor: backward-compatible feature bump.
- Major: breaking version jump → test + rollback.

### 7.2 — Ephemeral vs Persistent (the core split)
**"Ephemeral = vanishes with the box. Persistent = outlives the box."**
- Ephemeral: RAM, instance-store, /tmp, container scratch → safe to lose, no backup.
- Persistent: DB, block volume, object store → must back up / migrate, never destroy on decommission.

### 7.3 — EOL vs EOS
**"EOL = vendor retired it. EOS = this version gets no more fixes."**
Both = unsupported; past either, you're exposed. Migrate/upgrade before the date.

### 7.4 — Major change rule
**"Test → Canary → Rollback."**
Never major-upgrade prod blind. Stage it, roll out small, keep a way back.

### 7.5 — Decommission golden rule
**"Persistent first, then kill."**
Backup/migrate persistent data → drain → terminate → revoke access → clean up.

### 7.6 — Decommission checklist (story)
Confirm unused → **Backup persistent** → Migrate/archive → Drain → Stop → **Revoke access** → Clean (volumes/IaC) → Verify.

### 7.7 — Decision tree (ASCII)
```
Given a lifecycle scenario:
│
├─ Security fix released? ───────────► PATCH (test/canary, wave rollout)
├─ Small version bump? ─────────────► MINOR update (still test)
├─ Big version jump (breaking)? ────► MAJOR: stage + canary + rollback
├─ Data lost if instance dies? ─────► PERSISTENT (backup/migrate, never destroy)
├─ Data is scratch only? ───────────► EPHEMERAL (safe to recycle)
├─ Vendor retired the version? ─────► EOL → migrate
├─ Version gets no more fixes? ─────► EOS → upgrade before date
└─ Retiring the resource? ──────────► Decommission: backup persistent → drain → stop → revoke → clean
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| Patching | SSM Patch Manager | Azure Automation Update Mgmt | GCP OS patch management |
| Minor/Major updates | AMIs / launch templates | VM images / extensions | Machine images / MIG |
| Testing (staging) | Separate VPC/account | Separate RG/subscription | Separate project |
| Canary / blue-green | CodeDeploy / ALB | Deployment slots | Traffic split / Cloud Deploy |
| Ephemeral compute | EC2 instance-store, Spot, Lambda | Azure Spot, Functions | Preemptible, Cloud Run |
| Persistent storage | EBS, RDS, S3 | Managed Disks, Azure DB, Blob | Persistent Disk, Cloud SQL, GCS |
| Decommission | Terminate + revoke IAM + delete volume/IaC | Delete + revoke RBAC + remove IaC | Delete + revoke SA + remove TF |
| EOL/EOS tracking | AWS End-of-Support calendar | Azure retirement notices | GCP deprecation policy |
| Backup before decommission | Snapshot / AWS Backup | Azure Backup | Snapshot / Cloud Backup |
| Access revocation | IAM roles/keys | RBAC / service principals | IAM service accounts |

**Cross-cloud constant:** Patch via managed service → test in staging/canary → persistent data on durable storage → backup before decommission → revoke access + clean IaC. The *concept* is identical across providers.


---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What's the difference between a patch and a major update?**
A: A patch is a small security/bug fix within the same version; a major update is a breaking version jump needing testing and rollback.

**Q2: What is ephemeral data?**
A: Temporary data that dies with the resource (RAM, instance-store, /tmp) — safe to lose, no backup needed.

**Q3: What is persistent data?**
A: Data that must survive the resource (DB, block volume, object storage) — must be backed up and migrated, never destroyed on decommission.

### 🎓 Cloud Administrator Questions

**Q4: A critical CVE is announced for your OS. How do you patch 500 servers safely?**
A: Patch promptly but via wave/canary — patch a canary group, watch health (3.1), then roll out in waves with rollback (previous image) ready. Avoid all-at-once blast radius.

**Q5: You're upgrading the DB engine to a new major version. Your process?**
A: Snapshot/backup first (3.3), clone to staging, run compatibility tests, canary/blue-green rollout, keep rollback (PITR) ready, full rollout after healthy.

**Q6: A service is being retired and has a database. Steps?**
A: Confirm unused (monitor), back up + verify the DB (3.3), migrate/archive per retention, drain traffic, terminate, revoke IAM/keys, delete volumes per policy, remove from IaC, verify no orphaned cost, document.

### 🎓 Cloud Support / Pre-Sales Questions

**Q7: Client is on a version hitting end-of-support next month. Advice?**
A: Upgrade/migrate to a supported version before EOS — past EOS means no security patches (breach + compliance risk) and no vendor help. Plan and test the move now.

**Q8: Why not just delete a retired VM and call it done?**
A: You may lose persistent data, leave orphaned credentials (security hole), and keep orphaned volumes/IPs billing (cost). Proper decommission backs up, revokes, and cleans up.

**Q9: Should minor version updates be tested?**
A: Yes — they're low-risk and backward-compatible, but still validate (a bad minor has broken prod). Major updates especially need full staging + canary + rollback.

---

## SECTION 10 — FLASHCARDS

**Patches / Updates**
Q: Small security/bug fix within a version?
A: Patch.

Q: Backward-compatible small version bump?
A: Minor update.

Q: Breaking big version jump?
A: Major update.

Q: Major update to prod directly?
A: No — test + canary + rollback.

Q: Patch all servers at once?
A: No — wave/canary (blast radius).

**Data**
Q: Data dies with the instance?
A: Ephemeral (RAM, instance-store, /tmp).

Q: Data must outlive the instance?
A: Persistent (DB, block volume, object store).

Q: Ephemeral needs backup?
A: No (by design).

Q: Persistent on decommission?
A: Backup/migrate first — never destroy.

**EOL / EOS**
Q: Vendor retires the product/version entirely?
A: End of Life (EOL).

Q: This version gets no more fixes?
A: End of Support (EOS).

Q: Stay past EOS?
A: No — unpatched + compliance fail.

**Testing / Decommission**
Q: Validate change before prod?
A: Testing (staging/canary/rollback).

Q: Limit blast radius on rollout?
A: Canary.

Q: Safe retirement steps?
A: Confirm unused → backup persistent → migrate → drain → stop → revoke access → clean IaC → verify.

Q: Orphaned creds after delete?
A: Revoke IAM/keys/service accounts.

**Provider**
Q: AWS patch service?
A: SSM Patch Manager.

Q: Azure ephemeral compute?
A: Spot VMs / Functions.

Q: GCP persistent storage?
A: Persistent Disk / Cloud SQL / GCS.

Q: Decommission cleanup includes?
A: Revoke access + remove IaC + delete volumes per retention.


---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** A small security fix within the same version is a:
A) Major update  B) Patch  C) Minor update  D) Decommission
**Answer: B) Patch.**
*Why others wrong:* Major/minor are version changes; decommission is retirement.

**E2.** Data on instance-store that vanishes when the VM stops is:
A) Persistent  B) Ephemeral  C) Archived  D) Replicated
**Answer: B) Ephemeral.**
*Why others wrong:* Persistent survives the instance; others unrelated.

**E3.** When a vendor stops providing security fixes for a version, it has reached:
A) EOL  B) EOS  C) Ephemeral  D) Decommissioned
**Answer: B) EOS (End of Support).**
*Why others wrong:* EOL is full retirement; ephemeral/decommission unrelated.

**E4.** Before a major version upgrade to production, you should:
A) Deploy immediately  B) Test in staging + canary + rollback  C) Skip testing  D) Patch only
**Answer: B) Test in staging + canary + rollback.**
*Why others wrong:* Major can break; blind deploy = outage.

**E5.** A backward-compatible small version bump (4.2→4.3) is a:
A) Patch  B) Major  C) Minor  D) EOL
**Answer: C) Minor update.**
*Why others wrong:* Patch is a fix; major breaks; EOL is retirement.

### MEDIUM (10)

**M1.** You terminate an EC2 with a DB on instance-store. Result:
A) DB persists  B) DB lost (ephemeral)  C) Auto-backed up  D) Snapshot created
**Answer: B) DB lost** — instance-store is ephemeral.
*Why others wrong:* Persistent storage (EBS/RDS) would survive; instance-store does not.

**M2.** Retiring a service with a database. First step:
A) Delete the VM  B) Backup + verify persistent data  C) Revoke access  D) Remove IaC
**Answer: B) Backup + verify persistent data first.**
*Why others wrong:* Others come after; deleting first loses data.

**M3.** Critical CVE on 500 servers. Safest rollout:
A) All at once  B) Wave/canary with rollback  C) Ignore  D) Manual only
**Answer: B) Wave/canary with rollback.**
*Why others wrong:* All-at-once = blast radius; ignore = breach; manual doesn't limit scope.

**M4.** Version hits EOL next year. Best action:
A) Keep running  B) Plan migration to supported version now  C) Patch harder  D) Make it ephemeral
**Answer: B) Plan migration now.**
*Why others wrong:* EOL = unsupported; patching won't help; ephemeral unrelated.

**M5.** A stateless API behind an autoscaler should use:
A) Persistent block volume for sessions  B) Ephemeral compute  C) Always a DB  D) Instance-store for the DB
**Answer: B) Ephemeral compute** (safe to recycle, no state).
*Why others wrong:* Stateless = no durable local state; DB on instance-store = data loss.

**M6.** After decommissioning, you find orphaned IAM keys still valid. This is a:
A) Cost saving  B) Security hole  C) Backup  D) Minor issue
**Answer: B) Security hole** — access must be revoked.
*Why others wrong:* Orphaned creds are an attack surface, not a benefit.

**M7.** Major upgrade failed in staging. Action:
A) Deploy to prod anyway  B) Fix / use rollback plan, don't promote  C) Make data ephemeral  D) Skip testing
**Answer: B) Fix or roll back; don't promote a broken change.**
*Why others wrong:* Prod blind = outage; others irrelevant.

**M8.** Logs needed for a 7-year audit are on a retired server. Mistake:
A) Backed them up  B) Deleted without archiving per retention  C) Revoked access  D) Migrated them
**Answer: B) Deleted without archiving per retention** — compliance violation.
*Why others wrong:* The others are correct retirement steps.

**M9.** Minor update assumed to break everything, so avoided for years. Result:
A) Best practice  B) Version stagnation → forced major/EOL jump later  C) More secure  D) Ephemeral
**Answer: B) Version stagnation** → eventually a forced risky major/EOL migration.
*Why others wrong:* Avoiding minors causes bigger future risk.

**M10.** Persistent data lives on:
A) /tmp  B) RAM  C) Managed DB / block volume / object storage  D) Instance-store
**Answer: C) Managed DB / block volume / object storage.**
*Why others wrong:* A/B/D are ephemeral.

### HARD (5)

**H1.** App on runtime hitting EOS in 30 days; compliance mandates supported software; zero-downtime required. Plan:
A) Stay past EOS  B) Test migration in staging → blue-green cutover with rollback → decommission old (backup logs, revoke, clean IaC)  C) Just patch  D) Make it ephemeral
**Answer: B)** — migrate before EOS (compliance/security), test, zero-downtime cutover, clean retirement.
*Why others wrong:* A fails compliance; C won't fix EOS; D irrelevant.

**H2.** You must upgrade a production DB to a new major engine version with no data loss and minimal downtime. Most complete:
A) In-place upgrade on prod now  B) Snapshot/PITR backup → staging test → canary/blue-green → rollback ready → full rollout  C) Delete and recreate  D) Make data ephemeral
**Answer: B)** — backup first, test, staged rollout, rollback.
*Why others wrong:* A risks outage/data loss; C destroys data; D wrong for a DB.

**H3.** A fleet of 2000 servers needs a critical patch. Design for safety + speed:
A) All at once  B) Canary group → watch health (3.1) → automated wave rollout with rollback  C) Manual one-by-one  D) Skip
**Answer: B)** — canary limits blast radius; automated waves scale; rollback safety.
*Why others wrong:* A = outage risk; C = too slow; D = breach.

**H4.** Decommissioning a legacy service that holds user PII in a DB. To avoid compliance + data-loss + security issues:
A) Terminate and forget  B) Confirm unused → backup/verify PII → migrate/archive per retention → drain → stop → revoke access → delete volumes per policy → remove IaC → verify  C) Just revoke access  D) Make PII ephemeral
**Answer: B)** — full safe lifecycle: protect data, revoke, clean.
*Why others wrong:* A loses data + leaks access; C/D incomplete.

**H5.** Distinguish EOL from EOS in action:
A) Same thing  B) EOL = product/version retired (no dev); EOS = version gets no fixes (security gap) — both require migration/upgrade planning  C) EOS = still patched  D) EOL = still sold
**Answer: B)** — EOL is full retirement; EOS is no-more-fixes for that version; both drive migration.
*Why others wrong:* C/D are false; A misses the nuance.


---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Ephemeral vs Persistent | 🔴 CRITICAL | Drives backup + decommission decisions |
| Major update = test + rollback | 🔴 CRITICAL | Breaking-change trap |
| EOL vs EOS | 🔴 CRITICAL | Migration/compliance trigger |
| Safe decommission (persistent first) | 🔴 CRITICAL | Data loss + security hole if skipped |
| Patch vs Minor vs Major | 🟠 HIGH | Scope/risk spectrum |
| Testing (staging/canary/rollback) | 🟠 HIGH | Prevents prod outages |
| Revoke access + clean IaC on retire | 🟠 HIGH | Orphaned creds/cost |
| Retention on decommission | 🟡 MEDIUM | Compliance |
| Wave/canary patching | 🟡 MEDIUM | Blast-radius control |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**Change spectrum**
- **Patch:** small security/bug fix, same version. Low risk but still test/canary.
- **Minor update:** backward-compatible small bump (4.2→4.3). Low–medium risk, test.
- **Major update:** breaking version jump (→5.0). HIGH risk → stage + canary + rollback.

**Data**
- **Ephemeral:** dies with the resource (RAM, instance-store, /tmp, container scratch). No backup; safe to recycle. Stateless scaling (3.2) relies on it.
- **Persistent:** survives (DB, block volume, object store). MUST back up (3.3) + migrate on decommission. Never destroy.

**EOL vs EOS**
- **EOL:** product/version retired by vendor (no dev). 
- **EOS:** this version gets no more fixes (security gap).
- Both → plan migration/upgrade before the date. Past either = breach + compliance risk.

**Testing**
- Staging/pre-prod, canary (limit blast radius), rollback plan, CI/CD gates (2.4). Never major-upgrade prod blind.

**Decommission (golden order)**
1. Confirm unused (monitor). 2. Backup + verify persistent data (3.3). 3. Migrate/archive per retention. 4. Drain traffic. 5. Stop/terminate. 6. Revoke access (IAM/keys/SA). 7. Clean (volumes/IaC). 8. Verify + document.

**Acronyms**
- EOL = End of Life · EOS = End of Support
- CVE = Common Vulnerabilities and Exposures
- OS = Operating System · DB = Database
- IaC = Infrastructure as Code · CI/CD = Continuous Integration/Deployment
- SSM = Systems Manager (AWS) · RBAC = Role-Based Access Control
- SA = Service Account · VM = Virtual Machine · API = Application Programming Interface
- PITR = Point-In-Time Recovery

**Traps recap**
1. Major → test + canary + rollback (not blind prod). 2. Even patches: wave/canary. 3. Ephemeral dies with instance. 4. Persistent must be backed up/migrated before decommission. 5. EOL = retired; EOS = no more fixes. 6. Staying past EOS/EOL = risk. 7. Major needs rollback plan. 8. Revoke access on retire. 9. Clean volumes/IaC (orphan cost). 10. Minor ≠ breaking. 11. Patch ≠ feature. 12. Canary limits blast radius. 13. Confirm unused before delete. 14. Don't put DB on instance-store. 15. Retention on decommission (audit).

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **Managed patch services matured:** AWS SSM Patch Manager (patch baselines, maintenance windows), Azure Automation Update Management, GCP OS patch management — automated, tag-driven fleet patching is standard.
- **Canary / progressive delivery is default:** AWS CodeDeploy + ALB weighted targets, Azure Deployment Slots, GCP Cloud Deploy traffic splitting — major changes roll out gradually with automatic rollback on health failure (ties to 3.1).
- **Immutable / ephemeral compute rise:** Spot/Preemptible/Cloud Run + containers mean most compute is ephemeral by design — persistent state pushed to managed DB/object storage (the 3.4 split made explicit).
- **EOL/EOS automation:** Cloud providers now surface deprecation calendars and "end-of-support" banners (AWS Health, Azure Advisor, GCP deprecation policy) — lifecycle tracking is proactive, not manual.
- **Zero-downtime major upgrades:** Blue-green + read-replica promotion for DBs (Aurora blue/green, Cloud SQL clone-promote) make major version moves safe — but still require pre-upgrade backup + test.
- **Decommission hygiene tooling:** IaC drift detection (2.4) + orphaned-resource scanners (cost + security) flag resources to retire; automated access revocation on delete.
- **Compliance-driven retention:** Immutable/locked retention (WORM, 3.3) now mandatory in many regulated industries — decommission must archive before destroy.
- **SBOM + vulnerability management:** Patching is increasingly tied to a software bill of materials and continuous CVE scanning (Domain 4.1) — 3.4 patches feed 4.x security.

*End of Objective 3.4 notes.*
