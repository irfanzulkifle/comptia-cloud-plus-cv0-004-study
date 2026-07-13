# CompTIA Cloud+ CV0-004 — Domain 1.0 Cloud Architecture
## 📘 Objective 1.6: Containerization Concepts

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 1.0 Domain = 23% of total exam
> **Objective 1.6 Focus:** Understanding how containers work — from a single Docker container to a Kubernetes-managed fleet — including networking, storage, and image management.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 1.6
### Objective Name: *Compare and contrast containerization concepts.*

**What this objective means (beginner-friendly):**
Imagine containers are **standardized shipping containers** for software:
- A container bundles the app + its dependencies (libraries, configs) into one portable unit.
- It runs the same way on a laptop, a server, or in the cloud (no "works on my machine" problem).
- **Stand-alone** = one container on one machine.
- **Orchestrated** = hundreds/thousands of containers managed by Kubernetes.
- **Networking** = how containers talk to each other and the outside world.
- **Storage** = where containers keep data (some goes away when the container dies, some persists).
- **Image registries** = like GitHub for containers — a place to store and share images.

The exam will test whether you understand the container lifecycle: build → store → run → orchestrate → scale.

**Why it matters in the real world:**
- Containers are the **#1 deployment model** for cloud-native apps (Docker + Kubernetes are industry standards).
- Major companies (Google, Amazon, Netflix, Airbnb) run **thousands of containers** in production.
- Misunderstanding container storage = data loss. Misunderstanding networking = microservices that can't talk.
- Container security is a top cloud concern (vulnerable base images, runtime threats).

**Why CompTIA tests it:**
- Containers are a core cloud skill — Cloud+ candidates must know Docker + Kubernetes basics.
- Container decisions affect every other domain (security, operations, deployment, troubleshooting).
- CompTIA added containers to CV0-004 — it's relatively new exam content.
- PBQs often involve Kubernetes architecture (PV/PVC, services, deployments).

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Stand-Alone Containers

**Definition:**
A **single container** running on a single host, managed manually (e.g., via `docker run`). No orchestration layer, no auto-scaling, no self-healing.

**Purpose:**
- Run a single application in isolation.
- Ideal for development, testing, simple workloads, or learning.
- Simplest way to deploy a containerized app.

**How it works:**
- A container image (e.g., from Docker Hub) is pulled to a host.
- A container runtime (Docker Engine, containerd, Podman, CRI-O) starts the container from the image.
- The container runs as a process on the host, isolated by Linux namespaces and cgroups.
- The container has its own filesystem (from the image), network namespace (with a virtual IP), and resource limits.
- Lifecycle: start, stop, restart, delete — all manual.

**Common Tools:**
- **Docker:** The original container runtime (now part of Mirantis). Most popular for development.
- **containerd:** The industry-standard container runtime (used by Docker, K8s).
- **Podman:** Red Hat's daemonless container tool — rootless, systemd-friendly.
- **CRI-O:** Lightweight runtime for Kubernetes.

**Advantages:**
- Simple to understand and use.
- Fast startup (seconds vs. minutes for VMs).
- Resource-efficient (no guest OS overhead).
- Portable (same image runs anywhere).
- Good for dev/test.

**Disadvantages:**
- **No auto-scaling** — must manually start/stop containers.
- **No self-healing** — if a container crashes, it stays down.
- **No rolling updates** — must stop old, start new.
- **No service discovery** — must know IPs.
- **Single host** — limited by host capacity.
- **Production risk** — not suitable for critical workloads without orchestration.

**When to use:**
- Development and testing.
- Running a single tool (e.g., a database, a CLI utility).
- Small, simple workloads (one container per host).
- Learning container concepts.

**When NOT to use:**
- Production multi-container apps.
- Anything requiring HA, auto-scaling, or zero-downtime deploys.
- Multi-host deployments (use Kubernetes / ECS / Swarm).

**Real-world example:** A developer runs `docker run -d -p 8080:80 nginx` to test a website locally.

---

### 2.2 — Workload Orchestration

**Definition:**
**Workload orchestration** is the automated management of multiple containers across many hosts. It handles deployment, scaling, networking, storage, and lifecycle of containerized applications.

**Purpose:**
- Manage hundreds or thousands of containers.
- Provide HA (auto-restart, rescheduling on failure).
- Enable horizontal scaling (add more replicas as load grows).
- Support rolling updates and rollbacks.
- Handle service discovery and load balancing.
- Manage configuration and secrets.

**How it works:**
- A **control plane** (master nodes) manages the cluster.
- A **data plane** (worker nodes) runs the actual containers.
- You declare the **desired state** (e.g., "3 replicas of nginx") in YAML/JSON manifests.
- The orchestrator continuously compares the actual state to the desired state and **reconciles** (e.g., restarts failed containers, schedules new ones).
- Scheduler decides which node runs which container (based on resources, affinity, taints, etc.).
- Controllers watch state and react to changes.

**Container Orchestrators:**

| Orchestrator | Provider | Notes |
|---|---|---|
| **Kubernetes (K8s)** | CNCF (open source) | De facto standard. Used by EKS, AKS, GKE. |
| **Amazon ECS** | AWS | AWS-native, simpler than K8s, tight AWS integration. |
| **AWS Fargate** | AWS | Serverless containers (no EC2 management). |
| **Amazon EKS** | AWS | Managed Kubernetes on AWS. |
| **Azure Container Apps** | Azure | Serverless containers (built on K8s). |
| **Azure AKS** | Azure | Managed Kubernetes on Azure. |
| **GCP Cloud Run** | GCP | Serverless containers (built on Knative/K8s). |
| **GCP GKE** | GCP | Managed Kubernetes on GCP (pioneered K8s). |
| **Docker Swarm** | Docker | Built into Docker Engine. Simpler, less feature-rich. |
| **HashiCorp Nomad** | HashiCorp | Lightweight orchestrator, multi-runtime (containers, VMs, binaries). |
| **Apache Mesos** | Apache | Mature, complex, used at Twitter/X. |

**Kubernetes Architecture (Most Important to Know):**

- **Control Plane (Master):**
  - **API Server (kube-apiserver):** Frontend for the K8s API. All components talk to it.
  - **etcd:** Distributed key-value store. Cluster state.
  - **Scheduler (kube-scheduler):** Decides which node runs which pod.
  - **Controller Manager (kube-controller-manager):** Runs controllers (Deployment, ReplicaSet, Node, etc.).
  - **Cloud Controller Manager:** Integrates with the cloud provider (e.g., creates AWS load balancers).
- **Data Plane (Worker Nodes):**
  - **kubelet:** Agent on each node, talks to the API server, manages pods.
  - **kube-proxy:** Maintains network rules on the node (for service IPs).
  - **Container runtime:** containerd, CRI-O (runs the actual containers).
- **Pods:** The smallest deployable unit. A pod contains one or more containers that share a network namespace and storage.
- **Deployments:** A controller that manages ReplicaSets (which manage pods). Provides rolling updates and rollbacks.
- **Services:** Stable network endpoint for a set of pods. Provides load balancing.
- **Namespaces:** Virtual clusters within a physical cluster (for multi-tenancy).

**Key K8s Resources:**

- **Pod:** 1+ containers, shared network, ephemeral.
- **Deployment:** Manages ReplicaSets. Stateless apps.
- **StatefulSet:** Manages pods with stable identities. For stateful apps (DBs).
- **DaemonSet:** Runs one pod per node. For node-level services (logs, monitoring).
- **Job:** Runs a pod to completion. For batch jobs.
- **CronJob:** Schedules jobs. For periodic tasks.
- **Service:** Stable network endpoint + load balancing.
- **Ingress:** L7 routing for external traffic (HTTP path/host).
- **ConfigMap:** Non-sensitive config.
- **Secret:** Sensitive config (passwords, tokens).
- **PersistentVolume (PV):** Storage in the cluster.
- **PersistentVolumeClaim (PVC):** Request for storage by a pod.

**Advantages:**
- **Auto-scaling** (horizontal: more pods; vertical: more resources; cluster: more nodes).
- **Self-healing** (restart failed pods, reschedule on node failure).
- **Rolling updates** (zero-downtime deploys).
- **Rollbacks** (easy to revert to previous version).
- **Service discovery** (DNS-based, automatic).
- **Load balancing** (built-in, L4).
- **Multi-cloud** (K8s runs on any cloud or on-prem).
- **Self-healing infrastructure** — declarative.

**Disadvantages:**
- **Complexity** — K8s has a steep learning curve.
- **Operational overhead** — you need a team to manage the cluster (or use managed K8s).
- **YAML sprawl** — many manifests to manage.
- **Networking complexity** — CNI, services, ingresses, network policies.
- **Resource overhead** — control plane consumes resources.
- **Storage is hard** — distributed storage (e.g., Rook-Ceph, Portworx) adds complexity.

**When to use:**
- Microservices architectures (1.5).
- Multi-container apps with complex deployment needs.
- Multi-cloud or hybrid deployments.
- Apps that need auto-scaling, self-healing, and rolling updates.
- Large engineering teams.

**When NOT to use:**
- Small apps (over-engineering).
- Simple, single-container workloads (use stand-alone or serverless).
- Teams without K8s expertise.
- When a managed PaaS (App Service, Cloud Run) is sufficient.

**CompTIA Note:** K8s is the de facto standard. The exam will test K8s concepts (pods, deployments, services, PV/PVC). The exam will not require you to write K8s YAML from memory, but you should understand the architecture and resources.

---

### 2.3 — Networking (Including Port Mapping)

**Definition:**
The mechanisms that allow containers to **communicate with each other, with the host, and with the outside world**. Includes port mapping, network drivers, overlay networks, and service discovery.

**Purpose:**
- Enable container-to-container communication.
- Expose container services to external clients.
- Provide isolation between containers.
- Support service discovery and load balancing.

**How Container Networking Works:**

Each container gets a **virtual network interface** (veth pair) connected to a virtual bridge or overlay network. The container has its own IP, isolated from other containers' IPs.

**Container Network Drivers (Docker examples):**

| Driver | Description | Use Case |
|---|---|---|
| **bridge** | Default. Containers on the same host can communicate. | Stand-alone, single host |
| **host** | Container shares the host's network namespace. No isolation. | Performance-sensitive, network tools |
| **overlay** | Multi-host networking. Spans multiple Docker hosts. | Swarm / K8s |
| **macvlan** | Container has a MAC address on the physical network. Appears as a physical device. | Legacy apps, network monitoring |
| **none** | No networking. | Isolated workloads, batch jobs |

**Port Mapping (Port Forwarding):**

- The mechanism that maps a **host port** to a **container port**.
- Format: `host:container` (e.g., `-p 8080:80`).
- Example: `docker run -p 8080:80 nginx` means host port 8080 forwards to container port 80.
- Only the **container port** is exposed inside the container; the **host port** is what external clients connect to.
- If two containers on the same host need the same internal port (e.g., two nginx on port 80), you can use different host ports (8081, 8082).

**K8s Networking Model:**

K8s has a specific networking model:
- Every pod gets its **own IP address** (no NAT for pod-to-pod).
- Pods on any node can communicate with pods on any other node **without NAT**.
- Agents on a node (kubelet, kube-proxy) can communicate with pods on that node.
- Pods in the host network can communicate with all other pods **without NAT**.

