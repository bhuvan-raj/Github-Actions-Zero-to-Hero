# 🏃‍♂️ Self-Hosted Runners in GitHub Actions

<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Self%20hosted%20Runner/assets/runner.png" alt="Banner" />


## 📘 What is a Self-Hosted Runner?

A **self-hosted runner** is a system that you manage and maintain, which executes your GitHub Actions workflows instead of GitHub’s default cloud-hosted runners.

It can be:
- A physical machine (on-prem)
- A virtual machine (like an EC2 instance)
- A container or even a Raspberry Pi

Once registered, GitHub sends jobs to your self-hosted runner, which polls for work and executes CI/CD tasks using the GitHub Actions runner agent.

---

## ⚙️ How It Works

1. You configure your system (Linux, Windows, or macOS)
2. You download and run the GitHub Actions runner binary
3. You register the runner to a GitHub repository or organization using a token
4. The runner listens for jobs and runs them when triggered (e.g. push, PR, schedule)

> GitHub communicates with the runner over HTTPS — **not SSH** — using a secure polling mechanism.

---

## 🎯 Why Use Self-Hosted Runners?

### ✅ 1. **Customization**
- Install any tools, runtimes, or dependencies you want (e.g., Java 21, Maven, Docker-in-Docker, Terraform, etc.)
- Full control over OS, software versions, environment variables, and pre-installed caches

### ✅ 2. **Performance**
- Use larger, faster hardware than GitHub's free runners
- Avoid cold starts
- Cache Docker layers, Maven dependencies, or NPM packages locally

### ✅ 3. **Cost Optimization**
- No need to pay for GitHub-hosted minutes (especially for large enterprise-scale workflows)
- Reuse existing infrastructure (like internal VMs or cloud instances already under budget)

### ✅ 4. **Security and Compliance**
- Run CI/CD in a secure, private network (e.g., behind a firewall or in a VPC)
- Access internal systems, databases, and protected APIs
- Meet regulatory or compliance requirements (e.g., data residency, audit trails)

### ✅ 5. **Networking Control**
- Use private subnets, NATs, or proxy configurations
- Customize IPs for allowlists and integrations with firewalls, cloud APIs, etc.

---

## ⚠️ Limitations and Considerations

| Area                | Limitation / Concern                                           |
|---------------------|----------------------------------------------------------------|
| **Maintenance**     | You are responsible for updates, patches, and runner health    |
| **Security**        | Be cautious: GitHub Actions from forks can run on your runner  |
| **Scalability**     | Must manually scale if multiple concurrent jobs are needed     |
| **Access Controls** | Requires strict IAM and network firewall rules                 |

---

## 🆚 GitHub-Hosted vs Self-Hosted

| Feature              | GitHub-Hosted Runner            | Self-Hosted Runner                |
|----------------------|----------------------------------|-----------------------------------|
| Managed by GitHub    | ✅ Yes                           | ❌ No (you manage it)             |
| Custom tools/runtime | ❌ Limited                       | ✅ Full control                   |
| Concurrent jobs      | Limited by plan                 | Unlimited (but depends on setup) |
| Network access       | Public internet only            | Can access private/internal net  |
| Cost (GitHub minutes)| ✅ Yes (billed per min)          | ❌ No (but you pay for infra)     |

---

## 📦 When to Use Self-Hosted Runners

Use self-hosted runners when:
- You need specific software or build environments
- Your jobs require access to internal services or APIs
- You want to reduce GitHub Actions billing costs
- You have long-running, memory-intensive, or performance-sensitive jobs
- You need to use private IPs or on-premise systems

---

## 📚 References

- [GitHub Docs – Self-Hosted Runners](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners)
- [Actions Runner Releases](https://github.com/actions/runner/releases)


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
4. Follow the commands it gives you.

---

### 🧼 Cleanup

* Stop the runner service: `sudo ./svc.sh stop`
* Remove runner folder: `rm -rf actions-runner`
* Terminate EC2 instance
