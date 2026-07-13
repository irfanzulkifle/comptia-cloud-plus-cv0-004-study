## SECTION 1 — Exam Objectives

- **Objective 1.6:** Compare and contrast containerization concepts — part of Domain 1.0 (Cloud Architecture) of the CompTIA Cloud+ CV0-004 exam.
- **Stand-alone** — a single container running on one host, started manually (e.g., `docker run`), with no orchestration.
- **Workload orchestration** — automated management of many containers across many hosts (Kubernetes, ECS, Swarm).
- **Networking (Port mapping)** — how containers reach each other and the outside world, including mapping a host port to a container port.
- **Storage types** — **Persistent volumes** (survive container restart) versus **Ephemeral storage** (lost when the container dies).
- **Image registries** — centralized stores for building, versioning, and sharing container images.
- **Why it matters** — containers are the default deployment model for cloud-native apps; a wrong storage or networking choice breaks microservices, so Cloud+ expects you to tell these concepts apart and match them to the right scenario.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Stand-Alone Containers

- **Definition:** A stand-alone container is a single, isolated application instance running on one host, launched and managed manually by a container runtime such as Docker Engine, containerd, Podman, or CRI-O. There is no orchestration layer, no auto-scaling, and no self-healing.
- **Why it matters:** Stand-alone containers are the simplest way to run packaged software and are where every learner starts. They isolate an app and its dependencies into one portable unit that runs identically on a laptop, a server, or the cloud. The exam tests that you know the limit of "stand-alone" — it does not recover on its own and does not scale automatically.
- **Analogy:** A stand-alone container is like a single food truck parked on a corner. It serves customers fine, but if it breaks down or gets crowded, nobody automatically sends a second truck or restarts the first one.
- **Example:** A developer runs `docker run -d -p 8080:80 nginx` to serve a test website locally. The container is started by hand; if it crashes, it stays down until someone restarts it. This is perfect for dev/test but not for production HA.

### 2.2 — Workload Orchestration

- **Definition:** Workload orchestration is the automated management of many containers across a cluster of hosts. A control plane tracks the "desired state" (for example "3 replicas of nginx") and continuously reconciles the actual state, restarting failed containers, rescheduling them onto healthy nodes, and scaling replicas up or down.
- **Why it matters:** Orchestration delivers high availability, horizontal scaling, rolling updates, rollbacks, service discovery, and load balancing — capabilities a stand-alone container lacks. Kubernetes is the de facto standard; cloud providers offer managed versions (Amazon EKS, Azure AKS, Google GKE). The exam expects you to recognize orchestration scenarios and name the right tool.
- **Analogy:** If a stand-alone container is one food truck, orchestration is a city-wide food-truck dispatch system that sends replacements when one breaks, spreads trucks to where demand is high, and routes customers automatically.
- **Example:** A Deployment declares 3 nginx replicas. When a node fails, the scheduler reschedules its pod elsewhere, and a Service keeps a stable IP so clients never notice. Tools include Kubernetes, Amazon ECS, and Docker Swarm.

### 2.3 — Networking (Port Mapping)

- **Definition:** Container networking lets containers talk to each other, to the host, and to the outside world. Port mapping is the specific mechanism that maps a **host port** to a **container port** using the `host:container` format, such as `-p 8080:80`.
- **Why it matters:** Containers each receive their own isolated network namespace and IP. To expose a service, you map an external host port to the internal container port. If two containers both listen on container port 80, you give them different host ports (8081, 8082). In Kubernetes this is handled by Services (ClusterIP, NodePort, LoadBalancer) and Ingress rather than manual `-p` flags.
- **Analogy:** Port mapping is like a hotel front desk: guests (external users) ask for room 8080, and the desk forwards them to the actual room 80 inside where the service lives.
- **Example:** `docker run -p 8080:80 nginx` means external clients hit the host on 8080, which forwards to the container's web server on 80. In K8s, a LoadBalancer Service exposes pods to the internet and kube-proxy maintains the forwarding rules.

