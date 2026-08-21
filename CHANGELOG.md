# Changelog

All notable changes to this repository are documented here. This file follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- **`full notes/exam-ready/` — new v2 exam-ready study set.** Enhanced rewrite of the `full notes/` objectives against the official CV0-004 V4 objectives document (v5.0). The v1 notes are preserved unchanged.
- **Objective 1.1 — Cloud service models & shared responsibility** (`full notes/exam-ready/Objective-1.1-Cloud-Service-Models.md`), first note in the new set. Adds verbatim CompTIA objective bullets with a coverage checklist; per-model billing unit, elasticity, limitations and exam triggers for IaaS/PaaS/SaaS/FaaS; a 17-row shared-responsibility matrix including on-prem and the encryption in-transit/at-rest/key-management split; RACI framing; adjacent acronyms from CompTIA's official list (CaaS, DBaaS, DaaS/VDI, XaaS); AWS/Azure/GCP product mapping; 14 distractor patterns; 25 scenario-style practice questions; 3 PBQ-style drills (CV0-004 includes performance-based questions); and a 60-second recall sheet.
- **Objectives 1.2 – 1.11 — Domain 1.0 complete (11 / 11) in the exam-ready set.** ~10,700 lines across ten notes, each following the same section sequence. Highlights: 1.2 adds availability arithmetic (series/parallel, the nines table, `MTBF/(MTBF+MTTR)`) and the RTO/RPO timeline; 1.3 adds CIDR, longest-prefix match, stateless-NACL ephemeral ports and the VLAN→VXLAN scale limit; 1.4 adds the IOPS/throughput relationship, durability vs availability, and RAID write penalties; 1.5 adds the resilience patterns (circuit breaker, backoff, bulkhead, DLQ) and idempotency under at-least-once delivery; 1.6 adds the container-vs-VM comparison and the Kubernetes control plane; 1.7 adds hypervisor types, oversubscription/CPU-ready-time, and NUMA; 1.8 adds the cost-optimisation ladder and dedicated host vs dedicated instance; 1.9 adds CAP, ACID broken out, OLTP vs OLAP, replication, sharding and caching; 1.10 adds bottleneck analysis, orchestration vs workflow, and the bandwidth-delay product; 1.11 adds the analytical-vs-generative split, RAG, and the IoT range/power/bandwidth trade-off.
- **Objectives 2.1 – 2.5 — Domain 2.0 complete (5 / 5) in the exam-ready set.** ~4,500 lines. 2.1 adds the NIST five essential characteristics and the three tenancy traps (multicloud≠hybrid, private≠on-premises, VPC is a network not a private cloud); 2.2 adds version coexistence and the expand-contract database migration that makes rollback possible, plus feature flags and canary-vs-A/B; 2.3 flags that **CompTIA's six Rs differ from the common industry list** (no "repurchase"; refactor and re-architect are separate) and adds data-transfer maths; 2.4 adds real JSON/YAML syntax with the YAML gotchas (tabs forbidden, implicit booleans, unquoted version strings), state-file handling and drift remediation; 2.5 adds the constraint hierarchy in which compliance filters first and cost is optimised last.
- **Objectives 3.1 – 3.4 — Domain 3.0 complete (4 / 4) in the exam-ready set.** 3.1 adds the three pillars of observability and the metrics/logs/traces split; 3.2 adds vertical vs horizontal vs diagonal scaling, scaling policies and cooldowns; 3.3 adds full/incremental/differential backup arithmetic and restore-chain maths, 3-2-1, and immutability; 3.4 adds patch/minor/major versioning, EOL vs EOS, and deprecation planning.
- **Objectives 4.1 – 4.6 — Domain 4.0 complete (6 / 6) in the exam-ready set.** 4.1 adds CVE vs CWE vs CVSS vs CWSS and scan types; 4.2 adds data sovereignty vs residency vs locality and SOC 2 vs ISO 27001; 4.3 adds AAA and SAML vs OIDC vs OAuth 2.0 (authentication vs delegated authorisation); 4.4 adds zero trust, secrets management and API security; 4.5 adds the preventive/detective/corrective matrix and IDS vs IPS; 4.6 adds cryptojacking, zombie instances and attacks on the `169.254.169.254` metadata endpoint.
- **Objectives 5.1 – 5.4 — Domain 5.0 complete (4 / 4) in the exam-ready set.** 5.1 covers Git and branching; 5.2 adds continuous integration vs continuous *delivery* vs continuous *deployment* ("the difference is the approval step"), build-once-deploy-many, SAST/DAST/SCA and the test pyramid; 5.3 adds the REST/SOAP/RPC/GraphQL master comparison, WebSockets, and over- vs under-fetching; 5.4 groups the nine named DevOps tools into four families and drills the five confusable pairs (Terraform/Ansible, Docker/Kubernetes, Grafana/Kibana, Jenkins/Actions, Git/GitHub).
- **Objectives 6.1 – 6.3 — Domain 6.0 complete (3 / 3) in the exam-ready set — 33 / 33 objectives overall.** 6.1 is built on two confusion clusters, the "not enough" trio (sizing vs quota vs oversubscription, resolved by "does another AZ work?") and the "not available" trio (deprecation vs regional availability vs outage); 6.2 adds a bottom-up triage ladder, the full HTTP status-code table including 502/503/504 and 401/403, latency vs bandwidth vs packet loss, the MTU black-hole, NAT port exhaustion, and access vs trunk ports; 6.3 is organised around the attack chain and the distinction between root cause and symptom, plus incident-response ordering (contain before eradicate; isolate and snapshot rather than terminate).
- **Diagrams for Objective 1.1** — 5 total: a layered stack showing each model's responsibility boundary, a Mermaid boundary-slide comparison, a Mermaid "who is responsible?" decision flow, a Mermaid FaaS cold-start lifecycle, and a cost-vs-control trade-off plot. Mermaid blocks reuse the existing `cust` / `prov` `classDef` conventions from `diagrams/`.
- `full notes/exam-ready/README.md` — folder index, the shared 12-section note structure, exam facts (90 questions, 90 minutes, 750 passing score) and a per-objective progress table.
- **Objective 1.10 — Methods for Optimizing Workloads** (`CloudPlus_Objective_1.10_Notes.md`). Compute-model selection, storage IOPS vs throughput, orchestration, workflow, network latency vs throughput, managed services.
- **Objective 1.11 — Evolving Technologies in the Cloud** (`CloudPlus_Objective_1.11_Notes.md`). 7 AI/ML capabilities + 4 IoT components.
- **Domain 1.0 expanded** from 9 to 11 objectives (now 11 / 11 complete).
- `.github/REPO-SETTINGS.md` — internal copy-paste source for GitHub repo About / Topics / Releases (not surfaced in README).
- Repository Maintenance tooling under `.kilo/` (gitignored locally; agent command files).

