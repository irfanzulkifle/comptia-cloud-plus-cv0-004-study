---
type: certification
name: "CompTIA Cloud+ CV0-004 — Objective 4.3: Identity and Access Management"
aliases: [Objective-4.3-Identity-Access-Management, Domain-4.3-IAM, IAM, Identity, Access, Authentication, Authorization, Accounting, API, SDK, CLI, SSH, RDP, Bastion, SAML, OIDC, OAuth2, RBAC, MFA, Federation, Directory, Token, Audit-Trail]
certification: CompTIA Cloud+
exam-code: CV0-004
domain: 4.3
status: active
created: 2026-07-13
last-touched: 2026-07-13
tags: [certification, cloud-plus, comptia, domain-4.3, security, iam, identity, access, authentication, authorization, accounting, api, sdk, cli, ssh, rdp, bastion, saml, oidc, oauth2, rbac, mfa, federation, directory, token, audit-trail, exam-prep]
---

# CompTIA Cloud+ CV0-004 — Domain 4.0 Security
## 📘 Objective 4.3: Given a Scenario, Implement Identity and Access Management

> **Source:** Official CompTIA Cloud+ CV0-004 V4 Exam Objectives Document v5.0
> **Exam Weight:** 4.0 Security = 20% of total exam
> **Objective 4.3 Focus:** Implementing IAM — **secure access to the management environment** (programmatic API/SDK/CLI/web portal; SSH; RDP; bastion host), **secure access to resources** (API), **authentication models** (local, federation/SAML, token-based, directory-based, MFA, OpenID Connect), **authorization models** (RBAC, group-based, OAuth 2.0, discretionary), and **accounting** (audit trail).

---

## SECTION 1 — OBJECTIVE OVERVIEW

### Objective Number: 4.3
### Objective Name: *Given a scenario, implement identity and access management.*

**What this objective means (beginner-friendly):**

IAM answers three questions about everyone who touches your cloud:
1. **Authentication (who are you?):** Prove identity — password, MFA, token, SSO.
2. **Authorization (what can you do?):** Permissions — roles, groups, policies.
3. **Accounting (what did you do?):** A log of actions — audit trail.

And it covers *how* people and systems connect: admins via web portal / CLI / SDK / API, servers via SSH/RDP (ideally through a **bastion host**), and apps via APIs. 4.3 = "let the right identities in, give them only the right permissions, and record everything."

**Why it matters in the real world:**
- IAM is the front door — most breaches are stolen credentials or excessive permissions, not fancy exploits.
- Least privilege + MFA stops the majority of attacks.
- Federation/SSO lets enterprises use one identity source (Active Directory) across clouds.
- Audit trails prove compliance (ties to 4.2) and enable forensics (4.6).

**Why CompTIA tests it:**
- 4.3 is scenario-heavy: "connect to a private instance" → bastion + SSH key; "use corporate login in cloud" → federation/SAML; "limit blast radius" → RBAC/group.
- The **auth vs authz** distinction (who vs what) and the **model names** (SAML/OIDC/OAuth2/RBAC) are classic distractors.
- It builds on 2.4 (IAM in IaC), 3.4 (revoke access on decommission), 4.1/4.2.

---

## SECTION 2 — EXAM KNOWLEDGE

### 4.3.1 — SECURE ACCESS TO THE MANAGEMENT ENVIRONMENT

The "management environment" = the **control plane** you use to administer the cloud — consoles, CLIs, SDKs, and APIs.

**Programmatic access** (code-driven administration):
- **API** (Application Programming Interface) — REST calls to the control plane.
- **SDK** (Software Development Kit) — language libraries that wrap those APIs.

**CLI** (Command-Line Interface; the objective lists it as "Common Language Infrastructure (CLI)") — `aws`, `az`, `gcloud`.

**Web portal** — the browser-based console / GUI.

**Security (all of the above):** Use **service accounts / IAM roles** (not embedded user passwords); scope keys tightly; rotate; store in a secrets manager (never in code). CLI/SDK/API are how automation (2.4) and IaC run.
**Exam angle:** "A script needs to deploy" → service account / IAM role with least privilege (not your personal admin creds).

### 4.3.2 — SECURE ACCESS TO CLOUD RESOURCES

How users and apps reach the **resources themselves** (instances, data, services) — distinct from administering the control plane:

- **API** — programmatic access to resources (e.g., reading an object, calling a service); secured with keys / tokens / signed requests (SigV4), OAuth 2.0 for delegated app access, least privilege on the resource policy.
- **SSH** (Secure Shell) — encrypted terminal to Linux/Unix instances; **key-based auth** (not passwords); reached via bastion for private instances.
- **RDP** (Remote Desktop Protocol) — graphical remote desktop to Windows instances; enforce NLA, restrict exposure, reach via bastion (never public — it's a brute-force target).
- **Bastion host** (jump host) — a hardened, tightly controlled intermediate server used to reach **private** instances with no direct internet exposure; the single monitored entry point for SSH/RDP.

**Exam angle:** "Private DB needs admin access" → bastion host, not a public IP on the DB.

### 4.3.3 — AUTHENTICATION MODELS (WHO ARE YOU?)

#### Local Users
**Definition:** Identities stored **locally** in the cloud account (native IAM users).
**Use:** Small setups; each cloud has its own local users.
**Limitation:** No SSO across systems; more identities to manage.
**Exam angle:** "Standalone cloud account, few admins" → local IAM users.

#### Federation (SAML)
**Definition:** Trusting an **external identity provider (IdP)** — e.g., corporate Active Directory — to authenticate users. Uses **SAML** (Security Assertion Markup Language) to exchange assertions.
**Use:** Enterprise SSO — log in with your corporate credentials; no separate cloud password.
**Why:** Centralized identity, offboarding via IdP, MFA at the IdP.
**Exam angle:** "Use existing corporate login for cloud" → federation with SAML.

#### Directory-Based
**Definition:** Integrating with a directory service (e.g., AD, Entra ID, LDAP) as the identity source.
**Use:** Sync on-prem identities to cloud; group-based access from the directory.
**Exam angle:** "On-prem AD users need cloud access" → directory integration / federation.

#### Token-Based
**Definition:** Authentication via a **token** (e.g., JWT, session token, API token) issued after login; presented on each request.
**Use:** APIs, stateless auth, short-lived credentials.
**Exam angle:** "API calls authenticated per request" → token-based (bearer/JWT).

#### Multifactor Authentication (MFA)
**Definition:** Requiring **two+ factors** (something you know — password; have — device/OTP; are — biometrics).
**Purpose:** Defeats stolen-password attacks — the single biggest win.
**Use:** Enforce on all human logins, especially admin/root.
**Exam angle:** "Protect against credential theft" → MFA (mandatory conceptually).

#### OpenID Connect (OIDC)
**Definition:** An **authentication** layer on top of OAuth 2.0 — uses JWT **ID tokens** to verify *who* the user is (SSO for apps).
**Use:** Modern web/mobile app SSO; "Log in with Google/Microsoft."
**Distinction:** OIDC = authentication (who); OAuth 2.0 = authorization (what, delegated).
**Exam angle:** "App needs to know who the user is via JWT" → OIDC.

### 4.3.4 — AUTHORIZATION MODELS (WHAT CAN YOU DO?)

#### Role-Based Access Control (RBAC)
**Definition:** Permissions assigned by **role**; users/groups are granted roles that carry the needed permissions.
**Use:** The default cloud model — scales, easy to audit, supports least privilege.
**Exam angle:** "Limit blast radius by job function" → RBAC (assign least-privilege roles).

#### Group-Based Access Control
**Definition:** Permissions granted by **group membership** (often sourced from a directory like AD/Entra).
**Use:** Manage access in bulk — add a user to a group, they inherit its permissions.
**Exam angle:** "Whole team needs the same access" → group-based.

#### OAuth 2.0
**Definition:** A **delegated authorization** framework — an app gets scoped, short-lived tokens to act *on a user's behalf* (e.g., "post to your drive").
**Use:** Third-party/app access without sharing the user's credentials.
**Distinction:** OAuth 2.0 = authorization (what, delegated); OIDC = authentication (who). Don't swap them.
**Exam angle:** "App needs to access the user's cloud resources" → OAuth 2.0.

#### Discretionary Access Control (DAC)
**Definition:** The **resource owner** sets who can access their own resources via ACLs.
**Use:** Flexible/owner-driven (e.g., file/folder permissions); doesn't scale as well as RBAC for large orgs.
**Exam angle:** "Owner grants per-file access" → discretionary (DAC).

### 4.3.5 — ACCOUNTING

#### Audit Trail
**Definition:** A **record of every action** taken (who did what, when, from where) — the "accounting" leg of AAA.
**Purpose:** Forensics, incident investigation, and compliance evidence (ties to 4.2 retention/holds).
**How it works:** Native services log API/console activity — AWS CloudTrail, Azure Activity Log, GCP Cloud Audit Logs — streamed to a SIEM for detection (feeds 4.6).
**Exam angle:** "Investigators need to know what the attacker did" → the audit trail (accounting), not authn/authz.

> **The AAA split:** **Authentication** = who you are (4.3.3). **Authorization** = what you can do (4.3.4). **Accounting** = what you did (4.3.5, audit trail). AuthN → AuthZ → Accounting, in that order.


## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 AWS — IAM Roles, Federated SSO, Bastion & CloudTrail

In an AWS environment, you never embed a **user's long-lived access keys** inside application code. Instead, an **EC2 instance assumes an IAM role** through its instance profile, and that role's **temporary security token** (via STS) grants the least privilege needed to read an S3 bucket or write to DynamoDB. For automation, **IAM roles for service accounts (IRSA)** or cross-account roles replace static credentials entirely.

Human administrators reach the cloud through **IAM Identity Center (AWS SSO)**, which federates with the corporate **Active Directory / Entra ID** using **SAML 2.0**. The user logs in once at the corporate IdP, gets a SAML assertion, and is mapped to a permission set — no separate AWS password to manage. Privileged SSH into private subnets is forced through a **bastion host** (or AWS Systems Manager Session Manager) so private instances have **no public IP** and security groups only allow 22 from the bastion.

**MFA** is enforced at the IdP and optionally via an IAM policy condition (`aws:MultiFactorAuthPresent`). Every API call — who did what, from where, to which resource — is captured by **CloudTrail** for the **accounting / audit trail**. Least privilege is applied through managed and customer-managed **policies attached to groups and roles**, never to individual users where avoidable.

### 3.2 Azure — Entra ID, RBAC + Groups, Azure Bastion & Activity Log

Azure centres identity on **Microsoft Entra ID** (formerly Azure AD). On-prem **Active Directory** is synchronized via **Entra Connect**, and external partners federate in through **SAML / OIDC** enterprise apps so they authenticate at their own IdP. Users are placed into **security groups**, and **Azure RBAC** role assignments (e.g. *Contributor*, *Reader*, *Owner*) are attached to those groups scoped to a subscription or resource group — this is the authorization layer, deciding **what** each identity can do.

Admins never expose VMs to the internet for RDP. **Azure Bastion** is a PaaS service deployed in the virtual network that provides browser-based RDP/SSH through the Azure portal, keeping VMs with **no public IP**. For programmatic access, applications use a **managed identity** (system-assigned or user-assigned) that fetches an **OAuth 2.0 token** from Entra ID automatically — no secrets in config files.

**MFA** and **Conditional Access** (block legacy auth, require compliant device) are enforced at the tenant. All control-plane and data-plane activity is recorded in the **Azure Activity Log** and **Azure Monitor**, giving the **audit trail** required for accounting. Directory-based and group-based models keep access scalable instead of per-user.

### 3.3 GCP — IAM Roles, Service Accounts, Cloud Identity & Audit Logs

Google Cloud Platform uses **Cloud IAM** with three role types: **Basic** (Owner/Editor/Viewer — avoid), **Predefined** (granular, service-specific), and **Custom**. Access is granted to **members** (users, groups, service accounts) on resources using **least privilege**. Human users authenticate via **Cloud Identity** federated with a corporate IdP using **SAML 2.0 or OIDC**, so staff sign in with their existing corporate credentials.

For workloads, **service accounts** are the equivalent of AWS IAM roles — a VM's **service account** provides credentials to call APIs, and short-lived tokens are minted automatically. Workload Identity Federation even lets external workloads (e.g. on-prem or another cloud) assume a service account via **OIDC** without a stored key — strongly preferred over downloaded JSON keys. Private access is brokered by **Identity-Aware Proxy (IAP)**, which authorizes users per-application at the **HTTPS layer** using their identity, or by a **bastion** for SSH.

**MFA** is applied through the Google Cloud organization policy / IdP. All activity is captured in **Cloud Audit Logs** (Admin Activity, Data Access, System Event), forming the **accounting** record. Group-based binding keeps grant management clean.

### 3.4 Corporate SSO via Federation / SAML

A typical enterprise runs its own **identity provider (IdP)** — Active Directory, Entra ID, Okta, or PingFederate. Instead of creating a cloud account per user, the organisation sets up **federation**: the cloud becomes a **service provider (SP)** that trusts SAML assertions from the IdP. When an employee clicks "Log in to Cloud Console," they are redirected to the corporate login page, enter credentials + **MFA**, and the IdP returns a signed **SAML 2.0 assertion** carrying the user's identity and group memberships. The cloud maps those claims to local roles/groups.

**Key benefit:** the user has **one password** (at the IdP), and de-provisioning at the IdP instantly removes cloud access — satisfying **least privilege** and audit requirements. **OIDC** is the modern JSON/JWT alternative often used for web and mobile apps, while **SAML** remains dominant for enterprise SSO.

Exam cue: **SAML/OIDC federation is authentication + identity propagation** — it answers *who* you are. It does **not** by itself decide *what* you can do in the cloud; that authorization still comes from the cloud's **RBAC/group mapping** of the asserted identity. MFA is enforced at the **IdP**, so the cloud inherits it.

### 3.5 Bastion Host + Least-Privilege Service Account

When VMs live in **private subnets** with no public IP, administrators and automation cannot connect directly from the internet. A **bastion host** (jump box) sits in a public subnet with tightly locked-down security group rules — it is the **single, monitored entry point** for SSH/RDP into private instances. The bastion itself is hardened, patched, and its logs feed the **audit trail**. This satisfies the principle of **least privilege** for network exposure: only the bastion is reachable, only from approved source IPs, only on 22/3389.

For **programmatic / automation** access (CI/CD pipelines, backup scripts, config management), you do **not** put a human user's credentials in code. Instead you create a **least-privilege service account or IAM role** scoped to exactly the actions the script needs (e.g. read from bucket X, start instance Y). The application assumes/uses that identity and receives **short-lived tokens** — no long-lived secret embedded in source control.

Exam cue: **bastion = secure private access; not a public IP on every box.** **Service account/role = programmatic access; not user creds in code.** Pair both with **MFA at the IdP** for humans and **CloudTrail / Activity Log / Audit Logs** for accounting, and you have a complete 4.3 design.

---

## SECTION 4 — COMPARISON TABLES

### 4.1 Authentication vs Authorization

| Aspect | **Authentication** (AuthN) | **Authorization** (AuthZ) |
|--------|----------------------------|----------------------------|
| Answers | **WHO** are you? | **WHAT** can you do? |
| Proof | Password, key, token, MFA, biometric | Permissions, roles, policies |
| Stage | Happens **first** | Happens **after** AuthN |
| Example | Logging in with SAML/OIDC | Being allowed to delete a VM (RBAC) |
| Protocols | SAML, OIDC, MFA, tokens | RBAC, OAuth 2.0 scopes, group membership |

### 4.2 Authentication Models

| Model | What it is | Use / Exam cue |
|-------|-----------|----------------|
| **Local users** | Accounts stored in the system/cloud itself | Small setups; risk of many passwords; not scalable |
| **Federation / SAML** | Trust an external IdP's SSO assertion | Enterprise SSO; one corporate login; **authentication + identity** |
| **Directory-based** | Sync with AD / Entra ID / LDAP | Central identity source; group membership flows in |
| **Token-based / JWT** | Signed token proves identity for the session | APIs & microservices; stateless; short-lived |
| **MFA** | Second factor (TOTP, SMS, hardware key) | **Always** add for privileged/admin access |
| **OpenID Connect (OIDC)** | Auth layer on OAuth 2.0; returns **ID token (JWT)** | **Authentication** — confirms *who*; modern web/mobile SSO |

### 4.3 Authorization Models

| Model | What it does |
|-------|--------------|
| **RBAC** (Role-Based Access Control) | Permissions assigned by **role**; users get roles; scales well |
| **Group-based** | Access tied to **group membership**; admin manages groups not individuals |
| **OAuth 2.0** | **Delegated authorization** — grants an app scoped access to resources on your behalf (access token) |
| **Discretionary (DAC)** | Resource **owner** decides who accesses their object (e.g. file permissions); flexible, less central control |

### 4.4 Access Methods — Management Environment vs Resources

**Management environment (control plane):**
| Method | What | Security |
|--------|------|----------|
| **API** | REST control-plane calls | Service account / IAM role, least privilege |
| **SDK** | Language libraries over the API | Same as API |
| **CLI** | Command-line admin (`aws`/`az`/`gcloud`) | Roles, no embedded creds |
| **Web portal** | Browser console | MFA + conditional access |

**Cloud resources (data/compute access):**
| Method | What | Security |
|--------|------|----------|
| **API** | Programmatic resource access | Tokens / signed requests / OAuth 2.0, least privilege on resource policy |
| **SSH** | Encrypted Linux terminal | Key-based, via bastion |
| **RDP** | Windows remote desktop | NLA, via bastion, never public |
| **Bastion host** | Jump box to private instances | Single hardened, monitored entry; no public IP on targets |

### 4.5 SAML vs OIDC vs OAuth 2.0

| Protocol | Primary job | Token type | Typical use |
|----------|-------------|------------|-------------|
| **SAML 2.0** | **Federation / SSO** (authentication assertion) | XML assertion | Enterprise SSO into cloud console |
| **OIDC** | **Authentication** (who) | **JWT ID token** | Modern web/mobile login |
| **OAuth 2.0** | **Authorization** (delegated access) | Access token (bearer) | App acts on your behalf (scoped) |

> Remember: **SAML = federation**, **OIDC = auth**, **OAuth2 = authz**. OIDC rides on top of OAuth 2.0 but is the *authentication* part.

### 4.6 Local Users vs Federation

| | **Local users** | **Federation (SAML/OIDC)** |
|--|-----------------|-----------------------------|
| Password stored | In the cloud/system | Only at the corporate **IdP** |
| Login count | One per system | **One** corporate login (SSO) |
| De-provisioning | Manual per system | Automatic at IdP |
| MFA | Configured per system | Enforced once at IdP |
| Scalability | Poor | Strong |
| Exam cue | avoids password sprawl problems | preferred for enterprise / 4.3 scenarios |

---

## SECTION 5 — EXAM TRAPS

**Trap #1 — AuthN vs AuthZ mix-up**
*Scenario:* A user successfully logs in but cannot launch a VM. What failed?
*Correct:* **Authorization** (AuthZ) — they were authenticated (who) but lack permission (what).
*Why wrong:* Logging in is authentication; being blocked from an action is an authorization/RBAC failure, not an authentication failure.

**Trap #2 — SAML is federation, not authorization**
*Scenario:* Your company uses SAML SSO to the cloud. Does SAML decide what files a user may delete?
*Correct:* **No.** SAML **authenticates** (who) and asserts identity/groups; the cloud's **RBAC/group mapping** decides what they can do.
*Why wrong:* SAML answers *who you are*, not *what you can do*. Authorization is a separate cloud-side step.

**Trap #3 — OIDC vs OAuth 2.0**
*Scenario:* An app needs to know the logged-in user's identity. Which protocol?
*Correct:* **OIDC** — it returns an **ID token (JWT)** proving identity.
*Why wrong:* OAuth 2.0 is **authorization** (delegated access), not identity. Using OAuth 2.0 alone tells you what the app may do, not *who* the user is.

**Trap #4 — MFA is mandatory for privileged access**
*Scenario:* Admins log in via federated SSO. Is MFA optional?
*Correct:* **No — MFA should be enforced**, ideally at the IdP for all admin/privileged access.
*Why wrong:* Federation alone is not enough; without MFA a stolen password bypasses everything. MFA is a core 4.3 control.

**Trap #5 — Bastion for private access, not public IP**
*Scenario:* To let admins SSH to private VMs, you assign each VM a public IP and open port 22.
*Correct:* **Wrong.** Use a **bastion host**; keep VMs private (no public IP); allow 22 only from the bastion.
*Why wrong:* Exposing every VM to the internet massively increases attack surface. Bastion = single monitored entry point.

**Trap #6 — Service account/role, not user creds in code**
*Scenario:* A backup script needs S3 access. A developer hard-codes their own AWS access key in the script.
*Correct:* **Wrong.** Create a **least-privilege IAM role / service account**; the app assumes it and gets short-lived tokens.
*Why wrong:* Embedding user credentials in code leaks secrets and breaks least privilege. Roles/service accounts are the exam-correct design.

**Trap #7 — SSH keys, not passwords**
*Scenario:* Hardening Linux access, you require complex SSH passwords.
*Correct:* **Use SSH key-based auth**, not passwords.
*Why wrong:* Key-based authentication is stronger and the 4.3 standard; passwords are weaker and more brute-forceable. Disable password auth.

**Trap #8 — RBAC/group vs Discretionary**
*Scenario:* The cloud uses file-owner-set permissions where each user decides who accesses their objects.
*Correct:* That is **Discretionary (DAC)**, not **RBAC**. RBAC assigns permissions by **role**, centrally managed.
*Why wrong:* DAC = owner-controlled; RBAC = role/central-controlled. Mixing them up fails the scenario.

**Trap #9 — Directory integration**
*Scenario:* You want cloud group memberships to follow on-prem AD automatically.
*Correct:* Use **directory integration / federation** (e.g. Entra Connect, AD sync, LDAP).
*Why wrong:* Local-only cloud accounts won't reflect AD groups; you'd manage twice. Directory sync keeps one source of truth.

**Trap #10 — Token-based for APIs**
*Scenario:* A microservice calls the cloud API repeatedly using a static username/password.
*Correct:* Use **token-based (JWT) / OAuth** short-lived tokens instead.
*Why wrong:* Tokens are stateless, scoped, and revocable; long-lived passwords in services are a security risk and not the exam answer.

**Trap #11 — Audit trail = Accounting**
*Scenario:* The question asks which IAM pillar provides the record of "who did what, when."
*Correct:* **Accounting** — via **CloudTrail / Activity Log / Cloud Audit Logs**.
*Why wrong:* Authentication proves identity; authorization grants access. The *record* of activity is **accounting**, the third A in AAA.

**Trap #12 — Least privilege**
*Scenario:* You grant a new hire the "Admin/Owner" role "just in case."
*Correct:* **Wrong.** Apply **least privilege** — only the permissions the job requires.
*Why wrong:* Over-provisioning violates least privilege and is a classic trap; grant minimal roles/groups and escalate only when needed.

**Trap #13 — Federation removes the separate cloud password**
*Scenario:* After enabling SAML federation, users still need a cloud-specific password.
*Correct:* **No** — with true federation the user authenticates at the **IdP**; the cloud trusts the assertion. No separate cloud password is needed.
*Why wrong:* The whole point of federation/SSO is one credential at the IdP. A lingering cloud password means federation isn't fully configured.

**Trap #14 — MFA at the IdP with federation**
*Scenario:* SAML is configured, but the cloud console has no MFA prompt. Is MFA working?
*Correct:* **Maybe not.** MFA must be enforced at the **IdP** (or via the cloud's own MFA policy) — federation alone doesn't guarantee it.
*Why wrong:* The assertion only proves the IdP authenticated the user; if the IdP didn't require MFA, the cloud inherits no second factor. Verify MFA at the IdP.

**Trap #15 — Open RDP/SSH to the internet is bad**
*Scenario:* To simplify remote work, RDP port 3389 and SSH port 22 are opened to 0.0.0.0/0 on all VMs.
*Correct:* **Wrong.** Restrict to bastion / VPN / private network and enforce MFA; never expose management ports to the entire internet.
*Why wrong:* Internet-exposed RDP/SSH is a top breach vector. The exam expects bastion, private subnets, and narrowed source ranges.

## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

Below are 5 performance-based question (PBQ) scenarios in the style CompTIA uses on the CV0-004 exam. Each gives a Scenario, a Walkthrough that names the correct model/term, and a Bonus Q to test deeper understanding.

### PBQ 1 — Secure admin access to a private Linux database

**Scenario:** A Linux database instance lives in a private subnet with no public IP address. A cloud admin must connect over SSH to perform maintenance. You cannot expose the database to the internet.

**Walkthrough:**
- Place a **bastion host** (hardened jump box) in a public subnet to act as the single entry point — the private DB keeps **no public IP**.
- Use **SSH key-based authentication** (key pair), never password-only login, for both the bastion and the DB hop.
- Connect admin → bastion (public IP) → private DB (private IP only reachable from inside the VPC).
- Lock down the bastion with security-group rules: only the admin's source IP / CIDR allowed on port 22.
- Audit the session (accounting) since the bastion is a privileged chokepoint.

**Bonus Q:** Why is RDP with NLA a bad fit here and why must the DB itself have no public IP? *(Ans: RDP is a Windows protocol, not for Linux; a public IP on the DB defeats the private-subnet isolation and widens the attack surface. Use SSH + bastion.)*

### PBQ 2 — Enterprise corporate login to the cloud

**Scenario:** A large company wants its employees to log into the cloud console using their existing corporate usernames and passwords. They do not want to manage a separate cloud password for every user.

**Walkthrough:**
- Implement **federation** using **SAML 2.0** so the corporate IdP (e.g., on-prem AD/Entra) is the source of identity.
- Set up **SSO** so one corporate login grants access to the cloud — the cloud is the service provider (SP), the corporate directory is the identity provider (IdP).
- Enforce **MFA at the IdP** so the second factor is checked before the SAML assertion is issued.
- Map corporate groups/roles to cloud permissions (authorization stays in the cloud after authentication is federated).

**Bonus Q:** Could you achieve the same "use corporate login" goal with OIDC instead of SAML? *(Ans: Yes — OIDC is also a federation protocol and returns a JWT ID token proving "who" the user is. SAML uses XML assertions; OIDC uses JWTs. Either works for federated auth; the exam just wants you to recognize federation/SSO.)*

### PBQ 3 — Automation script needs to deploy resources

**Scenario:** A CI/CD pipeline script must deploy virtual machines and storage automatically at 3 a.m. with no human at the keyboard. You must not hardcode any passwords.

**Walkthrough:**
- Create a non-human **service account** / **IAM role** for the automation identity (not a real person's login).
- Grant the role **least-privilege** permissions — only the deploy actions it needs, nothing more.
- Use the platform's **programmatic access** (API / SDK / CLI) with short-lived tokens or assigned roles instead of static keys where possible.
- Store any required secrets in a **secrets manager** (not in the script, not in env files committed to git).
- Enable **accounting/audit logging** so each automated action is attributable to the service account.

**Bonus Q:** Why is a service account with least privilege safer than reusing an admin's API key? *(Ans: Blast radius is contained — if the automation is compromised it can only do its narrow job, and the credential is scoped/revocable independently of a human admin.)*

### PBQ 4 — Limit the blast radius of permissions

**Scenario:** You are designing permissions for a cloud project team. A mistake by one engineer must not be able to delete the whole production environment.

**Walkthrough:**
- Use **RBAC (role-based access control)** — assign permissions to roles, not directly to individuals.
- Combine with **group-based** assignment — put engineers in a "Dev" group mapped to a limited role; admins in a separate "Admin" group.
- Apply **least privilege** — Dev role gets deploy/read, not delete-production.
- Separate environments (dev/test/prod) into different roles or scopes so prod is protected.
- Keep an **audit trail** so any destructive action is traceable to a role/group.

**Bonus Q:** Where does "discretionary" (owner-set ACL) access fit, and why isn't it the primary control here? *(Ans: Discretionary ACLs let a resource owner grant access ad hoc — flexible but easy to over-share and hard to govern at scale. RBAC + groups give consistent, auditable, least-privilege control across a team.)*

### PBQ 5 — App needs access to a user's cloud resources

**Scenario:** A third-party photo-printing app wants to read a user's files stored in their cloud bucket so it can print them. The app should NOT get the user's password.

**Walkthrough:**
- Use **OAuth 2.0** for **delegated authorization** — the user grants the app scoped access ("what" it may do) without sharing credentials.
- The app receives an **access token** (often a JWT) limited to the granted scope and expiry.
- This is **authorization (WHAT)**, not authentication — the app acts on the user's behalf, it does not log the user in.
- Contrast with **OIDC**, which would be used if the goal were "let the user log into the app using their cloud identity" (auth, WHO, via a JWT ID token).

**Bonus Q:** If the requirement were "let users log into the printing app using their Google/Microsoft account" instead, which protocol changes? *(Ans: That is authentication, so you'd use OIDC (or SAML), not OAuth 2.0. OAuth 2.0 = delegated authorization/WHAT; OIDC = authentication/WHO.)*

---

## SECTION 7 — MEMORY AIDS

### 7.1 Authentication vs Authorization
- **Authentication = WHO** — proving the identity of the user/service ("Are you really you?").
- **Authorization = WHAT** — deciding what that identity is allowed to do ("What can you touch?").
- Mnemonic: **Auth-N = Name, Auth-Z = Ze permissions.**

### 7.2 Authentication models (one-liners)
- **Local users** = custodian-native accounts stored on the system itself.
- **Federation / SAML** = corporate SSO; log in once at the company IdP, access the cloud.
- **Directory-based (AD / Entra / LDAP)** = identities synced from a central directory.
- **Token-based (JWT / API token)** = present a token per request instead of re-logging in.
- **MFA** = 2+ independent factors (something you know + have + are).
- **OpenID Connect (OIDC)** = authentication (WHO) that returns a JWT ID token.

### 7.3 Authorization models
- **RBAC** = role-based — permissions follow the role, users get the role.
- **Group-based** = membership-based — being in a group grants the group's rights.
- **OAuth 2.0** = delegated — user lets an app act on their behalf (WHAT).
- **Discretionary** = owner-set ACLs — the resource owner decides who gets in.

### 7.4 SAML vs OIDC vs OAuth 2.0
- **SAML / SSO** = federation, **WHO** (XML assertion of identity).
- **OIDC** = authentication, **WHO** (JWT ID token).
- **OAuth 2.0** = authorization, **WHAT** (delegated access token).
- Quick rule: **SAML/OIDC prove who you are; OAuth 2.0 grants what you can do.**

### 7.5 Bastion rule
- **Private box = bastion + key, never public IP.**
- Private resources stay in private subnets; reach them through a hardened jump host over SSH with key pairs. RDP uses NLA and is not exposed publicly.

### 7.6 MFA
- **MFA defeats stolen passwords** — even if the password leaks, the attacker lacks the second factor (phone, token, biometric).

### 7.7 Decision tree (ASCII)

```
WHAT DO YOU NEED TO DO?
│
├─ Prove who a user is?  ──────────────► AUTHENTICATION
│     │
│     ├─ Use existing corporate login? ─► Federation / SAML (SSO) + MFA at IdP
│     ├─ Return a JWT "who" token? ─────► OIDC (auth, WHO)
│     ├─ Just this system's account? ──► Local user
│     ├─ From AD/Entra/LDAP? ──────────► Directory-based
│     ├─ Per-request stateless check? ─► Token-based (JWT / API token)
│     └─ Block stolen passwords? ──────► MFA (add a 2nd factor)
│
├─ Decide what they can access?  ──────► AUTHORIZATION
│     │
│     ├─ By job function? ─────────────► RBAC (+ group-based)
│     ├─ By team membership? ──────────► Group-based
│     ├─ Let an app act for the user? ─► OAuth 2.0 (delegated, WHAT)
│     └─ Owner picks who? ─────────────► Discretionary (ACL)
│
├─ Connect to a private server?  ─────► BASTION HOST + SSH key (no public IP)
│
└─ Track who did what?  ──────────────► ACCOUNTING / AUDIT TRAIL
```

---

## SECTION 8 — CLOUD PROVIDER MAPPING

Exam tip: the concepts are provider-agnostic — CompTIA tests the *concept*, not the product name. Know what each provider calls the same idea.

| IAM Concept | AWS | Microsoft Azure | Google Cloud (GCP) |
|---|---|---|---|
| **Identity service** | IAM (users, roles, policies) | Microsoft Entra ID (formerly Azure AD) | Cloud Identity |
| **Federation / SSO** | IAM Identity Center + SAML 2.0 | Entra ID Federation (SAML / OIDC) | Cloud Identity (SAML / OIDC) |
| **MFA** | IAM MFA (virtual / hardware / SMS) | Entra ID Multi-Factor Authentication | Cloud Identity / Google MFA |
| **Bastion / jump host** | EC2 bastion host (public subnet) | Azure Bastion (PaaS, no public IP on VM) | Identity-Aware Proxy (IAP) or GCE bastion |
| **RBAC / groups** | IAM roles + groups + policies | Azure RBAC roles + Entra groups | IAM roles + groups (Cloud IAM) |
| **Directory integration** | AWS Directory Service / AD Connector | Entra Connect (sync on-prem AD) | Managed Microsoft AD / LDAP via Cloud Identity |
| **Programmatic access** | IAM roles for services + access keys | Service principals / managed identities | Service accounts (IAM) |
| **Audit trail (accounting)** | AWS CloudTrail | Azure Activity Log / Entra sign-in logs | Cloud Audit Logs (Admin Activity) |

**Cross-provider reminders:**
- **AWS** leans on *roles* and *policies*; prefer roles over static access keys for programmatic access.
- **Azure** ties everything to **Entra ID** and uses *managed identities* so VMs/services never store secrets.
- **GCP** uses **service accounts** as the non-human identity and **IAP** as the bastion-like zero-trust front door.
- All three support **SAML/OIDC federation**, **MFA**, **RBAC + groups**, and a central **audit log** — the exam expects you to map the concept, not memorize the logo.

## SECTION 9 — INTERVIEW KNOWLEDGE

This section frames the CV0-004 Objective 4.3 IAM concepts the way they surface in technical interviews and oral exam-style questioning. Each item pairs a likely question with the crisp, defensible answer an exam candidate should be able to deliver.

### 9.1 Core Conceptual Questions

**Q: What is the difference between authentication and authorization?**
Authentication (AuthN) proves *who* you are — it validates identity against a trusted store using credentials, certificates, or biometrics. Authorization (AuthZ) determines *what* you are allowed to do — it evaluates policies and permissions against the authenticated identity. AuthN always precedes AuthZ. A common memory hook: "AuthN = login, AuthZ = access."

**Q: Explain the principle of least privilege.**
Least privilege means every identity (user, service, or workload) is granted only the minimum permissions required to perform its function, and nothing more. It limits blast radius when credentials are compromised and reduces the attack surface. In cloud IAM, this is operationalized through scoped roles, just-in-time (JIT) access, and permission boundaries.

**Q: What is the difference between RBAC, ABAC, and PBAC?**
- **RBAC (Role-Based Access Control):** access decisions are based on roles assigned to users. Simple, scalable, and easy to audit, but can lead to "role explosion" in complex environments.
- **ABAC (Attribute-Based Access Control):** access decisions use attributes (user department, resource tags, time of day, IP). More granular and dynamic than RBAC but harder to reason about and audit.
- **PBAC (Policy-Based Access Control):** centralizes decisions in policies evaluated by a policy engine, often combining role and attribute logic.

**Q: What is federation, and why do enterprises use it?**
Federation establishes trust between an identity provider (IdP) and one or more service providers (SPs) so users authenticate once with their home organization and access external resources without separate credentials. Enterprises use it to centralize identity lifecycle, enforce consistent policy, reduce password sprawl, and enable seamless cloud adoption.

### 9.2 Scenario-Style Questions

**Q: A developer's leaked access key was found in a public Git repo. Walk me through your response.**
1. Immediately deactivate/revoke the key (do not delete first — you may need it for forensics).
2. Rotate to a new key and update dependent systems.
3. Review CloudTrail/audit logs for anomalous API calls made with the key.
4. Assess blast radius via the key's attached policies.
5. Remediate: replace long-lived keys with short-lived tokens or workload identity.
6. Add secret-scanning to CI/CD and pre-commit hooks to prevent recurrence.

**Q: How would you give a CI/CD pipeline access to deploy without embedding static credentials?**
Use workload identity federation / OIDC-based trust. The pipeline presents a signed OIDC token to the cloud provider, which exchanges it for short-lived, scoped credentials via an assumed role. No long-lived secrets are stored.

**Q: Users complain SSO works for some apps but not others. Where do you look?**
Check the SAML/OIDC assertion configuration: matching entity IDs, correct ACS/redirect URIs, valid signing certificates (not expired), attribute/claim mapping, and clock skew between IdP and SP. Also verify the user is provisioned (SCIM) in the failing SP.

### 9.3 "Explain It Simply" Prompts

- **MFA:** "Something you know, something you have, something you are — combining at least two makes stolen passwords useless on their own."
- **Zero Trust:** "Never trust, always verify. Every request is authenticated and authorized regardless of network location; there is no trusted internal network."
- **Privileged Access Management (PAM):** "A vault and gatekeeper for admin accounts — checks out credentials just-in-time, records the session, and rotates the secret afterward."

---
---

## SECTION 10 — FLASHCARDS

**Auth vs Authz**
Q: Prove who you are?
A: Authentication (AuthN).

Q: What you're allowed to do?
A: Authorization (AuthZ).

Q: AuthN vs AuthZ order?
A: AuthN always precedes AuthZ.

**Authentication models**
Q: Native cloud identity (no SSO)?
A: Local users.

Q: Corporate login for cloud (SSO)?
A: Federation (SAML).

Q: On-prem AD users in cloud?
A: Directory-based (AD/Entra/LDAP).

Q: Per-request API auth (JWT)?
A: Token-based.

Q: Two+ factors (password + device)?
A: MFA.

Q: "Who is the user" via JWT for apps?
A: OpenID Connect (OIDC).

**Authorization models**
Q: Access by assigned role?
A: RBAC.

Q: Access by group membership?
A: Group-based access control.

Q: Delegated access to user's resources?
A: OAuth 2.0.

Q: Owner-set permissions (ACL)?
A: Discretionary access control.

**Access methods**
Q: Encrypted Linux terminal?
A: SSH (key-based).

Q: Graphical Windows remote desktop?
A: RDP (NLA, not public).

Q: Hardened jump box to private instances?
A: Bastion host.

Q: Programmatic resource access?
A: API.

Q: Automated cloud admin (script)?
A: CLI / SDK with service account.

Q: Console GUI?
A: Web portal.

**Protocols / Providers**
Q: SAML vs OIDC vs OAuth2?
A: SAML/SSO = federation (who); OIDC = auth (who, JWT); OAuth2 = authz (what, delegated).

Q: AWS IAM / Azure / GCP identity?
A: IAM / Entra ID / Cloud IAM.

Q: Recording actions = ?
A: Accounting (audit trail).

---

## SECTION 11 — PRACTICE QUESTIONS

### EASY (5)

**E1.** Proving your identity at login is:
A) Authorization  B) Authentication  C) Accounting  D) Federation
**Answer: B) Authentication.**
*Why others wrong:* Authorization is what you can do; accounting is logging; federation is SSO.