### 2.4 — Storage Types (Persistent vs Ephemeral)

- **Definition:** **Ephemeral storage** is tied to the container or pod lifecycle and is destroyed when the container stops — for example the container writable layer or a Kubernetes `emptyDir`. **Persistent volumes** live independently of any single container; a pod can be deleted and a new pod can re-mount the same data (Kubernetes PV/PVC, AWS EBS/EFS).
- **Why it matters:** Choosing wrong causes data loss. Databases, user uploads, and message queues need persistent storage; caches, scratch space, and build artifacts are fine as ephemeral. The exam frequently gives a scenario and asks which storage type fits.
- **Analogy:** Ephemeral storage is a whiteboard erased at the end of the meeting; persistent storage is a filing cabinet that stays even after the meeting room is emptied and reassigned.
- **Example:** A Postgres database pod mounts a PersistentVolumeClaim backed by EBS so its data survives pod restarts. A CI build job writes intermediate files to an `emptyDir` that vanishes when the pod completes.

### 2.5 — Image Registries

- **Definition:** An image registry is a centralized repository that stores, versions, and shares container images. Developers build and push images (`docker push`); orchestrators and runtimes pull them (`docker pull` or `kubectl`). Images are made of deduplicated layers and identified by tags (v1.0) or immutable digests (sha256).
- **Why it matters:** Registries are the distribution hub of container workflows and integrate with CI/CD, access control, and vulnerability scanning. Public registries (Docker Hub) and private ones (Amazon ECR, Azure ACR, Google Artifact Registry) both appear on the exam.
- **Analogy:** A registry is like GitHub for container images — a versioned library you push to and pull from instead of rewriting the app every time.
- **Example:** A CI pipeline builds `myapp:v1.2`, pushes it to Amazon ECR, and an ECS task definition pulls that exact tag to deploy. Scanners in the registry flag vulnerable base images before they reach production.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Local development with a stand-alone container.** A developer builds a Python API and runs it locally with `docker run -p 5000:5000 myapi:dev` to validate behavior before committing. No orchestration is needed; the container is started and stopped by hand. This is the classic stand-alone use case: fast, isolated, disposable.
- **Scenario 2 — Production microservices on Kubernetes (EKS).** An e-commerce company runs its catalog, cart, and checkout services as Kubernetes Deployments on Amazon EKS. The orchestrator keeps 3 replicas of each, restarts failed pods, and exposes them through a LoadBalancer Service plus Ingress. Persistent data for the catalog service lives on an EBS-backed PersistentVolumeClaim, while temporary session caches use ephemeral `emptyDir`.
- **Scenario 3 — Serverless containers with AWS Fargate + ECR.** A media company processes uploads with batch jobs. Each job pulls an image from Amazon ECR and runs as a Fargate task on ECS — no EC2 hosts to manage. Port mapping is handled by the task definition, and output is written to an EFS file system so results persist across tasks.
- **Scenario 4 — Multi-tenant SaaS with shared persistent storage.** A SaaS app runs many pods that all need to read the same static assets. It mounts an Amazon EFS volume with ReadWriteMany access so every pod can share the files, while each pod's own logs go to ephemeral storage and are shipped to a logging service.
- **Scenario 5 — CI/CD image promotion through registries.** A team builds images in CI, pushes them tagged by git commit to a private registry, scans them for vulnerabilities, and promotes only clean images to production. The registry is the single source of truth that both staging and production pull from.

---

## SECTION 4 — COMPARISON TABLES

**Table 1 — Stand-Alone vs Orchestrated**

| Aspect | Stand-Alone | Orchestrated (K8s/ECS) |
|---|---|---|
| Management | Manual (`docker run`) | Declarative, automated |
| Scaling | None / manual | Auto horizontal scaling |
| Self-healing | No | Yes (restart, reschedule) |
| Updates | Stop/start by hand | Rolling update + rollback |
| Service discovery | Manual IPs | Automatic DNS-based |
| Best for | Dev/test, single tool | Production microservices |

