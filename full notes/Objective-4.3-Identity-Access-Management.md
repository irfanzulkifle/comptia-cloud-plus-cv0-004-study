## SECTION 1 — Exam Objectives

**CompTIA Cloud+ CV0-004 — Objective 4.3: Given a scenario, implement identity and access management (IAM).** This objective sits inside Domain 4 (Security) and tests your ability to control who can enter a cloud environment, how they prove who they are, what they are allowed to do, and how you keep a record of their actions.

- Identity and access are two joined problems: identity answers *who are you?* (authentication); access answers *what are you allowed to touch?* (authorization).
- IAM is the front door, the bouncer, the rulebook, and the CCTV of the cloud — a beginner touches it every console login, CLI command, or app-to-cloud call.
- The exam blueprint breaks 4.3 into five ordered branches you must memorize: (1) secure access to the management environment, (2) secure access to cloud resources, (3) authorization models, (4) authentication models, (5) accounting.
- Keep the two lanes separate: managing the cloud (users, servers, policies) is the *management plane*; logging into a running workload is the *resource plane*. Scenario questions ask you to match a job role to the right combination of authentication + authorization + accounting.

## SECTION 2 — EXAM KNOWLEDGE

### Secure access to the cloud management environment

- **Programmatic access (API and SDK)** — software, not a human, calls the cloud; the API is the provider's web endpoints (HTTPS request in, JSON out) and the SDK wraps those calls into friendly language functions. Identified by access/secret keys or temporary tokens, never by username-and-password; risk is leaking secrets into public code, mitigated by least-privilege, rotation, and temporary role credentials.
- **Command-line interface (CLI)** — a text tool (e.g., AWS CLI, Azure CLI) that administers the cloud via commands like `aws s3 ls`; it is management-plane access. Authenticates with the same programmatic credentials; scriptable and favored for CI/CD, contrasted with the graphical web portal. Secure it with protected config files, no echoed secrets, and SSO federation over static keys.
- **Web portal** — the browser-based management console logged into with username + password + MFA; the most beginner-accessible, GUI path for management tasks. Always protect with MFA, strong password policy, and conditional access; do not confuse portal (management-plane) login with logging into a VM (resource-plane).
- **API vs CLI vs portal distinction** — all three are management-plane tools for administering the cloud, but CLI is scriptable/automatable, the portal is click-through for quick checks, and programmatic access is how applications and scripts call the cloud rather than a human clicking.

### Secure access to cloud resources

- **API access to resources** — applications call APIs to *use* the workloads they run (mobile app to backend, microservice to microservice); protect with tokens, API gateways, rate limiting, and TLS. Exam contrast: management APIs control the cloud account, resource APIs are the apps you built on top of it.
- **SSH (Secure Shell)** — encrypted protocol for a command-line shell on Linux/Unix; authenticate with an SSH key pair (private secret + public on server), not password. Key-based beats password, private keys get passphrases, and port 22 is restricted to a bastion or VPN — never open to the whole internet.
- **RDP (Remote Desktop Protocol)** — Microsoft's protocol for a full graphical desktop on Windows; uses password auth, ideally behind a gateway/VPN or with Network Level Authentication. Resource-plane remote access that must never be exposed directly to the public internet (port 3389) — place behind bastion, VPN, or session broker.
- **Bastion host** — a single, hardened, tightly monitored jump box that is the *only* machine accepting inbound SSH/RDP from the internet; admins connect to it first, then reach private servers with no public IP. Shrinks attack surface to one box, centralizes logging, and keeps real servers invisible; must itself be patched, MFA-protected, and access-listed.

### Authorization models

