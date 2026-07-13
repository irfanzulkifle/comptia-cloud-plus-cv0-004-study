---
title: CompTIA Cloud+ CV0-004 — Objective 5.1
objective: "5.1 Explain source control concepts."
domain: "5.0 DevOps Fundamentals"
weight: "10% of exam"
tags: [Cloud+, CV0-004, DevOps, Source-Control, Git, Study-Notes]
updated: 2026-07-13
status: in-progress
---

# CompTIA Cloud+ CV0-004 — Objective 5.1
## Explain Source Control Concepts

> **Objective statement:** *"Explain source control concepts."*
> **Domain:** 5.0 DevOps Fundamentals (10% of the exam)
> **What it tests:** Whether you understand the *vocabulary and workflow* of version control (mostly Git). This is the foundation of 5.2 (CI/CD) and 5.4 (DevOps tools) — you can't automate a pipeline (2.4 IaC/code-deploy, 5.2) if you don't know commit/branch/merge/PR. Expect definition + "which action does X" questions.

---

## SECTION 1 — OBJECTIVE OVERVIEW

**In one breath:** Source control (version control) = tracking code changes so teams can collaborate, revert mistakes, and review before shipping.

**The 5.1 sub-objectives (verbatim from the official exam objectives):**

| # | Sub-objective | What it means |
|---|----------------|---------------|
| 5.1.1 | **Version management** | Tracking history; revert/rollback to any point |
| 5.1.2 | **Code review** | Peer inspection of changes before merge |
| 5.1.3 | **Pull request (PR)** | Request to merge a branch via review |
| 5.1.4 | **Code push** | Upload local commits to the remote |
| 5.1.5 | **Code commit** | Save a local snapshot with a message |
| 5.1.6 | **Code merge** | Combine one branch's changes into another |
| 5.1.7 | **Branch management** | Create/organize branches (strategies) |

**Mental model — the Git lifecycle:**
`branch (5.1.7) → commit (5.1.5) → push (5.1.4) → pull request (5.1.3) → code review (5.1.2) → merge (5.1.6) → version management (5.1.1) keeps the whole history reversible`.

---

## SECTION 2 — EXAM KNOWLEDGE

### 5.1.1 — VERSION MANAGEMENT

**Definition:** Tracking every change to code over time so you can see history, compare versions, and **revert/rollback** to a known-good state.

- **Distributed (Git):** every clone is a full repo with history (GitHub/GitLab/Bitbucket).
- **Centralized (SVN/TFS):** one central server holds history; clients check out working copies.
- **Mechanisms:** commit history, **tags** (mark release points), **branches**, revert/reset.
- **Exam angle:** "A bad deploy broke prod — get the last good version back" → **version management** (checkout the prior tag / `git revert`).

### 5.1.2 — CODE REVIEW

**Definition:** Having peers **inspect code changes** before they're merged into the main line.

- **Purpose:** catch bugs early, enforce standards, share knowledge, **catch security issues** before they ship.
- **Forms:** formal review, pair programming, tool-based (PR review comments, Gerrit).
- **Ties to 4.1/4.2:** reviewing code is a quality + security gate (find vulns before merge).
- **Exam angle:** "Stop a vulnerable function reaching production" → **code review** before merge.

### 5.1.3 — PULL REQUEST (PR)

**Definition:** A request to **merge** one branch's changes into another, opened so others can **review** before accepting.

- Carries the diff, discussion, and approvals; often triggers CI checks (5.2).
- Platforms: GitHub PR, GitLab Merge Request, Bitbucket PR.
- **Exam angle:** "Dev finished a feature and wants the team to review + integrate it" → open a **pull request**.

### 5.1.4 — CODE PUSH

**Definition:** Uploading **local commits** from your machine to the **remote** repository.

- `git push <remote> <branch>`; establishes remote tracking.
- **Why:** share work, back it up, trigger CI (5.2) on the shared branch.
- **Exam angle:** "My commits are on my laptop — get them into the team repo" → **push** to remote.

### 5.1.5 — CODE COMMIT

**Definition:** Saving a **snapshot** of your staged changes into the local repository history, with a descriptive message.

- `git commit -m "..."`; commits should be **atomic** (one logical change) with clear messages.
- Local only — not shared until you **push** (5.1.4).
- **Exam angle:** "Save a checkpoint of my work before switching tasks" → **commit** locally.

### 5.1.6 — CODE MERGE

**Definition:** Combining changes from one branch into another.

- **Fast-forward** (no divergent history) vs **3-way merge** (creates a merge commit).
- **Merge conflicts** arise when the same lines changed on both sides — must be resolved manually.
- **Exam angle:** "Bring the finished feature branch into `main`" → **merge** (resolve any conflicts).

### 5.1.7 — BRANCH MANAGEMENT

**Definition:** Creating, naming, and organizing branches so parallel work doesn't collide.

- **Common branches:** `main`/`master` (production), `develop`, `feature/*`, `release/*`, `hotfix/*`.
- **Strategies:** Git Flow (structured), trunk-based (short-lived branches, frequent merge to main).
- **Purpose:** isolate work, enable parallel dev, protect the production line.
- **Exam angle:** "Ten devs shipping features without stepping on each other" → **branch management** (feature branches off main).