**E2.** Granting a user permission to delete a bucket is:
A) Authentication  B) Authorization  C) Federation  D) Token-based
**Answer: B) Authorization.**
*Why others wrong:* AuthN is who; the others are unrelated models.

**E3.** Letting staff log in with corporate Active Directory credentials is:
A) Local users  B) Federation (SAML)  C) Token-based  D) Discretionary
**Answer: B) Federation (SAML).**
*Why others wrong:* Local = native; token = JWT; discretionary = owner ACL.

**E4.** Accessing a private Linux instance securely uses:
A) Public IP + password SSH  B) Bastion host + key pair  C) Open RDP  D) Web portal only
**Answer: B) Bastion host + key pair.**
*Why others wrong:* Public IP/password and open RDP are insecure exposures.

**E5.** AWS programmatic access for a script should use:
A) Your personal admin password  B) IAM role / service account (least privilege)  C) Root credentials  D) Hardcoded user password
**Answer: B) IAM role / service account.**
*Why others wrong:* Personal/root/hardcoded creds violate least privilege + secrets hygiene.

### MEDIUM (10)

**M1.** "Log in with Google" for your app tells you the user's identity. Which protocol?
A) OAuth 2.0  B) OpenID Connect (OIDC)  C) SAML  D) RBAC
**Answer: B) OpenID Connect (OIDC).**
*Why others wrong:* OAuth2 is delegated authz; SAML is enterprise federation; RBAC is authz model.

