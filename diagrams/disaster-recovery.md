# Disaster Recovery Strategies

> Source: Domain 1.2 — RTO, RPO, hot/warm/cold sites, pilot light

```mermaid
flowchart LR
    subgraph Hot["🔥 Hot Site (Active-Active)"]
        H1[Region 1 PRIMARY]:::primary
        H2[Region 2 PRIMARY]:::primary
        H1 <-->|near real-time| H2
    end

    subgraph Warm["🌡️ Warm Site (Active-Passive, scaled down)"]
        W1[Region 1 ACTIVE]:::primary
        W2[Region 2 STANDBY - reduced capacity]:::standby
        W1 -.->|async replication| W2
    end

    subgraph Cold["❄️ Cold Site (Data backup only)"]
        C1[Region 1 ACTIVE]:::primary
        C2[Backups in S3 / Blob]:::standby
        C1 -.->|periodic snapshots| C2
    end

    subgraph Pilot["🚥 Pilot Light"]
        P1[Region 1 ACTIVE - full]:::primary
        P2[Region 2 - core services only]:::standby
        P1 -.->|async + templates ready| P2
    end

    classDef primary fill:#ff8787,stroke:#c92a2a
    classDef standby fill:#ffd8a8,stroke:#d9480f
```

## Strategy Comparison

| Strategy | Cost | RTO | RPO | When to choose |
|---|---|---|---|---|
| **Backup & Restore (Cold)** | $ | Hours–Days | Hours | Non-critical, dev/test, archival |
| **Pilot Light** | $$ | 10s of min | Minutes | Critical apps, acceptable short downtime |
| **Warm Standby** | $$$ | Minutes | Seconds | Important, near-zero data loss tolerated |
| **Hot Standby (Multi-Site Active-Active)** | $$$$ | Seconds | Near zero | Mission-critical, zero-tolerance |

> 📐 **AWS / Azure / GCP equivalents:** AWS Elastic Disaster Recovery · Azure Site Recovery · Google Cloud DR Service

---

🔗 See also: [1.2 — Service Availability Concepts](../objectives/domain-1/1.2-service-availability-concepts.md) · [Domain 1 Cheatsheet](../cheatsheets/domain-1-cheatsheet.md)
