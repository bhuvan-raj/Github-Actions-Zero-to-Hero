# GitHub Actions Conditional Execution: A Study Guide 🚦

<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Conditional%20Statement(if)/assets/if.png" alt="Banner" />


This document provides a detailed study guide on using `if` conditions in GitHub Actions workflows. The `if` keyword is fundamental for controlling the execution flow of your automation, allowing you to define "if-then" rules that ensure specific steps or jobs run only when certain criteria are met. This capability makes your CI/CD pipelines significantly more efficient, intelligent, and robust.

-----

## What is an `if` Condition? 🤔

An **`if` condition** in GitHub Actions makes parts of your workflow **conditional**. By attaching an `if:` statement to a `job` or a `step`, you dictate that the associated code will **only execute if the specified condition evaluates to `true`**. If the condition evaluates to `false`, that job or step is **skipped**.

### Why use `if` conditions?

  * **Efficiency:** Prevent unnecessary resource consumption by skipping steps, such as not attempting a deployment if prior tests have failed.
  * **Control:** Precisely define when actions should occur, for instance, deploying only when changes are pushed to the `main` branch.
  * **Error Handling:** Automate responses to pipeline failures, like sending a notification if a build encounters an error.

-----

## Where Can You Use `if`? 📍

`if` conditions can be applied at two distinct levels within a GitHub Actions workflow:

### Job Level

Controls whether an **entire job** (and all its steps) runs.

```yaml
jobs:
  my_job:
    if: <condition_expression> # Condition for the entire job
    runs-on: ubuntu-latest
    steps:
      # ... all steps in this job will respect the 'if' condition
```

### Step Level

Controls whether a **specific step within a job** runs.

```yaml
jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
      - name: This step always runs
        run: echo "This part is unconditional"
      - name: Conditional Step
        if: <condition_expression> # Condition for this specific step
        run: echo "This part runs only if the condition is true"
```

-----

## How to Write `if` Conditions 📝

`if` conditions are written using an **expression language** that supports:

  * **Status Check Functions:** Predetermined functions to check the outcome of previous workflow components.
  * **Contexts:** Objects that provide access to various information about the workflow run.
  * **Operators:** Logical and comparison operators to build complex conditions.

### 1\. Status Check Functions (Most Common) ✅❌

These functions return `true` or `false` based on the outcome of preceding jobs or steps. They are always invoked with parentheses `()`.

  * **`success()`**: Returns `true` if all preceding steps or jobs that it depends on have **succeeded**.

      * **Example:** `if: success()`
      * **Use Case:** Proceeding with deployment only after successful build and test stages.

  * **`always()`**: Returns `true` **regardless of the outcome** (success, failure, or cancellation) of preceding steps or jobs.

      * **Example:** `if: always()`
      * **Use Case:** Executing cleanup routines, sending general workflow completion notifications, or archiving logs, irrespective of whether the main job passed or failed.

  * **`failure()`**: Returns `true` if any of the preceding steps or jobs that it depends on have **failed**.

      * **Example:** `if: failure()`
      * **Use Case:** Triggering specific "workflow failed" notifications, initiating rollback actions, or automatically creating an issue on GitHub.

  * **`cancelled()`**: Returns `true` if the preceding steps or jobs that it depends on have been **manually cancelled** by a user.

      * **Example:** `if: cancelled()`
      * **Use Case:** Releasing resources that might have been acquired by a long-running job that was terminated prematurely.

-----

### 2\. Contexts 🌍

Contexts are objects that provide access to relevant information about the workflow run. You access their properties using the syntax `${{ <context_name>.<property> }}`.

  * **`github` context**: Contains information about the event that triggered the workflow, the repository, branch, pull request details, and more.

      * **Example:** `github.ref == 'refs/heads/main'` (checks if the current branch is `main`).
      * **Use Case:** Running a job exclusively when changes are pushed to a specific branch.

  * **`env` context**: Provides access to environment variables defined within the workflow.

      * **Example:** `env.MY_ENVIRONMENT == 'production'`
      * **Use Case:** Running a step only if a particular environment variable holds a specific value.

  * **`job` context**: Contains information about the current job.

      * **Example:** `job.status == 'success'` (while valid, `success()` is generally preferred for checking job status in `if` conditions).

  * **`steps` context**: Provides the output and status of previously executed steps within the *same job*.

      * **Example:** `steps.my_step_id.outcome == 'success'` (requires the previous step to have an `id` defined, e.g., `id: my_step_id`).
      * **Use Case:** Running a subsequent step only if a specific preceding step succeeded or produced a certain output.

-----

### 3\. Operators ⚖️

You can combine and evaluate conditions using standard logical and comparison operators:

  * `==` (equals)
  * `!=` (not equals)
  * `<`, `<=`, `>`, `>=` (comparison for numbers or versions)
  * `&&` (AND): Both conditions must evaluate to `true`.
      * **Example:** `if: success() && github.ref == 'refs/heads/main'` (Only deploy if the previous job was successful AND the push was to the `main` branch).
  * `||` (OR): At least one condition must evaluate to `true`.
      * **Example:** `if: failure() || cancelled()` (Run if the previous job either failed OR was cancelled).
  * `!` (NOT): Reverses the boolean outcome of a condition.
      * **Example:** `if: !cancelled()` (Run if the workflow was NOT cancelled).

-----

## Practical Examples 💡

Here's a comprehensive example demonstrating the use of `if` conditions at both the job and step levels, incorporating status check functions and contexts.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Run build script
        run: npm run build

  test:
    needs: build # This job depends on 'build' completing
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test

  deploy_to_production:
    needs: test # This job depends on 'test' completing
    runs-on: ubuntu-latest
    # ⭐ This job runs ONLY if the 'test' job succeeded AND the current branch is 'main'
    if: success() && github.ref == 'refs/heads/main'
    steps:
      - name: Deploy
        run: echo "🚀 Deploying to production environment..."

  send_failure_notification:
    needs: [build, test] # This job needs both 'build' and 'test' to have completed (regardless of their success)
    runs-on: ubuntu-latest
    # ⭐ This job runs ONLY if either 'build' OR 'test' failed
    if: failure()
    steps:
      - name: Notify on Failure
        run: echo "🔴 ALERT! Workflow failed in build or test stage. Please investigate."

  cleanup_artifacts:
    needs: [build, test, deploy_to_production, send_failure_notification] # Ensure all relevant previous jobs have finished
    runs-on: ubuntu-latest
    # ⭐ This job ALWAYS runs, regardless of success, failure, or cancellation of previous jobs
    if: always()
    steps:
      - name: Perform Cleanup
        run: echo "✅ Cleaning up temporary files and artifacts."
```
