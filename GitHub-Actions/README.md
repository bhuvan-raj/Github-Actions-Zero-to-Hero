 # Github Actions
<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/GitHub-Actions/assets/cicd1.png" alt="Banner" />
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

## Use Cases of GitHub Actions

GitHub Actions is a versatile automation platform built directly into GitHub, allowing you to automate nearly any aspect of your software development workflow. It extends far beyond traditional CI/CD to encompass a wide array of tasks. Here are its primary use cases:

- 1. Continuous Integration (CI)

Automate the build, test, and analysis phases of your development process to ensure code quality and early bug detection.

* **Automated Builds:** Automatically compile your code on every push or pull request.
* **Running Tests:** Execute unit, integration, and end-to-end tests to validate code functionality.
* **Code Linting & Formatting:** Enforce coding standards and maintain consistent code style.
* **Static Code Analysis:** Run tools to identify potential bugs, security vulnerabilities, and quality issues.

- 2. Continuous Delivery / Deployment (CD)

Streamline the process of getting your application from development to various environments.

* **Automated Deployments:** Deploy your application to development, staging, or production environments upon successful builds and tests, or specific triggers.
* **Publishing Packages/Artifacts:** Automatically publish libraries, Docker images, or other build artifacts to registries (e.g., npm, PyPI, GitHub Packages, Docker Hub).
* **Version Tagging & Releases:** Automate the creation of release tags, generation of release notes, and publishing of new releases on GitHub.

- 3. Project Management & Automation

Automate common repository management tasks to improve efficiency and collaboration.

* **Issue & Pull Request Automation:**
    * Automatically add labels based on content or author.
    * Close inactive issues or PRs.
    * Assign reviewers to pull requests.
    * Welcome new contributors.
* **Notifications:** Send alerts to Slack, Microsoft Teams, Discord, or email regarding workflow status, new issues, or other events.
* **Scheduled Tasks:** Run jobs at predefined intervals (e.g., daily cleanups, nightly reports, recurring data synchronization).

- 4. Code Quality & Security

Integrate checks and scans directly into your workflow to enhance code robustness and security posture.

* **Code Coverage Reporting:** Generate and track code coverage metrics to ensure adequate test coverage.
* **Dependency Scanning:** Scan for known vulnerabilities in your project's third-party dependencies.
* **Secret Management:** Securely manage and inject sensitive information (API keys, tokens) into your workflows using GitHub Secrets.

- 5. Infrastructure as Code (IaC) & Cloud Operations

Orchestrate cloud resource provisioning and management directly from your repository.

* **Terraform/Pulumi Automation:** Automate the deployment and management of infrastructure definitions.
* **Cloud Deployments:** Deploy applications and services to major cloud providers (AWS, Azure, Google Cloud, Kubernetes).

- 6. Miscellaneous & Custom Workflows

Beyond the core CI/CD, GitHub Actions enables a vast array of custom automations.

* **Documentation Generation:** Automatically build and publish project documentation.
* **Database Migrations:** Run database schema migration scripts as part of deployment.
* **Custom Script Execution:** Execute any arbitrary shell script or command.
* **Third-Party Integrations:** Leverage a rich ecosystem of pre-built actions from the GitHub Marketplace to integrate with various tools and services.


# Example of Workflow.yml 

```
name: Hello World Workflow

on:
  push:
    branches:
      - main

jobs:
  say-hello:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Say Hello
        run: echo "👋 Hello, World from GitHub Actions!"
```

**Breakdown**

   ```name```: Name of the workflow.

   ```on.push.branches```: Triggers this workflow when someone pushes to main.

   ```jobs.say-hello```: Defines a job called say-hello.

   ```runs-on```: Uses GitHub-hosted ubuntu-latest runner.

   ```steps```: Each step runs one command or uses an action.

   ```First step```checks out the code (optional but common).

   ```Second step``` runs a shell command using run.

