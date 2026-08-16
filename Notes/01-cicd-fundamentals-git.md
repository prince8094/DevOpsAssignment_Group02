# Unit 3 — CI/CD Fundamentals & Git Workflow

---

## Q. How does CI/CD work with Git?

Every time code is pushed to a Git repository, a connected CI/CD service (GitHub Actions, GitLab CI, Jenkins, etc.) automatically:

1. **Triggers** — detects the push/commit event
2. **Builds** the project (compiles/installs dependencies)
3. **Runs tests** (unit, integration)
4. **Deploys** the build (if configured, and if tests pass)
5. **Notifies** the developer immediately if any step fails (email, Slack, dashboard)

This removes manual "download → build → test → deploy" work and gives fast feedback on every change.

---

## Popular CI/CD Tools

| Tool | Hosted in | Config file | Notes |
|---|---|---|---|
| **Jenkins** | Self-hosted (your own server) | `Jenkinsfile` (Groovy) | Open-source automation server, huge plugin library, most flexible/self-managed |
| **GitHub Actions** | Built into GitHub | `.github/workflows/*.yml` | YAML-based, tightly integrated with GitHub repos/PRs |
| **GitLab CI/CD** | Built into GitLab | `.gitlab-ci.yml` | YAML-based, native to GitLab |
| **CircleCI** | Cloud (SaaS) | `.circleci/config.yml` | Works with GitHub/GitLab, fast setup, supports many languages |
| **Travis CI** | Cloud (SaaS) | `.travis.yml` | Historically popular for open-source projects |
| **Azure Pipelines** | Azure DevOps | `azure-pipelines.yml` | Works with Azure DevOps and GitHub, supports many platforms/languages |

> **Core functionality shared by all CI/CD tools:** trigger a build → run tests → provide feedback.

---

## Key CI/CD Concepts (Terminology)

| Term | Meaning |
|---|---|
| **Workflow** | The full automated process — a set of jobs that run together in response to a trigger |
| **Job / Task** | A group of steps that run together to accomplish one part of the workflow (e.g. "test job") |
| **Step** | A single action inside a job (e.g. checkout code, run `npm install`, run tests) |
| **Runner** | The machine/server (physical, VM, or container) that actually executes the workflow |
| **Trigger** | The event that decides *when* a workflow runs (push, pull request, schedule, manual) |
| **Env (Environment Variables)** | Configurable settings/values available to the workflow (e.g. `NODE_ENV=production`) |
| **Secrets** | Encrypted, protected values (API keys, passwords, tokens) injected into the workflow without exposing them in code |

**Relationship:** `Workflow → contains → Jobs → contain → Steps`, and the whole thing runs on a `Runner`, kicked off by a `Trigger`.

---

## Q. A student accidentally overwrote source code after multiple commits. How does Git help recover a previous version?

Git keeps every committed snapshot, so nothing is truly lost as long as it was committed at some point. Recovery options, from safest to most drastic:

1. **`git log`** — find the commit hash of the last good version.
2. **`git checkout <commit-hash> -- <file>`** — restore a specific file from an old commit into the working directory without touching history.
3. **`git revert <commit-hash>`** — creates a *new* commit that undoes the changes from a bad commit. Safe for shared/team branches because it doesn't rewrite history.
4. **`git reset --hard <commit-hash>`** — moves the branch pointer back to an earlier commit, discarding later commits entirely. Powerful but rewrites history — risky on shared branches.
5. **`git reflog`** — Git's safety net. It logs every place `HEAD` has pointed to (even after resets or deleted branches), so a commit that seems "lost" can almost always be found and restored with `git reflog` + `git checkout`/`git reset`.

**Rule of thumb:** use `revert` on shared/public branches (non-destructive), use `reset` only on your own local/unpushed work, and treat `reflog` as the last-resort recovery tool.

---

## Q. Developers working at a startup on Login, Cart, and Payment modules — explain a complete Git workflow using branch, commit, push, and merge

**Scenario:** 3 developers, 3 features, one shared repository.

```
main (production-ready code)
  │
  ├── feature/login    (Dev A)
  ├── feature/cart      (Dev B)
  └── feature/payment   (Dev C)
```

**Step-by-step workflow:**

1. **Branch** — Each developer creates a feature branch off `main` so they don't disturb the stable code:
   ```
   git checkout -b feature/login
   ```
2. **Commit** — As each developer writes code, they commit in small, logical chunks:
   ```
   git add .
   git commit -m "Add login form validation"
   ```
3. **Push** — The branch is pushed to the remote (GitHub/GitLab) so it's backed up and visible to the team:
   ```
   git push origin feature/login
   ```
4. **Pull Request / Merge Request** — Developer opens a PR from `feature/login` → `main`. This is where CI/CD kicks in automatically: the pipeline builds the branch and runs tests before anyone reviews it.
5. **Code Review** — Teammates review the diff, leave comments, request changes if needed.
6. **Merge** — Once approved and CI passes, the branch is merged into `main`:
   ```
   git checkout main
   git merge feature/login
   ```
7. **Conflict handling (if any)** — If two people edited the same lines (e.g. Cart and Payment both touch `checkout.js`), Git flags a **merge conflict**. The developer manually resolves it, then commits the resolution.
8. **Deploy** — Once merged into `main`, the CI/CD pipeline automatically builds and deploys the updated app.

**Why this workflow matters:** each feature is isolated (no one breaks someone else's work), every change is tested before it reaches `main`, and `main` always stays deployable.