> **Commit vs Push vs Merge — the 3 that get swapped:** **commit** = save locally · **push** = send local→remote · **merge** = combine branches. A commit is NOT visible to the team until pushed; a push does NOT merge into main (that's the PR→merge step).



## SECTION 3 — REAL WORLD CLOUD EXAMPLES

### 3.1 Bad Prod Deploy Reverted via Version Management
A fintech team in Kuala Lumpur pushed a new payment-gateway release to production on a Friday evening. Within 20 minutes, the monitoring dashboard lit up with 5xx errors — a misconfigured retry loop was hammering the bank API and tripping rate limits. Because every release was **tagged** (`v2.3.1`) and the previous stable build was still tagged `v2.3.0`, the on-call engineer did not panic or re-clone anything. Using **version management**, they checked out the last known-good tag, rebuilt the artifact, and redeployed it in under 10 minutes. The broken commit stayed in history (no history was destroyed), so the root cause could be investigated the next morning. This is exactly what **version management** buys you: a reliable way to roll forward or roll back to a known state using tags, branches, and a clean commit history. **Exam angle:** reverting a bad deploy is done through tags/checkout/reset — not by re-cloning the repo.

### 3.2 Vulnerable Function Caught in Code Review Before Merge
A junior developer opened a **pull request** to add CSV export to a reporting service. In **code review**, a senior reviewer spotted a function that built an SQL query by string concatenation using raw user input — a classic SQL injection risk. The reviewer left an inline comment, requested changes, and the PR was **not merged** until the developer switched to a parameterized query. No vulnerable code ever reached `main`, so no incident, no customer data exposure, no emergency rollback. This shows the real value of **code review**: it is a quality gate that happens *before* merge, catching security flaws, logic errors, and style/standards issues while they are still cheap to fix. **Exam angle:** code review is a pre-merge gate, not a post-merge formality.

### 3.3 Feature Built Through Git Flow
The team follows **Git Flow**. A developer creates a `feature/user-audit-log` branch off `develop` and commits their work locally. When the feature is ready, they **push** the branch to GitHub and open a **pull request** targeting `develop`. CI runs, two teammates **review** the code, and once approved it is **merged** into `develop`. Later, when enough features accumulate, a `release/v1.4` branch is cut from `develop` for hardening and bug-fixes, then merged into `main` and back into `develop`. A critical bug in prod gets a `hotfix/...` branch off `main`, merged to both `main` and `develop`. Git Flow keeps long-lived lines of work cleanly separated. **Exam angle:** in Git Flow you branch from `develop` (features) and merge back to `develop` (or `main` for releases/hotfixes) — never code directly on `main`.

### 3.4 Resolving a Merge Conflict During Integration
Two developers both edited the same `config.yaml` on different branches that integrated into `develop`. When the second **merge** ran, Git reported a **merge conflict** — overlapping changes on the same lines. The repo was *not* broken; Git simply paused and marked the conflict with `<<<<<<<` / `=======` / `>>>>>>>` markers. The engineer opened the file, decided which settings to keep (and combined a few), removed the markers, **staged** the resolved file, and committed the merge. The integration continued normally. Merge conflicts are routine, expected, and resolved by humans — they do not mean the repository is corrupted. **Exam angle:** a conflict = overlapping edits to resolve manually, not a broken/unsafe repo.

### 3.5 Trunk-Based Branching for Fast CI
A SaaS startup uses **trunk-based development**: everyone works in short-lived branches off `main` (or directly on `main`) and merges back multiple times per day. Feature flags hide incomplete work so `main` is always deployable. Because branches are tiny and integrated fast, **CI** runs quickly and catches breaks early — no long-lived `develop` or `release` branches to reconcile. This maximizes throughput and keeps the build green. It contrasts with Git Flow's heavier model. **Exam angle:** trunk-based = short-lived branches + frequent merges to `main` + fast CI; it trades Git Flow's structure for speed.

## SECTION 4 — COMPARISON TABLES

### 4.1 Commit vs Push vs Merge
| Action | Where it happens | What it does | Visibility |
|--------|------------------|--------------|------------|
| **Commit** | Local repo only | Records a snapshot of staged changes in local history | Private until pushed |
| **Push** | Local → remote | Uploads local commits to the remote repository | Makes commits visible to the team |
| **Merge** | Local or remote | Combines two branches' histories into one | Creates a merge commit (or fast-forwards) |

### 4.2 Centralized (SVN) vs Distributed (Git) VCS
| Aspect | Centralized (SVN) | Distributed (Git) |
|--------|-------------------|-------------------|
| History location | On central server only | Full history on every clone |
| Offline work | Limited (need server for many ops) | Full commit/branch/merge offline |
| Single point of failure | Yes (server down = stuck) | No (any clone is a backup) |
| Branching cost | Relatively heavy | Lightweight and cheap |

### 4.3 Git Flow vs Trunk-Based
| Aspect | Git Flow | Trunk-Based |
|--------|----------|-------------|
| Branch model | Long-lived `main`, `develop`, `release`, `hotfix` | Mostly `main` + short-lived feature branches |
| Merge frequency | Lower (batched releases) | Very high (multiple times/day) |
| CI speed | Slower (bigger integrations) | Fast (small, frequent) |
| Best for | Large teams, scheduled releases | SaaS/CI-heavy, fast delivery |

### 4.4 Fast-Forward vs 3-Way Merge
| Aspect | Fast-Forward | 3-Way Merge |
|--------|--------------|-------------|
| When used | Target has no new commits since branch | Both branches diverged |
| New commit? | No — pointer just moves | Yes — a merge commit is created |
| History shape | Linear | Shows the branch point |

### 4.5 Pull Request vs Code Review
| Concept | Pull Request (PR) | Code Review |
|---------|-------------------|-------------|
| What it is | A **request** to merge a branch | The **act** of inspecting/evaluating the code |
| Initiated by | Author of the branch | Reviewers/peers |
| Outcome | Approved → merged (manually or by rule) | Feedback, approval, or change requests |

### 4.6 Branch Purpose Mapping
| Branch | Purpose | Created from | Merged into |
|--------|---------|--------------|-------------|
| `main` | Stable, production-ready code | — | — |
| `develop` | Integration line for features (Git Flow) | `main` | `main` (via release) |
| `feature` | New feature work | `develop` | `develop` |
| `release` | Hardening a version for ship | `develop` | `main` + `develop` |
| `hotfix` | Urgent prod fix | `main` | `main` + `develop` |

## SECTION 5 — EXAM TRAPS

**Trap 1 — "A commit is immediately visible to the whole team."**
- **Scenario:** A developer commits a fix locally and tells the team it's done.
- **Correct:** The commit lives only in the **local** repo until **pushed**.
- **Why wrong:** Committing records locally; teammates can't see it until you `git push` to the remote.

**Trap 2 — "Pushing a branch automatically merges it into main."**
- **Scenario:** You push your feature branch and assume main now has your code.
- **Correct:** Push only **uploads** commits; merging requires a **PR/merge**.
- **Why wrong:** Push ≠ merge. `main` is unchanged until the branch is explicitly merged (via PR approval).

**Trap 3 — "A merge and a commit are the same thing."**
- **Scenario:** The question asks what creates a merge commit.
- **Correct:** A **3-way merge** creates a merge commit; a plain commit is a single snapshot.
- **Why wrong:** A commit snapshots changes; a merge combines histories (and may add a merge commit). Don't conflate them.

**Trap 4 — "Code review happens after the code is merged."**
- **Scenario:** A bug slips to prod; the team says they'll review it now.
- **Correct:** Code review is a **pre-merge** quality gate.
- **Why wrong:** Review should occur *before* merge, so bad code never reaches `main`. Post-merge "review" is too late.

**Trap 5 — "Branch management just means creating a branch."**
- **Scenario:** "We do branch management — I create a branch when I start."
- **Correct:** Branch management is the **strategy**: naming, lifetime, merge targets, cleanup.
- **Why wrong:** Creating a branch is one action; management is the governing policy (Git Flow vs trunk-based, etc.).

**Trap 6 — "To revert a bad deploy you must re-clone the repo."**
- **Scenario:** Prod breaks; the engineer re-clones from scratch to "reset."
- **Correct:** Use **version management** — checkout a prior tag, or `reset`/`revert`.
- **Why wrong:** History is already there; you roll back via tags/reset, not by re-cloning.

**Trap 7 — "Opening a PR means it gets merged automatically."**
- **Scenario:** A trainee opens a PR and walks away expecting deploy.
- **Correct:** A PR needs **review and approval**, then an explicit merge.
- **Why wrong:** PR = request to merge, not auto-merge. Approval/review gates are required first.

**Trap 8 — "Git is a centralized version control system."**
- **Scenario:** Exam asks to classify Git.
- **Correct:** Git is **distributed** — every clone has the full history.
- **Why wrong:** Centralized (SVN) keeps history only on the server; Git distributes it to every developer.

**Trap 9 — "You write your daily code directly on the main branch."**
- **Scenario:** A dev commits features straight to `main`.
- **Correct:** Use **feature branches**; keep `main` stable/production-ready.
- **Why wrong:** Direct commits to `main` bypass review and risk breaking the stable line.

**Trap 10 — "A merge conflict means the repository is corrupted/broken."**
- **Scenario:** Git reports a conflict and the dev thinks the repo is ruined.
- **Correct:** A conflict is just **overlapping edits** to resolve manually.
- **Why wrong:** Git pauses safely with conflict markers; resolve, stage, commit — repo is fine.

**Trap 11 — "A tag and a branch are the same thing."**
- **Scenario:** Question asks to mark a release point.
- **Correct:** A **tag** is a fixed label on a commit; a **branch** is a moving pointer.
- **Why wrong:** Branches move as you commit; tags stay pinned to one commit (ideal for releases/rollbacks).

**Trap 12 — "Pushing requires code review/approval."**
- **Scenario:** A student thinks every push triggers a review.
- **Correct:** **Push is just an upload** to the remote; review happens on the PR.
- **Why wrong:** Push has no built-in review gate — that's what pull requests provide.

**Trap 13 — "SVN keeps the full project history on your local machine."**
- **Scenario:** Comparing SVN and Git offline capabilities.
- **Correct:** Only **Git** has full local history; SVN stores history on the central server.
- **Why wrong:** In SVN you must reach the server for history; Git gives it locally on every clone.

**Trap 14 — "Force-push is always safe to use."**
- **Scenario:** Someone force-pushes to `main` to "clean up" history.
- **Correct:** **Force-push can overwrite/rewrite shared history** and lose others' work.
- **Why wrong:** It rewrites remote history; use cautiously (never on shared branches without coordination).

**Trap 15 — "Code review only checks for bugs."**
- **Scenario:** Reviewer approves because there are no obvious bugs.
- **Correct:** Review also covers **standards, security, readability, and design**.
- **Why wrong:** Review is broader than bug-hunting — it enforces security, style, and architecture too.


## SECTION 6 — PERFORMANCE-BASED QUESTION (PBQ) PREP

Practical, hands-on scenarios you could see in the CV0-004 performance-based questions. For each, walk the steps as if you are at the keyboard, then test yourself with the **Bonus question**.

### 6.1 — Revert a bad production deployment via version management

**Scenario**
A teammate merged a change to `main` that broke the production API. You are on-call and must roll production back to the last known-good state without losing the history or the fix work that was already in progress.

**Walkthrough**
1. **Identify the bad commit** — list history and find the commit hash just before the bad deploy:
   ```bash
   git log --oneline -10
   ```
2. **Check what is currently live** so you can prove the rollback target:
   ```bash
   git tag          # e.g. v1.4.2 was the last good release
   ```
3. **Revert non-destructively** using `git revert` (preferred in shared/prod branches — it adds a NEW commit instead of rewriting history):
   ```bash
   git revert <bad-commit-hash>
   git push origin main
   ```
   - Alternatively, if you deploy by tag, just re-point the release tag to the last good commit and redeploy **v1.4.2**.
4. **Verify** the API health check passes after the redeploy, then open a **pull request** later to properly fix the root cause.

**Bonus question**
Why use `git revert` rather than `git reset --hard` on a shared production branch?
> **Answer:** `reset --hard` rewrites shared history and can break every teammate's local clone; `revert` is safe on shared branches because it only adds an inverse commit and preserves an auditable trail.

---

### 6.2 — Open a PR and get a vulnerable function caught in review

**Scenario**
You wrote a new function that reads a config file. A reviewer must catch that it uses unsafe `eval()` on user input before it ever reaches `main`.

**Walkthrough**
1. **Commit locally** on a feature branch:
   ```bash
   git checkout -b feature/config-loader
   git add config_loader.py
   git commit -m "Add config loader"
   ```
2. **Push** the branch to the remote:
   ```bash
   git push -u origin feature/config-loader
   ```
3. **Open a Pull Request (PR)** / Merge Request — this is the **gateway**, not a merge. Fill in the description and request a reviewer.
4. **Code review** catches the `eval()` call; the reviewer leaves a comment and requests changes.
5. **Fix** the function (replace `eval()` with `json.load`), commit, and push — the PR updates automatically. Reviewer approves, then **merge**.

**Bonus question**
At which step does the vulnerable code actually enter the shared `main` branch — when you push, or when the PR is merged?
> **Answer:** Only when the **PR is merged**. Pushing a branch and opening a PR does NOT change `main`; review happens before merge.

---

### 6.3 — Merge a finished feature branch into main resolving a conflict

**Scenario**
Branch `feature/login` is done. While you were working, someone merged a change to `main` that touches the same `auth.py` file. Your merge now shows a **conflict**.

**Walkthrough**
1. **Stay current** — bring `main` into your branch first:
   ```bash
   git checkout feature/login
   git fetch origin
   git merge origin/main
   ```
2. **Git reports a conflict** in `auth.py`. Open the file; conflict markers look like:
   ```
   <<<<<<< HEAD
   your code
   =======
   their code
   >>>>>>> origin/main
   ```
3. **Resolve** by editing the file to keep the correct combined logic, then delete the `<<<<<<<` / `=======` / `>>>>>>>` markers.
4. **Stage and commit the resolution:**
   ```bash
   git add auth.py
   git commit -m "Resolve auth.py conflict with main"
   ```
5. **Push and open/complete the PR/merge** into `main`.

**Bonus question**
After fixing the conflict, which command marks the file as "resolved" so Git will let you finish the merge?
> **Answer:** `git add <file>` — staging the resolved file tells Git the conflict is handled, after which you `git commit` the merge.

---

### 6.4 — Set up trunk-based branching for a CI pipeline

**Scenario**
Your team wants short-lived feature branches that automatically build and test on every push, then merge quickly back to a single `main` (trunk). Configure the **branch strategy** and CI trigger.

**Walkthrough**
1. **Define the strategy** — **trunk-based development**: one long-lived `main` (trunk), with short-lived feature branches (hours to a couple of days, never long-lived).
2. **Create a short-lived branch** off `main`:
   ```bash
   git checkout -b feature/ci-hook main
   ```
3. **Add CI config** (e.g. `.github/workflows/ci.yml` on GitHub) that triggers on **push** and **pull_request** to `main`:
   ```yaml
   on:
     push:
       branches: [ main ]
     pull_request:
       branches: [ main ]
   jobs:
     build-test:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
         - run: make build && make test
   ```
4. **Push** and open a PR; CI runs automatically. On green, **merge** to trunk.
5. **Keep branches short** — delete the feature branch after merge to avoid drift.

**Bonus question**
In trunk-based development, why should feature branches be short-lived rather than kept for weeks?
> **Answer:** Long-lived branches diverge from `main`, causing painful **merge conflicts** and defeating the fast feedback and CI safety net that trunk-based development exists to provide.

---

### 6.5 — Identify the right action given a described state

**Scenario**
The exam may describe a state and ask which source-control action applies. Match the state to the correct verb.

**Walkthrough — decision table**

| Described state | Correct action | Why |
|---|---|---|
| You edited files on your laptop, not yet saved to repo | **commit** | A commit is a **local snapshot** of staged changes |
| You committed locally and want it on the server | **push** | Push is **upload-only** — it does not review or merge |
| You want teammates to review before it hits `main` | **open a PR** | The PR is a **gateway**, not a merge |
| Review passed and you want it in `main` | **merge** | Merge integrates the branch into the target |
| Two branches changed the same lines | **resolve conflict, then merge** | Manual resolution required before integration |

**Bonus question**
A developer says "I pushed my fix so it's in production now." What is wrong with that statement?
> **Answer:** **Push** only uploads the commit to the remote; it does not merge into `main` and certainly does not deploy. Production changes require a **merge** (and usually a deploy step) after review.

---

## SECTION 7 — MEMORY AIDS

Quick hooks to lock in the 5.1 sub-objectives. Bold the term, remember the one-liner.

### 5.1.1 — version-mgmt-revert
- **Version management** = tracking every change with tags/commits so you can go back.
- **Revert rule:** `git revert` = safe undo on shared branches; `git reset --hard` = destructive, local only.
- Mnemonics: *Revert = Reverse (add a fix-commit). Reset = Rewind (erase history).*

### 5.1.3 — PR=gateway-not-merge
- A **Pull Request (PR)** / Merge Request is a **review gateway**, NOT a merge.
- Opening a PR does **not** change `main`. Only the **merge** after approval changes `main`.
- Mnemonics: *PR = Please Review (first), then Merge.*

### 5.1.4 — push=upload-only
- **Push** = upload your local commits to the remote. That's it.
- Push does **not** trigger review and does **not** merge.
- Mnemonics: *Push = Post Up Safely (just copies commits).*

### 5.1.5 — commit=local-snapshot
- A **commit** is a **local snapshot** of staged changes — it lives on your machine until you push.
- Nothing leaves your computer on commit; it's your personal save point.
- Mnemonics: *Commit = Camera (snapshot here, now).*

### 5.1.6 — merge-conflict-resolve
- A **merge conflict** happens when two branches edit the same lines.
- Resolve by editing the file, removing `<<<<<<<`/`=======`/`>>>>>>>`, then `git add` + `git commit`.
- Mnemonics: *Conflict = Choose (pick the right combo, mark resolved).*

### 5.1.7 — branch-strategy
- **Branch management**: pick a strategy — e.g. **trunk-based** (one `main`, short-lived branches) vs long-lived feature branches.
- Short-lived branches + CI = fast, safe integration.
- Mnemonics: *Strategy = Streamline (keep branches short, merge often).*

### 5.1.8 — ASCII lifecycle (edit → commit → push → PR → review → merge)

```
   edit
    |
    v
 commit  ........  local snapshot (your machine only)
    |
    v
  push  .........  upload-only (remote now has your commits)
    |
    v
   PR  ..........  gateway / request review (main NOT changed yet)
    |
    v
 review  .......  teammates comment + approve
    |
    v
  merge  ........  integrated into target branch (e.g. main)
```

**One-line lifecycle chant:** *edit, commit, push, PR, review, merge* — say it in order and you can never mix up the verbs on exam day.

---

## SECTION 8 — CLOUD PROVIDER MAPPING

All three major hosts are **Git-underlying** — the concepts (commit, push, PR, merge, branch) are identical; only the UI labels and CI triggers differ.

| Concept | **GitHub** | **GitLab** | **Bitbucket** |
|---|---|---|---|
| Git host / repo | GitHub Repo | GitLab Project | Bitbucket Repo |
| Code review request | **Pull Request (PR)** | **Merge Request (MR)** | **Pull Request (PR)** |
| Default integration branch | `main` (was `master`) | `main` | `main` |
| CI config file | `.github/workflows/*.yml` | `.gitlab-ci.yml` | `bitbucket-pipelines.yml` |
| CI trigger event | `push` / `pull_request` | `push` / `merge_request` | `push` / `pull-request` |
| Conflict resolution | In-browser or local `git merge` | In-browser or local `git merge` | In-browser or local `git merge` |
| Revert a deploy | `git revert` + redeploy tag | `git revert` + redeploy tag | `git revert` + redeploy tag |
| Short-lived branch model | Feature branch → PR → CI → merge | Feature branch → MR → CI → merge | Feature branch → PR → Pipelines → merge |

### Cross-provider reminders (all Git-underlying)
- **The verbs never change.** `commit`, `push`, `PR/MR`, `review`, `merge`, `revert`, `conflict-resolve` mean the same thing on every platform — the CV0-004 exam tests the **concept**, not the button location.
- **PR vs MR is only a label.** GitHub/Bitbucket call it *Pull Request*; GitLab calls it *Merge Request*. Both are the **review gateway**, not a merge.
- **CI trigger names differ slightly** (`pull_request` on GitHub, `merge_request` on GitLab, `pull-request` on Bitbucket) but all fire on the same event: a review request against the trunk.
- **Conflicts and reverts are universal.** The `<<<<<<<` markers and `git revert` behave identically because the engine underneath is **Git** everywhere.
- **Branch strategy is provider-agnostic.** Trunk-based development works the same on all three — only the pipeline YAML file name changes.


## SECTION 9 — INTERVIEW KNOWLEDGE

### Conceptual Q&A

**Q: What is the difference between a centralized and a distributed version control system?**
**A:** A **centralized** system (e.g., SVN) keeps one single source of truth on a server; every action needs the server. A **distributed** system (e.g., **Git**) gives every developer a full local copy of the repo's history, so commits, branching, and diffing work offline. Git is the dominant model because it is fast, allows offline work, and supports cheap branching.

**Q: What is the difference between `git commit` and `git push`?**
**A:** `git commit` saves a snapshot to your **local** repository's history only — teammates cannot see it yet. `git push` uploads those local commits to a **remote** repository (e.g., GitHub) so others can pull them. A common mistake is committing locally and forgetting to push, so the change never reaches the team.

**Q: Why does `git merge` sometimes create a merge commit?**
**A:** When two branches have diverged (both changed since the last common ancestor), Git must reconcile them. A **merge commit** is an extra commit with two parents that records the union of both lines of history. Fast-forward merges happen when the target branch hasn't moved, so no merge commit is needed.

**Q: What is the purpose of a code review?**
**A:** A **code review** is a peer inspection of proposed changes before they merge. It catches bugs, enforces standards, shares knowledge, and reduces risk. It is a key **shift-left** practice — finding defects early is far cheaper than fixing them in production.

### Scenario Questions

**Scenario 1:** Your teammate says, "I finished the login feature on my laptop but nobody else can see it." What is the missing step and the correct Git action?
**Answer:** They committed locally but never pushed. The correct action is `git push` to send the local commits to the shared remote so the team can access them. If the remote has new commits, they must first `git pull` (or `git fetch` + `git merge`) to avoid a rejected push.

**Scenario 2:** Two developers edited the same line of `config.yaml` on two different branches. You need both changes in `main` but Git reports a conflict during merge. What should you do?
**Answer:** Perform `git merge`, then resolve the **conflict** by manually editing `config.yaml` to keep the correct lines, mark it resolved with `git add`, and create the **merge commit**. Do not blindly accept either side — pick or combine the intended values, then commit.

**Scenario 3:** A junior dev wants their bug fix checked by seniors before it touches production code. Which workflow object should they open, and on which branch should it be based?
**Answer:** They should open a **Pull Request (PR)** (or Merge Request) targeting `main`/`develop`, branching from a feature branch. The PR triggers review and automated checks; only after approval should it be merged.

### Explain-It-Simply Prompts

- **Explain version control like you're 10:** "It's like a save-history for your project. Every time you 'save a checkpoint,' the computer remembers exactly what changed, who changed it, and when — so you can always go back if you break something."
- **Explain a branch simply:** "A branch is a copy of the project where you can try new ideas without messing up the real thing. When the idea works, you glue it back in with a merge."
- **Explain a pull request simply:** "A PR is a polite note saying 'I made changes, please look before we add them to the main book.' Others comment, you fix, then it gets merged."
- **Explain code review simply:** "It's asking a friend to double-check your homework before you hand it in, so silly mistakes don't become big problems later."

---

## SECTION 10 — FLASHCARDS

**Q:** What does version control (source control) track? **A:** Changes to files over time, by whom, and when — enabling history, rollback, and collaboration.

**Q:** What is a repository (repo)? **A:** A storage location (local and/or remote) holding all project files and their full change history.

**Q:** What is the difference between centralized and distributed VCS? **A:** Centralized (SVN) has one server copy; distributed (Git) gives every dev a full local history.

**Q:** What is a commit? **A:** A saved snapshot of changes to the local repo with a message, author, and timestamp.

**Q:** What makes a good commit message? **A:** Short summary, explains WHY, uses imperative mood (e.g., "Fix login timeout").

**Q:** What is the staging area (index)? **A:** The middle step where you select which changed files go into the next commit.

**Q:** What command adds files to staging? **A:** `git add <file>` (or `git add .` for all).

**Q:** What command records a commit? **A:** `git commit -m "message"`.

**Q:** What is a branch? **A:** A lightweight movable pointer to a commit, letting you work in isolation from other branches.

**Q:** What is the default main branch typically called? **A:** `main` (formerly `master`).

**Q:** How do you create a new branch? **A:** `git branch <name>` then `git switch <name>` (or `git checkout -b <name>`).

**Q:** What is branch management? **A:** Creating, naming, switching, deleting, and organizing branches (e.g., feature/release/hotfix).

**Q:** What is a naming convention example for branches? **A:** `feature/login`, `bugfix/timeout`, `release/v1.2`, `hotfix/crash`.

**Q:** What is `git merge`? **A:** Combines changes from one branch into another, creating a merge commit if histories diverged.

**Q:** What is a merge conflict? **A:** When Git can't auto-combine changes (same line edited); needs manual resolution.

**Q:** How do you resolve a merge conflict? **A:** Edit conflicted files, remove markers, `git add`, then commit the merge.

**Q:** What is a fast-forward merge? **A:** When target hasn't changed, the pointer just moves forward — no merge commit created.

**Q:** What is a rebase (conceptually)? **A:** Replays your commits onto another base, creating a linear history (avoid on shared branches).

**Q:** What is a push? **A:** Uploads local commits to a remote repository so others can access them.

**Q:** What is `git push` used for? **A:** Sharing your committed work to GitHub/GitLab/etc.

**Q:** What command downloads and integrates remote changes? **A:** `git pull` (= `git fetch` + `git merge`).

**Q:** What is `git fetch`? **A:** Downloads remote changes without merging them — safe to inspect first.

**Q:** What is a pull request (PR)? **A:** A request to merge changes from one branch into another, triggering review.

**Q:** What is a merge request (MR)? **A:** GitLab's term for the same concept as a pull request.

**Q:** What happens during code review? **A:** Peers comment on the PR, request changes, and approve before merge.

**Q:** Why does code review matter? **A:** Catches bugs early, enforces standards, shares knowledge, reduces risk (shift-left).

**Q:** What are CI checks in a PR? **A:** Automated build/test/scan pipelines that run before merge is allowed.

**Q:** What is the difference between commit and push? **A:** Commit = local save; push = share to remote.

**Q:** What is the difference between PR and merge? **A:** PR = request for review/merge; merge = the act of combining branches.

**Q:** What is a fork? **A:** A personal server-side copy of a repo to propose changes via PR (common in open source).

**Q:** What is a tag? **A:** A fixed label on a commit, usually for releases (e.g., v1.0.0).

**Q:** What is a release branch? **A:** A branch cut to prepare and stabilize a version for deployment.

**Q:** What is a hotfix branch? **A:** A branch made directly from production to urgently patch a live bug.

**Q:** What is `.gitignore`? **A:** A file listing paths Git should not track (logs, secrets, build output).

**Q:** Why keep secrets out of version control? **A:** Committed secrets leak permanently into history; use env vars / secret managers.

**Q:** What is a remote? **A:** A named pointer to another repo (usually `origin`) hosted on a server.

**Q:** What is `git clone`? **A:** Copies a remote repo (with history) to your local machine.

**Q:** What is `git status`? **A:** Shows working tree state: staged, unstaged, and untracked files.

**Q:** What is `git diff`? **A:** Shows line-by-line differences between states (working vs staged, branches).

**Q:** What is `git log`? **A:** Displays commit history (author, date, message, hash).

**Q:** What is `git revert`? **A:** Creates a new commit that undoes a previous commit (safe, non-destructive).

**Q:** What is `git reset`? **A:** Moves the branch pointer (can unstage or undo — use with care on shared history).

**Q:** What is trunk-based development? **A:** Short-lived branches, frequent merges to `main`, small PRs — a modern best practice.

**Q:** What is the benefit of small, frequent commits/PRs? **A:** Easier review, faster feedback, simpler blame/rollback.

---

## SECTION 11 — PRACTICE QUESTIONS

1. You finished coding a feature on your laptop and saved it with `git commit`, but your teammate says they still can't see it. Which Git action is missing?
   A) `git merge`
   B) `git push`
   C) `git branch`
   D) `git clone`
   Answer: B — A commit only writes to your local repo; `git push` uploads it to the remote so others can access it.

