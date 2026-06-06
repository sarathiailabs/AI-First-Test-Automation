# Day 4: Git & GitHub

## Session Plan

| Topic | Estimated Time |
| --- | --- |
| Version Control Concepts | 10 mins |
| Git Fundamentals | 15 mins |
| Git Clone | 10 mins |
| Git Commit | 15 mins |
| Git Push | 10 mins |
| Git Pull | 10 mins |
| Branching Strategy | 15 mins |
| Feature Branch Workflow | 10 mins |
| Pull Requests (PRs) | 10 mins |
| Merge Conflicts | 10 mins |
| Code Reviews & Best Practices | 5 mins |

### Total Duration: 2 Hours (120 Minutes)

---

## Learning Objectives

By the end of this session, you will be able to:
* Explain the difference between Centralized and Distributed Version Control systems.
* Configure Git profiles and initialize local repositories.
* Stage, commit, push, and pull files to and from GitHub.
* Create and manage code branch structures using branching strategies.
* Resolve code merge conflicts safely inside terminal editors.
* Raise Pull Requests and follow code review best practices in teams.

---

## Introduction

This module covers Git version control and GitHub team collaboration workflows. We will study core commands (clone, commit, push, pull), branching strategies, pull requests, merge conflict resolutions, and standard code review practices.

---

## Version Control Concepts

### Definition
**Version Control** is a system that records changes to files over time, allowing developers to track modifications, revert to previous states, and collaborate concurrently. *(Files me hone wale updates ka record rakhna aur back tracks load karne ka software system).*

### Key Concepts
* **Local VCS:** Changes saved on a single machine (Fragile).
* **Centralized VCS (CVCS):** Single central server holds history; clients check out files (e.g., SVN). If the server goes down, collaboration stops.
* **Distributed VCS (DVCS):** Every developer has a complete clone of the repository history locally (e.g., Git). Highly secure and fast.

### Visual Explanation
Analogy: Kirana shop security camera log.
```text
  [ Date/Time ] ──► [ User ] ────► [ Action ]
  10:00 AM      ──► Ram       ──► Added Item 'Atta'
  10:05 AM      ──► Shyam     ──► Changed Price of 'Atta'
```
You can rewind the footage to see exactly who did what at any millisecond.

### Topic Summary
Version control tracks file histories, with Distributed systems (like Git) providing local database backups for every developer.

---

## Git Fundamentals

### Definition
**Git** is an open-source, distributed version control system designed to handle speed and data integrity across project files. *(Distributed version control tool jo projects files tracking manage karta hai).*

### Key Concepts
* **Working Directory:** The actual local project folder you edit.
* **Staging Area (Index):** A preparation zone where files are marked to be committed.
* **Local Repository:** The local database where Git saves committed checkpoints.
* **Remote Repository:** The hosted cloud database (like GitHub/GitLab).

### Visual Explanation
The Three States of Git:
```text
  [ Working Directory ] ──► (git add) ──► [ Staging Area ] ──► (git commit) ──► [ Local Repository ]
```

### Syntax
```bash
# Configure user profile globally
git config --global user.name "Your Name"
git config --global user.email "your.email@vjti.ac.in"

# Initialize a new local repository
git init
```

### Example
#### Code
```bash
# Configure profiles and verify
git config --global user.name "Rahul Verma"
git config --list
```
#### Output
```text
user.name=Rahul Verma
```

### Common Mistakes
* **Working without configuring user profiles:** If you do not set your username and email, Git will block commits and throw an configuration warning.

### Topic Summary
Git manages code history in three stages: Working directory, Staging area, and Local repository databases.

---

## Clone

### Definition
**Git Clone** is the command used to download a complete copy of an existing remote repository (including all history, branches, and files) onto your local machine. *(Cloud server se remote project copy local computer par download karna).*

### Important Syntax
```bash
git clone <remote_repository_url>
```

### Example
#### Command
```bash
git clone https://github.com/vjti-qa/playwright-bootcamp.git
```
#### Output
```text
Cloning into 'playwright-bootcamp'...
remote: Enumerating objects: 45, done.
Receiving objects: 100% (45/45), done.
```

