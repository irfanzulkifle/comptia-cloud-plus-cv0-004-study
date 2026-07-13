---
title: CompTIA Cloud+ CV0-004 — Objective 5.2
objective: "5.2 Explain concepts related to continuous integration/continuous deployment (CI/CD) pipelines."
domain: "5.0 DevOps Fundamentals"
weight: "10% of exam"
tags: [Cloud+, CV0-004, DevOps, CI/CD, Pipelines, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 5.2
## Continuous Integration / Continuous Deployment (CI/CD) Pipelines

> **Objective statement:** *"Explain concepts related to continuous integration/continuous deployment (CI/CD) pipelines."*
> **Domain:** 5.0 DevOps Fundamentals (10% of the exam)
> **What it tests:** Whether you understand the *stages and artifacts* of a CI/CD pipeline (ties to 2.4 code-deploy/configure and 5.1 source control). Expect "what happens at the build/test/deploy stage" and "what artifact type is this" questions.

---

## SECTION 1 — OBJECTIVE OVERVIEW

**In one breath:** CI/CD = automating the path from committed code to deployed artifact — integrate, build, test, secure, deploy, store.

**The 5.2 sub-objectives (verbatim from the official exam objectives):**

| # | Sub-objective | What it means |
|---|----------------|---------------|
| 5.2.1 | **Automation** | Pipeline runs steps with no manual touch |
| 5.2.2 | **Code integration** | Merge feature code into shared branch frequently (CI) |
| 5.2.3 | **Code deployment** | Push build to target (env/stage/prod) (CD) |
| 5.2.3a | └ **Build** | Compile/package source into an artifact |
| 5.2.4 | **Testing** | Automated tests in the pipeline (unit/integration) |
| 5.2.5 | **Security** | Scan code/deps/artifacts (SAST/DAST/secret scan) in pipeline |
| 5.2.6 | **Workflow** | The defined pipeline stages + triggers |
| 5.2.7 | **Artifacts** | Outputs stored after build (images, packages, files) |
| 5.2.8 | **Repositories** | Where code/artifacts live — Public vs Private |
| 5.2.8a | ├ Public | Open/shared registry or repo |
| 5.2.8b | └ Private | Restricted, authenticated registry/repo |
| 5.2.9 | **Images** | VM images vs Container images |
| 5.2.9a | ├ VM | Golden VM images (AMIs, Azure Image) |
| 5.2.9b | └ Container | Docker/OCI images |
| 5.2.10 | **Packages** | Distro/archive packaging |
| 5.2.10a | ├ Red Hat Package Manager (RPM) | `.rpm` (RHEL/CentOS) |
| 5.2.10b | ├ Debian | `.deb` (Debian/Ubuntu) |
| 5.2.10c | ├ ZIP | `.zip` archive |
| 5.2.10d | └ tar | `.tar` / `.tar.gz` archive |
| 5.2.11 | **Flat file** | Unstructured single-file artifact (config, data) |

**Mental model — the pipeline flow:**
`Code integration (5.2.2) → Build (5.2.3a) → Test (5.2.4) → Security scan (5.2.5) → Artifact (5.2.7: image/package/flat) → Deploy (5.2.3) → stored in Repo (5.2.8). All driven by Workflow (5.2.6) + Automation (5.2.1).`

---

## SECTION 2 — EXAM KNOWLEDGE

### 5.2.1 — AUTOMATION

**Definition:** Pipeline steps (build/test/deploy) run **without manual intervention** once triggered (e.g., on push/PR).

- Removes human error, speeds delivery, enforces consistency.
- Tools: Jenkins, GitHub Actions, GitLab CI, Argo CD, CircleCI.
- **Ties to 2.4:** automation/ IaC is how cloud resources get deployed.
- **Exam angle:** "We want builds to run on every push with no clicking" → **automation** via CI runner.

### 5.2.2 — CODE INTEGRATION (CI)

**Definition:** Developers **frequently merge** feature branches into a shared mainline; the pipeline **integrates + builds + tests** on each merge to catch breakage early.

- "Continuous" = often (multiple times/day), not at release-end.
- Catches integration conflicts and broken builds fast (shift-left).
- **Exam angle:** "Team merges daily so bugs surface immediately" → **continuous integration**.

### 5.2.3 — CODE DEPLOYMENT (CD) + BUILD

**Deployment (CD):** Automatically releasing the built artifact to a target — dev → stage → prod, often with approval gates.

**Build (5.2.3a):** Compiling source + dependencies into a deployable **artifact** (binary, image, package).
- Build should be **reproducible** (same input → same output); use lockfiles.
- **Exam angle:** "Turn the committed code into something we can ship" → **build** stage produces the artifact; "release it to prod automatically" → **deployment**.

### 5.2.4 — TESTING

**Definition:** Automated tests run inside the pipeline to validate the build before it ships.

- **Unit** (single component), **Integration** (components together), sometimes **Functional/E2E**.
- Failing tests **block** the pipeline (gate).
- **Exam angle:** "Pipeline should stop a broken build from deploying" → enforce **testing** as a gate.

### 5.2.5 — SECURITY

**Definition:** Security checks **baked into** the pipeline (shift-left security / DevSecOps).

- **SAST** (static code scan), **DAST** (dynamic running-app scan), **SCA** (dependency CVE scan), **secret scanning** (catch hardcoded keys — ties 4.4.6).
- Blocks vulnerable/leaky builds before deploy.
- **Exam angle:** "Stop a commit that hardcoded a DB password from reaching prod" → **secret scanning** in the security stage.

### 5.2.6 — WORKFLOW

**Definition:** The **defined sequence of pipeline stages** + their **triggers** (on-push, on-PR, scheduled).

- Expressed as code (YAML): GitHub `workflow.yml`, GitLab `.gitlab-ci.yml`, Jenkinsfile.
- Stages: build → test → security → deploy; with conditions/approvals.
- **Exam angle:** "Define the order build→test→deploy runs in" → the **workflow** definition.

### 5.2.7 — ARTIFACTS

**Definition:** The **outputs** a build produces and stores for later deploy/promotion.

- Types covered below: **Images** (VM/container), **Packages** (rpm/deb/zip/tar), **Flat file**.
- Stored in artifact repos/registries; **immutable + versioned**.
- **Exam angle:** "The pipeline produced a Docker image we now deploy to 3 envs" → that image is the **artifact**.

### 5.2.8 — REPOSITORIES (Public vs Private)

- **Public:** open/shared — anyone can pull (e.g., public Docker Hub, public GitHub repo). Risk: supply-chain/secret exposure.
- **Private:** authenticated, access-controlled (e.g., private ECR/ACR/GCR, private Git repo). Preferred for proprietary code/artifacts.
- **Exam angle:** "Our internal images must not be world-pullable" → use a **private** repository/registry.

### 5.2.9 — IMAGES (VM vs Container)

- **VM images:** golden machine images — AWS AMI, Azure Image Gallery, GCP Image. Contain OS + baked config.
- **Container images:** OCI/Docker images (layers) run by a runtime (containerd/CRI-O). Portable, lightweight.
- **Exam angle:** "Bake a standardized server template to launch many VMs" → **VM image**; "Package the app to run anywhere" → **container image**.

### 5.2.10 — PACKAGES

Packaging formats for distribution/install:
- **RPM** (`.rpm`) — Red Hat family (RHEL, CentOS, Fedora).
- **Debian** (`.deb`) — Debian/Ubuntu family.
- **ZIP** (`.zip`) — cross-platform compressed archive.
- **tar** (`.tar`, `.tar.gz`) — Unix archive (often gzipped).
- **Exam angle:** "Build a distributable for our RHEL fleet" → **RPM** package; "Bundle the app for Ubuntu" → **Debian (.deb)**.

### 5.2.11 — FLAT FILE

**Definition:** A single unstructured/plain file artifact — config, data dump, or script — not a compiled package/image.

- Examples: a `config.yaml`, a `.json` data file, a shell script.
- Stored in artifact storage (S3, blob) or a repo.
- **Exam angle:** "The pipeline outputs a settings file consumed by the deploy step" → **flat file** artifact.

> **Artifact-type cheat:** VM image = full server template · Container image = portable app runtime · RPM/DEB = OS-package installer · ZIP/tar = archive bundle · Flat file = lone config/data file. Public vs Private = who can pull it.



## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 — CI Pipeline That Builds and Tests on Every PR
A fintech team hosts a Python payments service on GitHub. They define a **CI** workflow in `.github/workflows/ci.yml` that triggers on every `pull_request`. The **workflow** has three stages: *checkout → build → test*. The build stage compiles the code and creates a **build artifact** (a wheel plus a container image), while the test stage runs unit and integration tests in parallel. Because CI only *integrates, builds, and tests* — it never ships to production — a failed test automatically blocks the PR merge via branch protection. Engineers get fast feedback in minutes instead of discovering breakage after a manual release. **Exam angle:** CI produces artifacts and gates quality; it does NOT deploy. Deployment is the job of **CD**.

### 3.2 — Baking Secrets-Scan Into the Security Stage to Block a Leak
A GitLab project adds a dedicated **security** stage before the deploy stage. The pipeline runs a **secret-scan** (e.g., gitleaks/trufflehog) that inspects the diff for AWS keys, tokens, and `.env` files. When a developer commits an `AWS_SECRET_ACCESS_KEY`, the scan fails the job, the pipeline stops, and the merge is blocked — the leaked credential is rotated before it ever reaches an image or registry. This is a **static, code-level** check, distinct from **SAST** (which finds code flaws) and **DAST** (which attacks a running app). **Exam angle:** secret-scan is its own scan stage — it is NOT DAST, and it runs *before* deploy, not after.

### 3.3 — Golden VM Image vs Container Image for Deployment
A startup must deploy a legacy monolith with a fixed OS, kernel module, and licensed agent. They build a **golden VM image** (an **AMI** in AWS) via Packer, baking in the OS, dependencies, and config so every instance is identical and boots fast. A separate microservice team instead ships a **container image** pushed to **AWS ECR**, which is lightweight, portable across hosts, and starts in seconds. The VM image is a full operating system; the container image shares the host kernel and is just the app + libs. **Exam angle:** VM image ≠ container image — one is a full OS, the other is an app-layer package. Don't confuse a **flat file** (a single binary/archive) with a container image.

### 3.4 — Choosing RPM vs Debian Package for the Target Fleet
The ops team manages two fleets: RHEL/CentOS servers (500 nodes) and Ubuntu servers (200 nodes). For the RHEL fleet they package the agent as an **RPM** (`.rpm`) and distribute via a local `yum`/`dnf` repo; for Ubuntu they package as a **Debian** (`.deb`) and serve an `apt` repo. The build pipeline produces both artifacts from one source so each node installs with its native package manager, gets clean upgrades, and verifies signatures. **Exam angle:** RPM is for Red Hat family; Debian (`.deb`) is for Ubuntu/Debian. Never ship an `.rpm` to an Ubuntu box — use `.deb`. A **ZIP** or **tar** is just an archive, not an installed/managed package.

### 3.5 — Private vs Public Registry for Proprietary Images
The company's proprietary billing image must never be world-pullable. They push it to a **private repository** in **AWS ECR** with IAM-scoped pull permissions, so only authenticated workloads in the VPC can pull. A public registry (Docker Hub public repo) would expose the image to anyone — catastrophic for proprietary IP. **Exam angle:** a **private** repository is NOT world-pullable; it requires auth. A public registry is fine for open-source images but wrong for proprietary code. Keep proprietary artifacts in a private registry.

## SECTION 4 — COMPARISON TABLES

### 4.1 — CI vs CD
| Aspect | CI (Continuous Integration) | CD (Continuous Deployment/Delivery) |
| --- | --- | --- |
| Purpose | Integrate, build, test code on every change | Deploy built artifacts to environments |
| Runs | On every commit / PR | After CI passes |
| Output | Build **artifact** (binary, image, package) | Released/deployed running service |
| Touches prod? | **No** | **Yes** (deploy stage) |
| Key goal | Catch breakage early | Ship reliably & repeatedly |
| Exam anchor | "CI = integrate/build/test" | "CD = deploys" |

### 4.2 — Build vs Deploy
| Aspect | Build | Deploy |
| --- | --- | --- |
| Stage | Build stage (part of CI) | Deploy stage (part of CD) |
| Action | Compile code, package into **artifact** | Push artifact to target (VM/container/host) |
| Output | **Artifact** (image, RPM, .deb, ZIP, tar) | Running application |
| Happens first? | **Yes** — must build before deploy | **No** — only after build succeeds |
| Reversible? | Just re-run | Needs rollback strategy |
| Exam anchor | Build = produce output | Deploy = place output live |

### 4.3 — VM Image vs Container Image
| Aspect | VM Image (e.g., AMI) | Container Image |
| --- | --- | --- |
| Contains | Full OS + kernel + app | App + libs only (shares host kernel) |
| Size | Large (GBs) | Small (MBs–low GBs) |
| Boot time | Slow (minutes) | Fast (seconds) |
| Portability | Tied to hypervisor/cloud | Runs anywhere with container runtime |
| Use case | Legacy/stateful, kernel-level needs | Microservices, stateless scale-out |
| Registry | AMI store / image service | ECR / Docker Hub |
| Exam anchor | VM = whole machine | Container = app layer, NOT a VM |

### 4.4 — Public vs Private Repository
| Aspect | Public Repository | Private Repository |
| --- | --- | --- |
| Access | **World-pullable** (anyone) | Auth/permission required |
| Use for | Open-source images/packages | **Proprietary** code & images |
| Risk | IP exposure if misused | Misconfig can still leak |
| Example | Docker Hub public, public ECR | Private ECR, private GitLab reg. |
| Exam anchor | Private ≠ world-pullable | Proprietary → private |

### 4.5 — RPM vs Debian vs ZIP vs tar Packages
| Aspect | RPM (.rpm) | Debian (.deb) | ZIP | tar (.tar/.tar.gz) |
| --- | --- | --- | --- | --- |
| Package manager | yum/dnf (RHEL family) | apt/dpkg (Debian/Ubuntu) | None | None |
| Installed/managed? | **Yes** (registry of files) | **Yes** (registry of files) | **No** (just extract) | **No** (just extract) |
| Dependency resolve | Yes | Yes | No | No |
| Target OS | Red Hat family | Debian/Ubuntu | Any (archive) | Any (archive) |
| Exam anchor | RPM ≠ Ubuntu | .deb for Debian/Ubuntu | ZIP/tar = archive, NOT installer | tar.gz ≠ .deb |

### 4.6 — SAST vs DAST vs SCA vs Secret-Scan
| Tool | What it checks | Needs running app? | Stage |
| --- | --- | --- | --- |
| **SAST** | Source code flaws (static) | **No** | Security (static) |
| **DAST** | Runtime/running app attacks | **Yes** | Security (dynamic) |
| **SCA** | Open-source/lib vulnerabilities | No (manifest scan) | Security |
| **Secret-scan** | Hard-coded keys/tokens in code | No (diff/file scan) | Security (separate) |
| Exam anchor | SAST = static, no app | DAST = needs running app | Secret-scan ≠ DAST |

## SECTION 5 — EXAM TRAPS

**Trap 1 — "CI deploys to production"**
- **Scenario:** A question says the CI pipeline pushes the new build straight to the production servers.
- **Correct:** CI only integrates, builds, and tests; **CD** is what deploys. CI must never touch prod.
- **Why wrong:** Confusing CI (build/test gate) with CD (deploy). CI produces artifacts; it does not ship them.

**Trap 2 — "Build equals deploy"**
- **Scenario:** An option claims the build stage places the application live on servers.
- **Correct:** Build produces the **artifact**; **deploy** is a separate later stage that places it.
- **Why wrong:** Build happens first and only creates output. Deploy is downstream in CD.

**Trap 3 — "A private repo is world-pullable"**
- **Scenario:** A private ECR repo is described as pullable by anyone on the internet.
- **Correct:** A **private** repository requires authentication/permissions — it is NOT publicly pullable.
- **Why wrong:** "Private" means restricted access. Only public repos are world-pullable.

**Trap 4 — "A container image is a VM image"**
- **Scenario:** An option treats a container image as a full virtual machine with its own kernel.
- **Correct:** A container image is app + libs only; it **shares the host kernel**. A VM image includes a full OS.
- **Why wrong:** Containers are not virtual machines. VM images bundle the OS; containers don't.

**Trap 5 — "Use an RPM on Ubuntu"**
- **Scenario:** A fleet of Ubuntu servers should be packaged with `.rpm` files.
- **Correct:** Ubuntu is Debian-based — use a **.deb** package with `apt`/`dpkg`.
- **Why wrong:** RPM is for the Red Hat family (RHEL/CentOS). Ubuntu needs `.deb`.

**Trap 6 — "A ZIP is an installed package"**
- **Scenario:** A ZIP file is called a managed, installed package with a file registry.
- **Correct:** A ZIP is just an **archive** you extract; it is not installed or tracked by a package manager.
- **Why wrong:** Installed packages (RPM/.deb) register files and resolve deps. ZIP/tar do neither.

**Trap 7 — "Secret-scan is a form of DAST"**
- **Scenario:** The secret-scanning step is grouped as dynamic application security testing.
- **Correct:** Secret-scan is its own **static** check for hard-coded credentials; **DAST** attacks a running app.
- **Why wrong:** Secret-scan doesn't run the app and isn't DAST. They are separate security tools.

**Trap 8 — "Testing runs after deploy"**
- **Scenario:** Tests execute on the production environment after the code is deployed.
- **Correct:** Testing must **gate before** deploy; a failing test blocks the release, it doesn't follow it.
- **Why wrong:** CI tests are a pre-deploy quality gate. Testing after deploy defeats the purpose.

**Trap 9 — "A flat file is a container image"**
- **Scenario:** A single binary/flat file is described as a container image ready to run in a runtime.
- **Correct:** A **flat file** is just one file/binary; a container image is a layered, runnable package with metadata.
- **Why wrong:** A container image is far more than a lone file — it has layers, an entrypoint, and config.

**Trap 10 — "The workflow is the build tool"**
- **Scenario:** A question says the workflow itself is the compiler/build engine.
- **Correct:** The **workflow** is the stage *definition* (what runs, in what order); the build tool (Maven, npm) does the compiling.
- **Why wrong:** Workflow = orchestration of stages. The build tool is the program that actually builds.

**Trap 11 — "Automation still needs manual clicks"**
- **Scenario:** An automated pipeline requires an engineer to manually click "run" each time.
- **Correct:** **Automation** triggers on events (PR, commit, schedule) with no human clicks required.
- **Why wrong:** The whole point of CI/CD automation is hands-off execution. Manual clicks are the opposite.

**Trap 12 — "SAST needs the running application"**
- **Scenario:** SAST is described as testing a live, deployed application for vulnerabilities.
- **Correct:** **SAST** is static — it scans source code and needs **no running app**. That's **DAST**.
- **Why wrong:** SAST analyzes code at rest; DAST is the one that requires a running app.

**Trap 13 — "The artifact is the source code"**
- **Scenario:** The build artifact is identified as the original source repository/code.
- **Correct:** The **artifact** is the *output* of the build (binary, image, package) — not the input source.
- **Why wrong:** Source is the input; artifact is what the build produces and what deploy consumes.

**Trap 14 — "A public registry is fine for proprietary images"**
- **Scenario:** Proprietary company images are pushed to a public Docker Hub repository.
- **Correct:** Proprietary images belong in a **private** registry (e.g., private ECR) with restricted access.
- **Why wrong:** Public = world-pullable = IP leak. Proprietary code must stay private.

**Trap 15 — "A tar.gz is a .deb installer"**
- **Scenario:** A `.tar.gz` archive is treated as a Debian installer that registers files via apt.
- **Correct:** A `.tar.gz` is just a compressed **archive** you extract manually; a **.deb** is a managed Debian package.
- **Why wrong:** tar.gz has no package database, deps, or installer. `.deb` is the real Debian package format.


## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

These PBQs target CompTIA Cloud+ **CV0-004** sub-objective **5.2 (CI/CD Pipelines)**. Each one gives you a scenario, a walkthrough of the expected steps, and a bonus question to test deeper understanding.

### 6.1 — Design a Pipeline (integrate → build → test → security → deploy)

**Scenario**
Your team asks you to build a CI/CD pipeline for a small web app from scratch. The only requirement is that it must follow best-practice order: **integrate → build → test → security → deploy**. No specific tools are named — pick reasonable ones.

**Walkthrough**
1. **Integrate** — Trigger on `git push`/`pull_request`. Check out the code, install dependencies, run a linter/merge check. This is *continuous integration* — merging code into a shared mainline, not deploying.
2. **Build** — Compile/pack the code into a **deployable artifact** (JAR, container image, zip). The output here is the **artifact**, not the source.
3. **Test** — Run automated unit/integration tests against the build output. Fail the pipeline if any test fails.
4. **Security** — Run a **secret-scan** and dependency/vulnerability scan (e.g. `trivy`, `gitleaks`). Block the pipeline on findings.
5. **Deploy** — Promote the *same* artifact to staging then production (e.g. `kubectl apply`, `aws deploy`). Deploy the artifact — do not rebuild.

```
git push ─▶ integrate ─▶ build ─▶ test ─▶ security ─▶ deploy ─▶ artifact
```

**Bonus question**
*Why must the security stage run after build but before deploy, and why should deploy use the same artifact that passed testing rather than rebuilding?*
> Answer: Running security after build means you scan the actual artifact that will ship. Rebuilding for deploy breaks provenance — the artifact you tested is no longer the one you deploy, so your test/security results no longer apply.

### 6.2 — Add a Secret-Scan Stage to Block a Hardcoded Key

**Scenario**
A developer committed a hardcoded AWS access key (`AKIA...`) into `config.py`. You must add a **secret-scanning** stage to the pipeline that **blocks** the build when a secret is detected.

**Walkthrough**
1. Add a new **security** stage between `test` and `deploy`.
2. Run a scanner such as `gitleaks detect --source . --redact` or `trufflehog filesystem .`.
3. Configure the stage to **fail (exit non-zero)** when a secret is found — this blocks the pipeline before `deploy`.
4. Rotate the leaked key in the cloud console and purge it from git history (`git filter-repo` / BFG).
5. Move the secret into a **secret manager** (AWS Secrets Manager, Azure Key Vault, GCP Secret Manager) and inject it at runtime — never in source.

**Bonus question**
*After blocking the pipeline, why is rotating the key and rewriting history just as important as adding the scan?*
> Answer: The scan only stops future leaks. The key is already exposed in git history/CI logs, so it must be rotated (invalidated) and scrubbed from history or it remains compromised.

### 6.3 — Pick VM Image vs Container Image for a Workload

**Scenario**
You have two workloads: (A) a legacy monolith that needs a full OS, a specific kernel module, and persistent state; (B) a stateless microservice that must scale fast and run identically everywhere. Choose the right image type for each.

**Walkthrough**
1. **Workload A → VM image** (e.g. **AMI**, Azure Image, GCP Image). Use when you need a full guest OS, kernel control, or long-lived stateful services.
2. **Workload B → Container image** (e.g. **Docker** image in a registry). Use for stateless, portable, fast-starting, horizontally-scaling services.
3. Document the choice: VM images are heavier (boot a whole OS) but give OS isolation; container images share the host kernel, start in seconds, and are portable across any runtime.

| Workload | Image type | Why |
|---|---|---|
| Legacy monolith, kernel module, state | **VM image** | Full OS + kernel control, persistent state |
| Stateless microservice, fast scale | **Container image** | Lightweight, portable, seconds to start |

**Bonus question**
*Can a container image run inside a VM? Why would you do both?*
> Answer: Yes — containers commonly run on VM-based nodes (e.g. EKS on EC2). The VM gives the isolation/hardware boundary; the container gives fast, portable packaging inside it.

### 6.4 — Choose RPM vs Debian for Target OS + Private Registry

**Scenario**
Your target servers run **RHEL/CentOS/Rocky** in one region and **Ubuntu/Debian** in another. You must decide the **package format** and set up a **private package registry** so builds don't hit the public internet.

**Walkthrough**
1. **RHEL-family hosts → `.rpm`** packages. Use `dnf`/`yum` and a repo like **RPM-based** private registry.
2. **Debian-family hosts → `.deb`** packages. Use `apt` and a private **APT** repo.
3. Stand up a **private registry** (e.g. Nexus, Artifactory, or cloud-native: AWS ECR/CodeArtifact, Azure Artifacts, GCP Artifact Registry) to host both `.rpm` and `.deb` internally.
4. Point each host's package manager at the **private** endpoint — not the public mirror — for repeatability and offline/secure builds.

| Target OS | Package format | Tooling |
|---|---|---|
| RHEL / CentOS / Rocky | **`.rpm`** | `dnf` / `yum`, RPM repo |
| Ubuntu / Debian | **`.deb`** | `apt`, APT repo |

**Bonus question**
*Why is a private registry important for both `.rpm` and `.deb` pipelines even when the public repos are reachable?*
> Answer: A private registry gives reproducible, version-pinned, auditable builds and removes dependency on external network availability/security — critical for supply-chain control.

### 6.5 — Read a Workflow YAML and Name the Stages

**Scenario**
You are given the following pipeline YAML. Read it and **name the five stages in execution order**, then say which one is missing for a secure pipeline.

```yaml
stages:
  - integrate
  - build
  - test
  - deploy
jobs:
  integrate: { script: [ "npm ci", "npm run lint" ] }
  build:     { script: [ "npm run build" ] }
  test:      { script: [ "npm test" ] }
  deploy:    { script: [ "kubectl apply -f k8s/" ] }
```

**Walkthrough**
1. **Read top-to-bottom**: the `stages:` list defines order — `integrate`, `build`, `test`, `deploy`.
2. **Name them**: (1) integrate, (2) build, (3) test, (4) deploy.
3. **Spot the gap**: there is **no `security` stage** between `test` and `deploy`. A secure pipeline must add it (secret-scan + dependency scan) before deploy.
4. Confirm each `jobs:` block maps to a declared stage and runs in that order.

**Bonus question**
*Where exactly would you insert a `security` stage in this YAML, and what would its `script` contain?*
> Answer: Insert `security` between `test` and `deploy` in the `stages:` list, and add a `security:` job running a secret scanner + vulnerability scanner that fails the pipeline on findings.

---

## SECTION 7 — MEMORY AIDS

Quick recall hooks for the **5.2** CI/CD sub-objectives. Bold the term, remember the rule.

**1. 5.2.2 — CI = integrate, NOT deploy**
> **Continuous Integration** means merging/validating code into a shared branch automatically. It does *not* mean shipping to production. Integrate ≠ deploy.

**2. 5.2.3 — build ≠ deploy**
> The **build** stage produces an artifact. **Deploy** ships that artifact. Never confuse making the package with releasing it.

**3. 5.2.7 — artifact = OUTPUT, not source**
> An **artifact** is the *compiled/packaged result* of a build (image, JAR, zip). Source code is the input — the artifact is what you store in a registry and deploy.

**4. 5.2.8 — private ≠ public**
> A **private** registry/repo is authenticated and internal; a **public** one is open to the internet. Production pipelines should pull/push to **private** endpoints for security and reproducibility.

**5. 5.2.9 — VM image vs container image**
> **VM image** = whole OS + kernel (heavy, stateful, OS isolation). **Container image** = app + deps, shares host kernel (light, portable, fast). Pick by workload isolation vs speed.

**6. 5.2.10 — rpm / deb / zip / tar**
> **`.rpm`** = RHEL family (`dnf`/`yum`). **`.deb`** = Debian family (`apt`). **`.zip`** / **`.tar`** = generic archive artifacts (OS-agnostic packaging). Match the format to the target OS.

**7. ASCII pipeline (the one diagram to memorize)**
```
push ─▶ integrate ─▶ build ─▶ test ─▶ security ─▶ deploy ─▶ artifact
```
> Say it out loud before the exam: *"push, integrate, build, test, security, deploy, artifact."*

---

## SECTION 8 — CLOUD PROVIDER MAPPING

How CV0-004 5.2 CI/CD concepts map onto the three big clouds. Use this to answer "which service does X on provider Y?" questions.

| Concept | AWS | Azure | GCP |
|---|---|---|---|
| **CI tooling** | CodePipeline / CodeBuild | Azure Pipelines / DevOps | Cloud Build |
| **Build service** | CodeBuild | Azure DevOps Build | Cloud Build |
| **Artifact registry** | ECR / CodeArtifact | Azure Artifacts / ACR | Artifact Registry |
| **Container image registry** | **ECR** (Elastic Container Registry) | **ACR** (Container Registry) | **Artifact Registry** / GCR |
| **VM image** | **AMI** (Amazon Machine Image) | Azure Image / Managed Image | GCP Image / Compute Image |
| **Packages (.rpm/.deb/zip/tar)** | CodeArtifact / S3 | Azure Artifacts | Artifact Registry / Storage |

**Cross-provider reminders**
- **Images are not portable by default** — an AMI won't boot on Azure/GCP. Use **container images** (OCI) for cross-cloud portability; use VM images for provider-native stateful workloads.
- **Private by default** — ECR/ACR/Artifact Registry are private; you must grant IAM/role access. Public registries (Docker Hub) are a supply-chain risk for prod.
- **Registry ≠ CI tool** — the registry *stores* artifacts/images; the CI tool *builds and moves* them. Don't mix up "where it runs" (CodeBuild/Cloud Build/DevOps) with "where it's stored" (ECR/ACR/Artifact Registry).
- **Package format follows the OS, not the cloud** — RHEL on any cloud → `.rpm`; Ubuntu on any cloud → `.deb`. The cloud just hosts the private repo.
- **Security stage is cloud-agnostic** — `gitleaks`/`trivy` run the same in every pipeline; only the secret-manager target differs (Secrets Manager / Key Vault / Secret Manager).


## SECTION 9 — INTERVIEW KNOWLEDGE

### Conceptual Q&A

**Q: What is a CI/CD pipeline?**
A: A **CI/CD pipeline** is a series of automated steps that move code from a developer's commit all the way to a running deployment. It stitches together **source control**, **build**, **test**, and **deploy** activities so releases become repeatable and fast instead of manual and error-prone.

**Q: What is the difference between Continuous Integration (CI) and Continuous Delivery/Deployment (CD)?**
A: **CI** focuses on merging code changes frequently and automatically building + testing them so the shared branch stays healthy. **CD** takes those verified builds and delivers/deploys them — *Continuous Delivery* prepares releases and waits for a manual approval, while *Continuous Deployment* releases automatically to production with no human gate.

**Q: Why do we automate pipelines instead of doing releases by hand?**
A: Automation removes human inconsistency, catches errors **earlier** (cheaper to fix), gives an **auditable trail**, and lets teams ship many times a day. Manual releases are slow and a classic source of "works on my machine" outages.

**Q: What is the difference between a trigger, a stage, and a step?**
A: A **trigger** is what *starts* the pipeline (webhook, schedule, or manual). A **stage** is a logical grouping of work (build, test, deploy) often separated by gates. A **step** is a single task (run a command, call a script) inside a stage.

**Q: What is shift-left and why does it matter?**
A: **Shift-left** means moving testing and security checks *earlier* in the development lifecycle — closer to the commit — instead of waiting until production. It finds bugs and vulnerabilities when they are cheapest to fix.

### Scenario Questions

**Scenario 1 — Name the missing stage.**
A team's pipeline looks like this: `Code commit → Build → ??? → Deploy to staging`. The missing stage runs automated unit and integration tests and fails the run if coverage drops. What stage is missing?
> **Answer:** The **Test stage** (automated testing). Build produces artifacts; tests validate them before any deployment.

**Scenario 2 — Explain shift-left in a security context.**
A company keeps finding critical vulnerabilities in production penetration tests, two weeks before every release. How would you restructure their pipeline using shift-left?
> **Answer:** Insert **security scanning** (SAST on source, SCA on dependencies, and later DAST on a running build) into the CI stage right after the build, so vulnerabilities are caught at commit time instead of at the pre-release pen-test. Pair this with an **SBOM** so every component is tracked from day one.

**Scenario 3 — Pick the right trigger.**
You need a full pipeline run every night at 2 a.m. to catch slow memory leaks, but you also want an instant run the moment a developer opens a pull request. Which two trigger types do you configure?
> **Answer:** A **scheduled (cron) trigger** for the nightly run, and a **webhook trigger** from the repo that fires on pull-request events.

### "Explain It Simply" Prompts

- **Explain a pipeline like I'm 5:** Imagine a conveyor belt. You drop in code at one end; machines automatically check it, wrap it up (build), test the wrapper, and slide it onto the shelf (deploy). If any machine finds a problem, the belt stops.
- **Explain an artifact simply:** An artifact is the "lunchbox" the pipeline packs — the finished app file (a JAR, a zip, a Docker image) that later steps or other people can grab and use.
- **Explain a secret simply:** A secret is a password or key the pipeline needs but must never write down in the recipe book (the flat file). It is kept in a locked vault and handed over only at cook time.
- **Explain GitOps simply:** GitOps means "Git is the boss." The desired state of your system lives in Git; a robot constantly checks if reality matches Git and fixes any drift automatically.

---

## SECTION 10 — FLASHCARDS

**Q:** What does CI stand for and what is its purpose?
**A:** **Continuous Integration** — automatically merging developer code changes into a shared repo and building/testing them to catch issues early.

**Q:** What does CD stand for?
**A:** **Continuous Delivery** (auto-prep to deploy, manual approval) or **Continuous Deployment** (auto-deploy straight to prod).

**Q:** What is a pipeline trigger?
**A:** An event that starts the pipeline — a **webhook** (code push), a **schedule** (cron), or a **manual** run.

**Q:** What is a webhook trigger?
**A:** An automated HTTP callback from a repo/tool that fires the pipeline when an event (push, PR) occurs.

**Q:** Give an example of a scheduled trigger.
**A:** A **cron schedule** that runs a nightly build or a weekly security scan.

**Q:** What is a manual trigger?
**A:** A human starts the pipeline via button/CLI — common for production **deployments** needing approval.

**Q:** What is the build stage?
**A:** Compiles source code into **artifacts** (binaries, packages, images) from the repo.

**Q:** What is the difference between Continuous Delivery and Continuous Deployment?
**A:** Delivery stops at "ready to deploy" with manual approval; Deployment auto-releases to prod with no human gate.

**Q:** What is a pipeline artifact?
**A:** A **digital object** produced by a stage (JAR, WAR, Docker image, zip) stored for later stages or download.

**Q:** What is a container image?
**A:** An immutable, runnable package of app code + OS libs + deps (e.g. **Docker image**) built from a Dockerfile.

**Q:** What is a package in CI/CD context?
**A:** A versioned software bundle published to a **package repository** (npm, Maven, PyPI, NuGet).

**Q:** What is a flat file in CI/CD?
**A:** A plain-text **configuration file** (YAML/JSON) defining the pipeline, e.g. `.gitlab-ci.yml` or `Jenkinsfile`.

**Q:** Name common flat-file pipeline definitions.
**A:** `.gitlab-ci.yml`, `Jenkinsfile`, GitHub Actions `workflow.yml`, Azure `azure-pipelines.yml`.

**Q:** What is unit testing?
**A:** Testing individual functions/modules in isolation — fastest, runs earliest in CI.

**Q:** What is integration testing?
**A:** Testing how multiple components work together (e.g. app + database).

**Q:** What is functional testing?
**A:** Verifying the app behaves per requirements from a user/business perspective.

**Q:** What is performance testing?
**A:** Checking responsiveness, throughput, and load (load, stress, soak tests).

**Q:** What is security scanning in a pipeline?
**A:** Automated checks for vulns, secrets, and misconfig — e.g. **SAST**, **DAST**, dependency/CVE scans.

**Q:** What is SAST?
**A:** **Static Application Security Testing** — scans source/bytecode without running the app.

**Q:** What is DAST?
**A:** **Dynamic Application Security Testing** — tests a running app for runtime vulnerabilities.

**Q:** What is SCA?
**A:** **Software Composition Analysis** — scans open-source dependencies for known CVEs (often via SBOM).

**Q:** What is a repository in CI/CD?
**A:** **Source code management** (Git) storing code + history — GitHub, GitLab, Bitbucket.

**Q:** What is a branch?
**A:** An independent line of development; merging branches integrates changes into main.

**Q:** What is a merge?
**A:** Combining changes from one branch into another (e.g. feature → main).

**Q:** What is a stage in a pipeline?
**A:** A logical grouping of steps (build/test/deploy), often with gates/approvals between them.

**Q:** What is a step?
**A:** A single executable task within a stage (run a command, call a script).

**Q:** What is an approval gate?
**A:** A manual/automatic checkpoint requiring sign-off before advancing (common before prod deploy).

**Q:** What is a rollback?
**A:** Reverting to a previous stable release when a deploy fails.

**Q:** What is a pipeline variable?
**A:** A reusable value (e.g. `$ENV`, version) injected at runtime to parameterize stages.

**Q:** What is a secret in a pipeline?
**A:** Sensitive data (API keys, tokens, passwords) stored encrypted, never in flat files.

**Q:** Why store secrets outside flat files?
**A:** To avoid leaking credentials in source control; use **secret stores**/vaults instead.

**Q:** What is GitOps?
**A:** Managing infra/app deployments via **Git** as the single source of truth + automated reconciliation.

**Q:** What is an environment in CD?
**A:** Target runtime (dev/test/staging/prod) where builds are deployed.

**Q:** What is automation/orchestration in CI/CD?
**A:** Using tools (Jenkins, GitLab CI, GH Actions) to run pipeline steps without manual intervention.

**Q:** What is the main goal of a CI pipeline?
**A:** Keep the **main branch** always buildable and tested after every merge.

**Q:** What is an SBOM?
**A:** **Software Bill of Materials** — a list of all components/deps in a build for supply-chain security.

**Q:** What does shift-left mean?
**A:** Moving testing/security earlier in the dev lifecycle (closer to commit) to find issues sooner.

**Q:** What is the difference between a package repository and a container registry?
**A:** Package repo stores libs/binaries (npm, Maven); registry stores container **images** (Docker Hub, ECR).

**Q:** What is an artifact/digital-object repository?
**A:** A store (Artifactory, Nexus) for build outputs reused across pipeline runs and teams.

**Q:** Why separate dev/test/staging/prod environments?
**A:** To validate changes safely before prod and limit blast radius of failures.

---

## SECTION 11 — PRACTICE QUESTIONS

1. Which pipeline stage runs first after a code commit to verify the code actually compiles?
A. Deploy
B. Test
C. Build
D. Rollback
Answer: C — The **Build** stage compiles source into artifacts and is the first validation step.

2. A pipeline automatically releases code to production with no manual sign-off. This is:
A. Continuous Delivery
B. Continuous Deployment
C. Continuous Integration
D. Continuous Testing
Answer: B — **Continuous Deployment** auto-releases to prod; Delivery stops for manual approval.

3. Which artifact type is an immutable, runnable package of app code plus OS libraries built from a Dockerfile?
A. Flat file
B. Package
C. Container image
D. Source repository
Answer: C — A **container image** is the runnable build output from a Dockerfile.

4. A `.gitlab-ci.yml` file that defines the pipeline is an example of a:
A. Package
B. Artifact
C. Flat file
D. Repository
Answer: C — It is a plain-text **flat file** (configuration) describing the pipeline.

5. Scanning source code for vulnerabilities without executing it is known as:
A. DAST
B. SAST
C. IAST
D. SCA
Answer: B — **SAST** (Static Application Security Testing) analyzes code at rest.

6. Which CI/CD concept tests individual functions in isolation?
A. Integration testing
B. Unit testing
C. Functional testing
D. Performance testing
Answer: B — **Unit testing** isolates single functions/modules.

7. A nightly security scan triggered by a cron job is an example of which trigger type?
A. Webhook
B. Manual
C. Scheduled
D. Polling
Answer: C — A **scheduled** (cron) trigger runs on a time basis.

8. Combining a feature branch into the main branch is called a:
A. Fork
B. Merge
C. Clone
D. Tag
Answer: B — A **merge** integrates branch changes into another branch.

9. Before deploying to production, a manager must click "Approve". This checkpoint is a:
A. Step
B. Artifact
C. Approval gate
D. Webhook
Answer: C — An **approval gate** requires human sign-off before advancing.

10. An HTTP callback from GitHub that starts a pipeline when code is pushed is a:
A. Scheduled trigger
B. Manual trigger
C. Webhook trigger
D. Polling trigger
Answer: C — A **webhook** is the event-driven HTTP callback.

11. Which testing type checks how an application performs under heavy load?
A. Functional
B. Unit
C. Performance
D. Integration
Answer: C — **Performance testing** measures load/throughput behavior.

12. A versioned software bundle published to npm or Maven Central is a:
A. Container image
B. Package
C. Flat file
D. Repository
Answer: B — A **package** is a versioned library/binary in a package repo.

13. Storing API keys encrypted and injecting them at runtime (not in the repo) describes a pipeline:
A. Variable
B. Secret
C. Artifact
D. Stage
Answer: B — A **secret** holds sensitive values kept out of source control.

14. Reverting to the previous stable release after a failed deploy is a:
A. Rollback
B. Rollforward
C. Merge
D. Gate
Answer: A — A **rollback** returns to the last known-good release.

15. Managing deployments by keeping desired state in Git and auto-reconciling is:
A. Shift-left
B. GitOps
C. SAST
D. SBOM
Answer: B — **GitOps** uses Git as the source of truth with reconciliation.

16. Which artifact lists all open-source components used in a build for supply-chain security?
A. SBOM
B. SAST
C. DAST
D. SCA
Answer: A — An **SBOM** is the software bill of materials.

17. (Identification — artifact type) A compiled `app-bundle.zip` stored in Nexus for reuse by other teams is classified as a(n):
A. Package
B. Artifact
C. Container image
D. Flat file
Answer: B — It is an **artifact** (a digital object output of a build stage).

18. (Identification — pipeline stage) In a pipeline ordered build → test → approve → deploy, the stage that requires human sign-off is:
A. Build
B. Test
C. Approval gate
D. Deploy
Answer: C — The **approval gate** stage blocks until a human approves.

19. (Identification — artifact type) A `python:3.11-slim` pulled from Docker Hub to run the app is a:
A. Package
B. Container image
C. Flat file
D. Artifact
Answer: B — It is a **container image** from a registry.

20. (Identification — pipeline stage) Running automated checks that verify separate modules work together (app + database) occurs in the:
A. Build stage
B. Integration testing stage
C. Deploy stage
D. Merge stage
Answer: B — **Integration testing** validates how components interact.

---

## SECTION 12 — EXAM PRIORITY

Ranked by likely exam weight for CV0-004 5.2:

| Rank | Sub-objective | Likelihood | Why it shows up |
|------|---------------|-----------|-----------------|
| 1 | CI vs CD (Delivery vs Deployment) | High | Classic definition/distinction question, almost guaranteed |
| 2 | Pipeline stages (build/test/deploy) | High | Scenario "name the missing stage" is a favorite |
| 3 | Triggers (webhook/schedule/manual) | High | Easy to test as identify-the-trigger questions |
| 4 | Testing types (unit/integration/functional/performance/security) | High | Many "which test type" identification items |
| 5 | Security scanning (SAST/DAST/SCA) | High | Shift-left security is heavily emphasized |
| 6 | Workflow (variables/secrets) | High | Secrets-vs-variables is a recurring trap |
| 7 | Automation/orchestration | High | Tools + "why automate" conceptual questions |
| 8 | Artifacts | Med | Identify-the-artifact type questions appear |
| 9 | Repositories (source control) | Med | Branches/merges show up in scenarios |
| 10 | Images (container) | Med | Docker image vs package distinction tested |
| 11 | Packages | Med | Package repo vs registry confusion item |
| 12 | Flat file (pipeline config) | Med | Recognize `.yml`/`Jenkinsfile` as config |
| 13 | Approvals / rollbacks | Med | Gate + rollback recovery scenarios |
| 14 | Environments (dev/test/staging/prod) | Med | Blast-radius / promotion path questions |
| 15 | GitOps / SBOM / SLSA / shift-left | Low | Contextual/newer; appears as enrichment, not core |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

- **CI vs CD:** CI = merge + build + test continuously; CD Delivery = ready + manual approve; CD Deployment = auto-release to prod.
- **Triggers:** Webhook (event/push/PR), Scheduled (cron), Manual (button) — know which starts what.
- **Stages:** Build → Test → (Approval gate) → Deploy; each stage groups steps and can have gates.
- **Steps:** Single executable task inside a stage.
- **Artifacts:** Digital objects (JAR, zip, image) output by builds, stored for reuse.
- **Repositories:** Git SCM (GitHub/GitLab/Bitbucket) holding code + history; branches + merges integrate changes.
- **Images:** Immutable container builds from a Dockerfile; stored in a registry (Docker Hub, ECR).
- **Packages:** Versioned libs/binaries in package repos (npm, Maven, PyPI, NuGet).
- **Flat file:** YAML/JSON pipeline definition (`.gitlab-ci.yml`, `Jenkinsfile`, `workflow.yml`).
- **Testing:** Unit (isolated) → Integration (components together) → Functional (requirements) → Performance (load) → Security.
- **Security scanning:** SAST (static source), DAST (running app), SCA (dependencies/CVEs).
- **Workflow:** Defined structure + automation; use variables to parameterize, secrets to protect credentials.
- **Secrets:** Encrypted, injected at runtime — NEVER in flat files or repos.
- **Approvals:** Manual/auto gates before prod; Rollback reverts to last good release on failure.
- **Environments:** dev → test → staging → prod; limit blast radius via promotion.
- **GitOps:** Git = source of truth; robot reconciles actual vs desired state.
- **SBOM:** Bill of materials for every component; foundation of supply-chain security.
- **Shift-left:** Push test + security earlier (near commit) to cut fix cost.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024-2025, CV0-004-relevant)