2. Two branches both edited the same line and Git halts with conflict markers during a merge. What is the correct next step?
   A) Delete the branch and start over
   B) Manually edit the file, `git add`, then commit the merge
   C) Run `git push` to overwrite
   D) Ignore it and continue coding
   Answer: B — Conflicts require manual resolution, staging the fixed file, then committing the merge.

3. A junior developer wants seniors to inspect their bug fix before it reaches production. Which object should they open?
   A) A commit
   B) A pull request (PR)
   C) A tag
   D) A clone
   Answer: B — A PR requests review and approval before changes merge into the target branch.

4. You want to test a new idea without affecting the stable `main` branch. Which action fits best?
   A) `git commit` directly to main
   B) Create a new branch, then commit to it
   C) `git push` to main
   D) Delete main
   Answer: B — Branching isolates experimental work from the main line until it's ready.

5. Which term describes combining two branches where the target hasn't changed, so no merge commit is created?
   A) Fast-forward merge
   B) Rebase conflict
   C) Squash commit
   D) Fork
   Answer: A — Fast-forward simply moves the pointer forward when there's no divergence.

6. What is the primary purpose of a code review?
   A) To deploy code to production
   B) To catch defects early, enforce standards, and share knowledge
   C) To delete old branches
   D) To compile the project
   Answer: B — Reviews are a shift-left practice that reduces cost of defects.

