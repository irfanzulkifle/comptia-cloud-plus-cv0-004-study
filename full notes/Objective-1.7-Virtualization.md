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

1. A single VM runs on one physical host with no resource pool and no HA. This is best described as:**
   - **A.** A cluster
   - **B.** A stand-alone VM
   - **C.** An overlay network
   - **D.** A SAN LUN

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A stand-alone VM runs alone on one physical host with no clustering or HA.

**Why A is wrong:** A cluster pools multiple hosts for HA, the opposite of a single isolated VM.
**Why C is wrong:** An overlay is a logical L2 fabric spanning hosts, not a single VM deployment.
**Why D is wrong:** A SAN LUN is block storage, not a VM placement model.

</details>

2. Which feature lets a running VM move from one host to another with zero downtime for maintenance?**
   - **A.** Snapshot
   - **B.** Clone
   - **C.** Live migration
   - **D.** Template

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Live migration (vMotion) moves a running VM to another host with zero downtime.

**Why A is wrong:** A snapshot is a point-in-time state, not movement of the running VM.
**Why B is wrong:** A clone is a copy/deployment, not live motion of the running instance.
**Why D is wrong:** A template is a gold-image source, not a running-VM move.

</details>

3. You need 10 identical web servers fast and consistent. The BEST approach is:**
   - **A.** Hand-build each VM
   - **B.** Clone from a template
   - **C.** Use local storage only
   - **D.** Disable DRS

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Cloning from a gold template deploys many identical VMs quickly and consistently.

**Why A is wrong:** Hand-building is slow, inconsistent, and error-prone versus templated cloning.
**Why C is wrong:** Local storage is a disk location, not a fast consistent deployment method.
**Why D is wrong:** Disabling DRS hurts balancing; it does not deploy servers.

</details>

4. To ensure two database replicas never run on the same failed host, you configure:**
   - **A.** Affinity
   - **B.** Anti-affinity
   - **C.** Pass-through
   - **D.** Overlay

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Anti-affinity keeps VMs on separate hosts so one host failure cannot kill both replicas.

**Why A is wrong:** Affinity keeps VMs together on one host, the opposite of what is wanted.
**Why C is wrong:** Pass-through grants direct device access, unrelated to placement separation.
**Why D is wrong:** An overlay is a network fabric, not a placement rule.

</details>

5. Giving a VM exclusive direct access to a physical GPU is an example of:**
   - **A.** VM network
   - **B.** Hardware pass-through
   - **C.** NAS
   - **D.** Affinity

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Hardware pass-through gives a VM exclusive direct access to a physical device such as a GPU.

**Why A is wrong:** A VM network is virtual switching, not direct device access.
**Why C is wrong:** NAS is file storage, unrelated to GPU or device access.
**Why D is wrong:** Affinity is a placement rule, not device assignment.

</details>

6. A logical network that encapsulates traffic and spans hosts/racks is a:**
   - **A.** VM network
   - **B.** Local disk
   - **C.** Overlay network
   - **D.** SAN

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** An overlay network such as VXLAN encapsulates traffic and spans hosts or racks logically.

**Why A is wrong:** A VM network is host-local virtual switching without encapsulation.
**Why B is wrong:** Local disk is storage attached to one host, not a network.
**Why D is wrong:** A SAN is block storage, not an encapsulated logical network.

</details>

7. Raw block devices delivered over Fibre Channel or iSCSI describe:**
   - **A.** NAS
   - **B.** SAN
   - **C.** Local storage
   - **D.** Overlay

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A SAN delivers raw block devices over Fibre Channel or iSCSI, seen as local disks.

**Why A is wrong:** NAS is file-level over NFS or SMB, not raw block devices.
**Why C is wrong:** Local storage is physically in the host, not a shared block network.
**Why D is wrong:** An overlay is a network, not a storage protocol.

</details>

8. File-level storage accessed via NFS or SMB is characteristic of:**
   - **A.** SAN
   - **B.** Local disk
   - **C.** NAS
   - **D.** Instance store

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** NAS exposes file shares over NFS or SMB, file-level storage over standard networks.

**Why A is wrong:** SAN is block-level, not file shares.
**Why B is wrong:** Local disk is host-attached, not a network file share.
**Why D is wrong:** Instance store is ephemeral local block storage, not file-level NAS.

</details>

9. Storage physically inside the host that is NOT shareable across hosts is:**
   - **A.** SAN
   - **B.** NAS
   - **C.** Local storage
   - **D.** EFS

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Local storage is physically inside the host and cannot be shared across hosts.

**Why A is wrong:** SAN LUNs are shared across hosts, enabling live migration.
**Why B is wrong:** NAS is network-mounted and shareable across hosts.
**Why D is wrong:** EFS is AWS file storage shared across instances.

</details>

10. A vSwitch port group tagged with a VLAN inside a hypervisor represents a:**
   - **A.** Overlay network
   - **B.** VM network
   - **C.** SAN LUN
   - **D.** Pass-through device

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A vSwitch port group tagged with a VLAN is part of the host-level VM network fabric.

**Why A is wrong:** An overlay spans hosts via encapsulation; a port group is local L2.
**Why C is wrong:** A SAN LUN is storage, not a network port group.
**Why D is wrong:** Pass-through is direct device access, not virtual switching.

