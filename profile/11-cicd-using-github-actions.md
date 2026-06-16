# Chapter 11: Continuous Integration and Continuous Delivery (CI/CD) Using GitHub Actions

## Introduction

In previous chapters, you learned how to write clean code, test it, manage it with Git, document it, and containerize it with Docker. But there is a gap between writing code on your machine and delivering it to users. How do you ensure that every code change is automatically tested? How do you guarantee that the code in your repository always builds successfully? How do you deploy new versions reliably without manual intervention?

This is where **CI/CD** comes in -- a set of practices and tools that automate the process of integrating code changes, testing them, and delivering them to production. In this chapter, you will learn about DevOps culture, understand the principles of CI/CD, and gain hands-on experience with **GitHub Actions**, one of the most popular CI/CD platforms available today.

By the end of this chapter, you will be able to create automated workflows that build, test, and package your Java applications every time you push code to GitHub.

---

## 11.1 What Is DevOps?

### 11.1.1 The Problem Before DevOps

Traditionally, software organizations were split into two separate teams:

- **Development (Dev)**: Developers wrote code, added features, and fixed bugs. Their goal was to ship new features as quickly as possible.
- **Operations (Ops)**: Operations engineers deployed code, managed servers, and ensured system reliability. Their goal was to keep the system stable.

These teams often had conflicting objectives. Developers wanted to push changes frequently, while operations wanted to minimize changes to avoid instability. Code would be "thrown over the wall" from development to operations, leading to deployment failures, blame games, and long release cycles.

### 11.1.2 The DevOps Philosophy

DevOps is a **set of practices, tools, and a cultural philosophy** that automates and integrates the processes between software development and IT operations teams. The term "DevOps" is a combination of "Development" and "Operations," reflecting the goal of unifying these two disciplines into one continuous process.

The DevOps movement began around 2007 when practitioners in both software development and IT operations recognized that the traditional siloed approach was fundamentally broken. Key principles of DevOps include:

- **Collaboration**: Breaking down silos between development and operations teams.
- **Automation**: Automating repetitive tasks like testing, building, and deploying.
- **Continuous improvement**: Constantly measuring and improving processes.
- **Shared responsibility**: Everyone is responsible for the quality and reliability of the software.
- **Fast feedback**: Getting feedback on code changes as quickly as possible.

### 11.1.3 DevOps in Practice

DevOps is not a single tool or technology -- it is a culture supported by tools. The DevOps toolchain typically includes:

| Practice | Tools |
|---|---|
| **Version Control** | Git, GitHub, GitLab |
| **CI/CD** | GitHub Actions, Jenkins, GitLab CI, CircleCI |
| **Containerization** | Docker, Podman |
| **Orchestration** | Kubernetes, Docker Swarm |
| **Infrastructure as Code** | Terraform, Ansible, Pulumi |
| **Monitoring** | Prometheus, Grafana, Datadog |
| **Logging** | ELK Stack, Loki |

CI/CD is at the heart of DevOps, providing the automation pipeline that connects code changes to production deployments.

---

## 11.2 CI/CD Explained

### 11.2.1 What Is CI/CD?

CI/CD stands for **Continuous Integration** and **Continuous Delivery** (or **Continuous Deployment**). Together, they form a pipeline that automates the process of getting code from a developer's machine to production.

CI/CD automates your builds, testing, and deployment so you can ship code changes faster and more reliably. It is a critical component for achieving DevOps success.

### 11.2.2 Continuous Integration (CI)

**Continuous Integration** is the practice of automatically building, testing, and integrating code changes within a shared repository.

Here is how CI works in practice:

1. A developer writes code and pushes it to a shared Git repository (e.g., GitHub).
2. An automated CI system detects the push event.
3. The CI system checks out the code.
4. It compiles/builds the application.
5. It runs automated tests (unit tests, integration tests).
6. It reports the results back to the developer.

If any step fails (compilation error, test failure), the developer is notified immediately and can fix the issue before it affects other team members.

