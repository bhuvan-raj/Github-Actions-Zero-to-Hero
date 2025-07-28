# GitHub Actions Matrix Builds for Java & Maven Projects

## Introduction to Matrix Projects in CI/CD

In the world of Continuous Integration/Continuous Deployment (CI/CD), building and testing your code across various environments is crucial for ensuring reliability and compatibility. A **Matrix Project** (or Matrix Build) in GitHub Actions is a powerful feature that allows you to run the same job multiple times, in parallel, with different configurations.

Think of it as a "for loop" for your CI/CD jobs. Instead of creating separate, redundant jobs for each configuration you want to test (e.g., different operating systems, different Java versions, different Maven versions), you define a "matrix" of variables. GitHub Actions then automatically generates and executes a separate job for every combination of these variables.

### Why use a Matrix Project?

  * **Comprehensive Testing:** Ensures your application works across various environments, such as different operating systems (Linux, Windows, macOS), programming language versions (e.g., Java 8, 11, 17, 21), and even different tool versions (e.g., Maven 3.8.x, 3.9.x).
  * **Efficiency:** Runs jobs in parallel, significantly reducing the total time it takes to complete your CI/CD pipeline, especially for projects requiring extensive compatibility testing.
  * **Reduced Redundancy (DRY - Don't Repeat Yourself):** You write your job definition once, and the matrix handles the variations. This makes your workflow files cleaner, more maintainable, and less prone to errors.
  * **Faster Feedback:** Catches compatibility issues early in the development cycle, preventing bugs from reaching production.

### Common Use Cases for Matrix Builds with Java & Maven

  * **Cross-platform testing:** Testing your Java application on `ubuntu-latest`, `windows-latest`, and `macos-latest`.
  * **Multiple JDK versions:** Running your Maven build and tests with `java-version: [8, 11, 17, 21]`.
  * **Maven version compatibility:** Ensuring your project builds correctly with `maven-version: [3.8.x, 3.9.x]`.
  * **Database compatibility:** (More advanced) Setting up different database versions as service containers within the matrix to test against.
  * **Maven profiles:** Running specific Maven profiles (e.g., for different environments or test types) within different matrix combinations.

-----

## Implementing a Matrix Project in GitHub Actions for Java & Maven

You implement a matrix project using the `strategy.matrix` keyword within a job in your GitHub Actions workflow YAML file (e.g., `.github/workflows/build.yml`).

Here's a comprehensive example for a Java Maven project:

```yaml
name: Java Maven Matrix Build

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-test:
    # The runner OS will be dynamically set by the matrix
    runs-on: ${{ matrix.os }}
    
    strategy:
      matrix:
        # Define a list of Operating Systems to run on
        os: [ubuntu-latest, windows-latest, macos-latest]
        
        # Define a list of Java Development Kit (JDK) versions to test
        java-version: [11, 17, 21] # Common LTS versions
        
        # Optional: You can also include Maven versions if you need to test compatibility
        # maven-version: [3.8.8, 3.9.6]

        # Use 'include' to add specific combinations or properties
        include:
          # Example: Run a specific set of "legacy" tests only on Java 8 on Ubuntu
          - os: ubuntu-latest
            java-version: 8
            test-profile: legacy-tests

          # Example: Ensure Java 21 always uses a specific Maven version if needed
          # - java-version: 21
          #   maven-version: 3.9.6

        # Use 'exclude' to remove specific combinations that are not needed or known to fail
        exclude:
          # Example: Don't run Java 11 tests on Windows (e.g., if you dropped support)
          - os: windows-latest
            java-version: 11
          
          # Example: If Java 21 on macOS has known issues with your project currently
          - os: macos-latest
            java-version: 21

      # Optional: Control behavior for failing jobs in the matrix
      # Default: true. Cancels all in-progress/queued jobs if one matrix job fails.
      # Set to false to allow all jobs to complete regardless of individual failures.
      fail-fast: true 
      
      # Optional: Limits the number of parallel jobs that can run from the matrix.
      # Useful for controlling resource usage on your runners.
      max-parallel: 6 

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK ${{ matrix.java-version }}
        uses: actions/setup-java@v4
        with:
          java-version: ${{ matrix.java-version }}
          distribution: 'temurin' # Recommended: 'temurin' (Adoptium OpenJDK), 'adopt', 'zulu', 'microsoft', etc.
          # Optional: Specify Maven version if you added it to your matrix
          # maven-version: ${{ matrix.maven-version }}
          cache: maven # Enable caching for Maven dependencies to speed up builds

      - name: Build with Maven
        run: mvn -B package --file pom.xml # -B for batch mode (non-interactive)
        # If you have specific Maven profiles (e.g., from 'include' matrix option):
        # run: mvn -B package --file pom.xml -P ${{ matrix.test-profile || '' }}

      - name: Run tests (often included in 'mvn package')
        # If your 'mvn package' does not run tests, or you have separate test phases:
        # run: mvn -B test --file pom.xml
        # If you're using a specific test profile from the matrix:
        # if: ${{ matrix.test-profile }}
        # run: mvn -B test --file pom.xml -P ${{ matrix.test-profile }}

      - name: Upload JaCoCo Coverage Report (Optional)
        if: success() # Only upload if the build/test was successful
        # Adjust path to your JaCoCo report as per your project's pom.xml configuration
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report-${{ matrix.os }}-java${{ matrix.java-version }}
          path: target/site/jacoco/jacoco.xml

      - name: Upload JAR/WAR artifact (Optional)
        if: success()
        uses: actions/upload-artifact@v4
        with:
          name: my-app-${{ matrix.os }}-java${{ matrix.java-version }}
          path: target/*.jar # Adjust to your actual artifact path, e.g., target/*.jar or target/*.war
```

### Explanation of the Java-Maven Matrix Example:

1.  **`name: Java Maven Matrix Build`**: The name of your GitHub Actions workflow.

2.  **`on: [push, pull_request]`**: Triggers the workflow on `push` and `pull_request` events to the `main` branch.

3.  **`jobs:`**: Defines the jobs in your workflow.

4.  **`build-and-test:`**: The name of your job.

5.  **`runs-on: ${{ matrix.os }}`**: This is critical for matrix builds. Instead of a fixed runner (e.g., `ubuntu-latest`), we use an expression `matrix.os`. GitHub Actions will substitute this with each value defined in the `os` array of the `matrix`.

6.  **`strategy:`**: This block defines the matrix configuration for the `build-and-test` job.

7.  **`matrix:`**:

      * **`os: [ubuntu-latest, windows-latest, macos-latest]`**: This defines a variable named `os` and a list of its possible values. Each value represents a different operating system runner provided by GitHub.
      * **`java-version: [11, 17, 21]`**: This defines another variable named `java-version` and its possible values, representing different JDK versions.

    GitHub Actions will then create a separate job for every *combination* of these values. For example, with `os` having 3 values and `java-version` having 3 values, GitHub Actions will create $3 \\times 3 = 9$ jobs initially:

      * `os: ubuntu-latest`, `java-version: 11`
      * `os: ubuntu-latest`, `java-version: 17`
      * `os: ubuntu-latest`, `java-version: 21`
      * `os: windows-latest`, `java-version: 11`
      * ...and so on, for all combinations.

8.  **`steps:`**: These are the actions that will be executed for *each* of the generated matrix jobs.

      * **`uses: actions/setup-java@v4`**: This action is essential for setting up the Java environment.

          * **`with.java-version: ${{ matrix.java-version }}`**: Dynamically sets the JDK version for each matrix job.
          * **`with.distribution: 'temurin'`**: Specifies the JDK distribution (e.g., Temurin OpenJDK from Adoptium).
          * **`with.cache: maven`**: A crucial performance optimization. This caches your Maven local repository (`~/.m2/repository`) between workflow runs, significantly speeding up dependency downloads for subsequent builds.

      * **`run: mvn -B package --file pom.xml`**: Executes the Maven build command.

          * `-B` (batch mode) is recommended for CI environments as it ensures non-interactive execution.
          * `package` goal typically compiles code, runs tests, and packages the application (e.g., into a JAR or WAR).
          * `--file pom.xml` explicitly specifies the `pom.xml` file.

      * **`actions/upload-artifact@v4`**: (Optional) Used to upload generated artifacts (like JaCoCo reports or your application JAR/WAR) from each matrix job. This is useful for inspection or for downstream jobs. Notice how `name` includes matrix variables to uniquely identify artifacts.

### Advanced Matrix Options

  * **`include`**:
    Allows you to add new configurations or extend existing ones that wouldn't be generated by the default Cartesian product of your matrix variables. This is perfect for unique testing scenarios (e.g., specific Java 8 tests on one OS).

    ```yaml
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        java-version: [11, 17]
        include:
          - os: macos-latest # Add a macOS job
            java-version: 17 # Specifically for Java 17 on macOS
          - os: ubuntu-latest
            java-version: 8 # Add an older Java version only for Ubuntu
            experimental-feature: true # Add an extra variable for this specific combination
    ```

  * **`exclude`**:
    Removes specific combinations from the matrix that are known to be incompatible, unnecessary, or for which you don't need test coverage.

    ```yaml
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        java-version: [11, 17, 21]
        exclude:
          - os: windows-latest
            java-version: 11 # Don't run Java 11 on Windows
          - os: macos-latest
            java-version: 21 # Don't run Java 21 on macOS for now
    ```

  * **`fail-fast`**:
    (Default: `true`) If set to `true`, all in-progress and queued jobs in the matrix are canceled if any job in the matrix fails. Setting it to `false` allows all jobs to attempt to complete, regardless of individual failures.

    ```yaml
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        java-version: [17, 21]
      fail-fast: false # All matrix jobs will run to completion, even if some fail
    ```

  * **`max-parallel`**:
    (Default: unlimited) Specifies the maximum number of jobs that can run concurrently from the matrix. This is useful for controlling resource usage, especially if you have limitations on your GitHub-hosted or self-hosted runners.

    ```yaml
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        java-version: [17, 21]
      max-parallel: 2 # Only 2 matrix jobs will run at the same time
    ```

  * **Dynamic Matrix Generation (Advanced)**:
    You can generate the matrix dynamically from the output of a previous job or step. This is useful when the configurations are not known beforehand or depend on external factors (e.g., a list of services to test determined by a script).

    ```yaml
    jobs:
      generate_matrix:
        runs-on: ubuntu-latest
        outputs:
          matrix: ${{ steps.set_matrix.outputs.matrix }}
        steps:
          - id: set_matrix
            run: |
              # In a real scenario, this JSON would be generated by a script
              # For example, listing different Spring Boot versions or database types
              echo "matrix={\"java-version\":[\"11\",\"17\"],\"os\":[\"ubuntu-latest\"]}" >> $GITHUB_OUTPUT

      run_tests:
        needs: generate_matrix # This job depends on the output of generate_matrix
        runs-on: ${{ matrix.os }}
        strategy:
          # Use fromJson to parse the JSON output from the previous job
          matrix: ${{ fromJson(needs.generate_matrix.outputs.matrix) }} 
        steps:
          - name: Checkout code
            uses: actions/checkout@v4
          - name: Set up Java ${{ matrix.java-version }}
            uses: actions/setup-java@v4
            with:
              java-version: ${{ matrix.java-version }}
              distribution: 'temurin'
          - name: Build and test
            run: mvn -B package
    ```

-----

## Best Practices for Java-Maven Matrix Builds

  * **Be Specific with JDK Distribution:** Always specify `distribution` in `actions/setup-java` (e.g., `temurin`, `zulu`). This ensures consistent builds.
  * **Leverage Caching:** Use `cache: maven` in `setup-java` to dramatically speed up your builds by reusing downloaded dependencies.
  * **Artifact Naming:** When uploading artifacts, include matrix variables in their names to easily distinguish them (e.g., `coverage-report-ubuntu-java17.xml`).
  * **Multi-Module Projects:** For multi-module Maven projects, consider `mvn -B install` instead of `mvn -B package` to ensure sub-modules are installed into the local Maven repository, making them available for other modules within the same build.
  * **Selective Static Analysis:** Running static analysis tools (SonarQube, Checkstyle, PMD, SpotBugs) on *every* matrix combination can be redundant and time-consuming. Consider running these tools only on a single, primary configuration (e.g., `ubuntu-latest` with the latest LTS Java version) using `if` conditions:
    ```yaml
    - name: Run SonarQube Analysis
      if: matrix.os == 'ubuntu-latest' && matrix.java-version == 21
      run: mvn -B verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar
    ```
  * **Review `fail-fast`:** Decide if you need all matrix jobs to complete (set `fail-fast: false`) or if you want to stop early on the first failure (default `fail-fast: true`). This depends on your testing strategy.
  * **Monitor Parallelism:** Use `max-parallel` if you have limits on your runners or want to avoid overwhelming them, especially when dealing with a large number of matrix combinations.

