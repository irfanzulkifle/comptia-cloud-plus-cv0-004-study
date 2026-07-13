---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 1.7: Virtualization"
aliases: [Objective-1.7-Virtualization, Domain-1.7-Virtualization]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 1.7
status: active
created: 2026-06-12
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-1.7, virtualization, hypervisor, vm, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 1.0 Cloud Architecture
## 📘 Objective 1.7: Virtualization Concepts

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 1.0 Domain = 23% of total exam
> **Objective 1.7 Focus:** The VM counterpart to 1.6 (containers) — how virtual machines are managed, made highly available, and connected to storage and networks.

---

## SECTION 1 — Exam Objectives

- **Objective number:** 1.7 — *Compare and contrast virtualization concepts.*
- **What this objective means:** Virtualization is the foundation of cloud computing. A hypervisor (VMware ESXi, Microsoft Hyper-V, KVM, Xen) lets one physical server run many virtual machines (VMs), each acting like an independent computer.
- **Concepts covered:**
  - **Stand-alone VMs:** A single VM on a single host (no HA).
  - **Clusters:** Multiple hosts working together (HA, live migration).
  - **Cloning:** Making copies of VMs for fast deployment.
  - **Host affinity rules:** "Run these VMs together" or "keep them apart."
  - **Hardware pass-through:** Direct access to physical hardware (GPU, NVMe) for performance.
  - **VM networks:** Virtual switches inside a hypervisor; overlay networks that span hosts.
  - **Storage:** Where VM disks live (local disk, SAN, NAS).
- **Exam focus:** Whether you understand the building blocks that make cloud VMs work — and how to design for HA, performance, and efficiency.
- **Why it matters in the real world:**
  - Without virtualization there is no cloud — every major cloud runs on hypervisors.
  - Wrong storage choice = slow VMs or data loss; wrong network design = isolated workloads.
  - Clustering and affinity rules decide whether an app survives a host failure.
- **Why CompTIA tests it:**
  - Virtualization is the most fundamental cloud skill in Domain 1.
  - Decisions here ripple into security, operations, and troubleshooting domains.
  - PBQs sometimes involve placing VMs on hosts or choosing storage/network types.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Stand-Alone Virtual Machines
- **Definition:** A stand-alone VM is a single virtual machine running on a single physical host with no clustering, HA, or shared resource pool — managed by itself.
- **Why it matters:** Simplest, baseline deployment — cheap and easy but offers no protection if the host fails; the opposite of clustered/HA.
- **Analogy:** A single food truck on one corner — if the truck breaks down, service stops completely (a cluster is a fleet of trucks).
- **Example:** One Ubuntu VM on a lab ESXi host; if the host loses power, the VM goes down with nothing to automatically recover it.

### 2.2 — Clustering
- **Definition:** A cluster is a group of physical hosts managed together as one resource pool, with HA, live migration (vMotion/Live Migration), and DRS — VMs restart on another host if one fails.
- **Why it matters:** How you achieve uptime and load balancing; HA restarts VMs after a host failure, live migration moves running VMs with no downtime for maintenance.
- **Analogy:** A team of cooks sharing one kitchen — if one cook faints, another picks up their station without the customer noticing.
- **Example:** A 4-node vSphere cluster runs 40 VMs; live-migrate one host's VMs, patch it, bring it back — zero downtime.

### 2.3 — Cloning
- **Definition:** Cloning makes an exact copy of a VM (disk, config, optionally identity); a full clone is independent, a linked clone shares the parent disk. Templates are the "golden image" source.
- **Why it matters:** Enables fast, consistent deployment of many identical VMs; distinct from templating (deploy from master) and snapshots (point-in-time, not deployable).
- **Analogy:** Photocopying a finished form — instant duplicate; a template is the original blank master you keep re-copying.
- **Example:** Configure one web server, convert to template, clone 10 identical web servers in minutes for a load-balanced tier.