**Benefits of CI:**

- Bugs are caught early, when they are cheapest to fix.
- The codebase is always in a buildable state.
- Integration conflicts are resolved continuously rather than in large, painful merges.
- Developers get fast feedback on their changes.

### 11.2.3 Continuous Delivery (CD)

**Continuous Delivery** extends CI by automatically preparing code changes for release to production. After the CI pipeline passes all tests, the code is automatically deployed to a staging or pre-production environment and made ready for release.

The key distinction is that **a human still needs to approve** the final deployment to production. This is a deliberate choice that allows teams to perform final checks, schedule releases during maintenance windows, or comply with regulatory requirements.

### 11.2.4 Continuous Deployment

**Continuous Deployment** goes one step further than Continuous Delivery. With Continuous Deployment, every code change that passes the automated test suite is **automatically deployed to production** without any human intervention.

### 11.2.5 Comparing CI, Continuous Delivery, and Continuous Deployment

```
                    Continuous Integration
                    |
Developer  --->  Push Code  --->  Build  --->  Test  --->  Integrate
                                                               |
                                          Continuous Delivery  |
                                                               v
                                                    Deploy to Staging
                                                               |
                                                        Human Approval
                                                               |
                                       Continuous Deployment   |
                                                               v
                                                    Deploy to Production
```

| Aspect | Continuous Delivery | Continuous Deployment |
|---|---|---|
| **Automation** | Up to staging | All the way to production |
| **Human approval** | Required before production | Not required |
| **Risk** | Lower (human gate) | Higher (requires mature testing) |
| **Speed** | Fast (with manual gate) | Fastest |
| **Common in** | Regulated industries, enterprises | Mature DevOps organizations |

> **Note**: When people say "CI/CD," the "CD" typically refers to Continuous Delivery, not Continuous Deployment. Most teams start with Continuous Delivery and evolve toward Continuous Deployment as their testing practices mature.

### 11.2.6 Why CI/CD Matters

The business case for CI/CD is compelling. Organizations that have mastered CI/CD report significantly higher deployment frequency and dramatically faster lead times from code commit to production.

The benefits fall into three categories:

1. **Development Velocity**: Developers can commit smaller changes more often, getting ongoing feedback instead of waiting for a single large release. This reduces risk and accelerates iteration.

2. **Stability and Reliability**: Automated, continuous testing ensures that the codebase remains stable and release-ready at any time. Bugs are caught within minutes of being introduced.

3. **Business Growth**: With manual tasks automated, teams can focus their resources on innovation, customer satisfaction, and paying down technical debt rather than managing releases.

---

## 11.3 What Is GitHub Actions?

### 11.3.1 Overview

GitHub Actions is a **CI/CD platform** built directly into GitHub. It allows you to automate your build, test, and deployment pipelines without needing a separate CI/CD server. Because it is integrated with GitHub, it can react to any event that occurs in your repository -- pushes, pull requests, issues, releases, and more.

Key advantages of GitHub Actions:

- **Native GitHub integration**: No external CI/CD service needed. Workflows live in your repository alongside your code.
- **Event-driven**: Workflows can be triggered by any GitHub event (push, pull request, issue creation, schedule, manual dispatch, etc.).
- **Marketplace**: Thousands of pre-built actions are available in the GitHub Marketplace, saving you from reinventing common tasks.
- **Multi-platform**: GitHub provides Linux, Windows, and macOS runners for your workflows.
- **Free tier**: GitHub Actions is free for public repositories and includes a generous free tier for private repositories.
- **Self-hosted runners**: If GitHub's runners do not meet your needs, you can run workflows on your own infrastructure.

### 11.3.2 GitHub Actions Beyond CI/CD

While CI/CD is the primary use case, GitHub Actions is a general-purpose automation platform. You can use it to:

- Automatically label issues and pull requests.
- Generate release notes.
- Synchronize data between systems.
- Send notifications to Slack or email.
- Enforce coding standards and policies.
- Automate documentation generation.

