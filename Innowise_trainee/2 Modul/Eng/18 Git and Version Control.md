# Git and Version Control

## Table of Contents

- [[#What Version Control Is]]
- [[#Git Basics]]
- [[#Branching and Merging]]
- [[#Remote Repositories]]
- [[#Git in AQA]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## What Version Control Is

**Version control** is a system that tracks changes in files. It helps a team work on the same project without losing history.

**Goal:** keep project history, compare changes, return to older versions, and work safely in branches.

Common systems:

- `Git` - the most common distributed version control system
- `TFS` / Azure DevOps - older Microsoft ecosystem and project platform
- `SVN` - older centralized version control system

---

## Git Basics

Git stores changes as commits.

| Term | Meaning |
|---|---|
| repository | project folder tracked by Git |
| working tree | current files on disk |
| staging area | place where changes are prepared for commit |
| commit | saved snapshot with a message |
| branch | movable line of development |

Common commands:

```bash
git status
git add file.md
git commit -m "Add test cases"
git log --oneline
git diff
```

**Example:** before changing an automation framework, check `git status`, create a branch, commit small logical changes, and open a pull request.

---

## Branching and Merging

A **branch** lets you work on a change without disturbing the main code line.

Common branch names:

- `feature/login-tests`
- `fix/flaky-wait`
- `chore/update-dependencies`

```bash
git switch -c feature/login-tests
git switch main
git merge feature/login-tests
```

**Advantages:**
- safer work on features and fixes
- easier code review
- clear history for each task

**Disadvantages:**
- merge conflicts can happen
- long-lived branches become hard to merge

> [!caution] Keep branches short-lived
> A branch that lives for weeks often becomes painful to merge. Pull changes from the main branch often.

---

## Remote Repositories

A **remote repository** is a shared copy of the project, usually on GitHub, GitLab, Bitbucket, or Azure DevOps.

Common commands:

```bash
git clone https://example.com/project.git
git pull
git push
```

A **pull request** or **merge request** is a review step before code is merged into the main branch.

---

## Git in AQA

An AQA engineer uses Git to:

- store test automation code
- review test changes
- track framework updates
- connect changes to tasks or bugs
- trigger CI pipelines after a push or pull request

Good practices:

- commit one logical change at a time
- write clear commit messages
- do not commit secrets, tokens, or passwords
- do not commit generated reports unless the team decided to store them
- review diffs before pushing

---

## Interview Questions

### Beginner Questions

**1. What is version control?**
Version control tracks file changes and keeps project history.

**2. What is a commit?**
A commit is a saved snapshot of changes with a message.

### Intermediate Questions

**3. Why do teams use branches?**
Branches let people work on separate tasks safely and review changes before merging.

**4. What is a pull request?**
A pull request is a request to review and merge changes from one branch into another.

### Advanced Questions

**5. Why should QA engineers avoid committing secrets?**
Secrets can leak access to systems. Tokens and passwords should stay in environment variables or secret managers.

### Code Questions

**6. What does this command show?**

```bash
git diff
```

It shows unstaged changes in tracked files.
