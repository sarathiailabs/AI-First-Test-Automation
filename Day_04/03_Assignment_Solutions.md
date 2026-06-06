# Day 4: Git & GitHub – Assignment Solutions

This file contains solutions and detailed explanations for the theory questions and practical assignments from Day 4.

---

## Theory Question Solutions

### Question 1 🔥 **Frequently Asked**
What is the difference between **Git** and **GitHub**? Explain the difference to an absolute beginner.

### Answer
* **Git:** Git is a local software tool that runs on your computer. It is the engine that tracks changes in your files, creates save checkpoints (commits), and manages your code history locally. It does not require internet to track changes.
* **GitHub:** GitHub is a cloud-based web service that hosts Git repositories online. It is a shared storage drive (like Google Drive or Dropbox) designed specifically for code, allowing teams to upload (push) their local Git histories, review each other's code (pull requests), and collaborate.
* **Hinglish Helper:** *Git aapke computer par code track karne ka local software engine hai; GitHub us tracking history ko cloud par host karne ka social database platform hai.*

---

### Question 2 🔥 **Frequently Asked**
What is the difference between `git fetch` and `git pull`? Under what conditions would you prefer `git fetch` over `git pull`?

### Answer
* **`git fetch`**: Connects to the remote server and downloads all metadata updates, branch pointer positions, and history logs that other developers have pushed. It **does not modify** your local working directory files.
* **`git pull`**: Performs a `git fetch` first to get remote updates, and then immediately runs `git merge` to integrate those updates into your current active local branch. It directly updates your files.
* **Hinglish Helper:** *Fetch sirf notification updates download karta hai bina local files touch kiye; Pull un updates ko local files me modify karke merge kar deta hai.*

#### Why prefer `git fetch` over `git pull`?
You prefer `git fetch` when you want to review what changes have been made in the remote repository before merging them. It lets you check the differences (`git diff`) first to ensure other developers' changes won't break your local code before committing to a merge.

---

### Question 3 📌 **Important**
Describe the staging area in Git. Why do we need a staging area instead of committing changes directly from the working directory?

### Answer
The **Staging Area** (also called the index) is an intermediate preparation zone between your local Working Directory and the Git Repository history database.

#### Why we need the Staging Area:
1. **Selective Commits:** If you modify three files (e.g., `login.spec.ts`, `playwright.config.ts`, and a temporary `scratch.txt`), you might only want to save the login script in this commit. The staging area lets you choose exactly which files to package:
   ```bash
   git add login.spec.ts # Staged
   # Now, committing saves only login.spec.ts. The config and scratch files stay unstaged.
   ```
2. **Logical Snapshots:** It prevents you from creating massive commits containing unrelated features. You can group related changes together into separate, logical commits.

---

### Question 4 📌 **Important**
What is a **Merge Conflict**? Describe a scenario in automated QA team environments where two engineers trigger a merge conflict, and explain the steps to resolve it.

### Answer
A **Merge Conflict** occurs when Git tries to merge two branches that modified the same line of code in the same file, and Git cannot automatically decide which version is correct.

#### QA Scenario:
1. **Engineer A** is working on `playwright.config.ts` locally on `main` and updates the timeout value:
   `timeout: 30000` (increases time limit).
2. **Engineer B** is working on a feature branch `feature/parallel-runs` and updates the same timeout line:
   `timeout: 45000` (needs more time for parallel execution).
3. **Engineer B** pushes their branch and merges it first.
4. When **Engineer A** tries to pull or merge, Git sees two different values for the same timeout line, stops the merge, inserts conflict markers, and asks for manual correction.

#### Resolution Steps:
1. Open `playwright.config.ts` in an editor.
2. Locate the conflict markers: `<<<<<<< HEAD`, `=======`, `>>>>>>>`.
3. Erase the marker lines completely.
4. Manually edit the line to contain the correct resolved value (e.g., `timeout: 45000`).
5. Run `git add playwright.config.ts` to stage the file as resolved.
6. Run `git commit -m "Resolve merge conflict in timeouts"` to complete the merge.

---