**M2.** Your app needs to post to a user's cloud drive on their behalf. Which?
A) OIDC  B) OAuth 2.0 (delegated authorization)  C) SAML  D) Local users
**Answer: B) OAuth 2.0.**
*Why others wrong:* OIDC is auth (who); SAML is SSO; local is native identity.

**M3.** A private database needs occasional admin access. Best design:
A) Public IP on the DB + password  B) Bastion host, then SSH/RDP to private DB  C) Open RDP to internet  D) No access
**Answer: B) Bastion host.**
*Why others wrong:* Public IP / open RDP expose the DB; bastion keeps it private + audited.

**M4.** To limit blast radius, permissions should be:
A) Broad admin for all  B) Role/group-based least privilege (RBAC)  C) Discretionary per user  D) None
**Answer: B) RBAC + least privilege.**
*Why others wrong:* Broad admin = huge blast radius; discretionary doesn't scale; none = no work.

**M5.** On-prem AD users must access cloud resources with their existing accounts:
A) Create separate local users  B) Directory-based integration / federation  C) Token-only  D) Disable AD
**Answer: B) Directory-based integration / federation.**
*Why others wrong:* Separate local users defeats SSO; others don't sync AD.

**M6.** API calls authenticated on every request via a short-lived JWT are:
A) Local users  B) Token-based  C) Federation  D) MFA
**Answer: B) Token-based.**
*Why others wrong:* Token = per-request stateless auth; others are different models.

