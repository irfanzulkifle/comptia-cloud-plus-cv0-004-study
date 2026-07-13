## SECTION 1 — Exam Objectives

- **Objective 5.2 — Explain concepts related to continuous integration/continuous deployment (CI/CD) pipelines.**
- A CI/CD pipeline automates the path from code change to running software: integrate → build → test → scan → deploy.
- CompTIA tests the *vocabulary* of pipeline stages and the artifacts they produce. Expect "which stage does X" and "what artifact is Y" questions.

**The eleven sub-objectives:**

- **5.2.1 Automation** — Scripting the pipeline so builds/deploys run without manual steps.
- **5.2.2 Code integration** — Merging changes frequently and validating them.
- **5.2.3 Code deployment / Build** — Compiling/packaging and releasing software.
- **5.2.4 Testing** — Automated verification (unit, integration, etc.).
- **5.2.5 Security (SAST/DAST/SCA/secret-scan)** — Shifting security left into the pipeline.
- **5.2.6 Workflow** — The ordered sequence of pipeline stages.
- **5.2.7 Artifacts** — Outputs produced and stored by the pipeline.
- **5.2.8 Repositories (public/private)** — Where code and packages are stored.
- **5.2.9 Images (VM/container)** — Machine/container images as deployable units.
- **5.2.10 Packages (RPM/Debian/ZIP/tar)** — OS and archive packaging formats.
- **5.2.11 Flat file** — Plain-file artifacts/configs.

---

## SECTION 2 — EXAM KNOWLEDGE

### 5.2.1 Automation
- **Definition:** Defining the build-test-deploy sequence as code (a pipeline file) that runs without human intervention on triggers like a push or merged PR.
- **Why it matters:** Gives consistency, speed, and repeatability; removes error-prone manual release steps; enables continuous integration/delivery/deployment.
- **Analogy:** Like a coffee machine that brews the same cup every morning with one button instead of hand-brewing each time.
- **Example:** A GitHub Actions workflow builds, tests, and deploys on every push to main.

### 5.2.2 Code Integration
- **Definition:** Merging developer changes into a shared branch frequently and validating them automatically (CI = build + test on every push).
- **Why it matters:** Surfaces integration problems early; avoids "integration hell" where rare merges cause days of conflict fixes.
- **Analogy:** Cleaning as you cook instead of leaving all the dishes until the end.
- **Example:** A PR triggers an automated build + test run; broken main is fixed immediately, not left red.

### 5.2.3 Code Deployment / Build
- **Definition:** Build = turning source into a deployable artifact (compile, bundle, lint, produce image/package); Deploy = releasing that artifact to dev/staging/prod.
- **Why it matters:** "Build once, deploy many" avoids environment drift; reproducible builds mean same input → same output.
- **Analogy:** Baking a cake (build) versus serving it to guests (deploy).
- **Example:** AWS CodeBuild builds the image; AWS CodeDeploy rolls it out blue/green.

### 5.2.4 Testing
- **Definition:** Automated verification that changes work and didn't break existing behavior (unit, integration, functional, regression, performance, acceptance).
- **Why it matters:** Failing tests block promotion; catching bugs before deploy is cheaper than after; testing complements—not replaces—security scanning.
- **Analogy:** A spell-checker that flags errors before you hit send.
- **Example:** Unit tests run in CI after build; slow UI tests are fewer (testing pyramid).

### 5.2.5 Security (SAST/DAST/SCA/Secret-scan)
- **Definition:** Shifting security left — SAST (static code), DAST (running app), SCA (dependencies/CVEs), secret-scan (hardcoded keys).
- **Why it matters:** These run as pipeline gates; failing scans can block merge/deploy; maps a tool to a target.
- **Analogy:** Four inspectors checking a car: blueprint (SAST), road test (DAST), parts list (SCA), keys left inside (secret-scan).
- **Example:** SCA flags a vulnerable npm package on every PR and blocks merge until upgraded.