**CNI (Container Network Interface):**

- A standard for **plugins** that provide networking for containers.
- Common CNI plugins: Calico, Cilium, Flannel, Weave Net, AWS VPC CNI.
- CNI handles: IP allocation, pod-to-pod routing, network policy, encryption (some).

**K8s Service Types:**

| Service Type | Description | Use Case |
|---|---|---|
| **ClusterIP** (default) | Internal IP, accessible only within the cluster. | Internal microservices. |
| **NodePort** | Exposes the service on each node's IP at a static port. | Dev/test, simple external access. |
| **LoadBalancer** | Provisions a cloud load balancer (e.g., AWS ELB). | Production external access. |
| **ExternalName** | Maps a service to a DNS name (CNAME). | Accessing external services. |
| **Ingress** | L7 routing (HTTP path/host). Not a service — an API object that routes to services. | Production web apps. |

**Service Discovery in K8s:**

- K8s services are assigned a DNS name: `<service>.<namespace>.svc.cluster.local`.
- Pods can reach services via DNS (CoreDNS).
- Service Discovery is automatic (see 1.5).

**Service Mesh:**

- For advanced networking (mTLS, traffic splitting, retries), use a service mesh.
- Istio, Linkerd, Consul Connect are the leading meshes.
- Mesh runs as sidecar proxies (Envoy/Istio) in each pod.

**Advantages of Container Networking:**
- Isolation (each container has its own network namespace).
- Portability (same network behavior across hosts).
- Service discovery (DNS-based, automatic in K8s).
- Scalability (overlay networks span thousands of nodes).

**Disadvantages:**
- Complexity (especially in K8s).
- Port conflicts (need to manage port mappings).
- Performance overhead (overlay network adds latency).
- Debugging is harder (network in a network).

**When to use what:**
- **Stand-alone (Docker):** Bridge network + port mapping.
- **Swarm:** Overlay network.
- **Kubernetes:** CNI plugin + Service + Ingress.
- **Production microservices:** Service mesh (Istio).

**When NOT to use:**
- Don't use host network unless you need maximum performance (less isolation).
- Don't use macvlan for typical apps (legacy use case).

---

### 2.4 — Storage Types

#### 2.4.1 — Ephemeral Storage

**Definition:**
Storage that is **tied to the container's or pod's lifecycle**. When the container/pod dies, the data is lost. Used for temporary data: caches, scratch space, intermediate results.

**Purpose:**
- Provide a writable filesystem for the container.
- Hold transient data that doesn't need to survive restarts.
- Enable fast, in-memory or on-disk operations.

**How it works:**
- The container image provides a read-only base filesystem.
- A **writable layer** is added on top (union mount / overlayfs).
- Any writes go to this writable layer.
- When the container is deleted, the writable layer is destroyed.
- In K8s, the default storage is `emptyDir` — a temporary directory created when a pod starts, deleted when the pod dies.

**Examples:**

| Ephemeral Storage | Provider | Description |
|---|---|---|
| **Container writable layer** | Docker / containerd | The top layer where the container writes. |
| **emptyDir** | Kubernetes | Per-pod temp directory. Lost on pod restart. |
| **hostPath** | Kubernetes | Mounts a file/directory from the host node. Lost when node is gone. |
| **In-memory (tmpfs)** | Docker / K8s | RAM-backed storage. Very fast, no persistence. |

**Advantages:**
- Fast (often local SSD or RAM).
- Simple (no external storage to manage).
- Free or cheap.
- Good for caches, scratch space.

**Disadvantages:**
- **No persistence** — data lost on container/pod death.
- **No sharing** across pods (per-pod).
- **No replication** — single point of failure.

**When to use:**
- Application logs (forwarded to a logging service).
- Caches (Redis has its own persistence; container cache is fine as ephemeral).
- Temp files in batch jobs.
- Intermediate build artifacts in CI.

**When NOT to use:**
- Any data that must survive pod restart.
- Database storage (databases need persistent volumes).
- User uploads (must persist).

---

#### 2.4.2 — Persistent Volumes

**Definition:**
Storage that **survives container or pod lifecycle**. The data lives independently of the container — when a pod is rescheduled, the new pod can mount the same volume and access the data.

**Purpose:**
- Provide durable storage for stateful applications (databases, queues, file servers).
- Decouple data lifetime from container lifetime.
- Enable data sharing between pods (with ReadWriteMany access mode).
- Support backups, snapshots, replication.

**How it works:**

- A **PersistentVolume (PV)** is a piece of storage in the cluster (e.g., an AWS EBS volume, a GCE Persistent Disk).
- A **PersistentVolumeClaim (PVC)** is a request for storage by a pod (e.g., "I need 100 GB of ReadWriteOnce storage").
- K8s binds the PVC to a suitable PV (dynamic provisioning via StorageClass, or static).
- The pod mounts the PVC as a volume in its filesystem.
- The volume outlives the pod — when the pod is deleted and a new one created, the new pod can mount the same PVC.

**K8s StorageClass (dynamic provisioning):**
- A **StorageClass** describes a "class" of storage (e.g., "fast SSD", "standard HDD").
- When a PVC is created with a StorageClass, K8s **dynamically provisions** a new PV (e.g., creates a new AWS EBS volume).
- This is the standard pattern — no manual PV creation.

**Storage Access Modes:**

| Mode | Description | Example |
|---|---|---|
| **ReadWriteOnce (RWO)** | Volume can be mounted as read-write by a single node. | AWS EBS, Azure Disk. |
| **ReadOnlyMany (ROX)** | Volume can be mounted as read-only by many nodes. | For static assets. |
| **ReadWriteMany (RWX)** | Volume can be mounted as read-write by many nodes. | NFS, EFS, Azure Files, Filestore. |
| **ReadWriteOncePod (RWOP)** | Volume can be mounted as read-write by a single pod. | Block storage, single-pod access. |

**Persistent Volume Providers:**

| Provider | Type | Access Mode | Example |
|---|---|---|---|
| **AWS EBS** | Block | RWO | gp3, io2 |
| **AWS EFS** | File (NFS) | RWX | Shared across many pods |
| **AWS FSx** | File (Lustre, NetApp, Windows) | RWX | HPC, enterprise |
| **Azure Disk** | Block | RWO | Premium SSD, Ultra Disk |
| **Azure Files** | File (SMB/NFS) | RWX | Shared |
| **GCP Persistent Disk** | Block | RWO | Balanced, SSD, Extreme |
| **GCP Filestore** | File (NFS) | RWX | Shared |
| **CSI driver** | Plugin | Various | Vendor-specific (NetApp, Portworx, Rook-Ceph) |

**Advantages:**
- **Data persistence** — survives pod restart.
- **Decoupling** — pod can be rescheduled, data stays.
- **Replicas for HA** — most providers support replication (e.g., EBS Multi-Attach, distributed filesystems).
- **Backup support** — snapshots, clones.
- **Sharing** (RWX) — multiple pods can access the same data.

**Disadvantages:**
- **Complexity** — storage classes, PVCs, mounting.
- **Cost** — persistent storage is more expensive than ephemeral.
- **Performance overhead** — network-attached storage has higher latency than local.
- **Single-AZ limitation** (for some types) — EBS is zonal; pod must be in same AZ.
- **Storage orchestration** — distributed storage (Ceph, Portworx) is operationally complex.

**When to use:**
- **Databases** (MySQL, Postgres, MongoDB).
- **Stateful applications** (any app that writes data).
- **Message queues** (Kafka, RabbitMQ).
- **User uploads** (images, videos, files).
- **Configuration that must persist** (though ConfigMaps are better for this).

**When NOT to use:**
- Stateless apps (no need).
- Pure cache data (use ephemeral).
- Data that's replicated elsewhere (e.g., in a managed DB service).

**CompTIA Note:** The exam will test the difference between ephemeral and persistent. The scenario will dictate the right answer:
- "User uploads a file" → Persistent.
- "Build cache" → Ephemeral.
- "DB writes" → Persistent.
- "Container scratch space" → Ephemeral.

**CSI (Container Storage Interface):**
- A standard for storage plugins in K8s.
- Replaces the older in-tree volume plugins.
- CSI drivers: AWS EBS CSI, Azure Disk CSI, GCE PD CSI, NetApp Trident, Portworx, Rook-Ceph.
- CSI allows K8s to work with any storage system that has a CSI driver.

---

### 2.5 — Image Registries

**Definition:**
A **centralized repository for storing, sharing, and versioning container images**. Images are pushed to the registry by developers/CI; pulled from the registry by orchestrators (K8s, ECS) when starting containers.

**Purpose:**
- Store container images in a central place.
- Version images (tags: v1.0, v1.1, latest).
- Share images across teams / environments.
- Provide access control (private vs public).
- Enable image scanning for vulnerabilities.
- Support CI/CD pipelines (build → push → deploy).

**How it works:**
- An image is built (e.g., `docker build`).
- The image is tagged (e.g., `myapp:v1.0`).
- The image is pushed to a registry (`docker push myregistry/myapp:v1.0`).
- The registry stores the image layers (deduplicated, content-addressed).
- A consumer (K8s, ECS, Docker) pulls the image (`docker pull` or `kubectl apply`).
- The image is verified (checksum, signature) and started as a container.

**Public Registries:**

| Registry | Provider | Use |
|---|---|---|
| **Docker Hub** | Docker | Largest public registry. Many official images (nginx, postgres, etc.). |
| **Amazon ECR Public** | AWS | AWS-managed public registry. |
| **GitHub Container Registry (ghcr.io)** | GitHub | GitHub-integrated. |
| **Google Container Registry / Artifact Registry** | GCP | GCP public registry. |
| **Quay** | Red Hat | Public + private. |
| **Microsoft Container Registry (MCR)** | Microsoft | Microsoft images (e.g., .NET, Windows). |

**Private Registries:**

| Registry | Provider | Use |
|---|---|---|
| **Amazon ECR (Elastic Container Registry)** | AWS | AWS-native private registry. |
| **Azure Container Registry (ACR)** | Azure | Azure-native. |
| **Google Artifact Registry** | GCP | GCP-native (replaced GCR). |
| **Harbor** | CNCF | Open-source, on-prem private registry. |
| **JFrog Artifactory** | JFrog | Enterprise universal artifact repo. |
| **GitLab Container Registry** | GitLab | Integrated with GitLab CI. |
| **Red Hat Quay** | Red Hat | Enterprise, geo-replicated. |

**Image Format:**

- **OCI (Open Container Initiative) standard** — Docker and OCI images are interoperable.
- An image consists of **layers** (read-only, deduplicated).
- Each layer represents a Dockerfile instruction (`FROM`, `RUN`, `COPY`, etc.).
- The final image is a stack of layers with a manifest and config.
- **Tags** identify versions (e.g., `nginx:1.25.0`, `nginx:latest`).
- **Digests** are content-addressed hashes (`nginx@sha256:abc...`) — fully immutable.

**Image Layers Example:**

```
FROM ubuntu:22.04     # Layer 1: base image (200 MB)
RUN apt-get install   # Layer 2: dependencies (50 MB)
COPY . /app           # Layer 3: source code (5 MB)
CMD ["./start.sh"]    # (metadata, not a layer)
```