**M7.** Best defense against stolen passwords:
A) Longer password  B) MFA  C) More local users  D) Open RDP
**Answer: B) MFA.**
*Why others wrong:* MFA adds a factor a thief lacks; password length alone isn't enough.

**M8.** Recording every API call for forensics/compliance is:
A) Authentication  B) Accounting (audit trail)  C) Authorization  D) Federation
**Answer: B) Accounting (audit trail).**
*Why others wrong:* It's the "what happened" log, not who/what.

**M9.** Owner can set who accesses their own file (ACL). This is:
A) RBAC  B) Group-based  C) Discretionary (DAC)  D) OAuth 2.0
**Answer: C) Discretionary (DAC).**
*Why others wrong:* RBAC/group are admin-assigned; OAuth2 is delegated.

**M10.** After offboarding an employee, you should:
A) Leave their access  B) Revoke/deactivate (federation removes centrally)  C) Share their key  D) Ignore
**Answer: B) Revoke/deactivate.**
*Why others wrong:* Lingering access = security hole (ties to 3.4 decommission).

### HARD (5)

**H1.** Enterprise: 2000 employees, mixed cloud + on-prem, must enforce MFA and central offboarding. Design:
A) Local users per cloud  B) Directory-based federation (SAML/SSO) + MFA at IdP + central offboarding + CloudTrail/audit  C) Token-only  D) Public RDP
**Answer: B).**
*Why others wrong:* Local users don't centralize; token-only skips SSO; public RDP is insecure.

