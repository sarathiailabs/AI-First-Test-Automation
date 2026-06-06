# Day 4: Git & GitHub – Assignments

This assignment file contains both theoretical and practical questions designed to reinforce the concepts of version control, git local states (working directory, staging, local repo), branching models, pull requests, and merge conflict resolution.

---

## Theory Questions

### Question 1 🔥 **Frequently Asked**
What is the difference between **Git** and **GitHub**? Explain the difference to an absolute beginner.

### Question 2 🔥 **Frequently Asked**
What is the difference between `git fetch` and `git pull`? Under what conditions would you prefer `git fetch` over `git pull`?

### Question 3 📌 **Important**
Describe the staging area in Git. Why do we need a staging area instead of committing changes directly from the working directory?

### Question 4 📌 **Important**
What is a **Merge Conflict**? Describe a scenario in automated QA team environments where two engineers trigger a merge conflict, and explain the steps to resolve it.

### Question 5 💡 **Good to Know**
What is the difference between a **Fast-forward merge** and a **Three-way merge** (non-fast-forward merge)?

---

## Practical Assignments

### Assignment 1: Create Repository & Initial Commit

* **Interview Relevance:** Sets up the foundations of workspace management. Demonstrates your ability to configure Git, initialize local tracking, and push to cloud origins.
* **Difficulty Level:** Beginner
* **Concepts Covered:** `git init`, `git config`, `git status`, `git add`, `git commit`, `git remote add`.

#### Problem Statement
Initialize a local repository named `playwright-workspace`, create a gitignore configuration, stage files, and commit them.

#### Requirements
1. Open your terminal, create a directory `playwright-workspace`, and navigate inside it.
2. Initialize a local Git repository.
3. Configure your username and email address locally for this repository.
4. Create a file named `package.json` with mock dependencies.
5. Create a `.gitignore` file and add `node_modules/` to it.
6. Verify status using the terminal.
7. Stage both `package.json` and `.gitignore`.
8. Commit the changes using a descriptive commit message.
9. Link your local repository to a remote repository URL (e.g. `https://github.com/vjti-qa/playwright-workspace.git`).

#### Expected Output
```text
[master (root-commit) a1b2c3d] Initial Commit: Add package configuration and gitignore
 2 files changed, 15 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 package.json
```

---

### Assignment 2: Feature Branch Workflow & Pull Request

* **Interview Relevance:** Demonstrates team collaboration capabilities. Developing in feature branches and writing clear Pull Requests are core daily tasks for any software engineer.
* **Difficulty Level:** Beginner-Intermediate
* **Concepts Covered:** `git checkout -b`, branch pushes, GitHub Pull Request reviews.

#### Problem Statement
Create a feature branch named `feature/login-tests` to develop login test files. Add changes, commit, push to GitHub, and write a detailed Pull Request description template.

#### Requirements
1. Ensure you are on the `main` branch.
2. Create and switch to a new branch named `feature/login-tests` using a single command.
3. Add a file named `login.spec.ts` under a new `tests/` directory containing a mock test.
4. Commit the changes to your branch.
5. Push the feature branch to the remote GitHub repository.
6. Draft a Pull Request description template containing:
   * **Title**: Clear description of PR (e.g., `feat: add student login test script`).
   * **Goal**: Why this test script was added.
   * **Verification**: List of commands run to test the code.
   * **Screenshots/Logs**: Text logs verifying execution success.

#### Hints
* Use `git checkout -b feature/login-tests` to create and switch.
* Push to the remote origin using `git push -u origin feature/login-tests`.

---

### Assignment 3: Merge Conflict Resolution Simulation

* **Interview Relevance:** Merge conflicts happen daily in collaborative settings. Demonstrates conflict triage capabilities.
* **Difficulty Level:** Intermediate
* **Concepts Covered:** Git conflict markers, text editing, staging resolved inputs, conflict commit.

#### Problem Statement
Resolve a merge conflict in a shared `playwright.config.ts` configuration file. Two engineers modified the worker limit simultaneously, triggering a conflict.

**Conflicted Code block in `playwright.config.ts`:**
```text
import { defineConfig } from '@playwright/test';

export default defineConfig({
<<<<<<< HEAD
  workers: 2, // Modified locally on main branch
=======
  workers: 4, // Modified on feature branch being merged
>>>>>>> feature/parallel-runs
  timeout: 30000,
});
```

#### Requirements
1. Open the file containing the conflict markers.
2. Identify the local changes vs. the incoming changes from the branch.
3. Resolve the conflict by deciding to keep **4 workers** as the final configuration.
4. Delete all Git conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) and cleaning up comments.
5. Stage the resolved `playwright.config.ts` file in the terminal.
6. Commit the resolved state using a clear message indicating a merge conflict resolution.

#### Hints
* The resolved code section in the file should look exactly like:
  ```typescript
  import { defineConfig } from '@playwright/test';

  export default defineConfig({
    workers: 4,
    timeout: 30000,
  });
  ```
* Complete the merge conflict using `git commit -m "Resolve merge conflict..."` after staging.
