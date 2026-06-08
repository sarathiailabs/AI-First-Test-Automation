# Day 16: CI/CD + Docker – Trainer Guide

This delivery handbook helps instructors teach Day 16: CI/CD + Docker. Follow the workflows, whiteboard outlines, and interactive student engagement guidelines below.

---

## Session Opening

### Welcome Script
> *"Good evening, class! Welcome to Day 16. Over the last few days, we built a robust automation framework with Page Objects, fixtures, and trace loggers. But so far, we have only run these tests on our local laptops. Today, we enter the world of DevOps. We will learn how to trigger tests automatically whenever code is pushed to GitHub using CI/CD pipelines, and how to package our tests inside lightweight isolated packages called Docker Containers. Let's get started!"*

### Session Goal
Provide a comprehensive understanding of automated build pipelines and containerization. Explain how to design YAML workflows, handle repository secrets, Matrix sharding, Dockerfile builds, volume mapping, and compose runners.

### Motivation
> *"How do you solve the 'It works on my machine but crashes on yours' problem? This is the most common debate between developers and QA testers. The developer has NodeJS v20, and the tester has NodeJS v18 with different browser binaries. Docker solves this by packing the exact NodeJS, browser engine, and code files into a standardized container. The container runs identically on local laptops, teammate machines, and cloud environments. It guarantees consistency!"*

---

## Topic 1: CI/CD Fundamentals

### Trainer Introduction
> *"CI/CD stands for Continuous Integration and Continuous Deployment. Imagine a large automobile assembly factory. If every worker compiled parts in their own way without validation, the final car would fail. CI/CD is the automated assembly line: every time a developer commits code (Integration), the system automatically compiles, builds, formats, and runs our Playwright test suite. If tests fail, the assembly line stops, preventing buggy code from reaching production."*

### Student Engagement Questions
1. *"What is the difference between manual regression testing and automated pipeline verification?"*
2. *"Can anyone give an everyday water filter analogy to explain code integration layers?"*

### Whiteboard Teaching
```text
  Developer Push ──► Build Project ──► Run Unit Tests ──► Run Playwright tests ──► Deploy to Prod
                                                                  ▲
                                                       (Gatekeeper Pipeline)
```

### Topic Recap
CI/CD automates validation gates to detect bugs early in code lifecycles.

### Transition Script
> *"To run a CI/CD pipeline on GitHub, we use GitHub's built-in platform: GitHub Actions. Let's learn how it works."*

---

## Topic 2: GitHub Actions & Workflow Design

### Trainer Introduction
> *"GitHub Actions is like an automatic sensor tap in a shopping mall. You don't turn handles; as soon as you place your hands (Git Event: push/PR), the water starts running (Workflow starts). We write YAML files that tell GitHub: 'Start an Ubuntu machine, checkout our code, install node, and run Playwright'."*

### Student Engagement Questions
1. *"When you open a Pull Request, how does GitHub know which test workflow to run?"*
2. *"What are Trigger Events in pipeline automation?"*

### Topic Recap
GitHub Actions triggers custom YAML automated workflows on repository events.

### Transition Script
> *"Let's build a workflow YAML script from scratch. This is Pipeline Creation."*

---

## Topic 3: Pipeline Creation & Scheduled Runs

### Trainer Introduction
> *"We write our pipeline steps inside a file saved under `.github/workflows/playwright.yml`. We can configure it to run on Git push events, or trigger it automatically on a timer using Cron schedule expressions."*

### Live Coding Demonstration
#### Step 1: Type
Show cron syntax configuration:
```yaml
on:
  schedule:
    - cron: '0 0 * * *' # Midnight run
```
#### Step 4: Questions
*"If cron is '30 8 * * 1', when will the test execute? (Answer: 8:30 AM on Monday)."*

### Topic Recap
Pipelines checkout code, install node/browsers, run tests, and support cron-scheduled triggers.

### Transition Script
> *"Running large test suites sequentially on a single pipeline machine takes a long time. Let's look at how we split tests across multiple runners: Parallel Execution."*

---

## Topic 4: Parallel Execution & Test Reporting (CI/CD)

### Trainer Introduction
> *"Playwright splits test suites into chunks using sharding flags (like `--shard=1/3`). We configure GitHub Actions to spin up multiple virtual machines concurrently, run these shards in parallel, and upload HTML reports as zip artifacts that we can download later."*

### Whiteboard Teaching
```text
                          [ Push main ]
                                │
          ┌─────────────────────┼─────────────────────┐ (Concurrnt VM Runners)
          ▼                     ▼                     ▼
  [ Shard 1/3 (Chrome) ] [ Shard 2/3 (Firefox) ] [ Shard 3/3 (Webkit) ]
          │                     │                     │
          └─────────────────────┼─────────────────────┘ (Upload Artifacts)
                                ▼
                       [ Consolidated ZIP ]
```

### Topic Recap
Parallel execution shards tests across cloud VM runners, archiving logs as build artifacts.