### Topic Summary
`git clone` downloads remote projects and sets up active local links to remote repository origins.

---

## Commit

### Definition
**Git Commit** is a saved snapshot checkpoint of staged files logged inside the repository's history database. *(Changes ko permanent record ke roop me local database me save karna).*

### Important Syntax
```bash
git add <filename>       # Stage file
git commit -m "message"  # Commit staged changes
```

### Example
#### Command
```bash
git add package.json
git commit -m "First commit: Add dependencies config file"
```
#### Output
```text
[main (root-commit) a1b2c3d] First commit: Add dependencies config file
 1 file changed, 12 insertions(+)
 create mode 100644 package.json
```

### Common Mistakes
* **Writing meaningless commit messages:** Messages like `git commit -m "fix"` or `git commit -m "changes"` make history tracking useless. Commit messages should explain *why* the change was made in simple words.

### Topic Summary
Commits save staged file snapshots in project history using descriptive log messages.

---

## Push

### Definition
**Git Push** is the command that uploads local repository commits to a remote cloud repository (like GitHub). *(Local computer par save kiye gaye changes ko cloud server (GitHub) par upload karna).*

### Important Syntax
```bash
git push <remote_name> <branch_name>
```

### Example
#### Command
```bash
git push -u origin main
```
#### Output
```text
Enumerating objects: 3, done.
Writing objects: 100% (3/3), done.
To https://github.com/vjti-qa/playwright-bootcamp.git
 * [new branch]      main -> main
```

### Common Mistakes
* **Pushing untracked changes:** You cannot push changes that have not been committed locally first. Stage and commit changes before running `git push`.

### Topic Summary
Push transmits local committed commits to remote branches, syncing remote repository targets.

---

## Pull

### Definition
**Git Pull** is the command that fetches the latest changes from a remote repository and merges them directly into your current local active branch. *(GitHub server se latest updates fetch karke local code me merge karna).*

### Important Syntax
```bash
git pull <remote_name> <branch_name>
```

### Example
#### Command
```bash
git pull origin main
```
#### Output
```text
Updating a1b2c3d..e4f5g6h
Fast-forward
 tests/login.spec.ts | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### Topic Summary
`git pull` fetches remote repository commits and integrates them into active local branches instantly.

---

## Branching Strategy

### Definition
A **Branching Strategy** is a set of rules used by software teams to dictate how code branches are created, named, and merged to maintain production codebase stability. *(Production code ko safe rakhne ke liye git branches create aur merge karne ke rules).*

### Key Concepts
* **Main Branch (`main`/`master`):** Holds stable production-ready code.
* **Development Branch (`dev`):** Integration branch for developer tasks.
* **Feature Branches (`feature/`):** Short-lived branches created to work on individual tickets.
* **Hotfix Branches (`hotfix/`):** Emergency branches created to patch live bugs.

### Visual Explanation
Branch pipelines:
```text
  main (Production)   ────────────────────────────────────────────────────────►
                         \                                            /
  feature/login-tests     └──► [ Develop/Test ] ──► [ Pull Request ] ──┘