7. In Git, what does the staging area (index) do?
   A) Stores the remote copy
   B) Holds files selected to go into the next commit
   C) Automatically merges branches
   D) Backs up the repo to the cloud
   Answer: B — You `git add` to staging, then `git commit` snapshots what's staged.

8. Which command downloads remote changes WITHOUT merging them?
   A) `git pull`
   B) `git push`
   C) `git fetch`
   D) `git merge`
   Answer: C — `fetch` retrieves history safely so you can inspect before merging.

9. A team uses `feature/`, `release/`, and `hotfix/` prefixes for branches. This is an example of:
   A) Distributed version control
   B) Branch management / naming conventions
   C) Code review
   D) Tagging
   Answer: B — Organized branch naming is core to branch management.

10. What is the difference between `git revert` and `git reset` in safe practice?
    A) Both delete history permanently
    B) `revert` adds a new undo commit; `reset` moves the pointer and can rewrite history
    C) They are identical
    D) Neither affects commits
    Answer: B — `revert` is safe on shared history; `reset` can rewrite/lose commits.

11. Which Git action fits: "I corrected a typo and want to record it in my local history with a message"?
    A) `git push`
    B) `git commit`
    C) `git merge`
    D) Open a PR
    Answer: B — Committing records a local snapshot; push/PR/merge are separate steps.