### Transition Script
> *"If our tests require secure passwords or API keys, how do we pass them to these cloud runners without exposing them in Git? We use Secrets Management."*

---

## Topic 5: Environment Variables & Secrets Management

### Trainer Introduction
> *"A GitHub Repository Secret is a secure locker. You type your password in GitHub Settings once. In the YAML pipeline, you reference it using a variable like `${{ secrets.ADMIN_PASS }}`. At runtime, GitHub pulls the password from the vault, passes it to the runner env, and masks it in test logs with asterisks (`***`)."*

### Student Engagement Questions
1. *"What happens if you hardcode your database password inside a public GitHub workflow file?"*
2. *"How does GitHub logs protect secret variables? (Answer: Masking)."*

### Topic Recap
Secrets management secures credentials by fetching them dynamically from encrypted repositories.

### Transition Script
> *"We now have automated cloud pipelines. But how do we ensure the cloud runner runs tests in the exact same environment as our local laptop? We use Docker."*

---

## Topic 6: Docker Fundamentals, Images, and Containers

### Trainer Introduction
> *"Docker is like the shipping container industry. In the old days, loading cars, pianos, and grains loose on a ship was chaotic. Standardized shipping containers solved this: they have fixed sizes, fit on any ship, and cranes load them uniformly. A Docker Image is the blueprint recipe. A Docker Container is the active running instance of that image containing the OS, NodeJS, code, and browser engines packaged together."*

### Student Engagement Questions
1. *"If you modify a recipe book page, does the cooked cake update automatically? How does this explain Images vs Containers?"*

### Whiteboard Teaching
```text
  [ Docker Image ] (Blueprint Template / Read-Only)
        │
        └──► [ Container 1 (Local Laptop) ]
        └──► [ Container 2 (QA Staging VM) ]  (Identical execution environment)
        └──► [ Container 3 (GHA Runner VM) ]
```

### Topic Recap
Docker containers package operating systems and browser dependencies into identical environments.

### Transition Script
> *"To build our own custom Playwright Docker image, we write a Dockerfile. Let's study how to structure it."*

---

## Topic 7: Dockerfile & Docker Compose

### Trainer Introduction
> *"A Dockerfile is a step-by-step recipe. It tells Docker: 'Start with the official Microsoft Playwright image (FROM), set a directory (WORKDIR), copy package files, run npm ci (dependencies), copy our tests, and set a default run command (CMD)'. We use Docker Compose to coordinate building and running these containers."*

### Live Coding Demonstration
#### Step 1: Type
Write simple Dockerfile steps:
```dockerfile
FROM mcr.microsoft.com/playwright:v1.40.0-jammy
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npx", "playwright", "test"]
```
#### Step 2: Explain
Explain that placing `COPY package*.json ./` and `RUN npm ci` before copying the rest of the project code saves time. Since package dependencies change rarely, Docker caches the NPM install layer. If we modify test files, Docker reuses the cached layer and skips `npm install`.

### Topic Recap
Dockerfile instructions assemble custom images, and Docker Compose orchestrates multi-container test runs.

### Transition Script
> *"Now let's look at the commands to run Playwright inside containers and integrate them with our CI/CD pipelines."*

---

## Topic 8: Running Playwright in Containers & CI/CD Integration

### Trainer Introduction
> *"Since containers have isolated file systems, any HTML reports generated during the run will be deleted when the container stops. To prevent this, we use Volume Mapping (`-v`) to link our host machine directory to the container. Finally, we integrate these docker execution commands inside our GitHub Actions workflow."*

### Live Coding Demonstration
#### Step 1: Type
Run compose trigger:
```bash
docker compose up --build
```

### Topic Recap
Container runs use volume mounts to retrieve test reports, integrating runs directly into pipeline steps.

---

## Session Closing

### Session Summary
* We covered CI/CD pipelines, GitHub Actions workflows, triggers, and schedulers.
* We configured matrix sharding, artifact uploads, and GitHub Secrets.
* We explored Docker OS virtualization, Images vs Containers, and Dockerfiles.
* We orchestrated container runs using Docker Compose and integrated them into CI workflows.

### Knowledge Check Questions
1. *"Why should you not save YAML files in the root folder? (Answer: GitHub only runs workflows saved in .github/workflows/)."*
2. *"What does 'if: always()' ensure in GHA? (Answer: Runs steps even if tests fail)."*
3. *"Is a Docker Container a VM? (Answer: No, it shares the host OS kernel, making it lightweight)."*
4. *"How do we preserve HTML reports generated inside a container? (Answer: Volume mapping)."*
5. *"Why is caching important in Dockerfile builds?"*

### Homework Guidance
Instruct students to complete the `Day_16` assignments:
1. GHA pipeline `.github/workflows/playwright-pipeline.yml`.
2. Custom `Dockerfile` setup.
3. Custom `docker-compose.yml` configuration.

### Next Session Preview
In the next session (Day 17: AI for Test Automation + Career Prep), we will explore how AI is used in test generation, prompt engineering, locator healing, and optimize resumes and LinkedIn portfolios for QA automation roles.
