# High Availability Architecture (Multi-AZ)

> Source: Domain 1.2 — Service Availability Concepts

```mermaid
flowchart TB
    User(["👤 Users (global)"])

    subgraph Edge["🌍 Edge / CDN"]
        CDN[Route 53 / CloudFront / Cloud CDN]
    end

    subgraph Region["🏞️ Region (e.g., us-east-1)"]
        direction TB

        subgraph AZ_A["Availability Zone A"]
            A1[App Server 1]:::app
            A2[App Server 2]:::app
            DB_A[(Primary DB)]:::primary
        end

        subgraph AZ_B["Availability Zone B"]
            B1[App Server 3]:::app
            B2[App Server 4]:::app
            DB_B[(Standby DB)]:::standby
        end

        subgraph AZ_C["Availability Zone C"]
            C1[App Server 5]:::app
        end

        LB[Internal Load Balancer]:::lb
        ASG[Auto Scaling Group]:::asg
    end

    DR["🏔️ DR Region (warm)"]:::dr

    User --> CDN
    CDN --> LB
    LB --> A1
    LB --> A2
    LB --> B1
    LB --> B2
    LB --> C1
    ASG -.manages.-> A1
    ASG -.manages.-> A2
    ASG -.manages.-> B1
    ASG -.manages.-> B2
    DB_A <-->|sync replication| DB_B
    DB_A -.async replication.-> DR

    classDef app fill:#d0bfff,stroke:#5f3dc4
    classDef primary fill:#ff8787,stroke:#c92a2a
    classDef standby fill:#ffd8a8,stroke:#d9480f
    classDef lb fill:#74c0fc,stroke:#1971c2
    classDef asg fill:#a5d8ff,stroke:#1864ab
    classDef dr fill:#c5f6fa,stroke:#0c8599
```

### Key Properties

| Property | Provided by |
|---|---|
| DNS-level failover | Route 53 / Traffic Manager / Cloud DNS |
| Edge caching | CloudFront / Cloud CDN / Azure Front Door |
| AZ-level redundancy | Multi-AZ deployment |
| Instance redundancy | Auto Scaling Group + health checks |
| Database HA | Synchronous replication between AZs |
| Region-level DR | Asynchronous replication to warm DR region |

### SLA Math (illustrative)

- Single EC2 instance SLA: **90%** → ~72 hrs downtime / year
- Multi-AZ service (e.g., ALB + ASG + RDS Multi-AZ): typically **99.99%** → ~52 min / year
- Multi-region active-active: can reach **99.999%** ("five nines") → ~5 min / year

---

🔗 See also: [1.2 — Service Availability Concepts](../objectives/domain-1/1.2-service-availability-concepts.md)