- **Role-based access control (RBAC)** — permissions are granted to roles (job functions like "Database Admin" or "Read-Only Auditor") and users are assigned to roles; define once and add/remove people without rewriting permissions. Embodies least privilege and is the default, most-tested cloud IAM mental model.
- **Group-based access control** — users are bundled into groups (e.g., "Finance," "Engineering") and permissions attach to the group, so a new hire inherits access automatically. A group is a collection of *users*; a role is a collection of *permissions* often assumable by users, services, or other accounts.
- **OAuth 2.0** — an *authorization* framework (not authentication by itself) that lets one app access another on a user's behalf via a limited, revocable access token, without sharing the user's password ("Log in with Google"). Appears as an authorization model and as the token mechanism behind OIDC; answers "what is this app allowed to do for me?"
- **Discretionary access control (DAC)** — the resource *owner* decides who gets access (e.g., sharing your own storage object or file); "discretionary" because control is left to the owner, unlike mandatory access control. Tell-tale exam sign: "the resource owner sets the permissions."

### Authentication models

- **Local users** — identities created and stored directly in the cloud account or OS (local username/password or local server account); simple but does not scale and creates password burden. Best practice: minimize standing local accounts and prefer federation or directory-based login, keeping only minimum break-glass accounts.
- **Federation (SAML)** — uses an external identity provider (e.g., Microsoft Entra ID, Google Workspace) so users keep one credential set across systems; SAML carries a signed XML assertion proving the user authenticated at the IdP. The enterprise browser SSO protocol that avoids duplicate cloud-only passwords.
- **Token-based authentication** — issues a token (often a JWT) after login that is presented on later requests instead of a password; can be short- or long-lived. Underpins stateless API and mobile auth and delivers OAuth 2.0 access tokens and OIDC ID tokens; secure pattern is short expiry + refresh tokens + HTTPS.
- **Directory-based authentication** — a central directory (LDAP/Active Directory or a cloud directory) holds identities and policies that apps and systems trust to authenticate and get group/role info. Hybrid-cloud example: a cloud workload consults on-prem AD so employees use their corporate login; one authoritative store, but must be highly available and secure.

### Multi-factor authentication (MFA) and OpenID Connect (OIDC)

- **Multifactor authentication (MFA)** — requires two or more independent factors: something you know (password), have (phone/hardware token), or are (biometrics). One of the highest-value controls; enabling MFA on the management console and privileged accounts is a near-automatic right answer. Prefer app- or hardware-based MFA over SMS (SIM-swap risk).
- **OpenID Connect (OIDC)** — an *authentication* layer on top of OAuth 2.0 that returns an ID token answering "who is this user?" SAML and OIDC both do federation/SSO, but SAML is older XML enterprise SSO while OIDC is the modern JSON/REST approach favored by mobile and web apps.
- **OAuth vs OIDC distinction** — OAuth 2.0 answers "what can this app do" (authorization); OIDC answers "who is this user" (authentication) and rides on OAuth's token machinery. Typical scenario: "log into our web app with Google/Microsoft and know who they are" is OIDC.
- **Token delivery for both** — token-based auth is the common delivery mechanism: OAuth 2.0 access tokens grant scoped delegated access, OIDC ID tokens confirm identity, and both are presented instead of a password on each request over HTTPS.

### Accounting — Audit trail

