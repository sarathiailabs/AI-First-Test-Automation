# Day 16: CI/CD + Docker

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| CI/CD Fundamentals | 10 mins |
| GitHub Actions | 10 mins |
| Workflow Design | 10 mins |
| Pipeline Creation | 10 mins |
| Scheduled Runs | 5 mins |
| Parallel Execution (CI/CD) | 5 mins |
| Test Reporting (CI/CD) | 5 mins |
| Environment Variables (CI/CD) | 5 mins |
| Secrets Management | 10 mins |
| Docker Fundamentals | 10 mins |
| Images | 5 mins |
| Containers | 5 mins |
| Dockerfile | 15 mins |
| Docker Compose | 10 mins |
| Running Playwright in Containers | 10 mins |
| CI/CD Integration (Docker) | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Explain CI/CD concepts and design GitHub Actions workflows.
* Construct automated yaml pipelines triggered by pushes, pull requests, and cron schedules.
* Configure matrix parallel runs and publish test report artifacts in pipelines.
* Use GitHub Secrets to manage sensitive API tokens and keys securely.
* Explain Docker virtualization and differentiate between Images and Containers.
* Write a custom `Dockerfile` to package your Playwright automation framework.
* Use Docker Compose to execute multi-container test runs.
* Integrate Docker execution steps into your GitHub Actions pipeline.

---

## CI/CD Fundamentals

### Definition
**CI/CD (Continuous Integration / Continuous Deployment)** is a DevOps practice that automates the building, testing, and deployment of code changes, ensuring that software changes are delivered frequently and reliably. *(CI/CD ek process hai jo code changes ko check-in karte hi automatic build, test aur server par deploy kar deta hai).*

### Key Concepts
* **Continuous Integration (CI):** Developers merge their code changes frequently. The system automatically builds the application and runs tests to detect bugs early.
* **Continuous Deployment (CD):** Once the build passes all automated checks (including automation test runs), the changes are deployed to Staging or Production servers automatically.