**Advantages:**
- **Portability** — same image runs anywhere Docker/K8s runs.
- **Reusability** — base images can be shared.
- **Versioning** — tag images with versions, digests for immutability.
- **Caching** — Docker caches layers; only changed layers are pushed/pulled.
- **Security** — registries support image scanning (Trivy, Clair, ECR scanning, ACR scanning).
- **Access control** — private repos with RBAC.

**Disadvantages:**
- **Storage cost** — large images consume storage.
- **Image bloat** — large images = slow pulls, big attack surface.
- **Vulnerability risk** — base images may have CVEs.
- **Registry management** — running a private registry is operational overhead.
- **Image sprawl** — too many untagged images.

**Best Practices:**
- Use small, official base images (e.g., `alpine`, `distroless`).
- Multi-stage builds to reduce final image size.
- Pin image versions with digests (not `latest`) for reproducibility.
- Scan images for vulnerabilities (in CI/CD).
- Clean up old images regularly.
- Use private registries for proprietary code.
- Sign images (Docker Content Trust, Cosign) for supply chain security.

**When to use:**
- **Always** — every container deployment uses an image registry.
- Private for proprietary code.
- Public for open-source base images.

**When NOT to use:**
- Never skip the registry. Building images directly on hosts is anti-pattern.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — Stand-Alone Containers in the Wild

**AWS Example — Local Development:**
- A developer runs `docker run -d -p 8080:80 -v /home/user/data:/data nginx` to test a website locally.
- The container runs on their laptop. No orchestration, just a single container.
- Mounts a host volume to /data for persistent data.

**Azure Example — Tool Container:**
- A sysadmin runs `docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock docker:cli` to use Docker-in-Docker for one-off tasks.

**GCP Example — Batch Job:**
- A data scientist runs `docker run --rm -v $(pwd):/work gcr.io/cloud-builders/bazel` to run a one-off Bazel build.

---

### 3.2 — Workload Orchestration in the Wild

**AWS Example — Amazon ECS:**
- A media company runs 200 microservices on Amazon ECS Fargate (no EC2 management).
- Each service has an ECS Service definition (desired count, task definition, load balancer).
- ECS handles scheduling, auto-scaling, service discovery (via Cloud Map), and rolling updates.
- Fargate removes the need to manage EC2 instances.

**AWS Example — Amazon EKS:**
- A bank runs Kubernetes on EKS for its microservices.
- They use Istio service mesh for mTLS and traffic management.
- Karpenter (K8s autoscaler) manages nodes dynamically.
- EKS handles the control plane; they manage worker nodes (or use Fargate for serverless).

**Azure Example — Azure Kubernetes Service (AKS):**
- A retailer runs AKS for its e-commerce platform.
- 50 microservices, 3 namespaces (dev, staging, prod).
- Uses Azure CNI for networking, Azure Disk for persistent storage.
- Integrated with Entra ID (Azure AD) for RBAC.

**Azure Example — Azure Container Apps:**
- A startup uses Container Apps for a serverless microservices experience.
- No K8s manifest files; declarative YAML that Container Apps interprets.
- Built-in KEDA (event-driven autoscaling), Dapr (microservice building blocks), mTLS.
- Auto-scales to zero.

**GCP Example — GKE Autopilot:**
- A fintech uses GKE Autopilot — Google manages nodes, you only manage pods.
- Cluster autoscaling is automatic.
- They focus on application logic, not infrastructure.

**GCP Example — Cloud Run:**
- A company uses Cloud Run for a serverless API.
- Each service is a container image deployed to Cloud Run.
- Auto-scales from 0 to thousands of instances based on HTTP traffic.
- Pay per request + CPU/memory used.

---

### 3.3 — Container Networking in the Wild

**AWS Example — ECS Service Discovery:**
- An ECS service registers with Cloud Map as `orders.internal`.
- Other services query `orders.internal` and get a healthy task IP.
- Service Connect (ECS feature) adds L7 routing, retries, mTLS.

**Azure Example — AKS Ingress with Application Gateway:**
- AKS deploys microservices with an Application Gateway Ingress Controller (AGIC).
- The ingress routes `/api` to one service, `/images` to another, based on URL path.
- TLS termination at the gateway.

**GCP Example — GKE with Cloud Load Balancing:**
- GKE creates a Google Cloud Load Balancer when you expose a Service of type LoadBalancer.
- Ingress resources create Google Cloud Armor (WAF) + global HTTPS load balancer.
- Native VPC networking, no overlay.

---

### 3.4 — Storage in the Wild

**AWS Example — WordPress on EKS:**
- A company runs WordPress on EKS.
- The WordPress container uses an EBS-backed PVC for `/var/www/html` (persistent content).
- The MySQL container uses a separate PVC for the database.
- When the WordPress pod restarts, the content persists on EBS.

**Azure Example — PostgreSQL on AKS with Azure Disk:**
- A team runs PostgreSQL on AKS using StatefulSet.
- Each pod gets its own Azure Disk (RWO) via PVC.
- StorageClass "managed-csi-premium" provisions Premium SSD dynamically.
- Snapshots for backup.

**GCP Example — GKE with Filestore for Shared Content:**
- A media company uses Filestore (NFS) to share video files across many GKE pods.
- PVC with RWX access mode.
- Pods in different nodes mount the same Filestore share.

**Ephemeral Storage Example — CI/CD:**
- A Jenkins CI job runs in a container with `emptyDir` for build artifacts.
- The container compiles code, runs tests, produces a binary in `/workspace`.
- When the job finishes, the container is destroyed; the binary is pushed to a registry.

---

### 3.5 — Image Registries in the Wild

**AWS Example — ECR for Microservices:**
- A company pushes 50 microservice images to Amazon ECR (private).
- Each image is scanned on push (ECR scanning) for CVEs.
- EKS pulls images from ECR using IRSA (IAM Roles for Service Accounts) for authentication.
- Lifecycle policies clean up untagged images after 14 days.

**Azure Example — ACR with Geo-Replication:**
- A global company uses Azure Container Registry with geo-replication (US, EU, Asia).
- Images are pushed to the home region; replicated to other regions.
- AKS clusters in each region pull from the local replica (low latency).

**GCP Example — Artifact Registry for ML Models:**
- A data science team uses Google Artifact Registry to store both container images AND ML models.
- Cloud Build pushes images on git push.
- Vertex AI pulls models from Artifact Registry for training and serving.

**Public Registry Example — Docker Hub:**
- Developers use Docker Hub to pull official base images (postgres:16, nginx:1.25, redis:7, etc.).
- They extend these with their own code in a multi-stage Dockerfile.
- Final image is pushed to a private registry (ECR, ACR, Artifact Registry).

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Stand-Alone Container vs Orchestrated

| Attribute | Stand-Alone | Orchestrated (K8s, ECS) |
|---|---|---|
| **Setup** | Single command | Cluster, manifests, networking |
| **Auto-scaling** | Manual | Automatic |
| **Self-healing** | None | Auto-restart on failure |
| **Service discovery** | Manual (hard-coded IPs) | Automatic (DNS) |
| **Load balancing** | None | Built-in |
| **Rolling updates** | Manual | Automatic |
| **HA** | Single host | Multi-host, multi-AZ |
| **Operational overhead** | Low | High (or use managed) |
| **Best for** | Dev/test, simple apps | Production, microservices |
| **Tools** | Docker, Podman | K8s, ECS, Swarm, Nomad |

---

### 4.2 — Container Orchestrators Compared

| Attribute | Kubernetes | ECS / Fargate | Docker Swarm | Nomad |
|---|---|---|---|---|
| **Complexity** | High | Medium | Low | Medium |
| **Market share** | Largest | AWS-only | Declining | Niche |
| **Managed offering** | EKS, AKS, GKE | ECS / Fargate | None native | Nomad Enterprise |
| **Ecosystem** | Huge | AWS-integrated | Limited | Limited |
| **Best for** | Multi-cloud, complex apps | AWS-native shops | Simple deployments | HashiCorp shops |
| **Learning curve** | Steep | Moderate | Easy | Moderate |
| **Exam tip** | "Industry standard" | "AWS-native simpler option" | "Legacy / simple" | "HashiCorp stack" |

---

### 4.3 — Container Network Drivers

| Driver | Scope | Isolation | Use Case |
|---|---|---|---|
| **bridge** | Single host | Strong | Default for stand-alone |
| **host** | Single host | None (shared) | Performance-critical, network tools |
| **overlay** | Multi-host | Strong | Swarm, K8s |
| **macvlan** | Multi-host | Strong (L2) | Legacy apps, network monitoring |
| **none** | N/A | None | Isolated workloads |

---

### 4.4 — Ephemeral vs Persistent Storage

| Attribute | Ephemeral | Persistent |
|---|---|---|
| **Lifespan** | Container/pod lifetime | Independent of pod |
| **Use case** | Cache, temp files, logs | Database, user uploads |
| **Examples** | emptyDir, container writable layer | PV/PVC, EBS, Azure Disk |
| **Survives restart?** | No | Yes |
| **Cost** | Cheap / free | More expensive |
| **Performance** | Fast (often local) | Variable (network-attached) |
| **Replicas** | No | Yes (with provider support) |
| **Backup** | No | Yes (snapshots) |
| **Exam tip** | "Temporary / scratch" | "Survives restart / stateful" |

---

### 4.5 — Public vs Private Image Registries

| Attribute | Public | Private |
|---|---|---|
| **Access** | Anyone | Authenticated users / services |
| **Cost** | Free (with limits) | Per-GB storage + transfer |
| **Use case** | Open-source base images, public projects | Proprietary code, internal images |
| **Examples** | Docker Hub, GitHub Container Registry | ECR, ACR, Artifact Registry |
| **Security** | Trust the publisher | Full control |
| **Compliance** | Limited | Auditable |

---

### 4.6 — Container Service Tiers (AWS as Example)

| Service | Type | Managed? | Serverless? | Use Case |
|---|---|---|---|---|
| **Docker (on EC2)** | Stand-alone | No | No | Dev, simple workloads |
| **ECS on EC2** | Orchestrated | Partial | No | AWS-native, control of EC2 |
| **ECS on Fargate** | Orchestrated | Yes | Yes | AWS-native, no EC2 management |
| **EKS** | K8s | Partial (control plane) | No | K8s on AWS, manage nodes |
| **EKS on Fargate** | K8s | Yes | Yes | Serverless K8s |
| **Lambda** | Functions | Yes | Yes | Event-driven, not containers in the traditional sense |
| **App Runner** | PaaS-lite | Yes | Yes | Simple containerized web apps |

---

## SECTION 5 — EXAM TRAPS

### Trap 1: "Stand-alone is fine for production"
- **Trap:** "We can run our app in `docker run` on a single EC2."
- **Reality:** Single container = single point of failure. No auto-scaling, no self-healing. Use K8s/ECS for production.

### Trap 2: "Kubernetes is the only choice"
- **Trap:** "For containers in production, you need K8s."
- **Reality:** ECS, Cloud Run, Container Apps are simpler alternatives. K8s is the standard, but not always the right tool.