---

## 11.4 Components of GitHub Actions

GitHub Actions has five core components. Understanding these components is essential for writing effective workflows.

### 11.4.1 Workflows

A **workflow** is a configurable automated process that runs one or more jobs. Workflows are defined as YAML files stored in the `.github/workflows/` directory of your repository.

Key characteristics of workflows:

- A repository can have **multiple workflows**, each performing different tasks.
- Workflows are **triggered by events** (push, pull request, schedule, manual trigger, etc.).
- Workflows can **reference other workflows** for reusability.

Example use cases for multiple workflows in a single repository:

- **CI workflow**: Builds and tests code on every push and pull request.
- **CD workflow**: Deploys the application when a release is created.
- **Labeler workflow**: Automatically labels pull requests based on changed files.

### 11.4.2 Events

An **event** is a specific activity in a repository that triggers a workflow run. Events can come from many sources:

| Event Type | Example Events |
|---|---|
| **Code changes** | `push`, `pull_request` |
| **Issues** | `issues` (opened, closed, labeled) |
| **Releases** | `release` (published, created) |
| **Manual** | `workflow_dispatch` |
| **Scheduled** | `schedule` (cron expressions) |
| **API** | `repository_dispatch` |
| **Other** | `fork`, `watch`, `create`, `delete` |

You can also configure events to trigger only for specific branches, paths, or tags:

```yaml
on:
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - 'pom.xml'
  pull_request:
    branches: [main]
```

### 11.4.3 Jobs

A **job** is a set of steps that execute on the same runner (virtual machine). Jobs are the primary organizational unit within a workflow.

Key characteristics:

- Each job runs in a **fresh virtual machine** (or container).
- By default, jobs run **in parallel** with each other.
- You can configure **dependencies** between jobs so they run sequentially.
- Steps within a job are executed **in order** and can share data with each other.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Build
        run: ./mvnw package

  test:
    runs-on: ubuntu-latest
    needs: build  # This job waits for 'build' to complete
    steps:
      - name: Run tests
        run: ./mvnw test

  deploy:
    runs-on: ubuntu-latest
    needs: test  # This job waits for 'test' to complete
    steps:
      - name: Deploy
        run: echo "Deploying..."
```

In this example, `build` runs first, then `test` (after build succeeds), then `deploy` (after test succeeds). If `build` fails, neither `test` nor `deploy` will run.

### 11.4.4 Actions

An **action** is a reusable, pre-built component that performs a common task. Actions save you from writing repetitive code in your workflow files. They are the building blocks of workflows.

Actions can:

- Check out your repository code (`actions/checkout@v4`)
- Set up a specific JDK version (`actions/setup-java@v4`)
- Cache dependencies (`actions/cache@v4`)
- Upload/download build artifacts (`actions/upload-artifact@v4`)
- Send notifications, deploy to cloud providers, and much more

You can find and browse actions in the [GitHub Marketplace](https://github.com/marketplace?type=actions).

You can also write your own custom actions and share them with the community.

### 11.4.5 Runners

A **runner** is a server that executes your workflow jobs. When a workflow is triggered, GitHub provisions a fresh virtual machine to run each job.

GitHub provides hosted runners with the following operating systems:

| Runner | Label | Description |
|---|---|---|
| **Ubuntu Linux** | `ubuntu-latest` | Most commonly used, best for Java projects |
| **Windows** | `windows-latest` | For Windows-specific builds |
| **macOS** | `macos-latest` | For macOS/iOS development |

Each workflow run gets a **fresh, newly-provisioned virtual machine**. This ensures that builds are clean and reproducible -- there is no leftover state from previous runs.

If you need custom hardware, specific software, or want to reduce costs, you can set up **self-hosted runners** on your own infrastructure.

---

## 11.5 YAML Syntax for Workflows

Before writing workflows, let us review the YAML syntax used in GitHub Actions workflow files. YAML (YAML Ain't Markup Language) is a human-readable data serialization format that uses indentation to represent structure.

### 11.5.1 YAML Basics

```yaml
# This is a comment