### Changed
- `README.md` — Domain 1 status updated from `9 / 9 objectives` → `11 / 11 objectives`; Key Learning Areas now lists 1.10 and 1.11; repository tree shows the new files in `objectives/domain-1/`.
- `progress/exam-progress.md` — Domain 1 row: objectives 9 → 11; Domain 1 detail table extended with 1.10 and 1.11; study-aids status marked as **partial** pending manual mirror of inline cards.
- `progress/learning-log.md` — new dated entry `Week of 2026-06-14` documenting the Domain 1 completion milestone.

### Fixed
- **Blueprint corrected repo-wide from CV0-003 to CV0-004.** `README.md`, `ROADMAP.md`, and `progress/exam-progress.md` all listed **five** domains with retired CV0-003 names and weights (Cloud Operations 17%, Cloud Security 20%, DevOps Fundamentals 17%, Troubleshooting 23%). CV0-004 has **six** domains: **1.0 Cloud Architecture 23% · 2.0 Deployment 19% · 3.0 Operations 17% · 4.0 Security 19% · 5.0 DevOps Fundamentals 10% · 6.0 Troubleshooting 12%**. Troubleshooting in particular was overstated at 23% when it is **12%** — a distortion that would misdirect study time. All three files now carry an explicit warning against comparing figures with CV0-003 material.
- **CHANGELOG "Manual Review Required" claim reversed.** A prior entry asserted that "the blueprint has only 5 domains with DevOps = Domain 4 and Troubleshooting = Domain 5" and flagged `CloudPlus_Objective_1.11_Notes.md` for referencing Domain 5 (DevOps) and Domain 6 (Troubleshooting). **The 1.11 file was correct and the CHANGELOG was wrong** — CV0-004 does have six domains with DevOps at 5 and Troubleshooting at 6. The erroneous entry has been removed. (The separate "Domain 2.0 — Security (19%)" line inside 1.11 remains inaccurate: Security is **Domain 4** at 19%.)
- Corrections carried into the exam-ready notes where the v1 files contained errors (v1 files left as-is by design): the v1 1.2 note stated "99.9% = 43 minutes down per year" — 43 minutes is **per month**; per year is **8.76 hours**. The v1 5.4 note claimed "Terraform" has one 'r' as an exam trick — it has three, and no such trick exists; the question was dropped. The v1 6.2 note labelled the same TLS scenario as both *deprecation* and *incompatibility* in different sections — resolved in v2 with an explicit test ("could your own setting restore it?"). The v1 6.3 note classified an over-permissive policy as *privilege escalation* (it is **excessive privilege**; escalation is acquiring rights beyond the grant) and recommended terminating a compromised instance, which **destroys forensic evidence** — v2 specifies snapshot and isolate instead.
- Two shared-responsibility inaccuracies carried over from the v1 Objective 1.1 note, corrected in the v2 note (the v1 file is left as-is by design):
  - Application code was listed as the **provider's** responsibility under FaaS. Function code and its dependencies are the **customer's**; only the runtime beneath it belongs to the provider.
  - The abstraction-ladder table ranked FaaS as lower customer effort than SaaS. **SaaS is the lowest** — under FaaS the customer still writes and maintains code.
