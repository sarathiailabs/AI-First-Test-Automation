# Day 4: Git & GitHub – Trainer Teaching Guide

This guide is designed for instructors delivering the "Git & GitHub" session. It details the lecture scripts, engagement strategies, whiteboard sketches, live terminal commands, and student coaching tips.

---

## Session Opening

### Welcome Script
"Hello everyone! Welcome to Day 4. Today, we are learning the tools of collaboration: Git and GitHub. Up until now, you've written code locally on your own computers. But in the software industry, you never work alone. You work in teams of 5, 50, or 500 engineers. How do we write code simultaneously without overwriting each other's work? Today, we will master local version tracking, branching strategies, code reviews, and resolving conflicts. Let's dive in."

### Session Goal
By the end of today's 2-hour session, you will confidently configure Git profiles, initialize repositories, track changes, branch out features, submit code reviews via Pull Requests, and resolve merge conflicts using terminal editors.

### Motivation
Imagine this: You and another QA engineer are writing tests for the VJTI portal. You edit the file `login.spec.ts` at 10:00 AM on your laptop. At the exact same time, the other engineer edits the same file on their laptop. If you upload your file to a shared folder, you overwrite their changes, and their work is lost. Git solves this problem. It acts as a time-machine and referee, tracking every edit and merging updates safely.

---

## 1. Version Control Concepts

### Trainer Introduction
"Version control is a system that records changes to our codebase over time. Think of it like a security camera in a shop. It logs exactly who came in, what item they moved, and what the shelf looked like before and after. This allows us to track history and revert files back to previous states if we introduce a bug."

### Student Engagement Questions
1. Have you ever made changes to a project folder and saved versions like `project_v1`, `project_v2_final`, `project_v2_final_actual`?
2. What is the risk of keeping all project files on a single shared central server? (If the server crashes, all history is lost).

### Whiteboard Teaching
Draw Distributed vs Centralized models:
```text
  Centralized: [ Server (holds history) ] ◄── checkout ── [ Developer A ]
  
  Distributed: [ Developer A (full history) ] ◄── sync ── [ Developer B (full history) ]
```

### Teaching Flow
1. Define Version Control Systems (VCS).
2. Compare Local, Centralized (SVN), and Distributed (Git) architectures.
3. Highlight that Git gives every developer a complete copy of the project history.

### Transition Script
"Since Git is a Distributed system, let's look at how it operates locally on your machine."

---

## 2. Git Fundamentals

### Trainer Introduction
"To work with Git, we must understand its three local states: the Working Directory (where we edit files), the Staging Area (the index where we choose what changes to save), and the Local Repository (the local database containing committed checkpoints)."

### Student Engagement Questions
1. Why does Git need a staging area? Why can't we just commit changes directly?
2. What hidden folder does Git create to track changes in a directory? (The `.git` folder).

### Whiteboard Teaching
Draw the three states of Git:
```text
  [ Working Directory ] ──► git add ──► [ Staging Area (Index) ] ──► git commit ──► [ Local Repository (.git) ]
```

### Teaching Flow
1. Define the Working Directory, Staging Area, and Local Repository.
2. Review configuration commands (`git config`).
3. Explain `git init` to initialize tracking.

### Live Coding Demonstration
#### Step 1
Create a folder and run:
```bash
git init
```
#### Step 2
Configure user name and email:
```bash
git config user.name "Rahul Verma"
git config user.email "rahul.verma@vjti.ac.in"
```
#### Step 3
Show the hidden `.git` folder using `ls -la` (or equivalent file explorer settings).

### Transition Script
"If a repository already exists on GitHub, we don't initialize it locally. We copy it using the Clone command."

---

## 3. Git Clone

### Trainer Introduction
"To copy a remote repository from GitHub to your local computer, we use `git clone`. This downloads the complete project history, branches, and files, setting up a link to the remote repository automatically."

### Student Engagement Questions
1. What is the difference between downloading a zip file from GitHub vs. running `git clone`? (A zip file only contains the current snapshot of the files; `git clone` downloads the entire repository history and tracking data).
2. What is the default remote name Git configures when you clone a repository? (`origin`).

### Whiteboard Teaching
Draw the cloning process:
```text
  [ GitHub Cloud Server ] ─────────── git clone ───────────► [ Local Machine ]
  (Holds complete history)                                    (Creates .git folder + files)
```

### Teaching Flow
1. Define `git clone`.
2. Review HTTPS vs. SSH URLs.
3. Show how to clone a repository.

### Live Coding Demonstration
#### Step 1
Find a public repository URL on GitHub.
#### Step 2
Run the clone command in your terminal:
```bash
git clone https://github.com/vjti-qa/playwright-bootcamp.git
```
#### Step 3
Navigate into the cloned directory and run `git log` to show the downloaded history.

### Transition Script
"Once we have our local copy, we edit files. To save these modifications locally, we stage and Commit."

---

## 4. Git Commit

