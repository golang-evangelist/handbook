# Git Command Reference

A complete practical **Git Command Reference** for **Developers, Software Engineers, and DevOps Engineers**.

This repository is a structured reference of Git commands, organized into **35 thematic chapters**. Each chapter is contained in a separate `*.md` file and can be read independently or as part of the complete Git guide.

---

## Table of Contents

* [About the Project](#about-the-project)
* [Documentation Goals](#documentation-goals)
* [Who This Documentation Is For](#who-this-documentation-is-for)
* [How the Documentation Is Organized](#how-the-documentation-is-organized)
* [Command Format](#command-format)
* [35 Chapters](#35-chapters)
* [Recommended Learning Order](#recommended-learning-order)
* [Quick Navigation](#quick-navigation)
* [Most Important Git Areas](#most-important-git-areas)
* [Developer Workflow](#developer-workflow)
* [DevOps Workflow](#devops-workflow)
* [Safe Git Usage](#safe-git-usage)
* [Dangerous Commands](#dangerous-commands)
* [Git Aliases](#git-aliases)
* [How to Use This Repository as a Reference](#how-to-use-this-repository-as-a-reference)
* [Commands Worth Memorizing](#commands-worth-memorizing)
* [Complete Project Structure](#complete-project-structure)

---

# About the Project

This repository is designed as a practical **Git Command Reference**, rather than a traditional Git tutorial.

The goal is to bring together, in one place, the Git commands required during everyday work on software projects:

* creating repositories;
* Git configuration;
* working with files;
* staging;
* committing;
* reviewing changes;
* branch management;
* merging;
* rebasing;
* remote repositories;
* pull/push;
* stash;
* tags and releases;
* history searching;
* conflict resolution;
* cherry-pick;
* recovery;
* bisect;
* submodules;
* worktrees;
* Git LFS;
* hooks;
* repository maintenance;
* Git internals;
* sparse checkout;
* archives;
* automation;
* CI/CD;
* DevOps workflows;
* aliases.

The documentation is structured so that it can be used as:

> **Learning Guide + Daily Cheat Sheet + Command Reference + DevOps Reference**

---

# Documentation Goals

Git is much more than a few commands such as:

```bash
git add
git commit
git push
git pull
```

In a real development environment, it is also necessary to understand:

* how Git stores history;
* how branches work;
* how history can be modified;
* how conflicts are resolved;
* how lost commits can be found;
* how changes can be analyzed;
* how remote repositories are used;
* how Git works inside CI/CD systems;
* how repositories are maintained;
* how Git objects and internals work;
* how repositories can be automated.

For this reason, the documentation is divided into 35 areas that together cover a large part of practical Git workflows.

---

# Who This Documentation Is For

## Developers

The documentation covers everyday operations:

```text
clone
status
add
commit
diff
branch
switch
merge
rebase
pull
push
stash
tag
log
```

The following chapters are particularly useful:

* `03-repository-status-and-information.md`
* `04-staging-and-committing.md`
* `05-diff-and-code-review.md`
* `06-branching.md`
* `07-merging.md`
* `08-rebasing.md`
* `09-remote-repositories.md`
* `10-undoing-changes.md`
* `11-stash.md`

---

## Software Engineers

For more advanced development workflows, the following chapters are especially useful:

* history analysis;
* branch comparison;
* conflict resolution;
* cherry-pick;
* reflog;
* bisect;
* worktrees;
* repository internals;
* automation;
* aliases.

Recommended chapters:

```text
13-searching-git-history.md
14-comparing-branches-and-commits.md
15-conflict-resolution.md
16-cherry-pick.md
17-reflog-and-recovery.md
18-git-bisect.md
20-worktrees.md
25-git-objects-internals.md
30-environment-and-automation.md
35-practical-git-aliases.md
```

---

## DevOps Engineers

For DevOps and CI/CD workflows, the most important chapters cover:

* automation;
* CI;
* repository diagnostics;
* Git internals;
* hooks;
* tags/releases;
* remote repository management;
* cleanup;
* maintenance;
* deterministic repository state.

Especially:

```text
09-remote-repositories.md
12-tags-and-releases.md
21-git-lfs.md
22-git-hooks.md
23-repository-maintenance.md
24-repository-diagnostics.md
25-git-objects-internals.md
30-environment-and-automation.md
31-common-devops-ci-commands.md
32-common-developer-workflows.md
```

---

# How the Documentation Is Organized

Each area is contained in a separate Markdown file.

The naming convention is:

```text
NN-topic-name.md
```

where:

* `NN` — chapter number;
* `topic-name` — the area name converted to lowercase kebab-case.

For example:

```text
01-configuration.md
03-repository-status-and-information.md
04-staging-and-committing.md
25-git-objects-internals.md
31-common-devops-ci-commands.md
```

The chapter numbers ensure that the documentation naturally sorts in the recommended order.

---

# Command Format

Commands are presented in the following standardized format:

| Command | Description | Example | Branch State Before and After command | Output |
| ------- | ----------- | ------- | ------------------------------------- | ------ |

Each row represents a specific Git operation.

## Command

Contains the Git command.

Example:

```bash
git status
```

## Description

Explains what the command does and why it is used.

## Example

Shows a real-world usage example.

## Branch State Before and After command

Describes the state of the branch before and after executing the command.

This is especially important for operations such as:

```text
merge
rebase
reset
cherry-pick
revert
pull
push
```

## Output

Shows a typical command result.

Output may depend on:

* Git version;
* repository;
* current branch;
* configuration;
* remote repository;
* operating system.

Therefore, output should be treated as a **representative example**, rather than a guaranteed identical result.

---

# 35 Chapters

## 01 — Configuration

**File:**

[`01-configuration.md`](01-configuration.md)

Covers Git configuration:

```text
git config
```

Includes:

* user configuration;
* global configuration;
* local configuration;
* aliases;
* default settings;
* configuration inspection.

---

## 02 — Creating Repositories

**File:**

[`02-creating-repositories.md`](02-creating-repositories.md)

Covers repository creation and initialization:

```text
git init
git clone
```

---

## 03 — Repository Status & Information

**File:**

[`03-repository-status-and-information.md`](03-repository-status-and-information.md)

Covers repository inspection:

```text
git status
git branch
git remote
git rev-parse
git show
```

---

## 04 — Staging & Committing

**File:**

[`04-staging-and-committing.md`](04-staging-and-committing.md)

The basic Git development cycle:

```text
working tree
    ↓
staging area
    ↓
commit
```

Covers:

```text
git add
git commit
```

---

## 05 — Diff & Code Review

**File:**

[`05-diff-and-code-review.md`](05-diff-and-code-review.md)

Covers change analysis:

```text
git diff
git diff --cached
git show
```

This chapter is especially important for code review and checking content before committing.

---

## 06 — Branching

**File:**

[`06-branching.md`](06-branching.md)

Covers:

```text
git branch
git switch
git checkout
```

and the branch lifecycle.

---

## 07 — Merging

**File:**

[`07-merging.md`](07-merging.md)

Covers branch integration:

```text
git merge
```

including:

* fast-forward;
* three-way merge;
* merge conflicts;
* merge strategies.

---

## 08 — Rebasing

**File:**

[`08-rebasing.md`](08-rebasing.md)

Covers:

```text
git rebase
git rebase -i
```

as well as:

* history rewriting;
* interactive rebase;
* squash;
* fixup;
* reordering commits;
* conflict handling.

---

## 09 — Remote Repositories

**File:**

[`09-remote-repositories.md`](09-remote-repositories.md)

Covers working with remote repositories:

```text
git remote
git fetch
git pull
git push
```

---

## 10 — Undoing Changes

**File:**

[`10-undoing-changes.md`](10-undoing-changes.md)

One of the most important chapters for everyday work.

Covers:

```text
git restore
git reset
git revert
```

and the difference between:

```text
working tree
staging area
repository history
```

---

## 11 — Stash

**File:**

[`11-stash.md`](11-stash.md)

Covers temporarily saving work:

```text
git stash
git stash push
git stash pop
git stash apply
git stash list
```

---

## 12 — Tags & Releases

**File:**

[`12-tags-and-releases.md`](12-tags-and-releases.md)

Covers:

```text
git tag
```

and Git tag workflows for release management.

---

## 13 — Searching Git History

**File:**

[`13-searching-git-history.md`](13-searching-git-history.md)

Covers history searching using:

```text
git log
git grep
git log -S
git log -G
```

---

## 14 — Comparing Branches & Commits

**File:**

[`14-comparing-branches-and-commits.md`](14-comparing-branches-and-commits.md)

Covers comparisons between:

```text
branch ↔ branch
commit ↔ commit
local ↔ remote
```

using `git diff`, `git log`, and related commands.

---

## 15 — Conflict Resolution

**File:**

[`15-conflict-resolution.md`](15-conflict-resolution.md)

Covers Git conflicts that occur during:

```text
merge
rebase
cherry-pick
```

and the process of resolving them.

---

## 16 — Cherry-Pick

**File:**

[`16-cherry-pick.md`](16-cherry-pick.md)

Covers:

```bash
git cherry-pick
```

and selectively transferring commits between branches.

---

## 17 — Reflog & Recovery

**File:**

[`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)

One of the most important chapters for recovery.

Covers:

```bash
git reflog
```

as well as recovery after:

* reset;
* rebase;
* accidental branch switching;
* lost commits;
* other history-rewriting operations.

---

## 18 — Git Bisect

**File:**

[`18-git-bisect.md`](18-git-bisect.md)

Covers finding the commit that introduced a bug:

```text
good commit
     ↓
bad commit
     ↓
binary search
     ↓
first bad commit
```

Main command:

```bash
git bisect
```

---

## 19 — Submodules

**File:**

[`19-submodules.md`](19-submodules.md)

Covers Git submodules:

```bash
git submodule
```

including:

* initialization;
* update;
* synchronization;
* recursive operations.

---

## 20 — Worktrees

**File:**

[`20-worktrees.md`](20-worktrees.md)

Covers:

```bash
git worktree
```

Worktrees allow you to work with multiple branches of the same repository in different directories.

---

## 21 — Git LFS

**File:**

[`21-git-lfs.md`](21-git-lfs.md)

Covers Git Large File Storage.

It is used for large binary files that are not ideal for standard Git object storage.

---

## 22 — Git Hooks

**File:**

[`22-git-hooks.md`](22-git-hooks.md)

Covers Git hooks and Git-event automation.

Examples:

```text
pre-commit
commit-msg
pre-push
post-merge
```

---

## 23 — Repository Maintenance

**File:**

[`23-repository-maintenance.md`](23-repository-maintenance.md)

Covers repository maintenance:

```text
git gc
git maintenance
git prune
git repack
```

and related operations.

---

## 24 — Repository Diagnostics

**File:**

[`24-repository-diagnostics.md`](24-repository-diagnostics.md)

Covers troubleshooting and diagnostics:

```text
git fsck
git status
git count-objects
git rev-parse
```

---

## 25 — Git Objects / Internals

**File:**

[`25-git-objects-internals.md`](25-git-objects-internals.md)

Covers Git internals:

```text
blob
tree
commit
tag
HEAD
refs
objects
```

as well as:

```text
.git/
```

repository structure.

---

## 26 — Ignoring Files

**File:**

[`26-ignoring-files.md`](26-ignoring-files.md)

Covers:

```text
.gitignore
.git/info/exclude
global gitignore
```

and file ignore rules.

---

## 27 — File Tracking

**File:**

[`27-file-tracking.md`](27-file-tracking.md)

Covers the file lifecycle:

```text
untracked
    ↓
staged
    ↓
committed
```

as well as:

```text
modified
deleted
renamed
copied
```

---

## 28 — Sparse Checkout

**File:**

[`28-sparse-checkout.md`](28-sparse-checkout.md)

Covers selectively checking out part of a repository:

```bash
git sparse-checkout
```

This is especially useful for large repositories.

---

## 29 — Git Archives

**File:**

[`29-git-archives.md`](29-git-archives.md)

Covers:

```bash
git archive
```

for creating source distributions from Git history.

---

## 30 — Environment & Automation

**File:**

[`30-environment-and-automation.md`](30-environment-and-automation.md)

Covers Git in automation environments:

* environment variables;
* scripting;
* automation;
* non-interactive usage;
* machine-readable output.

---

## 31 — Common DevOps / CI Commands

**File:**

[`31-common-devops-ci-commands.md`](31-common-devops-ci-commands.md)

Covers Git commands useful in:

```text
CI
CD
build systems
deployment pipelines
automation
```

---

## 32 — Common Developer Workflows

**File:**

[`32-common-developer-workflows.md`](32-common-developer-workflows.md)

Covers complete practical workflows.

For example:

```text
feature development
bug fixing
code review
release workflow
hotfix workflow
branch synchronization
```

---

## 33 — High-Value Commands to Memorize

**File:**

[`33-high-value-commands-to-memorize.md`](33-high-value-commands-to-memorize.md)

A compact list of Git commands with the highest practical value.

This chapter is intended for quick learning and memorization.

---

## 34 — Dangerous Commands

**File:**

[`34-dangerous-commands.md`](34-dangerous-commands.md)

Specifically covers commands that can:

* delete data;
* rewrite history;
* overwrite remote history;
* remove local changes.

Examples:

```text
git reset --hard
git clean -fd
git clean -fdx
git push --force
git rebase
```

---

## 35 — Practical Git Aliases

**File:**

[`35-practical-git-aliases.md`](35-practical-git-aliases.md)

Covers practical Git aliases for:

* everyday development;
* history inspection;
* branch management;
* staging;
* commits;
* remote operations;
* stash;
* recovery;
* DevOps;
* CI.

Example:

```bash
git config --global alias.st 'status -sb'
```

After that:

```bash
git st
```

---

# Recommended Learning Order

If you are learning Git from scratch, the recommended order is:

```text
01 Configuration
        ↓
02 Creating Repositories
        ↓
03 Repository Status & Information
        ↓
04 Staging & Committing
        ↓
05 Diff & Code Review
        ↓
06 Branching
        ↓
07 Merging
        ↓
08 Rebasing
        ↓
09 Remote Repositories
        ↓
10 Undoing Changes
        ↓
11 Stash
        ↓
12 Tags & Releases
```

After mastering the fundamentals:

```text
13 Searching Git History
14 Comparing Branches & Commits
15 Conflict Resolution
16 Cherry-Pick
17 Reflog & Recovery
18 Git Bisect
```

For advanced Git:

```text
19 Submodules
20 Worktrees
21 Git LFS
22 Git Hooks
23 Repository Maintenance
24 Repository Diagnostics
25 Git Objects / Internals
```

For automation and DevOps:

```text
26 Ignoring Files
27 File Tracking
28 Sparse Checkout
29 Git Archives
30 Environment & Automation
31 Common DevOps / CI Commands
32 Common Developer Workflows
```

For consolidating your knowledge:

```text
33 High-Value Commands to Memorize
34 Dangerous Commands
35 Practical Git Aliases
```

---

# Quick Navigation

## Basic Git

* [Configuration](docs/01-configuration.md)
* [Creating Repositories](docs/02-creating-repositories.md)
* [Repository Status & Information](docs/03-repository-status-and-information.md)
* [Staging & Committing](docs/04-staging-and-committing.md)
* [Diff & Code Review](docs/05-diff-and-code-review.md)

## Branching & History

* [Branching](docs/06-branching.md)
* [Merging](docs/07-merging.md)
* [Rebasing](docs/08-rebasing.md)
* [Searching Git History](docs/13-searching-git-history.md)
* [Comparing Branches & Commits](docs/14-comparing-branches-and-commits.md)

## Remote Workflow

* [Remote Repositories](docs/09-remote-repositories.md)
* [Tags & Releases](docs/12-tags-and-releases.md)
* [Cherry-Pick](docs/16-cherry-pick.md)

## Recovery

* [Undoing Changes](docs/10-undoing-changes.md)
* [Stash](docs/11-stash.md)
* [Conflict Resolution](docs/15-conflict-resolution.md)
* [Reflog & Recovery](docs/17-reflog-and-recovery.md)
* [Git Bisect](docs/18-git-bisect.md)

## Advanced Git

* [Submodules](docs/19-submodules.md)
* [Worktrees](docs/20-worktrees.md)
* [Git LFS](docs/21-git-lfs.md)
* [Git Hooks](docs/22-git-hooks.md)
* [Repository Maintenance](docs/23-repository-maintenance.md)
* [Repository Diagnostics](docs/24-repository-diagnostics.md)
* [Git Objects / Internals](docs/25-git-objects-internals.md)

## Repository Management

* [Ignoring Files](docs/26-ignoring-files.md)
* [File Tracking](docs/27-file-tracking.md)
* [Sparse Checkout](docs/28-sparse-checkout.md)
* [Git Archives](docs/29-git-archives.md)

## Automation / DevOps

* [Environment & Automation](docs/30-environment-and-automation.md)
* [Common DevOps / CI Commands](docs/31-common-devops-ci-commands.md)
* [Common Developer Workflows](docs/32-common-developer-workflows.md)

## Quick Reference

* [High-Value Commands to Memorize](docs/33-high-value-commands-to-memorize.md)
* [Dangerous Commands](docs/34-dangerous-commands.md)
* [Practical Git Aliases](docs/35-practical-git-aliases.md)

---

# Most Important Git Areas

If you do not want to read all 35 chapters immediately, the following areas represent the essential minimum for everyday development:

```text
03 Repository Status & Information
04 Staging & Committing
05 Diff & Code Review
06 Branching
07 Merging
08 Rebasing
09 Remote Repositories
10 Undoing Changes
15 Conflict Resolution
17 Reflog & Recovery
32 Common Developer Workflows
33 High-Value Commands to Memorize
34 Dangerous Commands
```

---

# Developer Workflow

A typical feature workflow looks like this:

```text
Clone / existing repository
        ↓
Check status
        ↓
Create branch
        ↓
Modify files
        ↓
Review diff
        ↓
Stage changes
        ↓
Commit
        ↓
Fetch remote changes
        ↓
Rebase / merge
        ↓
Resolve conflicts if necessary
        ↓
Push branch
        ↓
Code review
        ↓
Merge
```

Example basic workflow:

```bash
git status

git switch -c feature/login

git status

git diff

git add --all

git diff --cached

git commit -m "Add login feature"

git fetch origin

git rebase origin/main

git push -u origin feature/login
```

The exact strategy may depend on the rules of the specific project.

---

# DevOps Workflow

In DevOps environments, Git is often used as part of an automated pipeline:

```text
Repository
    ↓
Checkout
    ↓
Fetch
    ↓
Verify commit
    ↓
Build
    ↓
Test
    ↓
Package
    ↓
Deploy
```

Typical commands include:

```bash
git clone
git fetch
git checkout / git switch
git rev-parse HEAD
git describe
git diff
git status --porcelain
git tag
```

For CI systems, it is especially important to use predictable and machine-readable output.

---

# Safe Git Usage

Git enables very powerful operations.

Therefore, it is important to distinguish between:

```text
read-only operations
```

and:

```text
history-changing operations
```

and:

```text
destructive operations
```

## Relatively Safe Operations

Examples:

```bash
git status
git log
git diff
git show
git branch
git remote -v
git fetch
```

## Operations That Modify Local History

Examples:

```bash
git commit --amend
git reset
git rebase
git cherry-pick
```

## Operations That Can Modify or Delete Data

Examples:

```bash
git reset --hard
git clean -fd
git clean -fdx
git push --force
```

Before executing a destructive command, check:

```bash
git status
git branch
git log
git reflog
```

---

# Dangerous Commands

Chapter:

[`34-dangerous-commands.md`](34-dangerous-commands.md)

should be used as a safety reference before executing commands that modify Git history or delete data.

Pay particular attention to:

```bash
git reset --hard
git clean -fd
git clean -fdx
git push --force
git push --force-with-lease
git rebase
```

An important rule:

> **Do not use a destructive Git command if you are not sure what will be deleted or which history will be modified.**

---

# Git Aliases

Chapter:

[`35-practical-git-aliases.md`](35-practical-git-aliases.md)

contains practical aliases for everyday work.

Example:

```bash
git config --global alias.st 'status -sb'
```

After that:

```bash
git st
```

becomes a shorter form of:

```bash
git status -sb
```

Useful aliases may include:

```text
git st
git lg
git br
git current
git aa
git ap
git dc
git fa
git plr
git psu
git sl
git ref
git conflicts
git root
```

Aliases should be used in a way that increases productivity without hiding destructive operations behind unclear names.

---

# How to Use This Repository as a Reference

There are three recommended ways to use this repository.

## 1. Linear Learning

Read:

```text
01 → 02 → 03 → ... → 35
```

This approach is best for systematic Git learning.

---

## 2. Problem-Driven Learning

If you have a specific problem, open the corresponding chapter directly.

For example:

### "I accidentally performed a reset."

Open:

[`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)

### "I have a merge conflict."

Open:

[`15-conflict-resolution.md`](15-conflict-resolution.md)

### "I want to find the commit that introduced a bug."

Open:

[`18-git-bisect.md`](18-git-bisect.md)

### "I want to see what I changed."

Open:

[`05-diff-and-code-review.md`](05-diff-and-code-review.md)

### "I want to work with multiple branches in parallel."

Open:

[`20-worktrees.md`](20-worktrees.md)

---

## 3. Quick Reference

For everyday work, it is often enough to use:

```text
03
04
05
06
07
08
09
10
33
34
35
```

---

# Commands Worth Memorizing

## Repository

```bash
git status
git log
git show
git diff
```

## Branches

```bash
git branch
git switch
git switch -c
```

## Staging

```bash
git add
git add --patch
git restore --staged
```

## Commit

```bash
git commit
git commit --amend
```

## Remote

```bash
git fetch
git pull
git push
```

## Integration

```bash
git merge
git rebase
git cherry-pick
```

## Recovery

```bash
git restore
git reset
git revert
git reflog
```

## Search

```bash
git log --grep
git log -S
git log -G
git grep
```

## Debugging

```bash
git bisect
git reflog
git fsck
```

---

# Complete Project Structure

The repository should have the following structure:

```text
.
├── README.md
│
├── 01-configuration.md
├── 02-creating-repositories.md
├── 03-repository-status-and-information.md
├── 04-staging-and-committing.md
├── 05-diff-and-code-review.md
├── 06-branching.md
├── 07-merging.md
├── 08-rebasing.md
├── 09-remote-repositories.md
├── 10-undoing-changes.md
├── 11-stash.md
├── 12-tags-and-releases.md
├── 13-searching-git-history.md
├── 14-comparing-branches-and-commits.md
├── 15-conflict-resolution.md
├── 16-cherry-pick.md
├── 17-reflog-and-recovery.md
├── 18-git-bisect.md
├── 19-submodules.md
├── 20-worktrees.md
├── 21-git-lfs.md
├── 22-git-hooks.md
├── 23-repository-maintenance.md
├── 24-repository-diagnostics.md
├── 25-git-objects-internals.md
├── 26-ignoring-files.md
├── 27-file-tracking.md
├── 28-sparse-checkout.md
├── 29-git-archives.md
├── 30-environment-and-automation.md
├── 31-common-devops-ci-commands.md
├── 32-common-developer-workflows.md
├── 33-high-value-commands-to-memorize.md
├── 34-dangerous-commands.md
└── 35-practical-git-aliases.md
```

---

# Organization Concept

The entire documentation can be viewed through several levels.

```text
                    Git
                     │
       ┌─────────────┼─────────────┐
       │             │             │
    Basics        History       Collaboration
       │             │             │
       ↓             ↓             ↓
   01–05          13–18          06–12
       │             │             │
       └─────────────┼─────────────┘
                     │
                 Advanced
                     │
                  19–29
                     │
                     ↓
              Automation / DevOps
                  30–32
                     │
                     ↓
              Quick Reference
                  33–35
```

---

# What This Documentation Is Not

This repository is not intended to be a complete replacement for the official Git documentation.

Git has a large number of advanced options, configuration parameters, plumbing commands, and specific edge-case scenarios that cannot all be covered in a single practical reference.

This repository focuses on:

> **Practical commands and workflows that provide high value in real-world development and DevOps work.**

For detailed behavior of individual options, always check the documentation for your Git version and `git help`:

```bash
git help <command>
```

or:

```bash
git <command> --help
```

---

# Git Mental Model

The most important concept for understanding Git is distinguishing between three fundamental levels:

```text
Working Tree
     │
     │ git add
     ↓
Staging Area
     │
     │ git commit
     ↓
Repository
```

A remote repository is an additional layer:

```text
Local Repository
       │
       │ git push
       ↓
Remote Repository
```

And `git fetch` transfers information from the remote without automatically changing the current working tree:

```text
Remote Repository
       │
       │ git fetch
       ↓
Local Repository / Remote-tracking refs
```

Understanding this model makes a large number of Git commands much easier to understand.

---

# Branch Mental Model

A branch is not a separate folder containing a copy of the project.

A branch is essentially a reference pointing to a commit.

Simplified:

```text
main
  │
  ▼
A ─── B ─── C
```

When we create a new branch:

```text
             feature
                │
                ▼
A ─── B ─── C
```

A new commit on the feature branch:

```text
             feature
                │
                ▼
A ─── B ─── C ─── D
          │
         main
```

This mental model is essential for understanding:

```text
branch
merge
rebase
reset
reflog
cherry-pick
```

---

# History Rewriting

Some Git commands modify history:

```text
rebase
reset
commit --amend
cherry-pick
```

It is especially important to distinguish:

```text
history-preserving operations
```

from:

```text
history-rewriting operations
```

For example:

```bash
git revert
```

adds a new commit that reverses the effect of a previous commit.

In contrast:

```bash
git reset
```

moves references and can remove commits from the current branch history.

This distinction is covered in greater detail in the relevant chapters.

---

# Final Workflow Reference

For everyday development:

```bash
git status
git switch -c feature/name
git diff
git add --all
git diff --cached
git commit -m "Description"
git fetch origin
git rebase origin/main
git push -u origin feature/name
```

For inspection:

```bash
git status
git log --oneline --graph --decorate --all
git diff
git diff --cached
git branch -vv
```

For recovery:

```bash
git reflog
git status
git log
git branch
```

For debugging:

```bash
git bisect start
git bisect bad
git bisect good <commit>
```

For resolving conflicts:

```bash
git status
git diff
git add <resolved-file>
git rebase --continue
```

or:

```bash
git add <resolved-file>
git commit
```

depending on the operation that caused the conflict.

---

# Conclusion

This repository represents a complete practical journey through Git:

```text
Configuration
      ↓
Repository Creation
      ↓
Working Tree
      ↓
Staging
      ↓
Commits
      ↓
Branches
      ↓
Merge / Rebase
      ↓
Remote Repositories
      ↓
History Analysis
      ↓
Recovery
      ↓
Advanced Git
      ↓
Automation
      ↓
DevOps / CI
      ↓
Workflows
      ↓
Quick Reference
      ↓
Aliases
```

If you use Git every day, you do not need to memorize all 35 chapters.

The goal is to:

1. understand the Git mental model;
2. know which commands exist;
3. know when to use each command;
4. understand the consequences of a command;
5. know how to inspect repository state;
6. know how to recover from mistakes;
7. know how Git works in development and DevOps workflows.

For everyday work, first master:

```text
03 → 04 → 05 → 06 → 07 → 08 → 09 → 10
```

Then:

```text
15 → 17 → 18
```

Then:

```text
19 → 20 → 21 → 22 → 23 → 24 → 25
```

For DevOps and CI/CD:

```text
30 → 31 → 32
```

For quick revision:

```text
33 → 34 → 35
```

---

# Complete Navigation

|  # | Area                            | File                                                                                 |
| -: | ------------------------------- | ------------------------------------------------------------------------------------ |
| 01 | Configuration                   | [`01-configuration.md`](01-configuration.md)                                         |
| 02 | Creating Repositories           | [`02-creating-repositories.md`](02-creating-repositories.md)                         |
| 03 | Repository Status & Information | [`03-repository-status-and-information.md`](03-repository-status-and-information.md) |
| 04 | Staging & Committing            | [`04-staging-and-committing.md`](04-staging-and-committing.md)                       |
| 05 | Diff & Code Review              | [`05-diff-and-code-review.md`](05-diff-and-code-review.md)                           |
| 06 | Branching                       | [`06-branching.md`](06-branching.md)                                                 |
| 07 | Merging                         | [`07-merging.md`](07-merging.md)                                                     |
| 08 | Rebasing                        | [`08-rebasing.md`](08-rebasing.md)                                                   |
| 09 | Remote Repositories             | [`09-remote-repositories.md`](09-remote-repositories.md)                             |
| 10 | Undoing Changes                 | [`10-undoing-changes.md`](10-undoing-changes.md)                                     |
| 11 | Stash                           | [`11-stash.md`](11-stash.md)                                                         |
| 12 | Tags & Releases                 | [`12-tags-and-releases.md`](12-tags-and-releases.md)                                 |
| 13 | Searching Git History           | [`13-searching-git-history.md`](13-searching-git-history.md)                         |
| 14 | Comparing Branches & Commits    | [`14-comparing-branches-and-commits.md`](14-comparing-branches-and-commits.md)       |
| 15 | Conflict Resolution             | [`15-conflict-resolution.md`](15-conflict-resolution.md)                             |
| 16 | Cherry-Pick                     | [`16-cherry-pick.md`](16-cherry-pick.md)                                             |
| 17 | Reflog & Recovery               | [`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)                             |
| 18 | Git Bisect                      | [`18-git-bisect.md`](18-git-bisect.md)                                               |
| 19 | Submodules                      | [`19-submodules.md`](19-submodules.md)                                               |
| 20 | Worktrees                       | [`20-worktrees.md`](20-worktrees.md)                                                 |
| 21 | Git LFS                         | [`21-git-lfs.md`](21-git-lfs.md)                                                     |
| 22 | Git Hooks                       | [`22-git-hooks.md`](22-git-hooks.md)                                                 |
| 23 | Repository Maintenance          | [`23-repository-maintenance.md`](23-repository-maintenance.md)                       |
| 24 | Repository Diagnostics          | [`24-repository-diagnostics.md`](24-repository-diagnostics.md)                       |
| 25 | Git Objects / Internals         | [`25-git-objects-internals.md`](25-git-objects-internals.md)                         |
| 26 | Ignoring Files                  | [`26-ignoring-files.md`](26-ignoring-files.md)                                       |
| 27 | File Tracking                   | [`27-file-tracking.md`](27-file-tracking.md)                                         |
| 28 | Sparse Checkout                 | [`28-sparse-checkout.md`](28-sparse-checkout.md)                                     |
| 29 | Git Archives                    | [`29-git-archives.md`](29-git-archives.md)                                           |
| 30 | Environment & Automation        | [`30-environment-and-automation.md`](30-environment-and-automation.md)               |
| 31 | Common DevOps / CI Commands     | [`31-common-devops-ci-commands.md`](31-common-devops-ci-commands.md)                 |
| 32 | Common Developer Workflows      | [`32-common-developer-workflows.md`](32-common-developer-workflows.md)               |
| 33 | High-Value Commands to Memorize | [`33-high-value-commands-to-memorize.md`](33-high-value-commands-to-memorize.md)     |
| 34 | Dangerous Commands              | [`34-dangerous-commands.md`](34-dangerous-commands.md)                               |
| 35 | Practical Git Aliases           | [`35-practical-git-aliases.md`](35-practical-git-aliases.md)                         |
