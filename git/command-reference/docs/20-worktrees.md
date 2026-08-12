# 20. Git Worktrees

Git worktrees allow a single Git repository to have **multiple working directories simultaneously**, with each worktree normally associated with a different branch or commit.

Instead of repeatedly switching branches in one directory:

```text
project/
└── working tree
```

you can have:

```text
project/
├── main-worktree/
├── feature-worktree/
├── bugfix-worktree/
└── release-worktree/
```

All worktrees share the same underlying Git object database.

This is especially useful for:

* Parallel feature development
* Hotfixes
* Release branches
* Code reviews
* Running tests on one branch while developing another
* Comparing branches
* Long-running builds
* CI/CD workflows
* Maintaining multiple versions of a project

---

# Table of Contents

* [20.1 What Is a Git Worktree?](#201-what-is-a-git-worktree)
* [20.2 Why Use Worktrees?](#202-why-use-worktrees)
* [20.3 Worktree Architecture](#203-worktree-architecture)
* [20.4 Listing Worktrees](#204-listing-worktrees)
* [20.5 Creating a Worktree](#205-creating-a-worktree)
* [20.6 Creating a Worktree for a New Branch](#206-creating-a-worktree-for-a-new-branch)
* [20.7 Creating a Worktree From an Existing Branch](#207-creating-a-worktree-from-an-existing-branch)
* [20.8 Creating a Worktree at a Specific Commit](#208-creating-a-worktree-at-a-specific-commit)
* [20.9 Creating a Detached Worktree](#209-creating-a-detached-worktree)
* [20.10 Creating Worktrees From Remote Branches](#2010-creating-worktrees-from-remote-branches)
* [20.11 Creating Worktrees With `--track`](#2011-creating-worktrees-with---track)
* [20.12 Creating Worktrees With `--guess-remote`](#2012-creating-worktrees-with---guess-remote)
* [20.13 Locking a Worktree](#2013-locking-a-worktree)
* [20.14 Unlocking a Worktree](#2014-unlocking-a-worktree)
* [20.15 Moving a Worktree](#2015-moving-a-worktree)
* [20.16 Removing a Worktree](#2016-removing-a-worktree)
* [20.17 Pruning Stale Worktrees](#2017-pruning-stale-worktrees)
* [20.18 Repairing Worktrees](#2018-repairing-worktrees)
* [20.19 Worktree Status](#2019-worktree-status)
* [20.20 Worktree Configuration](#2020-worktree-configuration)
* [20.21 Worktrees and Branches](#2021-worktrees-and-branches)
* [20.22 Worktrees and Detached HEAD](#2022-worktrees-and-detached-head)
* [20.23 Worktrees and Git Status](#2023-worktrees-and-git-status)
* [20.24 Worktrees and Stash](#2024-worktrees-and-stash)
* [20.25 Worktrees and Rebase](#2025-worktrees-and-rebase)
* [20.26 Worktrees and Merge](#2026-worktrees-and-merge)
* [20.27 Worktrees and Cherry-Pick](#2027-worktrees-and-cherry-pick)
* [20.28 Worktrees for Feature Development](#2028-worktrees-for-feature-development)
* [20.29 Worktrees for Hotfixes](#2029-worktrees-for-hotfixes)
* [20.30 Worktrees for Code Review](#2030-worktrees-for-code-review)
* [20.31 Worktrees for DevOps and CI/CD](#2031-worktrees-for-devops-and-cicd)
* [20.32 Worktree Troubleshooting](#2032-worktree-troubleshooting)
* [20.33 Worktree Safety](#2033-worktree-safety)
* [20.34 High-Value Worktree Commands](#2034-high-value-worktree-commands)
* [20.35 Worktree Cheat Sheet](#2035-worktree-cheat-sheet)

---

# 20.1 What Is a Git Worktree?

A worktree is an additional working directory associated with an existing Git repository.

Suppose you have:

```text
main
feature/login
bugfix/payment
```

Without worktrees, you might repeatedly run:

```bash
git switch main
git switch feature/login
git switch bugfix/payment
```

With worktrees:

```text
project-main/
project-login/
project-payment/
```

Each directory can represent a different branch simultaneously.

For example:

```text
project-main/       -> main
project-login/      -> feature/login
project-payment/    -> bugfix/payment
```

All of them belong to the same Git repository.

---

# 20.2 Why Use Worktrees?

Worktrees solve several practical development problems.

## Without worktrees

You may have:

```text
feature branch
    |
    +-- uncommitted changes
    |
    +-- need urgent production hotfix
```

You would need to:

```bash
git stash
git switch main
```

With worktrees:

```text
feature-worktree/  -> feature/login
hotfix-worktree/   -> hotfix/production
```

No stashing is necessary.

---

# 20.3 Worktree Architecture

A repository using worktrees might look conceptually like:

```text
repository/
├── main/
├── feature-a/
├── feature-b/
└── hotfix/
```

Git maintains administrative information for each linked worktree.

The main worktree contains the primary repository metadata, while linked worktrees have their own worktree-specific administrative information.

Conceptually:

```text
             Shared Repository
          ┌──────────────────────┐
          │ Objects               │
          │ References            │
          │ Configuration         │
          └──────────┬───────────┘
                     |
       ┌─────────────┼─────────────┐
       |             |             |
       v             v             v
   Worktree A    Worktree B    Worktree C
     main        feature       hotfix
```

This means commits and objects are shared instead of being duplicated in every worktree.

---

# 20.4 Listing Worktrees

List all worktrees:

```bash
git worktree list
```

Example:

```text
/home/user/project       abc1234 [main]
/home/user/project-login def5678 [feature/login]
/home/user/project-fix   987abcd [bugfix/payment]
```

More detailed output:

```bash
git worktree list --verbose
```

Machine-readable output:

```bash
git worktree list --porcelain
```

These commands do not change branch state.

---

| Command                         | Description             | Example                         | Branch State Before and After command | Output                          |
| ------------------------------- | ----------------------- | ------------------------------- | ------------------------------------- | ------------------------------- |
| `git worktree list`             | List all worktrees      | `git worktree list`             | Unchanged                             | Paths, commits, branches        |
| `git worktree list --verbose`   | Show additional details | `git worktree list --verbose`   | Unchanged                             | Detailed worktree information   |
| `git worktree list --porcelain` | Machine-readable output | `git worktree list --porcelain` | Unchanged                             | Structured worktree information |

---

# 20.5 Creating a Worktree

Basic syntax:

```bash
git worktree add <path> <branch>
```

Example:

```bash
git worktree add ../feature-login feature/login
```

If `feature/login` already exists, Git creates:

```text
../feature-login/
```

and checks out:

```text
feature/login
```

Check:

```bash
git worktree list
```

The original worktree remains on its current branch.

---

# 20.6 Creating a Worktree for a New Branch

Create a new branch and worktree:

```bash
git worktree add -b feature/login ../feature-login
```

This performs conceptually:

```bash
git branch feature/login
git worktree add ../feature-login feature/login
```

Example:

```bash
git worktree add -b feature/payment ../feature-payment
```

The new directory is created and the new branch is checked out there.

Check:

```bash
git worktree list
```

---

| Command                           | Description                         | Example                                              | Branch State Before and After command       | Output           |
| --------------------------------- | ----------------------------------- | ---------------------------------------------------- | ------------------------------------------- | ---------------- |
| `git worktree add PATH BRANCH`    | Create worktree for existing branch | `git worktree add ../feature feature/login`          | Existing branch checked out in new worktree | Worktree created |
| `git worktree add -b BRANCH PATH` | Create new branch and worktree      | `git worktree add -b feature/login ../feature-login` | New branch created and checked out          | New worktree     |
| `git worktree add -B BRANCH PATH` | Create/reset branch and worktree    | `git worktree add -B feature/login ../feature-login` | Branch may be reset                         | Worktree created |

---

# 20.7 Creating a Worktree From an Existing Branch

If the branch already exists:

```bash
git branch
```

Example:

```text
* main
  feature/login
  feature/payment
```

Create:

```bash
git worktree add ../login feature/login
```

Now:

```text
../login -> feature/login
```

The original worktree remains on:

```text
main
```

---

# 20.8 Creating a Worktree at a Specific Commit

You can create a worktree from a commit:

```bash
git worktree add ../review abc1234
```

If the commit is not associated with a branch checkout, Git normally creates a detached HEAD worktree.

Check:

```bash
cd ../review
git status
```

You may see:

```text
HEAD detached at abc1234
```

This is useful for:

* Inspecting old versions
* Reproducing historical bugs
* Reviewing a specific commit
* Running tests against an exact revision

---

# 20.9 Creating a Detached Worktree

Explicitly create a detached worktree:

```bash
git worktree add --detach ../review abc1234
```

Example:

```bash
git worktree add --detach ../old-release v2.4.0
```

The worktree is not attached to a local branch.

Check:

```bash
git branch --show-current
```

Output:

```text
```

A detached worktree can be safely used for testing without changing a development branch.

---

# 20.10 Creating Worktrees From Remote Branches

Suppose:

```text
origin/feature/api
```

exists.

You can create:

```bash
git worktree add ../api feature/api
```

If the local branch does not exist, Git may be able to create a local tracking branch based on an unambiguous remote branch.

Alternatively, explicitly create it:

```bash
git worktree add -b feature/api ../api origin/feature/api
```

This creates:

```text
feature/api -> origin/feature/api
```

Then:

```bash
cd ../api
git branch -vv
```

---

# 20.11 Creating Worktrees With `--track`

Create a branch that tracks another branch:

```bash
git worktree add -b feature/api --track ../api origin/feature/api
```

The branch configuration can then be inspected:

```bash
git branch -vv
```

Example:

```text
* feature/api abc1234 [origin/feature/api] Implement API
```

This is useful when creating worktrees for remote development branches.

---

# 20.12 Creating Worktrees With `--guess-remote`

Git can infer a remote-tracking branch in suitable situations:

```bash
git worktree add --guess-remote ../api feature/api
```

This can create a local branch based on:

```text
origin/feature/api
```

The exact behavior depends on the available remote-tracking references and branch configuration.

Verify:

```bash
git branch -vv
```

---

# 20.13 Locking a Worktree

A worktree can be locked to prevent Git from automatically pruning it.

Lock:

```bash
git worktree lock ../feature-login
```

With a reason:

```bash
git worktree lock --reason "External disk" ../feature-login
```

A locked worktree is useful when:

* The worktree is on removable storage.
* The worktree is temporarily unavailable.
* The path cannot currently be accessed.
* You want to protect the administrative entry from pruning.

List:

```bash
git worktree list --verbose
```

---

# 20.14 Unlocking a Worktree

Unlock:

```bash
git worktree unlock ../feature-login
```

Check:

```bash
git worktree list --verbose
```

The worktree is now available for normal maintenance and pruning behavior.

---

# 20.15 Moving a Worktree

Move a worktree:

```bash
git worktree move <old-path> <new-path>
```

Example:

```bash
git worktree move ../feature-login ../login
```

Check:

```bash
git worktree list
```

Git updates its worktree metadata to reflect the new location.

Do not manually move worktree directories without updating Git's administrative information.

---

# 20.16 Removing a Worktree

Remove:

```bash
git worktree remove ../feature-login
```

If the worktree contains uncommitted changes, Git may refuse.

Force removal:

```bash
git worktree remove --force ../feature-login
```

Use `--force` carefully because uncommitted work can be lost.

After removal:

```bash
git worktree list
```

---

| Command                            | Description            | Example                                       | Branch State Before and After command | Output         |
| ---------------------------------- | ---------------------- | --------------------------------------------- | ------------------------------------- | -------------- |
| `git worktree remove PATH`         | Remove linked worktree | `git worktree remove ../feature`              | Worktree removed                      | Removal result |
| `git worktree remove --force PATH` | Force removal          | `git worktree remove --force ../feature`      | Worktree forcibly removed             | Removal result |
| `git worktree move OLD NEW`        | Move worktree          | `git worktree move ../feature ../feature-new` | Branch remains checked out            | Updated path   |
| `git worktree lock PATH`           | Lock worktree          | `git worktree lock ../feature`                | Worktree protected from pruning       | Lock result    |
| `git worktree unlock PATH`         | Unlock worktree        | `git worktree unlock ../feature`              | Worktree unlocked                     | Unlock result  |

---

# 20.17 Pruning Stale Worktrees

If a worktree directory was manually deleted, Git may retain stale administrative metadata.

Inspect what would be removed:

```bash
git worktree prune --dry-run
```

Then prune:

```bash
git worktree prune
```

Verbose:

```bash
git worktree prune --verbose
```

This is useful after:

```bash
rm -rf ../old-worktree
```

However, deleting worktree directories manually is not the recommended workflow.

Prefer:

```bash
git worktree remove ../old-worktree
```

---

# 20.18 Repairing Worktrees

If a worktree was moved manually, Git can attempt to repair its administrative information.

Example:

```bash
git worktree repair ../feature-login
```

For multiple worktrees:

```bash
git worktree repair
```

This is useful after:

* Moving directories manually
* Restoring from backup
* Changing mount points
* Moving a repository between storage locations

Verify:

```bash
git worktree list --verbose
```

---

# 20.19 Worktree Status

Inside a worktree:

```bash
git status
```

Check its branch:

```bash
git branch --show-current
```

Check commit:

```bash
git rev-parse HEAD
```

From the main worktree:

```bash
git worktree list
```

A useful diagnostic sequence is:

```bash
git worktree list --verbose
git status
git branch -vv
```

---

# 20.20 Worktree Configuration

Some repositories may require extensions for multiple worktrees.

Check:

```bash
git config --get extensions.worktreeConfig
```

Enable worktree-specific configuration:

```bash
git config extensions.worktreeConfig true
```

Git can then use worktree-specific configuration where supported.

Inspect:

```bash
git config --list --show-origin
```

---

# 20.21 Worktrees and Branches

A branch can generally be checked out in only one worktree at a time.

Suppose:

```text
main-worktree -> main
```

Attempting:

```bash
git worktree add ../another-main main
```

will normally fail because `main` is already checked out.

Git protects you from accidentally having the same branch actively checked out in multiple worktrees.

This is one of the major safety mechanisms of worktrees.

To see which worktree owns a branch:

```bash
git worktree list
```

---

# 20.22 Worktrees and Detached HEAD

Detached worktrees are useful for temporary operations.

Create:

```bash
git worktree add --detach ../test abc1234
```

Run tests:

```bash
cd ../test
./run-tests.sh
```

Inspect:

```bash
git status
```

When finished:

```bash
cd ..
git worktree remove ../test
```

Because the worktree is detached, no development branch needs to be created.

---

# 20.23 Worktrees and Git Status

Each worktree has its own working-tree state.

For example:

```text
main-worktree/
    main
    clean

feature-worktree/
    feature/login
    modified files
```

Running:

```bash
git status
```

inside `main-worktree` does not show uncommitted changes from `feature-worktree`.

Each working directory has independent:

* Working files
* Index
* HEAD
* Worktree-specific state

while sharing repository objects.

---

# 20.24 Worktrees and Stash

Worktrees often reduce the need for stash.

Instead of:

```bash
git stash
git switch main
```

you can create:

```bash
git worktree add ../hotfix hotfix/production
```

Now:

```text
feature-worktree -> feature/login
hotfix-worktree  -> hotfix/production
```

Your feature changes remain untouched.

Stashes are still repository-level objects, so care should be taken when using them across multiple worktrees.

A good default is to use worktrees for **parallel branch work** and stash for **temporary preservation of working changes**.

---

# 20.25 Worktrees and Rebase

Suppose:

```text
main-worktree    -> main
feature-worktree -> feature/api
```

Enter the feature worktree:

```bash
cd ../feature
```

Update:

```bash
git fetch origin
```

Rebase:

```bash
git rebase origin/main
```

The rebase affects the `feature/api` branch.

The `main` worktree remains on its branch.

Check:

```bash
git worktree list
```

This separation is especially useful when a rebase requires repeated conflict resolution and testing.

---

# 20.26 Worktrees and Merge

Suppose:

```text
main-worktree    -> main
feature-worktree -> feature/api
```

Develop in:

```bash
cd ../feature
```

Commit:

```bash
git add .
git commit -m "Implement API"
```

Then switch to the main worktree:

```bash
cd ../main
```

Merge:

```bash
git merge feature/api
```

This avoids repeatedly switching between branches in one directory.

---

# 20.27 Worktrees and Cherry-Pick

A hotfix workflow can use a dedicated worktree:

```bash
git worktree add -b hotfix/production ../hotfix main
```

Then:

```bash
cd ../hotfix
```

Cherry-pick:

```bash
git cherry-pick abc1234
```

Test:

```bash
./run-tests.sh
```

Commit/push:

```bash
git push -u origin hotfix/production
```

Meanwhile, feature development continues in another worktree.

---

# 20.28 Worktrees for Feature Development

Create:

```bash
git worktree add -b feature/search ../feature-search main
```

Develop:

```bash
cd ../feature-search
```

Check:

```bash
git status
```

Commit:

```bash
git add .
git commit -m "Implement search"
```

Push:

```bash
git push -u origin feature/search
```

Your original worktree remains on its previous branch.

List:

```bash
git worktree list
```

---

# 20.29 Worktrees for Hotfixes

Suppose you are developing:

```text
feature/payment
```

but production requires an urgent fix.

Create:

```bash
git worktree add -b hotfix/production ../hotfix main
```

Fix:

```bash
cd ../hotfix
```

Edit files and test:

```bash
./run-tests.sh
```

Commit:

```bash
git add .
git commit -m "Fix production issue"
```

Push:

```bash
git push -u origin hotfix/production
```

Your feature worktree remains untouched.

This is one of the strongest practical use cases for worktrees.

---

# 20.30 Worktrees for Code Review

Suppose a pull request branch is:

```text
feature/payment
```

Create:

```bash
git fetch origin
git worktree add --detach ../review origin/feature/payment
```

Enter:

```bash
cd ../review
```

Inspect:

```bash
git log --oneline --decorate -20
git diff origin/main...HEAD
```

Run:

```bash
./run-tests.sh
```

After review:

```bash
cd ..
git worktree remove ../review
```

This keeps review changes isolated from your main development work.

---

# 20.31 Worktrees for DevOps and CI/CD

Worktrees can be useful for:

* Testing multiple release branches
* Building multiple versions
* Comparing deployment configurations
* Testing infrastructure changes
* Reproducing historical builds
* Maintaining release branches

Example:

```bash
git worktree add --detach ../release-v1 v1.0.0
git worktree add --detach ../release-v2 v2.0.0
```

Then:

```bash
cd ../release-v1
./build.sh
```

and separately:

```bash
cd ../release-v2
./build.sh
```

This avoids repeatedly switching a single working directory.

For CI systems, normal isolated clones are often simpler, but worktrees can be useful in controlled build environments where a shared object database is beneficial.

---

# 20.32 Worktree Troubleshooting

## Branch Already Checked Out

If:

```bash
git worktree add ../main-copy main
```

fails because `main` is already checked out, inspect:

```bash
git worktree list
```

You can create a worktree for another branch instead:

```bash
git worktree add -b feature/new ../feature-new
```

---

## Worktree Directory Was Deleted

Check:

```bash
git worktree list
```

Then:

```bash
git worktree prune --dry-run
```

If the stale entry is correct to remove:

```bash
git worktree prune
```

---

## Worktree Was Moved Manually

Use:

```bash
git worktree repair ../new-location
```

Then:

```bash
git worktree list --verbose
```

---

## Worktree Contains Uncommitted Changes

Before removing:

```bash
cd ../feature
git status
```

Commit:

```bash
git add .
git commit -m "Save work"
```

or stash:

```bash
git stash push -m "Temporary worktree changes"
```

Then remove:

```bash
cd ..
git worktree remove ../feature
```

---

# 20.33 Worktree Safety

Before removing a worktree:

```bash
git status
```

Check:

```bash
git diff
```

and:

```bash
git diff --cached
```

Also inspect:

```bash
git worktree list
```

Avoid:

```bash
git worktree remove --force <path>
```

unless you are certain that uncommitted work can be discarded.

Avoid manually deleting worktree directories when possible.

Prefer:

```bash
git worktree remove <path>
```

For stale metadata:

```bash
git worktree prune --dry-run
```

before:

```bash
git worktree prune
```

---

# 20.34 High-Value Worktree Commands

| Command                                       | Description                      | Example                                                             | Branch State Before and After command | Output                      |
| --------------------------------------------- | -------------------------------- | ------------------------------------------------------------------- | ------------------------------------- | --------------------------- |
| `git worktree list`                           | List worktrees                   | `git worktree list`                                                 | Unchanged                             | Worktree paths and branches |
| `git worktree list --verbose`                 | List detailed worktrees          | `git worktree list --verbose`                                       | Unchanged                             | Detailed state              |
| `git worktree list --porcelain`               | Machine-readable list            | `git worktree list --porcelain`                                     | Unchanged                             | Structured data             |
| `git worktree add PATH BRANCH`                | Add worktree for existing branch | `git worktree add ../feature feature/api`                           | New worktree checks out branch        | Worktree created            |
| `git worktree add -b BRANCH PATH`             | Create branch and worktree       | `git worktree add -b feature/api ../feature`                        | New branch created                    | Worktree created            |
| `git worktree add -B BRANCH PATH`             | Reset/create branch and worktree | `git worktree add -B feature/api ../feature`                        | Branch may be reset                   | Worktree created            |
| `git worktree add --detach PATH COMMIT`       | Create detached worktree         | `git worktree add --detach ../review abc1234`                       | Detached HEAD                         | Worktree created            |
| `git worktree add --track PATH`               | Track remote branch              | `git worktree add -b feature/api --track ../api origin/feature/api` | Tracking branch created               | Worktree created            |
| `git worktree add --guess-remote PATH BRANCH` | Infer remote branch              | `git worktree add --guess-remote ../api feature/api`                | Remote tracking may be configured     | Worktree created            |
| `git worktree remove PATH`                    | Remove worktree                  | `git worktree remove ../feature`                                    | Worktree removed                      | Removal result              |
| `git worktree remove --force PATH`            | Force removal                    | `git worktree remove --force ../feature`                            | Worktree forcibly removed             | Removal result              |
| `git worktree move OLD NEW`                   | Move worktree                    | `git worktree move ../feature ../feature-new`                       | Same branch, new path                 | Updated metadata            |
| `git worktree lock PATH`                      | Lock worktree                    | `git worktree lock ../feature`                                      | Worktree locked                       | Lock result                 |
| `git worktree lock --reason TEXT PATH`        | Lock with reason                 | `git worktree lock --reason "External disk" ../feature`             | Worktree locked                       | Lock reason                 |
| `git worktree unlock PATH`                    | Unlock worktree                  | `git worktree unlock ../feature`                                    | Worktree unlocked                     | Unlock result               |
| `git worktree prune --dry-run`                | Preview stale entries            | `git worktree prune --dry-run`                                      | Unchanged                             | Candidates for pruning      |
| `git worktree prune`                          | Remove stale metadata            | `git worktree prune`                                                | Stale metadata removed                | Pruning result              |
| `git worktree prune --verbose`                | Verbose pruning                  | `git worktree prune --verbose`                                      | Stale metadata removed                | Detailed output             |
| `git worktree repair`                         | Repair worktree metadata         | `git worktree repair`                                               | Metadata repaired where possible      | Repair result               |
| `git worktree repair PATH`                    | Repair specific worktree         | `git worktree repair ../feature`                                    | Metadata repaired                     | Repair result               |

---

# 20.35 Worktree Cheat Sheet

## List Worktrees

```bash
git worktree list
```

## Detailed List

```bash
git worktree list --verbose
```

## Create Worktree From Existing Branch

```bash
git worktree add ../feature feature/api
```

## Create New Branch + Worktree

```bash
git worktree add -b feature/api ../feature-api
```

## Create Worktree From Remote Branch

```bash
git worktree add -b feature/api ../feature-api origin/feature/api
```

## Create Detached Worktree

```bash
git worktree add --detach ../review <commit>
```

## Create Hotfix Worktree

```bash
git worktree add -b hotfix/production ../hotfix main
```

## Lock

```bash
git worktree lock ../feature
```

## Lock With Reason

```bash
git worktree lock --reason "External storage" ../feature
```

## Unlock

```bash
git worktree unlock ../feature
```

## Move

```bash
git worktree move ../feature ../feature-new
```

## Remove

```bash
git worktree remove ../feature
```

## Force Remove

```bash
git worktree remove --force ../feature
```

## Check Stale Entries

```bash
git worktree prune --dry-run
```

## Prune

```bash
git worktree prune
```

## Repair

```bash
git worktree repair
```

---

# Recommended Daily Worktree Workflow

A practical developer workflow:

```bash
# Start from the main worktree
git status
git worktree list

# Create feature worktree
git worktree add -b feature/search ../feature-search main

# Enter feature worktree
cd ../feature-search

# Develop
git status
git add .
git commit -m "Implement search"

# Push
git push -u origin feature/search
```

When an urgent hotfix arrives:

```bash
# From the repository's main worktree
git worktree add -b hotfix/production ../hotfix main

cd ../hotfix

# Implement and test the fix
git status
git add .
git commit -m "Fix production issue"

git push -u origin hotfix/production
```

Return to feature development:

```bash
cd ../feature-search
```

No stash is required.

---

# Recommended Code Review Workflow

```bash
git fetch origin

git worktree add --detach ../review origin/feature/payment

cd ../review

git log --oneline --decorate -20
git diff origin/main...HEAD

./run-tests.sh

cd ..

git worktree remove ../review
```

This creates a clean and isolated review environment.

---

# Recommended Release Testing Workflow

```bash
git worktree add --detach ../release-v1 v1.0.0
git worktree add --detach ../release-v2 v2.0.0
git worktree add --detach ../release-v3 v3.0.0
```

Then independently test:

```bash
cd ../release-v1
./build.sh
```

```bash
cd ../release-v2
./build.sh
```

```bash
cd ../release-v3
./build.sh
```

The branches/commits remain isolated while the underlying Git objects are shared.

---

# Essential Mental Model

The key difference between ordinary branch switching and worktrees is:

```text
Traditional workflow:

project/
    |
    +-- switch main
    |
    +-- switch feature
    |
    +-- switch hotfix
```

versus:

```text
Worktree workflow:

project-main/
    |
    +-- main

project-feature/
    |
    +-- feature

project-hotfix/
    |
    +-- hotfix
```

All three worktrees can exist simultaneously:

```text
                    Shared Git Repository
                           |
              +------------+------------+
              |            |            |
              v            v            v
            main        feature       hotfix
         worktree      worktree      worktree
```

The most important commands to remember are:

```bash
git worktree list
git worktree add
git worktree remove
git worktree move
git worktree lock
git worktree unlock
git worktree prune
git worktree repair
```

For everyday development, the most valuable pattern is:

```bash
git worktree add -b <branch> <path> <start-point>
```

followed by:

```bash
cd <path>
```

This gives you an independent working directory without requiring another full clone.

---

## Next Part

**Next file:** `21-git-lfs.md`

[Next: Git LFS](21-git-lfs.md)