12. Which Git action fits: "I want to share my already-committed local work with the whole team on GitHub"?
    A) `git commit`
    B) `git push`
    C) `git merge`
    D) Code review
    Answer: B — Push uploads local commits to the remote repository.

13. Which Git action fits: "I want peers to formally approve my changes before they enter the main branch"?
    A) `git commit`
    B) `git push`
    C) Open a pull request
    D) `git clone`
    Answer: C — A PR is the review/approval gate before merging.

14. Which Git action fits: "I want to combine the approved feature branch into `main`"?
    A) `git merge`
    B) `git fetch`
    C) `git branch`
    D) `git status`
    Answer: A — Merge integrates one branch's changes into another.

15. What does `.gitignore` do?
    A) Encrypts the repo
    B) Lists files Git should not track
    C) Creates a remote
    D) Resolves conflicts
    Answer: B — It keeps logs, secrets, and build artifacts out of version control.

16. Why should API keys and passwords never be committed to a repo?
    A) They slow down Git
    B) They become part of permanent history and can leak
    C) Git rejects them automatically
    D) They increase commit count
    Answer: B — Secrets in history are hard to fully remove and risk exposure.

17. What is a tag most commonly used for?
    A) Daily backups
    B) Marking release points (e.g., v1.0.0)
    C) Creating branches
    D) Storing secrets
    Answer: B — Tags label specific commits, usually for releases.

