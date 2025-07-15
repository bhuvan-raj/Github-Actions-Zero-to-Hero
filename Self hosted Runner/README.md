Here's a complete **hands-on lab** for you, Bubu, to configure an **EC2 instance as a self-hosted GitHub Actions runner** and run a **Maven build** on it.

---

## 🧪 Lab: GitHub Actions Self-Hosted Runner on EC2 with Maven Build

---

### 🧱 **Lab Objective**

You will:

1. Launch an Ubuntu EC2 instance.
2. Install Java and Maven.
3. Register it as a self-hosted runner to a GitHub repository.
4. Run a Maven build using GitHub Actions on this runner.

---

### 🔧 **Step 1: Launch EC2**

* **AMI**: Ubuntu 22.04
* **Instance Type**: t2.micro or t2.medium
* **Security Group**:

  * SSH (port 22) open to your IP
  * No other ports needed

SSH into your EC2:

```bash
ssh -i <your-key.pem> ubuntu@<your-ec2-public-ip>
```

---

### 🧰 **Step 2: Install Java & Maven**

```bash
# Update
sudo apt update

# Install Java (OpenJDK 21)
sudo apt install openjdk-21-jdk -y

# Install Maven
sudo apt install maven -y

# Verify
java -version
mvn -v
```

---

### 🏃‍♂️ **Step 3: Register Self-Hosted Runner to GitHub Repo**

1. Go to your GitHub repo > **Settings** > **Actions** > **Runners**
2. Click **New self-hosted runner**
3. Choose OS: **Linux**, Arch: **x64**
4. Follow the commands it gives you. Example:

```bash
# Create a folder
mkdir actions-runner && cd actions-runner

# Download the runner binary
curl -o actions-runner-linux-x64-2.317.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz

# Extract it
tar xzf ./actions-runner-linux-x64-2.317.0.tar.gz

# Configure it (you’ll get the token & repo URL from GitHub)
./config.sh --url https://github.com/<your-username>/<your-repo> --token <runner-token>
```

5. Start the runner:

```bash
./run.sh
```

> 📌 Optional: To run it as a **service**, run:

```bash
sudo ./svc.sh install
sudo ./svc.sh start
```

---

### 📄 **Step 4: Add GitHub Actions Workflow**

In your GitHub repo, create a file:

`.github/workflows/maven-self-hosted.yml`

```yaml
name: Maven Build on Self-Hosted Runner

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: self-hosted  # 👈 use self-hosted runner
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Run Maven build
        run: mvn clean install
```

> 📦 Make sure your repo has a valid `pom.xml`.

---

### ✅ **Step 5: Trigger the Workflow**

Push code to `main` branch:

```bash
git add .
git commit -m "Trigger self-hosted runner build"
git push origin main
```

---

### 🎯 Expected Result

* GitHub Actions triggers the `maven-self-hosted.yml`.
* The job runs on your EC2 instance.
* You see logs like:

  ```
  Runner name: ip-172-31-XX-XX
  Job: Maven Build on Self-Hosted Runner
  mvn clean install ...
  BUILD SUCCESS
  ```

---

### 🧼 Cleanup

* Stop the runner service: `sudo ./svc.sh stop`
* Remove runner folder: `rm -rf actions-runner`
* Terminate EC2 instance

---

Want this in script format or Terraform-based automation? Just ask!