</details>

11. In AWS, the closest match to "hardware pass-through" is:**
   - **A.** EC2 bare metal
   - **B.** EBS
   - **C.** EFS
   - **D.** Placement group

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** EC2 bare metal removes the hypervisor, giving direct hardware access closest to pass-through.

**Why B is wrong:** EBS is block storage, not hardware pass-through.
**Why C is wrong:** EFS is file storage, not pass-through.
**Why D is wrong:** Placement groups control placement, not device access.

</details>

12. Which AWS feature keeps instances on separate physical hardware for fault isolation?**
   - **A.** Dedicated Host
   - **B.** Spread placement group
   - **C.** Instance store
   - **D.** Subnet

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A spread placement group distributes instances across distinct hardware for fault isolation.

**Why A is wrong:** A Dedicated Host pins to one physical server; it does not spread across hardware.
**Why C is wrong:** Instance store is ephemeral local disk, not a placement strategy.
**Why D is wrong:** A subnet is a network range, not a hardware-isolation construct.

</details>

13. In AWS, EBS is the mapping for which CV0-004 storage concept?**
   - **A.** NAS
   - **B.** Local
   - **C.** SAN (block)
   - **D.** Overlay

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** EBS is network block storage, the AWS analog of a SAN LUN, attachable in the AZ.

**Why A is wrong:** NAS or file maps to EFS or FSx, not EBS.
**Why B is wrong:** Local maps to instance store, not EBS.
**Why D is wrong:** Overlay maps to VPC, not block storage.

</details>

14. AMI / instance cloning in AWS maps to which concept?**
   - **A.** Clustering
   - **B.** Cloning
   - **C.** Affinity
   - **D.** Pass-through

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** AMIs let you launch identical instances, matching the cloning concept.

**Why A is wrong:** Clustering is pooled HA compute, not image-based cloning.
**Why C is wrong:** Affinity is placement, not image cloning.
**Why D is wrong:** Pass-through is device access, not cloning.

</details>

15. Which storage type BEST supports live migration because any host can attach it?**
   - **A.** Local
   - **B.** SAN
   - **C.** Instance store
   - **D.** None

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Shared SAN LUNs let the VM disk follow it to another host, enabling live migration.

**Why A is wrong:** Local storage is host-bound and breaks live migration.
**Why C is wrong:** Instance store is ephemeral and host-attached, cannot follow the VM.
**Why D is wrong:** SAN specifically supports it, so not none.

</details>

16. VPC with SDN in AWS maps to which networking concept?**
   - **A.** VM network
   - **B.** Overlay networks
   - **C.** Local storage
   - **D.** Clustering

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** A VPC with SDN is a logical overlay isolating tenants regardless of physical rack.

**Why A is wrong:** A VM network is host-local switching, not a tenant-spanning overlay.
**Why C is wrong:** Local storage is unrelated to a networking overlay.
**Why D is wrong:** Clustering is compute pooling, not a network.

</details>

17. EC2 Auto Scaling groups and EMR map to which concept?**
   - **A.** Stand-alone
   - **B.** Cloning
   - **C.** Clustering
   - **D.** Pass-through

<details>
<summary>Reveal Answer</summary>

**Correct: C**

**Why correct:** Auto Scaling groups and EMR provide pooled, highly available clustered compute.

**Why A is wrong:** Stand-alone is single-host with no clustering.
**Why B is wrong:** Cloning is image copy, not pooled HA compute.
**Why D is wrong:** Pass-through is device access, not clustering.

</details>

18. A trade-off of hardware pass-through is that it usually:**
   - **A.** Improves sharing
   - **B.** Blocks live migration
   - **C.** Reduces performance
   - **D.** Requires NAS

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Exclusive device access typically prevents live migration of that VM.

**Why A is wrong:** Pass-through is exclusive, not shared.
**Why C is wrong:** Pass-through improves performance with near-native access.
**Why D is wrong:** NAS is storage, unrelated to the trade-off.

</details>

19. Placement groups and Dedicated Hosts in AWS map to which concept?**
   - **A.** Host affinity
   - **B.** Overlay
   - **C.** NAS
   - **D.** Stand-alone

<details>
<summary>Reveal Answer</summary>

**Correct: A**

**Why correct:** Placement groups and Dedicated Hosts control VM-to-host placement, matching host affinity.

**Why B is wrong:** Overlay is networking, not placement.
**Why C is wrong:** NAS is storage, not placement.
**Why D is wrong:** Stand-alone is single-host; placement rules span hosts.

</details>

20. Which combination is MOST appropriate for a production database needing HA and shared disks?**
   - **A.** Stand-alone + local storage
   - **B.** Cluster + SAN
   - **C.** Overlay + NAS only
   - **D.** Pass-through + instance store

<details>
<summary>Reveal Answer</summary>

**Correct: B**

**Why correct:** Cluster plus SAN gives HA and shared block storage suited to production databases.

**Why A is wrong:** Stand-alone plus local has no HA and no shared disk.
**Why C is wrong:** NAS is file-level, not ideal for shared DB block, and no explicit HA.
**Why D is wrong:** Pass-through plus instance store is ephemeral with no clustering or HA.

</details>