### Trap 3: "Ephemeral = no good"
- **Trap:** "Containers can't store data."
- **Reality:** Containers CAN store data via volumes (PV/PVC, bind mounts, CSI). Ephemeral is for temp data; persistent is for stateful.

### Trap 4: "EBS = shared storage"
- **Trap:** "Mount one EBS to 10 pods."
- **Wrong.** EBS is RWO (ReadWriteOnce) — only one pod at a time.
- **For shared:** EFS, Azure Files, Filestore, NFS.

### Trap 5: "Docker Hub for production"
- **Trap:** "Pull production images from Docker Hub."
- **Wrong:** Docker Hub has rate limits; reliability varies. Use a private registry (ECR, ACR, Artifact Registry) for production.

### Trap 6: "Port mapping is the only way"
- **Trap:** "Use `-p 8080:80` to expose a container."
- **Reality:** Port mapping is for host port → container port. In K8s, Services + Ingress handle this declaratively.

### Trap 7: "Multi-stage builds are optional"
- **Trap:** "Use a 1 GB base image with all your dev tools."
- **Reality:** Multi-stage builds keep final images small (alpine, distroless). Smaller = faster pulls, smaller attack surface.

### Trap 8: "Containers = VMs"
- **Trap:** "Containers are just like VMs."
- **Reality:** Containers share the host OS kernel (not a guest OS). Lighter, faster, but less isolated.

### Trap 9: "K8s manages everything"
- **Trap:** "K8s will manage my data too."
- **Reality:** K8s manages pods and orchestrating containers. Data persistence is via PV/PVC backed by external storage (EBS, etc.).

### Trap 10: "Image registries are secure"
- **Trap:** "Pulled image from Docker Hub = safe."
- **Reality:** Public images can have CVEs. Always scan images. Use private registries + signing for production.

### Trap 11: "Container = persistent identity"
- **Trap:** "Container's IP doesn't change."
- **Wrong.** In K8s, pods are ephemeral — IPs change on restart. Use Services for stable identity.

### Trap 12: "Service = LoadBalancer = always"
- **Trap:** "Make every K8s service LoadBalancer type."
- **Wrong:** LoadBalancer provisions a cloud LB ($$). Use ClusterIP for internal services. Use Ingress for HTTP routing.

### Trap 13: "CSI is built into K8s"
- **Trap:** "K8s automatically supports any storage."
- **Reality:** K8s needs a CSI driver for the storage. AWS EBS CSI, Azure Disk CSI, etc. are separate components.

### Trap 14: "Largest image = most features"
- **Trap:** "Big base image = ready for everything."
- **Wrong:** Big image = slow pulls, large attack surface, wasted resources. Use minimal bases.

### Trap 15: "Sidecar = side effect"
- **Trap:** "Sidecar pattern is just for logging."
- **Reality:** Sidecar is the foundation of service mesh (Envoy proxy in every pod for mTLS, traffic management).

---

## SECTION 6 — PERFORMANCE-BASED QUESTION PREP (PBQs)

### PBQ 1 — Containerizing a Legacy App

**Scenario:**
A company has a Java monolith running on a VM. They want to:
- Containerize it for easier deployment.
- Keep the existing on-prem Oracle DB.
- Run the container on AWS.
- Ensure the container can reach the on-prem DB.

**Question:** Design the container architecture.

**Walkthrough:**
1. **Build the image:** Multi-stage Dockerfile — build with Maven in stage 1, copy the JAR to a JRE base in stage 2.
2. **Push to registry:** Amazon ECR.
3. **Where to run:** ECS on EC2 (or Fargate) — keep it simple.
4. **Networking:** Site-to-Site VPN from AWS VPC to on-prem. The container's task runs in a private subnet; routes to on-prem via VPN.
5. **Storage:** Container is stateless; DB is on-prem. No persistent storage needed in the container.
6. **Image:** Use a slim JRE base (e.g., `eclipse-temurin:17-jre-alpine`).
7. **Health check:** Add `HEALTHCHECK` in Dockerfile.
8. **Registry:** ECR with scanning enabled.

**Anti-pattern to avoid:** Using a 1 GB Ubuntu base when you only need JRE.

---

### PBQ 2 — Multi-Container App with Persistent Storage

**Scenario:**
A team wants to deploy a WordPress site on Kubernetes:
- WordPress frontend (container).
- MySQL database (container, stateful).
- They want the database to persist across pod restarts.

**Question:** Design the storage strategy.

**Walkthrough:**
1. **MySQL pod:** Uses a **PersistentVolumeClaim** for `/var/lib/mysql`.
2. **Storage class:** `gp3` (AWS) or `managed-csi-premium` (Azure) or `standard` (GCP).
3. **Access mode:** RWO (ReadWriteOnce) — only the MySQL pod writes.
4. **MySQL as StatefulSet** (not Deployment) — for stable network identity.
5. **WordPress pod:** Can use Deployment (stateless).
6. **MySQL credentials:** Use K8s **Secret** (not ConfigMap).
7. **Backup:** Use a **CronJob** to take periodic snapshots of the PVC (via VolumeSnapshot).
8. **Service:** MySQL exposed as ClusterIP (internal). WordPress connects via the service DNS name.
9. **External access:** WordPress exposed via Ingress (HTTP routing).

**Why not emptyDir for MySQL?** Data lost on pod restart.
**Why not hostPath?** Tied to a specific node; not portable.

---

### PBQ 3 — Service Discovery for Microservices

**Scenario:**
A team has 3 microservices on K8s: frontend, api, db.
- frontend calls api at `http://api/orders`.
- api calls db at `mysql://db:3306/orders`.

When pods restart, IP changes. They want stable service names.

**Question:** How do they implement service discovery?

**Walkthrough:**
1. **Create K8s Services for each pod:**
   - `api` Service → routes to api pods.
   - `db` Service → routes to db pods.
2. **DNS resolution:** CoreDNS resolves `api` and `db` to ClusterIPs.
3. **frontend config:** `http://api/orders` (uses the Service name).
4. **api config:** `mysql://db:3306/orders` (uses the Service name).
5. **Result:** When a pod restarts and gets a new IP, the Service updates and DNS resolves to the new IP. No app change needed.
6. **Bonus:** Use a Service Mesh (Istio) for advanced features (mTLS, retries, observability).

**Without Services:** IPs would be hard-coded — broken on every pod restart.

---

### PBQ 4 — Port Mapping for Legacy App

**Scenario:**
A team is migrating a legacy app to containers. The app listens on port 8080 inside the container. They want to expose it on the standard HTTP port 80 on the host.

**Question:** What's the docker run command?

**Walkthrough:**
```bash
docker run -d -p 80:8080 --name myapp myregistry/myapp:v1
```
- `-d`: detached (run in background).
- `-p 80:8080`: host port 80 → container port 8080.
- `--name myapp`: container name.
- `myregistry/myapp:v1`: image name and tag.

**Bonus:** What if the host already has a service on port 80?
- **Answer:** Use a different host port (e.g., `-p 8081:8080`) and access the app at `http://host:8081`.

**Bonus:** In K8s, this is done declaratively:
```yaml
apiVersion: v1
kind: Service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30080
```

---

### PBQ 5 — Image Registry Strategy

**Scenario:**
A company is moving from VMs to containers. They have:
- Public open-source base images (nginx, postgres).
- Proprietary application code.

**Question:** How do they manage images?