### Trainer Introduction
"A commit is a saved checkpoint in your project history. It is like saving your progress in a video game before entering a difficult level. If you make a mistake later, you can restore your files back to this exact checkpoint."

### Student Engagement Questions
1. Why should you write descriptive commit messages instead of messages like "updates"?
2. What command prepares files for a commit? (`git add`).

### Whiteboard Teaching
Draw staging and commit flows:
```text
  File A (Modified)  ──► git add File A ──► [ Staging Area ] ──► git commit -m "feat: add login spec"
  File B (Modified)                         (File A packaged)
```

### Teaching Flow
1. Explain staging files using `git add`.
2. Define the commit command and the `-m` message flag.
3. Discuss best practices for writing clean commit messages.

### Live Coding Demonstration
#### Step 1
Create a mock file `login.spec.ts`. Run `git status` to show it is untracked.
#### Step 2
Run `git add login.spec.ts` and show it is now staged.
#### Step 3
Run `git commit -m "feat: add initial login validation spec"` and show the commit confirmation log.

### Transition Script
"Commits are saved locally. To share them with your team, you must Push them to the remote server."

---

## 5. Git Push

### Trainer Introduction
"To share your local commits with the team, we use `git push`. This uploads your local branch commits to the remote repository on GitHub, syncing the cloud server."

### Student Engagement Questions
1. Can you push files without committing them first? (No, you can only push committed checkpoints).
2. What does `origin` mean in the push command? (It is the alias pointing to the remote repository URL).

### Whiteboard Teaching
Draw push data sync:
```text
  [ Local Repository ] (Commit: a1b2c3d) ──► git push origin main ──► [ GitHub Cloud Repository ]
                                                                      (Main branch synced)
```

### Teaching Flow
1. Explain the push command.
2. Discuss the `-u` upstream flag.

### Live Coding Demonstration
#### Step 1
Run `git push -u origin main` on a mock repository.
#### Step 2
Open GitHub in your browser and show the new commits and files appearing on the repository dashboard page.

### Transition Script
"If other team members upload their commits, your local repository becomes outdated. Let's see how we download their changes using Git Pull."

---

## 6. Git Pull

### Trainer Introduction
"To fetch the latest changes from GitHub and merge them directly into your current local active branch, we use `git pull`. This keeps your local workspace synchronized with the rest of the team's updates."

### Student Engagement Questions
1. What happens if you try to pull updates while you have uncommitted files that conflict with the remote changes? (Git will block the pull and ask you to commit or stash your changes first).
2. What two commands run under the hood when you execute `git pull`? (`git fetch` and `git merge`).

### Whiteboard Teaching
Draw pull pipeline:
```text
  [ GitHub Cloud ] (Has new commits) ──► git pull ──► [ Local Files updated ]
```

### Teaching Flow
1. Define `git pull`.
2. Contrast `git pull` with `git fetch`.

### Live Coding Demonstration
#### Step 1
Run `git pull origin main` in a terminal directory.
#### Step 2
Show the fast-forward update logs on screen, verifying file modifications.

### Transition Script
"To prevent team members from breaking the main production code, we use Branching Strategies."

---

## 7. Branching Strategy

### Trainer Introduction
"A branching strategy is a set of rules software teams follow to organize code changes. We keep the main branch locked and stable. Developers create short-lived feature branches to write and test changes. This ensures that experimental code does not affect production."

### Student Engagement Questions
1. What is the risk of allowing everyone to commit directly to the `main` branch? (Experimental or buggy code can break the stable version for everyone).
2. What branching names have you seen in software projects? (main, dev, feature/, hotfix/).

### Whiteboard Teaching
Draw branch strategy hierarchies:
```text
  main (Production)   ─────────────────────────────────────────────►
                         \                                 /
  dev (Integration)       └──► [ Feature Branches ] ──► PR ─┘
```

### Teaching Flow
1. Explain the purpose of branching strategies.
2. Detail branch naming conventions (`main`, `dev`, `feature/`, `hotfix/`).

### Transition Script
"Let's see how a developer isolates their work using the Feature Branch Workflow."

---

## 8. Feature Branch Workflow

### Trainer Introduction
"In the feature branch workflow, we create a new branch from `main` to work on a specific feature. This isolates our changes, allowing us to edit files and run tests without affecting the master codebase."

### Student Engagement Questions
1. How do you create and switch to a new branch in a single command? (`git checkout -b <branch_name>`).
2. How do you check which branch is currently active? (`git branch`).

### Whiteboard Teaching
Draw checkout branch scopes:
```text
  main branch:        [ commit 1 ] ──► [ commit 2 ]
                                         \
  feature branch:                         └──► [ commit 3 (isolated) ]
```

### Teaching Flow
1. Define the feature branch workflow.
2. Introduce the checkout and switch branch commands.
3. Show how to verify active branches.

### Live Coding Demonstration
#### Step 1
Run `git branch` to show that `main` is active.
#### Step 2
Create and switch to a new branch:
```bash
git checkout -b feature/vjti-login-tests
```
#### Step 3
Run `git branch` again to verify that the active branch has updated.