- `docs/GITHUB-OPTIMIZATION.md` removed; its content (recommended repo name, description, topics) is now covered by `.github/REPO-SETTINGS.md` and the README "Quick Links" / Repo metadata. **No references to the deleted file existed anywhere in the repo** — safe removal.
- `README.md` repository tree corrected: `1.10` / `1.11` no longer falsely listed under `objectives/domain-1/` (they live at repo root as `CloudPlus_Objective_1.10_Notes.md` / `CloudPlus_Objective_1.11_Notes.md`), consistent with the Key Learning Areas links.
- `.github/pull_request_template.md` broken relative links repaired: `../../CONTRIBUTING.md` and `../../CHANGELOG.md` → `../` (template is one level under root, not two). Repairs the pre-existing broken links flagged on 2026-06-14.

### Planned
- Mirror the exam-ready set's inline flashcards and practice questions into `flashcards/` and `practice-questions/`
- Extend `cheatsheets/` beyond Domain 1
- Anki flashcard export (`flashcards/*.apkg`)
- Quiz-mode GitHub Pages site
- Visual verification that every Mermaid block in the exam-ready set renders on GitHub

### Manual Review Required (not auto-fixed)
- File-naming inconsistency: 1.10 and 1.11 live at the repo root (`CloudPlus_Objective_1.10_Notes.md`), whereas 1.1–1.9 have mirrors in `objectives/domain-1/1.X-…md`. Recommend mirroring for consistency.
- `cheatsheets/domain-1-cheatsheet.md`, `flashcards/domain-1-flashcards.md`, and `practice-questions/domain-1-practice.md` cover only Domain 1 (and not 1.10 / 1.11). The exam-ready notes carry inline questions and recall sheets, but a central mirror would help recruiters.
- Pre-existing broken links in `.github/pull_request_template.md` (lines 19, 24) — **Resolved in this sync** (`../../` → `../`).

## [1.1.0] — 2025-XX-XX — Portfolio Restructure

### Added
- Professional README with skills matrix, progress tracker, and architecture overview.
- `LICENSE` (MIT), `.gitignore`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, `ROADMAP.md`.
- New folder structure: `objectives/`, `diagrams/`, `cheatsheets/`, `flashcards/`, `practice-questions/`, `resources/`, `progress/`, `docs/`, `.github/`.
- Mermaid diagrams: shared responsibility, HA, DR, Kubernetes, CI/CD.
- Domain 1 cheatsheet, shared responsibility matrix, exam-day quick reference.
- Domain 1 flashcards (Markdown / Anki-compatible).
- Domain 1 practice questions (multiple-choice, scenario-based).
- Cloud provider mapping (CV0-004 concept → AWS / Azure / GCP).
- Exam progress dashboard and learning log.

### Changed
- Renamed objective files to `domain-X.Y-slug.md` convention.
- Centralized emoji-free frontmatter (metadata kept inside each file's existing header).

### Preserved
- All original note content (no deletions, no rewrites of authored material).

## [1.0.0] — Initial Release

### Added
- 9 Domain 1 objective notes (`1.1` through `1.9`) totalling ~885 KB of structured content.
- 14-section template per objective: Overview, Exam Knowledge, Real-World, Industry Updates, Cheat Codes, Knowledge Check, Acronyms, etc.
