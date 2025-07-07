# Github Actions
<img src="https://github.com/bhuvan-raj/Github-Actions/Github-Actions/assets/cicd1.png" alt="Banner" />
GitHub-Actions/assets


## What is GitHub Actions?

**GitHub Actions** is an **event-driven automation platform** provided by GitHub. It enables you to automate, customize, and execute your software development workflows directly in your GitHub repository. These automations are triggered by specific events (like a code push or a pull request) and can run on various operating systems.

## Core Concepts: Workflow, Job, and Runner

To build automations with GitHub Actions, it's essential to understand its core components:

### 1. Workflow

A **Workflow** is a **configurable automated process** that defines how your automation should run. It's the blueprint for your CI/CD pipeline or any other automated task.

* **Definition:** Workflows are defined in YAML (`.yml` or `.yaml`) files located in the `.github/workflows/` directory of your repository.
* **Trigger:** Each workflow specifies one or more `on:` events that will cause it to run (e.g., `push`, `pull_request`, `schedule`, `workflow_dispatch` for manual triggers).
* **Composition:** A workflow consists of one or more **jobs** that are executed sequentially or in parallel.
* **Purpose:** To define the entire automation process from start to finish.

### 2. Job

A **Job** is a **set of steps** that execute on the same **runner**. It represents a logical unit of work within a workflow.

* **Isolation:** Each job runs in a fresh, isolated virtual environment. This means that variables and files created in one job are not automatically available to another job (unless explicitly passed as artifacts).
* **Execution Order:** Jobs run in parallel by default, but you can configure them to run sequentially by specifying dependencies using the `needs:` keyword.
* **Steps:** A job is composed of one or more **steps**, which are individual tasks. A step can be a shell command (e.g., `run: npm install`) or an **Action** (a reusable piece of code).
* **Purpose:** To perform a specific, isolated segment of your overall workflow (e.g., "build-app," "run-tests," "deploy-staging").

### 3. Runner

A **Runner** is a **server or virtual machine** where your workflow **jobs** actually execute. It's the compute environment that picks up jobs from GitHub Actions, runs the defined steps, and reports the results back to GitHub.

* **Role:** Runners provide the necessary operating system, tools, and resources (CPU, RAM) for your workflow steps to run.
* **Ephemeral Nature:** GitHub-hosted runners are ephemeral. A fresh runner instance is created for each job run and destroyed once the job completes, ensuring a clean and consistent environment for every execution.
* **Purpose:** To provide the runtime environment for your GitHub Actions workflows.

---

## Types of GitHub Actions Runners

GitHub Actions offers two primary types of runners, each suited for different use cases and offering varying levels of control and flexibility:

### 1. GitHub-hosted Runners

These are virtual machines provided and managed directly by GitHub. They are the most common and easiest way to run your workflows.

* **Management:** Fully managed by GitHub. You don't need to worry about provisioning, scaling, patching, or maintaining them.
* **Operating Systems:** Available with various operating systems:
    * `ubuntu-latest` (or specific versions like `ubuntu-22.04`, `ubuntu-20.04`)
    * `windows-latest` (or specific versions like `windows-2022`, `windows-2019`)
    * `macos-latest` (or specific versions like `macos-13`, `macos-12`)
* **Pre-installed Software:** Come with a wide range of pre-installed software, tools, and SDKs for common languages and platforms (Node.js, Python, Java, .NET, Docker, Git, etc.).
* **Scalability:** GitHub automatically scales these runners up and down based on demand, so you don't have to worry about capacity.
* **Cost:** Included in GitHub's free tier for public repositories, with competitive per-minute pricing for private repositories.
* **Use Cases:** Ideal for most public and private projects that can run their builds and tests in a standard cloud VM environment.
* **Limitations:**
    * Limited control over the underlying VM's configuration.
    * Cannot access private networks unless configured via a VPN/gateway (complex).
    * Time limits per job run apply.

### 2. Self-hosted Runners

These are machines (physical, virtual, or even containers) that you provide and manage yourself. You install the GitHub Actions runner application on your machine, and it connects to your GitHub repository or organization to pick up jobs.

* **Management:** You are responsible for provisioning, maintaining, upgrading, and securing the runner machine and its installed software.
* **Operating Systems:** Can run on virtually any OS that supports the runner application (Linux, Windows, macOS, ARM-based systems).
* **Custom Environments:** Allows you to install specific software, tools, or dependencies that are not available on GitHub-hosted runners.
* **Private Network Access:** Can access resources within your private data center or VPC, which is crucial for internal deployments or accessing internal services.
* **Hardware Control:** You can specify the hardware capabilities (CPU, RAM, GPUs) of your runners to meet specific performance needs.
* **Cost:** You pay for the infrastructure hosting the runner, but not for the runner minutes themselves from GitHub.
* **Use Cases:**
    * Projects with very specific hardware requirements (e.g., large memory, specialized GPUs).
    * Workflows that need to access resources in a private network or on-premises.
    * Organizations with strict security policies that require all code execution to remain within their controlled infrastructure.
    * Long-running jobs that might exceed GitHub-hosted runner time limits.
* **Considerations:**
    * Requires more operational effort to set up and maintain.
    * Needs careful security configuration as it executes code from your repository.

---