- **Audit trail** — the "A" in the AAA triad; an immutable, time-stamped log of who did what, when, and from where (console logins, API calls, resource changes, policy edits). Every access decision should be logged and logs protected from tampering (write-once or centralized).
- **Purpose** — audit trails support forensics, compliance, and misuse detection; if a question describes an unexplained change or investigation, the audit trail is the artifact you turn to. The takeaway: IAM is not finished at login + authorization — you must *record* actions to prove what happened.
- **Enable and retain** — a recurring right answer is to enable and retain CloudTrail / activity logs / audit logs for all management and data-plane actions so you can reconstruct any event later.
- **Beginner perspective** — for a beginner the critical point is simple: recording actions after authentication and authorization is what closes the loop and makes IAM accountable.

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **University cloud lab with SAML single sign-on** — a Malaysian public university gives students and lecturers one Microsoft Entra ID (Azure AD) account for email and the student portal; SAML federation lets the same login open the cloud console. No separate cloud password to forget, and graduating disables the Entra ID account, automatically revoking cloud access (federation + SAML, reducing password sprawl and supporting off-boarding).
- **E-commerce company locks down server access with a bastion host** — a Kuala Lumpur online shop runs database and app servers with private IPs only; admins cannot reach them directly, so they SSH into a single hardened bastion (MFA-protected, IP-restricted, fully logged) and jump to internal servers. Shrinks the public attack surface to one monitored box and keeps real servers invisible (bastion host pattern + SSH hardening).
- **Startup automates nightly backups with programmatic access (SDK + least privilege)** — a small team writes a Python script using the cloud SDK to snapshot storage buckets at midnight; instead of a long-lived admin key, the script assumes a narrowly scoped role that can only create snapshots. Programmatic access + RBAC least privilege; if the key leaks, the blast radius is tiny.
- **Mobile banking app uses OAuth 2.0 / OIDC for "log in with bank"** — a fintech app authenticates customers via OIDC (proving who they are) and authorizes the app with OAuth 2.0 scoped tokens (limiting what it can do). The banking password is never shared with the third-party app and each token expires quickly (token-based auth + OIDC authentication + OAuth 2.0 authorization together).
- **Government agency enforces MFA and centralizes the audit trail for compliance** — a statutory body requires MFA on every privileged console login and RDP session, and streams all activity into a tamper-resistant audit log retained for years. When an unauthorized change is later found, the audit trail shows exactly which identity, from which IP, at what time — illustrating MFA (authentication) plus audit trail (accounting).

## SECTION 4 — COMPARISON TABLES

### Table 1 — Federation & Token Protocols: SAML vs OAuth 2.0 vs OpenID Connect

| Feature | SAML 2.0 | OAuth 2.0 | OpenID Connect (OIDC) |
|---|---|---|---|
| Primary purpose | Authentication (SSO) + carries attributes | Authorization (delegated access) | Authentication (identity) on top of OAuth 2.0 |
| Format | XML assertions, signed | Token-based (access token), often JSON | JSON Web Token (ID token) + OAuth tokens |
| Typical use | Enterprise browser SSO to console/apps | "Log in with Google" delegated API access | Modern web/mobile login knowing who the user is |
| Answers the question | "This user is who they claim, per the IdP" | "What is this app allowed to do for me?" | "Who is this user?" |
| Cloud+ placement | Authentication model (Federation) | Authorization model + token delivery | Authentication model |

### Table 2 — Authorization Models: RBAC vs Group-based vs DAC vs OAuth 2.0

| Model | What it grants to | Best for | Tell-tale sign on exam |
|---|---|---|---|
| RBAC (Role-based) | Permissions bundled into roles; users assigned roles | Scaling least privilege by job function | "Give the intern the read-only role" |
| Group-based | Permissions assigned to groups of users | Bundling identities for convenience | "Add the new hire to the Engineering group" |
| DAC (Discretionary) | Resource owner decides access | User-owned sharing of objects/files | "The owner can share their own resource" |
| OAuth 2.0 | Scoped tokens to an app, on a user's behalf | Delegating limited access to third-party apps | "App needs limited access without my password" |

### Table 3 — Authentication Models Comparison

| Model | What proves identity | Key exam note |
|---|---|---|
| Local users | Username + password stored in the system | Simple but doesn't scale; minimize standing accounts |
| Federation (SAML) | Signed assertion from external IdP | One login across systems; enterprise SSO |
| Token-based | Presented token (JWT) instead of password | Stateless API/mobile auth; short-lived preferred |
| Directory-based | Central directory (AD/LDAP) check | One authoritative identity store; hybrid cloud |
| MFA | Two+ factors (know/have/are) | Highest-value control; enable on privileged access |
| OpenID Connect | OIDC ID token on top of OAuth 2.0 | Proves *who*; modern JSON SSO |

