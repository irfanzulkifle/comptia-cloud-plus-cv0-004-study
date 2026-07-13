# CompTIA Cloud+ CV0-004 — Domain 1.0 Cloud Architecture
## 📘 Objective 1.7: Virtualization Concepts

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 1.0 Domain = 23% of total exam
> **Objective 1.7 Focus:** The VM counterpart to 1.6 (containers) — how virtual machines are managed, made highly available, and connected to storage and networks.

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 1.7
### Objective Name: *Compare and contrast virtualization concepts.*

**What this objective means (beginner-friendly):**
Virtualization is the **foundation of cloud computing**. A **hypervisor** (VMware ESXi, Microsoft Hyper-V, KVM, Xen) lets one physical server run many virtual machines (VMs), each acting like an independent computer.

This objective covers:
- **Stand-alone VMs:** A single VM on a single host (no HA).
- **Clusters:** Multiple hosts working together (HA, live migration).
- **Cloning:** Making copies of VMs (for fast deployment).
- **Host affinity rules:** "Run these VMs together" or "keep them apart."
- **Hardware pass-through:** Direct access to physical hardware (GPU, NVMe) for performance.
- **VM networks:** Virtual switches inside a hypervisor.
- **Storage:** Where VM disks live (local, SAN, NAS, distributed).

The exam will test whether you understand the building blocks that make cloud VMs work — and how to design for HA, performance, and efficiency.