### Transition Script
"Once feature development is complete on your branch, you request reviews using a Pull Request."

---

## 9. Pull Requests (PRs)

### Trainer Introduction
"A Pull Request is a request on GitHub to merge your feature branch changes into the main branch. It notifies team members that your code is ready for review, allowing them to inspect the diff, leave comments, and approve the changes before they are merged."

### Student Engagement Questions
1. What details should you write in a Pull Request description? (The goal of the changes, verification commands, and execution logs).
2. Why are pull requests used as gateways for test automation pipelines? (They run tests automatically in CI environments to verify changes before they are merged).

### Teaching Flow
1. Define Pull Requests (PRs).
2. Detail PR description templates.
3. Explain how PRs facilitate code reviews.

### Live Coding Demonstration
#### Step 1
Push the feature branch to GitHub:
```bash
git push -u origin feature/vjti-login-tests
```
#### Step 2
Open GitHub in your browser. Point out the **Compare & pull request** banner.
#### Step 3
Click the button, fill out the review template, and click **Create pull request**.

### Transition Script
"If two developers modify the same line of code, Git cannot merge the branches automatically. This triggers a Merge Conflict."

---

## 10. Merge Conflicts

### Trainer Introduction
"A merge conflict occurs when two branches make different changes to the same line of code in the same file, and Git cannot automatically decide which version to keep. Merging stops, conflict markers are inserted, and manual resolution is required."

### Student Engagement Questions
1. What characters does Git use to mark conflict boundaries? (`<<<<<<<`, `=======`, `>>>>>>>`).
2. Who is responsible for resolving a merge conflict? (The developer who is performing the merge).

### Whiteboard Teaching
Explain conflict markers layout:
```text
  <<<<<<< HEAD
  workers: 2      <── Local branch code
  =======
  workers: 4      <── Incoming branch code
  >>>>>>> feature/parallel-runs
```
The developer must delete the markers, select the desired code (e.g. `workers: 4`), stage the file, and commit.

### Teaching Flow
1. Define merge conflicts.
2. Review conflict markers.
3. Detail conflict resolution steps (edit, add, commit).

### Live Coding Demonstration
#### Step 1
Open a mock file containing conflict markers.
#### Step 2
Erase the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`) and keep the final configuration value:
```typescript
workers: 4
```
#### Step 3
Save the file. Stage the resolution in your terminal:
```bash
git add playwright.config.ts
```
#### Step 4
Commit the resolution to complete the merge:
```bash
git commit -m "Resolve merge conflict in playwright configuration: kept parallel worker limit"
```

### Transition Script
"To ensure merge conflicts and code quality issues are caught early, team members conduct peer Code Reviews."

---

## 11. Code Reviews & Best Practices

### Trainer Introduction
"A code review is the process where team members inspect code changes in a Pull Request before approval. We look for bugs, styling issues, and verify that automated tests pass. Following best practices (like committing often, writing small PRs, and reviewing logs) keeps the project codebase clean."

### Student Engagement Questions
1. What does 'LGTM' mean in code reviews? (Looks Good To Me).
2. Why should you avoid raising massive pull requests containing thousands of lines of changes? (They are difficult and time-consuming to review, making bugs harder to spot).

### Teaching Flow
1. Define code reviews.
2. Outline review best practices (small PRs, frequent commits, descriptive messages).
3. Review code safety checks.

### Topic Recap
* **Best Practice:** Keep PR changes small and verify that automated tests pass before approving merges.

---

## Session Closing

### Session Summary
"Today we covered:
1. Version Control: Local, Centralized, and Distributed systems.
2. Git Stages: Working directory, staging area, and local repository.
3. Core Commands: init, clone, add, commit, push, pull.
4. Collaboration: Branching strategies, feature branch workflows, and Pull Requests.
5. Conflict resolution: Identifying conflict markers, editing files, staging, and committing.
6. Code reviews: Peer feedback loops."

### Knowledge Check Questions
1. What is the difference between Git and GitHub? (Git is the local version control software; GitHub is the cloud-based repository hosting service).
2. What does `git pull` do? (Fetches remote changes and merges them into the current local branch).
3. Why do we need the staging area? (To select exactly which changes to package in the next commit).
4. What do the conflict markers `<<<<<<<` and `>>>>>>>` enclose? (The conflicting lines of code from the local branch and the incoming branch).
5. Where do you configure your Git username and email? (Using `git config` commands).

### Assignment Introduction
"To practice these concepts, open `02_Assignments.md`. You will initialize a local repository, create a feature branch, push changes to GitHub, and simulate and resolve a merge conflict in a configuration file. These exercises prepare you for real-world development workflows."

### Homework Guidance
* Complete the three assignments.
* Create a free GitHub account if you don't have one.
* Create a test repository on GitHub and practice pushing and pulling files from the command line.

### Next Session Preview
"In our next session, we will start UI Automation. We will write scripts using Playwright to open browsers, locate elements in the DOM tree, and perform basic actions."