### 2.4 — Host Affinity (and Anti-Affinity)
- **Definition:** Affinity rules control VM placement — affinity keeps specified VMs on the same host (low latency, licensing); anti-affinity keeps them on separate hosts (survive a single host failure).
- **Why it matters:** Placement rules affect availability and performance; anti-affinity for HA, affinity for license-bound or chatty VMs.
- **Analogy:** Affinity seats best friends at the same table; anti-affinity splits them across classrooms so a fire in one room doesn't take both out.
- **Example:** Anti-affinity keeps primary and standby domain controllers on different physical hosts — a single host crash can't kill both.

### 2.5 — Hardware Pass-Through (PCIe/DirectPath)
- **Definition:** Hardware pass-through (PCI passthrough, DirectPath I/O, SR-IOV) gives a VM direct, exclusive access to a physical device (GPU, NIC, NVMe) bypassing the hypervisor's virtualized layer.
- **Why it matters:** Removes virtualization overhead for maximum performance (GPUs, ultra-low-latency); trade-off — device can't be shared and live migration usually breaks.
- **Analogy:** Handing one guest the physical key to a sports car instead of a shared rental — fastest, but only that guest can drive it.
- **Example:** An ML VM gets a passthrough NVIDIA GPU for near-native training speed; the GPU can't be used by any other VM on that host.

### 2.6 — Network Types: Overlay Networks
- **Definition:** An overlay network is a logical network built on top of the physical underlay using encapsulation (VXLAN, GRE, NVGRE), letting VMs on different hosts appear on the same L2 segment.
- **Why it matters:** Decouples network addressing from hardware, enabling seamless cross-host migration and multi-tenant isolation; contrasts with local VM networks.
- **Analogy:** A private walkie-talkie channel layered over the public radio spectrum — users talk as if adjacent regardless of physical distance.
- **Example:** VXLAN overlay lets a VM keep its IP/subnet while live-migrating from rack A to rack B with no reconfiguration.

### 2.7 — Network Types: VM Networks (Virtual Switches)
- **Definition:** A VM network is the virtual switching fabric inside a hypervisor (vSwitch/virtual bridge) connecting VMs to each other and to physical NICs; port groups define VLANs and security policy.
- **Why it matters:** How VMs get connectivity, isolation, and traffic policy; the exam asks about vSwitches, port groups, VLAN tagging, and how it differs from an overlay.
- **Analogy:** A building's internal phone exchange — routing calls between offices before they ever hit the outside line.
- **Example:** A "Web" port group tagged VLAN 100 on a vSwitch; all web-tier VMs plug in, isolated from the "DB" port group on VLAN 200.