### 5.2.6 Workflow
- **Definition:** The ordered, visual definition of the pipeline (build → test → scan → deploy) in a file like `.github/workflows/*.yml` or Jenkinsfile.
- **Why it matters:** It's the orchestration layer tying stages together; deterministic and observable; gates can pause (manual approval).
- **Analogy:** A recipe card listing steps and conditions, not the cooking itself.
- **Example:** A workflow runs unit tests and SCA in parallel, then pauses for manual approval before prod.

### 5.2.7 Artifacts
- **Definition:** Any output a pipeline produces and stores (binaries, images, packages, test reports, SBOMs, manifests).
- **Why it matters:** Artifacts are immutable and versioned; "build once, promote" the same artifact; checksums verify integrity; last-good artifact enables rollback.
- **Analogy:** A sealed, labeled jar you can ship or return to, never reopened and edited.
- **Example:** A container image with tag `v1.2` stored in a registry, redeployed on rollback.

### 5.2.8 Repositories (Public/Private)
- **Definition:** Stores code and/or packages — source repos (Git) and package/artifact repos; public = internet-accessible, private = access-restricted.
- **Why it matters:** Public risks supply-chain exposure; private protects proprietary/secret code; choice affects security posture.
- **Analogy:** A public library (anyone reads) versus a locked filing cabinet (authorized only).
- **Example:** Consume public base images but publish internal images to a private ECR registry.

### 5.2.9 Images (VM / Container)
- **Definition:** Prebuilt portable runtime environments — VM image (OS + apps, own kernel) versus container image (app + libs, shared kernel).
- **Why it matters:** Exam distinguishes on size/startup/isolation; immutable units; pin versions (avoid `:latest`); scan for CVEs.
- **Analogy:** A full apartment building (VM) versus a shipping container dropped on a shared lot (container).
- **Example:** Docker image `app:v1.2` pulled by ECS/EKS; AMI for a VM fleet.

### 5.2.10 Packages (RPM / Debian / ZIP / tar)
- **Definition:** Distribution formats — RPM/Debian (OS-level, dependency resolution) versus ZIP/tar (generic archives, no dep metadata).
- **Why it matters:** RPM→RHEL/CentOS/Fedora, Debian→Ubuntu/Debian; tar preserves perms/symlinks; ZIP is cross-platform; affects install reliability.
- **Analogy:** A furnished, labeled moving box (RPM/deb) versus a loose bundle of stuff (ZIP/tar).
- **Example:** CI builds `.rpm`, publishes to an internal repo, Ansible installs across hundreds of servers.

### 5.2.11 Flat File
- **Definition:** A plain, simply structured file (CSV/JSON/text) used as an artifact or config — no database, no special format.
- **Why it matters:** Easy to version and diff; lacks dependency/integrity features of packages; validate schema; protect secrets if present.
- **Analogy:** A sticky note versus a filing system.
- **Example:** A `config.json` promoted across environments, or a `.env` file (watch for secrets).

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Blue/green on AWS:** CodePipeline builds a new AMI/container, CodeDeploy shifts traffic from the blue (old) to green (new) environment, and a manual approval gate sits before production—if health checks fail, traffic rolls back.
- **Container CI with ECR:** A push to CodeCommit triggers CodeBuild to compile, run unit tests, scan the image for CVEs, then push a tagged image to a private ECR repo that ECS/EKS pulls from.
- **Canary deployment:** A SaaS uses CodeDeploy canary (10% traffic first) so a bad release affects only a slice of users before full cutover, with CloudWatch alarms auto-rolling back.
- **Dependency scanning in pipelines:** A team runs SCA on every PR to flag vulnerable npm/PyPI packages (CVEs) and blocks merge until upgraded—preventing supply-chain incidents.
- **RPM distribution at scale:** A Linux shop builds `.rpm` packages in CI, publishes them to an internal repo, and uses Ansible (5.4) to roll them out across hundreds of servers with version pinning.

---

## SECTION 4 — COMPARISON TABLES

**Table 4.1 — CI vs. CD**

