# Objective3.4 — Resource Lifecycle

## SECTION 1 — Exam Objectives

- **Objective3.4:** *Given a scenario, manage the life cycle of cloud resources.* This is the operations-side objective: keeping resources healthy from the moment they are deployed until the moment they are retired. The exam does NOT test vulnerability scanning or CVE assessment here — that belongs to Objective 4.1. Instead, 3.4 is about the routine and planned work of maintaining systems.
- **Patches** — small, targeted fixes applied to keep software on its current version.
- **Updates** — changes to software versions:
  - **Major** updates (big, breaking, infrequent).
  - **Minor** updates (smaller, backward-compatible, more frequent).
- **Testing** — validating changes in staging before production, with rollback plans.
- **Data** — classifying and placing data correctly:
  - **Ephemeral** (temporary, lost when the resource stops).
  - **Persistent** (durable, survives stops and termination).
- **Decommissioning** — retiring resources safely:
  - **End of life (EOL)** — vendor stops developing/selling.
  - **End of support (EOS)** — vendor stops patching and supporting.
- **Why it matters:** The exam is scenario-based: you are given a situation (an OS needs a fix, a version is EOL, a workload writes temp files) and must pick the correct lifecycle action. Mastery of patches, updates, testing, data handling, and decommissioning is what closes the resource lifecycle.
- **How CompTIA tests it:** Recognize patch vs. minor vs. major, staging/canary/rollback as testing, ephemeral vs. persistent placement, and EOL vs. EOS decommissioning triggers — then choose the right lifecycle action for the given scenario.

---

## SECTION 2 — EXAM KNOWLEDGE

### 3.4.1 — Patches

- **Definition:** A patch is a small, targeted software fix released by a vendor to correct a specific bug, security flaw, or performance problem in an existing version of an operating system or application. Patches (sometimes called hotfixes or service packs) do not change the product's major or minor version number — they keep you on the same release while repairing what is broken.
- **Why it matters:** In the cloud, dozens or hundreds of instances often run the same base image. An unpatched instance is a known weakness waiting to be exploited, and under the shared-responsibility model the customer is responsible for patching the guest OS and applications. Cloud+ tests that you understand patches are routine, low-risk, frequent changes that should be automated and scheduled, not applied by hand one server at a time.
- **Analogy:** A patch is like replacing a worn washer in your tap — the tap (the running system) stays the same model, you just stop the leak. You do not buy a new tap; you fix the small fault.
- **Example:** A Linux web-server fleet receives a kernel security patch. Using a configuration-management tool (or AWS SSM Patch Manager), you stage the patch on a test instance, confirm the site still loads, then roll it out to the rest of the autoscaling group during a maintenance window. No version change, minimal downtime.

### 3.4.2 — Updates — Major

- **Definition:** A major update (major version) introduces significant new features, architectural changes, or breaking changes that may require data migration, retraining, or re-architecture. The first digit of the version number jumps (for example v2 to v3, or MySQL 5.7 to 8.0). Major updates are infrequent and carry the highest risk of any update type.
- **Why it matters:** Cloud+ expects you to recognize that major updates are planned, project-level events with rollback plans, compatibility testing, and stakeholder sign-off. They can change APIs, remove deprecated features, and invalidate old configurations. Skipping too many majors eventually strands you on unsupported software that cannot be patched.
- **Analogy:** A major update is like renovating your house — new rooms, moved walls, and maybe you have to live elsewhere during the work. You would not do it without blueprints and a builder.
- **Example:** Migrating a monolithic application from Java 8 to Java 21: you must recompile, replace removed APIs, test the build pipeline, and run a parallel environment before cutting over. Rollback means redeploying the previous AMI.

### 3.4.3 — Updates — Minor

