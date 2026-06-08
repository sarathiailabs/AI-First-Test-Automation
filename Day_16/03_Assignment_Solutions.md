# Day 16: CI/CD + Docker – Solutions

This file contains solutions for the theory questions and practical programming assignments assigned for Day 16.

---

## Theory Question Solutions

### Question 1
What is a CI/CD pipeline? How does automating test suites inside a pipeline help maintain software quality?

### Answer
* **CI/CD Pipeline:** An automated software process that builds, tests, and deploys code changes immediately upon developer check-ins.
* **Maintaining Quality:** By running regression suites automatically on every pull request, the pipeline acts as a gatekeeper. If a developer accidentally introduces a breaking code change, the automated checks fail immediately and block the code merge, preventing bugs from reaching staging or production.

---

### Question 2
What is the difference between a Docker Image and a Docker Container? Explain using an analogy.

### Answer
* **Docker Image:** A static, read-only template containing the OS blueprint, code, libraries, and launch configurations.
* **Docker Container:** An active, isolated running instance generated from the Docker Image template.
* **Analogy:** Think of a **Docker Image** as a cooking recipe printed in a book (static blueprint). A **Docker Container** is the actual meal prepared and cooking on the stove (active execution instance of the recipe).

---

### Question 3
What is the purpose of **Volume Mapping** (`-v` or `volumes:`) when running test automation containers? What happens to your HTML reports if volumes are not mapped?

### Answer
* **Purpose:** To link a directory on your host computer (physical machine) to a directory inside the container, allowing files generated inside the container to persist locally.
* **What happens without mapping:** Docker containers have isolated file systems that are destroyed when the container stops. If you do not map volumes, any HTML reports or failure screenshots generated during the run are deleted inside the container, leaving you with no test evidence files to debug.

---

### Question 4
How do you pass database passwords or API keys securely to a GitHub Actions pipeline? Why should these never be hardcoded in the YAML workflow file?

### Answer
* **Secure Passing:** Store the values inside GitHub Repository Secrets (`Settings -> Secrets and variables -> Actions`) and reference them in the YAML workflow under the environment block:
  ```yaml
  env:
    MY_PASSWORD: ${{ secrets.MY_SECRET_NAME }}
  ```
* **Why not hardcoded:** Hardcoded values are saved in plain text in your Git repository history. Anyone with access to the codebase can read your keys, exposing databases and production APIs to security breaches.

---

### Question 5
Explain why the order of commands in a `Dockerfile` matters. What is the benefit of copying `package.json` and running `npm install` before copying the rest of the project code?

### Answer
* **Why Order Matters:** Docker builds images in layers and caches them. If a layer's files change, Docker rebuilds that layer and all subsequent layers, invalidating the cache.
* **Benefit of package.json first:** Test files change frequently, but project dependencies (`package.json`) change rarely. By copying `package.json` and running `npm ci` first, Docker caches the heavy `node_modules` install layer. When you modify test files and rebuild, Docker reuses the cached dependency layer, cutting rebuild times from minutes to seconds.

---

## Programming Assignment Solutions

### Assignment 1: Create GitHub Actions Pipeline

#### Solution Code (`.github/workflows/playwright-pipeline.yml`)
```yaml
name: Playwright Tests Pipeline
on:
  push:
    branches: [ main ]

jobs:
  test-run:
    runs-on: ubuntu-latest
    steps:
    # 1. Fetch source code from repository
    - name: Checkout Code
      uses: actions/checkout@v4

    # 2. Setup Node environment
    - name: Setup NodeJS
      uses: actions/setup-node@v4
      with:
        node-version: 18

    # 3. Clean install NPM dependencies
    - name: Install Dependencies
      run: npm ci

    # 4. Install Playwright browser binaries and system deps
    - name: Install Playwright Browsers
      run: npx playwright install --with-deps

    # 5. Execute tests
    - name: Run Tests
      run: npx playwright test

    # 6. Archive reports as build artifacts
    - name: Upload HTML Report
      uses: actions/upload-artifact@v4
      if: always() # Run even if test execution step failed
      with:
        name: vjti-test-report
        path: playwright-report/
        retention-days: 30
```

---

### Assignment 2: Dockerize Playwright Framework

#### Solution Code (`Dockerfile`)
```dockerfile
# Step 1: Use official Playwright base image
FROM mcr.microsoft.com/playwright:v1.40.0-jammy

# Step 2: Set working directory inside container
WORKDIR /usr/src/app

# Step 3: Copy package files first to optimize cache layers
COPY package*.json ./

# Step 4: Install NPM dependencies
RUN npm ci

# Step 5: Copy framework source code files
COPY . .

# Step 6: Define execution trigger command
CMD ["npx", "playwright", "test"]
```

---

### Assignment 3: Compose Test Orchestration

#### Solution Code (`docker-compose.yml`)
```yaml
version: '3.8'

services:
  # Define test framework service
  vjti-automation:
    build: . # Build the image using the local Dockerfile
    volumes:
      # Map container report output folder back to local directory
      - ./playwright-report:/usr/src/app/playwright-report
```

#### CLI Execution Commands
1. **Build and execute tests:**
   ```bash
   docker compose up --build
   ```
   *Explanation:* This command tells Docker Compose to read `docker-compose.yml`, compile the image using the `Dockerfile` recipes, map the report directories, launch the container process, and shut down once tests finish.
2. **Retrieve HTML Report:**
   After the container stops, the local folder `./playwright-report` will contain the generated test reports, allowing you to open `index.html` on your local browser.
