#  Conditional Email Notifications with GitHub Actions & AWS SES 📧

This project demonstrates how to set up a robust CI/CD workflow in GitHub Actions that sends **conditional email notifications** based on the success or failure of your build, leveraging **AWS Simple Email Service (SES)** for reliable delivery. This is a crucial skill for any developer or DevOps engineer, ensuring you stay informed about your automated pipelines.

-----

## 🎯 Learning Objectives

By completing this project, you will learn:

  * How to create a basic GitHub Actions workflow.
  * How to integrate with **AWS IAM using OpenID Connect (OIDC)** for secure, credential-less authentication from GitHub Actions to AWS services.
  * How to send emails using **AWS SES (Simple Email Service)** via the AWS CLI.
  * How to use **`if` conditions (`success()` and `failure()`)** in GitHub Actions to trigger steps conditionally based on the outcome of previous steps.
  * The importance of **GitHub Secrets** for storing sensitive information.
  * The significance of **email identity verification** in AWS SES.

-----

## ⚙️ Project Setup

### 1\. AWS Account & SES Configuration

Before you start, you'll need an AWS account and some setup in AWS SES.

  * **Verify Sender Email Identity:**

      * Go to the [AWS SES Console](https://console.aws.amazon.com/ses/).
      * Under "Configuration" in the left navigation, click on **"Verified identities"**.
      * Click **"Create identity"**, select **"Email address"**, and enter the email address you wish to send emails *from* (e.g., `your-verified-sender@example.com`).
      * **Crucially:** AWS will send a verification email to this address. You **must click the link** in this email to complete verification.

  * **Request Production Access (if needed):**

      * If your AWS account is new, it's likely in **SES Sandbox mode**. In this mode, you can *only* send emails to recipient email addresses that are *also* verified in your SES account.
      * If your intended recipient (`cherry107123@gmail.com` in our example) is not verified, you **must request production access** for your SES account. On the SES console dashboard, look for a banner or link to "Request production access" and follow the instructions. This process takes time for AWS approval.

  * **Configure IAM OIDC Provider for GitHub Actions:**

      * This is a **one-time setup per AWS account** for secure authentication.
      * Go to the [AWS IAM Console](https://www.google.com/search?q=https://console.aws.amazon.com/iam/home%23/providers).
      * Under "Access management," click **"Identity providers"**.
      * Click **"Add provider"**.
          * Provider type: `OpenID Connect`
          * Provider URL: `https://token.actions.githubusercontent.com`
          * Audience: `sts.amazonaws.com`
          * Click "Add provider."

  * **Create an IAM Role for GitHub Actions:**

      * Go to **"Roles"** in the IAM Console.
      * Click **"Create role"**.
      * Trusted entity type: **"Web identity"**.
      * Identity provider: Select `token.actions.githubusercontent.com`.
      * Audience: Select `sts.amazonaws.com`.
      * **GitHub Organization (Required):** Enter your GitHub organization name. If you're using a personal repository, enter your **GitHub username** here.
      * **GitHub Repository (Optional, but Recommended):** Enter the name of your repository (e.g., `your-repo-name`).
      * Click "Next."
      * **Add Permissions:** Attach a policy that allows sending emails via SES. You can search for `AmazonSESFullAccess` for quick setup, or create a custom policy for more granular control (recommended minimum permissions below):
        ```json
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": [
                        "ses:SendEmail",
                        "ses:SendRawEmail"
                    ],
                    "Resource": "*"
                }
            ]
        }
        ```
      * Click "Next," give the role a descriptive name (e.g., `GitHubActionsSesSenderRole`), and "Create role."
      * **Copy the Role ARN:** After creating, click on the role and **copy its ARN** (e.g., `arn:aws:iam::123456789012:role/GitHubActionsSesSenderRole`). You'll need this for your workflow.

-----

### 2\. GitHub Repository Setup

  * **Create a Repository:** If you don't have one, create a new GitHub repository for this project.
  * **Create Workflow File:** In your repository, create a directory `.github/workflows/` and inside it, create a file named `ses-email-notification.yml` (or any `.yml` file name).
  * **Paste the Workflow Code:** Copy the complete workflow code from the next section and paste it into `ses-email-notification.yml`.

-----

## 📝 The Workflow Code (`.github/workflows/ses-email-notification.yml`)

```yaml
name: CI with AWS SES Email (OIDC) # Name of your workflow

on:
  push:
    branches:
      - main # Triggers on pushes to the 'main' branch
  workflow_dispatch: # Allows manual triggering from the GitHub Actions tab

# Permissions required for OIDC authentication. DO NOT REMOVE.
permissions:
  id-token: write # Required to fetch the OIDC token for AWS authentication
  contents: read  # Required for 'actions/checkout' to read your repository code

jobs:
  build:
    runs-on: ubuntu-latest # Specifies the runner environment

    steps:
      - name: Checkout code
        uses: actions/checkout@v4 # Action to clone your repository

      # Step to configure AWS credentials using the IAM Role and OIDC
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          # IMPORTANT: Replace 'YOUR_IAM_ROLE_ARN_GOES_HERE' with the actual ARN of your IAM role from AWS!
          role-to-assume: YOUR_IAM_ROLE_ARN_GOES_HERE
          # This must be the AWS region where your SES sender identity is verified.
          aws-region: us-east-1 # Example: us-east-1

      - name: Run build (example)
        run: |
          echo "Simulating a build process..."
          # Replace this with your actual build commands, e.g., 'mvn clean install' for Maven, 'npm test', etc.
          # To test the email notification for a FAILED build, uncomment the line below:
          # exit 1

      - name: Send Email Notification (on success)
        # This step runs ONLY if all previous steps in this job have succeeded.
        if: success()
        run: |
          # The 'aws ses send-email' command directly calls the AWS SES API.
          # The 'from' address MUST be an identity you verified in AWS SES.
          # The 'to' address will receive the email. If your SES account is in sandbox, 'to' must also be verified.
          aws ses send-email \
            --from 'bhuvanraj107123@gmail.com' \
            --destination 'ToAddresses="cherry107123@gmail.com"' \
            --message 'Subject={Data="✅ Build Success - ${{ github.repository }}"},Body={Text={Data="Build for ${{ github.repository }} on branch `${{ github.ref_name}}` was successful!\n\nCommit: `${{ github.sha}}`\nWorkflow Run: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"}}' \
            --region us-east-1 # Ensure this matches your aws-region above

      - name: Send Email Notification (on failure)
        # This step runs ONLY if any previous step in this job has failed.
        if: failure()
        run: |
          aws ses send-email \
            --from 'bhuvanraj107123@gmail.com' \
            --destination 'ToAddresses="cherry107123@gmail.com"' \
            --message 'Subject={Data="❌ Build FAILED - ${{ github.repository }}"},Body={Text={Data="*ALERT:* Build for ${{ github.repository }} on branch `${{ github.ref_name}}` *FAILED!*\n\nCommit: `${{ github.sha}}`\n*Please review logs:* ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"}}' \
            --region us-east-1
```

-----

## ▶️ How to Run and Test

1.  **Commit and Push:** Save the `ses-email-notification.yml` file and push it to your `main` branch on GitHub.
2.  **Monitor Workflow:** Go to the "Actions" tab in your GitHub repository. You should see a new workflow run initiated by your push.
3.  **Test Success:** Allow the workflow to complete successfully. You should receive a "Build Success" email at `cherry107123@gmail.com`.
4.  **Test Failure:**
      * Edit your `ses-email-notification.yml` file.
      * In the `Run build (example)` step, **uncomment** the line `# exit 1`. This will force the build step to fail.
      * Commit and push this change to your `main` branch.
      * Monitor the workflow run. It should fail.
      * You should now receive a "Build FAILED" email at `cherry107123@gmail.com`.
      * **Remember to re-comment or remove `exit 1`** after testing to allow your builds to pass again.

-----

## ❓ Troubleshooting Tips

  * **Email Not Received (Success/Failure):**
      * **Check Spam/Junk Folder:** Sometimes emails land there.
      * **AWS SES Sandbox Mode:** This is the \#1 reason if your recipient email is not verified. Ensure you have production access if sending to unverified recipients.
      * **AWS Region Mismatch:** Verify that the `aws-region` in your workflow (e.g., `us-east-1`) is the *exact* region where your `from` email identity (`bhuvanraj107123@gmail.com`) is verified in SES.
  * **Workflow Error in GitHub Actions:**
      * **"Error: Process completed with exit code 252" / "Unknown options":** This means there's a syntax error in your `aws ses send-email` command. Double-check for extra spaces, comments after backslashes, or missing quotes around arguments.
      * **"Access Denied" / IAM Permissions:** Revisit your IAM Role's **Permissions Policy** in AWS. Ensure it grants `ses:SendEmail` and `ses:SendRawEmail` actions.
      * **IAM Role ARN:** Make sure the `role-to-assume` in your `Configure AWS Credentials` step is the **exact and correct ARN** for the IAM role you created.
      * **`permissions` block:** Ensure you have `id-token: write` and `contents: read` at the job or workflow level.
      * **CloudWatch Logs:** For more detailed error messages from AWS, check the CloudWatch logs associated with your SES sending attempts.

This project will provide a solid foundation for understanding secure and conditional communication within your automated workflows\!