- **Definition:** A minor update (minor version) adds backward-compatible features and smaller improvements within the same major version. The middle digit changes (for example v2.3 to v2.4, or PostgreSQL 14.4 to 14.6). Minor updates are more frequent than majors and usually low-risk, but they still warrant testing before production.
- **Why it matters:** Minor updates deliver new capabilities and bug fixes without breaking existing workflows. Cloud+ tests that you schedule them in staging first, then production, and that you track which minor version each environment runs so support and security stay consistent across the fleet.
- **Analogy:** A minor update is like a phone getting a new camera mode in the same model year — same phone, new trick, nothing breaks.
- **Example:** Upgrading a Kubernetes cluster from 1.28 to 1.29: new beta APIs appear, deprecations are flagged (not removed), and node pools are drained and recycled. You verify workloads reschedule before promoting to production.

### 3.4.4 — Testing

- **Definition:** Testing is the practice of validating that a patch, update, or new deployment works correctly BEFORE it reaches production users. It includes unit, integration, and user-acceptance testing, plus staging and QA environments that mirror production as closely as possible.
- **Why it matters:** The cloud makes it cheap to clone a production-like environment, so there is little excuse to test on live users. Cloud+ emphasizes staged rollouts, canary deployments, and having a tested rollback path. A failed update that was not tested can cause outages and data corruption that are expensive to reverse.
- **Analogy:** Testing is tasting the soup before serving the banquet — you check the seasoning on a small bowl, not after all the guests are seated.
- **Example:** Before applying a database minor update, you restore last night's backup into a staging instance, run the upgrade, execute a test query suite, and confirm replication. Only then do you schedule the production maintenance window with a rollback snapshot ready.

### 3.4.5 — Data — Ephemeral

- **Definition:** Ephemeral (temporary) data exists only for the life of a process or instance and is lost when that resource stops. Examples include instance-store volumes, the /tmp directory, swap space, session caches, and scratch space. It is typically fast but non-durable.
- **Why it matters:** Cloud+ tests that you never place anything you cannot afford to lose on ephemeral storage. Ephemeral storage is ideal for buffers, temp files, and high-speed caches, but if the instance terminates, the data is gone. Mistaking ephemeral for persistent storage is a classic exam trap and a real-world cause of data loss.
- **Analogy:** Ephemeral storage is like a whiteboard — great for quick notes during a meeting, wiped clean when you leave the room. You would not archive the company books on it.
- **Example:** A batch-rendering job writes intermediate frames to instance store for speed, then uploads the final output to object storage. If the instance crashes, only recomputed temp frames are lost, not the source or the results.

### 3.4.6 — Data — Persistent

- **Definition:** Persistent data survives reboots, stops, and even instance termination because it lives on durable storage independent of any single compute resource — block volumes (such as EBS), file systems (such as EFS or NFS), object storage, and databases.
- **Why it matters:** Persistent data is your business records, user files, and databases. Cloud+ expects you to choose the right persistent tier (block for disks, file for shared access, object for backups and archives) and to protect it with snapshots, replication, and lifecycle policies. Losing persistent data is usually unrecoverable without a backup.
- **Analogy:** Persistent storage is like a bank safety-deposit box — it stays put and intact no matter how many times you come and go.
- **Example:** A web application stores user uploads on object storage and its records in a managed database with automated backups. Even if every web server is replaced, the data remains available.

### 3.4.7 — Decommissioning — End of Life (EOL)

- **Definition:** End of life is the vendor's announced date after which a product or version is no longer sold or actively developed — no new features and often no further support. EOL is the "we have stopped making this" milestone on the vendor's roadmap.
- **Why it matters:** Running software past EOL means you are on an unmaintained product; you cannot get new fixes or guidance. Cloud+ tests that you track EOL dates in your asset inventory and plan migrations before that date so you are not forced into a rushed, risky upgrade at the last minute.
- **Analogy:** EOL is like a car model being discontinued — the dealer stops selling it and eventually stops stocking parts.
- **Example:** A company still running an EOL Linux distribution plans a six-month migration to the current long-term-support release, building new images and moving workloads before the old OS loses all support.

