Here you go Bubu — a complete `README.md` file formatted for your GitHub repository, covering **GitHub Actions: Variables and Expressions (3.1)**:

---

````markdown
# 🔧 GitHub Actions: Variables and Expressions (3.1)

This guide explains how to use **environment variables** and **expressions** in GitHub Actions, and how to access different **context objects** like `github`, `runner`, `job`, and `steps`.

---

## 📘 Table of Contents

- [Environment Variables (`env`)](#-environment-variables-env)
  - [Workflow Level](#workflow-level)
  - [Job Level](#job-level)
  - [Step Level](#step-level)
- [Expressions (`${{ }}`)](#-expressions-)
- [Context Objects](#-context-objects)
  - [GitHub Context](#github-context)
  - [Runner Context](#runner-context)
  - [Job Context](#job-context)
  - [Steps Context](#steps-context)
- [Combined Example](#-combined-example)
- [Summary Table](#-summary-table)

---

## ✅ Environment Variables (`env`)

Environment variables can be set at three different levels in a workflow.

### 🟢 Workflow Level

```yaml
env:
  APP_ENV: production

jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Environment: $APP_ENV"
````

### 🟡 Job Level

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      JAVA_HOME: /usr/lib/jvm/java-21
    steps:
      - run: echo "Java is installed at $JAVA_HOME"
```

### 🔵 Step Level

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Step with local env
        env:
          TEMP_PATH: /tmp/data
        run: echo "Path is $TEMP_PATH"
```

---

## 🧠 Expressions `${{ ... }}`

Expressions allow referencing **variables, functions, and contexts** in GitHub Actions. They are always wrapped with `${{ ... }}`.

### Example

```yaml
- name: Print commit SHA
  run: echo "Commit SHA is ${{ github.sha }}"
```

---

## 📦 Context Objects

GitHub provides predefined **contexts** that contain metadata about the workflow run, event, and environment.

### 🔹 GitHub Context

| Expression                                | Description                                  |
| ----------------------------------------- | -------------------------------------------- |
| `${{ github.repository }}`                | Repository (`owner/repo`)                    |
| `${{ github.ref }}`                       | Branch or tag that triggered the workflow    |
| `${{ github.sha }}`                       | Full commit SHA                              |
| `${{ github.actor }}`                     | Username of the trigger                      |
| `${{ github.event_name }}`                | Type of event (`push`, `pull_request`, etc.) |
| `${{ github.event.pull_request.number }}` | PR number (for `pull_request` event)         |

### 🔹 Runner Context

| Expression           | Description                   |
| -------------------- | ----------------------------- |
| `${{ runner.os }}`   | Operating system              |
| `${{ runner.name }}` | Name of the runner            |
| `${{ runner.arch }}` | Architecture (e.g., X64, ARM) |

### 🔹 Job Context

| Expression          | Description                                    |
| ------------------- | ---------------------------------------------- |
| `${{ job.status }}` | Job status (`success`, `failure`, `cancelled`) |

### 🔹 Steps Context

Used to access outputs from earlier steps.

```yaml
- id: get-time
  run: echo "now=$(date)" >> "$GITHUB_OUTPUT"

- run: echo "Current time is ${{ steps.get-time.outputs.now }}"
```

---

## 🔧 Combined Example

```yaml
name: Context Demo

on: push

env:
  GLOBAL_ENV: "global_value"

jobs:
  context-info:
    runs-on: ubuntu-latest
    env:
      JOB_ENV: "job_level_value"
    steps:
      - name: Step 1
        run: |
          echo "Global ENV: $GLOBAL_ENV"
          echo "Job ENV: $JOB_ENV"
          echo "GitHub Ref: ${{ github.ref }}"
          echo "Commit SHA: ${{ github.sha }}"
          echo "Runner OS: ${{ runner.os }}"
```

---

## 📌 Summary Table

| Feature    | Purpose                                   |
| ---------- | ----------------------------------------- |
| `env`      | Set environment variables                 |
| `${{ }}`   | Access variables, functions, and contexts |
| `github.*` | Metadata about repo, event, actor, etc.   |
| `runner.*` | Runner details (OS, name, arch)           |
| `job.*`    | Job-level metadata (status)               |
| `steps.*`  | Share outputs between steps               |

---

## 🧪 Practice Idea

Try creating a GitHub Action that prints:

* Who triggered the workflow
* The branch name
* The current date and time
* The OS of the runner

---

> ✍️ Created for educational purposes by Bubu.

```

---

Let me know if you'd like a matching `workflow.yml` for this `README.md`, Bubu!
```
