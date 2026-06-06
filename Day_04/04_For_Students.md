# Day 4: Git & GitHub – Student Revision Notes

Quick reference guide to revise the concepts covered in the Git & GitHub session. Use this to review basic commands, branching strategies, and merge conflict resolution.

---

## Version Control Concepts

### Definition
**Version Control** is a software system that tracks history modifications, author edits, and file updates, allowing collaboration and rollbacks. *(Files ke dynamic updates logs ko save aur rollback karne ka process).*

### Example
A shared repository where multiple developers edit the same codebase simultaneously without overwriting each other's changes.

### Key Points
* Centralized version control (SVN) depends on a single server.
* Distributed version control (Git) clones the entire codebase history onto every developer's local machine, providing full backups.

### Common Mistakes
* **Not using version control for automated tests:** Saving test scripts in plain folders on local desks without version control makes team collaboration impossible.

---

## Git Fundamentals

### Definition
**Git** is a distributed version control tool that manages project file history across three local states: working directory, staging area, and local repository. *(Local project history track karne wala distributed version control engine).*

### Important Syntax
```bash
git init
git config --global user.name "Your Name"
```

### Example
Initializing a local folder as a git repository and configuring your developer profile.

### Key Points
* Working Directory: Where you edit your files.
* Staging Area (Index): Preparation area where you choose which files to commit.
* Local Repository: Local database containing committed checkpoints.

### Common Mistakes
* **Committing without configuration:** Git requires you to set your username and email before committing.

---

## Clone

### Definition
**Git Clone** downloads a complete copy of an existing remote repository (including history logs and branches) to your local computer. *(Remote cloud repository download karke local setup link register karna).*

### Important Syntax
```bash
git clone <remote_repository_url>
```

### Example
```bash
git clone https://github.com/vjti-qa/playwright-bootcamp.git
```

### Key Points
* Copies all files, branches, and commit histories.
* Automatically configures a remote link pointing to the source URL (origin).

### Common Mistakes
* **Cloning inside another active git repository:** Ensure you are in a clean directory before running `git clone`.

---

## Commit

### Definition
**Git Commit** saves a snapshot of staged changes into the local repository's history database. *(Staged files ko description message ke sath local repository history log me permanently save karna).*

### Important Syntax
```bash
git add <filename>
git commit -m "commit message"
```

### Example
```bash
git add package.json
git commit -m "feat: add initial project configurations"
```

### Key Points
* Staging changes using `git add` is required before committing.
* Commit messages should explain *why* the change was made.

### Common Mistakes
* **Writing meaningless commit messages:** Messages like `git commit -m "updates"` make history tracking useless. Commit messages should explain the changes in simple words.

---

## Push

### Definition
**Git Push** uploads local repository commits to a remote cloud repository (like GitHub). *(Local changes ko cloud repository server (GitHub) par upload karna).*

### Important Syntax
```bash
git push <remote_name> <branch_name>
```

### Example
```bash
git push -u origin main
```

### Key Points
* Transmits local commits to remote branches.
* `-u` sets the default remote upstream branch for future pushes.

### Common Mistakes
* **Pushing untracked changes:** You cannot push changes that have not been committed locally first. Stage and commit changes before running `git push`.

---

## Pull

### Definition
**Git Pull** fetches the latest commits from a remote repository and merges them directly into your current local active branch. *(GitHub server se latest changes fetch karke local code me merge karna).*

### Important Syntax
```bash
git pull <remote_name> <branch_name>
```

### Example
```bash
git pull origin main
```

### Key Points
* Connects to the remote server, downloads updates, and merges them.
* Equivalent to running `git fetch` followed by `git merge`.

### Common Mistakes
* **Pulling with uncommitted local changes:** Git will block the pull if remote changes conflict with uncommitted local modifications. Commit or stash your changes first.

---

## Branching Strategy

### Definition
A **Branching Strategy** is a set of rules software teams follow to create, name, and merge branches to protect production code. *(Stable production code ko safe rakhne ke liye git branches workflows structure).*

### Example
Using `main` for production, `dev` for development builds, and `feature/` branches for individual developer tickets.

### Key Points
* Short-lived branches isolate developer workflows.
* Main production branches remain locked, allowing updates only via pull requests.