### 3.4.8 — Decommissioning — End of Support (EOS)

- **Definition:** End of support is the date after which the vendor provides no more security patches, bug fixes, or technical support — even for an existing install. After EOS, known vulnerabilities go unpatched. EOS usually follows EOL by some period of time.
- **Why it matters:** EOS is the hard stop: beyond it, staying on the version is a security and compliance risk. Cloud+ expects you to retire (decommission) resources running past EOS, archive their data to durable storage, revoke access, and confirm that billing stops so you are not paying for dead resources.
- **Analogy:** EOS is like the warranty and repair service expiring — after that day, if it breaks, you are on your own and no one will fix it.
- **Example:** After a database reaches EOS, the team snapshots its data to object storage for archive, terminates the instance, deletes the associated volumes (after confirmation), and removes the IAM role so no one can accidentally restart an unsupported system.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

1. **Monthly patch Tuesday for a web fleet:** A company runs 40 web servers from one golden image. Each month a Linux security patch is released. The cloud operations team uses a patch manager to scan the fleet, apply the patch to two canary instances in staging, verify the health checks pass and response times are normal, then roll the patch across the production autoscaling group in batches during a low-traffic window. No version change occurs, and the fleet stays consistent.
2. **Minor version bump of a managed database:** An application uses PostgreSQL 14.4. The vendor releases 14.6 with bug fixes. The team restores a recent backup into a staging database, applies the minor upgrade, runs the application's integration test suite, and confirms replication health. They then schedule the production upgrade during a maintenance window with a pre-upgrade snapshot so they can roll back instantly if queries regress.
3. **Major version migration of an application runtime:** A legacy service runs on Java 8. A new feature requires Java 21. Because this is a major update with removed APIs, the team treats it as a project: they branch the code, replace deprecated calls, rebuild the CI pipeline, deploy to a parallel staging environment, run user-acceptance testing, then cut over by launching new instances from a new image while keeping the old instances available for immediate rollback.
4. **Decommissioning an end-of-support system:** A file server VM reaches end of support. The team first copies all business files to durable object storage and verifies checksums. They then revoke the server's access keys, take a final archive snapshot, terminate the instance, delete its now-unneeded volumes, and remove it from the asset inventory and billing reports so the company stops paying for a system it can no longer safely use.

## SECTION 4 — COMPARISON TABLES

**Table 1 — Patch vs Minor Update vs Major Update**

| Aspect | Patch | Minor Update | Major Update |
| --- | --- | --- | --- |
| Version change | None (same version) | Middle digit (2.3 to 2.4) | First digit (2 to 3) |
| Size of change | Tiny, targeted fix | New compatible features | Big, breaking changes |
| Frequency | Frequent | Moderate | Infrequent |
| Risk | Low | Low to moderate | High |
| Rollback | Usually simple | Snapshot-based | Parallel env + old image |

**Table 2 — Ephemeral vs Persistent Data**

| Aspect | Ephemeral Data | Persistent Data |
| --- | --- | --- |
| Lifespan | Dies with the instance/process | Survives stops and termination |
| Examples | Instance store, /tmp, swap, cache | EBS, EFS, object storage, databases |
| Durability | Non-durable | Durable |
| Best use | Scratch, buffers, temp files | Business records, user files |
| Loss on crash | Expected, acceptable | Must be prevented/recoverable |

**Table 3 — End of Life vs End of Support**

| Aspect | End of Life (EOL) | End of Support (EOS) |
| --- | --- | --- |
| Meaning | Vendor stops developing/selling | Vendor stops patching/helping |
| New features after date | No | No |
| Security patches after date | Usually no | Definitely no |
| Typical order | Comes first | Follows EOL |
| Action | Plan migration | Retire/decommission resource |

**Table 4 — Testing Stages**