### Visual Explanation
**The Water Filtration Analogy:**
Imagine raw river water (developer's raw code changes) passing through a series of filter layers (automated checks):
1. **Filter 1 (Compilation):** Does the code compile without syntax errors?
2. **Filter 2 (Unit Tests):** Do individual code functions work?
3. **Filter 3 (Automation Suite):** Do UI and API workflows still pass in Playwright?
4. **Output (Deployment):** Pure drinking water (verified production-ready code) reaches consumers.
If any filter fails, the flow stops immediately, alerting the team.

### Topic Summary
CI/CD automates integration testing and delivery pipelines, catching regression bugs early before code reaches production environments.

---

## GitHub Actions

### Definition
**GitHub Actions** is a continuous integration and continuous delivery (CI/CD) platform built directly into GitHub that automates build, test, and deployment pipelines via YAML files. *(GitHub Actions GitHub ka built-in tool hai jo Git events (push/pull request) par actions workflows ko run karta hai).*

### Key Concepts
* **Workflow:** An automated process defined in a `.github/workflows/` directory as a YAML file.
* **Events:** Triggers that kick off a workflow (e.g. `push`, `pull_request`, `schedule`).
* **Jobs:** A set of steps that execute on a fresh virtual machine runner (e.g., Ubuntu).
* **Steps:** Individual tasks that run commands or actions (e.g., checking out code, running tests).

### Example
#### Workflow Trigger configuration (YAML)
```yaml
# Define trigger events
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
```

### Real World Usage
When a developer raises a Pull Request (PR) in a GitHub repository, GitHub Actions automatically spins up a machine, runs the Playwright regression test suite, and blocks the PR merge if any test fails.

### Topic Summary
GitHub Actions uses YAML configurations to define event-driven automation jobs that run on cloud-hosted virtual machines.

---

## Workflow Design

### Definition
**Workflow Design** is the sequence planning of pipeline stages, dependencies, and conditions that coordinate how code changes are validated (e.g. check out code $\rightarrow$ install node $\rightarrow$ run tests). *(Workflow design pipeline ke step-by-step actions aur dynamic rules ko define karne ka system hai).*

### Key Concepts
* **Sequence Flow:** Running prerequisite setups (like installing dependencies) before executing tests.
* **Action Reuse:** Importing verified actions from the GitHub marketplace (like `actions/checkout` or `actions/setup-node`).

### Visual Explanation
**The Assembly Line Factory Analogy:**
An automobile manufacturing factory has a strict sequence of steps:
```text
  [ Step 1: Chassis placement ] ──► [ Step 2: Install Engine ] ──► [ Step 3: Paint Body ]
```
You cannot paint the body before placing the chassis. Similarly, workflow design ensures we fetch code and install node *before* running Playwright commands.

### Topic Summary
Workflow design structures pipeline steps logically, mapping out clean execution paths.

---

## Pipeline Creation

### Definition
**Pipeline Creation** is the practical implementation of writing a `.yml` workflow script inside your repository to define the machine environment, project setup steps, and runner instructions to execute your Playwright tests automatically. *(Pipeline creation YAML file me computer configuration aur execution commands likhne ka step hai).*

### Key Concepts
* **Workflow file path:** Must reside in `.github/workflows/playwright.yml`.
* **Runner Environment:** Typically `runs-on: ubuntu-latest`.
* **Playwright Dependencies:** We run `npx playwright install --with-deps` to install browser binaries on the virtual machine runner.

### Example
#### Pipeline Script (`.github/workflows/playwright.yml`)
```yaml
name: Playwright Tests Pipeline
on:
  push:
    branches: [ main ]
jobs:
  test-execution:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository code
      uses: actions/checkout@v4
      
    - name: Install NodeJS environment
      uses: actions/setup-node@v4
      with:
        node-version: 18
        
    - name: Install Project Dependencies
      run: npm ci
      
    - name: Install Playwright Browsers
      run: npx playwright install --with-deps
      
    - name: Execute Playwright Automation Tests
      run: npx playwright test
```

### Common Mistakes
* **Incorrect directory path:** Saving the YAML file in the wrong folder (e.g. `.github/playwright.yml` or `workflows/playwright.yml`). GitHub will ignore the file completely unless it is saved in `.github/workflows/`.

### Topic Summary
Playwright pipelines checkout code, install node, resolve dependencies, fetch browser binaries, and trigger test execution.

---

## Scheduled Runs

### Definition
**Scheduled Runs** are automated pipeline executions triggered at specific times or intervals using Cron expressions, independent of user code commits. *(Scheduled runs pipeline ko schedule parameters (Cron) par automatically trigger karne ka setup hai, jaise har raat ko tests chalana).*

### Key Concepts
* **Cron Syntax:** A string configuration of 5 fields: `minute hour day-of-month month day-of-week`.
* **Nightly Regression:** Running heavy, slow regression suites during off-peak hours (e.g., at midnight) to check code health.

### Example
#### Code
```yaml
on:
  schedule:
    # Trigger execution every night at midnight UTC (12:00 AM)
    - cron: '0 0 * * *'
```

### Topic Summary
Scheduled runs automate recurring pipeline execution scopes (like nightly tests) using standard cron expressions.

---

## Parallel Execution (CI/CD)

### Definition
**Parallel Execution in CI/CD** is the pipeline optimization setup that splits the test suite across multiple separate virtual machine runners (called Shards) running concurrently in the cloud, cutting total execution times. *(CI/CD Parallel execution tests ko multiple remote cloud virtual machines me divide karke ek sath run karta hai).*

### Key Concepts
* **Sharding:** Playwright splits test files into chunks using `--shard=X/Y` parameter limits.
* **Matrix Strategy:** GitHub Actions configures a matrix block to spin up multiple Ubuntu runners concurrently.

### Example
#### Code
```yaml
jobs:
  test-shards:
    strategy:
      fail-fast: false
      matrix:
        # Spin up 3 Ubuntu runners concurrently in the cloud
        shardIndex: [1, 2, 3]
        shardTotal: [3]
    runs-on: ubuntu-latest
    steps:
    - name: Checkout and Setup...
      uses: actions/checkout@v4
    - name: Run Sharded Tests
      run: npx playwright test --shard=${{ matrix.shardIndex }}/${{ matrix.shardTotal }}
```

### Topic Summary
Pipeline parallel sharding scales execution speeds by launching matrix matrices of concurrent runners in the cloud.

---

## Test Reporting (CI/CD)

### Definition
**Test Reporting in CI/CD** is the process of uploading and storing the test execution outputs (such as HTML reports, screenshots, videos, and trace logs) as build artifacts on GitHub so they can be downloaded and reviewed after the pipeline completes. *(Test reporting pipeline ke final status reports aur failure zip files ko GitHub artifacts dashboard par upload karke save karne ka system hai).*

### Key Concepts
* **Artifact Upload:** Using the marketplace action `actions/upload-artifact` to archive test logs.
* **Conditional Actions:** Configuring the upload step to run even if the test execution fails (`if: always()`).

### Example
#### Code
```yaml
    - name: Execute Playwright Automation Tests
      run: npx playwright test
      
    - name: Upload HTML report folder
      uses: actions/upload-artifact@v4
      if: always() # Ensures reports are archived even if tests failed
      with:
        name: playwright-report-artifact
        path: playwright-report/
        retention-days: 30
```

### Topic Summary
Test reporting uploads execution logs to build storage containers using conditional post-run upload commands.

---

## Environment Variables (CI/CD)

### Definition
**Environment Variables in CI/CD** are runtime configuration parameters passed from the pipeline execution context down to the test execution script (e.g. telling the script to point to Staging URL instead of Production). *(Pipeline configuration se test script ko targets swap parameters process.env me pass karna).*

### Key Concepts
* **YAML env blocks:** Configured at the workflow or job level using the `env` keyword.

### Example
#### Code
```yaml
    - name: Execute Playwright Automation Tests
      env:
        # Inject target environment host URL to NodeJS process.env
        TEST_ENV: staging
        TEST_API_URL: https://staging.vjti.edu
      run: npx playwright test
```

### Topic Summary
Environment configurations pass host details dynamically to test run processes at the step level.

---

## Secrets Management

### Definition
**Secrets Management** is the security practice of storing sensitive credentials (like passwords, API keys, database connection strings) in encrypted GitHub Repository Secrets, and passing them to pipelines securely at runtime without exposing them in the YAML code. *(Secrets management password aur access keys ko encrypted settings me secure rakhne ka system hai jo YAML script me variables ($) ke roop me fetch hota hai).*

### Key Concepts
* **Secrets Storage:** Saved in GitHub under `Settings -> Secrets and variables -> Actions`.
* **Dynamic Resolution:** Referenced in YAML files using the `${{ secrets.SECRET_NAME }}` syntax.

### Example
#### Code
```yaml
    - name: Execute Secure Login Test
      env:
        # Access secure credentials dynamically from encrypted GitHub Vault
        PORTAL_ADMIN_PASS: ${{ secrets.VJTI_ADMIN_PASSWORD }}
      run: npx playwright test
```

### Common Mistakes
* **Hardcoding API Keys in YAML:** Writing plain-text keys in the YAML file. Anyone who has repository access can read your secrets, which is a major security risk. Use GitHub Secrets instead.

### Topic Summary
Secrets management secures passwords by loading them dynamically from encrypted GitHub repositories at runtime.

---

## Docker Fundamentals

### Definition
**Docker** is an open-source platform that uses OS-level virtualization to deliver software in packages called containers, ensuring that applications run identically in any environment. *(Docker ek system container platform hai jo application aur uski saari requirements ko ek package me wrap kar deta hai taaki wo har machine par same run kare).*

### Key Concepts
* **The "It works on my machine" Problem:** A test runs fine on your local laptop, but fails on a teammate's machine due to different NodeJS or Chrome versions. Docker solves this by packing the exact OS, node, and browser binaries into a standard package.
* **Virtual Machines vs Containers:** Virtual machines carry a complete guest OS, making them slow and heavy (Gigabytes). Containers share the host OS kernel, making them lightweight, fast, and small (Megabytes).

### Visual Explanation
**The Shipping Container Analogy:**
In the old days, shipping cargo on ships was a nightmare. Cars, grains, piano boxes, and oil barrels were loaded loose. They rolled around, broke, and loading them took days.
The shipping industry solved this by introducing standard **Shipping Containers**:
* The container has a standard size.
* The ship does not care what is inside (it could be books or cars).
* The crane loads all containers using the same mechanism.
Docker containers work exactly like shipping containers. They package your code and dependencies, and run identically on any platform.

### Topic Summary
Docker containers package configurations, operating systems, and browser dependencies into identical running environments.

---

## Images

### Definition
A **Docker Image** is a read-only, static blueprint template containing instructions for creating a running Docker container. *(Docker Image ek read-only template hai jisme operating system, node files, aur dependencies load hote hain container run karne ke liye).*

### Key Concepts
* **Layered File System:** Images are built in layers. If you change a file, only the affected layer is rebuilt, saving compile time.
* **Base Images:** Images are built on top of parent images (e.g. starting with a clean Ubuntu base image or Playwright base image).

### Example
#### Image Reference
`mcr.microsoft.com/playwright:v1.45.0-jammy` (Official Microsoft Playwright base image containing Ubuntu Jammy, Node, and browser binaries).

### Topic Summary
Images are the static blueprint builds that serve as templates for launching running containers.

---

## Containers

### Definition
A **Docker Container** is a runnable, isolated instance of a Docker image that executes applications inside an isolated OS process. *(Docker Container image blueprint ka active running instance hai jahan code execute hota hai).*

### Key Concepts
* **Active Execution:** If an Image is a class definition, a Container is an instantiated object of that class.
* **Isolation:** A container cannot view or modify processes or files in the host system unless explicitly configured.

### Visual Explanation
```text
  [ Docker Image ] (Blueprint / Class) ──► Instantiate ──► [ Docker Container ] (Instance / Object)
```

### Topic Summary
Containers represent isolated running system contexts generated dynamically from static images.

---

## Dockerfile

### Definition
A **Dockerfile** is a text script containing a sequential list of CLI commands that Docker executes to automatically assemble a custom Docker Image. *(Dockerfile ek recipe manual hai jisme step-by-step commands hote hain custom automation image build karne ke liye).*

### Key Concepts
* **FROM:** Defines the base parent image.
* **WORKDIR:** Sets the working directory inside the container.
* **COPY:** Copies files from your local directory to the container.
* **RUN:** Executes terminal commands during the build phase (e.g. `npm install`).
* **CMD:** The default command to run when the container starts.

### Example
#### Script (`Dockerfile`)
```dockerfile
# Step 1: Use official Playwright base image
FROM mcr.microsoft.com/playwright:v1.40.0-jammy

# Step 2: Set working directory inside container
WORKDIR /app

# Step 3: Copy package files first to optimize cache layers
COPY package*.json ./

# Step 4: Install NPM dependencies
RUN npm ci

# Step 5: Copy framework source code files
COPY . .

# Step 6: Define execution trigger command
CMD ["npx", "playwright", "test"]
```

### Common Mistakes
* **Copying everything before installing dependencies:** Placing `COPY . .` before `RUN npm ci`. If you modify any test file, Docker will invalidate its build cache for all subsequent steps, forcing a slow `npm install` on every rebuild. Always copy package files and run `npm ci` *first*.

### Topic Summary
Dockerfile instructions build custom images by setting work directories, fetching NPM packages, copying source files, and defining default run commands.

---

## Docker Compose

### Definition
**Docker Compose** is a tool for defining and running multi-container Docker applications using a single YAML configuration file. *(Docker Compose multiple containers ko manage karne ka tool hai jo YAML file configurations ke through chalta hai).*

### Key Concepts
* **Service Definitions:** Configure your app container, database container, and testing container to launch together.
* **Orchestration:** Run `docker-compose up` to build and launch all defined services with one command.

### Example
#### Configuration (`docker-compose.yml`)
```yaml
version: '3.8'

services:
  # Define test framework service
  playwright-tests:
    build: . # Build using the local Dockerfile
    volumes:
      # Map local test-results folder to capture reports from the container
      - ./test-results:/app/test-results
```

### Topic Summary
Docker Compose coordinates multi-container configurations using YAML service definitions.

---

## Running Playwright in Containers

### Definition
**Running Playwright in Containers** is the execution of automation scripts inside a Docker container using pre-configured browser dependencies and display servers (like Xvfb framebuffers) to run tests headless. *(Running Playwright in containers ka matlab hai system environment variables aur mapping folders configurations set karke test container me build run karna).*

### Key Concepts
* **Volume Mapping (Mounting):** Linking folders on your host machine to folders inside the container. Without this, HTML reports generated inside the container will be deleted when the container stops.
* **Headless Runs:** Playwright runs headless inside Docker since containers lack physical screens.

### Example
#### CLI Execution Commands
```powershell
# 1. Build Custom Docker Image
docker build -t vjti-playwright-image .

# 2. Run container and mount test-results directory to fetch HTML report outputs
docker run --rm -v ${PWD}/test-results:/app/test-results vjti-playwright-image
```

### Topic Summary
Containerized runs mount report volumes to capture logs, running tests headless inside verified base images.

---

## CI/CD Integration (Docker)

### Definition
**CI/CD Integration of Docker** is the practice of running your containerized automation tests inside your CI/CD pipeline virtual machines, ensuring that tests run in the exact same environment locally and on remote runners. *(Pipeline VM machine me Docker commands run karke tests execute karna).*

### Key Concepts
* **Consistent Runners:** Instead of installing node, browsers, and system libraries on the GitHub Actions runner, the pipeline just pulls your Docker image and runs it, preventing pipeline flakiness.

### Example
#### Script Workflow (`.github/workflows/docker-playwright.yml`)
```yaml
name: Docker Playwright Pipeline
on: [push]
jobs:
  container-tests:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Code
      uses: actions/checkout@v4
      
    - name: Execute Playwright inside Docker container
      run: |
        docker build -t test-container .
        docker run --rm test-container
```

### Topic Summary
Docker pipeline integrations launch container instances directly inside virtual environments to ensure environment consistency.

---

## Session Summary

### Key Takeaways
1. **Automation Pipelines:** GitHub Actions schedules jobs using YAML files triggered on push, pull requests, or cron schedules.
2. **Resource Security:** Encrypted secrets store sensitive API credentials, protecting them from git history.
3. **Report Artifacts:** HTML files and failure screenshots are archived as pipeline build artifacts.
4. **Environment Isolation:** Docker virtualizes environments to prevent the "works on my machine" discrepancy.
5. **Blueprint Builds:** Dockerfile rules compile images, run tests inside isolated container processes, and mount volumes for reporting.

### Important Interview Points
* **What is the difference between a Docker Image and a Container?**
  * An Image is a static, read-only template containing the OS, code, and configurations (the blueprint). A Container is a running instance of that image (the execution environment).
* **How do you handle sensitive credentials inside a GitHub Actions pipeline?**
  * We store credentials inside GitHub Repository Secrets and reference them dynamically inside the YAML environment block using the `${{ secrets.SECRET_NAME }}` syntax.
* **Why do we use volume mapping (`-v`) when running tests inside Docker?**
  * Containers have isolated file systems. Any HTML reports or failure screenshots generated during a run will be lost when the container stops. Volume mapping links a folder on the host machine to a folder inside the container to persist test reports.
* **What is a Cron schedule in pipeline automation?**
  * A cron schedule triggers a pipeline automatically at specific times or intervals. It is configured in the YAML file using a 5-field cron string (e.g. `'0 0 * * *'` to run at midnight every day).

### Quick Revision Sheet

| DevOps Tool | Key Syntax | Configuration Location | Primary Benefit |
| --- | --- | --- | --- |
| **Pipeline Trigger** | `on: [push, pull_request]` | `.github/workflows/yml` | Automate test execution on git updates |
| **Cron Trigger** | `cron: '0 0 * * *'` | `.github/workflows/yml` | Run nighty regressions automatically |
| **Secrets Vault**| `${{ secrets.DB_KEY }}` | GitHub Repo Settings | Secure API credentials |
| **Report Save** | `actions/upload-artifact@v4` | `.github/workflows/yml` | Preserve HTML test results |
| **Docker Build** | `docker build -t my-img .` | CLI command | Compile custom test environment |
| **Dockerfile** | `FROM`, `WORKDIR`, `RUN`, `CMD` | `./Dockerfile` | Script recipe for image creation |
| **Compose** | `docker-compose up` | `./docker-compose.yml` | Manage multi-container setups |
