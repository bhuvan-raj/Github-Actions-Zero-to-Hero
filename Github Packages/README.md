
# GitHub Packages & GitHub Actions Integration


<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Github%20Packages/assets/gh.webp" alt="Banner" />

This in-depth README covers **GitHub Packages**, its **integration with GitHub Actions**, and why it's a valuable tool for modern software development workflows. Ideal for students learning CI/CD.

---

## 📦 What is GitHub Packages?

GitHub Packages is a platform for hosting and managing software packages and containers, tightly integrated with your GitHub repositories.

It centralizes:
- Code
- Artifacts (packages, containers, libraries)
- Access control
- Billing

---

## 💡 Why Use GitHub Packages?

Modern applications rely on dependencies—external or internal. GitHub Packages simplifies package hosting by offering:

### ✅ Key Benefits

1. **Centralized Development**  
   One platform for code and packages—simplifies permissions and access.

2. **Integrated Permissions**  
   Package access inherits from GitHub repository permissions.

3. **Seamless GitHub Actions Integration**  
   Automate package publishing and consumption directly in CI/CD pipelines.

4. **Private & Public Hosting**  
   Use it for both internal distribution and public packages.

5. **Multi-language Support**  
   - **Containers (Docker, OCI)**
   - **npm (JavaScript)**
   - **Maven & Gradle (Java)**
   - **RubyGems (Ruby)**
   - **NuGet (.NET)**

---

## ⚙️ How GitHub Packages Works

Each supported package type has its own **registry endpoint**. You authenticate using:

- `GITHUB_TOKEN` (in Actions)
- Personal Access Token (PAT)

### 🔑 Core Concepts

- **Registries**: e.g., `npm.pkg.github.com`
- **Scopes**: e.g., `@org-name/package`
- **Permissions**: Inherited from repository or user/org
- **Versions**: Fully supported with semantic versioning

---

## 🔁 GitHub Packages + GitHub Actions = ❤️

Use GitHub Actions to build, test, publish, and consume packages.

### 🔁 Example Workflow Steps

1. **Checkout Code**  
   `actions/checkout@v4`

2. **Setup Runtime**  
   `actions/setup-node`, `actions/setup-java`, etc.

3. **Authentication**
   - Use `GITHUB_TOKEN` for same-repo or public access
   - Use PAT (stored in GitHub Secrets) for cross-repo or private access

4. **Build & Test**
   Run commands like `npm ci`, `mvn install`, etc.

5. **Publish**
   Use your package manager’s `publish` or `deploy` command

---

## 📄 Example: Publish npm Package

```yaml
name: Publish Node.js Package to GitHub Packages

on:
  release:
    types: [published]

jobs:
  build-and-publish:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          registry-url: 'https://npm.pkg.github.com'

      - name: Install dependencies
        run: npm ci

      - name: Build package
        run: npm run build

      - name: Publish package to GitHub Packages
        run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}
````

---

## 📥 Consuming Packages in Actions

To consume a GitHub Package:

* Set the registry in your config (`.npmrc`, `.m2/settings.xml`, etc.)
* Authenticate using `GITHUB_TOKEN` or PAT

---

## 🔐 Security Best Practices

1. **Least Privilege**
   Scope tokens to only `read:packages` or `write:packages`.

2. **Use GitHub Secrets**
   Never hardcode PATs. Always use secrets.

3. **Token Rotation**
   Rotate PATs regularly.

4. **Log Masking**
   Use `::add-mask::VALUE` to hide secrets in logs.

5. **Security Scans**
   Use Dependabot and secret scanning.

6. **Branch Protection**
   Protect branches that trigger publishing workflows.

---

## 💰 Pricing

| Type                 | Cost                                                   |
| -------------------- | ------------------------------------------------------ |
| Public Repositories  | **Free**                                               |
| Private Repositories | Limited free tier (e.g., 500MB storage, 1GB bandwidth) |

> ⚠️ Bandwidth used by GitHub Actions is **free**, even for private packages.

---

## 🛠️ Use Cases

* **Internal Package Hosting**: Share libraries across internal projects
* **CI/CD Artifact Management**: Store release builds, deployable artifacts
* **Dependency Caching**: Faster builds with internal mirrors
* **Release Automation**: Automate version publishing on release
* **Docker Image Hosting**: Use GHCR for container builds

---

## 🎓 Summary

Teaching GitHub Packages with GitHub Actions equips students to:

* Automate builds and tests
* Manage and distribute artifacts
* Build secure and efficient CI/CD pipelines

> 📘 **Skill Outcomes**: CI/CD, package management, GitHub Actions proficiency, security, artifact versioning.

---

```

Let me know if you want this saved as a downloadable file or tailored to a specific language (e.g., Java/Maven, Docker, etc.), Bubu!
```
