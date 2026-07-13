## SECTION 1 — Exam Objectives

**Objective 5.1 — Explain source control concepts.** Source control (also called version control) is the discipline of tracking every change to code so that teams can collaborate safely, audit history, and undo mistakes.

- CompTIA treats this as the vocabulary foundation for CI/CD (5.2) and DevOps tooling (5.4): you cannot automate a pipeline if you do not understand commit, branch, merge, and pull request.
- Expect definition-style and "which action does X" questions.
- **5.1.1 Version management** — Track history; roll back to any prior state.
- **5.1.2 Code review** — Peer inspection of changes before they merge.
- **5.1.3 Pull request (PR)** — A request to merge a branch, gated by review.
- **5.1.4 Code push** — Upload local commits to the shared remote.
- **5.1.5 Code commit** — Save a local snapshot of changes with a message.
- **5.1.6 Code merge** — Combine one branch's changes into another.
- **5.1.7 Branch management** — Create, name, and organize branches (strategies).
- **Mental model — the Git lifecycle:** `branch (5.1.7) → commit (5.1.5) → push (5.1.4) → pull request (5.1.3) → code review (5.1.2) → merge (5.1.6) → version management (5.1.1) keeps the whole history reversible`. Everything loops back to version management, which is the umbrella capability that makes the other six actions safe.

---

## SECTION 2 — EXAM KNOWLEDGE

### 5.1.1 Version Management
- Every saved state is recorded with a unique commit hash, author, timestamp, and message, creating a complete, reversible history (a directed acyclic graph in Git).
- `git revert` undoes a prior commit with a new commit (safe on shared branches); `git reset` rewrites history and is dangerous on shared branches.
- Covers tagging (release markers, e.g. v1.2.0) and semantic versioning (MAJOR.MINOR.PATCH) to signal breaking versus safe upgrades.
- Distributed VCS (Git, Mercurial) gives every developer a full local history and offline commits; centralized VCS (SVN, TFVC) needs a server connection for most operations.

### 5.1.2 Code Review
- A human quality gate where teammates inspect proposed changes before they enter mainline—catching bugs, enforcing standards, sharing knowledge, and verifying security.
- Happens on pull requests; reviewers leave inline comments, request changes, or approve; branch protection can require N approvals or specific code owners.
- A process, not a tool—complements automated SAST/secret scanning (5.2) as defense-in-depth and creates an audit trail of who accepted the risk.
- Avoid pitfalls: rubber-stamping (approving unread), huge late PRs (keep changes small), and lacking explicit ownership.

### 5.1.3 Pull Request (PR)
- A formalized request to merge one branch into another, bundling the diff, description, and review thread (called a merge request in GitLab).
- Author pushes a feature branch, opens the PR to a base branch (usually `main`/`develop`), assigns reviewers; CI checks can be required to pass first.
- Branch protection governs PRs: require reviews, require status checks, prohibit force-push, restrict who may merge; merging creates a merge/rebase/squash commit.
- A PR is the social/process wrapper; the merge is the mechanical combination. Not the same as `git pull` (fetch + merge of remote changes locally)—terminology trap.

### 5.1.4 Code Push
- Uploads local commits to a remote (GitHub/GitLab/Bitbucket/AWS CodeCommit), transferring commit objects and moving the remote branch pointer—the trigger that usually starts CI/CD (5.2).
- If the remote has commits you lack, the push is rejected; you must pull/fetch and integrate first.
- `git push -u` sets upstream tracking so future `git push`/`git pull` know the default remote branch.
- `git fetch` downloads without merging; `git pull` is fetch + merge. Force push (`--force`/`--force-with-lease`) overwrites remote history—avoid on shared branches.

### 5.1.5 Code Commit
- A commit is a snapshot identified by a SHA-1/SHA-256 hash, recording author, committer, timestamp, tree, and parent(s); a merge commit has two parents.
- Commit small, atomic, logical changes with clear messages (subject ≤50 chars, body explaining why); `git add` stages, only staged changes commit.
- Local until pushed (5.1.4); frequent commits aid `git bisect` (binary search for the breaking change) and review.
- `git commit --amend` modifies the most recent local commit; never commit secrets, artifacts, or large binaries—use `.gitignore` and secret scanning (5.2).