**Why it matters in the real world:**
- Every cloud VM (EC2, Azure VM, Compute Engine) is a virtual machine running on a hypervisor.
- Cloud platforms are essentially **hypervisors at massive scale** (Google's KVM-based, AWS's Nitro/Xen, Azure's Hyper-V).
- HA, live migration, and resource pooling are what make cloud possible.
- Misunderstanding affinity/anti-affinity = downtime (e.g., placing both DB replicas on the same host).

**Why CompTIA tests it:**
- Virtualization is THE core technology of cloud.
- Cloud+ builds on Server+ and Network+ — VM knowledge is foundational.
- PBQs often include VM architecture (cluster design, affinity rules, storage choices).
- Connects to 1.6 (containers) — both VMs and containers are deployment options.

---

## SECTION 2 — EXAM KNOWLEDGE

### 2.1 — Stand-Alone VMs

**Definition:**
A **single VM running on a single physical host** without clustering or high availability. If the host fails, the VM dies. No automatic restart, no live migration.

**Purpose:**
- Run a single workload in isolation.
- Use for dev/test, small workloads, or non-critical apps.
- Simplest VM deployment — no infrastructure dependencies.

**How it works:**
- A hypervisor (ESXi, Hyper-V, KVM) runs on a physical host.
- One or more VMs are created on that host.
- Each VM has virtual CPU, RAM, disk, NIC, allocated by the hypervisor.
- The VM runs its own OS and apps, unaware of other VMs.
- If the host fails, the VM is down. Recovery requires manual restart on another host.

**Advantages:**
- Simple to set up and manage.
- No clustering overhead.
- Full control of the VM (and the host).
- Good for dev/test or isolated workloads.

**Disadvantages:**
- **No HA** — host failure = VM failure.
- **No live migration** — can't move without downtime.
- **No automatic restart** — manual intervention required.
- **Resource-constrained** — limited to host's capacity.
- **Single point of failure** for the workload.

**When to use:**
- Dev/test environments.
- Small, non-critical workloads.
- Learning virtualization.
- Isolated workloads (security, compliance).
- Single-tenant legacy apps.

**When NOT to use:**
- Mission-critical production workloads (use clustering).
- Apps that need HA (use clustered hosts).
- Apps that need live migration (use clusters).
- Anything that must survive host failure (use HA, clustering, or cloud-managed VMs).

**Real-world example:** A developer creates a single VM on a workstation to test code. Host fails, the test VM is gone. The developer recreates it from a template.

---

### 2.2 — Clustering

**Definition:**
A **group of physical hosts (cluster) working together** to provide HA, live migration, and resource pooling for VMs. If one host fails, the VMs are automatically restarted on another host in the cluster. VMs can be moved between hosts without downtime (live migration).

**Purpose:**
- Provide **HA** for VMs (survive host failure).
- Enable **live migration** (move VMs with zero downtime).
- **Pool resources** across hosts (CPU, RAM, storage).
- Enable **load balancing** (VMs can be distributed across hosts).
- Support **DR** strategies (cluster spans racks, AZs, sites).

**How it works:**
- Multiple hypervisor hosts are joined to a cluster.
- A management plane (vCenter, SCVMM, Proxmox) coordinates the cluster.
- VMs are registered in a cluster-level inventory.
- **HA (High Availability):** If a host fails, VMs are automatically restarted on another host in the cluster. Detected via heartbeat/network.
- **Live Migration (vMotion, Live Migration):** Move a running VM from one host to another with zero downtime. Used for maintenance, load balancing.
- **DRS (Distributed Resource Scheduler):** Automatically balances VMs across hosts based on resource utilization.
- **Storage vMotion:** Move VM storage to another datastore without downtime.

**Clustering Features:**

| Feature | Description |
|---|---|
| **HA** | Auto-restart VMs on host failure. |
| **Live Migration** | Move VMs between hosts with zero downtime. |
| **DRS** | Auto-balance VMs across hosts. |
| **Fault Tolerance** | Run a VM in lockstep on two hosts (zero downtime, no data loss). VMware-specific. |
| **Storage vMotion** | Migrate VM storage without downtime. |
| **Distributed Switch** | Cluster-wide virtual switch (vSphere Distributed Switch). |
| **Cluster-wide resource pools** | Aggregate CPU/RAM across hosts. |

**Clustering Technologies:**

| Hypervisor | Cluster Product | Notes |
|---|---|---|
| **VMware vSphere** | vCenter + clusters (HA, DRS, vMotion) | Industry standard. |
| **Microsoft Hyper-V** | Failover Clustering + SCVMM | Windows-based. |
| **KVM** | Built-in KVM HA, oVirt, Proxmox, OpenStack | Open source. |
| **Xen** | XenServer / Citrix Hypervisor | Older. |
| **Nutanix** | Nutanix AHV (built on KVM) | HCI (hyperconverged). |

**Cloud Provider Mapping (clusters in the cloud):**

| Cloud | Cluster Equivalent |
|---|---|
| **AWS** | EC2 fleet behind Auto Scaling Group + ALB. EC2 Dedicated Hosts for licensing. |
| **Azure** | Virtual Machine Scale Sets (VMSS) + Availability Zones. |
| **GCP** | Managed Instance Groups (MIGs) + regional/zonal. |

**Advantages:**
- **HA** — VMs survive host failure.
- **Live migration** — zero-downtime maintenance.
- **Resource efficiency** — VMs can be packed efficiently.
- **DR** — cluster can span AZs / sites.
- **Automated load balancing** (DRS).

**Disadvantages:**
- **Complexity** — cluster management, networking, storage.
- **Cost** — multiple hosts, shared storage, licensing.
- **Single point of failure** in management plane (mitigated with HA vCenter).
- **Live migration requires shared storage** (VMs must be on storage accessible by all hosts).

**When to use:**
- Production VMs (almost always).
- Mission-critical workloads.
- Apps requiring HA.
- Environments where maintenance windows are not acceptable.

**When NOT to use:**
- Dev/test with no HA requirement.
- Very small deployments (1–2 VMs).
- When cost is the primary driver.

---

### 2.3 — Cloning

**Definition:**
The process of **creating a copy of a VM** (or a VM template). Used for rapid deployment of new VMs from a pre-configured "golden image."

**Purpose:**
- Deploy new VMs quickly (minutes, not hours).
- Ensure consistency (every VM is identical).
- Standardize configurations (patched, secured, configured).
- Scale out (add more web servers, etc.).

**How it works:**
- A **template** (or "golden image") is created — a master VM with the OS, patches, and standard software.
- The template is used to **deploy new VMs** (clones).
- The hypervisor copies the template's virtual disks and creates a new VM with new IDs, MAC addresses, and computer names.

**Types of Clones:**

| Type | Description | Storage | Use Case |
|---|---|---|---|
| **Full Clone** | Independent copy of the VM. Doesn't depend on the parent. | More storage (full copy) | Long-lived, production VMs. |
| **Linked Clone** | Shares virtual disks with the parent VM. | Less storage (deltas only) | Short-lived, VDI, dev/test. |
| **Template** | A read-only master image used to create new VMs. | Read-only, no running VM | Source for full/linked clones. |

**Full Clone:**
- All VMDKs (or VHDs) are copied.
- The new VM is fully independent.
- Takes more time to create (full copy).
- Uses more storage.
- Used for production VMs that need to be independent.

**Linked Clone:**
- The new VM shares the parent's virtual disks.
- A small delta disk holds the differences (writes).
- Faster to create, uses less storage.
- **Depends on the parent** — if the parent is deleted, the clone breaks.
- Used for VDI (Virtual Desktop Infrastructure), dev/test, where many short-lived VMs share a base.

**Template:**
- A master VM that's been "sealed" (generalized, no unique IDs).
- Used as the source for clones.
- Cannot be powered on (it's a template).
- Stored as a file in the hypervisor's storage.

**Cloning Process:**
1. **Install OS** on a base VM.
2. **Patch and configure** the OS (install apps, set policies).
3. **Generalize / sysprep** the VM (remove unique identifiers).
4. **Convert to template** (in VMware: Convert to Template).
5. **Deploy from template** (Full Clone or Linked Clone).
6. **Customize** the new VM (hostname, IP, joins to domain).

**Cloud Equivalent:**
- **AWS:** AMI (Amazon Machine Image) → launch EC2 instances from it. EC2 Image Builder automates patching.
- **Azure:** Managed Image (from a generalized VM) or Shared Image Gallery. Azure Image Builder automates.
- **GCP:** Custom Image from a boot disk. Image Builder (not as mature as AWS).

**Advantages:**
- **Speed** — new VMs in minutes.
- **Consistency** — every VM is identical.
- **Standardization** — golden image ensures compliance.
- **Cost savings** — linked clones use less storage.

**Disadvantages:**
- **Template drift** — if you don't update the template, new VMs are out of date.
- **Linked clone dependency** — parent deletion = broken clones.
- **Initial setup time** — building the golden image takes effort.
- **Storage growth** — many full clones = lots of storage.

**Best Practices:**
- Maintain ONE golden image per OS + app stack.
- Regularly patch and update the template.
- Use automation (Image Builder, Packer) to keep templates fresh.
- Document what's in the template.
- Use linked clones for ephemeral workloads (VDI, dev/test).
- Use full clones for production.

**When to use:**
- Deploying many identical VMs (web servers, app servers, VDI).
- Standardizing configurations across the org.
- DR scenarios (clone a VM in another region/site).
- Dev/test (linked clones for fast spin-up).

**When NOT to use:**
- Single, unique VM (no need to template).
- VMs with highly specific configurations (each is different).
- When automation is overkill.

---

### 2.4 — Host Affinity

**Definition:**
**Rules that determine which VMs run on which hosts** (or which VMs are kept apart from each other). Used to control placement for HA, performance, licensing, or compliance.

**Purpose:**
- **Enforce HA:** Spread VMs across hosts (so a single host failure doesn't take them all down).
- **Improve performance:** Keep related VMs on the same host (low-latency communication).
- **Comply with licensing:** Pin a VM to specific hosts (e.g., per-socket licensing).
- **Optimize resource use:** Group VMs that benefit from each other.

**How it works:**
- The hypervisor (or cloud scheduler) maintains a list of affinity rules.
- When a VM is started, the scheduler checks the rules and chooses a host accordingly.

**Types of Affinity Rules:**

| Rule Type | Description | Use Case |
|---|---|---|
| **Affinity (Keep Together)** | VMs SHOULD run on the same host. | Low-latency communication between VMs (e.g., app + cache). |
| **Anti-Affinity (Keep Apart)** | VMs SHOULD NOT run on the same host. | HA — keep replicas on different hosts. |
| **Required vs. Preferred** | Required = must follow. Preferred = should follow if possible. | Strict HA (required) vs. optimization (preferred). |
| **Host affinity** | VM runs on a specific host (or set of hosts). | Licensing, dedicated hardware. |
| **VM-VM affinity** | VM's placement relative to other VMs. | The two above. |
| **VM-Host affinity** | VM's placement relative to hosts (e.g., hosts with GPU). | Pin to GPU hosts. |

**Examples:**

| Scenario | Rule | Why |
|---|---|---|
| **2 web servers for HA** | Anti-affinity: run on different hosts | Host failure doesn't take both down. |
| **App server + database on same host** | Affinity: run on same host | Low latency between them. |
| **GPU workload** | VM-host affinity: pin to GPU host | Only GPU hosts can run the VM. |
| **Windows Server with per-socket license** | VM-host affinity: pin to dedicated host | License is per socket; can't share. |
| **Database replicas across AZs** | Anti-affinity: spread across hosts + AZs | Survive host AND AZ failure. |

**Cloud Provider Mapping:**

| Cloud | Affinity Mechanism |
|---|---|
| **AWS** | EC2 Placement Groups (Cluster, Spread, Partition). Host affinity via Dedicated Hosts. |
| **Azure** | Proximity Placement Groups, Availability Zones, VM Scale Set placement. |
| **GCP** | Instance templates + affinity/anti-affinity labels, Sole-tenant nodes. |
| **VMware** | DRS Rules (Must Run, Should Run, Must Not Run, Should Not Run). |
| **K8s** | Pod affinity / anti-affinity / node affinity / taints & tolerations. |

**AWS Placement Groups:**

- **Cluster Placement Group:** Low-latency, same AZ, same rack. For HPC.
- **Spread Placement Group:** Each instance on a different rack. For HA (max 7 instances per group).
- **Partition Placement Group:** Instances spread across partitions (racks). For large-scale distributed apps (e.g., HDFS, Kafka).

**Advantages:**
- **HA enforcement** — anti-affinity ensures replicas are separated.
- **Performance optimization** — affinity keeps related VMs close.
- **Licensing compliance** — host affinity enforces license terms.
- **Resource optimization** — group VMs that benefit from each other.

**Disadvantages:**
- **Reduced flexibility** — VMs can't be packed optimally.
- **Cluster resource imbalance** — if rules are too strict.
- **Complexity** — managing rules at scale.

**When to use:**
- **Always** for HA (anti-affinity for replicas).
- For licensing compliance (host affinity).
- For low-latency apps (affinity).
- For GPU/FPGA workloads (host affinity).

**When NOT to use:**
- For unrelated VMs (no need to constrain).
- When flexibility is more important than rules.

---

### 2.5 — Hardware Pass-Through

**Definition:**
A mechanism that gives a VM **direct access to physical hardware**, bypassing the hypervisor's abstraction layer. The VM "sees" the actual physical device.

**Purpose:**
- Provide near-native performance for hardware-sensitive workloads.
- Support devices that can't be virtualized (USB, GPU, NVMe).
- Enable direct device access for specialized workloads (GPU computing, hardware crypto).

**How it works:**
- The hypervisor assigns a physical device (GPU, NIC, USB) directly to a VM.
- The VM's OS installs the device driver and uses the device as if it owned the host.
- The hypervisor doesn't intercept I/O for that device — it's direct.

**Types of Hardware Pass-Through:**

| Technology | Description | Use Case |
|---|---|---|
| **PCI Passthrough (IOMMU / VT-d)** | Direct PCI device access to a VM. | GPU, NVMe, NIC. |
| **SR-IOV (Single Root I/O Virtualization)** | A physical device exposes multiple virtual functions (VFs). Each VF can be assigned to a VM. | High-performance networking. |
| **NPIV (N_Port ID Virtualization)** | Virtualizes Fibre Channel HBAs. | SAN access for VMs. |
| **USB Passthrough** | Passes a USB device to a VM. | License dongles, security keys. |
| **GPU Passthrough** | Assigns a physical GPU to a VM. | ML training, gaming, VDI. |
| **vGPU (virtual GPU)** | A GPU is shared (time-sliced or MIG) across multiple VMs. | VDI, AI inference, multi-tenant GPU. |

**PCI Passthrough (IOMMU / VT-d):**
- Uses Intel VT-d or AMD-Vi (IOMMU).
- The device is dedicated to one VM — can't be shared.
- Near-native performance.

**SR-IOV:**
- A physical NIC (or other device) presents multiple "virtual functions" (VFs).
- Each VF is a separate PCI device that can be assigned to a VM.
- VMs get near-native network performance.
- Common in NFV (Network Functions Virtualization) and high-performance computing.

**GPU Passthrough / vGPU:**
- **Passthrough:** One GPU → one VM (best perf, can't share).
- **vGPU (NVIDIA GRID, AMD MxGPU):** One GPU → many VMs (time-sliced or hardware-partitioned). Used in VDI.

**Cloud Provider Mapping:**

| Cloud | GPU / Hardware Pass-Through |
|---|---|
| **AWS** | EC2 P-series (P4, P5) with NVIDIA GPUs. Bare metal (`.metal`) instances for full hardware access. |
| **Azure** | N-series VMs with NVIDIA GPUs. ND-series for AI. Bare metal via specialized instances. |
| **GCP** | A2/A3 VMs with NVIDIA GPUs. Bare metal instances. Confidential VMs with encrypted memory. |

**Advantages:**
- **Near-native performance** — bypasses hypervisor overhead.
- **Hardware access** — VMs can use specialized devices.
- **Lower latency** — direct I/O.
- **Higher throughput** — for network, storage, GPU.

**Disadvantages:**
- **No sharing** (for passthrough) — the device is dedicated to one VM.
- **No live migration** — passthrough devices can't be migrated easily.
- **Complexity** — requires CPU support (VT-d, AMD-Vi) and driver setup.
- **Reduced flexibility** — VM is tied to the host.

**When to use:**
- GPU compute (ML training, VDI).
- High-performance networking (SR-IOV for low-latency).
- Hardware that can't be virtualized (USB, serial).
- Compliance (encryption HSMs).

**When NOT to use:**
- Standard workloads (overkill).
- When sharing is needed (use vGPU instead of GPU passthrough).
- When live migration is required (passthrough breaks it).

---

### 2.6 — Network Types (Overlay and VM Networks)

#### 2.6.1 — VM Networks (Virtual Switches)

**Definition:**
A **virtual network switch (vSwitch)** that runs inside the hypervisor. It connects VMs to each other, to the physical network, and to external networks. The vSwitch is software-defined — no physical hardware.

**Purpose:**
- Provide networking for VMs without physical switches per VM.
- Enable VM-to-VM communication on the same host.
- Connect VMs to physical networks (via uplinks).
- Implement network policies (security, QoS, VLANs).

**How it works:**
- The hypervisor creates a virtual switch (vSwitch).
- Each VM's virtual NIC (vNIC) is connected to a port group on the vSwitch.
- The vSwitch has uplinks to physical NICs (pNICs) for external connectivity.
- Port groups define VLANs, security policies, and traffic shaping.

**VM Network Components:**

| Component | Description |
|---|---|
| **vSwitch** | Virtual switch inside the hypervisor. |
| **Port Group** | A named set of ports with shared config (VLAN, security). |
| **vNIC** | Virtual NIC in the VM. |
| **pNIC** | Physical NIC on the host (uplink from vSwitch). |
| **VLAN** | Layer 2 segmentation on the vSwitch. |
| **VLAN Trunk** | Carries multiple VLANs to a single pNIC. |
| **NIC Teaming** | Multiple pNICs bonded for redundancy/throughput. |

**Examples:**

| Hypervisor | vSwitch Name |
|---|---|
| **VMware** | vSwitch (standard) or VDS (Distributed vSwitch — cluster-wide). |
| **Hyper-V** | Hyper-V Virtual Switch (external, internal, private). |
| **KVM** | Linux Bridge, OVS (Open vSwitch). |
| **Xen** | XenBridge, OVS. |

**Cloud Mapping:**
- **AWS:** The "vSwitch" concept maps to the VPC subnet. Each subnet is like a port group.
- **Azure:** VNet + subnet.
- **GCP:** VPC + subnet.

**Advantages:**
- **Software-defined** — easy to configure, replicate, automate.
- **No hardware per VM** — scales to many VMs.
- **Policy enforcement** — security, QoS at the vSwitch.
- **Distributed switches** — consistent config across hosts (VMware VDS).

**Disadvantages:**
- **CPU overhead** — software switching uses host CPU.
- **Single point of failure** — if the vSwitch crashes, VMs lose connectivity (mitigated with redundant uplinks and HA).
- **Complexity at scale** — many port groups to manage.

---

#### 2.6.2 — Overlay Networks

**Definition:**
A **virtual network built on top of an existing physical network**. The physical network is called the **underlay**; the overlay is a tunnel-based logical network that runs over it. Overlay networks enable multi-tenancy, network abstraction, and isolation.

**Purpose:**
- **Multi-tenancy:** Many isolated virtual networks on shared physical infrastructure.
- **L2 over L3:** Extend Layer 2 networks across Layer 3 boundaries (data centers, clouds).
- **Network virtualization:** Decouple virtual networks from physical topology.
- **Cloud provider scale:** AWS, Azure, GCP all use overlay networks internally to provide VPCs/VNets.

**How it works:**
- Packets are **encapsulated** with an outer header (the underlay) and an inner header (the overlay).
- The underlay routes the packet to the destination endpoint.
- The destination decapsulates and forwards to the inner destination.
- Each tenant gets a unique overlay network identifier (e.g., VNI, VxLAN Network Identifier).

**Common Overlay Protocols:**

| Protocol | Description | Use Case |
|---|---|---|
| **VXLAN (Virtual Extensible LAN)** | Encapsulates L2 frames in UDP. 24-bit VNI = 16M networks. | AWS VPC, Azure VNet, GCP VPC. |
| **GENEVE (Generic Network Virtualization Encapsulation)** | More flexible than VXLAN. Used in modern data centers. | NSX, Cilium. |
| **NVGRE (Network Virtualization using Generic Routing Encapsulation)** | Microsoft's overlay. Less popular than VXLAN. | Older Azure. |
| **STT (Stateless Transport Tunneling)** | Used in Nicira NVP (predecessor of NSX). | Legacy. |
| **IPsec / GRE tunnels** | Generic tunnels for site-to-site VPN. | Hybrid cloud. |
| **MPLS (Multi-Protocol Label Switching)** | Service provider overlay. | WAN, telco. |
| **OTV (Overlay Transport Virtualization)** | Cisco's L2 over L3. | Data center interconnect. |

**VXLAN (Most Important to Know):**
- **VXLAN** = Virtual Extensible LAN.
- Encapsulates Layer 2 Ethernet frames inside Layer 3 UDP packets.
- Uses **VTEPs** (VXLAN Tunnel Endpoints) to encapsulate/decapsulate.
- Supports up to **16 million logical networks** (24-bit VNI — way more than VLAN's 4094).
- Used by AWS, Azure, GCP for their VPCs.
- Allows VMs in different physical hosts to be on the same logical L2 network.

**Overlay vs. Underlay:**

| Underlay | Overlay |
|---|---|
| Physical network (switches, routers, fiber). | Virtual network (VXLAN, GENEVE, GRE). |
| L3 routing between endpoints. | L2 connectivity on top of L3. |
| Built by network engineers. | Built by virtualization/cloud engineers. |
| Provider-managed (in cloud). | Tenant-isolated (per VPC). |

**Real-World Examples:**

| Use Case | Overlay |
|---|---|
| **AWS VPC** | VXLAN over the AWS global backbone. |
| **Azure VNet** | VXLAN. |
| **GCP VPC** | Andromeda (Google's SDN) — uses custom overlay. |
| **VMware NSX** | GENEVE overlay over the physical network. |
| **Kubernetes (Calico, Cilium)** | VXLAN or IP-in-IP overlay for pod networking. |
| **K8s on AWS (AWS VPC CNI)** | No overlay — pods get native VPC IPs. |
| **Hybrid cloud** | IPsec / WireGuard / GRE tunnels. |

**Advantages:**
- **Tenant isolation** — many logical networks on shared physical.
- **Scale** — 16M networks (VXLAN) vs 4K (VLAN).
- **Mobility** — VM can move; its logical network follows.
- **L2 over L3** — extend networks across subnets / data centers.

**Disadvantages:**
- **Encapsulation overhead** — adds 50+ bytes per packet.
- **MTU issues** — must reduce inner MTU to accommodate outer headers (jumbo frames don't work easily).
- **Complexity** — extra layer to manage.
- **Troubleshooting** — overlay issues are harder to debug.

**When to use:**
- **Always** in cloud (AWS VPC, Azure VNet, GCP VPC are all overlay-based).
- Multi-tenant environments.
- When you need L2 extension across L3.
- When you need more than 4K logical networks.

**When NOT to use:**
- Simple single-tenant networks.
- When MTU / performance is critical (and overlay overhead matters).
- When L2 connectivity is not required.

---

### 2.7 — Storage

#### 2.7.1 — Local Storage

**Definition:**
Storage that is **physically attached to a single host** (DAS — Direct Attached Storage). The disks are inside the host, or in a directly-attached enclosure (JBOD, RAID array).

**Purpose:**
- Provide fast, low-latency storage for VMs on a single host.
- Use for high-IOPS workloads (databases, caches).
- Cost-effective for non-critical data.

**How it works:**
- Disks are physically installed in the host (or in a directly-attached enclosure).
- The hypervisor formats the disks and creates datastores.
- VMs use virtual disks on the datastore.
- **Local to the host** — not accessible from other hosts (without special networking).

**Local Storage Options:**

| Type | Description | Performance |
|---|---|---|
| **HDD** | Spinning magnetic disks. | Moderate. |
| **SSD (SATA)** | Consumer-grade SSD. | Good. |
| **NVMe SSD** | High-performance NVMe. | Excellent (sub-ms). |
| **NVMe-oF (over Fabrics)** | NVMe accessed over network. | Excellent (with low-latency network). |
| **RAM Disk** | Storage in memory. | Extreme (volatile). |
| **Instance Store / Ephemeral Disk** | Cloud VM's local disk (lost on stop). | High. |

**Cloud Mapping:**
- **AWS:** Instance Store (ephemeral, lost on stop), local NVMe on `.metal` instances.
- **Azure:** Temporary disk (D: drive on Windows, /dev/sdb on Linux), Local SSD.
- **GCP:** Local SSD (scratch disks).

**Advantages:**
- **Fastest** — no network overhead.
- **Cheap** — local disks are inexpensive.
- **Low latency** — microseconds.
- **High IOPS** — NVMe can do millions of IOPS.

**Disadvantages:**
- **Single host** — not accessible from other hosts.
- **No HA** — host failure = data loss (unless replicated).
- **No live migration** (without storage vMotion to shared storage).
- **Limited capacity** — bound to host's disk slots.
- **Data loss risk** — local disk is lost when host dies.

**When to use:**
- Ephemeral data (caches, scratch).
- Performance-critical workloads (databases, real-time analytics).
- Temporary data that can be reconstructed.
- Boot disks in single-host setups.

**When NOT to use:**
- Data that must survive host failure (use shared storage).
- Multi-host clusters (need shared storage for vMotion).
- Long-term data (use SAN or object storage).

---

#### 2.7.2 — Storage Area Network (SAN)

**Definition:**
A **dedicated, high-speed network** that provides **block-level storage** to multiple servers. The SAN is its own network (often Fibre Channel or iSCSI) and is separate from the LAN. Servers see SAN storage as local disks.

**Purpose:**
- Provide shared, high-performance block storage to many hosts.
- Enable features like live migration (shared storage between hosts).
- Centralize storage management.
- Provide HA, replication, and snapshots at the storage layer.

**How it works:**
- A dedicated storage array (e.g., NetApp, Dell EMC, Pure Storage, HPE 3PAR) provides block storage.
- Hosts connect to the array via:
  - **Fibre Channel (FC):** Dedicated FC switches, up to 32 Gbps. Lowest latency.
  - **iSCSI:** IP-based (SCSI over TCP). Easier to deploy (uses existing network). 10/25/100 Gbps.
  - **FCoE (Fibre Channel over Ethernet):** FC frames over Ethernet. Less common.
- Hosts see the SAN storage as a local block device (e.g., /dev/sdc).
- Multiple hosts can access the same LUN (Logical Unit Number) — the SAN enforces reservation/locking.
- VMs use virtual disks on the SAN-backed datastore.

**SAN Topologies:**

| Topology | Description |
|---|---|
| **Direct Attach** | Server directly connects to the array (no switch). |
| **Fibre Channel Switched Fabric (FC-SW)** | Servers and arrays connect to FC switches. Most common. |
| **iSCSI** | IP-based; uses Ethernet switches. Easier. |
| **Converged** | FC and Ethernet on the same physical infrastructure (FCoE). |

**SAN in Cloud:**

| Cloud | SAN Equivalent |
|---|---|
| **AWS** | EBS (Elastic Block Store) — block storage, accessible from one EC2 at a time. (EBS is SAN-like, not exactly SAN.) |
| **Azure** | Azure Disk Storage (Premium SSD, Ultra Disk) — block storage. |
| **GCP** | Persistent Disk — block storage. |
| **On-prem** | True SAN (NetApp, EMC, Pure). |

**SAN vs. NAS (Network Attached Storage):**

| SAN | NAS |
|---|---|
| **Block-level** (raw blocks). | **File-level** (files/folders). |
| Appears as a local disk. | Appears as a network share (NFS, SMB). |
| Single host (typically) per LUN. | Multiple hosts share. |
| Higher performance. | Higher convenience. |
| FC, iSCSI. | NFS, SMB. |
| Used for: databases, VMs. | Used for: file shares, content. |

**Hypervisor Integration:**
- vSphere: VMFS (VMware File System) on FC/iSCSI LUNs. Datastores created on the LUNs.
- Hyper-V: Cluster Shared Volumes (CSV) over SMB or FC.
- KVM: LVM, Ceph, or simple filesystem on SAN.

**Advantages:**
- **High performance** — dedicated network, low latency.
- **Shared** — multiple hosts can access (with reservation).
- **HA** — array has redundant controllers, disks.
- **Replication** — many arrays support synchronous/async replication for DR.
- **Snapshots** — instant, low-overhead.
- **Enables live migration** — VMs can move between hosts accessing the same shared storage.

**Disadvantages:**
- **Cost** — expensive (FC switches, HBAs, arrays).
- **Complexity** — FC SAN requires expertise.
- **Single point of failure** (mitigated) — the array itself.
- **Distance limitations** — FC has distance limits (10 km per hop).

**When to use:**
- **Enterprise datacenters** with mission-critical VMs.
- **VMware vSphere / Hyper-V clusters** — enables vMotion, HA.
- **Databases** that need high IOPS and low latency.
- **DR** with array-level replication.

**When NOT to use:**
- Small environments (use local or NAS).
- Cloud-native (use cloud block storage).
- When cost is the primary driver.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — Stand-Alone VMs in the Wild

**AWS Example — Dev/Test on EC2:**
- A developer launches a single `t3.medium` EC2 instance for testing.
- No Auto Scaling, no load balancer — just a single VM.
- If the instance fails, the dev restarts it.

**Azure Example — Quick VM for Demo:**
- A sales engineer spins up a single Azure VM to demo a product to a customer.
- No HA, no clustering. Deallocated after the demo.

**GCP Example — Test VM:**
- A QA tester creates a single Compute Engine VM with a specific OS version for compatibility testing.
- Deleted after the test.

---

### 3.2 — Clustering in the Wild

**AWS Example — Auto Scaling Group + ALB:**
- A company runs 50 EC2 instances across an Auto Scaling Group.
- The ASG spans 3 AZs (high availability).
- ALB distributes traffic; if an instance fails, ASG replaces it.
- This is the cloud-equivalent of a cluster (managed for you).

**Azure Example — VM Scale Sets:**
- A company uses VM Scale Sets (VMSS) with 100 VMs.
- VMSS automatically scales out/in based on metrics.
- Built-in HA via Availability Zones.
- Custom autoscaler for advanced scenarios.

**GCP Example — Managed Instance Groups (MIGs):**
- A company uses a regional MIG with auto-healing and auto-scaling.
- MIG health-checks each instance; failed instances are recreated.
- Auto-balances across zones.

**On-Prem Example — VMware vSphere Cluster:**
- A typical enterprise runs 4–16 ESXi hosts in a vSphere cluster.
- vCenter manages the cluster.
- HA, DRS, vMotion enabled.
- Storage on a SAN (NetApp, Pure, EMC).
- This is the "private cloud" of many enterprises.

---

### 3.3 — Cloning in the Wild

**AWS Example — Golden AMI:**
- A company builds a "golden AMI" with the OS, patches, monitoring agent, and base config.
- New EC2 instances are launched from this AMI.
- EC2 Image Builder automates the patching and AMI refresh.

**Azure Example — Shared Image Gallery:**
- A company uses Azure Shared Image Gallery to store multiple versions of a custom image.
- Image definitions per region, image versions per build.
- AKS uses these images for node pools.
- AVD (Azure Virtual Desktop) uses them for golden desktop images.

**GCP Example — Custom Image:**
- A company creates a custom image from a configured VM (with COS, agents, base config).
- Instance templates use this image to create MIGs.
- New VMs are consistent.

**On-Prem Example — VDI with Linked Clones:**
- A company uses VMware Horizon or Citrix for VDI.
- Each user's desktop is a linked clone from a master Windows image.
- Fast provisioning (seconds), low storage use.
- Parent image is patched centrally; linked clones inherit the patches on next refresh.

---

### 3.4 — Host Affinity in the Wild

**AWS Example — Spread Placement Group:**
- A company runs 7 critical database replicas in a Spread Placement Group.
- Each instance is on a separate rack (max 7 per group).
- A single hardware failure takes out only 1 replica.

**AWS Example — Cluster Placement Group:**
- An HPC workload uses a Cluster Placement Group for low-latency inter-node communication.
- All instances are on the same AZ, same rack, close together.

**Azure Example — Proximity Placement Group:**
- A latency-sensitive app (e.g., SAP HANA) uses a Proximity Placement Group.
- All VMs are placed in the same data center for sub-ms latency.

**GCP Example — Sole-Tenant Nodes:**
- A company with per-socket Windows licensing uses Sole-Tenant Nodes.
- The VM is pinned to specific physical hardware.
- License is honored.

**On-Prem Example — VMware DRS Rules:**
- A company creates a "Must Run" rule: the database VMs must run on hosts with high-memory configuration.
- Creates a "Should Not Run" rule: app servers should not run on the same host as the database (to balance load).

---

### 3.5 — Hardware Pass-Through in the Wild

**AWS Example — GPU EC2 for ML:**
- A company uses `p5.48xlarge` instances for ML training.
- Each instance has 8 NVIDIA H100 GPUs.
- Bare metal (`.metal` size) for full hardware access.

**Azure Example — ND H100 v5 for AI:**
- A company uses ND H100 v5 VMs for training large language models.
- 8 NVIDIA H100 GPUs per VM, InfiniBand interconnect for multi-node training.

**GCP Example — A3 VM with GPUs:**
- A company uses A3 instances for AI inference.
- NVIDIA H100 GPUs with high-bandwidth networking.

**On-Prem Example — Virtual Desktop with vGPU:**
- A company uses NVIDIA vGPU for VDI.
- Each user gets a share of a physical GPU for graphics-intensive apps.
- Better user experience than software-rendered desktops.

**On-Prem Example — SR-IOV for NFV:**
- A telco uses SR-IOV to give VNFs (Virtual Network Functions) near-bare-metal network performance.
- Each VM gets a VF (Virtual Function) of a 100 Gbps NIC.

---

### 3.6 — VM Networks and Overlay in the Wild

**AWS Example — VPC = VXLAN Overlay:**
- AWS VPC is implemented as an overlay network on top of AWS's global network.
- Each VPC has a unique VXLAN Network Identifier (VNI).
- Customers don't see this; it's transparent.

**Azure Example — VNet = VXLAN:**
- Azure VNet uses VXLAN for tenant isolation.
- Each VNet has its own address space, isolated from other tenants.

**GCP Example — Andromeda SDN:**
- GCP's VPC is built on Andromeda, Google's SDN.
- Subnets are regional (unique to GCP).
- VPC is global; subnets span regions.

**On-Prem Example — VMware NSX:**
- A company uses VMware NSX for network virtualization.
- NSX creates overlay networks (GENEVE) on top of the physical underlay.
- Microsegmentation via distributed firewall.

**Kubernetes Example — Calico VXLAN:**
- A K8s cluster uses Calico CNI in VXLAN mode.
- Pods in different nodes communicate via VXLAN tunnels.
- Provides tenant isolation and L3 networking.

---

### 3.7 — Storage in the Wild

**AWS Example — EBS as Cloud SAN:**
- AWS EBS provides block storage to EC2 instances.
- EBS is a managed SAN-like service (under the hood, it's distributed storage).
- Multi-Attach io2 for clusters that need shared block storage.

**Azure Example — Azure Disk for VMs:**
- A company uses Premium SSD v2 for SQL Server on Azure VMs.
- 80K IOPS, sub-ms latency.
- Snapshots for backup.

**GCP Example — Persistent Disk:**
- A company uses Balanced Persistent Disk for VMs.
- Automatic replication within a zone.
- Snapshots stored in Cloud Storage.

**On-Prem Example — VMware vSAN:**
- A company uses vSAN (hyperconverged) — local disks of all hosts in a cluster are pooled into a shared datastore.
- No external SAN needed; hosts contribute disks.
- Distributed, resilient.

**On-Prem Example — NetApp SAN:**
- A bank uses a NetApp AFF SAN for their VMware cluster.
- FC connectivity to each ESXi host.
- SnapMirror for DR replication to a second site.
- Live migration (vMotion) works because all hosts see the same shared LUNs.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 — Stand-Alone vs Clustered VMs

| Attribute | Stand-Alone | Clustered |
|---|---|---|
| **Hosts** | 1 | 2+ |
| **HA** | No | Yes (auto-restart on host failure) |
| **Live migration** | No | Yes (vMotion, Live Migration) |
| **Resource pooling** | No | Yes (aggregate host resources) |
| **Management** | Per-host | Cluster-wide (vCenter, SCVMM) |
| **Cost** | Low (1 host) | High (multiple hosts, shared storage) |
| **Complexity** | Low | High |
| **Use case** | Dev/test, isolated workloads | Production, mission-critical |

---

### 4.2 — Full Clone vs Linked Clone

| Attribute | Full Clone | Linked Clone |
|---|---|---|
| **Independence** | Fully independent | Depends on parent |
| **Storage** | Full copy of parent | Delta only |
| **Speed to create** | Slower (copy) | Fast (delta) |
| **Survives parent deletion?** | Yes | No |
| **Use case** | Production VMs | VDI, dev/test, ephemeral |
| **CompTIA tip** | "Long-lived, production" | "Short-lived, VDI" |

---

### 4.3 — Affinity vs Anti-Affinity

| Attribute | Affinity (Keep Together) | Anti-Affinity (Keep Apart) |
|---|---|---|
| **Behavior** | VMs run on same host | VMs run on different hosts |
| **Use case** | Low-latency, related workloads | HA, fault tolerance |
| **Example** | App + cache on same host | DB replicas on different hosts |
| **Trade-off** | Better performance, worse HA | Better HA, worse latency |

---

### 4.4 — Hardware Pass-Through Technologies

| Technology | Description | Sharing | Use Case |
|---|---|---|---|
| **PCI Passthrough** | Whole device to one VM | No | GPU, NVMe |
| **SR-IOV** | One device, multiple VFs | Yes (VFs) | High-perf networking |
| **vGPU** | One GPU, many VMs | Yes (time/MIG) | VDI, AI |
| **USB Passthrough** | USB device to one VM | No | License dongles |
| **NPIV** | Virtual FC HBA | Yes | SAN access from VMs |

---

### 4.5 — VM Network Types

| Type | Description | Use Case |
|---|---|---|
| **vSwitch (standard)** | Per-host virtual switch | Simple setups |
| **Distributed vSwitch (VDS)** | Cluster-wide virtual switch | Consistent config across cluster |
| **Open vSwitch (OVS)** | Open-source vSwitch | KVM, OpenStack, K8s |
| **Linux Bridge** | Native Linux bridge | KVM |
| **Overlay (VXLAN, GENEVE)** | Virtual network on top of physical | Multi-tenant, cloud |

---

### 4.6 — Local vs SAN Storage

| Attribute | Local | SAN |
|---|---|---|
| **Scope** | Single host | Multiple hosts |
| **Network** | Direct (DAS) | Dedicated (FC, iSCSI) |
| **Performance** | Highest | High (with low-latency FC) |
| **HA** | No (host failure = data loss) | Yes (array redundancy) |
| **Live migration** | No (unless storage is also local) | Yes (shared LUNs) |
| **Cost** | Low | High |
| **Use case** | Ephemeral, high-IOPS, dev/test | Production VMs, databases |
| **Cloud equivalent** | Instance store, local SSD | EBS, Azure Disk, Persistent Disk |

---

### 4.7 — SAN vs NAS

| Attribute | SAN | NAS |
|---|---|---|
| **Protocol** | FC, iSCSI | NFS, SMB |
| **Level** | Block | File |
| **Access** | Single host per LUN (typically) | Multiple hosts share files |
| **Performance** | Higher | Lower |
| **Use case** | Databases, VMs, high-IOPS | File shares, content, home dirs |
| **Examples** | NetApp AFF, Pure Storage, EMC PowerMax | NetApp FAS, Isilon, Windows Server |

---

### 4.8 — Hypervisor Vendors (Detailed)

| Hypervisor | Vendor | Type | Notes |
|---|---|---|---|
| **VMware ESXi** | VMware (Broadcom) | Type 1 (bare metal) | Industry standard, expensive. |
| **Microsoft Hyper-V** | Microsoft | Type 1 | Windows-based. |
| **KVM** | Red Hat / Linux | Type 1 (Linux kernel) | Open source, used by AWS, GCP, Azure. |
| **Xen** | Citrix / Linux Foundation | Type 1 | Used in older AWS. |
| **Nutanix AHV** | Nutanix | Type 1 (KVM-based) | Hyperconverged. |
| **Oracle VM Server** | Oracle | Type 1 (Xen-based) | Oracle workloads. |
| **Proxmox VE** | Proxmox | Type 1 (KVM-based) | Open source, popular. |
| **VirtualBox** | Oracle | Type 2 (hosted) | Desktop, dev/test. |
| **VMware Workstation / Fusion** | VMware | Type 2 | Desktop. |
| **Parallels** | Parallels | Type 2 | Mac. |

> **Type 1 (bare-metal):** Runs directly on hardware. Used in production data centers and cloud.
> **Type 2 (hosted):** Runs on a host OS. Used for dev/test, desktop virtualization.

---

## SECTION 5 — EXAM TRAPS

### Trap 1: "Stand-alone VM = production"
- **Trap:** "Single VM on a single host is fine for production."
- **Wrong.** Single host = single point of failure. Production = cluster with HA.

### Trap 2: "Linked clone = production VM"
- **Trap:** "Linked clones are great for everything."
- **Wrong.** Linked clones depend on the parent. If the parent is deleted, clones break. Use full clones for production.

### Trap 3: "Affinity = always good"
- **Trap:** "Keep related VMs together for performance."
- **Wrong:** Affinity is sometimes good (low latency) but reduces HA. For replicas, use **anti-affinity** (keep apart).

### Trap 4: "GPU passthrough = always better"
- **Trap:** "Use GPU passthrough for all GPU workloads."
- **Wrong:** GPU passthrough is one VM per GPU. For VDI or multi-tenant, use **vGPU** (shared GPU).

### Trap 5: "VLAN = 16M networks"
- **Trap:** "VXLAN and VLAN are the same."
- **Wrong:** VLAN supports 4,094 networks (12-bit ID). VXLAN supports **16 million** networks (24-bit VNI). Cloud scale requires VXLAN.

### Trap 6: "Local storage = no production"
- **Trap:** "Local storage is for dev/test only."
- **Half-true:** Local is risky because host failure = data loss. But high-IOPS workloads (databases) often use local NVMe for performance — paired with replication to SAN/object for HA.

### Trap 7: "SAN = NAS"
- **Trap:** "SAN and NAS are the same."
- **Wrong.** SAN = block (raw). NAS = file (NFS, SMB). Different access patterns.

### Trap 8: "Cluster = automatic HA"
- **Trap:** "Cluster = VMs always recover from host failure."
- **Half-true:** HA works if there are enough cluster resources. If the cluster is full, HA may fail. Capacity planning is required.

### Trap 9: "Live migration = zero downtime"
- **Trap:** "Live migration always works with zero downtime."
- **Wrong:** Live migration requires shared storage. Local storage prevents it. Also, certain workloads (high-IOPS) may pause briefly.

### Trap 10: "Hardware pass-through = easy"
- **Trap:** "PCI passthrough is just a checkbox."
- **Wrong:** Requires CPU support (VT-d, AMD-Vi), BIOS config, driver setup, and breaks live migration.

### Trap 11: "VDS = per-host vSwitch"
- **Trap:** "VDS is a regular vSwitch."
- **Wrong:** VDS (Distributed vSwitch) is cluster-wide — same config across all hosts. Standard vSwitch is per-host.

### Trap 12: "Template = running VM"
- **Trap:** "Templates are VMs you can boot."
- **Wrong:** Templates are read-only masters. You deploy from them, but you don't power them on.

### Trap 13: "VM cloning = backups"
- **Trap:** "Cloned VMs are backups."
- **Wrong:** A clone is a copy at a point in time — not a backup. Use snapshots, replication, or backup software for actual backups.

### Trap 14: "SR-IOV = PCI passthrough"
- **Trap:** "SR-IOV is the same as PCI passthrough."
- **Wrong:** PCI passthrough is one device to one VM. SR-IOV is one device to many VMs (via VFs).

### Trap 15: "Overlay = slower"
- **Trap:** "Overlay networks are always slower than physical."
- **Mostly true but:** In modern data centers with 25/100 Gbps networks, overlay overhead is negligible. Cloud is entirely overlay.

---

## SECTION 6 — PERFORMANCE-BASED QUESTION PREP (PBQs)

### PBQ 1 — VMware Cluster Design

**Scenario:**
A company is deploying a new VMware vSphere cluster for production:
- 4 ESXi hosts, each with 256 GB RAM and 32 cores.
- Mission-critical: web tier, app tier, database tier.
- Web tier: 10 VMs (load balanced).
- App tier: 5 VMs.
- Database: 1 VM with replicas.

Requirements:
- HA for all VMs.
- Web tier VMs should NOT all fail together (anti-affinity).
- Database and app should be on separate hosts (for performance).
- Live migration required for maintenance.

**Question:** Design the cluster, affinity rules, and storage.

**Walkthrough:**
1. **Cluster:** 4 ESXi hosts joined to a vCenter cluster.
2. **HA:** Enabled. vSphere HA monitors hosts; failed VMs restart on surviving hosts.
3. **DRS:** Enabled. Auto-balances VMs across hosts.
4. **vMotion:** Enabled. Live migration works (shared storage).
5. **Affinity rules:**
   - **Web tier (10 VMs):** Anti-affinity — each on a different host. (Or: anti-affinity groups of 2 across 4 hosts.)
   - **App tier (5 VMs):** Anti-affinity — each on a different host.
   - **DB primary + replicas:** Anti-affinity — each on a different host.
   - **DB and App:** Anti-affinity (separate hosts for performance).
6. **Storage:** Shared SAN (e.g., NetApp, Pure). LUNs created and formatted as VMFS.
7. **Network:** Distributed vSwitch (VDS) for consistent config.
8. **Result:** HA + DRS + vMotion + anti-affinity = resilient, performant cluster.

---

### PBQ 2 — Cloning Strategy for VDI

**Scenario:**
A company is rolling out VDI for 500 users. They want:
- Fast provisioning (each desktop in seconds).
- Low storage cost.
- Centralized patching.

**Question:** What's the cloning strategy?

**Walkthrough:**
1. **Build golden image:** Windows 10/11 + apps + config.
2. **Convert to template** (sealed, generalized).
3. **Use linked clones** for user desktops.
   - Each user desktop is a linked clone of the golden image.
   - Storage: only the user's deltas (5–20 GB) vs. full image (~50 GB).
   - 500 users × 10 GB = 5 TB total (vs. 25 TB with full clones).
4. **Patching:** Update the golden image; linked clones inherit on next refresh.
5. **Persistence:** User data on a separate persistent disk (not part of the linked clone).
6. **Result:** 5x storage savings, fast provisioning, centralized patching.

---

### PBQ 3 — Anti-Affinity for HA in Cloud

**Scenario:**
A company has 3 EC2 instances running a critical API. They want to ensure that no single host failure takes all 3 down.

**Question:** How do you enforce this in AWS?

**Walkthrough:**
1. **Option 1: Spread Placement Group** — AWS ensures each instance is on a different rack. Max 7 per group.
2. **Option 2: Auto Scaling Group across 3 AZs** — instances are distributed across AZs.
3. **Option 3: Mix of instance types and AZs** — use diverse instance families and AZs.
4. **Decision:** Spread Placement Group (rack-level isolation) is most strict. For 3 instances, this works well.
5. **Caveat:** Spread Placement Group has a hard limit of 7 instances per group. For more, use Partition Placement Group (up to 7 partitions × many instances per partition).

---

### PBQ 4 — Hardware Pass-Through for AI

**Scenario:**
A data science team needs to train ML models with maximum GPU performance. They use a private cloud (KVM-based) with NVIDIA GPUs. They need:
- Near-native GPU performance.
- A single VM uses the entire GPU.

**Question:** What technology?

**Walkthrough:**
1. **PCI passthrough with IOMMU** (Intel VT-d or AMD-Vi).
2. The hypervisor assigns the physical GPU to the VM.
3. VM installs NVIDIA driver; uses the GPU as if it owned the host.
4. **Trade-off:** No live migration (passthrough breaks it), no GPU sharing.
5. **Alternative:** If multiple VMs need GPU, use **vGPU** (time-sliced or hardware-partitioned).
6. **For pure training performance:** Passthrough is best.
7. **For VDI / shared use:** vGPU.

**Bonus Q:** What about MIG (Multi-Instance GPU) on H100?
- **Answer:** MIG allows hardware-level partitioning of a single H100 into up to 7 isolated instances. Each acts like a separate GPU. Best of both worlds — performance + sharing.

---

### PBQ 5 — Storage Choice for VM Cluster

**Scenario:**
A company is designing a VMware vSphere cluster. They need:
- Live migration (vMotion) between hosts.
- HA for VMs.
- High performance (database VMs need 50K IOPS).
- Shared storage accessible by all hosts.

**Question:** What storage type?

**Walkthrough:**
1. **Need:** Shared, high-performance, supports vMotion + HA.
2. **Options:**
   - **Local storage** (each host's disks): Doesn't support vMotion (no shared). Fail.
   - **NAS (NFS):** Supports vMotion (shared). Moderate performance. OK for some workloads.
   - **SAN (FC or iSCSI):** Supports vMotion, high performance, low latency. Best for databases.
   - **vSAN (hyperconverged):** Combines local disks into a shared datastore. Supports vMotion. Good performance.
3. **Decision:** For 50K IOPS database: **FC SAN** (best latency) or **vSAN with all-flash** (more flexible).
4. **Trade-offs:**
   - FC SAN: high cost, complex, requires FC switches.
   - vSAN: lower cost, easier, requires only local SSDs.
   - NAS: easier, lower perf.
5. **Result:** Pick based on budget and ops maturity.

---

### PBQ 6 — Overlay Networking for Multi-Tenancy

**Scenario:**
A cloud provider has 10,000 customers on shared physical infrastructure. Each customer needs an isolated network. The provider's physical network is a flat L3 fabric.

**Question:** How do they provide isolated virtual networks?

**Walkthrough:**
1. **Use VXLAN overlay:**
   - Each customer VPC is a VXLAN with a unique VNI.
   - Customer's VMs send/receive packets; provider's hypervisors (or ToR switches with VXLAN) encapsulate in VXLAN.
   - Underlay routes the encapsulated packet to the destination.
   - Destination decapsulates and forwards to the inner destination.
2. **Scale:** VXLAN supports 16M VNIs (way more than enough).
3. **Multi-tenancy:** Each tenant's VXLAN is isolated; they can't see each other.
4. **Real-world:** This is exactly how AWS, Azure, GCP work internally.
5. **Bonus:** Modern switches support **VXLAN routing** (EVPN + VXLAN) for hardware-accelerated overlays.

---

## SECTION 7 — MEMORY AIDS

### 🎯 Stand-Alone vs Clustered Mnemonic: **"Solo = Sad"**

- **Solo** stand-alone VM = **S**ad (no HA).
- **Cluster** = **C**heerful (HA, scaling).

### 🎯 Cluster Features Mnemonic: **"H-D-V-F"**

- **H**A — restart on host failure.
- **D**RS — auto-balance.
- **V**Motion — live migration.
- **F**ault Tolerance — lockstep VM (VMware-specific).

### 🎯 Cloning Mnemonic: **"Full = Fork, Linked = Leaf"**

- **Full clone** = a complete **fork** — independent.
- **Linked clone** = a **leaf** on a branch — depends on the parent.

### 🎯 Affinity Mnemonic: **"**A**ffinity = **A**ttract, Anti-Affinity = **A**void"**

- **A**ffinity: VMs **a**ttract each other (same host).
- **A**nti-affinity: VMs **a**void each other (different hosts).
- **Memory:** "Both start with A, but one is for together, one is for apart."

### 🎯 Hardware Pass-Through Mnemonic: **"Passthrough is Private, SR-IOV is Shared"**

- **PCI Passthrough** = the whole device is **private** to one VM.
- **SR-IOV** = the device is **shared** (multiple VFs).

### 🎯 Overlay vs VLAN Mnemonic: **"VLAN = 4K, VXLAN = 16M"**

- **V**LAN: 4,094 networks (12-bit).
- **V**XLAN: 16 million networks (24-bit).
- "**V**XLAN is **V**ery e**X**tra Large."

### 🎯 Storage Mnemonic: **"Local = Loco, SAN = Solid, NAS = Nice-and-Shared"**

- **Local** = **L**oco (crazy fast, but no HA).
- **SAN** = **S**olid (block, performance, expensive).
- **NAS** = **N**ice-**A**nd-**S**hared (file, multiple hosts).

### 🎯 SAN vs NAS Mnemonic: **"B-F"**

- **SAN = Block** (raw disks).
- **NAS = Files** (NFS, SMB).
- "**B**locks are **B**are. **F**iles are **F**riendly."

### 🎯 Template vs Clone Mnemonic: **"Template = Master Cookie Cutter"**

- A template is a master mold.
- Clones are the cookies.
- You don't eat the mold (template isn't powered on).

### 🎯 vGPU vs GPU Passthrough Mnemonic: **"Pass = Personal, vGPU = Vacation"**

- **GPU Passthrough** = **Personal** GPU (one VM, full power).
- **vGPU** = **Vacation** GPU (shared, time-sliced).

### 🎯 Live Migration Mnemonic: **"Live = Latency-Intact Virtual Environment"**

- VM moves without stopping.
- Requires shared storage.
- "If storage is local, live migration is impossible."

### 🎯 Right Storage Decision

```
Q: Single host, ephemeral, fast?
└── YES → Local (NVMe / SSD)

Q: Multi-host, shared, high-IOPS?
└── YES → SAN (FC / iSCSI)

Q: Multi-host, shared, file-level?
└── YES → NAS (NFS / SMB)

Q: Multi-host, want vMotion + HA?
└── YES → Shared storage (SAN or vSAN)

Q: Cloud?
└── YES → Block storage (EBS / Disk / PD)
```

### 🎯 Right Cluster Decision

```
Q: HA required?
├── NO  → Stand-alone VM
└── YES → Cluster (2+ hosts, shared storage)

Q: Live migration needed?
├── NO  → Stand-alone or clustered
└── YES → Cluster with shared storage

Q: Mission-critical?
├── NO  → Single host, no HA
└── YES → Cluster + HA + DR + anti-affinity

Q: Cost > uptime?
└── YES → Stand-alone or pilot light
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

### Hypervisors Used by Cloud Providers

| Cloud | Hypervisor | Notes |
|---|---|---|
| **AWS** | Xen (older), Nitro (KVM-based, custom) | Nitro is the current system (since ~2017). |
| **Azure** | Hyper-V (Windows-based) | Custom Azure hypervisor (still Hyper-V root). |
| **GCP** | KVM (Linux-based) | Custom, optimized. |
| **Oracle Cloud** | KVM | |
| **Alibaba Cloud** | KVM | |

### Managed VM Services (Cloud Cluster Equivalents)

| Cloud | VM Service | Cluster Features |
|---|---|---|
| **AWS** | EC2 + ASG + ALB | ASG = auto-scaling, AZ isolation. Placement groups for affinity. |
| **Azure** | VMSS + Availability Zones | VMSS = auto-scaling, AZ isolation. Proximity Placement Groups for latency. |
| **GCP** | MIGs + Regional Managed Instance Groups | Auto-healing, auto-scaling, regional distribution. |

### Cloning / Image Services

| Cloud | Service | Notes |
|---|---|---|
| **AWS** | AMI + EC2 Image Builder | AMIs are the templates. Image Builder automates patching. |
| **Azure** | Managed Image + Shared Image Gallery + Azure VM Image Builder | SIG for global image distribution. |
| **GCP** | Custom Image + Image Builder | Less mature than AWS. |
| **On-prem** | VMware Template + Content Library | Standard. |

### Host Affinity Services

| Cloud | Affinity Service |
|---|---|
| **AWS** | Placement Groups (Cluster, Spread, Partition), Dedicated Hosts, Host Resource Groups. |
| **Azure** | Proximity Placement Groups, Availability Sets, Capacity Reservations, Dedicated Hosts. |
| **GCP** | Sole-Tenant Nodes, Affinity/Anti-affinity labels in MIGs, Placement Policies. |
| **On-prem** | DRS Rules (VMware), Affinity Rules (Hyper-V), oVirt policies. |

### Hardware Pass-Through (GPU and Specialized)

| Cloud | GPU / Specialized Service |
|---|---|
| **AWS** | P-series (P4, P5) — NVIDIA H100. G-series (G5) — NVIDIA A10. Trainium (custom AI chip). Inferentia (custom inference). Bare metal (`.metal`) for full hardware. |
| **Azure** | N-series — NVIDIA. ND H100 v5 — InfiniBand for multi-node. Confidential VMs. |
| **GCP** | A2/A3 — NVIDIA H100/A100. T2D/T2A — custom ARM. Confidential VMs. Bare metal. |
| **On-prem** | NVIDIA vGPU, AMD MxGPU, Intel GVT-g, SR-IOV, PCI passthrough. |

### VM Network / Overlay

| Cloud | Network Type | Notes |
|---|---|---|
| **AWS** | VPC — VXLAN-based overlay | Transparent to customer. |
| **Azure** | VNet — VXLAN-based overlay | Transparent. |
| **GCP** | VPC — Andromeda SDN (custom overlay) | Global VPC, regional subnets. |
| **On-prem** | VMware NSX — GENEVE overlay | Microsegmentation, distributed firewall. |
| **K8s** | Calico, Cilium — VXLAN or IP-in-IP | Pod-to-pod networking. |

### Storage (Cloud VM Block Storage)

| Cloud | Block Storage | Equivalent to SAN? |
|---|---|---|
| **AWS** | EBS (gp3, io2, st1, sc1) | SAN-like (managed, replicated, but accessible from one VM at a time). |
| **Azure** | Disk Storage (Premium SSD, Standard SSD, Ultra Disk) | SAN-like. |
| **GCP** | Persistent Disk (Standard, Balanced, SSD, Extreme) | SAN-like. |
| **On-prem** | NetApp, Pure Storage, EMC PowerMax, HPE 3PAR | True SAN. |

### Storage (Hyperconverged)

| Vendor | Product | Notes |
|---|---|---|
| **Nutanix** | Nutanix HCI | KVM-based, distributed storage on local disks. |
| **VMware** | vSAN | Aggregates local disks into shared datastore. |
| **Microsoft** | Storage Spaces Direct (S2D) | Hyper-V + local disks = shared. |
| **Scale Computing** | HC3 | KVM + local storage. |
| **Open Source** | Ceph, GlusterFS, MinIO | Software-defined storage. |

---

## SECTION 9 — INTERVIEW KNOWLEDGE

### 🎓 Junior Cloud Engineer Questions

**Q1: What's a hypervisor?**
**Ideal answer:** "Software that creates and runs virtual machines. Type 1 (bare-metal) runs directly on hardware (VMware ESXi, Hyper-V, KVM). Type 2 (hosted) runs on a host OS (VirtualBox, VMware Workstation). Production cloud uses Type 1."

**Q2: What's the difference between a VM and a container?**
**Ideal answer:** "A VM runs a full guest OS on a hypervisor. A container shares the host OS kernel and packages just the app + dependencies. VMs are heavier (more isolation, more resources). Containers are lighter (faster startup, more density). Use VMs for OS-level control or different OS types; use containers for cloud-native, fast-scaling apps."

**Q3: What is a vMotion / live migration?**
**Ideal answer:** "Moving a running VM from one host to another without downtime. Used for maintenance, load balancing, or DR. Requires shared storage (so the VM's disks are accessible from both hosts). Without shared storage, the VM's disk must also be migrated (Storage vMotion), which is slower."

**Q4: What is vSAN?**
**Ideal answer:** "VMware's hyperconverged storage. Local disks of all hosts in a cluster are pooled into a shared datastore. No external SAN needed. Each host contributes its disks; the cluster presents a unified datastore to VMs."

**Q5: What is a VMware HA cluster?**
**Ideal answer:** "A group of ESXi hosts managed together for high availability. If a host fails, its VMs are automatically restarted on other hosts in the cluster. Requires shared storage, redundant networking, and vCenter management."

**Q6: What is a template?**
**Ideal answer:** "A read-only master VM used to create new VMs. The template is built with the OS, patches, and standard software. New VMs are deployed from the template via full clone or linked clone. Templates ensure consistency across deployments."

**Q7: What is VXLAN?**
**Ideal answer:** "Virtual Extensible LAN — an overlay network protocol that encapsulates Layer 2 Ethernet frames inside Layer 3 UDP packets. Used to create isolated virtual networks on top of a shared physical network. Supports 16 million logical networks (vs. 4,094 for VLAN). Used by AWS, Azure, GCP for tenant isolation."

---

### 🎓 Cloud Administrator Questions

**Q1: How do you design a VMware cluster for HA?**
**Ideal answer:** "Several requirements:
1. **Multiple hosts** (3+ minimum, 4+ recommended) — for redundancy.
2. **Shared storage** (SAN, NAS, or vSAN) — for vMotion and HA to work.
3. **Redundant networking** — multiple NICs, separate switches for HA traffic and storage traffic.
4. **vCenter** — cluster management.
5. **HA enabled** — vSphere HA monitors hosts; failed VMs restart on survivors.
6. **DRS enabled** — auto-balances VMs.
7. **Anti-affinity rules** — keep replicas on different hosts.
8. **Capacity planning** — N+1 or N+2 (cluster can survive 1 or 2 host failures).
9. **Proper licensing** — vSphere Enterprise Plus for full feature set.
10. **Backup solution** — Veeam, Commvault, etc., for VM backups."

**Q2: A critical VM in your cluster just failed and HA didn't restart it. What's the most likely reason?**
**Ideal answer:** "Common reasons:
1. **Insufficient cluster resources** — no other host has enough capacity. Solution: add hosts or reduce VM sizes.
2. **HA is not enabled** — check cluster settings.
3. **VM is configured for 'do not restart'** — check VM HA settings.
4. **Storage is unavailable** — the VM can't access its disks. Check storage connectivity.
5. **Network isolation** — the host is isolated from the cluster (network partition). Check network.
6. **VM is constrained by affinity rules** — anti-affinity may have prevented restart. Check rules.
7. **VM has a host affinity to the failed host** — and the cluster can't satisfy it.
Most common: cluster resource exhaustion."

**Q3: A company has 1 PB of VM storage on NetApp SAN. They want to migrate to AWS. What are the options?**
**Ideal answer:** "Several options:
1. **Lift-and-shift:** Convert VMs to EC2 + EBS. Use AWS Application Migration Service (CloudEndure) for replication. Best for short migrations.
2. **Refactor:** Re-architect to use cloud-native services (RDS instead of EC2 DB, S3 instead of SAN). Best long-term.
3. **Cloud Volume ONTAP (NetApp):** Run NetApp in AWS as CVO. Same management as on-prem.
4. **FSx for NetApp ONTAP (AWS):** Managed NetApp in AWS. Lower ops.
5. **VMware Cloud on AWS:** Run VMware in AWS — vSphere cluster in AWS, with native AWS services.
6. **AWS DataSync / Snowball:** For large data transfers.
For 1 PB, the answer depends on:
- Timeline (urgent vs. months)
- Cost (lift-and-shift is fastest but may not optimize cost)
- Team skills (familiar with VMware or want to go cloud-native)
- Compliance (data residency)"

**Q4: A team uses linked clones for production. They delete the parent template and all VMs break. How do you prevent this?**
**Ideal answer:** "Several practices:
1. **Document the parent-child relationship** — keep a record of which template a clone came from.
2. **Use full clones for production** — independent, no parent dependency.
3. **If using linked clones in production:** Lock the parent (make it read-only, prevent deletion).
4. **Monitoring:** Alert if a template with active linked clones is being deleted.
5. **Convert to full clone:** Once a linked clone is in production, convert it to a full clone.
6. **Backup the parent** — so you can restore the template if accidentally deleted.
7. **Naming convention:** Make it clear which VMs are linked clones (e.g., prefix `lnk-`).
Best practice: linked clones for ephemeral (dev/test, VDI), full clones for production."

**Q5: A company has VMware VMs and wants to move to containers. How would you approach it?**
**Ideal answer:** "Strangler Fig Pattern — incremental migration:
1. **Identify candidates:** Stateless VMs (web servers, app servers) are easiest. Stateful VMs (databases) are harder.
2. **Containerize:** Convert each VM to a container. Use multi-stage Dockerfiles, small base images.
3. **Migrate traffic:** Use a load balancer or DNS to shift traffic from VM to container gradually.
4. **Validate:** Run both side-by-side, compare behavior.
5. **Decommission VM:** Once the container is stable, retire the VM.
6. **For stateful apps:** Externalize state (move DB to managed service, or use StatefulSet in K8s with PV/PVC).
7. **Tools:** Cloud Skillet (VMware), KubeVirt (run VMs in K8s), or manual conversion.
Don't do a big-bang rewrite. Migrate incrementally, learning as you go."

---

### 🎓 Cloud Support / Architecture Pre-Sales Questions

**Q1: A customer asks "What's the difference between a cluster and a cloud?" How do you explain?**
**Ideal answer:** "A cluster is a group of physical hosts (servers) managed together — usually in one data center, sharing storage and networking. A cloud is what you build when you take cluster technology and scale it to thousands of hosts, multiple data centers, with software-defined everything (networking, storage, identity). The cloud abstracts the underlying cluster — you don't see the hosts, you just consume resources via API. VMware vSphere is a cluster product. AWS is a cloud product — built on clusters at massive scale."

**Q2: A customer wants to run 1,000 VMs on-prem. What hypervisor would you recommend?**
**Ideal answer:** "It depends on:
1. **Team skills:** VMware expertise → vSphere (industry standard, mature, expensive). Linux expertise → KVM (open source, free, used by AWS/GCP).
2. **Existing infrastructure:** If they already have VMware, stick with it. If greenfield, consider KVM.
3. **Storage:** For SAN, VMware integrates well (VMFS). For HCI, vSAN or Nutanix.
4. **Budget:** VMware (Broadcom) is expensive (per-CPU licensing). KVM is free; you pay for support (Red Hat, Canonical).
5. **Support:** VMware has the strongest enterprise support. KVM support depends on the vendor.
For most enterprises, vSphere remains the safe choice. For cost-sensitive or Linux shops, KVM (via oVirt, Proxmox, or OpenStack) is a strong alternative."

**Q3: A prospect says "we want a private cloud, but we're not sure if we need VMware or OpenStack." How do you help?**
**Ideal answer:** "Compare them:
- **VMware vSphere:** Industry standard, mature, strong support, expensive, vendor lock-in.
- **OpenStack:** Open source, more flexible, no licensing cost, requires significant ops expertise to run.
- **Nutanix:** HCI (hyperconverged), simpler, less mature than VMware, mid-cost.
- **Proxmox VE:** Open source, KVM-based, popular in SMB, easy to set up.
Decision factors:
- **Team:** VMware-trained vs. Linux-trained.
- **Budget:** VMware expensive; OpenStack/Proxmox cheap.
- **Scale:** VMware proven at huge scale; OpenStack proven but more variable.
- **Existing relationship:** If they're a VMware shop, vSphere. If they want to escape VMware, OpenStack or KVM.
For most enterprises, VMware is the safe bet. For cost-sensitive or dev-focused teams, OpenStack or Proxmox is a strong alternative."

---

## SECTION 10 — FLASHCARDS

```
Q: What is a hypervisor?
A: Software that creates and runs virtual machines. Type 1 runs on bare metal. Type 2 runs on a host OS.

Q: What is Type 1 vs Type 2 hypervisor?
A: Type 1 = bare-metal (ESXi, Hyper-V, KVM). Type 2 = hosted (VirtualBox, VMware Workstation).

Q: What is a stand-alone VM?
A: A VM on a single host with no clustering. No HA, no live migration.

Q: What is a cluster of VMs?
A: Multiple hosts grouped together for HA, live migration, and resource pooling.

Q: What is vSphere HA?
A: VMware's HA feature — if a host fails, its VMs are automatically restarted on other hosts in the cluster.

Q: What is vMotion?
A: VMware's live migration — move a running VM from one host to another with zero downtime.

Q: What is DRS?
A: Distributed Resource Scheduler — VMware's auto-balancing feature. Moves VMs across hosts based on load.

Q: What is a VMware template?
A: A read-only master VM used to create new VMs. Cannot be powered on.

Q: What is a full clone?
A: A complete independent copy of a VM. Uses more storage, no parent dependency.

Q: What is a linked clone?
A: A VM that shares virtual disks with a parent template. Uses less storage, but depends on the parent.

Q: What is host affinity?
A: A rule that constrains which VMs run on which hosts. Affinity = together, anti-affinity = apart.

Q: What is anti-affinity used for?
A: HA — keep VM replicas on different hosts so a single host failure doesn't take them all down.

Q: What is a hardware pass-through?
A: Direct assignment of physical hardware (GPU, NIC) to a VM, bypassing the hypervisor.

Q: What is PCI passthrough?
A: Direct PCI device access to a VM. Uses IOMMU (VT-d / AMD-Vi). One device to one VM.

Q: What is SR-IOV?
A: Single Root I/O Virtualization — a physical device exposes multiple VFs, each assigned to a VM.

Q: What is vGPU?
A: A GPU shared across multiple VMs (time-sliced or hardware-partitioned). For VDI, AI inference.

Q: What is a virtual switch (vSwitch)?
A: A software-defined switch inside a hypervisor. Connects VMs to each other and to physical networks.

Q: What is a Distributed vSwitch (VDS)?
A: A cluster-wide virtual switch with consistent config across all hosts.

Q: What is an overlay network?
A: A virtual network built on top of a physical network. Encapsulates packets (VXLAN, GENEVE).

Q: What is VXLAN?
A: Virtual Extensible LAN — overlay protocol that supports 16 million logical networks (24-bit VNI). Used in cloud.

Q: What is the difference between VLAN and VXLAN?
A: VLAN supports 4,094 networks (12-bit). VXLAN supports 16 million (24-bit).

Q: What is local storage?
A: Disks directly attached to a host. Fast, no network, no HA.

Q: What is a SAN?
A: Storage Area Network — dedicated network providing block-level storage to multiple hosts.

Q: What is FC?
A: Fibre Channel — high-speed network protocol for SANs. Up to 32+ Gbps. Lowest latency.

Q: What is iSCSI?
A: SCSI over IP — block storage over Ethernet. Easier than FC, slightly higher latency.

Q: What is NAS?
A: Network Attached Storage — file-level storage (NFS, SMB). Multiple hosts share files.

Q: What is the difference between SAN and NAS?
A: SAN = block (raw disks). NAS = file (NFS, SMB). Different access patterns.

Q: What is vSAN?
A: VMware's hyperconverged storage. Local disks of hosts pooled into a shared datastore.

Q: What is a hyperconverged infrastructure (HCI)?
A: Compute, storage, and networking in one system. Local disks of hosts pool to form shared storage.

Q: What is a cloud golden image?
A: A pre-configured, patched template VM used to deploy new instances. Examples: AWS AMI, Azure Managed Image.

Q: What is an AWS AMI?
A: Amazon Machine Image — the template for launching EC2 instances. Includes OS, apps, config.

Q: What is an AWS Placement Group?
A: EC2 placement strategy: Cluster (low latency), Spread (different racks), Partition (different partitions).

Q: What is the difference between an instance store and EBS?
A: Instance store = local, ephemeral, lost on stop. EBS = network-attached, persistent, separate from host.

Q: What is a bare metal instance?
A: A cloud VM that runs directly on hardware (no hypervisor). For workloads needing full hardware access.

Q: What is a VMware HA admission control?
A: Policy that reserves cluster resources for HA — ensures there's enough capacity to restart VMs on host failure.

Q: What is Storage vMotion?
A: Migrating VM storage to another datastore without downtime.

Q: What is Fault Tolerance (FT)?
A: VMware feature that runs a VM in lockstep on two hosts. Zero downtime, zero data loss on host failure.

Q: What is VADP?
A: VMware vStorage APIs for Data Protection. Used by backup tools to back up VMs without LAN.

Q: What is Open vSwitch (OVS)?
A: Open-source virtual switch used in KVM, OpenStack, K8s. Supports OpenFlow, VXLAN, GENEVE.

Q: What is GENEVE?
A: Generic Network Virtualization Encapsulation — overlay protocol more flexible than VXLAN. Used in NSX.

Q: What is an MTU issue with overlays?
A: Overlay adds bytes to packets. Inner MTU must be reduced to fit inside the outer MTU. Default 1500 becomes ~1450 with VXLAN.

Q: What is VTEP?
A: VXLAN Tunnel Endpoint — encapsulates/decapsulates VXLAN packets. Typically a hypervisor or ToR switch.

Q: What is a Content Library in VMware?
A: A centralized repository for VM templates and ISO images. Replaces the older "templates" feature.

Q: What is a vSphere Distributed Switch (VDS)?
A: A cluster-wide virtual switch — consistent config across all hosts in the cluster.

Q: What is VMFS?
A: VMware File System — the cluster filesystem used by vSphere for VM storage. Supports shared access.

Q: What is NFS in the context of VM storage?
A: NFS (Network File System) can be used as a shared datastore for VMs. Easier than FC SAN, slightly higher latency.

Q: What is NPIV?
A: N_Port ID Virtualization — allows VMs to have their own virtual Fibre Channel HBA. For SAN access from VMs.

Q: What is KVM?
A: Kernel-based Virtual Machine — Linux kernel hypervisor. Open source, used by AWS, GCP, Oracle, Azure (some).
```

---

## SECTION 11 — PRACTICE QUESTIONS

### Q1 (Easy)
What is a hypervisor?
A) A container runtime
B) Software that creates and runs virtual machines
C) A network switch
D) A type of storage

**Answer: B) Software that creates and runs virtual machines**
**Explanation:** Hypervisors (ESXi, Hyper-V, KVM) abstract physical hardware into VMs.
**Why others are wrong:**
- A) Container runtimes are different (Docker, containerd).
- C) Switches are networking gear.
- D) Storage is for disks.

---

### Q2 (Easy)
What's the difference between a full clone and a linked clone?
A) Full clone is read-only.
B) Full clone is independent; linked clone depends on a parent.
C) They are the same.
D) Linked clone is for production.

**Answer: B) Full clone is independent; linked clone depends on a parent.**
**Explanation:** Full clone = full copy, no parent. Linked clone = shares disks with parent, has delta disk.
**Why others are wrong:**
- A) Full clone is read-write.
- C) Different.
- D) Full clone is for production (linked is for VDI/dev).

---

### Q3 (Easy)
What is vMotion?
A) Creating a VM.
B) Live migration of a running VM between hosts with zero downtime.
C) Backing up a VM.
D) Deleting a VM.

**Answer: B) Live migration of a running VM between hosts with zero downtime.**
**Explanation:** vMotion moves a running VM from one host to another. Used for maintenance, load balancing.
**Why others are wrong:**
- A) VM creation is provisioning.
- C) Backup is for DR.
- D) Delete removes the VM.

---

### Q4 (Medium)
Why is anti-affinity important for HA?
A) It improves performance.
B) It keeps VM replicas on different hosts so a single host failure doesn't take them all down.
C) It saves storage.
D) It enables live migration.

**Answer: B) It keeps VM replicas on different hosts so a single host failure doesn't take them all down.**
**Explanation:** Anti-affinity spreads replicas. If one host fails, only some replicas are affected.
**Why others are wrong:**
- A) Affinity (together) might improve performance, but anti-affinity is for HA.
- C) Storage isn't directly affected.
- D) Anti-affinity doesn't enable vMotion (shared storage does).

---

### Q5 (Medium)
A team uses linked clones for production VMs. What could go wrong?
A) Nothing — linked clones are fine for production.
B) Deleting the parent template breaks all linked clones.
C) Linked clones are slower.
D) Linked clones don't support networking.

**Answer: B) Deleting the parent template breaks all linked clones.**
**Explanation:** Linked clones depend on the parent. If the parent is deleted, clones break. Use full clones for production.
**Why others are wrong:**
- A) Linked clones are risky for production.
- C) Performance isn't significantly different.
- D) Networking works the same.

---

### Q6 (Medium)
A company wants a VM to use a GPU with near-native performance. What technology?
A) vGPU
B) PCI passthrough
C) vSwitch
D) SAN

**Answer: B) PCI passthrough**
**Explanation:** PCI passthrough (with IOMMU) gives the VM direct access to the GPU. Near-native performance. vGPU is for sharing.
**Why others are wrong:**
- A) vGPU is shared, lower per-VM performance.
- C) vSwitch is networking.
- D) SAN is storage.

---

### Q7 (Medium)
A cloud uses 16 million isolated virtual networks for its customers. What technology?
A) VLAN
B) VXLAN
C) GENEVE
D) GRE

**Answer: B) VXLAN**
**Explanation:** VXLAN uses 24-bit VNI = 16M networks. VLAN only supports 4,094.
**Why others are wrong:**
- A) VLAN only 4K.
- C) GENEVE is overlay but less common for this.
- D) GRE is tunneling but not specific to this scale.

---

### Q8 (Hard)
A VMware cluster is reporting HA failures. VMs aren't restarting on surviving hosts. What's the FIRST thing to check?
A) vCenter license.
B) Cluster resource capacity (is there enough CPU/RAM to restart VMs?).
C) VM tools version.
D) VM operating system.

**Answer: B) Cluster resource capacity (is there enough CPU/RAM to restart VMs?).**
**Explanation:** HA requires sufficient cluster capacity. If the cluster is full, HA can't restart VMs. Check admission control and capacity.
**Why others are wrong:**
- A) License affects features but not usually HA function.
- C) VM tools is for guest operations, not HA.
- D) OS is rarely the cause.

---

### Q9 (Hard)
What's the difference between SR-IOV and PCI passthrough?
A) They are the same.
B) PCI passthrough is one device to one VM; SR-IOV is one device to many VMs (via VFs).
C) SR-IOV is for GPUs only.
D) PCI passthrough is for networking.

**Answer: B) PCI passthrough is one device to one VM; SR-IOV is one device to many VMs (via VFs).**
**Explanation:** PCI passthrough dedicates a device to one VM. SR-IOV partitions a device into multiple VFs, each to a VM.
**Why others are wrong:**
- A) Different.
- C) SR-IOV is for any device (NIC, storage, GPU).
- D) PCI passthrough is for any device, not just networking.

---

### Q10 (Hard)
A company needs high-performance block storage accessible from all hosts in a VMware cluster. They also need vMotion to work. What storage?
A) Local disks on each host.
B) SAN (FC or iSCSI).
C) Object storage.
D) NAS with public access.

**Answer: B) SAN (FC or iSCSI)**
**Explanation:** SAN provides shared block storage across all hosts. vMotion requires shared storage.
**Why others are wrong:**
- A) Local disks are not shared — vMotion fails.
- C) Object storage is for S3-style, not VM disks.
- D) NAS is file storage (NFS), not block. (NFS can be used for VMFS datastores, but the question is about block specifically.)

---

### Q11 (Hard)
What's the difference between a vSphere standard vSwitch and a Distributed vSwitch (VDS)?
A) Standard is per-host; VDS is cluster-wide with consistent config.
B) VDS is older.
C) Standard is faster.
D) They are the same.

**Answer: A) Standard is per-host; VDS is cluster-wide with consistent config.**
**Explanation:** Standard vSwitch is configured per host. VDS is configured once and applies to all hosts in the cluster.
**Why others are wrong:**
- B) VDS is newer.
- C) Same performance.
- D) Different.

---

### Q12 (Medium)
What's the purpose of a VM template?
A) To run multiple VMs.
B) To serve as a master image for deploying new VMs.
C) To back up VMs.
D) To monitor VMs.

**Answer: B) To serve as a master image for deploying new VMs.**
**Explanation:** Templates are read-only master images. New VMs are cloned from them.
**Why others are wrong:**
- A) Templates aren't powered on.
- C) Backups are different.
- D) Monitoring uses different tools.

---

### Q13 (Hard)
A team is using vSphere. They want their database VMs to be on separate hosts from their app VMs. What rule?
A) Affinity between DB and app.
B) Anti-affinity between DB and app.
C) Affinity between DB replicas.
D) No rule needed.

**Answer: B) Anti-affinity between DB and app.**
**Explanation:** Keep DB and app on different hosts to avoid resource contention and for better HA.
**Why others are wrong:**
- A) Affinity would put them together.
- C) DB replicas should be anti-affinity, not affinity.
- D) Rules are needed for this.

---

### Q14 (Hard)
A company has 100 VMware VMs. They want to migrate to AWS. They want to keep the existing OS and applications (lift-and-shift). What AWS services?
A) EC2 + EBS + AMIs
B) Lambda + DynamoDB
C) S3 + CloudFront
D) ECS + Fargate

**Answer: A) EC2 + EBS + AMIs**
**Explanation:** Lift-and-shift = VMs to EC2. EBS for block storage. AMIs as the templates.
**Why others are wrong:**
- B) Lambda is serverless, no VMs.
- C) S3 is object storage, not VMs.
- D) ECS/Fargate is containers, not VM lift-and-shift.

---

### Q15 (Hard)
A company wants to use their existing VMware licenses in AWS. What AWS service?
A) AWS Outposts
B) VMware Cloud on AWS
C) EC2 Dedicated Hosts
D) AWS Snowball

**Answer: B) VMware Cloud on AWS**
**Explanation:** VMware Cloud on AWS runs VMware vSphere in AWS — use your existing VMware skills and tools.
**Why others are wrong:**
- A) Outposts is AWS hardware in your DC, not VMware in AWS.
- C) Dedicated Hosts are for licensing (BYOL) but not VMware.
- D) Snowball is for data transfer.

---

### Q16 (Medium)
What's the difference between a Type 1 and Type 2 hypervisor?
A) Type 1 runs on a host OS; Type 2 runs on bare metal.
B) Type 1 runs on bare metal; Type 2 runs on a host OS.
C) They are the same.
D) Type 2 is for production.

**Answer: B) Type 1 runs on bare metal; Type 2 runs on a host OS.**
**Explanation:** Type 1 (ESXi, Hyper-V, KVM) runs directly on hardware — production. Type 2 (VirtualBox, Workstation) runs on a host OS — dev/test.
**Why others are wrong:**
- A) Reversed.
- C) Different.
- D) Type 1 is for production.

---

### Q17 (Hard)
A cloud provider uses an overlay network to isolate 10,000 customer networks on shared infrastructure. What protocol is most likely?
A) VLAN
B) VXLAN
C) MPLS
D) OSPF

**Answer: B) VXLAN**
**Explanation:** VXLAN supports 16M networks (24-bit VNI), far beyond VLAN's 4,094. Used by AWS, Azure, GCP.
**Why others are wrong:**
- A) VLAN only 4K.
- C) MPLS is for service providers.
- D) OSPF is a routing protocol, not overlay.

---

### Q18 (Hard)
A team needs sub-millisecond latency for VM-to-VM communication. They want VMs on the same physical rack. What AWS feature?
A) Spread Placement Group
B) Cluster Placement Group
C) Partition Placement Group
D) Auto Scaling Group

**Answer: B) Cluster Placement Group**
**Explanation:** Cluster Placement Group places instances close together (same rack, same AZ) for low-latency HPC workloads.
**Why others are wrong:**
- A) Spread is for HA, not low latency.
- C) Partition is for large distributed systems, not low latency.
- D) ASG is for scaling, not placement.

---

### Q19 (Hard)
A company wants to ensure no two critical VMs run on the same physical host. What cloud feature?
A) Affinity
B) Anti-affinity (e.g., AWS Spread Placement Group, GCP instance affinity)
C) Cluster Placement Group
D) Auto Scaling

**Answer: B) Anti-affinity (e.g., AWS Spread Placement Group, GCP instance affinity)**
**Explanation:** Anti-affinity rules or spread placement groups enforce that VMs run on different hosts.
**Why others are wrong:**
- A) Affinity puts them together.
- C) Cluster puts them together.
- D) ASG is scaling, not placement.

---

### Q20 (Hard)
What's the difference between SAN and vSAN?
A) SAN is for files; vSAN is for blocks.
B) SAN is external storage; vSAN aggregates local disks of hosts.
C) They are the same.
D) vSAN is older.

**Answer: B) SAN is external storage; vSAN aggregates local disks of hosts.**
**Explanation:** Traditional SAN is a separate storage array (NetApp, Pure). vSAN is hyperconverged — local disks of hosts pooled into a shared datastore.
**Why others are wrong:**
- A) SAN is block, not file.
- C) Different.
- D) vSAN is newer (2014).

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Stand-alone vs clustered VMs | 🔴 **CRITICAL** | Foundation. |
| vSphere HA / DRS / vMotion | 🔴 **CRITICAL** | Direct questions. |
| Full vs linked clone | 🔴 **CRITICAL** | Direct comparison. |
| Affinity vs anti-affinity | 🔴 **CRITICAL** | HA scenarios. |
| VM templates | 🟠 **HIGH** | Deployment scenarios. |
| Local vs SAN storage | 🟠 **HIGH** | Direct comparison. |
| SAN vs NAS | 🟠 **HIGH** | Direct comparison. |
| VXLAN vs VLAN | 🟠 **HIGH** | Networking. |
| Hardware pass-through types | 🟠 **HIGH** | Performance scenarios. |
| Cloud equivalents (AMI, EC2, etc.) | 🟠 **HIGH** | Cloud-specific. |
| Placement Groups (AWS) | 🟠 **HIGH** | AWS-specific. |
| Hypervisor types (Type 1 vs 2) | 🟡 **MEDIUM** | Foundational. |
| vSAN (HCI) | 🟡 **MEDIUM** | Niche. |
| VMware Cloud on AWS | 🟡 **MEDIUM** | Migration scenarios. |
| Live migration requirements | 🟡 **MEDIUM** | Operational. |
| SR-IOV specifics | 🟡 **MEDIUM** | Performance. |
| GENEVE overlay | 🟢 **LOW** | Advanced. |
| Specific vendor products (NetApp, etc.) | 🟢 **LOW** | Recognition only. |
| VMFS details | 🟢 **LOW** | Deep dive. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-Page Cram Sheet)

### 📋 Cram Sheet: 1.7 Virtualization Concepts

**Stand-Alone vs Clustered:**

| | Stand-Alone | Clustered |
|---|---|---|
| **Hosts** | 1 | 2+ |
| **HA** | No | Yes |
| **Live migration** | No | Yes (with shared storage) |
| **Use case** | Dev/test | Production |

**Cluster Features (VMware):**
- **HA** — restart VMs on host failure.
- **DRS** — auto-balance VMs.
- **vMotion** — live migration.
- **FT** — lockstep VM (zero downtime).

**Cloning:**

| | Full Clone | Linked Clone |
|---|---|---|
| **Independence** | Independent | Depends on parent |
| **Storage** | Full copy | Delta only |
| **Speed to create** | Slower | Fast |
| **Use case** | Production | VDI, dev/test |

**Host Affinity:**
- **Affinity (Keep Together):** Low-latency, related workloads.
- **Anti-Affinity (Keep Apart):** HA, fault tolerance.
- **Host Affinity:** Pin to specific host (GPU, licensing).

**Hardware Pass-Through:**

| Technology | Sharing | Use Case |
|---|---|---|
| **PCI Passthrough** | No (1 device, 1 VM) | GPU, NVMe |
| **SR-IOV** | Yes (VFs) | High-perf networking |
| **vGPU** | Yes | VDI, AI |
| **USB Passthrough** | No | License dongles |

**Networks:**

| Type | Description | Use Case |
|---|---|---|
| **vSwitch** | Per-host virtual switch | Simple setups |
| **VDS (Distributed)** | Cluster-wide vSwitch | Consistent config |
| **Overlay (VXLAN)** | Virtual network on physical | Multi-tenant cloud |
| **VLAN** | L2 segmentation (4K networks) | On-prem |

**VXLAN vs VLAN:**
- VLAN: 4,094 networks (12-bit).
- VXLAN: 16M networks (24-bit). Used by cloud.

**Storage:**

| | Local | SAN | NAS |
|---|---|---|---|
| **Type** | Direct attached | Block (FC, iSCSI) | File (NFS, SMB) |
| **Scope** | Single host | Multiple hosts | Multiple hosts |
| **Performance** | Highest | High | Moderate |
| **HA** | No | Yes | Yes |
| **Use case** | Ephemeral, high-IOPS | DB, VMs | File shares |

**Cloud Storage Mapping:**
- **Local** → Instance Store, Local SSD, Temporary Disk.
- **SAN-like** → EBS, Azure Disk, Persistent Disk.
- **NAS-like** → EFS, Azure Files, Filestore.

**Cloud Cluster Equivalents:**
- **AWS:** EC2 + ASG + ALB + Placement Groups.
- **Azure:** VMSS + Availability Zones + Proximity Placement Groups.
- **GCP:** Managed Instance Groups + Regional + Affinity Labels.

**Acronyms to Know Cold:**

- **IaaS / PaaS / SaaS / FaaS** = (1.1)
- **RTO / RPO** = (1.2)
- **VPC / TGW / NACL / SG / WAF** = (1.3)
- **IOPS / SSD / HDD** = (1.4)
- **MS / EDA / Fan-Out** = (1.5)
- **K8s / Pod / PV / PVC** = (1.6)
- **HA** = High Availability
- **DRS** = Distributed Resource Scheduler
- **vMotion** = VM live migration
- **FT** = Fault Tolerance
- **VDS** = vSphere Distributed Switch
- **OVS** = Open vSwitch
- **VXLAN / GENEVE** = Overlay protocols
- **VTEP** = VXLAN Tunnel Endpoint
- **VNI** = VXLAN Network Identifier
- **SAN / NAS / DAS** = Storage types
- **FC / iSCSI** = SAN protocols
- **NFS / SMB** = NAS protocols
- **SR-IOV** = Single Root I/O Virtualization
- **vGPU** = Virtual GPU
- **IOMMU / VT-d** = CPU features for PCI passthrough
- **HCI** = Hyperconverged Infrastructure
- **vSAN** = VMware's HCI storage
- **VMFS** = VMware File System
- **AMI** = Amazon Machine Image (AWS template)

**Top Exam Triggers:**

- "Single host, dev/test" → **Stand-alone VM**
- "Mission-critical / HA required" → **Cluster**
- "Replicas on different hosts" → **Anti-affinity**
- "Low-latency same-host" → **Affinity**
- "Linked clone for production" → **Wrong** (use full clone)
- "GPU for single VM" → **PCI passthrough**
- "GPU for many VMs" → **vGPU**
- "16M isolated networks" → **VXLAN**
- "4K networks" → **VLAN**
- "Shared block storage for vMotion" → **SAN**
- "Local fast storage" → **NVMe / SSD**
- "Multiple hosts share files" → **NAS (NFS/SMB)**
- "Block-level shared storage" → **SAN (FC/iSCSI)**

---

## SECTION 14 — LATEST INDUSTRY UPDATES (CV0-004 Relevant)

### VMware Updates (2024–2025)
- **VMware by Broadcom (2023 acquisition):** Major changes — vSphere is now sold as bundles (VCF — VMware Cloud Foundation). Pricing model changed.
- **vSphere 8.0** (2023): DPU support (VMware on SmartNIC), Kubernetes via vSphere with Tanzu.
- **vSAN 8.0:** Express Storage Architecture (ESA) — high-performance all-flash HCI.
- **NSX 4.0:** New licensing, improved multi-cloud networking.
- **VMware Cloud on AWS:** Continued expansion (more regions, new instance types).
- **VCF (VMware Cloud Foundation):** The new bundle (vSphere + vSAN + NSX + Aria).

### AWS Updates (2024–2025)
- **AWS Nitro System** continues to be the backbone of EC2 — custom hardware + hypervisor for performance and security.
- **EC2 Capacity Blocks for ML** — Reserve GPU instances for short ML workloads.
- **EC2 Instance Types:** New families (M7i, R8g, C7i) with Graviton4 (ARM) and Intel Sapphire Rapids.
- **Bare Metal Instances** continue for full hardware access (`.metal` size).
- **EBS** continues to evolve — io2 Block Express, gp3, snapshot improvements.
- **AWS Outposts** — AWS-managed hardware in your DC for hybrid.

### Azure Updates (2024–2025)
- **Azure Boost** — Custom hardware (DPU-like) for storage and networking offload.
- **Azure VMs:** New series (Dv6, Ev6) with latest Intel/AMD.
- **Confidential VMs** with AMD SEV-SNP — encrypted memory.
- **Azure Dedicated Hosts** — for licensing (BYOL).
- **Azure VMware Solution** — VMware in Azure (similar to VMware Cloud on AWS).
- **Ultra Disk** — Sub-ms latency, 160K IOPS.

### Google Cloud Updates (2024–2025)
- **GCP Compute Engine** runs on KVM (custom, optimized for Google).
- **Confidential VMs** with AMD SEV.
- **Bare Metal Instances** for full hardware access.
- **Spot VMs** (replaced Preemptible) for cheap, interruptible workloads.
- **Sole-Tenant Nodes** for licensing.

### Hyperconverged Infrastructure (HCI)
- **Nutanix** continues to grow as VMware alternative post-Broadcom acquisition.
- **VMware vSAN** (now part of VCF) — leader in HCI.
- **Microsoft Azure Local (Azure Stack HCI)** — HCI for hybrid cloud.
- **Scale Computing** — SMB-friendly HCI.
- **Open Source:** Ceph, GlusterFS, MinIO for software-defined storage.

### Container vs VM
- **K8s on VMs** is the standard pattern (K8s runs as VMs, pods run inside).
- **KubeVirt** (CNCF) — Run VMs in K8s. Unifies VM and container management.
- **Firecracker** (AWS) — MicroVMs for serverless (Lambda, Fargate).
- **Kata Containers** — Lightweight VMs that run like containers.

### Hardware Pass-Through Trends
- **DPU (Data Processing Unit):** Offload networking, storage, security from CPU. AWS Nitro, Azure Boost, NVIDIA BlueField.
- **GPU:** H100, H200, B100/B200 (Blackwell). MIG for hardware partitioning.
- **vGPU:** NVIDIA vGPU, AMD MxGPU, Intel GVT-g.
- **SR-IOV:** Standard for NFV, high-perf networking.

### Overlay Networking
- **VXLAN** remains the dominant overlay in cloud.
- **GENEVE** is gaining in modern data centers (NSX, Cilium).
- **EVPN + VXLAN** is the standard for fabric networks.
- **eBPF** is the next generation — Cilium, Tetragon, Pixie for cloud-native networking.

### Industry Trends
- **VMware Broadcom fallout** — Many customers exploring alternatives (Nutanix, OpenStack, KVM).
- **Hybrid cloud** continues to grow (AWS Outposts, Azure Stack, Google Distributed Cloud).
- **AI workloads** drive demand for GPU pass-through and bare metal.
- **Confidential computing** — Encrypted VMs (Azure Confidential, AWS Nitro Enclaves, GCP Confidential).
- **Software-defined everything** — networking, storage, security all in software.

---

## ✅ END OF OBJECTIVE 1.7 NOTES

**Coverage Summary:**
- ✅ Stand-alone VMs
- ✅ Clustering (HA, DRS, vMotion)
- ✅ Cloning (full vs linked, templates)
- ✅ Host affinity (affinity vs anti-affinity)
- ✅ Hardware pass-through (PCI, SR-IOV, vGPU)
- ✅ Network types (vSwitch, VDS, overlay, VXLAN)
- ✅ Storage (local, SAN, NAS)

**Next step:** Ping me for **1.8 — Cost Considerations** (billing models — dedicated host, reserved, pay-as-you-go, spot; resource metering, tagging, rightsizing) — practical exam topic that tests FinOps fundamentals.

Study tip from Cikgu Danial: *"For 1.7, the key distinction is VMs vs containers (1.6). VMs = full OS, heavier, more isolation. Containers = shared OS, lighter, faster. Cluster vs stand-alone is the second key: clustered VMs have HA + live migration; stand-alone is single point of failure. If you can sketch a VMware cluster with HA, anti-affinity, and shared SAN storage, you can answer most 1.7 questions."* 💪