### Table 4 — Remote Access Methods: SSH vs RDP vs Bastion

| Method | Protocol / port | OS typical | Best practice |
|---|---|---|---|
| SSH | Secure Shell, port 22 | Linux/Unix | Key-based auth + passphrase; restrict to bastion/VPN |
| RDP | Remote Desktop, port 3389 | Windows | NLA + gateway/VPN; never expose publicly |
| Bastion host | Jump box for SSH/RDP | Any | Single hardened, logged entry point to private servers |

### Table 5 — Management-plane vs Resource-plane Access

| Plane | Tools | Purpose | Example |
|---|---|---|---|
| Management environment | API, SDK, CLI, Web portal | Administer the cloud (users, servers, policies) | Log into console, run `aws` commands |
| Resource access | API, SSH, RDP, Bastion | Reach the workloads you run | SSH into a Linux VM, RDP into Windows VM |

### Table 6 — The AAA Triad Applied to Objective 4.3

| AAA element | 4.3 coverage | Example control |
|---|---|---|
| Authentication | Local users, Federation/SAML, Token-based, Directory-based, MFA, OIDC | MFA on console login |
| Authorization | RBAC, Group-based, OAuth 2.0, Discretionary | Assign read-only role |
| Accounting | Audit trail | Centralized, tamper-resistant activity logs |

## SECTION 5 — AWS PROVIDER MAPPING

This section maps every 4.3 concept to its AWS-specific service or feature. AWS is used here as the concrete provider example; the objective itself is provider-neutral, but mapping to AWS helps you recognize the same ideas in real labs and on scenario questions.

| 4.3 Concept | AWS Service / Feature | What it does |
|---|---|---|
| IAM (identity & access management core) | **AWS IAM** | Central service for users, roles, groups, and policies controlling authentication and authorization |
| Programmatic access (API / SDK) | **SigV4 signing / AWS SDK** | Requests are signed with Signature Version 4 using access key + secret; SDKs handle signing automatically |
| CLI | **AWS CLI** | Text tool that calls AWS APIs using configured credentials/profiles; management-plane access |
| Web portal | **AWS Management Console** | Browser GUI; protected by password + MFA; management-plane access |
| SSH to resources | **AWS SSM Session Manager / EC2** | Preferred agent-based shell without opening port 22; or key-based SSH to EC2 with restricted security groups |
| RDP to resources | **AWS SSM Session Manager / EC2** | Managed RDP/port-forward sessions to Windows EC2 without public exposure; or RDP behind a bastion |
| Bastion host | **Bastion / jump host on EC2** | Single hardened, public-facing instance for controlled SSH/RDP into private subnets |
| Authorization — RBAC | **IAM policies / roles** | JSON policies attached to roles grant least-privilege permissions by job function |
| Authorization — Group-based | **IAM groups** | Collections of IAM users; permissions attached to the group |
| OAuth 2.0 / OIDC | **Amazon Cognito** | User pools and identity pools provide OAuth 2.0 authorization and OIDC authentication for apps |
| Federation — SAML | **IAM SAML identity provider (IdP)** | Trusts external SAML IdP (e.g., Entra ID) for SSO into AWS; maps assertions to IAM roles |
| MFA | **IAM MFA** | Virtual, U2F, or hardware MFA devices bound to IAM users/roles |
| Accounting — Audit trail | **AWS CloudTrail** | Records all management (and optional data) events: who did what, when, from where |

**Quick narrative for revision:** A user logs into the **AWS Console** (web portal) or runs the **AWS CLI** (CLI), both authenticated via **IAM** and **MFA**. Applications use the **AWS SDK** with **SigV4**-signed requests (programmatic access). Admins reach private servers through **SSM Session Manager** or a **bastion host** (SSH/RDP) instead of exposing ports. Permissions follow least privilege via **IAM policies/roles** and **IAM groups** (RBAC / group-based). App sign-in uses **Cognito** for **OAuth 2.0 / OIDC**, and enterprise SSO uses an **IAM SAML IdP**. Every action is recorded by **CloudTrail** (audit trail / accounting).