**H2.** CI/CD must deploy without static secrets in code. Approach:
A) Hardcode admin key  B) OIDC workload-identity federation → short-lived scoped role creds  C) Personal password  D) Root
**Answer: B).**
*Why others wrong:* Static/hardcoded/root creds violate secrets hygiene + least privilege.

**H3.** A private instance is breached; investigators need to know what the attacker did. Which control matters most?
A) MFA  B) Audit trail (accounting)  C) Federation  D) Bastion
**Answer: B) Audit trail.**
*Why others wrong:* MFA prevents; audit *records* post-facto (accounting) for forensics.

**H4.** Distinguish SAML, OIDC, OAuth2 correctly:
A) All three are the same  B) SAML = enterprise SSO federation (who); OIDC = auth (who, JWT ID token); OAuth2 = delegated authorization (what)  C) OAuth2 = auth  D) SAML = authz
**Answer: B).**
*Why others wrong:* Confusing auth vs authz across the three is the classic trap.

**H5.** Most complete secure-access design for a cloud admin team:
A) Public IP + password  B) Bastion + SSH keys + federation/SSO + MFA + RBAC/group least privilege + audit trail  C) Local users, no MFA  D) Open RDP
**Answer: B).**
*Why others wrong:* Others miss MFA, least privilege, audit, or expose access.