### 5.1.6 Code Merge
- **Fast-forward** merge: target has no new commits, pointer just moves forward (no merge commit). **3-way merge**: creates a merge commit with two parents when branches diverged.
- **Merge conflict**: same lines changed differently; Git marks with `<<<<<<<`/`=======`/`>>>>>>>` and a human resolves, then `git add` + `git commit`.
- **Rebase** replays commits onto the base tip for linear history but rewrites hashes—never on shared branches. **Squash** collapses many commits into one on merge.
- Merge preserves history and is safe on shared branches; rebase gives clean linear history at the cost of rewritten history. `git merge --abort` cancels a messy in-progress merge.

### 5.1.7 Branch Management
- A branch is a lightweight movable pointer to a commit; common strategies: Git Flow (rigorous, high overhead), GitHub Flow (main + short-lived feature branches, low overhead), trunk-based (tiny branches, merge daily with flags—modern CI/CD ideal).
- Naming conventions (`feature/login`, `bugfix/1234`) aid automation and review; branch protection (no direct pushes to main, require PR + status checks) enforces discipline.
- `git branch -d` deletes a local branch; `git push origin --delete` deletes a remote one; `git fetch --prune` cleans stale branches.
- The organizational layer that makes version management (5.1.1) and code review (5.1.2) usable at team scale; tags are immutable release markers while branches move as work progresses.

---

## SECTION 3 — REAL WORLD CLOUD EXAMPLES

- **Startup using GitHub Flow on AWS:** A two-person team keeps `main` deployable, opens a `feature/` branch per task, pushes, opens a PR, gets one approval, and merges—CodePipeline (5.2) then deploys to a staging environment automatically.
- **Enterprise Git Flow for quarterly releases:** A bank maintains `develop` and `release/2026-Q3` branches; hotfix branches patch production without disturbing the in-flight release, and tags mark each audited production build.
- **Trunk-based development with feature flags:** A SaaS company merges to `main` multiple times per day behind flags; CI runs on every push, and CodeDeploy (5.4) rolls out canaries only after tests pass.
- **Fork-and-PR open-source model:** An external contributor forks a public cloud-tooling repo, branches, commits a fix, and opens a PR that maintainers review and merge—keeping the upstream mainline protected.
- **CodeCommit in a VPC:** A regulated workload uses AWS CodeCommit (private repo inside the account) with branch policies requiring MFA and two approvals, so version control never leaves the cloud account boundary.

---

## SECTION 4 — COMPARISON TABLES

**Table 4.1 — Push vs. Pull/Fetch**

| Action | Direction | Effect | Rejected when? |
|--------|-----------|--------|----------------|
| `git push` | local → remote | Uploads commits, moves remote pointer | Remote diverged (push first) |
| `git fetch` | remote → local | Downloads objects, no merge | Rarely rejected |
| `git pull` | remote → local | fetch + merge | Conflicts during merge |

**Table 4.2 — Merge vs. Rebase vs. Squash**

| Strategy | History shape | Rewrites hashes? | Safe on shared branch? |
|----------|---------------|-----------------|------------------------|
| Merge | Preserves forks + merge commit | No | Yes |
| Rebase | Linear | Yes | No (use only on private) |
| Squash | One commit on merge | Yes (collapses) | Yes (on merge only) |

**Table 4.3 — Branching Strategies**

| Strategy | Branches | Best for | Overhead |
|----------|----------|----------|----------|
| Git Flow | main, develop, feature, release, hotfix | Structured releases | High |
| GitHub Flow | main + feature | Continuous delivery | Low |
| Trunk-based | main + tiny branches | High-frequency CI/CD | Lowest |

**Table 4.4 — Centralized vs. Distributed VCS**

| Aspect | Centralized (SVN) | Distributed (Git) |
|--------|-------------------|-------------------|
| Local history | No | Yes (full clone) |
| Offline commit | No | Yes |
| Branch cost | Heavy | Lightweight |

---

## SECTION 5 — AWS PROVIDER MAPPING

AWS-native services that map to Objective 5.1 source control concepts:

