# 33. High-Value Commands to Memorize

This chapter contains the Git commands that provide the highest practical value for developers, software engineers, DevOps engineers, and anyone working with Git on a daily basis.

The goal is not to memorize every Git option. The goal is to build a compact mental model of the commands that cover the majority of real-world Git operations.

---

# Table of Contents

* [33.1 Essential Daily Commands](#331-essential-daily-commands)
* [33.2 Configuration](#332-configuration)
* [33.3 Repository Creation](#333-repository-creation)
* [33.4 Repository Inspection](#334-repository-inspection)
* [33.5 Branch Management](#335-branch-management)
* [33.6 Staging](#336-staging)
* [33.7 Committing](#337-committing)
* [33.8 Reviewing Changes](#338-reviewing-changes)
* [33.9 Remote Repositories](#339-remote-repositories)
* [33.10 Synchronization](#3310-synchronization)
* [33.11 Rebase](#3311-rebase)
* [33.12 Merge](#3312-merge)
* [33.13 Undoing Changes](#3313-undoing-changes)
* [33.14 Stash](#3314-stash)
* [33.15 Tags](#3315-tags)
* [33.16 History Search](#3316-history-search)
* [33.17 File History](#3317-file-history)
* [33.18 Blame](#3318-blame)
* [33.19 Cherry-Pick](#3319-cherry-pick)
* [33.20 Reflog](#3320-reflog)
* [33.21 Bisect](#3321-bisect)
* [33.22 Conflict Resolution](#3322-conflict-resolution)
* [33.23 Worktrees](#3323-worktrees)
* [33.24 Cleanup](#3324-cleanup)
* [33.25 Diagnostics](#3325-diagnostics)
* [33.26 Git Internals](#3326-git-internals)
* [33.27 Common Command Combinations](#3327-common-command-combinations)
* [33.28 Commands to Memorize First](#3328-commands-to-memorize-first)
* [33.29 Intermediate Commands](#3329-intermediate-commands)
* [33.30 Advanced Commands](#3330-advanced-commands)
* [33.31 Developer Cheat Sheet](#3331-developer-cheat-sheet)
* [33.32 DevOps Cheat Sheet](#3332-devops-cheat-sheet)
* [33.33 Emergency Recovery Cheat Sheet](#3333-emergency-recovery-cheat-sheet)
* [33.34 Recommended Memorization Order](#3334-recommended-memorization-order)
* [33.35 Final Command Reference](#3335-final-command-reference)

---

# 33.1 Essential Daily Commands

These are the commands worth memorizing first.

| Command             | Description              | Example                     | Branch State Before and After command | Output              |
| ------------------- | ------------------------ | --------------------------- | ------------------------------------- | ------------------- |
| `git status`        | Show repository state    | `git status`                | Unchanged → unchanged                 | Working-tree status |
| `git add`           | Stage changes            | `git add file.c`            | Unstaged → staged                     | Usually none        |
| `git commit`        | Create commit            | `git commit -m "Fix bug"`   | Working changes staged → new commit   | Commit summary      |
| `git diff`          | Show unstaged changes    | `git diff`                  | Unchanged                             | Patch               |
| `git diff --cached` | Show staged changes      | `git diff --cached`         | Unchanged                             | Patch               |
| `git log`           | Show history             | `git log --oneline`         | Unchanged                             | Commit history      |
| `git switch`        | Switch branch            | `git switch main`           | Branch A → branch B                   | Usually none        |
| `git switch -c`     | Create and switch branch | `git switch -c feature/api` | Current branch → new branch           | Usually none        |
| `git fetch`         | Download remote refs     | `git fetch origin`          | Local branch unchanged                | Fetch information   |
| `git pull`          | Fetch and integrate      | `git pull --ff-only`        | Branch may advance                    | Pull information    |
| `git push`          | Publish commits          | `git push`                  | Local branch → remote updated         | Push information    |

The core daily cycle is:

```bash
git status
git diff
git add
git diff --cached
git commit
git fetch
git push
```

---

# 33.2 Configuration

Check configuration:

```bash
git config --list
```

Set identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Set default branch:

```bash
git config --global init.defaultBranch main
```

Set a useful default:

```bash
git config --global fetch.prune true
```

Check a specific setting:

```bash
git config --get user.name
```

---

# 33.3 Repository Creation

Initialize:

```bash
git init
```

Clone:

```bash
git clone <URL>
```

Verify repository:

```bash
git rev-parse --show-toplevel
```

This is useful when scripts or shell sessions may start inside nested directories.

---

# 33.4 Repository Inspection

The most useful inspection commands are:

```bash
git status
git status -sb
git branch --show-current
git remote -v
git log --oneline --decorate --graph --all
git rev-parse HEAD
```

A compact diagnostic sequence:

```bash
git status -sb
git branch --show-current
git remote -v
git log -1 --oneline
```

---

# 33.5 Branch Management

List local branches:

```bash
git branch
```

List all branches:

```bash
git branch -a
```

Create:

```bash
git branch feature/api
```

Create and switch:

```bash
git switch -c feature/api
```

Switch:

```bash
git switch feature/api
```

Rename:

```bash
git branch -m feature/api feature/rest-api
```

Delete safely:

```bash
git branch -d feature/api
```

Force delete:

```bash
git branch -D feature/api
```

The most important branch command to memorize is:

```bash
git switch -c <branch>
```

---

# 33.6 Staging

Stage one file:

```bash
git add file.c
```

Stage multiple files:

```bash
git add file1.c file2.c
```

Stage a directory:

```bash
git add src/
```

Stage all changes:

```bash
git add .
```

Interactive staging:

```bash
git add -p
```

The interactive form is especially valuable for creating clean, focused commits.

---

# 33.7 Committing

Normal commit:

```bash
git commit -m "Add authentication"
```

Commit with editor:

```bash
git commit
```

Amend message:

```bash
git commit --amend -m "Improve authentication"
```

Amend with additional staged changes:

```bash
git add file.c
git commit --amend --no-edit
```

View latest commit:

```bash
git show --stat HEAD
```

---

# 33.8 Reviewing Changes

Unstaged changes:

```bash
git diff
```

Staged changes:

```bash
git diff --cached
```

Working tree summary:

```bash
git diff --stat
```

Staged summary:

```bash
git diff --cached --stat
```

Check whitespace:

```bash
git diff --check
```

Reviewing before committing should become habitual:

```bash
git diff
git diff --cached
git diff --check
```

---

# 33.9 Remote Repositories

List remotes:

```bash
git remote -v
```

Add remote:

```bash
git remote add origin <URL>
```

Rename remote:

```bash
git remote rename origin upstream
```

Remove remote:

```bash
git remote remove upstream
```

Show detailed remote information:

```bash
git remote show origin
```

---

# 33.10 Synchronization

Fetch:

```bash
git fetch origin
```

Fetch everything:

```bash
git fetch --all
```

Fetch and remove stale references:

```bash
git fetch --prune
```

Fast-forward local main:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
```

Push:

```bash
git push
```

First push of a branch:

```bash
git push -u origin feature/api
```

---

# 33.11 Rebase

Update feature branch:

```bash
git fetch origin
git rebase origin/main
```

Interactive rebase:

```bash
git rebase -i HEAD~5
```

Continue:

```bash
git rebase --continue
```

Abort:

```bash
git rebase --abort
```

Skip:

```bash
git rebase --skip
```

The essential concept:

```text
Rebase rewrites commits.
```

Therefore, after rebasing a previously pushed feature branch:

```bash
git push --force-with-lease
```

---

# 33.12 Merge

Merge another branch:

```bash
git merge feature/api
```

Fast-forward only:

```bash
git merge --ff-only origin/main
```

Abort conflicted merge:

```bash
git merge --abort
```

Complete conflicted merge:

```bash
git add <resolved-file>
git commit
```

A merge preserves the existing branch histories and may create a merge commit.

---

# 33.13 Undoing Changes

Unstage a file:

```bash
git restore --staged file.c
```

Discard working-tree changes:

```bash
git restore file.c
```

Restore a file from a commit:

```bash
git restore --source=HEAD~1 file.c
```

Undo a published commit:

```bash
git revert <commit>
```

Undo the latest local commit while keeping changes staged:

```bash
git reset --soft HEAD~1
```

Undo the latest local commit while keeping changes unstaged:

```bash
git reset HEAD~1
```

Discard the latest local commit and working-tree changes:

```bash
git reset --hard HEAD~1
```

The key distinction:

```text
restore -> file contents
reset   -> branch/index/history position
revert  -> new commit that undoes another commit
```

---

# 33.14 Stash

Create stash:

```bash
git stash push -m "WIP"
```

Include untracked files:

```bash
git stash push -u -m "WIP"
```

List:

```bash
git stash list
```

Apply:

```bash
git stash apply
```

Pop:

```bash
git stash pop
```

Delete:

```bash
git stash drop stash@{0}
```

Clear all:

```bash
git stash clear
```

Useful mental model:

```text
stash = temporarily store unfinished working-tree changes
```

---

# 33.15 Tags

List tags:

```bash
git tag
```

Create lightweight tag:

```bash
git tag v2.0.0
```

Create annotated tag:

```bash
git tag -a v2.0.0 -m "Release v2.0.0"
```

Inspect:

```bash
git show v2.0.0
```

Push:

```bash
git push origin v2.0.0
```

Push all tags:

```bash
git push origin --tags
```

Delete local tag:

```bash
git tag -d v2.0.0
```

Delete remote tag:

```bash
git push origin --delete v2.0.0
```

---

# 33.16 History Search

Compact history:

```bash
git log --oneline
```

Graph:

```bash
git log --oneline --graph --decorate --all
```

Search messages:

```bash
git log --grep="authentication"
```

Search author:

```bash
git log --author="Alice"
```

Search all branches:

```bash
git log --all --oneline
```

The following command is particularly valuable:

```bash
git log --oneline --decorate --graph --all
```

Memorize it.

---

# 33.17 File History

History of a file:

```bash
git log -- path/to/file
```

Follow renames:

```bash
git log --follow -- path/to/file
```

Show patches:

```bash
git log -p -- path/to/file
```

Find commits changing a string:

```bash
git log -S"function_name" -- path/to/file
```

Search regular expression changes:

```bash
git log -G"regex" -- path/to/file
```

---

# 33.18 Blame

Basic:

```bash
git blame path/to/file
```

Specific lines:

```bash
git blame -L 100,130 path/to/file
```

Ignore whitespace:

```bash
git blame -w path/to/file
```

Show commit details:

```bash
git show <commit>
```

Typical investigation:

```bash
git blame path/to/file
git show <commit>
git log --follow -- path/to/file
```

---

# 33.19 Cherry-Pick

Apply one commit:

```bash
git cherry-pick <commit>
```

Apply multiple commits:

```bash
git cherry-pick <commit1> <commit2>
```

Continue after conflict:

```bash
git cherry-pick --continue
```

Abort:

```bash
git cherry-pick --abort
```

Cherry-pick is particularly useful for:

* backports;
* hotfixes;
* maintenance branches;
* selectively applying a known fix.

---

# 33.20 Reflog

Show local reference movements:

```bash
git reflog
```

All references:

```bash
git reflog --all
```

Inspect recovered commit:

```bash
git show <commit>
```

Create recovery branch:

```bash
git switch -c recovery <commit>
```

Memorize:

```bash
git reflog
```

It is one of the most important Git recovery commands.

---

# 33.21 Bisect

Start:

```bash
git bisect start
```

Mark bad:

```bash
git bisect bad
```

Mark good:

```bash
git bisect good <commit>
```

Continue:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Finish:

```bash
git bisect reset
```

Automate:

```bash
git bisect run ./test-regression.sh
```

The mental model:

```text
Known good
    |
    | binary search
    v
Unknown commits
    |
    v
Known bad
```

---

# 33.22 Conflict Resolution

When Git reports a conflict:

```bash
git status
```

Inspect:

```bash
git diff
```

Edit conflicted files.

Stage resolution:

```bash
git add <file>
```

Continue merge:

```bash
git commit
```

Continue rebase:

```bash
git rebase --continue
```

Continue cherry-pick:

```bash
git cherry-pick --continue
```

Abort according to operation:

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
```

The universal first response to a conflict should be:

```bash
git status
```

---

# 33.23 Worktrees

List:

```bash
git worktree list
```

Create:

```bash
git worktree add ../feature-a feature/a
```

Create detached:

```bash
git worktree add --detach ../review HEAD
```

Remove:

```bash
git worktree remove ../review
```

Prune stale metadata:

```bash
git worktree prune
```

Worktrees are especially useful when you need multiple branches checked out simultaneously.

---

# 33.24 Cleanup

Preview untracked files:

```bash
git clean -nd
```

Delete untracked files:

```bash
git clean -fd
```

Preview ignored files:

```bash
git clean -ndX
```

Delete ignored files:

```bash
git clean -fdX
```

Prune remote-tracking branches:

```bash
git fetch --prune
```

Always preview `git clean` before executing destructive cleanup.

---

# 33.25 Diagnostics

Repository check:

```bash
git fsck --full
```

Object statistics:

```bash
git count-objects -vH
```

Repository directory:

```bash
git rev-parse --git-dir
```

Root directory:

```bash
git rev-parse --show-toplevel
```

Current commit:

```bash
git rev-parse HEAD
```

Git version:

```bash
git --version
```

Git command help:

```bash
git help <command>
```

---

# 33.26 Git Internals

Inspect commit:

```bash
git cat-file -p HEAD
```

Inspect object type:

```bash
git cat-file -t HEAD
```

Show object:

```bash
git show <object>
```

Hash content:

```bash
git hash-object file.txt
```

List tree:

```bash
git ls-tree HEAD
```

These commands become important when debugging repository internals or learning Git's object model.

---

# 33.27 Common Command Combinations

## Start Feature

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git switch -c feature/name
```

---

## Commit Feature

```bash
git status
git diff
git add .
git diff --cached
git commit -m "Implement feature"
```

---

## Update Feature

```bash
git fetch origin
git rebase origin/main
```

---

## Publish Feature

```bash
git push -u origin feature/name
```

---

## Review Feature

```bash
git diff origin/main...HEAD
git log --oneline origin/main..HEAD
git diff --check
```

---

## Finish Feature

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git branch -d feature/name
git fetch --prune
```

---

# 33.28 Commands to Memorize First

If you are learning Git, memorize these first:

```bash
git status
git add
git commit
git diff
git log
git switch
git switch -c
git branch
git fetch
git pull
git push
git merge
git rebase
git restore
git reset
git revert
git stash
```

A compact mental model:

```text
status  -> What is happening?
diff    -> What changed?
add     -> What goes into the next commit?
commit  -> Save a new version.
log     -> What happened?
switch  -> Move between branches.
fetch   -> Download remote information.
pull    -> Fetch + integrate.
push    -> Publish local commits.
merge   -> Combine histories.
rebase  -> Replay commits on a new base.
restore -> Restore file contents.
reset   -> Move branch/index state.
revert  -> Create an undo commit.
stash   -> Temporarily hide unfinished work.
```

---

# 33.29 Intermediate Commands

After mastering the basics, learn:

```bash
git cherry-pick
git reflog
git blame
git grep
git tag
git worktree
git clean
git bisect
git show
git diff --check
git log --follow
git log -S
git log -G
git remote
git branch --merged
git fetch --prune
```

These commands cover a large portion of real-world engineering work.

---

# 33.30 Advanced Commands

For advanced Git usage:

```bash
git rebase -i
git reflog --all
git fsck --full
git cat-file
git ls-tree
git rev-list
git rev-parse
git update-ref
git replace
git filter-repo
git range-diff
git rerere
git worktree
git bisect run
```

Advanced commands should be learned according to actual needs rather than memorized mechanically.

---

# 33.31 Developer Cheat Sheet

## Daily

```bash
git status
git diff
git add .
git diff --cached
git commit -m "message"
git fetch origin
git push
```

## Branch

```bash
git branch
git switch main
git switch -c feature/name
git branch -d feature/name
```

## History

```bash
git log --oneline --graph --decorate --all
git show HEAD
git log --follow -- file
git blame file
```

## Compare

```bash
git diff main...feature/name
git log main..feature/name --oneline
```

## Remote

```bash
git remote -v
git fetch origin
git push -u origin feature/name
git fetch --prune
```

## Undo

```bash
git restore file
git restore --staged file
git reset --soft HEAD~1
git revert <commit>
```

## Recovery

```bash
git reflog
git fsck --full
```

---

# 33.32 DevOps Cheat Sheet

For DevOps and CI/CD workflows, the most useful commands include:

```bash
git clone <URL>
git fetch --prune --tags
git checkout --detach <SHA>
git rev-parse HEAD
git describe --tags --always
git diff --exit-code
git diff --check
git status --porcelain
git archive
git ls-remote
git tag
git show
```

A typical CI verification:

```bash
git fetch --prune --tags
git status --porcelain
git diff --check
git rev-parse HEAD
git describe --tags --always
```

For reproducible builds, prefer an exact commit SHA or immutable tag rather than an ambiguous moving branch name.

---

# 33.33 Emergency Recovery Cheat Sheet

When something goes wrong, avoid immediately executing another destructive command.

First:

```bash
git status
```

Then:

```bash
git reflog
```

Inspect:

```bash
git log --oneline --graph --decorate --all
```

Inspect candidate:

```bash
git show <commit>
```

Create recovery branch:

```bash
git switch -c recovery <commit>
```

For a merge conflict:

```bash
git status
git diff
```

Abort if necessary:

```bash
git merge --abort
```

For rebase:

```bash
git rebase --abort
```

For cherry-pick:

```bash
git cherry-pick --abort
```

The recovery principle is:

```text
STOP
  |
  v
INSPECT
  |
  v
IDENTIFY
  |
  v
RECOVER
  |
  v
VERIFY
```

---

# 33.34 Recommended Memorization Order

## Level 1 — Daily Essentials

Memorize:

```bash
git status
git diff
git add
git commit
git log
git switch
git branch
```

---

## Level 2 — Collaboration

Then learn:

```bash
git remote
git fetch
git pull
git push
git merge
```

---

## Level 3 — History Management

Then:

```bash
git rebase
git revert
git reset
git restore
git stash
```

---

## Level 4 — Investigation

Then:

```bash
git show
git blame
git grep
git log --follow
git log -S
git log -G
```

---

## Level 5 — Recovery

Then:

```bash
git reflog
git fsck
git cherry-pick
git bisect
```

---

## Level 6 — Advanced Engineering

Finally:

```bash
git worktree
git rerere
git range-diff
git rev-list
git rev-parse
git cat-file
git ls-tree
```

---

# 33.35 Final Command Reference

The following is a compact high-value command list suitable for memorization.

| Command              | Description                | Example                           | Branch State Before and After command | Output                 |
| -------------------- | -------------------------- | --------------------------------- | ------------------------------------- | ---------------------- |
| `git status`         | Inspect repository state   | `git status`                      | Unchanged                             | Status                 |
| `git status -sb`     | Compact status             | `git status -sb`                  | Unchanged                             | Short status           |
| `git add`            | Stage changes              | `git add file`                    | Unstaged → staged                     | Usually none           |
| `git add -p`         | Interactively stage hunks  | `git add -p`                      | Partially staged                      | Interactive prompt     |
| `git commit`         | Create commit              | `git commit -m "Fix bug"`         | Current tip → new commit              | Commit summary         |
| `git commit --amend` | Replace latest commit      | `git commit --amend`              | Latest commit replaced                | New commit ID          |
| `git diff`           | Show unstaged changes      | `git diff`                        | Unchanged                             | Patch                  |
| `git diff --cached`  | Show staged changes        | `git diff --cached`               | Unchanged                             | Patch                  |
| `git diff --check`   | Check whitespace errors    | `git diff --check`                | Unchanged                             | Errors or none         |
| `git log`            | View history               | `git log --oneline`               | Unchanged                             | History                |
| `git show`           | Inspect object/commit      | `git show HEAD`                   | Unchanged                             | Commit details         |
| `git switch`         | Switch branch              | `git switch main`                 | Branch A → branch B                   | Usually none           |
| `git switch -c`      | Create branch              | `git switch -c feature/api`       | Current → new branch                  | Usually none           |
| `git branch`         | Manage branches            | `git branch -a`                   | Unchanged                             | Branch list            |
| `git fetch`          | Download refs              | `git fetch origin`                | Local branch unchanged                | Fetch status           |
| `git fetch --prune`  | Fetch and prune stale refs | `git fetch --prune`               | Local branch unchanged                | Fetch status           |
| `git pull`           | Fetch and integrate        | `git pull --ff-only`              | Branch may advance                    | Pull status            |
| `git push`           | Publish commits            | `git push`                        | Remote branch advances                | Push status            |
| `git merge`          | Combine histories          | `git merge feature/api`           | Current branch may advance            | Merge result           |
| `git rebase`         | Replay commits             | `git rebase origin/main`          | Commit IDs may change                 | Rebase result          |
| `git restore`        | Restore file contents      | `git restore file`                | Working tree changes removed          | Usually none           |
| `git reset`          | Move branch/index state    | `git reset --soft HEAD~1`         | Branch moves                          | Usually none           |
| `git revert`         | Undo with new commit       | `git revert HEAD`                 | New inverse commit                    | Commit created         |
| `git stash`          | Temporarily save work      | `git stash push -u`               | Working tree cleaned                  | Stash created          |
| `git tag`            | Manage tags                | `git tag -a v1.0.0 -m "Release"`  | Branch unchanged                      | Tag created            |
| `git cherry-pick`    | Apply commit               | `git cherry-pick abc123`          | New commit may be created             | Cherry-pick result     |
| `git reflog`         | Recover previous refs      | `git reflog`                      | Unchanged                             | Reference history      |
| `git bisect`         | Binary-search history      | `git bisect start`                | HEAD may move                         | Bisect state           |
| `git blame`          | Identify line authorship   | `git blame file.c`                | Unchanged                             | Line attribution       |
| `git grep`           | Search tracked files       | `git grep "TODO"`                 | Unchanged                             | Matching lines         |
| `git worktree`       | Manage multiple checkouts  | `git worktree add ../review HEAD` | Main branch unchanged                 | Worktree created       |
| `git clean`          | Remove untracked files     | `git clean -nd`                   | Preview only                          | Candidate files        |
| `git fsck`           | Check object database      | `git fsck --full`                 | Unchanged                             | Repository diagnostics |
| `git rev-parse`      | Resolve references         | `git rev-parse HEAD`              | Unchanged                             | SHA                    |
| `git rev-list`       | Enumerate commits          | `git rev-list main..feature`      | Unchanged                             | Commit IDs             |
| `git cat-file`       | Inspect Git objects        | `git cat-file -p HEAD`            | Unchanged                             | Object content         |
| `git ls-tree`        | Inspect tree objects       | `git ls-tree HEAD`                | Unchanged                             | Tree entries           |

---

# The 20 Commands Most Worth Memorizing

If you only memorize 20 commands, start here:

```bash
git status
git status -sb

git add
git add -p

git commit
git commit --amend

git diff
git diff --cached

git log --oneline --graph --decorate --all
git show

git branch
git switch
git switch -c

git fetch
git pull
git push

git merge
git rebase

git restore
git revert
```

Then add these recovery and advanced commands:

```bash
git stash
git cherry-pick
git reflog
git bisect
git worktree
git blame
git grep
git tag
git clean
git fsck
```

---

# The Git Mental Model to Memorize

Git becomes significantly easier once the role of each major command is understood.

```text
                    REMOTE REPOSITORY
                           ^
                           |
                         push
                           |
                           |
LOCAL REPOSITORY <------ fetch
       |
       |
       v
     branch
       |
       v
  working tree
       |
      diff
       |
       v
     staging area
       |
      add
       |
       v
     commit
       |
       v
    history
```

The most important relationships are:

```text
git add
    working tree
        ↓
    staging area

git commit
    staging area
        ↓
    repository history

git fetch
    remote repository
        ↓
    remote-tracking references

git push
    local commits
        ↓
    remote repository

git merge
    two histories
        ↓
    combined history

git rebase
    commits
        ↓
    replayed commits on new base

git revert
    existing commit
        ↓
    new inverse commit

git reflog
    reference movements
        ↓
    recovery information
```

---

# Final Memorization Strategy

Do not try to memorize Git as a collection of unrelated commands.

Memorize the workflow:

```bash
git status
git diff
git add
git diff --cached
git commit
git fetch
git rebase
git push
```

Then memorize the recovery workflow:

```bash
git status
git reflog
git show
git switch -c recovery <commit>
```

Finally, memorize the investigation workflow:

```bash
git log --oneline --graph --decorate --all
git show <commit>
git blame <file>
git log --follow -- <file>
git bisect start
```

These command groups provide a strong foundation for everyday development, code review, debugging, release engineering, and DevOps workflows.

---

# Next Part

**Next file:** `34-dangerous-commands.md`

[Next: Dangerous Commands](34-dangerous-commands.md)
