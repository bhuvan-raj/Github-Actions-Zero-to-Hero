

# Build with Maven

<img src="https://github.com/bhuvan-raj/Github-Actions/blob/main/Build%20with%20Maven/assets/maven.png" alt="Banner" />


## Table of Contents

  * [About](https://www.google.com/search?q=%23about)
  * [What is Maven?](https://www.google.com/search?q=%23what-is-maven)
  * [Getting Started](https://www.google.com/search?q=%23getting-started)
      * [Prerequisites](https://www.google.com/search?q=%23prerequisites)
      * [Building the Project](https://www.google.com/search?q=%23building-the-project)
      * [Important Maven Commands](https://www.google.com/search?q=%23important-maven-commands)
  * [Installing Maven on a Self-Hosted Runner](https://www.google.com/search?q=%23installing-maven-on-a-self-hosted-runner)
  * [Contributing](https://www.google.com/search?q=%23contributing)
  * [License](https://www.google.com/search?q=%23license)
  * [Contact](https://www.google.com/search?q=%23contact)

## About
This repository demonstrates how to build a simple Java application using **Apache Maven**, a powerful build automation tool used primarily for Java projects. It also explains how to configure Maven on self-hosted runners for CI/CD pipelines (like GitHub Actions).

## What is Maven?

Apache Maven is a software project management and comprehension tool. Based on the concept of a Project Object Model (POM), Maven can manage a project's build, reporting, and documentation from a central piece of information.

**Apache Maven** is a build tool that helps developers:
- Manage project dependencies (JAR files).
- Compile, test, and package Java applications.
- Standardize build processes across teams.
- Automate tasks like running tests, generating reports, and deploying builds.


Key features of Maven include:

  * **Standardized Project Structure:** Maven enforces a convention-over-configuration approach, providing a consistent directory layout for projects.
  * **Dependency Management:** It automatically downloads and manages project dependencies (libraries, frameworks, etc.) from remote repositories, avoiding "JAR hell."
  * **Build Lifecycle:** Maven defines a clear lifecycle with phases (e.g., `compile`, `test`, `package`, `install`, `deploy`) that you can hook into.
  * **Plugins:** Extensible through a rich plugin architecture, allowing for tasks like code generation, reporting, and deployment.
  * **Consistency:** Promotes consistent builds across different environments.

## Getting Started

To get a local copy up and running follow these simple steps.

### Prerequisites to Download Maven on a server

  * **Java Development Kit (JDK):** Maven requires a JDK to be installed. Ensure you have JDK 8 or higher. You can download it from the [Oracle Website](https://www.oracle.com/java/technologies/downloads/) or use OpenJDK distributions like [Adoptium](https://adoptium.net/).
  * **Apache Maven:** While not strictly necessary for building if you just use the wrapper, it's good to have a local installation for general development. See the "Installing Maven on a Self-Hosted Runner" section for instructions.


### Important Maven Commands

Here are some commonly used Maven commands:

  * **`./mvnw clean`**: Cleans the project by deleting the `target` directory. This removes all compiled classes, test results, and packaged artifacts.
  * **`./mvnw compile`**: Compiles the source code of the project.
  * **`./mvnw test`**: Compiles the test source code and runs the unit tests.
  * **`./mvnw package`**: Compiles, tests, and packages the compiled code into a distributable format (e.g., JAR, WAR). The output is placed in the `target` directory.
  * **`./mvnw verify`**: Runs all checks to ensure the package is valid and meets quality criteria. This often includes running integration tests.
  * **`./mvnw install`**: Performs `package` and then installs the packaged artifact into the local Maven repository (usually `~/.m2/repository`). This makes the artifact available to other local projects.
  * **`./mvnw deploy`**: Performs `install` and then copies the final package to a remote repository for sharing with other developers and projects. (Requires proper repository configuration in `pom.xml`).
  * **`./mvnw site`**: Generates a project website based on the project's POM.
  * **`./mvnw dependency:tree`**: Displays the project's dependency tree, showing all direct and transitive dependencies.
  * **`./mvnw help:effective-pom`**: Displays the effective POM, which is the final POM after all inheritance and profiles have been applied.

##  Most common used maven cmd to do a clean install

```
mvn clean install
```

## Installing Maven on a Self-Hosted Runner

If you're setting up a CI/CD pipeline with a self-hosted runner (e.g., GitHub Actions self-hosted runner, Jenkins agent, GitLab Runner), you'll need to ensure Maven is installed and configured.

Here's a general guide for Linux-based runners (adjust paths and commands for Windows if necessary):

1.  **Install Java Development Kit (JDK):**
    Maven requires a JDK. Install it first if not already present. For Ubuntu/Debian:

    ```bash
    sudo apt update
    sudo apt install openjdk-11-jdk # Or openjdk-17-jdk depending on your project's needs
    ```

    Verify the installation:

    ```bash
    java -version
    javac -version
    ```

2.  **Download Apache Maven:**
    Go to the [Apache Maven Downloads page](https://maven.apache.org/download.cgi) and get the binary tar.gz archive.

    ```bash
    wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz -P /tmp
    ```

    *(Note: Replace `3.9.6` with the latest stable version if available)*

3.  **Extract Maven:**
    Extract the archive to a suitable location, for example, `/opt/maven`.

    ```bash
    sudo mkdir -p /opt/maven
    sudo tar -xvzf /tmp/apache-maven-3.9.6-bin.tar.gz -C /opt/maven
    ```

    You might want to create a symbolic link to make it easier to update later:

    ```bash
    sudo ln -s /opt/maven/apache-maven-3.9.6 /opt/maven/latest
    ```

4.  **Set Environment Variables:**
    You need to set `M2_HOME` (or `MAVEN_HOME`) and add Maven's `bin` directory to your `PATH`.
    Edit your shell's profile file (e.g., `~/.bashrc`, `~/.profile`, or `/etc/profile` for system-wide):

    ```bash
    sudo nano /etc/profile # Or ~/.bashrc for user-specific
    ```

    Add the following lines at the end:

    ```bash
    export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java)))) # Automatically find JAVA_HOME
    export M2_HOME=/opt/maven/latest
    export MAVEN_HOME=/opt/maven/latest
    export PATH=${M2_HOME}/bin:${PATH}
    ```

    Save the file and then source it to apply changes:

    ```bash
    source /etc/profile # Or source ~/.bashrc
    ```

5.  **Verify Maven Installation:**

    ```bash
    mvn -version
    ```

    You should see output similar to this:

    ```
    Apache Maven 3.9.6 (xxxxx; xxxxx)
    Maven home: /opt/maven/latest
    Java version: 11.0.23, vendor: Private Build, runtime: /usr/lib/jvm/java-11-openjdk-amd64
    Default locale: en_US, platform encoding: UTF-8
    OS name: "linux", version: "5.15.0-107-generic", arch: "amd64", family: "unix"
    ```

**Important Considerations for Runners:**

  * **User Permissions:** Ensure the user running the CI/CD agent has appropriate permissions to access the Maven installation directory and build directories.
  * **Maven Wrapper:** Even with a global Maven installation, it's generally recommended to use the Maven Wrapper (`./mvnw`) in CI/CD pipelines. This ensures that the exact Maven version specified in your project's `pom.xml` (via `.mvn/wrapper/maven-wrapper.properties`) is used, leading to more consistent builds. The global installation is mainly for local development and fallbacks.
  * **Caching:** For faster builds on runners, consider caching the Maven local repository (`~/.m2/repository`). Most CI/CD platforms offer caching mechanisms.

## Contributing

[Instructions on how to contribute to your project, e.g., how to open issues, submit pull requests, coding style guidelines, etc.]

## License

See the [**LICENSE**](./LICENSE) file for more details.

-----
