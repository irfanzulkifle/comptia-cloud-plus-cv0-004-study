# Objective 2.1 — Compare and contrast cloud deployment models

> **Domain 2.0 — Deployment (19% of exam)** · Exam CV0-004 V4 · Objectives doc v5.0
> **Status:** Exam-ready · **Version:** 2.0 (enhanced) · Supersedes `full notes/Objective-2.1-Cloud-Deployment-Models.md`

---

## 0. How to use this note

| Pass | What to read | Time |
|---|---|---|
| **1st (learn)** | Sections 1 → 8 in order | ~50 min |
| **2nd (drill)** | Section 2.2 (the three questions) + 2.4 (deployment ≠ service model) + 7.2 (hybrid vs multicloud) | ~15 min |
| **3rd (test)** | Section 11 (practice) + Section 12 (PBQ drills) | ~25 min |
| **Exam eve** | Section 13 (60-second recall sheet) only | ~4 min |

> 📌 **This is the first objective of Domain 2 (Deployment, 19%).** It is short and high-scoring, but it contains three of the exam's favourite traps: **hybrid vs multicloud**, **on-premises vs private cloud**, and **"VPC" meaning a network inside the public cloud, not a private cloud**.

---

## 1. Official objective coverage

> **2.1 Compare and contrast cloud deployment models.**
> - Public
> - **Private**
>   - On premises
> - Hybrid
> - Community

### 1.1 What the verb tells you

**"Compare and contrast"** — the same verb as 1.4, 1.6, 1.7, and 1.10. Questions are built from the contrast dimensions:

**tenancy · location · ownership · cost model · control · scalability · compliance**

Learn the comparison tables (Section 8) as primary material.

### 1.2 Coverage checklist

- [ ] I can define all four models by **who shares the infrastructure**
- [ ] I know **deployment model ≠ service model** — they are independent axes
- [ ] I know a **private cloud need not be on premises**
- [ ] I know a traditional on-prem data centre is **not automatically a private cloud**
- [ ] I know **"VPC" is a network construct inside public cloud**, not a private cloud
- [ ] I can distinguish **hybrid** from **multicloud** — and say when it is both
- [ ] I know hybrid requires **integration**, not just owning two things
- [ ] I can identify a **community cloud** from its defining feature
- [ ] I can map each model to **CapEx vs OpEx**
- [ ] I know the **five essential characteristics** that make something a cloud at all

---

## 2. The core mental model

### 2.1 The four models, visually

```text
   PUBLIC                              PRIVATE
   ┌──────────────────────────┐        ┌──────────────────────────┐
   │  PROVIDER'S DATA CENTRE  │        │  DEDICATED TO ONE ORG    │
   │  ┌────┐┌────┐┌────┐┌────┐│        │  ┌──────────────────────┐│
   │  │ Co ││ Co ││ Co ││ Co ││        │  │                      ││
   │  │ A  ││ B  ││ C  ││ D  ││        │  │     ONE COMPANY      ││
   │  └────┘└────┘└────┘└────┘│        │  │                      ││
   │  MULTI-TENANT — anyone   │        │  └──────────────────────┘│
   │  can buy                 │        │  SINGLE-TENANT           │
   └──────────────────────────┘        │  on-prem OR hosted       │
                                       └──────────────────────────┘

   COMMUNITY                           HYBRID
   ┌──────────────────────────┐        ┌───────────┐    ┌───────────┐
   │  SHARED BY A DEFINED     │        │  PRIVATE  │◄══►│  PUBLIC   │
   │  GROUP with a COMMON     │        │  or       │    │           │
   │  CONCERN                 │        │  ON-PREM  │    │           │
   │  ┌────┐┌────┐┌────┐      │        └───────────┘    └───────────┘
   │  │Hosp││Hosp││Hosp│      │           ▲ INTEGRATED — data and
   │  │ A  ││ B  ││ C  │      │             workloads move between
   │  └────┘└────┘└────┘      │             them as ONE environment
   │  same regulator, mission │
   │  or compliance regime    │        ⚠ Two disconnected environments
   └──────────────────────────┘          are NOT hybrid
```

### 2.2 ★ The three questions that identify any model

```text
   ① WHO SHARES THE INFRASTRUCTURE?
        Anyone who pays          → PUBLIC
        Only my organisation     → PRIVATE
        A defined GROUP with a
        shared concern           → COMMUNITY
        A mix, integrated        → HYBRID

   ② WHERE DOES IT PHYSICALLY LIVE?
        Provider's data centre · your data centre · a hosting partner
        ⚠ Location does NOT determine the model — TENANCY does.

   ③ ARE TWO OR MORE ENVIRONMENTS BOUND TOGETHER?
        Yes, private + public working as one → HYBRID
        Yes, two or more PUBLIC providers    → MULTICLOUD (not a NIST
                                                deployment model)
```

### 2.3 What makes something a "cloud" at all

CompTIA's four models come from **NIST SP 800-145**, which also defines **five essential characteristics**. All five must be present, in *any* deployment model:

| Characteristic | Meaning |
|---|---|
| **On-demand self-service** | Users provision resources themselves, without a human ticket |
| **Broad network access** | Available over the network from standard devices |
| **Resource pooling** | Resources are pooled and dynamically assigned across consumers |
| **Rapid elasticity** | Capacity scales out and in quickly, appearing unlimited |
| **Measured service** | Usage is metered, monitored, and reported (see 1.8) |

> ★ **The nuance this creates:** a rack of virtualised servers you provision by raising a ticket is **virtualization, not a private cloud**. A private cloud requires a self-service portal, pooling, elasticity, and metering/chargeback. Expect a question that hinges on this.

### 2.4 ★ Deployment model ≠ service model

