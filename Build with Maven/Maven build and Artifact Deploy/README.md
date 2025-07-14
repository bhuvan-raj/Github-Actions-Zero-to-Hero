# 📦 Maven Java Project — Build and Publish to GitHub Packages

This project demonstrates how to:
- Build a simple Java application using **Maven**
- Use **GitHub Actions** to automate the build process
- Deploy the packaged `.jar` file to **GitHub Packages (Maven Registry)**

---

## 🚀 Setup Instructions

### 🔐 1. Create a GitHub Personal Access Token

Go to: [https://github.com/settings/tokens](https://github.com/settings/tokens)

Generate a **classic token** with the following scopes:
- `write:packages`
- `read:packages`
- `repo`

---

### 🔑 2. Add Secrets to GitHub Repo

Go to your GitHub repo → `Settings > Secrets and variables > Actions`  
Click `New repository secret` and add:

| Name      | Value                  |
|-----------|------------------------|
| `USERNAME`| Your GitHub username   |
| `TOKEN`   | The Personal Access Token (PAT) you created |

---

### 📦 3. Configure Your `pom.xml`

---

### ⚙️ 4. GitHub Actions Workflow

The file `.github/workflows/maven-build-publish.yml`:

* Checks out the code
* Sets up Java & Maven
* Builds the app
* Deploys the `.jar` to GitHub Packages

You can trigger it by:

* Pushing to the `main` branch
* Manually from the Actions tab

---

## 📦 Published Artifacts

After a successful build, your package will be available at:

```
https://github.com/YOUR_USERNAME/YOUR_REPO/packages
```

It includes:

* `myapp-1.0.0.jar` — the built application
* `myapp-1.0.0.pom` — metadata
* `.sha1` and `.md5` files — hash validations (security)

---

## 🧪 Using This Package in Another Maven Project

To consume the artifact, add this to your `pom.xml` in another project:

```xml
<repositories>
  <repository>
    <id>github</id>
    <url>https://maven.pkg.github.com/YOUR_USERNAME/YOUR_REPO</url>
  </repository>
</repositories>

<dependency>
  <groupId>com.example</groupId>
  <artifactId>myapp</artifactId>
  <version>1.0.0</version>
</dependency>
```

Also update your `settings.xml` with GitHub credentials (like deployment).

---

## 🧠 Notes

* Checksum files (`.sha1`, `.md5`) are auto-generated and used for integrity checks
* Version bump in `pom.xml` is needed for redeploying (Maven does not overwrite)
* GitHub Packages requires authentication for both read/write operations

---