# Scalar values
name: my-workflow
version: 1.0
enabled: true

# Lists (arrays)
branches:
  - main
  - develop
  - feature/*

# Inline list
on: [push, pull_request]

# Maps (objects/dictionaries)
environment:
  DB_HOST: localhost
  DB_PORT: 5432

# Nested structure
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: First step
        run: echo "Hello"
      - name: Second step
        run: echo "World"
```

### 11.5.2 Key YAML Rules

1. **Indentation matters**: Use spaces (not tabs). Typically 2 spaces per level.
2. **Key-value pairs**: Use `key: value` syntax.
3. **Lists**: Use `- item` syntax (with a dash and space).
4. **Multi-line strings**: Use `|` for literal blocks or `>` for folded blocks:

```yaml
# Literal block (preserves newlines)
run: |
  echo "Line 1"
  echo "Line 2"
  echo "Line 3"

# Folded block (joins lines)
description: >
  This is a long description
  that spans multiple lines
  but will be joined into one.
```

### 11.5.3 GitHub Actions YAML Expressions

GitHub Actions supports expressions using the `${{ }}` syntax:

```yaml
# Access context variables
run-name: ${{ github.actor }} is running the workflow

# Conditional execution
if: ${{ github.ref == 'refs/heads/main' }}

# Access secrets
env:
  API_KEY: ${{ secrets.API_KEY }}

# Access job outputs
needs: build
env:
  VERSION: ${{ needs.build.outputs.version }}
```

---

## 11.6 Creating Your First Workflow

### 11.6.1 The Example from the Slides

Let us walk through the example workflow from the course slides step by step:

**File: `.github/workflows/learn-github-actions.yml`**

```yaml
name: learn-github-actions
run-name: ${{ github.actor }} is learning GitHub Actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm install -g bats
      - run: bats -v
```

### 11.6.2 Line-by-Line Explanation

Let us break down every line:

**`name: learn-github-actions`**

This is the name of the workflow. It appears in the "Actions" tab of your GitHub repository. If omitted, GitHub uses the filename instead.

**`run-name: ${{ github.actor }} is learning GitHub Actions`**

This sets a custom name for each individual workflow run. The `${{ github.actor }}` expression is replaced with the GitHub username of the person who triggered the run. For example, if user "jsmith" pushes code, the run name will be "jsmith is learning GitHub Actions."

**`on: [push]`**

This defines the event trigger. The workflow runs every time someone pushes a commit to any branch in the repository. You can specify multiple events and add filters:

```yaml
# Single event
on: push

# Multiple events
on: [push, pull_request]

# Events with filters
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```

**`jobs:`**

This top-level key groups all the jobs in the workflow. A workflow can have one or more jobs.

**`check-bats-version:`**

This defines a job named `check-bats-version`. The name is used as an identifier and appears in the GitHub Actions UI. You can name jobs anything you want, but the names should be descriptive.

**`runs-on: ubuntu-latest`**

This specifies that the job should run on the latest version of an Ubuntu Linux runner provided by GitHub. The job gets a fresh virtual machine for each run.

**`steps:`**

This key groups the sequential steps within the job. Steps run in order, and each step can either run a shell command or use a pre-built action.

**`- uses: actions/checkout@v4`**

This step uses the `actions/checkout` action (version 4) to clone your repository onto the runner. This is required in almost every workflow because the runner starts with an empty workspace. The `uses` keyword indicates that this step runs a pre-built action.

**`- uses: actions/setup-node@v4` with `node-version: '20'`**

This step uses the `actions/setup-node` action to install Node.js version 20 on the runner. The `with` keyword passes configuration parameters to the action.

**`- run: npm install -g bats`**

The `run` keyword executes a shell command on the runner. This installs the `bats` testing framework globally using npm.

**`- run: bats -v`**

This runs the `bats` command to print its version, verifying that the installation was successful.

### 11.6.3 Workflow File Structure Diagram

```
Workflow (learn-github-actions.yml)
|
+-- Event Trigger: push
|
+-- Job: check-bats-version
    |
    +-- Runner: ubuntu-latest
    |
    +-- Step 1: actions/checkout@v4        (Action)
    +-- Step 2: actions/setup-node@v4      (Action)
    +-- Step 3: npm install -g bats        (Shell command)
    +-- Step 4: bats -v                    (Shell command)
```

---

## 11.7 Practical Java/Maven CI Workflow

Now let us create a real-world CI workflow for a Java project using Maven. This is the type of workflow you will use in your own Java projects.

### 11.7.1 Basic Java CI Workflow

**File: `.github/workflows/java-ci.yml`**

```yaml
name: Java CI with Maven

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    name: Build and Test
    runs-on: ubuntu-latest

    steps:
      # Step 1: Check out the repository code
      - name: Checkout code
        uses: actions/checkout@v4

      # Step 2: Set up JDK 17
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      # Step 3: Build the project
      - name: Build with Maven
        run: mvn clean compile

      # Step 4: Run tests
      - name: Run tests
        run: mvn test

      # Step 5: Package the application
      - name: Package application
        run: mvn package -DskipTests

      # Step 6: Upload the built artifact
      - name: Upload JAR artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-jar
          path: target/*.jar
          retention-days: 7
```

Let us examine each step:

1. **Checkout code**: Clones the repository onto the runner.
2. **Set up JDK 17**: Installs Eclipse Temurin JDK 17 and configures Maven dependency caching. The cache speeds up subsequent builds by reusing downloaded dependencies.
3. **Build with Maven**: Compiles the source code using `mvn clean compile`.
4. **Run tests**: Executes all unit tests using `mvn test`. If any test fails, the workflow fails.
5. **Package application**: Creates the JAR file using `mvn package`. The `-DskipTests` flag skips tests since they already ran in the previous step.
6. **Upload JAR artifact**: Uploads the built JAR file as a workflow artifact. Artifacts can be downloaded from the GitHub Actions UI and used in subsequent jobs or workflows.

### 11.7.2 Multi-JDK Testing Workflow

To ensure your application works across multiple Java versions, use a **matrix strategy**:

```yaml
name: Java CI - Multi Version

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    name: Build with JDK ${{ matrix.java-version }}
    runs-on: ubuntu-latest

    strategy:
      matrix:
        java-version: ['11', '17', '21']

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK ${{ matrix.java-version }}
        uses: actions/setup-java@v4
        with:
          java-version: ${{ matrix.java-version }}
          distribution: 'temurin'
          cache: maven

      - name: Build and test
        run: mvn clean verify
```

The `strategy.matrix` configuration creates three parallel jobs, one for each Java version. This ensures your code is compatible with Java 11, 17, and 21.

### 11.7.3 Complete CI/CD Pipeline with Docker

Here is a more advanced workflow that builds a Java application, runs tests, builds a Docker image, and pushes it to Docker Hub:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # Job 1: Build and Test
  build:
    name: Build and Test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Build and run tests
        run: mvn clean verify

      - name: Upload JAR
        uses: actions/upload-artifact@v4
        with:
          name: app-jar
          path: target/*.jar

  # Job 2: Build and Push Docker Image (only on main branch push)
  docker:
    name: Build Docker Image
    runs-on: ubuntu-latest
    needs: build  # Only runs after build succeeds
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Download JAR artifact
        uses: actions/download-artifact@v4
        with:
          name: app-jar
          path: target/

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:latest
            ${{ secrets.DOCKERHUB_USERNAME }}/myapp:${{ github.sha }}
```

Key concepts in this advanced workflow:

- **`needs: build`**: The `docker` job depends on the `build` job. It only runs after `build` completes successfully.
- **`if` condition**: The Docker job only runs on pushes to the `main` branch, not on pull requests. This prevents pushing Docker images for every PR.
- **Secrets**: Docker Hub credentials are stored as GitHub repository secrets (not in the code). You configure these in your repository's Settings > Secrets and variables > Actions.
- **Artifact passing**: The JAR built in the `build` job is uploaded as an artifact and downloaded in the `docker` job.
- **Docker tags**: The image is tagged with both `latest` and the Git commit SHA for traceability.

---

## 11.8 Viewing Workflow Runs

### 11.8.1 Navigating to the Actions Tab

After pushing your workflow file to GitHub, you can view the results:

1. Navigate to your repository on GitHub.
2. Click the **Actions** tab in the repository navigation bar.
3. In the left sidebar, you will see a list of all workflows defined in your repository.
4. Click on a specific workflow to see its run history.

### 11.8.2 Understanding the Workflow Run View

Each workflow run shows:

- **Status**: Whether the run succeeded (green check), failed (red X), or is in progress (yellow circle).
- **Trigger**: What event triggered the run (push, pull request, schedule).
- **Actor**: Who triggered the run.
- **Duration**: How long the run took.
- **Jobs**: A visualization graph showing the jobs and their dependencies.

### 11.8.3 Viewing Step-by-Step Results

Click on a specific job to see its detailed output:

1. Click the workflow name in the left sidebar.
2. Click a specific run from the list.
3. Click a job name in the visualization graph or the left sidebar.
4. Expand each step to see its console output.

This detailed view is essential for debugging failed workflows. When a step fails, you can see the exact command that failed and its error output.

### 11.8.4 Status Badges

You can add a status badge to your repository's README to show the current build status:

```markdown
![CI](https://github.com/username/repo/actions/workflows/java-ci.yml/badge.svg)
```

This badge automatically updates to reflect the status of the latest workflow run.

---

## 11.9 GitHub Actions Best Practices

### 11.9.1 Security

- **Never hardcode secrets**: Always use GitHub Secrets (`${{ secrets.MY_SECRET }}`).
- **Pin action versions**: Use specific commit SHAs or version tags instead of `@main` for third-party actions.
- **Limit permissions**: Use the `permissions` key to restrict what the workflow can access:

```yaml
permissions:
  contents: read
  packages: write
```

### 11.9.2 Performance

- **Cache dependencies**: Use `actions/cache` or the built-in cache feature of `actions/setup-java` to avoid downloading dependencies on every run:

```yaml
- uses: actions/setup-java@v4
  with:
    java-version: '17'
    distribution: 'temurin'
    cache: maven  # Automatically caches ~/.m2/repository
```

- **Use matrix strategies wisely**: Matrix builds run in parallel, but each job consumes runner minutes.
- **Skip unnecessary builds**: Use path filters to only trigger builds when relevant files change:

```yaml
on:
  push:
    paths:
      - 'src/**'
      - 'pom.xml'
      - '.github/workflows/**'
```

### 11.9.3 Maintainability

- **Name your steps**: Always give descriptive names to steps for easier debugging.
- **Keep workflows focused**: Have separate workflow files for different purposes (CI, CD, linting).
- **Use reusable workflows**: Factor out common patterns into reusable workflows.
- **Document non-obvious decisions**: Add comments in your YAML files explaining why certain configurations exist.

### 11.9.4 Reliability

- **Test on the same OS you deploy to**: If you deploy to Linux, test on `ubuntu-latest`.
- **Use `mvn verify` instead of `mvn test`**: The `verify` phase runs integration tests in addition to unit tests.
- **Fail fast**: In matrix builds, use `fail-fast: true` (the default) to cancel remaining jobs as soon as one fails.

---

## 11.10 Common Workflow Patterns for Java Projects

### 11.10.1 Pull Request Checks

```yaml
name: PR Checks

on:
  pull_request:
    branches: [main, develop]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Check code compiles
        run: mvn clean compile

      - name: Run unit tests
        run: mvn test

      - name: Check code style
        run: mvn checkstyle:check
```

### 11.10.2 Scheduled Builds

```yaml
name: Nightly Build

on:
  schedule:
    # Run every day at 2:00 AM UTC
    - cron: '0 2 * * *'

jobs:
  nightly:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Full build and test
        run: mvn clean verify
```

### 11.10.3 Manual Trigger with Parameters

```yaml
name: Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
      version:
        description: 'Version to deploy'
        required: true
        type: string

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ${{ inputs.environment }}
        run: echo "Deploying version ${{ inputs.version }} to ${{ inputs.environment }}"
```

---

## 11.11 Summary and Key Takeaways

- **DevOps** is a cultural philosophy and set of practices that unify software development and IT operations, emphasizing automation, collaboration, and continuous improvement.

- **CI/CD** automates the process of building, testing, and deploying code:
  - **Continuous Integration (CI)**: Automatically builds, tests, and integrates code changes in a shared repository.
  - **Continuous Delivery (CD)**: Automatically prepares code for release, with human approval required for production deployment.
  - **Continuous Deployment**: Automatically deploys code to production without human intervention.

- **GitHub Actions** is a CI/CD platform built into GitHub that automates workflows based on repository events.

- GitHub Actions has five core components:
  - **Workflows**: YAML files in `.github/workflows/` that define automated processes.
  - **Events**: Triggers that start workflows (push, pull_request, schedule, etc.).
  - **Jobs**: Groups of steps that run on the same runner; jobs run in parallel by default.
  - **Actions**: Reusable components from the GitHub Marketplace that perform common tasks.
  - **Runners**: Virtual machines (Ubuntu, Windows, macOS) that execute workflow jobs.

- A typical Java CI workflow includes: checking out code, setting up the JDK, building with Maven, running tests, and uploading artifacts.

- **Best practices** include caching dependencies, using secrets for credentials, pinning action versions, naming steps descriptively, and using path filters to avoid unnecessary builds.

---

## 11.12 Homework

### Assignment: Set Up CI for a Java Project

Create a GitHub Actions workflow for a Java project that implements Continuous Integration. Complete the following tasks:

#### Option A: Continuous Integration (Required)

1. **Create a Java project** (or use an existing one) with:
   - At least one class with meaningful logic.
   - At least two unit tests using JUnit.
   - A `pom.xml` file configured for Maven.

2. **Push the project to a GitHub repository.**

3. **Create a workflow file** at `.github/workflows/ci.yml` that:
   - Triggers on every push to the `main` branch and on pull requests.
   - Runs on `ubuntu-latest`.
   - Checks out the code.
   - Sets up JDK 17 (Temurin distribution) with Maven caching.
   - Builds the project with `mvn clean compile`.
   - Runs all tests with `mvn test`.
   - Packages the application with `mvn package -DskipTests`.

4. **Push the workflow file** to GitHub and verify that it runs successfully in the Actions tab.

5. **Intentionally break a test** (make a test fail), push the change, and observe the workflow failure in the Actions tab. Then fix the test and push again.

#### Option B: Continuous Delivery (Bonus)

Extend your CI workflow to include a Continuous Delivery step:

1. Add a second job that builds a Docker image from the application.
2. The Docker job should only run after the build/test job succeeds.
3. The Docker job should only run on pushes to `main` (not on pull requests).
4. Push the Docker image to Docker Hub using secrets for credentials.

### Verification Checklist

- [ ] GitHub repository contains a Java project with tests.
- [ ] `.github/workflows/ci.yml` exists and is properly formatted.
- [ ] The workflow triggers automatically on push.
- [ ] The workflow builds the project successfully (green check mark).
- [ ] Tests run as part of the workflow.
- [ ] A failing test causes the workflow to fail (red X).
- [ ] Fixing the test causes the workflow to pass again.
- [ ] (Bonus) A Docker image is built and pushed on successful `main` branch pushes.

### Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [actions/setup-java](https://github.com/actions/setup-java) -- Action for setting up JDK
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) -- Browse pre-built actions
- [Workflow syntax reference](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions) -- Complete YAML syntax