## SECTION 6 — PRACTICE QUESTIONS

1. Which access method lets software call the cloud without a human clicking buttons?
   A. Web portal
   B. Programmatic access via API/SDK
   C. RDP
   D. Bastion host
   Answer: B

2. A developer needs to run a nightly script that creates storage snapshots. What is the most secure credential approach?
   A. Share the root account password
   B. Long-lived admin key on the laptop
   C. A narrowly scoped role assumed by the script
   D. Disable logging to speed it up
   Answer: C

3. Which tool is the browser-based graphical way to manage the cloud?
   A. CLI
   B. SDK
   C. Web portal
   D. Bastion host
   Answer: C

4. You must give a new intern read-only access to resources. Which model fits best?
   A. DAC
   B. RBAC with a read-only role
   C. Local user only
   D. OpenID Connect
   Answer: B

5. Staff already log into Microsoft Entra ID. You want them to use that same login for the cloud console. Which protocol enables this SSO?
   A. OAuth 2.0
   B. SAML
   C. RDP
   D. SSH
   Answer: B

6. "Log in with Google" so an app can access your profile without your password. Which framework grants this scoped access?
   A. SAML
   B. OAuth 2.0
   C. RDP
   D. DAC
   Answer: B

7. OIDC is best described as:
   A. Authorization only
   B. An authentication layer on top of OAuth 2.0
   C. A bastion host protocol
   D. A directory service
   Answer: B

8. A server has no public IP and admins reach internal machines through one hardened, logged entry point. This is a:
   A. Web portal
   B. Bastion host
   C. SAML IdP
   D. Directory
   Answer: B

9. For Linux server remote shell access, the recommended authentication is:
   A. Password-only SSH open to the internet
   B. Key-based SSH with restricted access
   C. RDP
   D. Web portal
   Answer: B

10. For Windows graphical remote desktop, which protocol is used?
   A. SSH
   B. RDP
   C. SAML
   D. OAuth 2.0
   Answer: B

11. Requiring a password plus a phone-based code is an example of:
   A. Federation
   B. MFA
   C. DAC
   D. RBAC
   Answer: B

12. A resource owner decides who may access their own files. This is:
   A. RBAC
   B. DAC
   C. OAuth 2.0
   D. Group-based
   Answer: B

13. Centralizing user identities in Active Directory that apps consult is:
   A. Token-based
   B. Directory-based
   C. Local users
   D. Bastion host
   Answer: B

14. Permissions assigned to a collection of users so a new hire inherits them automatically describe:
   A. Group-based access control
   B. DAC
   C. SAML
   D. MFA
   Answer: A

15. After login, an app presents a short-lived JSON token instead of a password on each request. This is:
   A. Directory-based
   B. Token-based
   C. Local user
   D. Bastion host
   Answer: B

16. The "A" in AAA that records who did what and when is:
   A. Authentication
   B. Authorization
   C. Accounting / audit trail
   D. Aggregation
   Answer: C

17. Which service records AWS management events for forensics and compliance?
   A. CloudTrail
   B. IAM groups
   C. Cognito
   D. SSM
   Answer: A

18. In AWS, fine-grained permissions by job function are delivered through:
   A. IAM policies/roles
   B. Web portal
   C. RDP
   D. SDK only
   Answer: A

19. App sign-in with social identity that proves who the user is, on top of OAuth, uses:
   A. SAML
   B. Cognito with OIDC
   C. Bastion host
   D. Local users
   Answer: B

20. Reaching a private Linux VM without opening port 22 to the internet is best done with:
   A. Public RDP
   B. AWS SSM Session Manager
   C. Web portal
   D. SAML
   Answer: B