**Table 2 — Persistent vs Ephemeral Storage**

| Aspect | Persistent Volume | Ephemeral Storage |
|---|---|---|
| Lifetime | Outlives container/pod | Tied to container/pod |
| Survives restart | Yes | No |
| Examples | EBS, EFS, PV/PVC | Writable layer, emptyDir |
| Sharing | RWX possible | Per-pod only |
| Cost | Higher | Low/free |
| Use case | DB, uploads | Cache, scratch |

**Table 3 — Container Network Access Patterns**

| Pattern | Scope | Example |
|---|---|---|
| Bridge + port map | Single host | `docker -p 8080:80` |
| Overlay | Multi-host | Swarm, K8s CNI |
| ClusterIP | In-cluster only | Internal microservice |
| NodePort | Node IP + port | Dev/test external |
| LoadBalancer | Cloud LB | Production external |
| Ingress | L7 HTTP routing | Web apps |

**Table 4 — Public vs Private Image Registries**

| Type | Example | Use |
|---|---|---|
| Public | Docker Hub | Shared official images |
| Public | ECR Public | AWS public images |
| Private | Amazon ECR | AWS-native private |
| Private | Azure ACR | Azure-native private |
| Private | Artifact Registry | GCP-native private |

**Table 5 — Storage Access Modes**

| Mode | Meaning | Example |
|---|---|---|
| RWO | Read-write by one node | AWS EBS |
| ROX | Read-only by many | Static assets |
| RWX | Read-write by many | EFS, NFS |
| RWOP | Read-write by one pod | Block storage |

---

## SECTION 5 — AWS PROVIDER MAPPING

This objective maps to AWS services as follows (AWS-only framing for the exam):

| Concept | AWS Service / Mapping |
|---|---|
| Stand-alone | ECS on EC2 or AWS Fargate running a single task (no cluster orchestration logic needed) |
| Workload orchestration | Amazon ECS (AWS-native orchestration) or Amazon EKS (managed Kubernetes) |
| Networking — Port mapping | Container port mapped to host port; in ECS the task definition maps `containerPort` → `hostPort`, in EKS a Service/Ingress handles exposure |
| Persistent volumes | Amazon EBS (block, RWO) or Amazon EFS (file, RWX) mounted via PersistentVolumeClaim / ECS volume |
| Ephemeral storage | The container's writable layer and the task/pod's ephemeral disk; `emptyDir` in EKS, task storage in ECS |
| Image registries | Amazon ECR (Elastic Container Registry) — private and public repositories for pushing/pulling images |

**Quick memory aid:** Stand-alone → Fargate single task; Orchestration → ECS/EKS; Ports → container port → host port; Persistent → EBS/EFS; Ephemeral → container layer; Registry → ECR.

---

## SECTION 6 — PRACTICE QUESTIONS

1. A developer runs a single container by hand with `docker run` and no orchestration. Which term best describes this?
   A. Workload orchestration
   B. Stand-alone container
   C. Persistent volume
   D. Service mesh
   Answer: B — A manually started single container with no orchestration is a stand-alone container.

2. Which technology is the de facto standard for workload orchestration?
   A. Docker Compose
   B. Kubernetes
   C. VirtualBox
   D. Ansible
   Answer: B — Kubernetes is the industry-standard container orchestrator.

3. The command `docker run -p 8080:80 nginx` maps which port to which?
   A. Container 8080 to host 80
   B. Host 8080 to container 80
   C. Host 80 to container 8080
   D. Container 80 to host 8080 (reversed)
   Answer: B — The host port (8080) forwards to the container port (80) in `host:container` form.

4. Which storage type survives a container or pod restart?
   A. Ephemeral writable layer
   B. emptyDir
   C. Persistent volume
   D. tmpfs
   Answer: C — Persistent volumes outlive the container/pod lifecycle.