### Common Mistakes
* **Committing code directly to the `main` branch:** Committing directly to the stable production branch can bypass test assertions and break code for the entire team.

---

## Feature Branch Workflow

### Definition
The **Feature Branch Workflow** is a team collaboration model where all new features are developed in isolated branches before being reviewed and merged. *(Isolated feature branches create karke changes local test complete hone par master branch me review request raise karna).*

### Important Syntax
```bash
git checkout -b <branch_name>
```

### Example
```bash
git checkout -b feature/login-tests
```

### Key Points
* Isolate feature development in temporary branch scopes.
* Prevent main production branches from breaking during active development.

### Common Mistakes
* **Forgetting to pull updates before creating a branch:** Always pull the latest changes from the main branch before creating a new feature branch to avoid outdated baselines.

---

## Pull Requests

### Definition
A **Pull Request** (PR) is a submission on GitHub that notifies team members that a feature branch is ready to be merged, inviting feedback and code reviews. *(Feature branch updates merge authorization ke liye request dashboard page open karna).*

### Example
Opening a pull request on GitHub to merge `feature/login-tests` into `main`.

### Key Points
* Summarizes branch goals, changes, and verification commands.
* Serves as a gateway for code reviews and test automation pipelines before code merges.

### Common Mistakes
* **Writing blank PR descriptions:** PR descriptions should explain the changes clearly to help reviewers understand the modifications.

---

## Merge Conflicts

### Definition
A **Merge Conflict** occurs when Git tries to merge two branches that modified the same line of code in the same file, and Git cannot automatically decide which version is correct. *(Same code line updates par Git merging halt checks).*

### Example
```text
<<<<<<< HEAD
  workers: 2
=======
  workers: 4
>>>>>>> feature/parallel-runs
```

### Key Points
* Git inserts conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
* To resolve: Delete the markers, select the desired code, stage, and commit.

### Common Mistakes
* **Committing conflict markers accidentally:** If you commit without erasing the marker lines, the file will contain compiler errors. Always clean up files before committing.

---

## Code Reviews

### Definition
A **Code Review** is a quality assurance process where team members inspect code changes in a Pull Request before approval. *(Merge se pehle code checks validation step).*

### Example
Approving a PR with "Looks Good To Me" (LGTM) after checking test execution logs.

### Key Points
* Reviewers check code logic, styling, and test coverage.
* Authors push commits to the same feature branch to resolve review comments.

### Common Mistakes
* **Approving pull requests without checking test execution logs:** Always verify that automated tests pass before approving merges.

---

## Assignment Summary

* **Assignment 1: Create Repository & Initial Commit**
  * *Concepts Practiced:* Initializing git local directories, staging files, committing configs, and remote origin linking.
* **Assignment 2: Feature Branch Workflow & Pull Request**
  * *Concepts Practiced:* Creating checkout branches, committing spec files, pushing remote origins, and drafting pull request templates.
* **Assignment 3: Merge Conflict Resolution Simulation**
  * *Concepts Practiced:* Conflict markers cleanup, selecting final parameters, staging, and conflict resolution commit blocks.

---

## Quick Revision Sheet

| Git Command | Target State | Description |
| :--- | :--- | :--- |
| **`git init`** | Working Directory | Initializes local directory tracking |
| **`git clone <url>`** | Working Directory | Downloads remote repository copy |
| **`git add <file>`** | Staging Area | Stages file changes for package commits |
| **`git commit -m "msg"`** | Local Repository | Saves snapshot committed logs |
| **`git push origin <br>`** | Remote Repository | Uploads commits to GitHub server |
| **`git pull origin <br>`** | Local working files | Fetches and merges remote updates |
| **`git checkout -b <br>`**| Branch Context | Creates and switches local branch |

---

## Important Takeaways

1. **DVCS Backups:** Distributed version control means you have a complete copy of the project history on your local machine, allowing you to track changes offline.
2. **Scoping commits:** Stage files selectively using `git add` to keep commits focused and meaningful.
3. **Conflict Resolution:** Merge conflicts are normal. Resolve them manually by removing conflict markers, selecting the desired code, staging, and committing the resolution.