| Stage | Purpose | Risk if skipped |
| --- | --- | --- |
| Dev/Unit | Verify individual code works | Broken logic reaches later stages |
| Staging/QA | Mirror production, full test | Surprises in production |
| Canary | Small live slice exposed | Full outage on bad change |
| Rollback test | Confirm you can revert | Stuck on a failing version |

**Table 5 — Storage Tier Selection**

| Need | Recommended Tier | Reason |
| --- | --- | --- |
| OS/app disk on a VM | Block (EBS) | Persistent, attach to one instance |
| Shared files across many VMs | File (EFS/NFS) | Concurrent multi-instance access |
| Backups and archives | Object storage | Cheap, durable, infinite scale |
| Fast temp scratch | Instance store | Highest speed, ephemeral |

## SECTION 5 — AWS PROVIDER MAPPING

This section maps the vendor-neutral 3.4 sub-objectives to AWS-specific services only. It is intended to help you connect CompTIA's general concepts to the AWS console and CLI you may see in labs or on the job.

| 3.4 Sub-objective | AWS Service / Approach |
| --- | --- |
| Patches | AWS Systems Manager (SSM) Patch Manager — define patch baselines, scan and install on a schedule across EC2 fleets. |
| Updates — Major version | AWS upgrade strategy: build a new AMI with the major version, deploy via Auto Scaling groups / launch templates, run a parallel environment, and keep the old AMI for rollback. |
| Updates — Minor version | AWS upgrade strategy: apply minor version changes in staging first (e.g., RDS engine upgrade with a snapshot), then production, validating compatibility. |
| Testing | AWS CodeDeploy (deployment with canary and blue/green) and AWS CodePipeline (staged CI/CD) plus rollback AMIs and pre-update EBS snapshots for instant revert. |
| Data — Ephemeral | EC2 instance store volumes and the /tmp directory — fast, local, non-durable; lost on stop or termination. |
| Data — Persistent | Amazon EBS (block), Amazon EFS (file), and Amazon S3 (object) for durable, independent storage. |
| Decommissioning — End of life (EOL) | Track versions via AWS Config rules and asset inventory; plan migration before the AWS/EOL date using new AMIs and managed-service version upgrades. |
| Decommissioning — End of support (EOS) | AWS Backup lifecycle policies to archive data, terminate resources (stop billing), delete volumes after confirmation, and use AWS Config rules to detect and flag unsupported versions for retirement. |

## SECTION 6 — PRACTICE QUESTIONS

1. A vendor releases a small fix that corrects a specific security flaw in your current OS version without changing the version number. This is best described as a:
   A. Major update
   B. Minor update
   C. Patch
   D. End-of-life event
   Answer: C — A patch is a targeted fix within the same version; it does not change major/minor numbers.

2. Which task is MOST appropriate to automate with a patch manager across a large EC2 fleet?
   A. Migrating from Java 8 to Java 21
   B. Applying the latest Linux security hotfix monthly
   C. Decommissioning an end-of-support server
   D. Archiving data to object storage
   Answer: B — Patches are routine, frequent fixes ideal for scheduled automation; major migrations are not patches.

3. A version change from v2 to v3 of an application that removes old APIs is a:
   A. Patch
   B. Minor update
   C. Major update
   D. Canary deployment
   Answer: C — A first-digit jump with breaking/removed features defines a major update.

4. Upgrading PostgreSQL from 14.4 to 14.6 is an example of a:
   A. Major update
   B. Minor update
   C. Patch
   D. End-of-support action
   Answer: B — A middle-digit change with backward-compatible fixes is a minor update.

5. What is the KEY difference between a minor update and a major update?
   A. Minor updates change the first digit
   B. Major updates are backward-compatible
   C. Major updates can include breaking changes
   D. Minor updates are never tested
   Answer: C — Major updates may break compatibility; minor updates stay backward-compatible.

6. Before promoting a new deployment to production, a team validates it in an environment that mirrors production. This practice is called:
   A. Decommissioning
   B. Testing in staging/QA
   C. Ephemeral storage
   D. End-of-life planning
   Answer: B — Staging/QA environments that mirror production are the core of pre-production testing.

