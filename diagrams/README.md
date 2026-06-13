# Diagrams

> Native **Mermaid** diagrams — render directly on GitHub. No plugins, no images, no build step.

## Available Diagrams

| Diagram | File | Best for |
|---|---|---|
| Shared Responsibility Model | [shared-responsibility.md](./shared-responsibility.md) | Objective 1.1 — see who manages what across IaaS/PaaS/SaaS/FaaS |
| High Availability Architecture | [high-availability.md](./high-availability.md) | Objective 1.2 — multi-AZ HA pattern |
| Disaster Recovery Strategy | [disaster-recovery.md](./disaster-recovery.md) | Objective 1.2 — hot / warm / cold / pilot light |
| Kubernetes Architecture | [kubernetes-architecture.md](./kubernetes-architecture.md) | Objective 1.6 — control plane, nodes, pods |
| CI/CD Pipeline | [cicd-pipeline.md](./cicd-pipeline.md) | Domain 4 — code → build → test → deploy |

## How to Edit

Open any `.md` file in this folder and edit the Mermaid block between the ```` ```mermaid ```` fences. GitHub will re-render automatically.

## Conventions

- All node labels are quoted (`Node["Label"]`) to allow spaces and special characters.
- Subgraphs use `subgraph ID["Title"]` and `end`.
- Arrows: `-->` solid, `-.->` dashed, `==>` thick.
- Colors: prefer built-in `classDef` for emphasis (e.g., `classDef critical fill:#ff6b6b`).