---

## SECTION 12 — EXAM PRIORITY

| Concept | Priority | Why |
|---|---|---|
| Authentication vs Authorization | 🔴 CRITICAL | Core split; constant distractor |
| SAML vs OIDC vs OAuth2 | 🔴 CRITICAL | Who/what confusion |
| Bastion for private access | 🔴 CRITICAL | Never public IP on private resource |
| Service accounts / roles for programmatic | 🔴 CRITICAL | No hardcoded/user creds |
| RBAC / Group-based (least privilege) | 🟠 HIGH | Limit blast radius |
| MFA | 🟠 HIGH | Defeats stolen passwords |
| Federation (SAML/directory) | 🟠 HIGH | Enterprise SSO + central offboarding |
| Token-based (API/JWT) | 🟡 MEDIUM | Stateless per-request auth |
| Audit trail (accounting) | 🟡 MEDIUM | Forensics + compliance (4.2) |
| SSH keys / RDP NLA | 🟡 MEDIUM | Secure remote access |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

**The AAA model (IAM)**
- **Authentication (WHO):** prove identity — local, federation/SAML, directory, token/JWT, MFA, OIDC.
- **Authorization (WHAT):** permissions — RBAC, group-based, OAuth 2.0, discretionary (DAC).
- **Accounting:** audit trail of actions (CloudTrail / Activity Log / Cloud Audit Logs).