7. A deployment exposes a new version to 5% of users first to watch for errors. This is a:
   A. Blue/green cutover
   B. Canary deployment
   C. Patch baseline
   D. Major update
   Answer: B — A canary releases to a small slice first; blue/green switches all at once.

8. Why should a tested rollback path be part of every update plan?
   A. It removes the need for staging
   B. It lets you revert quickly if the change fails
   C. It makes patches unnecessary
   D. It extends end-of-support dates
   Answer: B — A rollback plan minimizes downtime and data risk when an update goes wrong.

9. Which of the following is EPHPEMERAL data?
   A. A database on EBS
   B. User files in object storage
   C. Temp files in /tmp
   D. A shared NFS volume
   Answer: C — /tmp and similar temp spaces are lost when the instance stops; they are ephemeral.

10. Persistent data is best defined as data that:
    A. Disappears when the instance terminates
    B. Survives reboots and termination on durable storage
    C. Lives only in RAM during a process
    D. Is always stored on instance store
    Answer: B — Persistent data lives on durable storage independent of any single instance.

11. A high-speed cache that is lost if the VM stops should be placed on:
    A. An EBS volume
    B. Instance store
    C. An EFS file system
    D. Object storage
    Answer: B — Instance store is fast and ephemeral, ideal for caches you can lose.

12. A web app's customer records should be stored on:
    A. Instance store for speed
    B. /tmp for convenience
    C. A managed database with backups
    D. Swap space
    Answer: C — Customer records are persistent and must be durable and backed up.

13. End of life (EOL) for a software version means the vendor:
    A. Stops providing security patches immediately
    B. No longer develops or sells that version
    C. Requires you to upgrade today
    D. Deletes your data
    Answer: B — EOL is the "no longer developed/sold" milestone; EOS is when patches stop.

14. End of support (EOS) is best described as the date when the vendor:
    A. Releases a new major version
    B. Stops providing patches and technical support
    C. Announces the product
    D. Starts charging more
    Answer: B — EOS is the hard stop for patches and support, usually after EOL.

15. Which statement correctly orders the two milestones?
    A. EOS comes before EOL
    B. EOL comes before EOS
    C. They happen on the same day
    D. EOL and EOS are the same thing
    Answer: B — Vendors typically announce EOL first, then stop support (EOS) later.

16. As part of decommissioning an end-of-support server, you should FIRST:
    A. Terminate the instance immediately
    B. Archive its data to durable storage and verify
    C. Increase its instance size
    D. Apply a new patch
    Answer: B — Preserve and verify data before destroying the resource to avoid permanent loss.

17. Storing a batch job's intermediate frames on instance store is acceptable because:
    A. They must survive termination
    B. They are reproducible and loss is acceptable
    C. Instance store is durable
    D. They are customer records
    Answer: B — Ephemeral, reproducible temp data is fine on instance store; only final output needs durability.

18. The BEST reason to schedule patches during a maintenance window is to:
    A. Avoid changing the version number
    B. Minimize user impact from brief restarts
    C. Make the patch a major update
    D. Extend EOL
    Answer: B — Maintenance windows reduce disruption from the restarts patches may require.

19. Before a minor database upgrade, taking a snapshot primarily supports:
    A. Ephemeral storage cleanup
    B. A fast rollback if the upgrade fails
    C. End-of-life tracking
    D. Removing IAM roles
    Answer: B — A pre-upgrade snapshot enables quick rollback, a key testing/safety step.

20. A complete resource-lifecycle plan includes patching, updating, testing, data handling, and:
    A. Vulnerability scanning (CVE assessment)
    B. Decommissioning at EOL/EOS
    C. Writing new application code
    D. Purchasing new hardware
    Answer: B — Decommissioning at end-of-life/end-of-support closes the lifecycle; CVE scanning is 4.1, not 3.4.