| Term | Scope | Deploys to prod automatically? |
|------|-------|-------------------------------|
| CI (Continuous Integration) | Build + test on every merge | No |
| Continuous Delivery | Release-ready, manual deploy | No (button) |
| Continuous Deployment | Auto-deploy on pass | Yes |

**Table 4.2 — Deployment Strategies**

| Strategy | Downtime | Risk | Rollback |
|----------|----------|------|----------|
| Rolling | Low | Medium | Replace back |
| Blue/Green | Near zero | Low | Switch back |
| Canary | Low | Lowest | Stop ramp |

**Table 4.3 — Security Scan Types**

| Scan | Target | When |
|------|--------|------|
| SAST | Source code (static) | Build |
| DAST | Running app (dynamic) | Post-deploy |
| SCA | Dependencies | Build |
| Secret-scan | Repo/credentials | Pre-commit/build |

**Table 4.4 — VM Image vs. Container Image**

| Aspect | VM image (AMI) | Container image |
|--------|----------------|-----------------|
| Isolation | Strong (own kernel) | Shared kernel |
| Size | Large | Small |
| Startup | Slow | Fast |

**Table 4.5 — Package Formats**

| Format | Ecosystem | Dependency mgmt |
|--------|-----------|-----------------|
| RPM | RHEL/CentOS/Fedora | Yes (yum/dnf) |
| Debian (.deb) | Ubuntu/Debian | Yes (apt) |
| ZIP/tar | Any | No |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-native services that map to Objective 5.2 CI/CD concepts:

- **AWS CodePipeline** — The workflow/orchestration service. It defines the ordered stages (source → build → test → deploy) and connects triggers, gates, and approvals. This is the "workflow" (5.2.6) and automation (5.2.1) engine that ties the other services together.
- **AWS CodeBuild** — The managed build service. It compiles source, runs tests, executes security scans, and produces artifacts (images, packages, flat files). Maps to code integration/build (5.2.2, 5.2.3) and the testing (5.2.4) and security (5.2.5) stages.
- **AWS CodeDeploy** — The deployment service. It pushes artifacts to targets using rolling, blue/green, or canary strategies and automates rollback on failed health checks. Maps directly to code deployment (5.2.3) and the deployment half of CD.

> Note: CodeCommit (5.1) supplies the source; CodePipeline orchestrates; CodeBuild builds/tests/scans; CodeDeploy deploys. Artifacts (5.2.7) typically land in S3 or Amazon ECR (5.4), and repositories (5.2.8) are CodeCommit (private) or ECR (private registry).

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which practice merges code frequently and builds/tests on every push?
   A. Continuous Deployment
   B. Continuous Integration
   C. Blue/green
   D. Canary

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CI = merge to a shared branch frequently and build + test automatically on every push.
> **Why A is wrong:** Continuous Deployment auto-ships to prod; the question describes integration, not deploy.
> **Why C is wrong:** Blue/green is a deployment strategy, not the build/test-on-merge practice.
> **Why D is wrong:** Canary is a deployment rollout tactic, not the integration practice.

2. SAST analyzes which of the following?
   A. A running application
   B. Source code without executing it
   C. Dependencies only
   D. Network traffic

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** SAST is static analysis of source code (no execution), run at build time.
> **Why A is wrong:** A running app is the target of DAST, not SAST.
> **Why C is wrong:** Dependencies are checked by SCA, not SAST.
> **Why D is wrong:** Network traffic is not analyzed by SAST; it is static code only.

3. DAST is best described as:
   A. Static code analysis
   B. Dynamic testing of a running app
   C. Dependency scanning
   D. Secret detection

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** DAST attacks/tests a running application from the outside, post-deploy.
> **Why A is wrong:** Static code analysis is SAST, not DAST.
> **Why C is wrong:** Dependency scanning is SCA.
> **Why D is wrong:** Secret detection is a separate scan type.