```text
                    SERVICE MODEL (1.1) — WHAT you rent
                 IaaS         PaaS         SaaS         FaaS
              ┌───────────┬───────────┬───────────┬───────────┐
   PUBLIC     │  EC2-like │ managed   │ M365,     │ Lambda-   │
              │  VMs      │ app plat. │ Salesforce│ like      │
              ├───────────┼───────────┼───────────┼───────────┤
   PRIVATE    │ VMware /  │ OpenShift │ internal  │ Knative   │
   D          │ OpenStack │ on-prem   │ portal    │ on-prem   │
   E          ├───────────┼───────────┼───────────┼───────────┤
   P  HYBRID  │ workloads split across both, integrated       │
   L          ├───────────┼───────────┼───────────┼───────────┤
   O COMMUNITY│ shared sector platform, any service model     │
   Y          └───────────┴───────────┴───────────┴───────────┘
   M
   E   DEPLOYMENT MODEL — WHO shares it and WHERE it runs
   N
   T   ★ These are INDEPENDENT AXES. A private PaaS is entirely normal.
       Any cell in this grid is a valid architecture.
```

> ⚠️ **Do not confuse 1.1 with 2.1.** 1.1 = *what you rent* (IaaS/PaaS/SaaS/FaaS). 2.1 = *who shares it and where* (public/private/hybrid/community). The exam deliberately mixes them in distractors.

---

## 3. Public cloud

| | |
|---|---|
| **Definition** | Infrastructure owned and operated by a **third-party provider**, made available to **the general public** over the network. **Multi-tenant** — many unrelated customers share the same physical infrastructure, isolated logically. |
| **Ownership** | The provider owns, operates, and maintains everything |
| **Location** | The provider's data centres (off premises) |
| **Cost model** | **OpEx**, pay-as-you-go, no capital outlay (see 1.8) |
| **Strengths** | **No upfront cost**; near-unlimited, instant elasticity; global reach in minutes; provider-grade availability and security investment; the fastest route to market; the broadest service catalogue |
| **★ Limitations** | **Least control**; **multi-tenancy** (noisy-neighbour and shared-blast-radius concerns); data resides with the provider, raising **residency and sovereignty** questions; **egress costs** and **vendor lock-in**; you inherit the provider's outages and maintenance |
| **Best for** | Startups and new workloads, variable/unpredictable demand, public-facing web and mobile, dev/test, global distribution, disaster recovery targets |
| **Exam triggers** | "no capital budget", "must scale quickly", "unpredictable traffic", "global users", "pay only for what we use", "no hardware to manage", "fastest time to market" |

---

## 4. Private cloud

| | |
|---|---|
| **Definition** | Cloud infrastructure provisioned for the **exclusive use of a single organisation**. **Single-tenant** — the hardware is not shared with anyone else. |
| **★ Ownership and location** | Per NIST, a private cloud "may be owned, managed, and operated by the organisation, a third party, or some combination, and **it may exist on or off premises**." Exclusivity defines it — **not** location |
| **Cost model** | **CapEx-heavy** (hardware, facility, licences) plus ongoing operational cost and staff; a hosted private cloud shifts some of this to OpEx |
| **Strengths** | **Maximum control** over hardware, hypervisor, network, and data placement; **strongest isolation** — no other tenant on the hardware; predictable performance (no noisy neighbour); easiest path for strict **compliance, sovereignty, and audit**; can run legacy workloads unchanged |
| **★ Limitations** | **High upfront cost**; **capacity is finite** — you must buy for peak, and elasticity ends at the hardware you own; you own **all** operational burden (patching, HA, DR, refresh cycles); slower to provision; requires in-house expertise |
| **Best for** | Regulated industries (banking, healthcare, government), data-sovereignty requirements, workloads with predictable steady load, organisations with existing data-centre investment |
| **Exam triggers** | "data must never leave our facility", "regulatory requirement for single tenancy", "full control of the hardware", "sensitive/classified data", "no other customer may share the infrastructure" |

### 4.1 On premises — CompTIA's sub-bullet

**"On premises"** describes the *location* variant of a private cloud: the organisation owns the building and the hardware.

| | **On-premises private cloud** | **Hosted / managed private cloud** |
|---|---|---|
| Hardware location | **Your facility** | Provider's or partner's facility |
| Hardware ownership | You | Provider (dedicated to you) |
| Tenancy | Single | **Single** — still private |
| Cost | **CapEx** | More OpEx |
| Physical control | **Total** | Contractual |
| Still a private cloud? | ✅ Yes | ✅ **Yes** |

> ⚠️ **Two on-premises traps:**
> **① On-premises ≠ private cloud automatically.** A traditional data centre with manually provisioned VMs lacks self-service, pooling, elasticity, and metering — that is **virtualization**, not cloud.
> **② Private cloud ≠ on-premises automatically.** A dedicated, single-tenant environment hosted by a provider is still a private cloud.

---

## 5. Hybrid cloud

| | |
|---|---|
| **Definition** | A composition of **two or more distinct cloud infrastructures** (typically **private/on-premises + public**) that remain separate entities but are **bound together by technology enabling data and application portability**. |
| **★ The defining requirement** | **Integration.** Merely owning a data centre *and* a public cloud account is not hybrid — the environments must be connected and managed so workloads and data can move between them |
| **What binds them** | **Network connectivity** — VPN or dedicated connection (see 1.3) · **federated identity** — one set of credentials across both · **consistent tooling** — the same IaC, monitoring, and policy · **data replication and portability** · often a **hybrid platform** extending provider services on-premises |
| **Strengths** | Keep regulated data private while using public elasticity; **cloud bursting** for peaks (see 1.2); a **staged migration** path rather than a big bang; public cloud as an affordable **DR target**; preserves existing data-centre investment |
| **★ Limitations** | **The most complex model to operate** — two control planes, two security models, two skill sets; **latency between environments**; **data gravity** — moving large datasets is slow and costly; **egress charges**; consistent security policy across both is genuinely hard |
| **Best for** | Regulated organisations needing elasticity, gradual cloud migration, cloud bursting, DR to public cloud, workloads with mixed sensitivity |
| **Exam triggers** | "keep sensitive data on premises but scale in the cloud", "burst during peaks", "gradual migration", "use the cloud for DR", "connect our data centre to the cloud" |