18. In trunk-based development, teams typically:
    A) Keep long-lived feature branches for months
    B) Use short-lived branches and merge to main frequently
    C) Avoid commits
    D) Never use PRs
    Answer: B — Trunk-based development favors small, frequent merges to reduce integration pain.

19. What does `git clone` do?
    A) Deletes a remote repo
    B) Copies a remote repo with its full history to local
    C) Merges two branches
    D) Stages files
    Answer: B — Clone creates a local working copy including all history.

20. A hotfix branch is best described as:
    A) A long experiment branch
    B) A branch cut from production to urgently patch a live bug
    C) A branch for documentation only
    D) The same as a release branch
    Answer: B — Hotfix branches address urgent production issues directly.

---

## SECTION 12 — EXAM PRIORITY

| Rank | Sub-Objective | Exam Likelihood | Why |
|------|---------------|-----------------|-----|
| 1 | **Commit** | High | Core concept; frequently tested on what a commit is vs push/PR. |
| 2 | **Push** | High | Common scenario: "committed but teammates can't see it" → push. |
| 3 | **Pull Request (PR)** | High | Review/approval workflow is a favorite scenario question. |
| 4 | **Merge** | High | Conflicts, fast-forward, and merge commits are heavily tested. |
| 5 | **Branch Management** | High | Naming conventions (feature/release/hotfix) appear in PBQs. |
| 6 | **Code Review** | Medium | Tested conceptually (why it matters, shift-left benefits). |
| 7 | **Version Management** | Medium | Definitions (centralized vs distributed, repo, history) — foundational but broader. |

