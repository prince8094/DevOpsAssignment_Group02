# Unit 3 — Jenkinsfile Basics & Pipeline Syntax

---

## What Is a Jenkinsfile?

A **Jenkinsfile** is a text file that defines a Jenkins pipeline **as code**. It's checked into the project's Git repository (usually at the root), so the entire build/test/deploy process is version-controlled, reviewable, and reproducible — the same way application code is.

---

## Two Pipeline Syntaxes

| | Declarative | Scripted |
|---|---|---|
| Structure | Strict, structured — must follow the `pipeline { }` block format | Free-form Groovy code |
| Readability | Easier for beginners, closer to a config file | Requires programming knowledge |
| Flexibility | Limited to what the declarative DSL supports | Full flexibility — loops, conditionals, custom logic |
| Recommended for | Most teams, standard build/test/deploy flows | Complex/custom automation logic |

**Rule of thumb:** start with Declarative. Only reach for Scripted when you need logic the Declarative syntax can't express.

---

## Anatomy of a Declarative Jenkinsfile

```groovy
pipeline {
    agent any                       // run on any available agent

    environment {
        NODE_ENV = 'production'     // environment variable available to all stages
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/example/repo.git'
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                sh 'scp -r build/ user@server:/var/www/app'
            }
        }
    }

    post {
        failure {
            mail to: 'team@company.com', subject: 'Build Failed', body: 'Check Jenkins console output.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
```

### Key blocks explained

| Block | Purpose |
|---|---|
| `pipeline { }` | Wraps the entire declarative pipeline |
| `agent any` | Tells Jenkins to run on *any* available agent (can also target a specific label, e.g. `agent { label 'linux' }`) |
| `environment { }` | Defines variables available across all stages |
| `stages { }` | Contains one or more `stage()` blocks — the logical divisions of the pipeline |
| `stage('Name') { }` | One phase of the pipeline (Checkout, Build, Test, Deploy, etc.) |
| `steps { }` | The actual commands/actions executed inside a stage |
| `post { }` | Runs *after* the pipeline finishes — used for notifications on `success`, `failure`, `always`, etc. |

---

## Breaking Down `sh 'npm install'`

This line commonly confuses students — here's exactly what each part means:

- **`sh`** → stands for **shell**. It tells Jenkins "run the following command in the shell/terminal" (on Linux/macOS agents; Windows agents use `bat` instead).
- **`npm`** → **Node Package Manager** — the tool that manages JavaScript project dependencies.
- **`install`** → the npm subcommand that reads `package.json` and downloads/installs all the listed dependencies into a `node_modules` folder.

So `sh 'npm install'` = *"Jenkins, open a shell on the agent and run `npm install` there."*

---

## Practice / Exam Questions

1. What is the difference between a Freestyle job and a Pipeline job?
2. Why is storing the pipeline as a `Jenkinsfile` in Git better than configuring it in the Jenkins UI?
3. Differentiate between Declarative and Scripted pipeline syntax.
4. What does the `agent` directive control?
5. What is the purpose of the `post` block, and name two conditions it can respond to.
6. In `sh 'npm install'`, what does each part of the command do?
7. Explain the full Jenkins workflow starting from a developer's `git push` to a deployed application.
8. What is the difference between the Jenkins Controller and an Agent, and how do they communicate?
9. What is a GitHub webhook, and why is it preferred over Jenkins polling GitHub for changes?
10. What is a `stage` in a pipeline, and why are stages useful for debugging a failed build?

---

## One-Page Recap

> Jenkins automates the software delivery process. A developer pushes code → GitHub sends a **webhook** → Jenkins' **Controller** picks up the event and assigns work to an **Agent** → the Agent executes the pipeline defined in the **Jenkinsfile** (Checkout → Install → Test → Build → Deploy) → results and notifications appear on the **Dashboard**. Writing this pipeline **as code** (rather than clicking through a UI) is what makes it reliable, reviewable, and repeatable — the core idea behind modern CI/CD.