**Walkthrough:**
1. **Public base images:** Pull from **Docker Hub** (or vendor's public registry).
2. **Proprietary app images:** Build in CI (e.g., CodeBuild, GitHub Actions), push to **private registry** (ECR, ACR, Artifact Registry).
3. **Image scanning:** Enable on push (ECR scanning, ACR scanning, Trivy in CI).
4. **Image signing:** Use **Cosign** or **Docker Content Trust** to sign images.
5. **Tagging:** Use semantic versioning (`v1.0.0`), git SHA, or digest. Avoid `latest` in production.
6. **Lifecycle policy:** Clean up untagged images after 14 days.
7. **Replication (multi-region):** Enable cross-region replication (ECR, ACR geo-replication) for global deployments.

**Anti-pattern to avoid:** Pulling production images from Docker Hub (rate limits, no control).

---

### PBQ 6 — Storage Class for Performance Database

**Scenario:**
A team is running PostgreSQL on EKS. They need:
- 500 GB of storage.
- 10,000 IOPS.
- Low latency (<5 ms).
- Persistent across pod restarts.

**Question:** What storage solution?

**Walkthrough:**
1. **Storage class:** AWS EBS `io2` (or `io2 Block Express` for very high IOPS).
2. **Provisioned IOPS:** 10K (provision explicitly in the StorageClass parameters).
3. **Access mode:** RWO (only one pod at a time).
4. **PVC manifest:**
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: postgres-data
   spec:
     accessModes: [ReadWriteOnce]
     storageClassName: io2
     resources:
       requests:
         storage: 500Gi
     # parameters: iops: "10000"
   ```
5. **Backup:** Use VolumeSnapshots via the AWS EBS CSI driver.

**Why not EFS?** EFS is for shared access (RWX). PostgreSQL is single-tenant (RWO).
**Why not gp3?** gp3 maxes at 16K IOPS but at lower baseline; io2 has more consistent high-IOPS performance.

---

## SECTION 7 — MEMORY AIDS

### 🎯 Container Mnemonic: **"Container = Standard Shipping Box"**

- A shipping container holds anything, anywhere, the same way.
- A software container bundles code + dependencies.
- Runs the same on a laptop, server, or cloud.
- "**W**orks on **m**y **m**achine" → "**W**orks **e**verywhere."

### 🎯 K8s Resources Mnemonic: **"P-D-S-C-J-C-P-I-V"**

- **P**od
- **D**eployment (stateless)
- **S**tatefulSet (stateful)
- **C**onfigMap
- **J**ob / CronJob
- **C**lusterRole / ServiceAccount (RBAC)
- **P**ersistentVolumeClaim
- **I**ngress
- **V**olume

### 🎯 K8s Networking Mnemonic: **"KISS"**

- **K**ube-proxy routes traffic on each node.
- **I**P-per-pod — every pod gets its own IP.
- **S**ervice provides stable DNS + load balancing.
- **S**ervice Discovery via CoreDNS.

### 🎯 Ephemeral vs Persistent Mnemonic: **"Ephemeral = Erasable Pencil"**

- **Ephemeral** = pencil on a notepad (gone when you erase / pod dies).
- **Persistent** = written in a book (survives forever / until you delete the volume).

### 🎯 Image Registry Mnemonic: **"Registry = App Store"**

- App Store for containers.
- Public (Docker Hub) for base images.
- Private (ECR, ACR) for your own.
- Versioned with tags.
- Scanned for vulnerabilities.

### 🎯 K8s Architecture Mnemonic: **"Brain + Muscles"**

- **Control Plane = Brain** (decides what to do).
- **Worker Nodes = Muscles** (do the work).
- API server is the brain's "phone" — workers call it.
- Scheduler is the brain's "GPS" — decides where pods go.
- etcd is the brain's "memory" — cluster state.

### 🎯 Stand-Alone vs Orchestrated Mnemonic: **"Solo vs Orchestra"**

- **Solo** = one musician (one container, no conductor).
- **Orchestra** = many musicians, one conductor (orchestrator).
- The conductor (K8s) coordinates timing, dynamics, recovery.

### 🎯 PV/PVC Mnemonic: **"PV is the Storage, PVC is the Request"**

- **P**ersistent**V**olume = the actual storage (admin provides).
- **P**ersistent**V**olume**C**laim = the request for storage (app asks).
- K8s matches PVC to PV (or dynamically provisions via StorageClass).

### 🎯 Port Mapping Mnemonic: **"Host:Container"**

- Format: `host:container` (e.g., `-p 8080:80`).
- "**H**ost goes first, **C**ontainer second."
- If you forget, think: "Outside comes first, inside comes second."

### 🎯 Image Layers Mnemonic: **"Lego Stack"**

- Each Dockerfile instruction adds a Lego block.
- Layers are deduplicated (shared base images = shared layers).
- Smaller images = fewer blocks = faster pulls.

### 🎯 K8s Service Types Mnemonic: **"C-N-L-E" (CLNE)** — pronounced "clean"

- **C**lusterIP — internal.
- **N**odePort — each node.
- **L**oadBalancer — cloud LB.
- (No **E** in basic K8s, but **E**xternalName maps to DNS.)

### 🎯 Container Image Format Mnemonic: **"OCI"**

- **O**pen **C**ontainer **I**nitiative.
- Industry standard.
- Docker images = OCI images.
- Composed of layers + manifest + config.

### 🎯 "Don't put state in the container" Story

A team put their database inside a container. When the container died, the data was gone. They learned the hard way: **containers are ephemeral by default.** State goes in a **persistent volume**, not in the container's filesystem. Always.

### 🎯 Right Tool Decision

```
Q: Single container, dev/test?
└── YES → Stand-alone (Docker / Podman)

Q: Multi-container, production, multi-host?
└── YES → Orchestrator:
    ├── K8s (multi-cloud, complex)
    ├── ECS (AWS-native, simpler)
    ├── Cloud Run / Container Apps (serverless)
    └── Swarm / Nomad (niche)

Q: Stateful workload?
└── YES → Persistent Volume (PV/PVC + EBS/Disk/PD)
└── NO  → Ephemeral (emptyDir, writable layer)

Q: Public image?
└── YES → Public registry (Docker Hub)
└── NO  → Private registry (ECR, ACR, Artifact Registry)
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

### Container Runtimes and Tools

| Concept | Docker | Podman | containerd | CRI-O |
|---|---|---|---|---|
| **Daemon** | Yes (dockerd) | No (daemonless) | Yes (containerd daemon) | Yes |
| **Rootless** | With config | Yes (default) | No | No |
| **K8s compatible** | Via containerd | Via CRI | Yes (direct) | Yes (direct) |
| **Industry** | Most popular | Red Hat, sysadmins | Default in K8s | K8s default in OpenShift |

### Orchestrators (Detailed)

| Provider | Managed K8s | Serverless Containers | ECS-equivalent | Notes |
|---|---|---|---|---|
| **AWS** | EKS, EKS on Fargate | Fargate, App Runner | ECS, ECS on Fargate | EKS Anywhere for on-prem |
| **Azure** | AKS, AKS Automatic | Container Apps, AKS Virtual Nodes | Container Instances (ACI) | AKS Arc for hybrid |
| **GCP** | GKE, GKE Autopilot | Cloud Run | — (uses GKE) | GKE Enterprise for hybrid |
| **Other** | Rancher, OpenShift, K0s, K3s | Fly.io, Railway | Nomad | On-prem options |

### Container Networking (CNI Plugins)

| Plugin | Provider | Features | Use Case |
|---|---|---|---|
| **AWS VPC CNI** | AWS | Native VPC IPs for pods | EKS, production |
| **Azure CNI** | Azure | Native VNet IPs for pods | AKS |
| **GCP VPC-native** | GCP | Native VPC IPs for pods | GKE |
| **Calico** | Tigera | Network policy, BGP | Multi-cloud, security |
| **Cilium** | Isovalent | eBPF-based, observability | Modern, performance |
| **Flannel** | CoreOS | Simple overlay | Dev/test |
| **Weave Net** | Weaveworks | Mesh networking | Legacy |

### Storage (Container Storage Interface — CSI)

| Provider | CSI Driver | Type | Access Modes |
|---|---|---|---|
| **AWS** | EBS CSI, EFS CSI, FSx CSI | Block, File, File | RWO, RWX, RWX |
| **Azure** | Disk CSI, File CSI, Blob CSI | Block, File, Object | RWO, RWX, RWX |
| **GCP** | Persistent Disk CSI, Filestore CSI | Block, File | RWO, RWX |
| **3rd party** | NetApp Trident, Portworx, Rook-Ceph | Various | Various |
| **On-prem** | Local static provisioning, NFS CSI | Various | RWO, RWX |

### Image Registries

| Provider | Public Registry | Private Registry | Features |
|---|---|---|---|
| **AWS** | ECR Public | ECR | Scanning, replication, lifecycle |
| **Azure** | Microsoft Container Registry (MCR) | Azure Container Registry (ACR) | Geo-replication, tasks, scanning |
| **GCP** | Google Container Registry (GCR, deprecated) | Artifact Registry | Multi-format (images, packages, language libs) |
| **Docker** | Docker Hub | Docker Hub private repos | Largest public registry |
| **GitHub** | GitHub Container Registry (ghcr.io) | GitHub Packages | Integrated with GitHub Actions |
| **Red Hat** | Quay.io | Red Hat Quay | Enterprise features |
| **Self-hosted** | — | Harbor, JFrog Artifactory | On-prem, air-gapped |

### Container Build and CI/CD

| Tool | Use | Notes |
|---|---|---|
| **Docker / BuildKit** | Local builds | BuildKit is the new build engine |
| **Kaniko** | Building in K8s (no Docker daemon) | Daemonless |
| **Buildah** | OCI image builds | Daemonless |
| **img** | Library for image manipulation | Daemonless |
| **AWS CodeBuild** | Managed build service | Builds Docker images |
| **Azure Pipelines** | CI/CD | Builds Docker images |
| **GCP Cloud Build** | Managed build service | Builds Docker images |
| **GitHub Actions** | CI/CD | Builds Docker images, integrates with ghcr.io |

---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What is a container?**
**Ideal answer:** "A lightweight, portable, standalone executable package that includes an application and all its dependencies (libraries, configs, runtime). It runs consistently across any environment that supports containers. Containers share the host OS kernel but have isolated user-space."

**Q2: What's the difference between a container and a VM?**
**Ideal answer:** "A VM runs a full guest OS on a hypervisor — heavier, slower to start, fully isolated. A container shares the host OS kernel — lighter, faster to start, less isolated. Containers are more efficient for cloud-native apps; VMs are needed for full OS control or different OS types."

**Q3: What is Kubernetes?**
**Ideal answer:** "An open-source container orchestration platform. It automates deployment, scaling, and management of containerized applications. K8s manages clusters of nodes running containers, handles scheduling, self-healing, rolling updates, service discovery, and load balancing. It's the de facto standard for container orchestration."

**Q4: What is a Docker image vs. a container?**
**Ideal answer:** "An image is a static, immutable template — the blueprint. It includes the app code, libraries, and a base OS layer. A container is a running instance of an image — the live process. You can have many containers running from the same image."

**Q5: What is a Kubernetes pod?**
**Ideal answer:** "The smallest deployable unit in K8s. A pod contains one or more containers that share a network namespace (same IP) and storage. Pods are ephemeral — they come and go. You usually don't create pods directly; you create Deployments or StatefulSets that manage pods."

**Q6: What is the difference between a Deployment and a StatefulSet in K8s?**
**Ideal answer:** "Deployment is for stateless apps — pods are interchangeable, no stable identity. StatefulSet is for stateful apps — pods have stable network identities and persistent storage. Use Deployments for web servers; use StatefulSets for databases."

**Q7: What is an image registry?**
**Ideal answer:** "A storage system for container images. Images are pushed to the registry after build; orchestrators pull from the registry to start containers. Examples: Docker Hub (public), Amazon ECR, Azure Container Registry, Google Artifact Registry (private)."

---

### 🎓 Cloud Administrator Questions

**Q1: A team is running 100 microservices in production on EC2 (manually managed). They want to migrate to containers. What do you recommend?**
**Ideal answer:** "I'd recommend a managed container service: AWS ECS with Fargate (serverless) or EKS (managed K8s) for AWS-native. For multi-cloud or complex orchestration, K8s. For simplicity, ECS. Steps:
1. Containerize each microservice (multi-stage Dockerfile, small base image).
2. Push images to a private registry (ECR).
3. Define tasks (ECS) or deployments (EKS) for each service.
4. Configure service discovery (Cloud Map for ECS, K8s Services for EKS).
5. Set up CI/CD to build, push, and deploy automatically.
6. Configure auto-scaling, health checks, and rolling updates.
7. Set up logging and monitoring (CloudWatch, Prometheus, etc.).
8. Migrate traffic gradually using a load balancer."

**Q2: A database in a K8s pod loses its data when the pod is restarted. What's wrong?**
**Ideal answer:** "The database is using ephemeral storage (the container's writable layer or emptyDir). When the pod restarts, the storage is gone. Fix:
1. Create a **PersistentVolumeClaim (PVC)** with a **StorageClass** that provides durable storage (e.g., AWS EBS gp3, Azure Disk, GCP Persistent Disk).
2. Mount the PVC in the pod's manifest at the database's data directory (`/var/lib/mysql`).
3. Use a **StatefulSet** (not Deployment) so the pod has a stable identity and the volume is correctly reattached.
4. Verify the access mode (RWO for most block storage).
5. Test by deleting the pod — the new pod should mount the same PVC and find the data."

**Q3: A team has a monolithic app that they want to break into microservices. They plan to put each service in its own container. What's the next step?**
**Ideal answer:** "Beyond just containerizing, they need:
1. **Container orchestration:** K8s (EKS/AKS/GKE) or ECS.
2. **Service discovery:** K8s Services + CoreDNS, or ECS Service Discovery / Cloud Map.
3. **Communication:** REST APIs, gRPC, or async messaging (SQS, Pub/Sub, Service Bus).
4. **Persistent storage:** StatefulSet + PV/PVC for any stateful service.
5. **Configuration management:** ConfigMaps and Secrets for non-sensitive and sensitive config.
6. **CI/CD:** Build, scan, push, deploy pipelines per service.
7. **Observability:** Distributed tracing (X-Ray, App Insights, Cloud Trace), centralized logging, metrics.
8. **Service mesh (optional):** Istio or Linkerd for mTLS, traffic management.
9. **API gateway:** Centralized entry point.
The team also needs to rethink data — each service should own its data (no shared DBs)."

**Q4: How do you handle image vulnerabilities in a production container deployment?**
**Ideal answer:** "Multi-layered approach:
1. **Image scanning in CI/CD:** Tools like Trivy, Clair, Snyk, or provider scanning (ECR, ACR, Artifact Registry).
2. **Block on critical CVEs:** Pipeline fails if critical vulnerabilities are found.
3. **Use minimal base images:** Alpine, distroless, or scratch. Smaller attack surface.
4. **Pin versions:** Use image digests, not `latest`, for reproducibility.
5. **Image signing:** Cosign, Docker Content Trust — verify the image hasn't been tampered with.
6. **Runtime security:** Falco, Tetragon — detect anomalous container behavior.
7. **Regular rebuilds:** Base image updates bring in security patches; rebuild and redeploy.
8. **Admission controllers:** K8s admission webhooks to enforce policies (e.g., only signed images allowed)."

**Q5: A team is using EKS with many microservices. They want to control which services can talk to each other. What do you recommend?**
**Ideal answer:** "K8s **Network Policies** or a service mesh.
1. **Network Policies:** K8s-native, allow/deny traffic based on pod labels, namespaces, IPs. Requires a CNI that supports them (Calico, Cilium).
2. **Service Mesh (Istio):** More advanced — mTLS, L7 routing, retries, observability, and policy. Heavier.
For most teams starting out, Network Policies + a CNI like Calico/Cilium is the right answer. Service mesh for complex microservices fleets."

---

### 🎓 Cloud Support / Architecture Pre-Sales Questions

**Q1: A customer asks "Should we use K8s or ECS?" How do you answer?**
**Ideal answer:** "Five questions:
1. **Are you AWS-only or multi-cloud?** AWS-only → ECS is simpler. Multi-cloud → K8s.
2. **How complex is your deployment?** Simple → ECS. Complex (custom operators, CRDs) → K8s.
3. **Do you have K8s expertise?** No → ECS (or EKS with managed services). Yes → K8s.
4. **Are you already on K8s?** Yes → EKS.
5. **What's the team size?** Small (≤5) → ECS / Fargate (less operational overhead). Large → K8s.
Rule of thumb: ECS for AWS-native, simple, smaller teams. EKS for complex, multi-cloud, large teams."

**Q2: A customer says "we want to use Docker, not K8s." How do you respond?**
**Ideal answer:** "Docker (stand-alone) is fine for development and simple single-container workloads. But for production:
- Multiple containers → need orchestration (Docker Compose, Swarm, K8s, ECS).
- Auto-scaling → need an orchestrator.
- Self-healing → need an orchestrator.
- Multi-host → need an orchestrator.
For a single container, `docker run` on a VM is fine. For 50 microservices, you need a real orchestrator. CompTIA expects you to know that 'Docker' alone isn't enough for production — you need an orchestration layer."

**Q3: A prospect says "our images are too big, builds are slow." How do you help?**
**Ideal answer:** "Image size optimization:
1. **Multi-stage builds:** Use a builder stage with all dev tools, copy only the binary to a slim runtime stage.
2. **Smaller base images:** `alpine` (5 MB), `distroless` (no shell, ~20 MB), `scratch` (empty for Go binaries).
3. **Combine RUN commands:** Each `RUN` adds a layer; combine to reduce layers.
4. **`.dockerignore`:** Exclude `.git`, `node_modules`, `target/`, etc. from the build context.
5. **Layer caching:** Order Dockerfile from least-changed to most-changed for better cache hits.
6. **Image registry caching:** Use BuildKit, Kaniko with layer caching.
7. **Squash layers:** Some build tools can squash final layers.
Result: smaller images = faster pulls, less storage, smaller attack surface."

---

## SECTION 10 — FLASHCARDS

```
Q: What is a container?
A: A lightweight, portable executable package that includes an application and its dependencies. Runs consistently across environments. Shares the host OS kernel.

Q: What is Docker?
A: A popular container runtime that builds, ships, and runs containers. Uses images and containers.

Q: What is a container image?
A: A static, immutable template containing the application code, runtime, libraries, and dependencies. Used to create containers.

Q: What is the difference between a container image and a container?
A: Image = static template (blueprint). Container = running instance of the image.

Q: What is Kubernetes?
A: An open-source container orchestration platform. Automates deployment, scaling, and management of containerized applications.

Q: What is a Kubernetes pod?
A: The smallest deployable unit. Contains 1+ containers that share a network namespace and storage.

Q: What is a Kubernetes Deployment?
A: A controller that manages ReplicaSets for stateless applications. Provides rolling updates and rollbacks.

Q: What is a Kubernetes StatefulSet?
A: A controller for stateful applications. Pods have stable network identities and persistent storage.

Q: What is a Kubernetes Service?
A: A stable network endpoint for a set of pods. Provides load balancing and service discovery.

Q: What is a Kubernetes Ingress?
A: An API object that manages external HTTP/HTTPS access to services. L7 routing.

Q: What is a Kubernetes ConfigMap?
A: A resource for storing non-sensitive configuration as key-value pairs. Mounted as files or env vars.

Q: What is a Kubernetes Secret?
A: A resource for storing sensitive data (passwords, tokens). Base64-encoded (use external secret managers for production).

Q: What is a Kubernetes Namespace?
A: A virtual cluster within a physical cluster. Used for multi-tenancy and resource isolation.

Q: What is a PersistentVolume (PV)?
A: A piece of storage in a K8s cluster (e.g., EBS volume). Provisioned by an admin or dynamically.

Q: What is a PersistentVolumeClaim (PVC)?
A: A request for storage by a pod. K8s binds it to a suitable PV.

Q: What is a StorageClass?
A: A description of a "class" of storage (e.g., "fast SSD"). Enables dynamic provisioning.

Q: What is ephemeral storage?
A: Storage that is tied to the container/pod lifetime. Deleted when the pod dies. Examples: emptyDir, writable layer.

Q: What is persistent storage?
A: Storage that survives container/pod lifetime. Examples: EBS, Azure Disk, Persistent Disk via PV/PVC.

Q: What is the difference between ephemeral and persistent storage?
A: Ephemeral = lost on pod restart. Persistent = survives pod restart.

Q: What is an image registry?
A: A repository for storing, sharing, and versioning container images. Examples: Docker Hub, ECR, ACR, Artifact Registry.

Q: What is port mapping?
A: Mapping a host port to a container port (e.g., -p 8080:80). Allows external access to container services.

Q: What is a container network driver?
A: A networking mode for containers. Examples: bridge (default), host, overlay, macvlan, none.

Q: What is an overlay network?
A: A virtual network that spans multiple hosts. Used in K8s and Swarm for pod-to-pod communication across nodes.

Q: What is a CNI plugin?
A: Container Network Interface — a standard for networking plugins in K8s. Examples: Calico, Cilium, Flannel.

Q: What is CSI?
A: Container Storage Interface — a standard for storage plugins in K8s. Examples: AWS EBS CSI, Azure Disk CSI.

Q: What is a Docker volume?
A: A persistent storage mechanism for Docker. Can be named volumes or bind mounts.

Q: What is OCI?
A: Open Container Initiative — the industry standard for container image formats. Docker images are OCI-compliant.

Q: What is a multi-stage build?
A: A Dockerfile technique that uses multiple `FROM` statements to separate build and runtime environments. Reduces final image size.

Q: What is the difference between Docker Compose and Kubernetes?
A: Compose is for multi-container local dev/test. K8s is for production-grade orchestration across many hosts.

Q: What is Docker Swarm?
A: Docker's built-in orchestrator. Simpler than K8s, but less feature-rich and declining in popularity.

Q: What is ECS?
A: Amazon Elastic Container Service — AWS-native container orchestrator. Simpler than K8s, tight AWS integration.

Q: What is EKS?
A: Amazon Elastic Kubernetes Service — managed Kubernetes on AWS. AWS manages the control plane.

Q: What is AKS?
A: Azure Kubernetes Service — managed Kubernetes on Azure.

Q: What is GKE?
A: Google Kubernetes Engine — managed Kubernetes on GCP. Google's service (they created K8s).

Q: What is FaaS vs CaaS vs PaaS for containers?
A: FaaS = functions (Lambda). CaaS = containers as a service (Fargate, Cloud Run). PaaS = full apps (App Service, App Engine).

Q: What is a sidecar container?
A: A helper container that runs alongside the main container in a pod. Used for logging, proxies, mTLS (service mesh).

Q: What is a service mesh?
A: An infrastructure layer (e.g., Istio, Linkerd) that handles service-to-service communication: discovery, routing, retries, mTLS, observability.

Q: What is kubectl?
A: The K8s command-line tool for managing clusters.

Q: What is a pod's lifecycle?
A: Pending → Running → Succeeded/Failed. Pods are ephemeral and can be restarted, rescheduled, or deleted.

Q: What is a ReplicaSet?
A: A K8s controller that ensures a specified number of pod replicas are running. Used by Deployments.

Q: What is a DaemonSet?
A: A K8s controller that runs one pod per node. For node-level services (logs, monitoring, networking).

Q: What is a Job?
A: A K8s controller that runs a pod to completion. For batch jobs.

Q: What is a CronJob?
A: A K8s controller that schedules Jobs on a cron schedule. For periodic tasks.

Q: What is RBAC in K8s?
A: Role-Based Access Control — defines who can do what. Roles, ClusterRoles, RoleBindings, ClusterRoleBindings.

Q: What is the kubelet?
A: The K8s agent that runs on each worker node. Communicates with the API server and manages pods.

Q: What is etcd?
A: The distributed key-value store used by K8s to store cluster state.

Q: What is the API server in K8s?
A: The frontend for the K8s control plane. All components communicate through it.

Q: What is K8s Network Policy?
A: A specification of how groups of pods are allowed to communicate with each other and other network endpoints.

Q: What is a Volume Snapshot in K8s?
A: A snapshot of a PersistentVolume. Used for backup and restore.

Q: What is the difference between Docker Volume and Kubernetes PVC?
A: Docker Volume is for single-host persistence. PVC is for cluster-wide, multi-host, dynamic provisioning.
```

---

## SECTION 11 — PRACTICE QUESTIONS

### Q1 (Easy)
What is a container?
A) A lightweight VM
B) A portable, standalone executable package with code and dependencies
C) A type of database
D) A network protocol