4. SCA primarily checks for:
   A. SQL injection in code
   B. Vulnerable open-source dependencies
   C. Hardcoded keys
   D. Runtime auth flaws

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** SCA scans third-party libraries for known CVEs and license issues.
> **Why A is wrong:** SQL injection in code is a SAST finding, not SCA.
> **Why C is wrong:** Hardcoded keys are caught by secret scanning, not SCA.
> **Why D is wrong:** Runtime auth flaws are DAST's domain, not SCA.

5. Secret scanning detects:
   A. CVEs in libraries
   B. Hardcoded credentials/API keys
   C. SQL injection
   D. Slow queries

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Secret-scan flags credentials/keys committed into the repo.
> **Why A is wrong:** CVEs in libraries are SCA findings.
> **Why C is wrong:** SQL injection is found by SAST on source code.
> **Why D is wrong:** Slow queries are a performance concern, not a secret-scan target.

6. Which deployment strategy runs two identical environments and switches traffic?
   A. Rolling
   B. Canary
   C. Blue/green
   D. Immutable

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** Blue/green keeps identical blue (old) + green (new) envs and cuts traffic over.
> **Why A is wrong:** Rolling replaces instances gradually in one environment, not two parallel envs.
> **Why B is wrong:** Canary shifts a small percentage first, not a full switch between two envs.
> **Why D is wrong:** Immutable just means don't edit in place; it is not a two-env switch strategy.

7. A canary deployment sends what portion of traffic to the new version first?
   A. 100%
   B. A small percentage
   C. None
   D. Only internal

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Canary routes a small slice (e.g. 10%) to the new version before ramping up.
> **Why A is wrong:** 100% is a full cutover, not canary.
> **Why C is wrong:** Canary does send some traffic, not none.
> **Why D is wrong:** Canary is about percentage, not audience; it is not restricted to internal users.

8. "Build once, deploy many" promotes which principle?
   A. Bake config into the build
   B. Same immutable artifact across environments
   C. Rebuild per environment
   D. Manual config

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** It means building one artifact and promoting that identical, immutable artifact everywhere to avoid drift.
> **Why A is wrong:** Baking config into the build breaks environment-specific promotion.
> **Why C is wrong:** Rebuilding per environment defeats "build once" and risks drift.
> **Why D is wrong:** Manual config per env reintroduces drift, contrary to the principle.

9. Which artifact format is OS-specific with dependency resolution on RHEL?
   A. tar
   B. ZIP
   C. RPM
   D. CSV

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** RPM is the RHEL/CentOS/Fedora package with yum/dnf dependency resolution.
> **Why A is wrong:** tar is a generic archive with no dependency metadata.
> **Why B is wrong:** ZIP is a cross-platform archive without dependency resolution.
> **Why D is wrong:** CSV is a flat data file, not an OS package.

10. Debian packages use which toolset?
    A. yum/dnf
    B. apt/dpkg
    C. npm
    D. pip

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Debian/Ubuntu uses apt/dpkg for package management.
> **Why A is wrong:** yum/dnf is the RPM (Red Hat) toolset.
> **Why C is wrong:** npm is for Node.js packages, not OS deb packages.
> **Why D is wrong:** pip installs Python packages, not Debian system packages.

11. A container image shares what with the host?
    A. Nothing
    B. The OS kernel
    C. The disk
    D. The network only

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Containers share the host kernel (unlike VMs), enabling light weight and fast start.
> **Why A is wrong:** Containers are not fully isolated; they share the host kernel.
> **Why C is wrong:** Containers have their own filesystems, not the host's entire disk.
> **Why D is wrong:** Networking is namespaced; the kernel (not just network) is shared.

12. Compared to a VM image, a container image is:
    A. Heavier and slower
    B. Lighter and faster to start
    C. Strongly isolated
    D. Not portable

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Containers share the kernel and ship only app + libs, so they are small and quick.
> **Why A is wrong:** Containers are lighter/smaller, not heavier.
> **Why C is wrong:** Containers have weaker isolation (shared kernel), not stronger.
> **Why D is wrong:** Containers are highly portable across hosts.

