# Exam-Day Quick Reference

> Print this. Read it the morning of the exam. Move on.

## 30-Second Mental Model

1. **Pick the service model** (IaaS / PaaS / SaaS / FaaS) — *what does the customer manage?*
2. **Pick the architecture** (single region, multi-AZ, multi-region, multicloud) — *how much can we tolerate failing?*
3. **Pick the DR site** (hot / warm / cold / pilot light) — *what's the RTO and RPO?*
4. **Pick the network** (VPC, subnet, LB, VPN, CDN) — *who can reach what?*
5. **Pick the storage** (block / file / object / archive) — *how is it accessed and protected?*
6. **Pick the cost model** (on-demand / reserved / spot) — *predictability vs discount.*

## Acronyms to Know Cold

| | |
|---|---|
| **IaaS / PaaS / SaaS / FaaS** | Service models |
| **RTO / RPO** | Recovery time / point |
| **AZ** | Availability Zone |
| **VPC / VNet** | Virtual private cloud / network |
| **IAM** | Identity & access management |
| **ACL** | Access control list |
| **NACL** | Network ACL (stateless) |
| **SG** | Security group (stateful) |
| **CDN** | Content delivery network |
| **DNS** | Domain name system |
| **IaaC / IaC** | Infrastructure as Code |
| **CI / CD** | Continuous integration / delivery |
| **K8s** | Kubernetes |
| **OLTP / OLAP** | Transactional / analytical |
| **ACID / BASE** | RDBMS / NoSQL consistency models |
| **TCO** | Total cost of ownership |
| **FinOps** | Cloud financial operations |

## Decision Heuristics

| Scenario | Default answer |
|---|---|
| "Lift-and-shift legacy app" | IaaS |
| "Event-driven, spiky" | FaaS |
| "Email + Office for 5k users" | SaaS (M365 / Google Workspace) |
| "Need custom kernel module" | IaaS |
| "Minimize ops, push code" | PaaS |
| "Mission-critical, near-zero downtime" | Multi-AZ + hot DR site |
| "Low-priority batch job" | Spot / Preemptible + cold DR |
| "Global user base, static assets" | CDN + object storage + edge |

## Common Traps

1. **Confusing region vs AZ.** AZs are inside a region; "redundancy" usually means AZ-level.
2. **Assuming cross-region replication is default.** It is not. You opt in.
3. **Forgetting egress costs.** Cross-region and internet-out traffic is the #1 surprise cost.
4. **Over-allocating in IaaS when PaaS would do.** Pay for control you don't use.
5. **Under-provisioning FaaS memory / timeout.** Lambda has a 15-min max.
6. **Treating "serverless" as "no cost when idle".** You pay for requests + GB-s, even if tiny.

## Last-Minute Checks

- [ ] ID + confirmation number
- [ ] Schedule location + start time
- [ ] Two forms of ID
- [ ] No phones / smart watches
- [ ] Water bottle (clear, no label)
- [ ] Breath deeply — you know this.

**You've got this.** 💪
