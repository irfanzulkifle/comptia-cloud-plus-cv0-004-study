# Contributing

Thank you for your interest in improving this study repository! Contributions of all sizes are welcome — from a typo fix to a new diagram.

## How to Contribute

1. **Fork** the repository.
2. **Create a feature branch** from `main`:
   ```bash
   git checkout -b feat/improve-diagrams
   ```
3. **Make your changes** following the standards below.
4. **Commit** using [Conventional Commits](https://www.conventionalcommits.org/):
   - `docs:` documentation changes
   - `feat:` new content (objective, diagram, cheatsheet)
   - `fix:` corrections
   - `chore:` maintenance
5. **Push** to your fork and **open a Pull Request** describing your change.

## Content Standards

- **Markdown:** CommonMark + GitHub Flavored Markdown.
- **Diagrams:** Prefer native Mermaid over external images.
- **Tone:** Beginner-friendly where possible, but technically precise.
- **No fabrication:** Do not invent exam content. Cite the official CompTIA CV0-004 objective where relevant.
- **Naming:** `domain-X.Y-slug.md` for objectives, `kebab-case.md` otherwise.

## Style

- One H1 per file.
- Use callout blocks (`> **Note:**`) for important callouts.
- Use tables for comparisons.
- Cross-link related objectives using relative paths: `../domain-1/1.1-...md`.

## Reporting Issues

- Use the **Bug Report** template for typos / inaccuracies.
- Use the **Content Request** template for new topics or diagrams.
- Use the **Question** template for clarifications.

By contributing, you agree to abide by the [Code of Conduct](./CODE_OF_CONDUCT.md).
