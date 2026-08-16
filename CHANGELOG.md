# Changelog

All notable changes to this repository are documented here. This file follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- **`full notes/exam-ready/` — new v2 exam-ready study set.** Enhanced rewrite of the `full notes/` objectives against the official CV0-004 V4 objectives document (v5.0). The v1 notes are preserved unchanged.
- **Objective 1.1 — Cloud service models & shared responsibility** (`full notes/exam-ready/Objective-1.1-Cloud-Service-Models.md`), first note in the new set. Adds verbatim CompTIA objective bullets with a coverage checklist; per-model billing unit, elasticity, limitations and exam triggers for IaaS/PaaS/SaaS/FaaS; a 17-row shared-responsibility matrix including on-prem and the encryption in-transit/at-rest/key-management split; RACI framing; adjacent acronyms from CompTIA's official list (CaaS, DBaaS, DaaS/VDI, XaaS); AWS/Azure/GCP product mapping; 14 distractor patterns; 25 scenario-style practice questions; 3 PBQ-style drills (CV0-004 includes performance-based questions); and a 60-second recall sheet.
- **Objectives 1.2 – 1.11 — Domain 1.0 complete (11 / 11) in the exam-ready set.** ~10,700 lines across ten notes, each following the same section sequence. Highlights: 1.2 adds availability arithmetic (series/parallel, the nines table, `MTBF/(MTBF+MTTR)`) and the RTO/RPO timeline; 1.3 adds CIDR, longest-prefix match, stateless-NACL ephemeral ports and the VLAN→VXLAN scale limit; 1.4 adds the IOPS/throughput relationship, durability vs availability, and RAID write penalties; 1.5 adds the resilience patterns (circuit breaker, backoff, bulkhead, DLQ) and idempotency under at-least-once delivery; 1.6 adds the container-vs-VM comparison and the Kubernetes control plane; 1.7 adds hypervisor types, oversubscription/CPU-ready-time, and NUMA; 1.8 adds the cost-optimisation ladder and dedicated host vs dedicated instance; 1.9 adds CAP, ACID broken out, OLTP vs OLAP, replication, sharding and caching; 1.10 adds bottleneck analysis, orchestration vs workflow, and the bandwidth-delay product; 1.11 adds the analytical-vs-generative split, RAG, and the IoT range/power/bandwidth trade-off.
- **Objectives 2.1 – 2.5 — Domain 2.0 complete (5 / 5) in the exam-ready set.** ~4,500 lines. 2.1 adds the NIST five essential characteristics and the three tenancy traps (multicloud≠hybrid, private≠on-premises, VPC is a network not a private cloud); 2.2 adds version coexistence and the expand-contract database migration that makes rollback possible, plus feature flags and canary-vs-A/B; 2.3 flags that **CompTIA's six Rs differ from the common industry list** (no "repurchase"; refactor and re-architect are separate) and adds data-transfer maths; 2.4 adds real JSON/YAML syntax with the YAML gotchas (tabs forbidden, implicit booleans, unquoted version strings), state-file handling and drift remediation; 2.5 adds the constraint hierarchy in which compliance filters first and cost is optimised last.
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
- Two shared-responsibility inaccuracies carried over from the v1 Objective 1.1 note, corrected in the v2 note (the v1 file is left as-is by design):
  - Application code was listed as the **provider's** responsibility under FaaS. Function code and its dependencies are the **customer's**; only the runtime beneath it belongs to the provider.
  - The abstraction-ladder table ranked FaaS as lower customer effort than SaaS. **SaaS is the lowest** — under FaaS the customer still writes and maintains code.
- `docs/GITHUB-OPTIMIZATION.md` removed; its content (recommended repo name, description, topics) is now covered by `.github/REPO-SETTINGS.md` and the README "Quick Links" / Repo metadata. **No references to the deleted file existed anywhere in the repo** — safe removal.
- `README.md` repository tree corrected: `1.10` / `1.11` no longer falsely listed under `objectives/domain-1/` (they live at repo root as `CloudPlus_Objective_1.10_Notes.md` / `CloudPlus_Objective_1.11_Notes.md`), consistent with the Key Learning Areas links.
- `.github/pull_request_template.md` broken relative links repaired: `../../CONTRIBUTING.md` and `../../CHANGELOG.md` → `../` (template is one level under root, not two). Repairs the pre-existing broken links flagged on 2026-06-14.

### Planned
- Domain 2 — Cloud Operations notes (17%)
- Domain 3 — Cloud Security notes (20%)
- Domain 4 — DevOps Fundamentals notes (17%)
- Domain 5 — Troubleshooting notes (23%)
- Anki flashcard export (`flashcards/*.apkg`)
- Quiz-mode GitHub Pages site

### Manual Review Required (not auto-fixed)
- File-naming inconsistency: 1.10 and 1.11 live at the repo root (`CloudPlus_Objective_1.10_Notes.md`), whereas 1.1–1.9 have mirrors in `objectives/domain-1/1.X-…md`. Recommend mirroring for consistency.
- `cheatsheets/domain-1-cheatsheet.md`, `flashcards/domain-1-flashcards.md`, and `practice-questions/domain-1-practice.md` do not yet cover 1.10 / 1.11. The new notes carry inline cards and questions (Sections 10 & 11) but a central mirror would help recruiters.
- Domain-mapping claim conflict: `CloudPlus_Objective_1.11_Notes.md` line 2909 says *"Domain 2.0 — Security (19%)"*. The official CV0-004 v5.0 blueprint (and the rest of the repo) states **Domain 2 = Cloud Operations 17%**, **Domain 3 = Cloud Security 20%**. The 1.11 file also references *"Domain 5 (DevOps)"* and *"Domain 6 (Troubleshooting)"*, whereas the blueprint has only 5 domains with DevOps = Domain 4 and Troubleshooting = Domain 5. Recommend author review of 1.11 to align with the official blueprint.
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