```

### Topic Summary
Branching strategies organize feature workflows, separating active updates from stable production branches.

---

## Feature Branch Workflow

### Definition
The **Feature Branch Workflow** is a team collaboration model where all new features or bug fixes are developed in isolated branches before being reviewed and merged into the main line. *(Main branch se feature branch banana, changes save karna, aur testing complete hone par peer review step me merge karna).*

### Important Syntax
```bash
git checkout -b <branch_name> # Create and switch to new branch
# Or modern: git switch -c <branch_name>
```

### Example
#### Command
```bash
git checkout -b feature/vjti-login-tests
```
#### Output
```text
Switched to a new branch 'feature/vjti-login-tests'
```

### Topic Summary
Feature branch workflows isolate feature development inside temporary branch scopes, preventing main production branches from breaking.

---

## Pull Requests

### Definition
A **Pull Request** (PR) is a submission on GitHub that notifies team members that a feature branch is ready to be merged, inviting feedback and code reviews. *(Feature branch code ko reviews and approvals ke liye request dashboard par raise karna).*

### Key Concepts
* **Source branch:** The feature branch containing your changes.
* **Target branch:** The parent branch (e.g. `main`) where code will be merged.
* **Description:** Details what features were added, test outputs, and steps to verify.

### Real World Usage
Raising pull requests on GitHub:
1. Push your branch: `git push origin feature/vjti-login-tests`.
2. Open GitHub repository UI.
3. Click **Compare & pull request** button.
4. Fill review templates and submit.

### Topic Summary
Pull requests act as gatekeepers on remote servers, coordinating code reviews before changes are merged.

---

## Merge Conflicts

### Definition
A **Merge Conflict** is a block event that occurs when two branches make different changes to the same line in a file, and Git cannot automatically determine which version to keep. *(Jab do branches me same line par content change kiya jata hai aur git decision nahi le pata ki kaunsa save karein).*

### Key Concepts
* Conflict markers injected by Git:
  * `<<<<<<< HEAD` (Current local changes).
  * `=======` (Separator).
  * `>>>>>>> branch_name` (Incoming branch changes).
* To resolve: Edit the file, remove the conflict markers, select the desired code state, stage, and commit.

### Visual Explanation
Analogy: Two students writing their names on the same hostel room slot.
```text
  <<<<<<< HEAD
  workers: 2   (QA A changed local config limits)
  =======
  workers: 4   (QA B changed remote configuration)
  >>>>>>> incoming-changes-branch
```
The warden (you) must edit the file, erase the markers, choose either `2` or `4`, and save.

### Example
#### Resolving steps
```bash
# 1. Edit the file to remove markers and select final code.
# 2. Stage resolved file
git add playwright.config.ts
# 3. Commit conflict resolution
git commit -m "Resolve merge conflict in playwright configuration"
```

### Topic Summary
Merge conflicts stop merges, requiring manual code cleanup of marker lines before committing the resolution.

---

## Code Reviews

### Definition
A **Code Review** is a quality assurance process where team members inspect code changes in a Pull Request, looking for bugs, style guide violations, and performance issues before approval. *(Merge se pehle peers dwara code check karke defects and logic logic issues identify karne ka stage).*

### Key Concepts
* **LGTM:** "Looks Good To Me" (Approved).
* **Feedback Loops:** Reviewers leave comments on specific code lines; author pushes updates to the same branch.
* **Checks:** Ensure tests pass, styles match, and no passwords/tokens are committed.

### Topic Summary
Code reviews verify codebase quality and standard compliance through peer feedback before code merges.

---

## Session Summary

### Key Takeaways
1. **DVCS Power:** Git clone creates complete local repository databases, ensuring history backups on every workstation.
2. **Three States:** Understand working directory edits, staging selections (`add`), and commit snapshots.
3. **Branch Isolation:** Short-lived feature branches isolate code changes from main production environments.
4. **Conflict Resolution:** Erase conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`), select the target code, stage, and commit.
5. **Team Governance:** PRs and Code Reviews ensure project code quality before merging.

### Important Interview Points
* **What is the difference between git fetch and git pull?**
  * `git fetch` downloads remote repository history metadata to local references but does not touch local files. `git pull` runs `git fetch` and immediately merges those updates into the current local branch.
* **How do you resolve a merge conflict?**
  * Open the conflicted file, locate the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`), delete the markers, choose which code version to keep, run `git add` to mark it as resolved, and run `git commit` to complete the merge.
* **What is the staging area in Git?**
  * It is an intermediate preparation file index where changes are staged before they are saved in a commit.

### Quick Revision Sheet

| Command | Action | Scope |
| --- | --- | --- |
| `git init` | Initialize local git repository | Local directory only |
| `git clone <url>` | Download remote repository | Downloads history and files |
| `git add <file>` | Stage file updates | Prepares for commit |
| `git commit -m "msg"` | Save staged snapshots | Local repository database |
| `git push origin <br>` | Upload local commits | Cloud remote repository |
| `git pull origin <br>` | Fetch and merge remote updates | Working directory updates |
| `git checkout -b <br>`| Create and switch branch | Changes local context |
