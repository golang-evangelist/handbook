# 10. Undoing Changes

This chapter covers Git commands used to undo, restore, revert, reset, amend, recover, and selectively discard changes.

The commands in this chapter operate at different levels:

```text
Working Tree
     |
     v
Staging Area
     |
     v
Local Repository
     |
     v
Remote Repository
```

Understanding **which layer a command modifies** is essential before using it.

> **Important:** `restore`, `reset`, and `revert` are not interchangeable.
> `restore` primarily changes files/staging state, `reset` moves references and can rewrite local history, while `revert` creates a new commit that reverses an earlier commit.

---

## Table of Contents

* [10.1 Undoing Changes — Mental Model](#101-undoing-changes--mental-model)
* [10.2 Check Current State](#102-check-current-state)
* [10.3 Discard Working Tree Changes](#103-discard-working-tree-changes)
* [10.4 Restore a Deleted File](#104-restore-a-deleted-file)
* [10.5 Unstage a File](#105-unstage-a-file)
* [10.6 Unstage Everything](#106-unstage-everything)
* [10.7 Restore a File from HEAD](#107-restore-a-file-from-head)
* [10.8 Restore a File from Another Commit](#108-restore-a-file-from-another-commit)
* [10.9 Restore the Entire Working Tree](#109-restore-the-entire-working-tree)
* [10.10 Restore Staged and Working Tree Changes](#1010-restore-staged-and-working-tree-changes)
* [10.11 Reset — Overview](#1011-reset--overview)
* [10.12 Soft Reset](#1012-soft-reset)
* [10.13 Mixed Reset](#1013-mixed-reset)
* [10.14 Hard Reset](#1014-hard-reset)
* [10.15 Reset HEAD by One Commit](#1015-reset-head-by-one-commit)
* [10.16 Reset to a Specific Commit](#1016-reset-to-a-specific-commit)
* [10.17 Reset a File](#1017-reset-a-file)
* [10.18 Revert a Commit](#1018-revert-a-commit)
* [10.19 Revert Multiple Commits](#1019-revert-multiple-commits)
* [10.20 Revert a Merge Commit](#1020-revert-a-merge-commit)
* [10.21 Amend the Last Commit](#1021-amend-the-last-commit)
* [10.22 Change the Last Commit Message](#1022-change-the-last-commit-message)
* [10.23 Add Changes to the Previous Commit](#1023-add-changes-to-the-previous-commit)
* [10.24 Remove a File from the Last Commit](#1024-remove-a-file-from-the-last-commit)
* [10.25 Undo a Public Commit](#1025-undo-a-public-commit)
* [10.26 Undo a Pushed Commit Safely](#1026-undo-a-pushed-commit-safely)
* [10.27 Undo a Local Commit](#1027-undo-a-local-commit)
* [10.28 Recover from an Incorrect Reset](#1028-recover-from-an-incorrect-reset)
* [10.29 Recover Deleted Work](#1029-recover-deleted-work)
* [10.30 Undo a Merge](#1030-undo-a-merge)
* [10.31 Undo a Rebase](#1031-undo-a-rebase)
* [10.32 Abort Operations](#1032-abort-operations)
* [10.33 Safe Undo Workflow](#1033-safe-undo-workflow)
* [10.34 Dangerous Undo Commands](#1034-dangerous-undo-commands)
* [10.35 High-Value Undo Commands](#1035-high-value-undo-commands)

---

# 10.1 Undoing Changes — Mental Model

Git has several important states:

```text
Working Tree
    |
    | git add
    v
Staging Area
    |
    | git commit
    v
Repository / HEAD
```

Different commands undo changes at different levels.

### Working tree

Changes that have not been staged:

```bash
git restore file.txt
```

### Staging area

Changes that have been staged:

```bash
git restore --staged file.txt
```

### Local commit history

Move the branch reference:

```bash
git reset HEAD~1
```

### Public/shared history

Create a new inverse commit:

```bash
git revert <commit>
```

A simplified rule:

```text
Uncommitted file change
    -> git restore

Staged change
    -> git restore --staged

Local commit you want to rewrite
    -> git reset

Published commit that should be undone
    -> git revert
```

---

# 10.2 Check Current State

Before undoing anything, inspect the repository:

```bash
git status
```

More detailed:

```bash
git status --short
```

Inspect recent history:

```bash
git log --oneline --decorate -10
```

Inspect differences:

```bash
git diff
```

Inspect staged differences:

```bash
git diff --cached
```

A good safety sequence is:

```bash
git status
git diff
git diff --cached
git log --oneline --decorate -10
```

---

| Command                 | Description           | Example                 | Branch State Before and After command | Output                      |
| ----------------------- | --------------------- | ----------------------- | ------------------------------------- | --------------------------- |
| `git status`            | Show repository state | `git status`            | No branch change                      | Working tree/staging status |
| `git status --short`    | Compact status        | `git status --short`    | No branch change                      | Short status codes          |
| `git diff`              | Show unstaged changes | `git diff`              | No branch change                      | Patch                       |
| `git diff --cached`     | Show staged changes   | `git diff --cached`     | No branch change                      | Staged patch                |
| `git log --oneline -10` | Show recent commits   | `git log --oneline -10` | No branch change                      | Commit list                 |

---

# 10.3 Discard Working Tree Changes

If a tracked file has local modifications that have **not** been staged:

```bash
git restore file.txt
```

Before:

```text
HEAD/file.txt:
version A

Working tree:
version B
```

After:

```text
HEAD/file.txt:
version A

Working tree:
version A
```

The uncommitted modification is discarded.

---

## Restore multiple files

```bash
git restore file1.txt file2.txt
```

---

## Restore all tracked working-tree changes

```bash
git restore .
```

This discards unstaged modifications in the current directory.

---

> **Warning:** `git restore` can permanently discard uncommitted work. Make sure the changes are not needed before running it.

---

# 10.4 Restore a Deleted File

If a tracked file was deleted but the deletion has not been committed:

```bash
git restore file.txt
```

Example:

```text
Before:

D  config.yaml
```

Run:

```bash
git restore config.yaml
```

After:

```text
config.yaml
```

The file is restored from the index/HEAD state.

---

# 10.5 Unstage a File

Suppose:

```bash
git add app.py
```

was executed, but you do not want `app.py` staged anymore.

Use:

```bash
git restore --staged app.py
```

Before:

```text
Changes to be committed:
    modified: app.py
```

After:

```text
Changes not staged for commit:
    modified: app.py
```

The file remains modified in the working tree.

Only its staging state changes.

---

## Older equivalent

A commonly seen older command is:

```bash
git reset HEAD app.py
```

Modern Git generally favors:

```bash
git restore --staged app.py
```

for this purpose.

---

# 10.6 Unstage Everything

Unstage all staged changes:

```bash
git restore --staged .
```

This leaves the changes in the working tree.

Example:

```text
Before:

M  app.py
M  config.yaml
```

After:

```text
 M app.py
 M config.yaml
```

The modifications remain; they are simply no longer staged.

---

| Command                       | Description                    | Example                       | Branch State Before and After command                | Output        |
| ----------------------------- | ------------------------------ | ----------------------------- | ---------------------------------------------------- | ------------- |
| `git restore --staged <file>` | Unstage a file                 | `git restore --staged app.py` | Branch unchanged; file moves from staged to unstaged | Usually none  |
| `git restore --staged .`      | Unstage all files              | `git restore --staged .`      | Branch unchanged; staging cleared                    | Usually none  |
| `git reset HEAD <file>`       | Older equivalent for unstaging | `git reset HEAD app.py`       | Branch unchanged; file unstaged                      | Reset summary |

---

# 10.7 Restore a File from HEAD

Restore a file to the version currently pointed to by `HEAD`:

```bash
git restore --source=HEAD -- file.txt
```

Shorter:

```bash
git restore file.txt
```

The explicit form is useful because it clearly documents the source.

Example:

```text
HEAD:
A---B

Working tree:
modified file.txt
```

Run:

```bash
git restore --source=HEAD -- file.txt
```

Result:

```text
Working tree = HEAD version
```

---

# 10.8 Restore a File from Another Commit

You can restore a file from any commit:

```bash
git restore --source=<commit> -- <file>
```

Example:

```bash
git restore --source=HEAD~2 -- config.yaml
```

Or:

```bash
git restore --source=a1b2c3d -- config.yaml
```

This does **not** move the branch.

It replaces the working-tree version of the specified file with the version from the specified commit.

Then you can inspect it:

```bash
git diff
```

and optionally stage it:

```bash
git add config.yaml
```

---

## Restore a file from another branch

```bash
git restore --source=main -- config.yaml
```

Or:

```bash
git restore --source=feature/old-config -- config.yaml
```

---

# 10.9 Restore the Entire Working Tree

Discard unstaged changes to tracked files:

```bash
git restore .
```

To explicitly restore from `HEAD`:

```bash
git restore --source=HEAD -- .
```

This does not remove untracked files.

For example:

```text
modified tracked file -> restored
untracked file        -> remains
```

---

# 10.10 Restore Staged and Working Tree Changes

To restore both index and working tree to `HEAD`:

```bash
git restore --staged --worktree .
```

Equivalent shorthand:

```bash
git restore -SW .
```

This is destructive for staged and unstaged changes.

Afterward:

```text
HEAD
 |
 +-- index = HEAD
 |
 +-- working tree = HEAD
```

---

# 10.11 Reset — Overview

`git reset` has several modes.

The three most important are:

```text
--soft
--mixed
--hard
```

They differ in what happens to:

* `HEAD`
* the index
* the working tree

---

## Reset behavior

### Soft

```text
HEAD     -> moved
Index    -> unchanged
Working  -> unchanged
```

### Mixed

```text
HEAD     -> moved
Index    -> reset
Working  -> unchanged
```

### Hard

```text
HEAD     -> moved
Index    -> reset
Working  -> reset
```

This distinction is fundamental.

---

# 10.12 Soft Reset

Command:

```bash
git reset --soft HEAD~1
```

Suppose:

```text
A---B---C  HEAD
```

After:

```bash
git reset --soft HEAD~1
```

you get:

```text
A---B  HEAD

C's changes:
    staged
```

The commit `C` is removed from the current branch, but its changes remain staged.

This is useful when you want to redo the last commit.

---

## Typical workflow

```bash
git reset --soft HEAD~1
git commit -m "Improved commit message"
```

---

# 10.13 Mixed Reset

Default reset mode:

```bash
git reset HEAD~1
```

Equivalent:

```bash
git reset --mixed HEAD~1
```

Before:

```text
A---B---C  HEAD
```

After:

```text
A---B  HEAD

C's changes:
    working tree
    not staged
```

The changes from `C` remain in the working tree but are removed from the staging area.

---

## Typical use

You accidentally created a commit too early:

```bash
git reset HEAD~1
```

Then modify:

```bash
git add specific-file.txt
git commit -m "Better commit"
```

---

# 10.14 Hard Reset

Command:

```bash
git reset --hard HEAD~1
```

Before:

```text
A---B---C  HEAD
```

After:

```text
A---B  HEAD
```

The branch moves backward and the working tree/index are updated to match `B`.

The changes introduced by `C` are removed from the current branch and working tree.

---

> **Warning:** `git reset --hard` is one of the most destructive everyday Git commands.

Before using it, consider:

```bash
git branch backup-before-reset
```

or:

```bash
git stash push -u -m "backup before reset"
```

---

# 10.15 Reset HEAD by One Commit

Move the current branch back one commit:

```bash
git reset --soft HEAD~1
```

or:

```bash
git reset --mixed HEAD~1
```

or:

```bash
git reset --hard HEAD~1
```

The correct option depends on what should happen to the changes.

| Command                    | HEAD  | Index     | Working Tree |
| -------------------------- | ----- | --------- | ------------ |
| `git reset --soft HEAD~1`  | Moves | Preserved | Preserved    |
| `git reset --mixed HEAD~1` | Moves | Reset     | Preserved    |
| `git reset --hard HEAD~1`  | Moves | Reset     | Reset        |

---

# 10.16 Reset to a Specific Commit

Find the commit:

```bash
git log --oneline
```

Example:

```text
9f8e7d6 Add monitoring
7c6b5a4 Add API
3d2c1b0 Initial version
```

Reset to:

```bash
git reset --hard 7c6b5a4
```

Result:

```text
3d2c1b0---7c6b5a4  HEAD
```

The later commit is no longer referenced by the current branch.

It may still be recoverable through the reflog.

---

# 10.17 Reset a File

Modern Git:

```bash
git restore --staged file.txt
```

Older reset form:

```bash
git reset HEAD file.txt
```

This resets the file's staging state without moving `HEAD`.

For example:

```text
Before:

M  file.txt
```

After:

```text
 M file.txt
```

The file is still modified, but no longer staged.

---

# 10.18 Revert a Commit

`git revert` is the preferred method for undoing a commit that has already been shared.

Example:

```bash
git revert abc1234
```

Suppose:

```text
A---B---C
        ^
       bad
```

After:

```bash
git revert C
```

history becomes:

```text
A---B---C---D
            ^
        inverse of C
```

The original commit remains in history.

A new commit reverses its changes.

---

## Why revert is safer for shared history

If `C` has already been pushed:

```bash
git push
```

rewriting history with:

```bash
git reset --hard B
git push --force
```

can disrupt other developers.

Instead:

```bash
git revert C
git push
```

preserves the existing public history.

---

# 10.19 Revert Multiple Commits

Revert multiple commits:

```bash
git revert <commit1> <commit2>
```

Example:

```bash
git revert abc1234 def5678
```

Git may create separate revert commits.

---

## Revert a range

You can specify a revision range:

```bash
git revert A..B
```

Be careful with range semantics and ordering.

For example:

```bash
git revert HEAD~3..HEAD
```

targets commits after `HEAD~3` through `HEAD`.

Inspect the intended commits first:

```bash
git log --oneline HEAD~3..HEAD
```

---

## Revert without immediately committing

```bash
git revert --no-commit <commit>
```

or:

```bash
git revert -n <commit>
```

This applies the inverse changes to the working tree/index without creating the revert commit immediately.

You can then inspect:

```bash
git diff --cached
```

and commit manually:

```bash
git commit -m "Revert problematic change"
```

---

# 10.20 Revert a Merge Commit

A merge commit has multiple parents.

Example:

```text
      C---D
     /     \
A---B       M
     \     /
      E---F
```

To revert merge commit `M`:

```bash
git revert -m 1 M
```

`-m 1` tells Git which parent should be considered the mainline.

This is an advanced operation.

Before reverting a merge:

```bash
git show --summary M
```

Inspect its parents and understand which side should remain.

---

# 10.21 Amend the Last Commit

Modify the most recent commit:

```bash
git add changed-file.txt
git commit --amend
```

Git opens the commit-message editor.

To keep the existing message:

```bash
git commit --amend --no-edit
```

Example:

```text
Before:

A---B
    ^
   HEAD

working tree:
modified file
```

After:

```bash
git add .
git commit --amend --no-edit
```

the latest commit `B` is replaced by a new commit containing the additional changes.

The commit ID changes.

---

# 10.22 Change the Last Commit Message

Use:

```bash
git commit --amend
```

Or:

```bash
git commit --amend -m "Correct commit message"
```

The previous commit is replaced.

Example:

```bash
git commit --amend -m "Fix authentication timeout"
```

---

> If the original commit has already been pushed, amending it rewrites public history and normally requires a force push. Coordinate with the team before doing this.

---

# 10.23 Add Changes to the Previous Commit

Suppose the last commit was:

```text
A---B  HEAD
```

You discover another change belongs in `B`.

Run:

```bash
git add file.txt
git commit --amend --no-edit
```

Result:

```text
A---B'
```

`B'` contains the original commit plus the new staged changes.

---

# 10.24 Remove a File from the Last Commit

Suppose the last commit accidentally included:

```text
secret.txt
```

If the commit has not been pushed:

```bash
git reset --soft HEAD~1
git restore --staged secret.txt
git commit -c ORIG_HEAD
```

Alternatively, for a simpler correction:

```bash
git rm --cached secret.txt
git commit --amend --no-edit
```

This removes the file from the commit while retaining it locally.

If the file contains sensitive credentials and has already been pushed, removing it from the latest commit alone is **not sufficient**. The secret should be revoked/rotated, and repository history may need dedicated history rewriting.

---

# 10.25 Undo a Public Commit

For a public/shared branch:

```bash
git revert <commit>
```

Then:

```bash
git push
```

Example:

```bash
git revert abc1234
git push origin main
```

History:

```text
A---B---C---D
        ^   ^
       bad  revert
```

The original commit remains visible.

This is normally preferable to rewriting shared history.

---

# 10.26 Undo a Pushed Commit Safely

If a commit is already on:

```text
origin/main
```

use:

```bash
git revert <commit>
```

Then:

```bash
git push origin main
```

For example:

```bash
git revert HEAD
git push origin main
```

This creates a new commit.

---

# 10.27 Undo a Local Commit

If the commit has **not** been pushed, you have more options.

Keep changes staged:

```bash
git reset --soft HEAD~1
```

Keep changes unstaged:

```bash
git reset HEAD~1
```

Discard changes:

```bash
git reset --hard HEAD~1
```

Choose based on what you want to preserve.

---

# 10.28 Recover from an Incorrect Reset

Suppose:

```bash
git reset --hard HEAD~3
```

was accidentally executed.

First inspect:

```bash
git reflog
```

You may see:

```text
abc1234 HEAD@{0}: reset: moving to HEAD~3
def5678 HEAD@{1}: commit: Important work
```

Recover:

```bash
git reset --hard def5678
```

A safer workflow is to first create a recovery branch:

```bash
git branch recovery-point def5678
```

Then inspect it:

```bash
git log --oneline recovery-point
```

---

# 10.29 Recover Deleted Work

If work was committed and later became unreachable:

```bash
git reflog
```

Find the relevant commit:

```bash
git show <commit>
```

Create a recovery branch:

```bash
git branch recovered-work <commit>
```

Then switch to it:

```bash
git switch recovered-work
```

This is one of the main reasons the reflog is so valuable.

---

## Important limitation

The reflog is local repository data.

It is not a remote backup.

Do not rely on reflog as a permanent archival mechanism.

---

# 10.30 Undo a Merge

There are two fundamentally different scenarios.

## Merge has not been committed

If a merge is currently in progress:

```bash
git merge --abort
```

This attempts to restore the pre-merge state.

---

## Merge has already been committed

Use:

```bash
git revert -m 1 <merge-commit>
```

Example:

```bash
git revert -m 1 abc1234
```

Then push:

```bash
git push
```

---

## Local merge that should simply disappear

If the merge commit is local and has not been shared:

```bash
git reset --hard HEAD~1
```

Only use this when rewriting local history is appropriate.

---

# 10.31 Undo a Rebase

If a rebase is currently running:

```bash
git rebase --abort
```

If the rebase has completed, inspect:

```bash
git reflog
```

Find the commit/reference from before the rebase:

```bash
git reflog
```

Example:

```text
abc1111 HEAD@{0}: rebase (finish)
def2222 HEAD@{1}: rebase (start)
ghi3333 HEAD@{5}: checkout: moving from main
```

Create a safety branch:

```bash
git branch before-rebase-recovery <commit>
```

Then, if appropriate:

```bash
git reset --hard <commit>
```

---

# 10.32 Abort Operations

Git provides operation-specific abort commands.

## Abort merge

```bash
git merge --abort
```

## Abort rebase

```bash
git rebase --abort
```

## Abort cherry-pick

```bash
git cherry-pick --abort
```

## Abort revert

```bash
git revert --abort
```

## Abort am/rebase-like operation

Depending on the Git operation and version, inspect:

```bash
git status
```

Git normally tells you which command can continue, skip, or abort the current operation.

---

| Command                   | Description                | Example                   | Branch State Before and After command       | Output              |
| ------------------------- | -------------------------- | ------------------------- | ------------------------------------------- | ------------------- |
| `git merge --abort`       | Abort in-progress merge    | `git merge --abort`       | Attempts to return to pre-merge state       | Abort/status output |
| `git rebase --abort`      | Abort rebase               | `git rebase --abort`      | Attempts to return to pre-rebase state      | Abort/status output |
| `git cherry-pick --abort` | Abort cherry-pick sequence | `git cherry-pick --abort` | Attempts to return to pre-cherry-pick state | Abort/status output |
| `git revert --abort`      | Abort revert sequence      | `git revert --abort`      | Attempts to return to pre-revert state      | Abort/status output |

---

# 10.33 Safe Undo Workflow

Before destructive operations:

```bash
git status
```

Then:

```bash
git diff
git diff --cached
```

Inspect recent history:

```bash
git log --oneline --decorate --graph -20
```

If you are about to move a branch:

```bash
git branch backup-before-undo
```

If the branch is shared:

```bash
git fetch origin
git branch -vv
```

Determine whether your branch has already been pushed.

---

## Safe decision tree

```text
What do I want to undo?
        |
        +-- Unstaged file change
        |      |
        |      +-- git restore <file>
        |
        +-- Staged change
        |      |
        |      +-- git restore --staged <file>
        |
        +-- Local commit
        |      |
        |      +-- git reset --soft
        |      +-- git reset --mixed
        |      +-- git reset --hard
        |
        +-- Shared/public commit
        |      |
        |      +-- git revert <commit>
        |
        +-- Running merge/rebase
        |      |
        |      +-- git merge --abort
        |      +-- git rebase --abort
        |
        +-- Accidentally moved branch
               |
               +-- git reflog
```

---

# 10.34 Dangerous Undo Commands

## `git restore .`

Can discard unstaged changes:

```bash
git restore .
```

---

## `git restore --staged --worktree .`

Can discard staged and unstaged changes:

```bash
git restore --staged --worktree .
```

---

## `git reset --hard`

Can discard commits from the current branch and working-tree changes:

```bash
git reset --hard HEAD~1
```

---

## `git clean -fd`

Although covered more extensively elsewhere, this is relevant to undo operations.

```bash
git clean -fd
```

It removes untracked files and directories.

Preview first:

```bash
git clean -nd
```

---

## `git push --force`

Can rewrite remote history:

```bash
git push --force
```

Prefer:

```bash
git push --force-with-lease
```

when force pushing is genuinely necessary.

---

# 10.35 High-Value Undo Commands

| Command                                   | Description                                    | Example                                 | Branch State Before and After command      | Output                |
| ----------------------------------------- | ---------------------------------------------- | --------------------------------------- | ------------------------------------------ | --------------------- |
| `git restore <file>`                      | Discard unstaged changes to file               | `git restore app.py`                    | Branch unchanged; working file restored    | Usually none          |
| `git restore .`                           | Discard unstaged tracked changes               | `git restore .`                         | Branch unchanged; working tree restored    | Usually none          |
| `git restore --staged <file>`             | Unstage file                                   | `git restore --staged app.py`           | Branch unchanged; file becomes unstaged    | Usually none          |
| `git restore --staged .`                  | Unstage everything                             | `git restore --staged .`                | Branch unchanged; index reset              | Usually none          |
| `git restore --source=<commit> -- <file>` | Restore file from commit                       | `git restore --source=HEAD~2 -- app.py` | Branch unchanged; file content replaced    | Usually none          |
| `git reset --soft HEAD~1`                 | Remove last commit but preserve staged changes | `git reset --soft HEAD~1`               | Branch moves back; changes remain staged   | Reset summary         |
| `git reset HEAD~1`                        | Remove last commit and unstage changes         | `git reset HEAD~1`                      | Branch moves back; changes remain unstaged | Reset summary         |
| `git reset --hard HEAD~1`                 | Remove last commit and reset files             | `git reset --hard HEAD~1`               | Branch and working tree move back          | Reset summary         |
| `git reset --hard <commit>`               | Move branch to exact commit                    | `git reset --hard abc1234`              | Branch moves; index/worktree match commit  | Reset summary         |
| `git revert <commit>`                     | Create inverse commit                          | `git revert abc1234`                    | New commit reverses target commit          | Commit created        |
| `git revert --no-commit <commit>`         | Apply inverse without committing               | `git revert --no-commit abc1234`        | Working/index contain inverse changes      | Usually status output |
| `git commit --amend`                      | Replace latest commit                          | `git commit --amend`                    | Latest commit replaced with new commit     | Commit output         |
| `git commit --amend --no-edit`            | Amend without changing message                 | `git commit --amend --no-edit`          | Latest commit replaced                     | Commit output         |
| `git merge --abort`                       | Abort merge                                    | `git merge --abort`                     | Attempts pre-merge state                   | Abort result          |
| `git rebase --abort`                      | Abort rebase                                   | `git rebase --abort`                    | Attempts pre-rebase state                  | Abort result          |
| `git cherry-pick --abort`                 | Abort cherry-pick                              | `git cherry-pick --abort`               | Attempts pre-cherry-pick state             | Abort result          |
| `git reflog`                              | Find previous HEAD positions                   | `git reflog`                            | No branch change                           | Reference history     |
| `git branch recovery <commit>`            | Create recovery branch                         | `git branch recovery abc1234`           | New branch reference created               | Usually none          |
| `git reset --hard <reflog-ref>`           | Recover branch position                        | `git reset --hard HEAD@{3}`             | Branch moves to recovered position         | Reset summary         |

---

# Undo Scenarios

## Scenario 1 — "I changed a file and want to discard it"

```bash
git restore file.txt
```

---

## Scenario 2 — "I accidentally staged a file"

```bash
git restore --staged file.txt
```

---

## Scenario 3 — "I committed too early but want to keep the changes staged"

```bash
git reset --soft HEAD~1
```

---

## Scenario 4 — "I committed too early and want the changes unstaged"

```bash
git reset HEAD~1
```

---

## Scenario 5 — "I committed something locally and want to completely remove it"

```bash
git reset --hard HEAD~1
```

Use only if the changes are genuinely disposable.

---

## Scenario 6 — "I pushed a bad commit"

```bash
git revert <commit>
git push
```

---

## Scenario 7 — "I accidentally reset my branch"

```bash
git reflog
```

Then:

```bash
git branch recovery <correct-commit>
```

and inspect the recovery branch before moving the original branch.

---

## Scenario 8 — "I am in the middle of a bad merge"

```bash
git merge --abort
```

---

## Scenario 9 — "I am in the middle of a bad rebase"

```bash
git rebase --abort
```

---

## Scenario 10 — "I amended a commit that was already pushed"

The commit history has been rewritten.

Inspect:

```bash
git log --oneline --decorate -10
git reflog
git branch -vv
```

If the branch is intentionally rewritten and the team permits it:

```bash
git push --force-with-lease
```

Do not blindly use:

```bash
git push --force
```

---

# `restore` vs `reset` vs `revert`

| Command             | Main Purpose                         | Changes History? | Changes Working Tree? | Safe for Shared History? |
| ------------------- | ------------------------------------ | ---------------: | --------------------: | -----------------------: |
| `git restore`       | Restore files/index                  |               No |                   Yes |                      Yes |
| `git reset --soft`  | Move branch while preserving changes |              Yes |                    No |               Usually no |
| `git reset --mixed` | Move branch and reset index          |              Yes |            Usually no |               Usually no |
| `git reset --hard`  | Move branch and discard local state  |              Yes |                   Yes |                       No |
| `git revert`        | Create inverse commit                |               No |  Yes during operation |                      Yes |

---

# Final Remote/Public History Rule

When deciding between `reset` and `revert`:

```text
Has the commit been shared?
        |
        +-- NO
        |    |
        |    +-- reset can be appropriate
        |
        +-- YES
             |
             +-- revert is usually appropriate
```

For example:

```bash
# Local-only mistake
git reset --soft HEAD~1
```

versus:

```bash
# Shared/public mistake
git revert HEAD
git push
```

---

# Practical Recovery Rule

When unsure:

```bash
git status
git reflog
git log --oneline --decorate --graph --all
```

Before a destructive operation, create a backup reference:

```bash
git branch backup-before-undo
```

Git references are cheap. A temporary backup branch is often safer than trying to reconstruct lost work afterward.

---

## Next Part

**Next file:** `11-stash.md`

[Next: Stash](11-stash.md)
