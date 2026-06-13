# Commit Plan

> A recommended commit sequence using [Conventional Commits](https://www.conventionalcommits.org/). Each commit is a small, reviewable, atomic unit.

## Initial Push (Day 1)

```bash
# 1. Bootstrap an empty GitHub repo, then locally:
git init
git add .gitignore LICENSE README.md CHANGELOG.md ROADMAP.md CONTRIBUTING.md CODE_OF_CONDUCT.md SECURITY.md docs/
git commit -m "chore: bootstrap repository with professional structure and GitHub files"
```

## Domain 1 Commits (Phase 1 — already complete)

```bash
git add objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md
git commit -m "docs(1.1): add Cloud Service Models & Shared Responsibility notes"

git add objectives/domain-1/1.2-service-availability-concepts.md
git commit -m "docs(1.2): add Service Availability Concepts notes (regions, AZs, DR)"

git add objectives/domain-1/1.3-cloud-networking-concepts.md
git commit -m "docs(1.3): add Cloud Networking Concepts notes"

git add objectives/domain-1/1.4-storage-resources-and-technologies.md
git commit -m "docs(1.4): add Storage Resources and Technologies notes"

git add objectives/domain-1/1.5-cloud-native-design-concepts.md
git commit -m "docs(1.5): add Cloud-Native Design Concepts notes"

git add objectives/domain-1/1.6-containerization-concepts.md
git commit -m "docs(1.6): add Containerization Concepts notes"

git add objectives/domain-1/1.7-virtualization-concepts.md
git commit -m "docs(1.7): add Virtualization Concepts notes"

git add objectives/domain-1/1.8-cost-considerations.md
git commit -m "docs(1.8): add Cost Considerations notes"

git add objectives/domain-1/1.9-database-concepts.md
git commit -m "docs(1.9): add Database Concepts notes"
```

## Study Aids Commits

```bash
git add cheatsheets/
git commit -m "docs(cheatsheets): add Domain 1 cheatsheet, shared responsibility matrix, and exam-day quick reference"

git add flashcards/
git commit -m "feat(flashcards): add 25 Domain 1 Markdown flashcards (Anki-importable)"

git add practice-questions/
git commit -m "feat(practice): add 10 Domain 1 scenario-based practice questions"
```

## Diagrams Commits

```bash
git add diagrams/shared-responsibility.md
git commit -m "feat(diagram): add shared responsibility Mermaid diagram"

git add diagrams/high-availability.md
git commit -m "feat(diagram): add multi-AZ high availability architecture diagram"

git add diagrams/disaster-recovery.md
git commit -m "feat(diagram): add DR strategy comparison diagram (hot/warm/cold/pilot)"

git add diagrams/kubernetes-architecture.md
git commit -m "feat(diagram): add Kubernetes architecture diagram"

git add diagrams/cicd-pipeline.md
git commit -m "feat(diagram): add CI/CD pipeline diagram"
```

## Cross-linking & Polish

```bash
git add resources/
git commit -m "docs(resources): add exam resources and CV0-004 to cloud-provider mapping"

git add progress/
git commit -m "docs(progress): add exam progress tracker and learning log"

git add .github/
git commit -m "chore(github): add issue and PR templates"
```

## Future Domain Commits (template)

```bash
# Domain 2 — Cloud Operations
git commit -m "docs(2.1): add <objective name> notes"
git commit -m "docs(2.2): add <objective name> notes"
# ...etc.

# Phase 6 — Study aids for new domains
git commit -m "docs(cheatsheets): add Domain 2 cheatsheet"
git commit -m "feat(flashcards): add Domain 2 flashcards"

# Phase 7 — Portfolio polish
git commit -m "docs(README): update progress tracker after passing exam"
git commit -m "docs(CHANGELOG): record v1.0.0 — Cloud+ CV0-004 certified"
```

## Tagging Releases

```bash
git tag -a v1.0.0 -m "Domain 1 complete"
git tag -a v1.1.0 -m "Portfolio restructure + study aids"
git push origin --tags
```

## Branch Strategy

- `main` — always deployable / ready to share with recruiters.
- `feat/*` — new objectives, diagrams, study aids.
- `fix/*` — typos, inaccuracies, broken links.
- `chore/*` — meta, formatting, dependencies.

**Rule:** No commit to `main` without a PR. Even when working solo, the PR becomes your review surface.

## Recruiter-Friendly History

After these commits, your `git log --oneline` will tell a clean professional story:

```text
chore(github): add issue and PR templates
docs(progress): add exam progress tracker and learning log
docs(resources): add exam resources and CV0-004 to cloud-provider mapping
feat(diagram): add CI/CD pipeline diagram
feat(diagram): add Kubernetes architecture diagram
feat(diagram): add DR strategy comparison diagram
feat(diagram): add multi-AZ high availability architecture diagram
feat(diagram): add shared responsibility Mermaid diagram
feat(practice): add 10 Domain 1 scenario-based practice questions
feat(flashcards): add 25 Domain 1 Markdown flashcards
docs(cheatsheets): add Domain 1 cheatsheet and exam-day quick reference
docs(1.9): add Database Concepts notes
docs(1.8): add Cost Considerations notes
docs(1.7): add Virtualization Concepts notes
docs(1.6): add Containerization Concepts notes
docs(1.5): add Cloud-Native Design Concepts notes
docs(1.4): add Storage Resources and Technologies notes
docs(1.3): add Cloud Networking Concepts notes
docs(1.2): add Service Availability Concepts notes
docs(1.1): add Cloud Service Models & Shared Responsibility notes
chore: bootstrap repository with professional structure and GitHub files
```
