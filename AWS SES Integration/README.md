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