### Question 5 💡 **Good to Know**
What is the difference between a **Fast-forward merge** and a **Three-way merge** (non-fast-forward merge)?

### Answer
* **Fast-Forward Merge:** Occurs when the target branch has no new commits since the source branch was created. Git simply moves the branch pointer forward to point to the latest commit of the source branch. No new merge commit is created.
* **Three-Way Merge:** Occurs when both the target branch and the source branch have new commits since branching. Git compares the tips of both branches with their common ancestor (a 3-way check) to combine changes. This creates a new **Merge Commit** automatically.

---

## Programming Assignment Solutions

### Assignment 1: Create Repository & Initial Commit

Below is the complete terminal command sequence:

#### Solution Code
```bash
# 1. Create directory and navigate inside
mkdir playwright-workspace
cd playwright-workspace

# 2. Initialize Git
git init

# 3. Configure Git profiles locally for this folder
git config user.name "Rahul Verma"
git config user.email "rahul.verma@vjti.ac.in"

# 4. Create package.json file with dependencies config
echo '{ "dependencies": { "@playwright/test": "latest" } }' > package.json

# 5. Create .gitignore to skip node modules
echo "node_modules/" > .gitignore

# 6. Check status of files
git status

# 7. Stage all files
git add package.json .gitignore

# 8. Commit changes
git commit -m "Initial Commit: Add package configuration and gitignore"

# 9. Link local repository to remote GitHub repository origin
git remote add origin https://github.com/vjti-qa/playwright-workspace.git
```

#### Explanation
* **`git init`**: Creates a hidden `.git` folder in the directory to begin tracking.
* **`git config user.name`**: Sets the author identity for commits.
* **`echo "node_modules/" > .gitignore`**: Instructs Git to ignore node libraries so they aren't pushed to the remote server.

---

### Assignment 2: Feature Branch Workflow & Pull Request

Here are the terminal commands to switch, edit, and push the branch:

#### Solution Code
```bash
# 1. Switch back to main and pull latest updates
git checkout main
git pull origin main

# 2. Create and switch to new feature branch using single command
git checkout -b feature/login-tests

# 3. Create tests folder and add login spec file
mkdir tests
echo "import { test } from '@playwright/test';" > tests/login.spec.ts

# 4. Stage and commit changes
git add tests/login.spec.ts
git commit -m "feat: add initial login validation spec"

# 5. Push branch to GitHub origin
git push -u origin feature/login-tests
```

#### Pull Request Description Template (GitHub UI):
```markdown
# Pull Request: Add Student Login Test Script

## Goal
This PR introduces the initial automated login validation test script for the VJTI student portal, verifying basic credential forms and redirection paths.

## Changes
* Created a new `tests/` folder.
* Added `tests/login.spec.ts` containing mock login assertion steps.

## Verification
Executed local execution checks:
* Commands run: `npx playwright test tests/login.spec.ts`

## Verification Logs
```text
Running 1 test using 1 worker
  1 passed (1.2s)
\```
```

---

### Assignment 3: Merge Conflict Resolution Simulation

Here is the resolved configuration code and the terminal commands required to complete the merge.

#### Resolved `playwright.config.ts` Code
```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  workers: 4, // Conflict resolved: kept feature branch parallel limits
  timeout: 30000,
});
```

#### Solution Code
```bash
# 1. Identify conflicted files
git status
# Output shows playwright.config.ts has conflict markers

# 2. Open playwright.config.ts in editor and edit:
# - Remove the "<<<<<<< HEAD", "=======", and ">>>>>>> feature/parallel-runs" marker lines.
# - Keep the final "workers: 4," line and save.

# 3. Stage the resolved file
git add playwright.config.ts

# 4. Commit the resolution to complete the merge process
git commit -m "Resolve merge conflict in playwright configuration: kept parallel worker limit"
```

#### Explanation
* Manual resolution is required because Git cannot decide between workers 2 and 4.
* **`git add`**: Informs Git that the conflicts have been resolved in the file.
* **`git commit`**: Creates a merge commit, successfully completing the merge loop.