**Answer: B) A portable, standalone executable package with code and dependencies**
**Explanation:** Containers bundle code + dependencies into a portable unit. They are lighter than VMs (share host OS kernel).
**Why others are wrong:**
- A) Containers are not VMs — they share the host OS.
- C) Not a database.
- D) Not a network protocol.

---

### Q2 (Easy)
What's the difference between a container image and a container?
A) They are the same.
B) Image = template; container = running instance.
C) Image is a VM; container is a process.
D) Image is on-prem; container is in the cloud.

**Answer: B) Image = template; container = running instance.**
**Explanation:** An image is the static blueprint; a container is the running process.
**Why others are wrong:**
- A) Different.
- C) Neither is a VM.
- D) Both can be anywhere.

---

### Q3 (Easy)
What is Kubernetes primarily used for?
A) Building container images
B) Container orchestration (deploying, scaling, managing containers)
C) Storing container images
D) Writing application code

**Answer: B) Container orchestration (deploying, scaling, managing containers)**
**Explanation:** K8s manages containers across clusters — deploy, scale, heal, network, etc.
**Why others are wrong:**
- A) Images are built by Docker / BuildKit.
- C) Images are stored in registries.
- D) Code is written by developers.

---

### Q4 (Medium)
A database is running in a K8s pod. When the pod restarts, the data is lost. What's the most likely cause?
A) The database is not configured correctly.
B) The pod is using ephemeral storage instead of persistent storage.
C) The K8s cluster is broken.
D) The database software is buggy.

