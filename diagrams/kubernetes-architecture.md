# Kubernetes Architecture

> Source: Domain 1.6 — Containerization Concepts

```mermaid
flowchart TB
    subgraph CP["🎛️ Control Plane (managed by provider in EKS/AKS/GKE)"]
        API[API Server]:::cp
        ETCD[(etcd - cluster state)]:::store
        SCH[Scheduler]:::cp
        CM[Controller Manager]:::cp
        CMX[Cloud Controller Manager]:::cp
    end

    subgraph WN1["🖥️ Worker Node 1"]
        K1[kubelet]:::node
        K1P[kube-proxy]:::node
        C1[Container Runtime<br/>containerd]:::node
        subgraph Pods1["Pods"]
            PA1[App container]:::pod
            PB1[Sidecar]:::pod
        end
    end

    subgraph WN2["🖥️ Worker Node 2"]
        K2[kubelet]:::node
        K2P[kube-proxy]:::node
        C2[Container Runtime<br/>containerd]:::node
        subgraph Pods2["Pods"]
            PA2[App container]:::pod
        end
    end

    subgraph SVC["🌐 Services (stable endpoints)"]
        S1[ClusterIP]:::svc
        S2[NodePort / LoadBalancer]:::svc
        S3[Ingress]:::svc
    end

    API <--> ETCD
    SCH -.schedules.-> K1
    SCH -.schedules.-> K2
    CM -.controls.-> K1
    CM -.controls.-> K2
    K1 --> C1 --> Pods1
    K2 --> C2 --> Pods2
    S1 -.-> PA1
    S1 -.-> PA2
    S2 -.-> S1
    S3 -.-> S2

    classDef cp fill:#fff3bf,stroke:#f08c00
    classDef store fill:#ffd8a8,stroke:#d9480f
    classDef node fill:#d0bfff,stroke:#5f3dc4
    classDef pod fill:#a5d8ff,stroke:#1864ab
    classDef svc fill:#b2f2bb,stroke:#2f9e44
```

## Key Components

| Component | Role |
|---|---|
| **API Server** | Front door to the cluster; only component that talks to etcd |
| **etcd** | Distributed key-value store; source of truth for cluster state |
| **Scheduler** | Decides which node a new pod runs on |
| **Controller Manager** | Runs reconciliation loops (replicas, nodes, endpoints…) |
| **kubelet** | Node agent that ensures containers in pods are running |
| **kube-proxy** | Maintains network rules for service IP → pod IP |
| **Container runtime** | Runs containers (containerd, CRI-O) |
| **Pod** | Smallest deployable unit; 1+ co-scheduled containers |
| **Service** | Stable virtual IP + DNS for a set of pods |
| **Ingress** | L7 routing for external traffic into the cluster |

## Serverless Containers (PaaS-ish)

- **AWS Fargate** — run containers without managing nodes (EKS or ECS).
- **Azure Container Apps** — managed K8s without the YAML.
- **Google Cloud Run** — scale-to-zero containers, HTTP-driven.

---

🔗 See also: [1.6 — Containerization Concepts](../objectives/domain-1/1.6-containerization-concepts.md)
