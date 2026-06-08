# Day 16: CI/CD + Docker – Assignments

This assignment file contains theoretical questions and practical tasks designed to reinforce CI/CD pipeline design, YAML script syntax, secrets management, Dockerfile rules, volume mapping, and docker compose configurations.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is a CI/CD pipeline? How does automating test suites inside a pipeline help maintain software quality?

### Question 2 🔥 **Frequently Asked**
What is the difference between a Docker Image and a Docker Container? Explain using an analogy.

### Question 3 📌 **Important**
What is the purpose of **Volume Mapping** (`-v` or `volumes:`) when running test automation containers? What happens to your HTML reports if volumes are not mapped?

### Question 4 📌 **Important**
How do you pass database passwords or API keys securely to a GitHub Actions pipeline? Why should these never be hardcoded in the YAML workflow file?

### Question 5 💡 **Good to Know**
Explain why the order of commands in a `Dockerfile` matters. What is the benefit of copying `package.json` and running `npm install` before copying the rest of the project code?

---

## Practical Assignments

### Assignment 1: Create GitHub Actions Pipeline

* **Interview Relevance:** Writing a workflow pipeline YAML configuration from scratch is a standard DevOps/QA requirement. Verifies capability to structure run phases.
* **Difficulty Level:** Easy-Intermediate
* **Concepts Covered:** YAML triggers, checkout actions, NodeJS setup, Playwright dependencies installations.

#### Problem Statement
Write a GitHub Actions pipeline configuration file named `playwright-pipeline.yml` that triggers on every push to the `main` branch, installs node 18, resolves project dependencies, fetches browser binaries, and executes the Playwright suite.

#### Requirements
1. Create the workflow file in the path: `.github/workflows/playwright-pipeline.yml`.
2. Configure the trigger to run on `push` events to the `main` branch.
3. Set the runner machine environment to `ubuntu-latest`.
4. Implement steps:
   - Check out code using `actions/checkout@v4`.
   - Setup Node version 18 using `actions/setup-node@v4`.
   - Install dependencies using `npm ci`.
   - Install Playwright browsers and system dependencies using `npx playwright install --with-deps`.
   - Run tests using `npx playwright test`.
   - Upload the test results folder `playwright-report` as a build artifact named `vjti-test-report` (configured to run `always` even if the execution fails).

---

### Assignment 2: Dockerize Playwright Framework

* **Interview Relevance:** Packaging automation frameworks inside Docker containers ensures environment consistency across local machines and cloud execution nodes.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** `FROM`, `WORKDIR`, `COPY`, `RUN`, `CMD` instructions.

#### Problem Statement
Write a custom `Dockerfile` to package your Playwright automation framework. The image must use the official Playwright base image, install dependencies, copy project files, and define the run instruction.

#### Requirements
1. Create a file named `Dockerfile` in the root of your project folder.
2. Use the base image `mcr.microsoft.com/playwright:v1.40.0-jammy`.
3. Set the working directory to `/usr/src/app`.
4. Copy `package.json` and `package-lock.json`.
5. Run `npm ci` to install project dependencies.
6. Copy all other framework files from your local directory to the container workspace.
7. Set the default container start command to run Playwright tests: `npx playwright test`.

---

### Assignment 3: Compose Test Orchestration

* **Interview Relevance:** Docker Compose simplifies containerized execution commands, allowing engineers to run complex setups with a single YAML config.
* **Difficulty Level:** Intermediate-Advanced
* **Concepts Covered:** Compose services definitions, volume mappings.

#### Problem Statement
Write a `docker-compose.yml` file to coordinate building your Playwright framework image and running it while mounting local directories to capture output files.

#### Requirements
1. Create a file named `docker-compose.yml` in the root of your project folder.
2. Define a service named `vjti-automation`.
3. Configure the service to build using the local `Dockerfile` (`build: .`).
4. Set up volume mappings to map the host directory `./playwright-report` to `/usr/src/app/playwright-report` inside the container.
5. Provide a description of the CLI command used to spin up and run the service using compose.

#### Hints
* The compose run command is: `docker compose up --build`.