**Answer: B) The pod is using ephemeral storage instead of persistent storage.**
**Explanation:** Without a PV/PVC, the pod's data is in the writable layer or emptyDir, both lost on restart. Need a PVC backed by durable storage.
**Why others are wrong:**
- A) Database is fine; the issue is storage.
- C) The cluster works; storage is ephemeral.
- D) Database software is unrelated to storage.

---

### Q5 (Medium)
What is a Kubernetes Service?
A) A type of pod.
B) A stable network endpoint for a set of pods.
C) A storage class.
D) A container image.

**Answer: B) A stable network endpoint for a set of pods.**
**Explanation:** A Service provides a stable IP/DNS name that load-balances to a set of pods.
**Why others are wrong:**
- A) Pods and Services are different resources.
- C) Storage class is for PV/PVC.
- D) Image is in the registry.

---

### Q6 (Medium)
What's the difference between a Deployment and a StatefulSet?
A) Deployment is for stateful apps; StatefulSet for stateless.
B) Deployment is for stateless apps; StatefulSet for stateful apps.
C) They are the same.
D) StatefulSet is older and deprecated.

**Answer: B) Deployment is for stateless apps; StatefulSet for stateful apps.**
**Explanation:** StatefulSet provides stable network identities and per-pod persistent storage — needed for databases.
**Why others are wrong:**
- A) Reversed.
- C) Different resources.
- D) Both are actively used.

---

### Q7 (Medium)
A team wants 10 pods to share the same data. What storage should they use?
A) AWS EBS (RWO)
B) AWS EFS (RWX)
C) Local disk
D) emptyDir

**Answer: B) AWS EFS (RWX)**
**Explanation:** EFS supports ReadWriteMany (RWX) — many pods can mount and write simultaneously. EBS is RWO.
**Why others are wrong:**
- A) EBS is RWO — only one pod at a time.
- C) Local disk is per-node.
- D) emptyDir is per-pod, ephemeral.

---

### Q8 (Hard)
What is the purpose of a multi-stage Docker build?
A) To run multiple containers.
B) To reduce the final image size.
C) To improve security.
D) Both B and C.

**Answer: D) Both B and C.**
**Explanation:** Multi-stage builds use a builder stage (with all dev tools) and copy only the final artifact to a slim runtime stage. Result: smaller image (less storage, faster pulls) and smaller attack surface.
**Why others are wrong:**
- A) Multi-stage is about a single image, not multiple containers.
- B alone is correct but C is also correct.

---

### Q9 (Hard)
A team is running a database in a K8s pod using a PV. The pod is rescheduled to another node. What happens?
A) The database loses its data.
B) The new pod mounts the same PVC and continues.
C) The database fails to start.
D) The PV is re-created.

**Answer: B) The new pod mounts the same PVC and continues.**
**Explanation:** A PVC is decoupled from the pod. The new pod on the new node mounts the same PVC and accesses the data. (For some volume types, the volume must be in the same AZ as the node.)
**Why others are wrong:**
- A) Data is in the PV, not the pod.
- C) Should mount successfully (modulo AZ constraints).
- D) PV is not re-created — it persists.

---

### Q10 (Hard)
What is the difference between a ClusterIP service and a LoadBalancer service?
A) ClusterIP is for internal traffic; LoadBalancer exposes a cloud LB.
B) ClusterIP is for production; LoadBalancer is for dev.
C) They are the same.
D) LoadBalancer is cheaper.

**Answer: A) ClusterIP is for internal traffic; LoadBalancer exposes a cloud LB.**
**Explanation:** ClusterIP is internal-only (default). LoadBalancer provisions a cloud LB for external access. Costs more.
**Why others are wrong:**
- B) Both can be used in any env.
- C) Different.
- D) LoadBalancer costs more (cloud LB).

---

### Q11 (Hard)
A company is using Docker Hub to pull production images. What's the risk?
A) Images are too small.
B) Rate limits, no control, possible CVEs.
C) Docker Hub is free.
D) Nothing — Docker Hub is fine.

**Answer: B) Rate limits, no control, possible CVEs.**
**Explanation:** Docker Hub has rate limits (especially for free tier), you don't control upstream changes, and public images can have vulnerabilities. Use a private registry for production.
**Why others are wrong:**
- A) Size isn't the issue.
- C) Free doesn't mean reliable.
- D) Risky for production.

---

### Q12 (Medium)
What is the K8s control plane?
A) The user interface.
B) The set of master components that manage the cluster (API server, etcd, scheduler, controllers).
C) The worker nodes.
D) The container images.

**Answer: B) The set of master components that manage the cluster (API server, etcd, scheduler, controllers).**
**Explanation:** The control plane makes decisions (scheduling, state, reconciliation). Worker nodes run the actual pods.
**Why others are wrong:**
- A) UI is separate (kubectl / dashboard).
- C) Worker nodes are the data plane.
- D) Images are in the registry.

---

### Q13 (Hard)
A team wants to expose a service on a specific port on each K8s node. What Service type?
A) ClusterIP
B) NodePort
C) LoadBalancer
D) ExternalName

**Answer: B) NodePort**
**Explanation:** NodePort exposes the service on each node's IP at a static port (30000-32767).
**Why others are wrong:**
- A) ClusterIP is internal-only.
- C) LoadBalancer is a cloud LB, not per-node.
- D) ExternalName is a DNS CNAME.

---

### Q14 (Hard)
What's the difference between a Secret and a ConfigMap in K8s?
A) They are the same.
B) Secret is for sensitive data; ConfigMap is for non-sensitive.
C) Secret is encrypted; ConfigMap is not.
D) Secret is newer.

**Answer: B) Secret is for sensitive data; ConfigMap is for non-sensitive.**
**Explanation:** Secrets store sensitive data (passwords, tokens); ConfigMaps store non-sensitive config. Note: K8s Secrets are only base64-encoded by default, NOT encrypted. Use a secret manager (AWS Secrets Manager, etc.) for production.
**Why others are wrong:**
- A) Different.
- C) Secrets are not encrypted by default (this is a common misconception).
- D) Both are mature.

---

### Q15 (Hard)
A team needs to run a pod on every K8s node. What controller?
A) Deployment
B) StatefulSet
C) DaemonSet
D) Job

**Answer: C) DaemonSet**
**Explanation:** DaemonSet ensures one pod per node. Used for node-level services (logging agents, monitoring, CNI).
**Why others are wrong:**
- A) Deployment runs N replicas total, not per node.
- B) StatefulSet is for stateful apps, not per node.
- D) Job runs to completion, not per node.

---

### Q16 (Medium)
What does a Kubernetes Ingress do?
A) Balances TCP traffic.
B) Routes HTTP/HTTPS traffic based on URL or host.
C) Stores secrets.
D) Schedules pods.

**Answer: B) Routes HTTP/HTTPS traffic based on URL or host.**
**Explanation:** Ingress is L7 routing for external HTTP traffic. It can route by path, host, headers.
**Why others are wrong:**
- A) TCP balancing is Service / NodePort / LoadBalancer.
- C) Secrets are stored in Secret resources.
- D) Scheduling is the scheduler's job.

---

### Q17 (Hard)
What's the difference between a Docker volume and a Kubernetes PVC?
A) Docker volumes are for single-host; PVC is for cluster-wide, multi-host.
B) They are the same.
C) PVC is older.
D) Docker volumes are persistent.

**Answer: A) Docker volumes are for single-host; PVC is for cluster-wide, multi-host.**
**Explanation:** Docker volumes are scoped to a single host. PVCs are cluster-wide resources, can be backed by cloud storage, support dynamic provisioning.
**Why others are wrong:**
- B) Different.
- C) PVCs are part of K8s, not a replacement for Docker volumes.
- D) Both can be persistent.

---

### Q18 (Hard)
A team wants to make sure their pods are scheduled on nodes with SSD storage. What K8s feature?
A) NodeSelector / Node Affinity
B) ConfigMap
C) Secret
D) Service

**Answer: A) NodeSelector / Node Affinity**
**Explanation:** NodeSelector and Node Affinity allow you to constrain pods to nodes with specific labels (e.g., `disk=ssd`).
**Why others are wrong:**
- B) ConfigMap is for config, not scheduling.
- C) Secret is for sensitive data.
- D) Service is for networking, not scheduling.

---

### Q19 (Medium)
A team is migrating from Docker Compose to K8s. What's a key difference?
A) Compose is for production; K8s is for dev.
B) Compose is for local multi-container dev; K8s is for production orchestration.
C) They are identical.
D) Compose is newer.

**Answer: B) Compose is for local multi-container dev; K8s is for production orchestration.**
**Explanation:** Compose is a simple tool for defining multi-container apps for development. K8s is the production-grade orchestrator.
**Why others are wrong:**
- A) Reversed.
- C) Different tools.
- D) Compose (2014) is older than K8s (2014, but Compose matured earlier).

---

### Q20 (Hard)
A company wants their pods to NOT run on certain tainted nodes. What K8s feature?
A) Tolerations
B) Taints
C) NodeSelector
D) ConfigMap

**Answer: A) Tolerations**
**Explanation:** Taints repel pods from nodes. Tolerations allow pods to be scheduled on tainted nodes. Pods without tolerations are not scheduled on tainted nodes.
**Why others are wrong:**
- B) Taints are applied to nodes, not pods.
- C) NodeSelector is for matching labels, not tolerating taints.
- D) ConfigMap is for config.

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Container vs VM distinction | 🔴 **CRITICAL** | Foundation. |
| K8s core resources (Pod, Deployment, Service) | 🔴 **CRITICAL** | Direct questions. |
| Persistent vs Ephemeral storage | 🔴 **CRITICAL** | Direct scenario questions. |
| Container image vs container | 🔴 **CRITICAL** | Foundation. |
| Stand-alone vs Orchestrated | 🟠 **HIGH** | Architecture decisions. |
| K8s Service types (ClusterIP, NodePort, LoadBalancer) | 🟠 **HIGH** | Networking. |
| Image registry (public vs private) | 🟠 **HIGH** | Architecture. |
| Port mapping | 🟠 **HIGH** | Networking basics. |
| StatefulSet vs Deployment | 🟠 **HIGH** | Storage decisions. |
| PV/PVC + StorageClass | 🟠 **HIGH** | Storage decisions. |
| Multi-stage builds | 🟠 **HIGH** | Best practice. |
| Managed K8s services (EKS, AKS, GKE) | 🟠 **HIGH** | Cloud-specific. |
| CNI plugins (Calico, Cilium) | 🟡 **MEDIUM** | Networking. |
| CSI drivers | 🟡 **MEDIUM** | Storage. |
| DaemonSet, Job, CronJob | 🟡 **MEDIUM** | Specific use cases. |
| ConfigMap vs Secret | 🟡 **MEDIUM** | Config. |
| Sidecar pattern + Service mesh | 🟡 **MEDIUM** | Modern patterns. |
| ECS, Fargate (vs K8s) | 🟡 **MEDIUM** | AWS-specific. |
| Docker Swarm / Nomad | 🟢 **LOW** | Niche. |
| Docker Compose | 🟢 **LOW** | Dev tool. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-Page Cram Sheet)

### 📋 Cram Sheet: 1.6 Containerization Concepts

**Core Concepts:**

