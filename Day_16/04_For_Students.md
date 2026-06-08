# Day 16: CI/CD + Docker – Student Revision Notes

This revision sheet provides a quick-revision summary of DevOps fundamentals, GitHub Actions workflows, secrets management, Docker containers, images, Dockerfiles, and compose configurations for Day 16.

---

## Topic Revision

### 1. CI/CD Fundamentals
* **Definition:** Automating the build, test, and deployment flow of code updates to catch bugs early. *(Code push karte hi automation tests run karke pipeline pass/fail status update karna).*
* **Key Points:**
  * CI (Continuous Integration): Code compile and test execution checks.
  * CD (Continuous Deployment): Automated release to servers.

---

### 2. GitHub Actions
* **Definition:** A built-in GitHub runner platform to automate tasks via YAML workflow configurations. *(Git triggers ke through workflow execution manage karne ka platform).*
* **Key triggers:** `push`, `pull_request`, `schedule`.

---

### 3. Workflow Design & Pipeline Creation
* **Definition:** Designing execution stages (Checkout $\rightarrow$ Node Setup $\rightarrow$ NPM Install $\rightarrow$ Playwright Setup $\rightarrow$ Run Tests).
* **Example YAML snippet:**
  ```yaml
  name: CI
  on: [push]
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - run: npm ci
  ```
* **Common Mistakes:** Saving YAML files outside `.github/workflows/`, which causes GitHub to ignore them.

---

### 4. Scheduled Runs
* **Definition:** Triggering test suites automatically at intervals using Cron parameters. *(Cron specifications ke according schedule runs execution).*
* **Example Syntax:**
  `- cron: '0 0 * * *'` (Runs nightly at midnight).

---

### 5. Parallel Execution (CI/CD)
* **Definition:** Dividing a test suite across multiple separate virtual machine runners (shards) in the cloud. *(Matrix parameters settings se multiple Ubuntu systems me tests share run karna).*
* **Example Option:** `npx playwright test --shard=1/3`

---

### 6. Test Reporting (CI/CD)
* **Definition:** Storing HTML reports, trace files, and videos as pipeline artifacts on GitHub.
* **Example Action:** `uses: actions/upload-artifact@v4` with option `if: always()` to capture failures.

---

### 7. Secrets Management
* **Definition:** Storing API keys and passwords in encrypted Repository Secrets instead of plaintext code. *(Passwords ko vault repository settings me hide rakhna).*
* **Example Reference:** `${{ secrets.MY_PASSWORD }}`

---

### 8. Docker Fundamentals
* **Definition:** Virtualizing environments at the OS level to run apps inside isolated packages (containers). *(Code, operating system, node version aur browser binaries ko package (container) me wrap karna).*
* **Key Benefit:** Eliminates "works on my machine" discrepancy.

---

### 9. Images & Containers
* **Definition:** An Image is a read-only blueprint template. A Container is a running instance of that template. *(Image ek recipe recipe card hai, aur Container pakka hua khana hai).*

---

### 10. Dockerfile
* **Definition:** A script manual detailing steps to assemble a Docker image.
* **Example Script:**
  ```dockerfile
  FROM mcr.microsoft.com/playwright:v1.40-jammy
  WORKDIR /app
  COPY package*.json ./
  RUN npm ci
  COPY . .
  CMD ["npx", "playwright", "test"]
  ```

---

### 11. Docker Compose
* **Definition:** A tool for running multi-container applications using a single YAML configuration file.
* **Example command:** `docker compose up --build`

---

## Assignment Summary

During this session, we practice:
1. **GitHub Actions Pipeline Setup:** Creating `.github/workflows/playwright-pipeline.yml` with node installations and artifact uploads.
2. **Dockerfile Packaging:** Writing instructions to compile clean Playwright test container environments.
3. **Docker Compose Orchestration:** Creating `docker-compose.yml` to trigger runs and map local report outputs.

---

## Quick Revision Sheet

| DevOps Tool | Config File | Key Command | Main Purpose |
| --- | --- | --- | --- |
| **GHA Pipeline**| `.github/workflows/*.yml` | `npx playwright test` | Automate test builds on code updates |
| **Cron Trigger** | `.github/workflows/*.yml` | `cron: '0 0 * * *'` | Execute nightly regression checks |
| **Secrets Vault**| GHA Repository Secrets | `${{ secrets.KEY }}` | Secure production credentials |
| **Docker Build** | `./Dockerfile` | `docker build -t img .`| Build custom container environment |
| **Docker Run** | `./Dockerfile` | `docker run -v path:path`| Run test suite inside isolated OS |
| **Docker Comp.** | `./docker-compose.yml` | `docker compose up` | Orchestrate multi-container tests |

---

## Important Takeaways

1. **GitHub Secrets:** Never commit plaintext credentials to Git repositories; manage passwords dynamically using Repository Secrets.
2. **Cache Optimization:** Always copy `package.json` and run `npm ci` before copying test scripts in your Dockerfile to reuse layer caches.
3. **Volume Mounts:** Container files are deleted when containers stop. Map local directories using volumes to extract HTML test reports.