```text
   HYBRID — the environments are BOUND TOGETHER

   ┌─────────────────────────────┐         ┌─────────────────────────────┐
   │  PRIVATE / ON-PREMISES      │         │  PUBLIC CLOUD               │
   │                             │         │                             │
   │  [Regulated database]       │  VPN /  │  [Web tier — bursts on peak]│
   │  [Legacy ERP]               │◄═══════►│  [Dev/test environments]    │
   │  [Sensitive records]        │Dedicated│  [Backup / DR target]       │
   │                             │ circuit │  [Analytics on extracts]    │
   └─────────────────────────────┘         └─────────────────────────────┘
            │                                          │
            └──── FEDERATED IDENTITY (one login) ──────┘
            └──── CONSISTENT TOOLING & POLICY ─────────┘
            └──── DATA REPLICATION / PORTABILITY ──────┘

   ⚠ Remove the connection and the shared management, and you no
     longer have a hybrid cloud — you have two separate estates.
```

---

## 6. Community cloud

| | |
|---|---|
| **Definition** | Infrastructure provisioned for **exclusive use by a specific community of consumers from organisations that have shared concerns** — mission, security requirements, policy, or compliance regime. |
| **★ Defining feature** | Shared by **several organisations**, but **not by the general public**, and they share a **common concern**. It sits between public (anyone) and private (one organisation) |
| **Ownership** | Jointly owned and governed by the member organisations, or operated by a third party on their behalf; on or off premises |
| **Cost model** | **Shared cost** — members split the investment, making compliant infrastructure affordable that none could justify alone |
| **Strengths** | Cost sharing; **compliance built for the sector's specific regime**; enables data exchange between members; stronger governance than public for the shared concern |
| **Limitations** | Requires **multi-party governance** (slow decisions, competing priorities); **less elastic** than public; the **least common** model in practice; exit is complicated by joint ownership |
| **Typical examples** | Government/public-sector clouds, healthcare consortia sharing patient-data infrastructure, financial-services clouds meeting a shared regulator's rules, research and education networks, defence community clouds |
| **Exam triggers** | "several hospitals/agencies/universities", "same regulatory requirements", "share the cost of compliant infrastructure", "specific group of organisations with a common mission", "not open to the general public" |

---

## 7. Adjacent terms

### 7.1 Multicloud