- **AWS CodeCommit** — A fully managed, private Git repository service. It is AWS's direct equivalent of GitHub/GitLab and supports branches, commits, pull requests, and branch policies. Used when an organization wants source control inside its AWS account boundary (no data leaving to a third-party SCM).
- **AWS CodeStar** — A managed service that sets up the full development project: it provisions a CodeCommit repo, connects CI/CD (CodePipeline/CodeBuild), and provides a unified dashboard and project templates. It ties source control (5.1) into the broader DevOps workflow (5.2) with minimal setup.

> Note: 5.1 is about source control *concepts*; on AWS the concrete providers are **CodeCommit** (the repository/version-control engine) and **CodeStar** (the project scaffolding that wraps CodeCommit + pipeline). Git itself (5.4) is the underlying technology both rely on.

---

## SECTION 6 — PRACTICE QUESTIONS

1. Which action uploads local commits to a shared remote repository?
   A. git fetch
   B. git pull
   C. git push
   D. git merge
   Answer: C

2. A commit that has two parent commits is the result of which operation?
   A. git commit --amend
   B. A 3-way merge
   C. git rebase
   D. git cherry-pick
   Answer: B

3. Which command downloads remote changes without merging them?
   A. git pull
   B. git fetch
   C. git push
   D. git clone
   Answer: B

4. What is the primary purpose of a pull request?
   A. To delete a branch
   B. To request review and merge of a branch
   C. To rewrite commit history
   D. To tag a release
   Answer: B

5. Which branching strategy is best suited to high-frequency CI/CD with tiny branches?
   A. Git Flow
   B. GitHub Flow
   C. Trunk-based development
   D. Centralized SVN
   Answer: C

6. What marks a merge conflict in a file?
   A. >>>>>>> and <<<<<<<
   B. @@@ and ###
   C. === and +++
   D. *** and ///
   Answer: A

7. Which Git operation rewrites commit hashes and should NOT be used on shared branches?
   A. Merge
   B. Rebase
   C. Commit
   D. Tag
   Answer: B

8. In semantic versioning MAJOR.MINOR.PATCH, a breaking change increments which?
   A. PATCH
   B. MINOR
   C. MAJOR
   D. All three
   Answer: C

9. Code review is best described as:
   A. An automated scan for secrets
   B. A human quality gate before merge
   C. A build step
   D. A deployment trigger
   Answer: B

10. `git reset` differs from `git revert` because reset:
    A. Creates a new commit
    B. Rewrites history
    C. Cannot undo changes
    D. Only works on remote
    Answer: B

11. Branch protection rules commonly require:
    A. Force-push enabled
    B. Reviews and passing status checks
    C. No MFA
    D. Direct pushes to main
    Answer: B

12. A fork-and-PR workflow is typically used when:
    A. Working alone on a private repo
    B. External contributors propose to a public repo
    C. Running CI
    D. Deleting branches
    Answer: B

13. Which VCS gives every developer a full local copy of history?
    A. Centralized (SVN)
    B. Distributed (Git)
    C. TFVC
    D. None
    Answer: B

14. The safest command to undo a previous commit on a shared branch is:
    A. git reset --hard
    B. git revert
    C. git rebase -i
    D. git push --force
    Answer: B

15. `git commit --amend` is used to:
    A. Create a new separate commit
    B. Modify the most recent local commit
    C. Merge two branches
    D. Delete history
    Answer: B

16. A tag in Git is best used for:
    A. Daily development work
    B. Marking an immutable release point
    C. Resolving conflicts
    D. Rewriting history
    Answer: B

17. Which command sets the upstream tracking branch?
    A. git push -u
    B. git fetch
    C. git tag
    D. git stash
    Answer: A

18. A fast-forward merge occurs when:
    A. Both branches diverged
    B. The target has no new commits
    C. There is a conflict
    D. You rebase
    Answer: B

19. AWS CodeCommit is best described as:
    A. A CI engine
    B. A managed private Git repository
    C. A deployment tool
    D. A monitoring service
    Answer: B

20. `git bisect` is useful for:
    A. Resolving merge conflicts
    B. Binary-searching for the commit that broke something
    C. Tagging releases
    D. Force-pushing
    Answer: B
