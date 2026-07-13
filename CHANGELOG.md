# Changelog

All notable changes to this repository are documented here. This file follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
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