| | |
|---|---|
| **Definition** | Using **two or more public cloud providers** (e.g. AWS *and* Azure) for one organisation's workloads. |
| **Not a NIST deployment model** | It is a *strategy*, not one of the four. But it is examined alongside them and is a constant distractor |
| **Drivers** | Avoid vendor lock-in and retain negotiating leverage · resilience against a whole-provider outage · best-of-breed service selection · regulatory diversity requirements · inherited from mergers |
| **Costs** | Duplicated tooling and expertise · **inter-cloud egress fees** · no unified billing · the lowest-common-denominator effect (avoiding each provider's best proprietary services to stay portable) · wider attack surface |

### 7.2 ★ Hybrid vs multicloud vs both

```text
   HYBRID                    MULTICLOUD                BOTH
   ┌────────┐ ┌────────┐     ┌────────┐ ┌────────┐    ┌────────┐
   │ON-PREM │◄►│ PUBLIC │     │ AWS    │ │ AZURE  │    │ON-PREM │
   │/PRIVATE│ │  (one) │     │        │ │        │    └───┬────┘
   └────────┘ └────────┘     └────────┘ └────────┘        │
                                                      ┌───┴────┐
   Includes something        Two or more PUBLIC       │        │
   YOU own/control           providers            ┌───▼──┐ ┌───▼──┐
                                                  │ AWS  │ │AZURE │
   ★ "Includes your own      ★ "Multiple vendors"  └──────┘ └──────┘
     infrastructure"                               Hybrid AND multicloud
```

| | **Hybrid** | **Multicloud** |
|---|---|---|
| Composition | **Private/on-prem + public** | **Two or more public providers** |
| Includes infrastructure you own | ✅ **Yes** | ❌ Not necessarily |
| Primary driver | Compliance + elasticity, migration path | **Avoid lock-in**, resilience, best-of-breed |
| Requires integration | ✅ Yes, by definition | Not necessarily |
| Enables cloud bursting | ✅ **Yes** | ❌ Bursting requires a private side |
| A NIST deployment model | ✅ Yes | ❌ No — a strategy |

> ⚠️ **The most-repeated trap in this objective:** *"We use AWS and Azure — that's hybrid."* **No.** That is **multicloud**. Hybrid requires something you own or exclusively control.

### 7.3 Other terms worth recognising

| Term | Meaning |
|---|---|
| **Sovereign cloud** | A public or community cloud operated within a jurisdiction under local law and staffing, for data-residency and government requirements |
| **Virtual private cloud (VPC)** | ⚠️ **A logically isolated network inside a PUBLIC cloud** (see 1.3) — a networking construct, **not** a private cloud deployment model |
| **Dedicated host / bare metal** | Single-tenant *hardware* within a public cloud (see 1.8) — still public cloud deployment |
| **Distributed cloud / edge** | Provider services run at edge locations or in your data centre while managed from the public cloud control plane |
| **Cloud bursting** | Overflowing from private to public at peak — **requires hybrid** (see 1.2) |
| **Poly cloud** | Multicloud where each provider is chosen for its strongest service |

> ★ **The VPC trap in one line:** an "Amazon Virtual Private Cloud" is **public cloud**. The word "private" refers to network isolation, not tenancy of the underlying hardware.

---

## 8. Comparison tables

### 8.1 ★ The four models at a glance

| Dimension | **Public** | **Private** | **Hybrid** | **Community** |
|---|---|---|---|---|
| **Tenancy** | **Multi-tenant** — general public | **Single-tenant** — one org | Mixed | **Multi-tenant** — defined group |
| Who may use it | Anyone who pays | One organisation | The owning organisation | Members of the community |
| Location | Provider's DC | On premises **or** hosted | Both | On or off premises |
| Ownership | Provider | Organisation or third party | Both | Members jointly, or a third party |
| **Cost model** | **OpEx**, pay-as-you-go | **CapEx**-heavy | Mixed | **Shared** between members |
| Upfront cost | **None** | **High** | Medium | Shared/medium |
| **Elasticity** | **Near-unlimited** | Limited by owned hardware | High (burst to public) | Medium |
| **Control** | **Lowest** | **Highest** | High over the private part | Shared governance |
| Isolation | Logical | **Physical — strongest** | Strong for the private part | Within the group |
| Ops burden | **Lowest** | **Highest** | **Highest (two estates)** | Shared |
| Compliance/sovereignty | Hardest | **Easiest** | Mixed | **Good for a shared regime** |
| Complexity | Low | Medium | **Highest** | Medium |
| Best for | Startups, variable demand, global reach | Regulated, sensitive, steady load | Regulated **plus** elasticity, migration | A sector with common rules |

### 8.2 Scenario clue → model

| Clue | Model |
|---|---|
| "No capital budget, need to launch next month" | **Public** |
| "Traffic is unpredictable and global" | **Public** |
| "Data must never leave our own facility" | **Private (on premises)** |
| "Regulator requires single-tenant hardware" | **Private** |
| "Sensitive data stays put, but we need to scale for peaks" | **Hybrid** |
| "Burst capacity during the sale, release it after" | **Hybrid** (bursting needs a private side) |
| "Migrate gradually over two years" | **Hybrid** |
| "Use the cloud as a DR site for our data centre" | **Hybrid** |
| "Five hospitals under the same health regulator sharing a platform" | **Community** |
| "Government agencies jointly funding compliant infrastructure" | **Community** |
| "We use AWS and Azure to avoid lock-in" | **Multicloud** (not hybrid) |
| "Our data centre plus AWS plus Azure" | **Hybrid AND multicloud** |
| "Dedicated hardware inside a public provider" | Still **public** (dedicated host) |
| "Isolated network segment in AWS" | Still **public** (a VPC is networking) |

### 8.3 Shared responsibility by deployment model

| | **Public** | **Private (on-prem)** | **Hybrid** | **Community** |
|---|---|---|---|---|
| Physical security | Provider | **You** | Split by location | Community/operator |
| Hardware & hypervisor | Provider | **You** | Split | Community/operator |
| Guest OS / apps / data | **You** (per service model — see 1.1) | **You** | **You** | Members |
| Who is "the provider"? | The CSP | **Your own IT department** | Both | The community or its operator |

> 💡 **In a private cloud you are both provider and customer** — the shared responsibility model from 1.1 still applies, but both halves are yours.

### 8.4 Cost profile

| | **Public** | **Private** | **Hybrid** | **Community** |
|---|---|---|---|---|
| Capital outlay | None | **High** | Medium | Shared |
| Ongoing model | Usage-based | Fixed + staff | Both | Shared subscription |
| Cost at low/variable use | **Cheapest** | Most expensive | Medium | Medium |
| Cost at very high steady use | Can exceed owned kit | **Can be cheapest** | Depends on split | Depends |
| Hidden costs | **Egress**, over-provisioning | Refresh cycles, idle capacity, staff | Egress **+** two estates | Governance overhead |

---

## 9. Exam traps & distractor patterns

| # | Trap | The truth |
|---|---|---|
| 1 | "AWS + Azure = hybrid" | That is **multicloud**. Hybrid requires **private/on-premises** infrastructure |
| 2 | "Private cloud means on-premises" | A private cloud may be **hosted off premises** — exclusivity defines it, not location |
| 3 | "Our on-prem data centre is a private cloud" | Only if it has the **five essential characteristics** — self-service, pooling, elasticity, metering. Otherwise it is **virtualization** |
| 4 | "A VPC is a private cloud" | ⚠️ **No** — it is an isolated **network inside public cloud**. Still public cloud deployment |
| 5 | "Dedicated hosts make it a private cloud" | Single-tenant *hardware* within a public provider is still **public cloud** |
| 6 | "Owning a data centre and an AWS account is hybrid" | Hybrid requires **integration** — connectivity, identity, tooling, portability |
| 7 | "Community cloud is just a small public cloud" | It serves a **defined group with a shared concern**, not the general public |
| 8 | "Deployment model and service model are the same" | **Independent axes** — a private PaaS is entirely normal |
| 9 | "Public cloud is always cheaper" | At **very high steady** utilisation, owned infrastructure can be cheaper. Public wins on **variable** demand |
| 10 | "Private cloud has unlimited scalability" | Elasticity **stops at the hardware you bought** |
| 11 | "Hybrid is the easiest model" | It is the **most complex** — two control planes, two security models, latency, egress, data gravity |
| 12 | "Cloud bursting works with multicloud" | Bursting requires a **private side to burst from** — so it needs hybrid |
| 13 | "Public cloud cannot meet compliance" | It frequently can, with the right region, controls, and contracts — the customer remains **accountable** (see 1.1) |
| 14 | "Community cloud has no governance overhead" | **Multi-party governance** is its main operational drawback |
| 15 | "The provider secures everything in public cloud" | Shared responsibility still applies — data, identity, and configuration are always yours |

**Disambiguation drill:**

| Pair | The deciding question |
|---|---|
| **Public vs private** | Does **anyone** share the hardware, or only **your** organisation? |
| **Private vs community** | **One** organisation, or **several with a shared concern**? |
| **Community vs public** | Is it open to **the general public**, or a **defined group**? |
| **Hybrid vs multicloud** | Does it include infrastructure **you own or exclusively control**? |
| **On-prem vs private cloud** | Does it have **self-service, pooling, elasticity, and metering**? |
| **Private cloud vs VPC** | Dedicated **hardware** (private cloud) or an isolated **network** in public cloud (VPC)? |
| **Deployment vs service model** | **Who shares it / where** (2.1) vs **what you rent** (1.1) |

---

## 10. Keyword → answer trigger table

| If you see… | Think |
|---|---|
| no capital budget · launch quickly · unpredictable traffic · global users · pay per use | **Public** |
| data must not leave our facility · single-tenant required · full hardware control · classified/sensitive | **Private** |
| we own the building and the servers | **Private, on premises** |
| dedicated environment but hosted by a partner | **Private, hosted** — still private |
| sensitive data stays private but we need elasticity · burst at peaks · gradual migration · cloud DR for our data centre | **Hybrid** |
| several organisations · same regulator or mission · share the cost of compliant infrastructure · not open to the public | **Community** |
| two public providers · avoid lock-in · survive a provider outage | **Multicloud** (not hybrid) |
| our data centre plus two public providers | **Hybrid and multicloud** |
| isolated network segment inside a public provider | **VPC — still public cloud** |
| single-tenant hardware inside a public provider | **Dedicated host — still public cloud** |
| manually provisioned VMs, ticket to get a server | **Virtualization, not a cloud** (no self-service/elasticity) |
| self-service portal · pooled · elastic · metered | **The five essential characteristics — it qualifies as cloud** |
| operated in-country under local law for government data | **Sovereign cloud** |

---

## 11. Practice questions

<details>
<summary><b>Q1.</b> An organisation runs workloads on both AWS and Azure. How is this BEST described?</summary>

A. Hybrid cloud · **B. Multicloud** · C. Community cloud · D. Private cloud

**Correct: B — multicloud.** Two or more **public** providers. Hybrid specifically requires private or on-premises infrastructure in the mix.
- **A wrong:** This is the objective's most-repeated trap.
- **C wrong:** A community cloud serves a defined group of organisations with a shared concern.
- **D wrong:** Nothing here is single-tenant infrastructure dedicated to one organisation.
</details>

<details>
<summary><b>Q2.</b> A bank must keep customer records on hardware no other organisation can access, and regulators require single tenancy. Which model applies?</summary>

**A. Private cloud** · B. Public cloud · C. Community cloud · D. Multicloud

**Correct: A — private.** Exclusive, single-tenant infrastructure dedicated to one organisation is the defining characteristic.
- **B wrong:** Public cloud is multi-tenant by definition.
- **C wrong:** A community cloud is still shared between multiple organisations.
- **D wrong:** Multicloud describes using several public providers.
</details>

<details>
<summary><b>Q3.</b> Five hospitals subject to the same health regulator jointly fund and govern a shared platform for exchanging patient records. Which model is this?</summary>

A. Public · B. Private · C. Hybrid · **D. Community**

**Correct: D — community.** Several organisations with a **shared concern** — the same regulatory regime and mission — sharing infrastructure not open to the general public.
- **A wrong:** It is not available to anyone who pays.
- **B wrong:** Private serves a single organisation.
- **C wrong:** No integration of private and public environments is described.
</details>

<details>
<summary><b>Q4.</b> A company keeps its regulated database in its own data centre while running its public website in AWS, connected by a dedicated circuit with federated identity. Which model?</summary>

A. Public · B. Private · **C. Hybrid** · D. Community

**Correct: C — hybrid.** Private/on-premises and public environments **bound together** so data and workloads move between them.
- **A/B wrong:** Each describes only half the environment.
- **D wrong:** No community of organisations is involved.
</details>

<details>
<summary><b>Q5.</b> Which statement about a private cloud is CORRECT?</summary>

A. It must be located on premises · **B. It may be on premises or hosted by a third party — exclusivity to one organisation is what defines it** · C. It is always cheaper than public cloud · D. It offers unlimited elasticity

**Correct: B.** Per NIST, a private cloud may exist on or off premises and may be operated by the organisation or a third party.
- **A wrong:** Hosted private clouds are common.
- **C wrong:** It is CapEx-heavy and often more expensive at low or variable utilisation.
- **D wrong:** Elasticity is bounded by the hardware you own.
</details>

<details>
<summary><b>Q6.</b> An organisation has a traditional data centre where engineers request VMs by raising a ticket, and IT provisions them manually within three days. Is this a private cloud?</summary>

A. Yes, because the hardware is single-tenant · **B. No — it lacks on-demand self-service, rapid elasticity, and measured service, so it is virtualization rather than cloud** · C. Yes, because it is on premises · D. No, because private clouds must be hosted

**Correct: B.** The five essential characteristics — on-demand self-service, broad network access, resource pooling, rapid elasticity, measured service — must all be present.
- **A/C wrong:** Single tenancy and location alone do not make it a cloud.
- **D wrong:** Private clouds can be on premises.
</details>

<details>
<summary><b>Q7.</b> A company creates an isolated virtual network within a public provider and calls it a "private cloud." Is this accurate?</summary>

A. Yes, it is a private cloud · **B. No — a virtual private cloud is a network isolation construct inside public cloud; the underlying infrastructure remains multi-tenant** · C. Yes, if encryption is enabled · D. Only if dedicated hosts are used

**Correct: B.** "Private" in VPC refers to **network** isolation, not exclusive hardware tenancy.
- **A/C wrong:** Neither network isolation nor encryption changes the deployment model.
- **D wrong:** Dedicated hosts give single-tenant hardware but the deployment is still public cloud.
</details>

<details>
<summary><b>Q8.</b> Which deployment model is REQUIRED for cloud bursting?</summary>

A. Public only · B. Multicloud · **C. Hybrid** · D. Community

**Correct: C — hybrid.** Bursting means overflowing from private/on-premises capacity into public cloud at peak, which requires both sides connected.
- **A wrong:** With no private side there is nothing to burst from.
- **B wrong:** Multicloud is several public providers.
- **D wrong:** A community cloud does not imply a private-to-public overflow path.
</details>

<details>
<summary><b>Q9.</b> Which is the PRIMARY drawback of the hybrid model?</summary>

A. It cannot meet compliance requirements · **B. Significantly higher complexity — two control planes, two security models, plus latency, egress costs, and data gravity between environments** · C. It offers no elasticity · D. It requires multicloud

**Correct: B.** Hybrid buys flexibility and pays for it with operational complexity.
- **A wrong:** Hybrid is often chosen *for* compliance.
- **C wrong:** Elasticity via the public side is a main benefit.
- **D wrong:** Hybrid and multicloud are independent.
</details>

<details>
<summary><b>Q10.</b> A startup has no capital budget, expects unpredictable growth, and needs to serve users on three continents within a month. Which model?</summary>

**A. Public** · B. Private · C. Community · D. Hybrid

**Correct: A — public.** No upfront cost, immediate global reach, and near-unlimited elasticity.
- **B wrong:** Private requires capital investment and time to build.
- **C wrong:** No community of organisations with shared concerns exists.
- **D wrong:** There is no private infrastructure to integrate with.
</details>

<details>
<summary><b>Q11.</b> Which pairing of deployment model to tenancy is CORRECT?</summary>

A. Public — single-tenant · B. Private — multi-tenant · **C. Community — multi-tenant within a defined group** · D. Hybrid — always single-tenant

**Correct: C.** A community cloud is shared among member organisations but closed to the general public.
- **A wrong:** Public is multi-tenant.
- **B wrong:** Private is single-tenant.
- **D wrong:** Hybrid mixes tenancy models by definition.
</details>

<details>
<summary><b>Q12.</b> Which statement about deployment models and service models is CORRECT?</summary>

A. SaaS can only be delivered from a public cloud · **B. They are independent — any service model can be delivered from any deployment model** · C. Private clouds only support IaaS · D. Hybrid clouds cannot use PaaS

**Correct: B.** *What you rent* (IaaS/PaaS/SaaS/FaaS) and *who shares it and where* (public/private/hybrid/community) are orthogonal.
- **A/C/D wrong:** Each falsely couples the two axes; a private PaaS and an internal SaaS portal are both normal.
</details>

<details>
<summary><b>Q13.</b> A manufacturer plans to migrate to the cloud over two years, moving workloads in stages while keeping its ERP on premises until last. During this period, what model describes its environment?</summary>

A. Public · B. Private · **C. Hybrid** · D. Community

**Correct: C — hybrid.** A staged migration necessarily produces an integrated private-plus-public estate for the duration.
- **A/B wrong:** Each describes only part of the environment.
- **D wrong:** No shared-concern community is involved.
</details>

<details>
<summary><b>Q14.</b> Which is a DISADVANTAGE of the community cloud model?</summary>

A. It cannot meet sector-specific compliance · **B. Multi-party governance slows decision-making, and members' priorities can conflict** · C. It offers no cost sharing · D. It is open to the general public

**Correct: B.** Shared governance is the main operational friction, alongside lower elasticity than public cloud.
- **A wrong:** Sector compliance is its main benefit.
- **C wrong:** Cost sharing is a defining advantage.
- **D wrong:** It is closed to the general public.
</details>

<details>
<summary><b>Q15.</b> An organisation runs an on-premises data centre plus workloads on both AWS and Google Cloud, all integrated. How is this BEST described?</summary>

A. Hybrid only · B. Multicloud only · **C. Both hybrid and multicloud** · D. Community cloud

**Correct: C.** On-premises plus public makes it hybrid; two public providers makes it multicloud.
- **A/B wrong:** Each captures only half.
- **D wrong:** No shared-concern community exists.
</details>

<details>
<summary><b>Q16.</b> Which characteristic must be present for an environment to qualify as a cloud, regardless of deployment model?</summary>

A. Internet accessibility only · **B. On-demand self-service, broad network access, resource pooling, rapid elasticity, and measured service** · C. Multi-tenancy · D. Third-party ownership

**Correct: B.** These are the five essential characteristics; all must be present.
- **A wrong:** Broad network access does not mean it must be internet-facing.
- **C wrong:** Private clouds are single-tenant and still clouds.
- **D wrong:** An organisation can own and operate its own private cloud.
</details>

<details>
<summary><b>Q17.</b> A public-sector body must ensure citizen data resides in-country and is handled under national law by locally vetted staff. Which option BEST addresses this?</summary>

A. Any public cloud region · **B. A sovereign or in-country community cloud operated under local jurisdiction** · C. Multicloud across foreign providers · D. A VPC in an overseas region

**Correct: B.** Sovereignty requirements are about jurisdiction, law, and personnel — not merely encryption or network isolation.
- **A wrong:** Region choice alone may not satisfy jurisdictional and staffing requirements.
- **C wrong:** Spreading across foreign providers worsens the problem.
- **D wrong:** A VPC is network isolation inside a public region and does not change jurisdiction.
</details>

<details>
<summary><b>Q18.</b> Which model typically has the HIGHEST upfront capital cost?</summary>

A. Public · **B. Private (on premises)** · C. Community · D. Multicloud

**Correct: B.** Buying hardware, facilities, licences, and staffing is capital-intensive, and capacity must be sized for peak.
- **A wrong:** Public cloud is OpEx with no capital outlay.
- **C wrong:** Community cloud shares costs among members.
- **D wrong:** Multicloud is a consumption strategy across public providers.
</details>

<details>
<summary><b>Q19.</b> An organisation uses dedicated hosts in a public provider to satisfy per-socket licensing. Has its deployment model changed?</summary>

A. Yes, it is now a private cloud · **B. No — it remains public cloud with single-tenant hardware** · C. Yes, it is now hybrid · D. Yes, it is now community

**Correct: B.** Dedicated hardware within a public provider is a tenancy option (see 1.8), not a different deployment model.
- **A/C/D wrong:** The infrastructure is still owned and operated by the public provider.
</details>

<details>
<summary><b>Q20.</b> What BINDS the components of a hybrid cloud together?</summary>

A. Simply owning both environments · **B. Network connectivity, federated identity, consistent tooling and policy, and data/application portability between them** · C. Using the same billing account · D. Physical proximity

**Correct: B.** NIST's definition requires the infrastructures be bound by technology enabling data and application portability.
- **A wrong:** Ownership without integration produces two separate estates, not a hybrid cloud.
- **C/D wrong:** Neither creates workload portability.
</details>

<details>
<summary><b>Q21.</b> Which model is generally the MOST cost-effective for a workload running at high, steady utilisation for many years?</summary>

A. Public with on-demand pricing · **B. Private, or public with long-term committed capacity** · C. Community · D. Multicloud

**Correct: B.** Constant, predictable load is where owned infrastructure or committed cloud pricing beats on-demand rates (see 1.8).
- **A wrong:** On-demand is the most expensive way to run continuously.
- **C/D wrong:** Neither addresses steady-state economics directly.
</details>

<details>
<summary><b>Q22.</b> In a private cloud, who holds the provider-side responsibilities of the shared responsibility model?</summary>

A. The cloud service provider · **B. The organisation's own IT function — it is both provider and customer** · C. The hardware vendor · D. Nobody

**Correct: B.** In a private cloud the organisation owns physical security, hardware, hypervisor, *and* the customer-side duties.
- **A wrong:** There is no external CSP in an on-premises private cloud.
- **C wrong:** Vendors supply equipment; they do not operate your platform.
- **D wrong:** The responsibilities still exist.
</details>

<details>
<summary><b>Q23.</b> A retailer keeps its inventory system on premises but wants extra web capacity only during a two-week sale. Which approach fits?</summary>

A. Migrate everything to public cloud permanently · **B. Hybrid cloud with cloud bursting during the sale** · C. Build additional on-premises capacity · D. Adopt multicloud

**Correct: B.** Bursting to public cloud for a short peak avoids buying hardware that sits idle for 50 weeks a year.
- **A wrong:** Larger change than the requirement implies, and the inventory system is to stay put.
- **C wrong:** Capital expenditure for a two-week peak is precisely what bursting avoids.
- **D wrong:** Multicloud does not provide a burst path from on-premises.
</details>

<details>
<summary><b>Q24.</b> Which is a genuine benefit of public cloud that private cloud CANNOT match?</summary>

A. Physical isolation from other tenants · **B. Effectively unlimited, instantaneous elasticity without purchasing hardware** · C. Complete control of the hypervisor · D. Guaranteed data residency in your own building

**Correct: B.** Elasticity beyond owned capacity is the structural advantage of public cloud.
- **A/C/D wrong:** All three are private cloud advantages.
</details>

<details>
<summary><b>Q25.</b> Which sequence correctly orders the models from MOST to LEAST control over the infrastructure?</summary>

A. Public → hybrid → community → private · **B. Private → hybrid → community → public** · C. Community → private → public → hybrid · D. Hybrid → public → private → community

**Correct: B.** Control decreases as the infrastructure is shared more widely and operated by others.
- **A/C/D wrong:** All invert or scramble the relationship between exclusivity and control.
</details>

---

## 12. PBQ-style drills

### Drill A — Identify the model

| # | Scenario | Model? |
|---|---|---|
| 1 | Startup on AWS only, pay-as-you-go | |
| 2 | Bank's own KL data centre with a self-service VM portal and chargeback | |
| 3 | Six universities jointly running a research platform under one funding body | |
| 4 | Manufacturer's ERP on premises, web tier in Azure, connected by ExpressRoute | |
| 5 | Enterprise using AWS for compute and Google Cloud for analytics | |
| 6 | On-premises ERP plus AWS plus Azure, all integrated | |
| 7 | Dedicated single-tenant environment hosted in a partner's data centre | |

<details><summary>Answers</summary>

1 → **Public** · 2 → **Private (on premises)** — note the self-service portal and metering qualify it as a cloud · 3 → **Community** · 4 → **Hybrid** · 5 → **Multicloud** · 6 → **Hybrid AND multicloud** · 7 → **Private (hosted)** — still private
</details>

### Drill B — Is it a cloud at all?

For each, state whether the five essential characteristics are met.

| # | Environment |
|---|---|
| 1 | VMs provisioned in minutes from a self-service portal, pooled hardware, auto-scaling, per-department chargeback |
| 2 | A rack of virtualised servers; engineers email IT and wait three days |
| 3 | A public cloud account with auto-scaling groups and detailed billing |

<details><summary>Answers</summary>

1 → **Yes — a private cloud.** Self-service ✓, pooling ✓, elasticity ✓, measured service ✓
2 → **No — virtualization only.** No self-service, no rapid elasticity, no metering
3 → **Yes — public cloud.** All five present
</details>

### Drill C — Hybrid, multicloud, or both?

| # | Environment | Which? |
|---|---|---|
| 1 | Own data centre + one public provider, integrated | |
| 2 | Two public providers, no owned infrastructure | |
| 3 | Own data centre + two public providers | |
| 4 | One public provider only, using two regions | |
| 5 | Own data centre and an AWS account, with no connectivity between them | |

<details><summary>Answers</summary>

1 → **Hybrid** · 2 → **Multicloud** · 3 → **Both** · 4 → **Neither — single public cloud** (multi-region is not multicloud) · 5 → **Neither — two separate estates.** Hybrid requires **integration**
</details>

### Drill D — Match the requirement to the model

| # | Requirement | Model? |
|---|---|---|
| 1 | Zero capital budget, launch in three weeks | |
| 2 | Classified data must remain in a facility we control | |
| 3 | Keep the regulated database private, scale the web tier on demand | |
| 4 | Several agencies under one ministry sharing compliant infrastructure | |
| 5 | Survive an entire cloud provider's outage | |
| 6 | Per-socket licensed database inside a public provider | |

<details><summary>Answers</summary>

1 → **Public** · 2 → **Private, on premises** · 3 → **Hybrid** · 4 → **Community** · 5 → **Multicloud** · 6 → **Public with a dedicated host** — the deployment model does not change (see 1.8)
</details>

---

## 13. 60-second recall sheet

```text
╔══════════════════════════════════════════════════════════════════════╗
║  2.1 — CLOUD DEPLOYMENT MODELS  (Domain 2.0 Deployment = 19%)        ║
║  Ask: ① WHO SHARES IT?  ② WHERE IS IT?  ③ ARE THEY INTEGRATED?      ║
╠══════════════════════════════════════════════════════════════════════╣
║  PUBLIC     multi-tenant, ANYONE · provider-owned, off-prem · OPEX   ║
║             + no CapEx, unlimited elasticity, global, fastest        ║
║             − least control, shared tenancy, residency, egress       ║
║  PRIVATE    SINGLE-TENANT, ONE ORG · on-prem OR HOSTED · CAPEX       ║
║             + max control, strongest isolation, easiest compliance   ║
║             − high upfront, elasticity CAPPED BY OWNED HARDWARE,     ║
║               you own all operations                                 ║
║  HYBRID     private/on-prem + public, BOUND TOGETHER                 ║
║             + regulated data private + public elasticity, bursting,  ║
║               staged migration, cheap DR                             ║
║             − MOST COMPLEX: two control planes, latency, egress,     ║
║               data gravity                                           ║
║  COMMUNITY  shared by a DEFINED GROUP with a COMMON CONCERN          ║
║             (same regulator/mission) · NOT the general public        ║
║             + shared cost, sector-specific compliance                ║
║             − multi-party governance, less elastic, rarest           ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ TRAPS                                                             ║
║   AWS + Azure = MULTICLOUD, ***NOT HYBRID***                         ║
║     HYBRID needs something YOU own/control                           ║
║     Own DC + 2 public providers = BOTH                               ║
║   PRIVATE ≠ ON-PREM (may be hosted) ·                                ║
║   ON-PREM ≠ PRIVATE CLOUD (needs the 5 characteristics)              ║
║   "VPC" = isolated NETWORK inside PUBLIC cloud — NOT a private cloud ║
║   DEDICATED HOST = single-tenant HARDWARE, still PUBLIC deployment   ║
║   Owning both but NOT integrated = two estates, not hybrid           ║
║   CLOUD BURSTING REQUIRES HYBRID (needs a private side)              ║
╠══════════════════════════════════════════════════════════════════════╣
║  5 ESSENTIAL CHARACTERISTICS (NIST) — needed in ANY model            ║
║   ON-DEMAND SELF-SERVICE · BROAD NETWORK ACCESS · RESOURCE POOLING · ║
║   RAPID ELASTICITY · MEASURED SERVICE                                ║
║   Manual ticket-based VM provisioning = VIRTUALIZATION, not cloud    ║
╠══════════════════════════════════════════════════════════════════════╣
║  ★ DEPLOYMENT MODEL (2.1: who shares it / where)                     ║
║    ≠ SERVICE MODEL (1.1: IaaS/PaaS/SaaS/FaaS — what you rent)        ║
║    INDEPENDENT AXES — a private PaaS is entirely normal              ║
║  CONTROL: private > hybrid > community > public                      ║
║  ELASTICITY: public > hybrid > community > private                   ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 14. Cross-references

| Related objective | Connection |
|---|---|
| **1.1 Service models** | **The other axis.** 1.1 = what you rent; 2.1 = who shares it and where. In a private cloud you are both provider and customer |
| **1.2 Service availability** | **Cloud bursting requires hybrid**; multicloud tenancy is the resilience argument for multiple providers |
| **1.3 Cloud networking** | VPN and dedicated connections are what physically bind a hybrid cloud; a **VPC is a network, not a deployment model** |
| **1.7 Virtualization** | Private clouds are built on hypervisor clusters — but virtualization alone is not a cloud |
| **1.8 Cost considerations** | **CapEx vs OpEx** is the core economic contrast; dedicated hosts are a tenancy option within public cloud |
| **2.3 Cloud migration** | Migration types (on-prem→cloud, cloud→cloud, cloud→on-prem) move you between these models; vendor lock-in and data gravity are the constraints |
| **4.2 Compliance** | Data residency, sovereignty, and sector regulation are the main drivers of private and community choices |
| **4.3 IAM** | **Federated identity** is what makes a hybrid cloud usable as one environment |

> 🔑 **Carry this forward:** identify the model by **who shares the infrastructure**, never by where it happens to sit. Location is a property; tenancy is the definition.

---

*Source of truth: CompTIA Cloud+ CV0-004 V4 Exam Objectives, document version 5.0. Deployment-model and essential-characteristic definitions follow NIST SP 800-145, the standard CompTIA's list is drawn from. Product names are illustrative; the exam is vendor-neutral.*
