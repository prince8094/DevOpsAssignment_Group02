# Jenkins + CI/CD Cheat Sheet

## What CI/CD Actually Means

- **CI (Continuous Integration):** every push gets built and tested automatically.
- **CD (Continuous Delivery):** app is packaged and ready to deploy, but a human clicks "go."
- **CD (Continuous Deployment):** no human click — passes tests, goes live.

## What Jenkins Is

Open-source server that watches your repo and runs your build/test/deploy steps for you. Free, self-hosted, huge plugin library.

## The Basic Flow

```
push code → GitHub → webhook → Jenkins → build → test → deploy
```

Webhook = GitHub tells Jenkins "something changed," instantly. No polling needed.

## Architecture in Two Words

| Part | Job |
|---|---|
| Controller | Schedules and tracks builds |
| Agent | Actually runs the build |

They talk over SSH or JNLP.

## Core Vocabulary

- **Job** — a task Jenkins can run
- **Build** — one run of that job
- **Pipeline** — build → test → deploy, chained together
- **Stage** — one step of the pipeline (e.g. "Test")
- **Jenkinsfile** — the pipeline written as code, saved in your repo
- **Plugin** — add-on for Git, Docker, Slack, etc.

## Declarative vs Scripted

- **Declarative** — fixed structure, easy to read, use this by default.
- **Scripted** — raw Groovy, more power, more complexity. Only reach for it if declarative can't do what you need.

## Minimal Jenkinsfile

```groovy
pipeline {
    agent any
    stages {
        stage('Install') { steps { sh 'npm install' } }
        stage('Test')    { steps { sh 'npm test' } }
        stage('Deploy')  { steps { sh 'npm run deploy' } }
    }
}
```

`sh` = run this in the shell. `npm install` = grab the dependencies listed in package.json.

## Other CI/CD Tools (so you can name-drop them)

| Tool | Config file | Lives in |
|---|---|---|
| GitHub Actions | `.github/workflows/*.yml` | GitHub |
| GitLab CI | `.gitlab-ci.yml` | GitLab |
| CircleCI | `.circleci/config.yml` | Cloud |
| Azure Pipelines | `azure-pipelines.yml` | Azure DevOps |

## Git Moves Worth Remembering

- `git revert` — undo a commit safely, keeps history
- `git reset --hard` — rewind hard, only do this on your own local work
- `git reflog` — find "lost" commits, your safety net

## If You Forget Everything Else

Push code → Jenkins notices → builds it → tests it → tells you if it broke → ships it if it didn't.