- **2024 — SLSA & NIST SSDF go mainstream:** The **Supply-chain Levels for Software Artifacts (SLSA)** framework and NIST SP 800-218 (SSDF) push verifiable, tamper-resistant build provenance, making artifact integrity a standard pipeline requirement rather than a nice-to-have.

- **2024 — SBOM becomes default practice:** Following US EO 14028 / NTIA guidance, **SBOM** generation (CycloneDX, SPDX) is now expected at build time, so every pipeline should emit a component list for vulnerability tracing and license compliance.

- **2024–2025 — GitOps rises for cloud-native CD:** Tools like **Argo CD** and **Flux** drive declarative, Git-as-source-of-truth deployments with automatic drift reconciliation — the dominant pattern for Kubernetes CI/CD.

- **2024 — Container registry security hardening:** Image **signing and verification** via **Sigstore/Cosign** and Notary v2 became common, so pipelines now sign images and enforce signature checks before pull/deploy.

- **2025 — DevSecOps / shift-left security cements:** In-pipeline **SAST/DAST/SCA** and secret scanning are now baseline; "security is everyone's job from commit" is the expected exam-era mindset.

**Reporting note:** First line of this file is `## SECTION9 — INTERVIEW KNOWLEDGE`. Flashcard count = 40. Practice-question count = 20.
