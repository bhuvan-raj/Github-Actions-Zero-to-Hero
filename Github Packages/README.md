
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

# LAB MANUAL

## CI/CD Pipeline to Build and Publish Java JAR to GitHub Packages using Maven & GitHub Actions

---

## 1. Lab Objective

To implement a CI/CD pipeline that:

* Builds a Java application using Maven
* Packages it as a JAR
* Publishes the JAR to **GitHub Packages (Maven Registry)**
* Uses **GitHub Actions with secure authentication**

---

## 2. Tools & Technologies

* Java 17
* Apache Maven
* GitHub Actions
* GitHub Packages (Maven)
* Ubuntu GitHub-hosted Runner

---

## 3. Repository Structure (New)

```
java-github-packages-demo/
├── .github/
│   └── workflows/
│       └── maven-publish.yml
├── src/
│   └── main/
│       └── java/
│           └── org/
│               └── demo/
│                   └── MainApp.java
├── pom.xml
├── settings.xml
└── README.md
```

---

## 4. Java Application Source Code

### `MainApp.java`

```java
package org.demo;

public class MainApp {

    public static void main(String[] args) {
        System.out.println("CI/CD pipeline executed successfully. Artifact published to GitHub Packages.");
    }
}
```

---

## 5. Maven Project Configuration

### `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>org.demo</groupId>
    <artifactId>java-github-packages-demo</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <name>Java GitHub Packages Demo</name>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

    <!-- GitHub Packages Deployment Configuration -->
    <distributionManagement>
        <repository>
            <id>github</id>
            <name>GitHub Packages</name>
            <url>https://maven.pkg.github.com/<GITHUB_OWNER>/<REPOSITORY_NAME></url>
        </repository>
    </distributionManagement>

    <build>
        <plugins>
            <!-- Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
            </plugin>

            <!-- JAR Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>org.demo.MainApp</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

🔹 Replace:

```
<GITHUB_OWNER>/<REPOSITORY_NAME>
```

Example:

```
https://maven.pkg.github.com/Bubu/java-github-packages-demo
```

---

## 6. Maven Authentication Configuration

### `settings.xml`

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
          https://maven.apache.org/xsd/settings-1.0.0.xsd">

    <servers>
        <server>
            <id>github</id>
            <username>your-username</username>
            <password>${env.MAVEN_GITHUB_TOKEN}</password>
        </server>
    </servers>

</settings>
```

🔹 `id` **must exactly match** the `distributionManagement` repository ID.

---

### 7. Create a PAT & Repository Secret 

**Creating PAT**

Profile - Settings- Developer Settings - Personal Access Token - Tokens Classic - New - Tokens(Classic) 
- Give the token name
- scope - Packages Read , Packages Write , Workflow
  
**Creating Repository Secrets**
Repo - Settings - Secrets & Variables - New Repository Secret

- Secret Name - Your Github Username
- Secret - PAT

## 7. GitHub Actions Workflow

### `.github/workflows/maven-publish.yml`

```yaml
name: Publish Maven Package

on:
  push:
    branches: [ "main" ]

jobs:
  publish:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
          cache: maven

      - name: Publish to GitHub Packages
        run: mvn clean deploy --settings settings.xml
        env:
         GITHUB_TOKEN: ${{ secrets.MAVEN_GITHUB_TOKEN }}
```

---

## 8. CI/CD Execution Flow

1. Developer pushes code to the `main` branch
2. GitHub Actions workflow is triggered
3. Runner installs Java and Maven
4. Maven lifecycle executes:

   * `compile`
   * `package`
   * `deploy`
5. JAR is uploaded to **GitHub Packages Maven Registry**

---

## 9. Artifact Verification

* Navigate to your GitHub repository
* Open **Packages** section
* You will see:

  ```
  org.demo:java-github-packages-demo:1.0.0
  ```

---

## 10. Key Interview Notes

* `mvn deploy` publishes artifacts
* `distributionManagement` defines target registry
* `settings.xml` handles authentication
* `GITHUB_TOKEN` is injected automatically
* GitHub Actions permissions control package publishing

---
