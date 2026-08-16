# Unit 3 — Jenkins: Architecture & Workflow

---

## What Is Jenkins?

Jenkins is an **open-source automation server** that automates repetitive software development tasks — building, testing, and deploying code. It acts as the **central automation/orchestration tool** in a CI/CD pipeline, coordinating everything else (compilers, test runners, Docker, cloud deploys) rather than doing all the work itself.

---

## Q. Why is Jenkins used?

Jenkins is used because it can automatically:

- Detect code changes and trigger a build
- Run automated tests
- Generate build/test reports
- Deploy applications to servers or cloud platforms
- Send notifications immediately if a build fails
- Integrate with Git, GitHub, Maven, Docker, Kubernetes, and major cloud platforms

---

## Advantages of Jenkins

| Advantage | Why it matters |
|---|---|
| Free & open-source | No licensing cost, large community |
| 1,800+ plugins | Integrates with almost any tool in the DevOps ecosystem |
| Platform-independent | Runs on Windows, Linux, macOS, or as a Docker container |
| Easy DevOps integration | Works with Git, Maven, Docker, Kubernetes, AWS/Azure/GCP |
| Supports full CI/CD pipelines | Can handle build → test → deploy end-to-end |
| Distributed builds | Work can be spread across many agent machines |

---

## GitHub Webhooks in Jenkins

- A **webhook** is a mechanism where GitHub automatically notifies Jenkins the instant a change occurs in the repository (e.g. a push).
- Instead of Jenkins repeatedly **polling** GitHub ("has anything changed yet?" every few minutes), GitHub sends an **HTTP POST request** to a Jenkins URL immediately after the event happens — so Jenkins starts working with almost no delay.
- Polling still exists as a fallback (used when Jenkins isn't reachable from the internet, e.g. behind a firewall), but webhooks are the standard, faster approach.

### Jenkins Workflow (End-to-End)

```
1. Developer commits code (locally)
        │
2. Developer pushes code to GitHub
        │
3. GitHub sends a webhook (HTTP request) to Jenkins
        │
4. Jenkins automatically starts the CI/CD pipeline
        │
5. Pipeline runs: Build → Test → Package → Deploy
        │
6. Jenkins reports success/failure (dashboard + notification)
```

---

## Jenkins Architecture

```
                     Jenkins Controller
                  (schedules jobs, stores
                config, tracks results, brain
                     of the system)
                          │
           SSH / JNLP (TCP) — communication
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
     Agent-1            Agent-2           Agent-3
  (runs actual        (runs actual      (runs actual
   build/task)         build/task)       build/task)
```

| Component | Role |
|---|---|
| **Controller (Master)** | The main server. Schedules jobs, tracks build results, manages plugins and configuration. The "brain" of Jenkins — in production it usually delegates the actual work rather than running builds itself. |
| **Agent (Node/Slave)** | A separate machine (physical, VM, container, or cloud pod) that receives tasks from the Controller and runs the real build/test/deploy steps. |
| **Communication** | Controller ↔ Agent talk over **SSH** or **JNLP/TCP** (Java Network Launch Protocol), depending on how the agent is registered. |

---

## Jenkins Dashboard

| Section | What it shows |
|---|---|
| **Status View** | List of all defined projects/jobs and their current status (passing/failing/building) |
| **Build History** | Recent builds, their success/failure rate, and links to detailed logs |
| **Manage Jenkins** | Central hub for global configuration — security settings, plugin management, tool installations (JDK, Maven, Git, etc.) |

---

## Jobs & Pipelines

| Type | Description |
|---|---|
| **Freestyle Job** | GUI-driven configuration for simple, sequential automation tasks — good for beginners or simple builds, but not version-controlled by default |
| **Pipeline** | A code-driven workflow, defined in a text file (`Jenkinsfile`) and stored in version control alongside the project — the modern, recommended approach |
| **Stage** | A logical division within a pipeline (e.g. `Build`, `Test`, `Deploy`) — groups related steps and shows up as a visual block in the Jenkins UI |

**Freestyle vs Pipeline — why Pipeline wins in real teams:** a Freestyle job's config lives only inside Jenkins' UI (hard to review, easy to lose). A Pipeline's `Jenkinsfile` lives in the same Git repo as the code, so it's versioned, reviewable in pull requests, and reproducible if Jenkins itself is ever rebuilt.