- **Container:** Lightweight, portable package with code + dependencies. Shares host OS kernel.
- **Image:** Static, immutable template. Built from Dockerfile.
- **Container Runtime:** Software that runs containers (Docker, containerd, Podman, CRI-O).
- **Stand-Alone:** Single container, no orchestration. Dev/test.
- **Orchestrated:** K8s / ECS / Swarm / Nomad. Production.

**K8s Resources (Memorize):**

| Resource | Purpose | Stateful? |
|---|---|---|
| **Pod** | Smallest deployable unit (1+ containers) | Ephemeral |
| **Deployment** | Manages stateless ReplicaSets | No |
| **StatefulSet** | Manages stateful pods with stable identity | Yes |
| **DaemonSet** | One pod per node | Node-level |
| **Job / CronJob** | Run to completion / scheduled | No |
| **Service** | Stable network endpoint + LB | N/A |
| **Ingress** | L7 HTTP routing | N/A |
| **ConfigMap** | Non-sensitive config | N/A |
| **Secret** | Sensitive config | N/A |
| **PVC** | Storage request | Persistent |
| **PV** | Actual storage | Persistent |
| **StorageClass** | Storage "tier" (fast/slow/standard) | N/A |

**K8s Service Types:**

- **ClusterIP** (default) — internal only.
- **NodePort** — exposed on each node's IP at a static port.
- **LoadBalancer** — provisions a cloud LB.
- **ExternalName** — DNS CNAME.

**Storage Types:**

| | Ephemeral | Persistent |
|---|---|---|
| **Lifespan** | Container/pod | Independent |
| **Examples** | emptyDir, writable layer | PV/PVC + EBS, Azure Disk, PD |
| **Use case** | Cache, scratch | Database, uploads |
| **Survives restart?** | No | Yes |
| **Exam tip** | "Temporary" | "Survive restart / stateful" |

**Storage Access Modes:**

- **RWO** (ReadWriteOnce) — single node. EBS, Azure Disk, PD.
- **RWX** (ReadWriteMany) — many nodes. EFS, Azure Files, Filestore, NFS.
- **ROX** (ReadOnlyMany) — read-only, many nodes.

**Networking:**

- **Port mapping:** `host:container` (e.g., `-p 8080:80`).
- **Container network drivers:** bridge, host, overlay, macvlan, none.
- **CNI plugin:** Calico, Cilium, Flannel, AWS VPC CNI.
- **Service Mesh:** Istio, Linkerd (mTLS, traffic management, observability).

**Image Registries:**

- **Public:** Docker Hub, ghcr.io, ECR Public, Quay, MCR.
- **Private:** ECR, ACR, Artifact Registry, Harbor, JFrog.
- **Tagging:** Use versions (`v1.0.0`) or digests; avoid `latest` in production.
- **Best practices:** Scan, sign, multi-stage builds, small base images.

**Decision Tree:**

```
Q: Multi-container, production, multi-host?
├── NO  → Stand-alone (Docker / Podman)
└── YES → Orchestrator:
    ├── K8s (multi-cloud, complex)
    ├── ECS / Fargate (AWS-native)
    ├── Cloud Run / Container Apps (serverless)
    └── Swarm / Nomad (niche)

Q: Stateful data?
├── NO  → Ephemeral (emptyDir, writable layer)
└── YES → Persistent (PV/PVC + EBS/Azure Disk/PD)

Q: Multi-pod shared data?
├── NO  → RWO (EBS, Azure Disk)
└── YES → RWX (EFS, Azure Files, Filestore)

Q: Public image?
├── YES → Docker Hub, ghcr.io
└── NO  → Private (ECR, ACR, Artifact Registry)
```

**Acronyms to Know Cold:**

- **IaaS / PaaS / SaaS / FaaS** = (1.1)
- **RTO / RPO** = (1.2)
- **VPC / TGW / NACL / SG / WAF** = (1.3)
- **IOPS / SSD / HDD** = (1.4)
- **MS / EDA / Pub-Sub / Fan-Out** = (1.5)
- **K8s** = Kubernetes
- **Pod** = Smallest K8s deployable unit
- **PV / PVC** = PersistentVolume / PersistentVolumeClaim
- **CNI** = Container Network Interface
- **CSI** = Container Storage Interface
- **OCI** = Open Container Initiative
- **RWO / RWX / ROX** = ReadWriteOnce / ReadWriteMany / ReadOnlyMany
- **EBS / EFS** = AWS Elastic Block Store / Elastic File System
- **ACR / ECR / GCR** = Azure / AWS / Google Container Registry
- **ECS / EKS / AKS / GKE** = Container orchestrators
- **FaaS / CaaS / PaaS** = Functions/Containers/Platform as a Service
- **RBAC** = Role-Based Access Control
- **CRD** = Custom Resource Definition

**Top Exam Triggers:**

- "Database / stateful" → **PV/PVC + StatefulSet**
- "Cache / temporary" → **Ephemeral (emptyDir)**
- "Multi-pod shared data" → **RWX storage (EFS, NFS)**
- "Single-pod high-IOPS" → **RWO block + SSD**
- "Production multi-container" → **Orchestrator (K8s / ECS)**
- "Dev / single container" → **Stand-alone (Docker)**
- "Service discovery in K8s" → **Service + CoreDNS**
- "Stable network for pods" → **K8s Service**
- "L7 HTTP routing" → **Ingress**
- "Image vulnerability" → **Scan in CI/CD**
- "Build small images" → **Multi-stage + alpine/distroless**

---

## SECTION 14 — LATEST INDUSTRY UPDATES (CV0-004 Relevant)

### AWS Updates (2024–2025)
- **EKS Auto Mode** (2024) — Combines EKS managed control plane with Karpenter + auto-scaling. Easiest K8s setup.
- **EKS on Fargate** — Serverless K8s nodes (no EC2 management).
- **ECS Service Connect** (2023) — Service mesh-like features for ECS (service discovery, traffic mgmt, observability) without sidecars.
- **ECS Anywhere** — Run ECS on on-prem servers.
- **Fargate** continues to grow as the serverless container option.
- **AWS App Runner** — PaaS for containerized web apps and APIs.
- **ECR** now supports **KMS encryption** for images, immutable tags, pull-through cache.
- **EBS CSI driver** continues to be the standard for K8s persistent storage on AWS.
- **EFS** supports **Elastic Throughput** mode (no provisioning).
- **Bottlerocket** (2020+) — AWS's container-optimized OS. Used in EKS, ECS.
- **Firecracker** (serverless microVMs) — Underlies Lambda and Fargate.

### Azure Updates (2024–2025)
- **AKS Automatic** (2024) — Fully managed AKS with built-in best practices (auto-scaling, security, monitoring). Reduces ops.
- **AKS Arc** — AKS on hybrid/edge.
- **Container Apps** — Serverless containers with KEDA, Dapr, mTLS. Continues to grow.
- **Azure Service Mesh** — Istio-based, GA.
- **ACR** — Geo-replication, tasks (build in ACR), content trust, premium tier with private endpoints.
- **Azure CNI Overlay** (2023) — Reduces IP consumption in AKS.
- **Confidential Containers** (2024) — Encrypted memory for sensitive workloads in AKS.

### Google Cloud Updates (2024–2025)
- **GKE Autopilot** — Fully managed K8s (Google manages nodes, you manage pods).
- **GKE Enterprise** (2024) — Replaces Anthos. Multi-cluster, hybrid, edge.
- **Cloud Run** — Serverless containers with GPU support (2024). 
- **Artifact Registry** — Multi-format (container images, language packages, OS packages). Replaced GCR.
- **Cloud Build** — Serverless CI/CD. Builds, scans, deploys.
- **GKE Sandbox** (gVisor) — Extra isolation for untrusted workloads.
- **KRM** (KRM) — K8s Resource Model for cloud resources.

### Kubernetes Updates (CNCF, 2024–2025)
- **Kubernetes 1.29, 1.30, 1.31** (2024) — Continued improvements to sidecar containers, structured authorization, ephemeral containers.
- **Gateway API** (2023+) — Successor to Ingress. More expressive, multi-protocol.
- **Sidecar containers** (GA in 1.29) — Native sidecar support (was previously a hack with shared lifecycle).
- **Structured Authorization** (1.30) — More flexible auth policies.
- **Kustomize, Helm** — Standard K8s templating tools.
- **Operators** — Custom controllers for app-specific automation (e.g., PostgreSQL Operator).
- **Crossplane** — K8s-native infrastructure provisioning.
- **Cluster API** — Declarative K8s cluster lifecycle.

### Container Runtimes
- **containerd** is the dominant runtime (used by Docker and K8s).
- **Podman** continues to grow (daemonless, rootless).
- **CRI-O** is the default in OpenShift.
- **WASM (WebAssembly)** containers are emerging (Wasmtime, WasmEdge) — sub-millisecond startup, smaller footprint.

### Container Security
- **Image signing** (Cosign, Notary) is becoming standard.
- **SLSA** framework for supply chain security.
- **Trivy, Snyk, Grype** — Image vulnerability scanners.
- **Falco, Tetragon** — Runtime security (eBPF-based).
- **Distroless images** — Google maintains, used in production.
- **Confidential containers** — Encrypted memory (Azure, AWS, GCP all working on this).

### Industry Trends
- **Serverless containers** (Cloud Run, Container Apps, Fargate) continue to grow — easier than K8s for many use cases.
- **K8s adoption** continues to grow (96% of organizations report using K8s, CNCF 2024 survey).
- **Platform engineering** — Internal developer platforms built on K8s.
- **GitOps** — Argo CD, Flux — declarative K8s deployments from Git.
- **Service mesh** adoption is increasing but still selective.
- **AI/ML on K8s** — Kubeflow, KServe, Ray for ML training and serving.
- **Edge K8s** — K3s, KubeEdge, MicroK8s for edge deployments.

### AI / ML Impact
- **Cloud Run with GPU** — Serverless AI inference.
- **K8s + GPU operators** — NVIDIA GPU Operator for K8s, manages GPU drivers and runtime.
- **Vector databases on K8s** — pgvector, Milvus, Weaviate deployed on K8s.
- **ML training on K8s** — Kubeflow Training Operator, Ray on K8s.
- **LLM serving** — vLLM, TGI, Triton on K8s with GPU support.

---

## ✅ END OF OBJECTIVE 1.6 NOTES

**Coverage Summary:**
- ✅ Stand-alone containers
- ✅ Workload orchestration (K8s, ECS, Cloud Run, etc.)
- ✅ Networking (port mapping, network drivers, CNI)
- ✅ Storage types (ephemeral vs persistent, PV/PVC, access modes)
- ✅ Image registries (public vs private, OCI, best practices)

**Next step:** Ping me for **1.7 — Virtualization Concepts** (stand-alone, clustering, cloning, host affinity, hardware pass-through, overlay/VM networks, local/SAN storage) — directly related to 1.6 (containers) but focused on VMs.

Study tip from Cikgu Danial: *"For 1.6, the K8s resource model is the key. Memorize Pod → Deployment → Service → Ingress as the 'web app stack,' and StatefulSet → PVC → PV → StorageClass as the 'database stack.' If you can draw both stacks and explain how they interact, you own 1.6."* 💪