**Authentication models**
- **Local:** native IAM users. · **Federation (SAML):** corporate SSO. · **Directory:** AD/Entra/LDAP sync. · **Token-based:** JWT/API token per request. · **MFA:** 2+ factors. · **OIDC:** auth (who) via JWT ID token.

**Authorization models**
- **RBAC:** by role. · **Group-based:** by membership. · **OAuth 2.0:** delegated access (app acts for user). · **Discretionary:** owner-set ACL.

**Secure access**
- **Programmatic:** API / SDK / CLI / web portal → use service accounts / roles (least privilege), secrets manager.
- **SSH:** key-based, no password, via bastion. · **RDP:** NLA, via bastion, never public.
- **Bastion host:** hardened jump box to private instances (no public IPs on private resources).

**Protocol cheat:** SAML/SSO = federation = WHO (enterprise). OIDC = auth = WHO (JWT). OAuth2 = authz = WHAT (delegated).

**Acronyms**
- IAM = Identity and Access Management · SSO = Single Sign-On · AuthN = Authentication · AuthZ = Authorization
- SAML = Security Assertion Markup Language · OIDC = OpenID Connect · OAuth 2.0 = Open Authorization
- RBAC = Role-Based Access Control · DAC = Discretionary Access Control · MFA = Multifactor Authentication
- JWT = JSON Web Token · SSH = Secure Shell · RDP = Remote Desktop Protocol · NLA = Network Level Authentication
- IdP = Identity Provider · AD = Active Directory · API = Application Programming Interface · SDK = Software Development Kit · CLI = Command-Line Interface · ACL = Access Control List · CI/CD = Continuous Integration/Deployment

**Traps recap**
1. AuthN (who) ≠ AuthZ (what). 2. SAML = federation/SSO (who); OIDC = auth (who, JWT); OAuth2 = authz (what). 3. Private resource → bastion, not public IP. 4. Programmatic → service account/role, not user creds in code. 5. SSH keys, not passwords. 6. RBAC/group limits blast radius; discretionary doesn't scale. 7. Directory integration for AD users. 8. Token-based for APIs. 9. Audit trail = accounting. 10. Least privilege always. 11. Federation removes separate cloud password. 12. MFA at IdP with federation. 13. Open RDP/SSH to internet = bad. 14. Offboarding → revoke access centrally. 15. OIDC (auth) vs OAuth2 (authz) — don't swap.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024–2025, CV0-004-relevant)

- **Phishing-resistant MFA:** FIDO2 / Passkeys and hardware keys (WebAuthn) are now the standard — push-notification MFA is being retired where possible because it's phishable. MFA is non-negotiable (4.3).
- **Passwordless / federation everywhere:** OIDC-based SSO (Entra ID, Google Workspace) plus workload-identity federation lets CI/CD and apps authenticate with short-lived tokens — no static keys (ties to 2.4 secrets hygiene).
- **Zero Trust:** "never trust, always verify" — every request authenticated + authorized regardless of network; IAM is the enforcement plane, not the network perimeter.
- **Least-privilege automation (CIEM):** Cloud Infrastructure Entitlement Management tools continuously find over-permissioned roles and recommend RBAC tightening — least privilege as a monitored control, not a one-time setting.
- **Short-lived credentials over static keys:** IAM roles / workload identity / OIDC token exchange replace long-lived access keys; secret scanning in CI/CD catches leaks (the leaked-key scenario).
- **Centralized audit (SIEM):** CloudTrail / Activity Log / Cloud Audit Logs stream to a SIEM for real-time detection (feeds 4.6 suspicious-activity monitoring) — accounting as live security telemetry.
- **Bastion evolution:** Managed bastion services (Azure Bastion, AWS EC2 Instance Connect, GCP IAP) replace hand-built jump hosts — audited, broker-based access to private instances without public IPs.
- **IaC IAM scanning:** Policy-as-code (in 2.4) now flags over-permissive IAM in pull requests before deploy — IAM correctness shifts left.

*End of Objective 4.3 notes.*
