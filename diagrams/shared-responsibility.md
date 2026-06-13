# Shared Responsibility Model

> Source: Domain 1.1 — Cloud Service Models & Shared Responsibility

```mermaid
flowchart TB
    subgraph OnPrem["🏢 On-Premises (you own EVERYTHING)"]
        OP1[Data]
        OP2[Application]
        OP3[OS]
        OP4[Virtualization]
        OP5[Hardware + DC]
    end

    subgraph IaaS["☁️ IaaS — e.g., AWS EC2, Azure VM"]
        I1[Data]:::cust
        I2[Application]:::cust
        I3[OS]:::cust
        I4[Runtime / Middleware]:::cust
        I5[Virtualization]:::prov
        I6[Hardware + DC]:::prov
    end

    subgraph PaaS["☁️ PaaS — e.g., Elastic Beanstalk, App Service"]
        P1[Data]:::cust
        P2[Application]:::cust
        P3[OS]:::prov
        P4[Runtime / Middleware]:::prov
        P5[Virtualization]:::prov
        P6[Hardware + DC]:::prov
    end

    subgraph SaaS["☁️ SaaS — e.g., Microsoft 365, Salesforce"]
        S1[Data]:::cust
        S2[Identity / Access]:::cust
        S3[Application]:::prov
        S4[OS + Runtime]:::prov
        S5[Virtualization]:::prov
        S6[Hardware + DC]:::prov
    end

    subgraph FaaS["☁️ FaaS — e.g., AWS Lambda, Azure Functions"]
        F1[Function code]:::cust
        F2[Data + IAM]:::cust
        F3[Everything else]:::prov
    end

    classDef cust fill:#ffd43b,stroke:#f59f00,color:#000
    classDef prov fill:#74c0fc,stroke:#1971c2,color:#000
```

**Legend:** 🟨 Customer-managed · 🟦 Provider-managed

### Rule of Thumb

- The customer **always** owns: **Data** and **Identity**.
- The provider **always** owns: **Physical DC**, **Hardware**, and **Host infrastructure**.
- OS ownership flips at the IaaS ↔ PaaS line.

---

🔗 See also: [1.1 — Cloud Service Models & Shared Responsibility](../objectives/domain-1/1.1-cloud-service-models-and-shared-responsibility.md) · [Cheatsheet: Shared Responsibility Matrix](../cheatsheets/shared-responsibility-matrix.md)
