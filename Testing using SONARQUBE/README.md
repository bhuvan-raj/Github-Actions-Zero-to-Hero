# Code Quality Assurance with SonarQube and GitHub Actions 🧪


<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Testing%20using%20SONARQUBE/assets/testing.png" alt="Banner" />

This project demonstrates how to integrate **SonarQube** for continuous code quality inspection into a **GitHub Actions** workflow. By the end of this lab, you'll understand the importance of code quality, how to set up a local SonarQube instance, tunnel it for public access, and automate code analysis in your CI/CD pipeline.

## 📚 Table of Contents

1. [What is Software Testing?](#what-is-software-testing)
2. [Why Do We Need Software Testing?](#why-do-we-need-software-testing)
3. [Major Software Testing Tools](#major-software-testing-tools)
4. [What is SonarQube?](#what-is-sonarqube)
5. [Why SonarQube?](#why-sonarqube)
6. [Project Setup and Lab Steps](#project-setup-and-lab-steps)
- 6.1  [Prerequisites](#prerequisites)
- 6.2   [Step 1: Set up Local SonarQube with Docker](#step-1-set-up-local-sonarqube-with-docker)
- 6.3  [Step 2: Tunnel SonarQube with Ngrok](#step-2-tunnel-sonarqube-with-ngrok)
- 6.4  [Step 3: Create a Project in SonarQube](#step-3-create-a-project-in-sonarqube)
- 6.5  [Step 4: Configure GitHub Secrets](#step-4-configure-github-secrets)
- 6.6  [Step 5: Create GitHub Actions Workflow](#step-5-create-github-actions-workflow)
- 6.7  [Step 6: Trigger Scan and View Results](#step-6-trigger-scan-and-view-results)
7. [Conclusion](#conclusion)



## What is Software Testing?

**Software testing** is the process of evaluating and verifying that a software product or application functions correctly, securely, and efficiently according to its specific requirements. It involves executing a system or component to identify any gaps, errors, or missing requirements contrary to the actual requirements. Think of it as a quality check before a product goes to market.

-----

## Why Do We Need Software Testing?

Software testing is crucial for several reasons:

  * **Ensures Quality:** It helps identify defects and ensures the software meets specified requirements and user expectations.
  * **Reduces Costs:** Detecting bugs early in the development lifecycle is significantly cheaper than fixing them after deployment.
  * **Improves User Satisfaction:** Bug-free and well-performing software leads to a better user experience, increasing customer satisfaction and trust.
  * **Enhances Security:** Testing helps uncover vulnerabilities that could be exploited by malicious actors, protecting data and systems.
  * **Boosts Confidence:** Thorough testing provides confidence that the software will perform reliably in various scenarios.
  * **Ensures Compliance:** For many industries, testing ensures adherence to regulatory standards and guidelines.

-----

## Major Software Testing Tools

The world of software testing is vast, with many tools specializing in different areas:

  * **Functional Testing:**
      * **Selenium:** Widely used for automating web browser interactions.
      * **Cypress:** A modern, developer-friendly tool for end-to-end web testing.
      * **Playwright:** Microsoft's framework for reliable end-to-end testing across browsers.
  * **Performance Testing:**
      * **Apache JMeter:** Open-source tool for load and performance testing.
      * **Gatling:** A high-performance load testing tool.
  * **Mobile Testing:**
      * **Appium:** Cross-platform mobile automation framework.
      * **Espresso (Android) / XCUITest (iOS):** Native mobile UI testing frameworks.
  * **API Testing:**
      * **Postman:** Popular tool for API development, testing, and documentation.
      * **SoapUI:** Open-source tool for testing SOAP and REST APIs.
  * **Static Code Analysis:**
      * **SonarQube:** Our focus for this lab, used for continuous inspection of code quality and security.
      * **ESLint (JavaScript), Pylint (Python), Checkstyle (Java):** Language-specific linters.

-----

## What is SonarQube?

<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Testing%20using%20SONARQUBE/assets/sq.webp" alt="Banner" />


**SonarQube** is an **open-source platform** developed by SonarSource for **continuous inspection of code quality and security**. It performs **static analysis** of your code to detect bugs, vulnerabilities, code smells, duplication, and adherence to coding standards across various programming languages. Essentially, it acts as a "spell checker" and "grammar checker" for your code, helping you identify and fix issues *before* they become costly problems.

-----

## Why SonarQube?

SonarQube stands out for several compelling reasons:

  * **Comprehensive Analysis:** It provides a holistic view of your code quality, covering a wide range of issues beyond just bugs.
  * **Early Detection:** By integrating into your CI/CD pipeline, it catches issues early in the development process, reducing the cost and effort of fixing them.
  * **Quality Gates:** SonarQube allows you to define "Quality Gates" – a set of predefined conditions that your code must meet to pass analysis. This enforces code quality standards across your team and prevents the merge of low-quality code.
  * **Technical Debt Management:** It helps visualize and manage technical debt, guiding developers to prioritize and refactor problematic areas.
  * **Security Vulnerability Detection:** SonarQube can identify common security vulnerabilities, helping to build more secure applications.
  * **Integration Friendly:** It integrates seamlessly with popular build tools (Maven, Gradle) and CI/CD platforms (GitHub Actions, Jenkins, GitLab CI).
  * **Visibility and Reporting:** Provides insightful dashboards and detailed reports, making it easy to track quality trends and progress over time.

-----

## Project Setup and Lab Steps

This section will guide you through setting up a local SonarQube instance, exposing it publicly using Ngrok, and integrating it with a GitHub Actions workflow to analyze your Java project.

### Prerequisites

  * **Docker Desktop:** Installed and running on your machine.
  * **Ngrok Account & Authtoken:** Sign up at [ngrok.com](https://ngrok.com/) and follow their instructions to connect your authtoken to your local ngrok client.
  * **GitHub Account:** With a repository containing a simple Java Maven project.

-----

### Step 1: Set up Local SonarQube with Docker

First, let's get your local SonarQube server up and running using Docker.

1.  **Open your terminal or command prompt.**

2.  **Pull the SonarQube Docker image:**

    ```bash
    docker pull sonarqube:latest
    ```

3.  **Run the SonarQube container:**

    ```bash
    docker run -d --name sonarqube -p 9000:9000 sonarqube
    ```

    This command does the following:

      * `-d`: Runs the container in detached mode (in the background).
      * `--name sonarqube`: Assigns the name `sonarqube` to your container for easy management.
      * `-p 9000:9000`: Maps port `9000` on your host machine to port `9000` inside the container, where SonarQube runs.

4.  **Verify SonarQube is running:**
    Open your web browser and navigate to `http://localhost:9000`. You should see the SonarQube login page.

5.  **Log in to SonarQube:**

      * **Username:** `admin`
      * **Password:** `admin`
      * **Important:** You'll be prompted to change your password immediately. Do so for security.

-----

### Step 2: Tunnel SonarQube with Ngrok

Since GitHub Actions runs in the cloud, it can't directly access your `localhost`. We'll use Ngrok to create a secure tunnel to your local SonarQube instance, giving it a publicly accessible URL.

1.  **Open a new terminal or command prompt.**

2.  **Run the Ngrok command:**

    ```bash
    ngrok http 9000
    ```

    This command tells Ngrok to create a public tunnel to your local port `9000`.

3.  **Note the public URL:**
    Ngrok will output information, including a "Forwarding" URL (e.g., `https://xxxx-xx-xxx-xx-xxx.ngrok-free.app`). **Copy this HTTPS URL.** This will be your `SONAR_HOST_URL`.

-----

### Step 3: Create a Project in SonarQube

Now, let's set up a new project within your SonarQube instance.

1.  **Log in to your SonarQube dashboard** using the Ngrok public URL you just obtained (e.g., `https://xxxx-xx-xxx-xx-xxx.ngrok-free.app`).
2.  From the dashboard, click on **"Create New Project"** or the **"+"** button and select **"Manually"**.
3.  **Provide Project Details:**
      * **Project Key:** Choose a unique key (e.g., `my-java-app` or `github-actions`). This is what you'll use in your GitHub Actions workflow.
      * **Display Name:** A human-readable name for your project (e.g., `My Java App` or `GitHub Actions Demo`).
4.  Click **"Setup"**.
5.  **Generate a User Token:**
      * SonarQube will then prompt you to generate a token for analysis. **Provide a name** for the token (e.g., `github-actions-token`).
      * Click **"Generate"**.
      * **Crucially, copy this token immediately\!** You will not be able to see it again. This will be your `SONAR_TOKEN`.

-----

### Step 4: Configure GitHub Secrets

To securely provide your SonarQube host URL and token to your GitHub Actions workflow, you'll store them as repository secrets.

1.  **Navigate to your GitHub repository** in your web browser.
2.  Go to **`Settings`** (top right tab).
3.  In the left sidebar, click on **`Secrets and variables`** \> **`Actions`**.
4.  Click **`New repository secret`**.
5.  **Create `SONAR_TOKEN` secret:**
      * **Name:** `SONAR_TOKEN`
      * **Value:** Paste the SonarQube token you generated in the previous step.
      * Click **`Add secret`**.
6.  **Create `SONAR_HOST_URL` secret:**
      * **Name:** `SONAR_HOST_URL`
      * **Value:** Paste the Ngrok public HTTPS URL you copied in Step 2.
      * Click **`Add secret`**.

-----

### Step 5: Create GitHub Actions Workflow

- Get inside the sonarqube, Go to your project- scroll down and click on maven - click on Github Action template
- Copy the listed Yaml manifest and paste it on .github/workflows/test.yml
- **Commit this file** to your repository.
-----

### Step 6: Trigger Scan and View Results

With everything set up, it's time to trigger your first SonarQube scan\!

1.  **Push a change to your `main` branch** or **create a new Pull Request** in your GitHub repository.
2.  **Monitor the GitHub Actions run:**
      * Go to the **`Actions`** tab in your GitHub repository.
      * You'll see your "Build and Analyze with SonarQube" workflow running. Click on it to see the live logs.
      * Ensure the "Build and analyze" step completes successfully.
3.  **View results in SonarQube:**
      * Navigate back to your SonarQube dashboard (using your Ngrok URL).
      * You should now see your project listed with its analysis results. Explore the dashboard to see detected bugs, vulnerabilities, code smells, and other metrics.

**Important Notes:**

  * **Ngrok Session:** Keep the Ngrok terminal window open and running throughout your lab session. If it closes, the public URL will become invalid, and your GitHub Actions won't be able to reach SonarQube. You'll get a new URL each time you run `ngrok http 9000`. If the URL changes, remember to update the `SONAR_HOST_URL` secret in GitHub.
  * **Security:** For a production environment, you would typically deploy SonarQube on a dedicated server with a fixed domain and proper network security, rather than using Ngrok. Ngrok is excellent for local development and demonstration purposes.

-----

## Conclusion

Congratulations\! You've successfully implemented automated code quality analysis using SonarQube and GitHub Actions. You now have a powerful tool to continuously inspect your code, identify issues early, and maintain a high standard of quality in your software development projects. This is a vital skill in modern software engineering practices.