### 2.8 — Storage: Local
- **Definition:** Local storage is disk physically attached to the host (SATA, SAS, NVMe); VM disks live on that host's drives — fast and cheap but not shareable and no HA.
- **Why it matters:** Simplest and lowest-latency but breaks clustering and live migration (the disk doesn't follow the VM); local = single-host, non-redundant at the shared level.
- **Analogy:** A toolbox bolted to one workbench — great if you never move, useless if the bench is shared.
- **Example:** A stand-alone VM stores its disk on the host's NVMe; migrating it requires copying the disk file first.

### 2.9 — Storage: SAN (Storage Area Network)
- **Definition:** A SAN is a dedicated high-speed block-storage network (Fibre Channel or iSCSI) providing raw block devices to hosts; VMs see LUNs as local disks — shared, fast, supports central snapshots/cloning.
- **Why it matters:** Enables clustering, HA, and live migration because any host can attach the same LUN; contrasts SAN (block) with NAS (file).
- **Analogy:** A shared bank vault every teller can open — the data follows the customer anywhere in the branch.
- **Example:** vSphere hosts connect via iSCSI to a SAN; a VM's VMDK sits on a shared LUN so it can live-migrate between hosts instantly.

### 2.10 — Storage: NAS (Network-Attached Storage)
- **Definition:** A NAS is file-level storage accessed over a standard network (NFS, SMB/CIFS); hosts mount it as a share/folder, not a raw disk — good for files, ISOs, some VM storage, higher latency than SAN.
- **Why it matters:** Simpler and cheaper than SAN, great for unstructured files, but file-based (not block) so not ideal for high-IOPS databases; NAS vs SAN: file vs block.
- **Analogy:** A shared network drive / Dropbox everyone mounts — you read and write files, you don't format raw sectors.
- **Example:** A KVM host mounts an NFS NAS share for VM ISO images and backups; multiple hosts read the same ISO library simultaneously.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Scenario 1 — Three-Tier Web App on a vSphere Cluster (On-Prem Private Cloud):** A company runs a web → app → database stack on a 6-node ESXi cluster backed by a Fibre Channel SAN. Anti-affinity rules keep primary/standby DB VMs apart; HA restarts any VM if a host dies; DRS balances load. Local NVMe on each host is reserved only for scratch/temp, while all persistent disks live on the SAN so live migration works.
- **Scenario 2 — GPU VDI Farm with Hardware Pass-Through:** A design firm deploys virtual desktops that need 3D acceleration. Each host passes through an NVIDIA GPU directly to a VDI VM (DirectPath I/O). Affinity pins each graphics-heavy VM to its GPU host. Because pass-through blocks live migration, HA is configured to restart (not migrate) those VMs on another GPU host after a failure.
- **Scenario 3 — Multi-Tenant Overlay Network in a Public Cloud Region:** A cloud provider uses VXLAN overlays so hundreds of customer VPCs share the same physical fabric but stay isolated. A customer's VM live-migrates across racks during maintenance; the overlay keeps its private IP and security groups intact with no tenant reconfiguration.
- **Scenario 4 — Rapid Scale-Out via Cloning from a Template:** An e-commerce site expects Black Friday traffic. Ops clones 20 web-server VMs from a hardened gold template in minutes, places them behind a load balancer, and uses affinity to spread them across hosts. After the event, excess clones are deleted — far faster than building each VM by hand.
- **Scenario 5 — Branch Office File Services on NAS:** A distributed enterprise mounts an NFS/SMB NAS at each region for shared documents and VM backup repositories. Block-heavy databases stay on the SAN; file shares and ISO libraries use NAS because file-level access over the LAN is simpler and cheaper than provisioning LUNs.

---

## SECTION 4 — COMPARISON TABLES

### Table 4.1 — SAN vs NAS
| Attribute | SAN (Storage Area Network) | NAS (Network-Attached Storage) |
|---|---|---|
| Access level | Block (raw disks/LUNs) | File (shares/folders) |
| Protocols | Fibre Channel, iSCSI | NFS, SMB/CIFS |
| Appears to host as | A local physical disk | A mounted network folder |
| Best for | Databases, VM disks, high IOPS | File sharing, ISOs, backups |
| Performance | Very high, low latency | Good, higher latency |
| Cost/complexity | High (dedicated fabric) | Lower (uses standard LAN) |
| Live migration support | Yes (shared LUN) | Yes (shared mount), slower |

### Table 4.2 — Overlay Network vs VM (Local) Network
| Attribute | Overlay Network | VM Network (vSwitch) |
|---|---|---|
| Scope | Spans hosts/racks (logical) | Local to one host (unless extended) |
| Encapsulation | Yes (VXLAN/GRE/NVGRE) | No (native L2) |
| Enables | Cross-host L2, tenant isolation | Intra-host VM connectivity |
| VM migration | Seamless across hosts | Limited to host without overlay |
| Example | VXLAN-based VPC | vSphere vSwitch port group |

### Table 4.3 — Local vs SAN vs NAS Storage
| Attribute | Local | SAN | NAS |
|---|---|---|---|
| Location | Inside the host | Shared block network | Shared file network |
| Shareable across hosts | No | Yes | Yes |
| HA / live migration | No | Yes | Yes (slower) |
| Latency | Lowest | Low | Higher |
| Use case | Scratch, stand-alone | Production VM disks | Files, ISOs, backups |

### Table 4.4 — Stand-Alone vs Clustered VMs
| Attribute | Stand-Alone | Clustered |
|---|---|---|
| Hosts involved | One | Many (pool) |
| High availability | None | HA restarts on failure |
| Live migration | No | Yes |
| Resource balancing | Manual | DRS/automated |
| Cost/complexity | Low | Higher |

### Table 4.5 — Affinity vs Anti-Affinity
| Rule | Effect | Typical use |
|---|---|---|
| Affinity | Keep VMs on same host | Low-latency pairs, licensing |
| Anti-affinity | Keep VMs on different hosts | HA, redundancy (DB replicas) |

### Table 4.6 — Virtual Hardware vs Hardware Pass-Through
| Attribute | Virtual Hardware | Hardware Pass-Through |
|---|---|---|
| Access | Emulated via hypervisor | Direct to physical device |
| Sharing | Can be shared | Exclusive to one VM |
| Performance | Some overhead | Near-native |
| Live migration | Supported | Usually blocked |
| Example use | General VMs | GPU, NVMe, fast NIC |

---

## SECTION 5 — AWS PROVIDER MAPPING

> AWS-ONLY mapping for Objective 1.7 virtualization concepts. Use this table to translate exam vocabulary into concrete AWS services.

| Virtualization Concept (CV0-004) | AWS Service / Feature | Notes |
|---|---|---|
| **Stand-alone** | EC2 single instance | One VM on one host, no auto-recovery — baseline deployment. |
| **Clustering** | EC2 Auto Scaling groups, EMR (Elastic MapReduce) | ASG spreads instances across AZs for HA; EMR clusters compute nodes. |
| **Cloning** | AMI (Amazon Machine Image) / instance clone | Launch identical instances from an AMI or create an image of a running instance. |
| **Host affinity** | Placement groups (spread/partition), Dedicated Host | Spread groups keep instances on separate hardware; Dedicated Host pins to physical host. |
| **Hardware pass-through** | EC2 bare metal instances | No hypervisor layer — direct access to physical CPU/NIC for max performance. |
| **Overlay networks** | VPC with SDN (subnets, route tables, VXLAN-like fabric) | AWS VPC is a logical overlay isolating tenants regardless of physical rack. |
| **VM networks** | ENI (Elastic Network Interface) / subnet / security group | ENIs + subnets + security groups form the VM-level network fabric. |
| **Local storage** | Instance store (ephemeral NVMe/SSD) | Physically attached to host; lost on stop/terminate; not shared. |
| **SAN (block)** | EBS (Elastic Block Store) | Network block storage, attachable to any instance in the AZ; shared-at-block-level. |
| **NAS (file)** | EFS (Elastic File System) / FSx (Windows File Server, Lustre) | File-level shared storage mounted over the network (NFS/SMB). |

**Quick recall line:** Stand-alone→EC2, Cluster→ASG/EMR, Clone→AMI, Affinity→Placement/Dedicated Host, Pass-through→Bare Metal, Overlay→VPC, VM net→ENI, Local→Instance Store, SAN→EBS, NAS→EFS/FSx.

---

## SECTION 6 — PRACTICE QUESTIONS

**1. A single VM runs on one physical host with no resource pool and no HA. This is best described as:**
A. A cluster
B. A stand-alone VM
C. An overlay network
D. A SAN LUN
Answer: B — A stand-alone VM runs alone on one host with no clustering or HA.

**2. Which feature lets a running VM move from one host to another with zero downtime for maintenance?**
A. Snapshot
B. Clone
C. Live migration
D. Template
Answer: C — Live migration (vMotion) moves a running VM across hosts without downtime.

**3. You need 10 identical web servers fast and consistent. The BEST approach is:**
A. Hand-build each VM
B. Clone from a template
C. Use local storage only
D. Disable DRS
Answer: B — Cloning from a gold template deploys many identical VMs quickly.

**4. To ensure two database replicas never run on the same failed host, you configure:**
A. Affinity
B. Anti-affinity
C. Pass-through
D. Overlay
Answer: B — Anti-affinity keeps VMs on separate hosts for redundancy.

**5. Giving a VM exclusive direct access to a physical GPU is an example of:**
A. VM network
B. Hardware pass-through
C. NAS
D. Affinity
Answer: B — Hardware pass-through grants a VM direct physical device access.

**6. A logical network that encapsulates traffic and spans hosts/racks is a:**
A. VM network
B. Local disk
C. Overlay network
D. SAN
Answer: C — An overlay (e.g., VXLAN) is a logical network on top of the physical fabric.

**7. Raw block devices delivered over Fibre Channel or iSCSI describe:**
A. NAS
B. SAN
C. Local storage
D. Overlay
Answer: B — SAN provides block-level storage over a dedicated storage network.

**8. File-level storage accessed via NFS or SMB is characteristic of:**
A. SAN
B. Local disk
C. NAS
D. Instance store
Answer: C — NAS exposes file shares over standard network protocols.

**9. Storage physically inside the host that is NOT shareable across hosts is:**
A. SAN
B. NAS
C. Local storage
D. EFS
Answer: C — Local storage is attached to one host and cannot be shared.

**10. A vSwitch port group tagged with a VLAN inside a hypervisor represents a:**
A. Overlay network
B. VM network
C. SAN LUN
D. Pass-through device
Answer: B — A vSwitch port group is part of the host-level VM network fabric.

**11. In AWS, the closest match to "hardware pass-through" is:**
A. EC2 bare metal
B. EBS
C. EFS
D. Placement group
Answer: A — Bare metal EC2 removes the hypervisor for direct hardware access.

**12. Which AWS feature keeps instances on separate physical hardware for fault isolation?**
A. Dedicated Host
B. Spread placement group
C. Instance store
D. Subnet
Answer: B — A spread placement group distributes instances across distinct hardware.

**13. In AWS, EBS is the mapping for which CV0-004 storage concept?**
A. NAS
B. Local
C. SAN (block)
D. Overlay
Answer: C — EBS is network block storage, the AWS analog of a SAN LUN.

**14. AMI / instance cloning in AWS maps to which concept?**
A. Clustering
B. Cloning
C. Affinity
D. Pass-through
Answer: B — AMIs let you clone identical instances, matching the cloning concept.

**15. Which storage type BEST supports live migration because any host can attach it?**
A. Local
B. SAN
C. Instance store
D. None
Answer: B — Shared SAN LUNs let a VM's disk follow it to another host.

**16. VPC with SDN in AWS maps to which networking concept?**
A. VM network
B. Overlay networks
C. Local storage
D. Clustering
Answer: B — A VPC is a logical overlay network isolating tenants regardless of rack.

**17. EC2 Auto Scaling groups and EMR map to which concept?**
A. Stand-alone
B. Cloning
C. Clustering
D. Pass-through
Answer: C — ASG/EMR provide pooled, highly available clustered compute.

**18. A trade-off of hardware pass-through is that it usually:**
A. Improves sharing
B. Blocks live migration
C. Reduces performance
D. Requires NAS
Answer: B — Exclusive device access typically prevents live migration of that VM.

**19. Placement groups and Dedicated Hosts in AWS map to which concept?**
A. Host affinity
B. Overlay
C. NAS
D. Stand-alone
Answer: A — These control VM-to-host placement, matching host affinity rules.

**20. Which combination is MOST appropriate for a production database needing HA and shared disks?**
A. Stand-alone + local storage
B. Cluster + SAN
C. Overlay + NAS only
D. Pass-through + instance store
Answer: B — Clustering with SAN gives HA and shared block storage for databases.