---

## SECTION 13 — OBJECTIVE SUMMARY (1-PAGE CRAM SHEET)

- **Version Management:** VCS tracks who changed what/when; Git is distributed (full local history) vs SVN centralized (server-only).
- **Commit:** Local snapshot with message/author/time; `git add` → stage, `git commit` → save. Commit ≠ push.
- **Push:** `git push` uploads local commits to the remote (GitHub/GitLab) so the team can see them.
- **Pull Request (PR):** Review/approval gate before merging; triggers CI checks; GitLab calls it a Merge Request.
- **Merge:** Combines branches; conflicts need manual fix + `git add` + commit; fast-forward when no divergence.
- **Branch Management:** Isolate work; use `feature/`, `release/`, `hotfix/` naming; delete stale branches.
- **Code Review:** Peer inspection before merge; catches bugs early, enforces standards, shares knowledge (shift-left).

Quick distinction: **commit** = save locally · **push** = share to remote · **PR** = request review · **merge** = combine branches.

---

## SECTION 14 — LATEST INDUSTRY UPDATES (2024-2025, CV0-004-relevant)

- **2024 — Trunk-Based Development & Shift-Left Go Mainstream:** Industry surveys (State of DevOps / DORA 2024) show elite teams using short-lived branches and frequent merges to `main`, with code review and automated checks as the primary quality gate — directly reinforcing the commit/PR/merge sub-objectives.
- **2024 — AI in Code Review (GitHub Copilot & Copilot for PRs):** GitHub expanded AI-assisted pull request summaries and review suggestions (Copilot for Pull Requests / code review features), changing how teams triage PRs — relevant to the code review and PR workflow objectives.
- **2024 — Secret Scanning & Push Protection Default-On:** GitHub made **push protection** and secret scanning broadly default/enforced, blocking commits that contain credentials — a real-world reason why "never commit secrets" matters under version management.
- **2025 — Supply-Chain Security & Signed Commits:** Growing adoption of **GPG/Sigstore-signed commits** and verified provenance (SLSA) to protect the software supply chain; ties into version control integrity and the "who changed what" audit trail.
- **2025 — Platform Engineering & GitOps:** Git as the single source of truth for infrastructure (GitOps via ArgoCD/Flux) rose in enterprise cloud deployments, extending source-control concepts from app code to cloud config — relevant context for Cloud+ candidates.