5. Which Kubernetes object represents storage that survives pod deletion?
   A. emptyDir
   B. ConfigMap
   C. PersistentVolumeClaim
   D. Secret
   Answer: C — A PVC binds to durable storage that persists beyond the pod.

6. In AWS, which service provides a private container image registry?
   A. Amazon S3
   B. Amazon ECR
   C. AWS Lambda
   D. Amazon RDS
   Answer: B — Amazon ECR is AWS's managed container image registry.

7. Which AWS storage option supports ReadWriteMany access for sharing files across pods?
   A. Amazon EBS
   B. Amazon EFS
   C. Instance store
   D. S3 bucket mount
   Answer: B — EFS is a file system that supports RWX shared access.

8. Which AWS service runs containers without managing EC2 instances?
   A. ECS on EC2
   B. AWS Fargate
   C. EC2 Auto Scaling
   D. AWS Batch
   Answer: B — Fargate is serverless compute for containers (no host management).

9. What is the primary benefit of workload orchestration over stand-alone containers?
   A. Lower cost always
   B. Self-healing and auto-scaling
   C. No YAML needed
   D. Faster single startup
   Answer: B — Orchestration adds self-healing, scaling, and declarative management.

10. Which storage is appropriate for a database that must keep data after pod restarts?
    A. emptyDir
    B. Ephemeral layer
    C. Persistent volume (EBS)
    D. tmpfs
    Answer: C — Databases require persistent storage such as an EBS-backed PV.

11. A Kubernetes Service of type LoadBalancer does what?
    A. Routes internal pod-to-pod traffic only
    B. Provisions a cloud load balancer for external access
    C. Maps host port to container port
    D. Stores container images
    Answer: B — LoadBalancer provisions an external cloud load balancer.

12. Which registry is the largest public source of official images like nginx and postgres?
    A. Amazon ECR
    B. Azure ACR
    C. Docker Hub
    D. GitHub Container Registry
    Answer: C — Docker Hub is the largest public registry with many official images.

13. In Kubernetes, what is the smallest deployable unit that can contain one or more containers?
    A. Node
    B. Pod
    C. Cluster
    D. Volume
    Answer: B — A Pod is the smallest deployable unit in Kubernetes.

14. Which network driver lets Docker containers on different hosts communicate?
    A. bridge
    B. host
    C. overlay
    D. none
    Answer: C — overlay networks span multiple Docker hosts.

15. An image identified by `nginx@sha256:abc...` uses which identifier?
    A. Tag
    B. Digest
    C. Layer
    D. Manifest name
    Answer: B — A sha256 digest is the immutable content-addressed identifier.

16. Which ECS launch type is best described as "stand-alone" style with no cluster host management?
    A. ECS on EC2
    B. AWS Fargate single task
    C. EKS managed node group
    D. EC2 Spot Fleet
    Answer: B — Fargate single task runs a container without managing hosts.

17. What does a Kubernetes Ingress provide?
    A. Block storage
    B. L7 HTTP path/host routing to Services
    C. Ephemeral scratch space
    D. Image vulnerability scanning
    Answer: B — Ingress performs Layer-7 HTTP routing to Services.

18. Which access mode allows a volume mounted read-write by a single node only?
    A. RWX
    B. ROX
    C. RWO
    D. RWOP
    Answer: C — ReadWriteOnce (RWO) is single-node read-write.

19. Why would a CI build job use ephemeral storage?
    A. To keep artifacts forever
    B. For temporary intermediate files that can be discarded
    C. To share with other tenants
    D. For database writes
    Answer: B — Ephemeral storage suits temporary, discardable build artifacts.

20. In the AWS mapping, persistent volumes correspond to which services?
    A. EBS and EFS
    B. S3 and Glacier
    C. DynamoDB and RDS
    D. CloudWatch and CloudTrail
    Answer: A — Persistent container storage maps to Amazon EBS (block) and EFS (file).