13. A flat file artifact is best described as:
    A. A full VM image
    B. A plain/config file like JSON or CSV
    C. An RPM package
    D. A running container

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** A flat file is a simply structured plain file (JSON/CSV/text) used as an artifact.
> **Why A is wrong:** A full VM image is an image artifact, not a flat file.
> **Why C is wrong:** An RPM is a packaged binary, not a plain flat file.
> **Why D is wrong:** A running container is a process, not a file artifact.

14. Which AWS service orchestrates the pipeline stages?
    A. CodeBuild
    B. CodeDeploy
    C. CodePipeline
    D. CodeCommit

> [!note]- Reveal Answer
> **Correct: C**
> **Why correct:** CodePipeline defines and orchestrates the ordered source → build → test → deploy stages.
> **Why A is wrong:** CodeBuild runs the build/test stage, not orchestration.
> **Why B is wrong:** CodeDeploy handles deployment, not overall orchestration.
> **Why D is wrong:** CodeCommit is the source repo, not the pipeline orchestrator.

15. Which AWS service performs the build and test stage?
    A. CodePipeline
    B. CodeBuild
    C. CodeDeploy
    D. ECR

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** CodeBuild compiles, runs tests, and executes scans to produce artifacts.
> **Why A is wrong:** CodePipeline orchestrates but does not run the build itself.
> **Why C is wrong:** CodeDeploy deploys artifacts; it does not build.
> **Why D is wrong:** ECR is a registry for storing images, not a build service.

16. Which AWS service handles deployment with rollback?
    A. CodeDeploy
    B. CodeBuild
    C. CodePipeline
    D. CodeCommit

> [!note]- Reveal Answer
> **Correct: A**
> **Why correct:** CodeDeploy pushes artifacts using rolling/blue-green/canary and auto-rolls back on failed health checks.
> **Why B is wrong:** CodeBuild builds, it does not deploy.
> **Why C is wrong:** CodePipeline orchestrates; the deployment is done by CodeDeploy.
> **Why D is wrong:** CodeCommit is source control.

17. An immutable artifact should be:
    A. Editable after creation
    B. Never changed after creation
    C. Deleted nightly
    D. Rebuilt each deploy

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Immutable artifacts are sealed at build time and promoted unchanged across envs.
> **Why A is wrong:** Editing after creation breaks immutability and reproducibility.
> **Why C is wrong:** Deletion cadence is not the point; immutability is about not changing.
> **Why D is wrong:** Rebuilding each deploy defeats the "same artifact" principle.

18. A manual approval gate in a workflow is typically placed:
    A. Before the build
    B. Before production deploy
    C. After rollback
    D. Inside a unit test

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Approval gates pause the pipeline before the risky prod deploy for human sign-off.
> **Why A is wrong:** Build runs automatically; you do not gate before building.
> **Why C is wrong:** Rollback happens after a failure, not as a pre-gate.
> **Why D is wrong:** Unit tests are not a place for a manual approval gate.

19. Public vs. private repositories primarily differ in:
    A. Build speed
    B. Access/restriction
    C. Image size
    D. Test coverage

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** Public repos are internet-accessible; private repos restrict access to authorized users.
> **Why A is wrong:** Build speed is not determined by public vs private.
> **Why C is wrong:** Image size is unrelated to repo visibility.
> **Why D is wrong:** Test coverage is not a function of repo access level.

20. The main risk of using `:latest` image tags is:
    A. Faster startup
    B. Non-reproducible deployments
    C. Smaller size
    D. Better security

> [!note]- Reveal Answer
> **Correct: B**
> **Why correct:** `:latest` floats to whatever was last pushed, so deploys are not reproducible/pinnable.
> **Why A is wrong:** Faster startup is not a real benefit of `:latest`; it is a pinning problem.
> **Why C is wrong:** Tag choice does not change image size.
> **Why D is wrong:** `:latest` hurts—not helps—security (no pinned, auditable version).
