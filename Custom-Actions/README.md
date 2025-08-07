# ✨ Importance of Custom Actions

Creating custom GitHub Actions provides significant benefits for your development workflow:

  * **Reusability:** Encapsulate complex or frequently used logic into a single, reusable unit.
  * **Consistency:** Ensure consistent execution of tasks across multiple projects or repositories.
  * **Efficiency:** Automate repetitive manual processes, saving time and reducing human error.
  * **Encapsulation:** Keep your workflow files clean and readable by abstracting away implementation details.
  * **Shareability:** Share your actions publicly with the community or privately within your organization.

-----

## 💡 Use Cases for Custom Actions

Custom Actions can automate a vast array of tasks:

  * **Custom Deployment Logic:** Deploying applications to specific cloud providers (AWS, Azure, GCP) or custom infrastructure.
  * **Code Quality Checks:** Running static analysis, linters, or security scanners with custom configurations.
  * **Environment Setup:** Preparing specific development or testing environments with custom tools.
  * **Data Processing:** Running scripts to transform, analyze, or migrate data.
  * **Notifications:** Sending custom notifications to Slack, Teams, or other services based on workflow events.
  * **API Interactions:** Interacting with third-party APIs for various purposes (e.g., issue tracking, content management).
  * **Internal Tooling Automation:** Automating tasks related to internal tools, reporting, or auditing.

-----

## 🏗️ How to Create a Custom Action: An Example

This example demonstrates creating a simple **composite run steps action** that executes two basic Linux commands: `who` and `date`. This type of action is great for bundling a sequence of shell commands.

### Action Repository Structure

Your action will reside in its own GitHub repository (e.g., `your-username/my-linux-commands-action`). The core definition is in `action.yml`:

```
my-linux-commands-action/
└── action.yml
```

### `action.yml` Content

The `action.yml` file defines the action's metadata and the commands it runs.

```yaml
# action.yml
name: 'Basic Linux Commands' # User-friendly name for the action
description: 'Runs the `who` and `date` Linux commands and prints their output.'
runs:
  using: "composite" # Defines this as a composite action
  steps:
    - name: Get current users
      run: who # Executes the 'who' command
      shell: bash # Specifies the shell to use for this command

    - name: Get current date and time
      run: date # Executes the 'date' command
      shell: bash
```

### How it Works:

  * **`runs.using: "composite"`**: This tells GitHub Actions that this file defines a series of steps (shell commands in this case) that will be executed sequentially.
  * **`steps`**: Each item under `steps` is an individual command or a step that will be run.
      * `name`: A descriptive name for the step, visible in the workflow logs.
      * `run`: The actual command or script to execute.
      * `shell: bash`: Specifies that the command should be run using `bash`.

### Versioning (Best Practice)

After pushing your `action.yml` to your repository's `main` branch, it's a best practice to create a **version tag** (e.g., `v1`). This allows users to reference a stable version of your action, preventing unexpected changes if you modify the `main` branch later.

```bash
git tag -a v1 -m "Initial release of Linux Commands Action"
git push origin v1
```

-----

## 🚀 How to Use Your Custom Action in a Workflow

Once your action is defined and pushed to GitHub (and ideally tagged), you can incorporate it into any of your GitHub Workflows.

### Example Workflow (`.github/workflows/your-ci-cd.yml`)

```yaml
name: My CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  workflow_dispatch: # Allows manual trigger

jobs:
  build-and-test:
    runs-on: ubuntu-latest # Or another suitable runner

    steps:
      - name: Checkout repository code
        uses: actions/checkout@v4 # Essential for accessing your repo's code

      - name: Execute my custom Linux Commands Action
        # Reference your action using its repository path and version tag
        # Replace 'YOUR_GITHUB_USERNAME' with your actual username/organization name
        uses: YOUR_GITHUB_USERNAME/my-linux-commands-action@v1

      # You can add more steps here for building, testing, deploying, etc.
      - name: Show build complete message
        run: echo "Build process complete!"
```

### Explanation of Usage:

  * **`uses: actions/checkout@v4`**: This standard action checks out your repository's code, making it available to subsequent steps in the job.
  * **`uses: YOUR_GITHUB_USERNAME/my-linux-commands-action@v1`**: This is how you invoke your custom action.
      * `YOUR_GITHUB_USERNAME`: Replace this with your actual GitHub username or the organization name where the action repository resides.
      * `my-linux-commands-action`: The name of the GitHub repository containing your `action.yml` file.
      * `@v1`: The version tag you created. Using tags ensures your workflow uses a stable, unchanging version of the action.

When this workflow runs, the output of the `who` and `date` commands from your custom action will be visible in the logs of the "Execute my custom Linux Commands Action" step.

-----
