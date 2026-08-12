# 16. Cherry-Pick

`git cherry-pick` applies the changes introduced by one or more existing commits to the current branch.

It is useful when you need to selectively transfer specific changes without merging an entire branch.

Typical use cases:

* Backporting a bug fix to a release branch
* Applying a single feature commit to another branch
* Moving a hotfix between branches
* Applying selected commits without merging all branch history
* Recovering a useful commit from another development line

> **Important:** Cherry-picking creates a **new commit** on the current branch. The original commit remains unchanged.

---

# Table of Contents

* [16.1 Cherry-Pick Fundamentals](#161-cherry-pick-fundamentals)
* [16.2 Basic Cherry-Pick](#162-basic-cherry-pick)
* [16.3 Cherry-Pick a Commit from Another Branch](#163-cherry-pick-a-commit-from-another-branch)
* [16.4 Cherry-Pick Multiple Commits](#164-cherry-pick-multiple-commits)
* [16.5 Cherry-Pick a Range of Commits](#165-cherry-pick-a-range-of-commits)
* [16.6 Cherry-Pick Consecutive Commits](#166-cherry-pick-consecutive-commits)
* [16.7 Cherry-Pick Non-Consecutive Commits](#167-cherry-pick-non-consecutive-commits)
* [16.8 Cherry-Pick Without Committing](#168-cherry-pick-without-committing)
* [16.9 Cherry-Pick and Edit the Commit](#169-cherry-pick-and-edit-the-commit)
* [16.10 Cherry-Pick With an Explicit Commit Message](#1610-cherry-pick-with-an-explicit-commit-message)
* [16.11 Cherry-Pick a Merge Commit](#1611-cherry-pick-a-merge-commit)
* [16.12 The Mainline Parent](#1612-the-mainline-parent)
* [16.13 Cherry-Pick Conflicts](#1613-cherry-pick-conflicts)
* [16.14 Continue After a Conflict](#1614-continue-after-a-conflict)
* [16.15 Abort a Cherry-Pick](#1615-abort-a-cherry-pick)
* [16.16 Skip a Cherry-Pick](#1616-skip-a-cherry-pick)
* [16.17 Check Cherry-Pick State](#1617-check-cherry-pick-state)
* [16.18 Resolve Cherry-Pick Conflicts](#1618-resolve-cherry-pick-conflicts)
* [16.19 Cherry-Pick From a Remote Branch](#1619-cherry-pick-from-a-remote-branch)
* [16.20 Cherry-Pick From a Tag](#1620-cherry-pick-from-a-tag)
* [16.21 Cherry-Pick From Reflog](#1621-cherry-pick-from-reflog)
* [16.22 Cherry-Pick With Sign-Off](#1622-cherry-pick-with-sign-off)
* [16.23 Cherry-Pick With GPG Signing](#1623-cherry-pick-with-gpg-signing)
* [16.24 Cherry-Pick Strategy Options](#1624-cherry-pick-strategy-options)
* [16.25 Cherry-Pick and Empty Commits](#1625-cherry-pick-and-empty-commits)
* [16.26 Detect Already Applied Changes](#1626-detect-already-applied-changes)
* [16.27 Cherry-Pick and Duplicate Commits](#1627-cherry-pick-and-duplicate-commits)
* [16.28 Cherry-Pick vs Merge](#1628-cherry-pick-vs-merge)
* [16.29 Cherry-Pick vs Rebase](#1629-cherry-pick-vs-rebase)
* [16.30 Backporting a Bug Fix](#1630-backporting-a-bug-fix)
* [16.31 Hotfix Workflow](#1631-hotfix-workflow)
* [16.32 Release Branch Workflow](#1632-release-branch-workflow)
* [16.33 DevOps and CI/CD Use Cases](#1633-devops-and-cicd-use-cases)
* [16.34 Automation-Friendly Commands](#1634-automation-friendly-commands)
* [16.35 Dangerous Cherry-Pick Scenarios](#1635-dangerous-cherry-pick-scenarios)
* [16.36 Practical Cherry-Pick Workflow](#1636-practical-cherry-pick-workflow)
* [16.37 High-Value Cherry-Pick Commands](#1637-high-value-cherry-pick-commands)
* [16.38 Cherry-Pick Cheat Sheet](#1638-cherry-pick-cheat-sheet)

---

# 16.1 Cherry-Pick Fundamentals

Suppose the repository contains:

```text
A --- B --- C --- D        main
           \
            E --- F        feature
```

You are on `main` and want only commit `E`.

Run:

```bash
git cherry-pick E
```

The resulting history becomes:

```text
A --- B --- C --- D --- E'
```

Where:

```text
E
```

is the original commit, while:

```text
E'
```

is a new commit created by cherry-pick.

The content introduced by `E` is applied to the current branch.

---

# 16.2 Basic Cherry-Pick

Syntax:

```bash
git cherry-pick <commit>
```

Example:

```bash
git cherry-pick a1b2c3d
```

Before:

```text
A --- B --- C        main

           \
            D        feature
```

After:

```text
A --- B --- C --- D'
```

The branch pointer moves forward.

The original `D` remains where it was.

---

| Command                              | Description                      | Example                      | Branch State Before and After command | Output                     |
| ------------------------------------ | -------------------------------- | ---------------------------- | ------------------------------------- | -------------------------- |
| `git cherry-pick COMMIT`             | Apply one commit                 | `git cherry-pick a1b2c3d`    | `main -> C` becomes `main -> D'`      | New commit                 |
| `git cherry-pick --no-commit COMMIT` | Apply changes without committing | `git cherry-pick -n a1b2c3d` | Branch pointer unchanged              | Working tree/index changed |
| `git cherry-pick --edit COMMIT`      | Apply commit and edit message    | `git cherry-pick -e a1b2c3d` | Branch advances after commit          | Commit editor              |
| `git cherry-pick --continue`         | Continue after conflict          | `git cherry-pick --continue` | Operation continues                   | New commit or next commit  |
| `git cherry-pick --abort`            | Abort operation                  | `git cherry-pick --abort`    | Returns to pre-operation state        | Operation cancelled        |
| `git cherry-pick --skip`             | Skip current commit              | `git cherry-pick --skip`     | Current commit skipped                | Next operation step        |

---

# 16.3 Cherry-Pick a Commit from Another Branch

Suppose:

```text
main:
A --- B --- C

feature:
      \
       D --- E
```

Switch to `main`:

```bash
git switch main
```

Then apply `D`:

```bash
git cherry-pick D
```

Result:

```text
A --- B --- C --- D'
             \
              D
```

The exact graph depends on other references, but the important point is:

```text
D' = new commit on main
D  = original commit on feature
```

---

# 16.4 Cherry-Pick Multiple Commits

You can specify multiple commits:

```bash
git cherry-pick a1b2c3d d4e5f6a
```

Git applies them in the order specified.

For example:

```bash
git cherry-pick commitA commitB commitC
```

is different from:

```bash
git cherry-pick commitC commitB commitA
```

when the commits depend on one another.

Prefer chronological order when applying related commits.

---

# 16.5 Cherry-Pick a Range of Commits

Syntax:

```bash
git cherry-pick <oldest>^..<newest>
```

Example:

```bash
git cherry-pick a1b2c3d^..d4e5f6a
```

This includes:

```text
a1b2c3d
b2c3d4e
c3d4e5f
d4e5f6a
```

The `^` is important because:

```bash
git cherry-pick a1b2c3d..d4e5f6a
```

normally excludes `a1b2c3d`.

---

# 16.6 Cherry-Pick Consecutive Commits

Given:

```text
A --- B --- C --- D --- E
```

To cherry-pick:

```text
C
D
E
```

use:

```bash
git cherry-pick C^..E
```

Equivalent conceptual range:

```text
C through E, inclusive
```

This is commonly used for backporting a sequence of commits.

---

# 16.7 Cherry-Pick Non-Consecutive Commits

Example:

```text
A --- B --- C --- D --- E --- F
```

You only want:

```text
B
D
F
```

Run:

```bash
git cherry-pick B D F
```

The resulting branch receives new commits:

```text
B'
D'
F'
```

This is useful when only selected fixes should be transferred.

---

# 16.8 Cherry-Pick Without Committing

Use:

```bash
git cherry-pick --no-commit <commit>
```

Short form:

```bash
git cherry-pick -n <commit>
```

This applies the changes but does not create a commit.

Example:

```bash
git cherry-pick -n a1b2c3d
```

The state becomes:

```text
HEAD
 |
 v
Original commit

Index
 |
 v
Cherry-picked changes

Working Tree
 |
 v
Cherry-picked changes
```

You can then modify the result:

```bash
git diff
git diff --cached
```

and commit manually:

```bash
git commit
```

This is useful when combining multiple commits into one.

---

# 16.9 Cherry-Pick and Edit the Commit

Use:

```bash
git cherry-pick --edit <commit>
```

Short form:

```bash
git cherry-pick -e <commit>
```

Git opens the commit-message editor.

This allows you to modify the commit message before the new commit is created.

---

# 16.10 Cherry-Pick With an Explicit Commit Message

For more control, use:

```bash
git cherry-pick --no-commit <commit>
```

Then:

```bash
git commit -m "Backport authentication fix"
```

Example:

```bash
git cherry-pick -n a1b2c3d
git commit -m "Backport authentication fix"
```

This is preferable when the original commit message is not appropriate for the target branch.

---

# 16.11 Cherry-Pick a Merge Commit

Cherry-picking a normal commit is straightforward.

Cherry-picking a merge commit requires additional information.

Example:

```text
          D --- E
         /       \
A --- B --- C --- M
```

`M` has two parents:

```text
Parent 1 = C
Parent 2 = E
```

Running:

```bash
git cherry-pick M
```

will normally fail because Git cannot determine which parent should be considered the mainline.

You must specify one:

```bash
git cherry-pick -m 1 M
```

or:

```bash
git cherry-pick -m 2 M
```

---

# 16.12 The Mainline Parent

The `-m` option means:

```text
-m <parent-number>
```

For a merge commit:

```text
          E
         /
A --- B
         \
          C --- M
```

The parent numbering is:

```text
1 = first parent
2 = second parent
```

Inspect the merge commit:

```bash
git show --summary M
```

or:

```bash
git rev-list --parents -n 1 M
```

Example:

```text
M parent1 parent2
```

Then:

```bash
git cherry-pick -m 1 M
```

means:

> Treat parent 1 as the mainline and apply the changes introduced by the merge relative to that parent.

---

# 16.13 Cherry-Pick Conflicts

A conflict can occur when the target branch has diverged from the original context of the commit.

Example:

```bash
git cherry-pick a1b2c3d
```

Output:

```text
Auto-merging app.js
CONFLICT (content): Merge conflict in app.js
error: could not apply a1b2c3d...
```

Git pauses the operation.

Check:

```bash
git status
```

You may see:

```text
You are currently cherry-picking a commit.
Unmerged paths:
    both modified: app.js
```

---

# 16.14 Continue After a Conflict

First resolve the file:

```bash
nano app.js
```

Then stage it:

```bash
git add app.js
```

Continue:

```bash
git cherry-pick --continue
```

If there are multiple commits:

```text
Commit 1
   ↓
Conflict
   ↓
Resolve
   ↓
Continue
   ↓
Commit 2
   ↓
Conflict
   ↓
Resolve
   ↓
Continue
```

---

# 16.15 Abort a Cherry-Pick

To completely cancel the current cherry-pick operation:

```bash
git cherry-pick --abort
```

Git attempts to restore the state from before the cherry-pick sequence began.

This is the safest option when you decide:

> "I do not want to apply these commits."

---

# 16.16 Skip a Cherry-Pick

When cherry-picking a sequence:

```bash
git cherry-pick A^..D
```

a particular commit may be unnecessary or already effectively applied.

After resolving or deciding to skip it:

```bash
git cherry-pick --skip
```

Git continues with the remaining commits.

---

# 16.17 Check Cherry-Pick State

Use:

```bash
git status
```

You can also inspect the repository state:

```bash
git status --short
```

Check for unresolved files:

```bash
git diff --name-only --diff-filter=U
```

Check the current HEAD:

```bash
git log -1 --oneline
```

Git also maintains cherry-pick state internally while an operation is active.

---

# 16.18 Resolve Cherry-Pick Conflicts

Typical workflow:

```bash
git cherry-pick a1b2c3d
```

Conflict:

```text
CONFLICT
```

Inspect:

```bash
git status
git diff
```

Resolve:

```bash
nano app.js
```

Stage:

```bash
git add app.js
```

Continue:

```bash
git cherry-pick --continue
```

For an "ours" resolution:

```bash
git checkout --ours app.js
git add app.js
git cherry-pick --continue
```

For a "theirs" resolution:

```bash
git checkout --theirs app.js
git add app.js
git cherry-pick --continue
```

> During a cherry-pick, `ours` and `theirs` refer to the current target branch and the commit being applied, respectively. Always verify the result before continuing.

---

# 16.19 Cherry-Pick From a Remote Branch

First fetch the remote:

```bash
git fetch origin
```

Inspect the remote branch:

```bash
git log --oneline origin/feature
```

Then cherry-pick:

```bash
git cherry-pick origin/feature
```

Usually you want a specific commit:

```bash
git cherry-pick origin/feature~2
```

or:

```bash
git cherry-pick a1b2c3d
```

Fetching first ensures your remote-tracking references are current.

---

# 16.20 Cherry-Pick From a Tag

Tags point to specific Git objects, commonly commits.

Example:

```bash
git show v2.0.0
```

Find a commit:

```bash
git log --oneline v2.0.0
```

Then cherry-pick it:

```bash
git cherry-pick a1b2c3d
```

If the tag directly references a commit:

```bash
git cherry-pick v2.0.0
```

can apply that commit.

However, if your goal is to transfer an entire release, merging or another release-specific strategy may be more appropriate.

---

# 16.21 Cherry-Pick From Reflog

A commit can sometimes be recovered from the reflog:

```bash
git reflog
```

Example:

```text
a1b2c3d HEAD@{3}: commit: Fix authentication
```

Cherry-pick it:

```bash
git cherry-pick a1b2c3d
```

This is useful when a commit is no longer reachable from a normal branch reference but still exists in the repository.

---

# 16.22 Cherry-Pick With Sign-Off

Use:

```bash
git cherry-pick -s <commit>
```

Equivalent:

```bash
git cherry-pick --signoff <commit>
```

This adds a `Signed-off-by` line to the resulting commit message.

Example:

```text
Fix authentication

Signed-off-by: Developer <developer@example.com>
```

Use sign-off only when it matches the contribution policy of the project.

---

# 16.23 Cherry-Pick With GPG Signing

If commit signing is configured:

```bash
git cherry-pick -S <commit>
```

This signs the resulting commit.

Equivalent:

```bash
git cherry-pick --gpg-sign=<keyid> <commit>
```

Example:

```bash
git cherry-pick -S ABCDEF1234567890 a1b2c3d
```

The original commit's signature is not simply transferred. The cherry-picked commit is a new commit and therefore needs its own signature.

---

# 16.24 Cherry-Pick Strategy Options

You can pass merge strategy options using:

```bash
git cherry-pick -X <option> <commit>
```

For example:

```bash
git cherry-pick -X theirs a1b2c3d
```

or:

```bash
git cherry-pick -X ours a1b2c3d
```

These are strategy options and are not identical in meaning to:

```bash
git checkout --ours
git checkout --theirs
```

Use them carefully, especially when resolving complex conflicts.

---

# 16.25 Cherry-Pick and Empty Commits

A commit can become empty when its changes are already present in the target branch.

Example:

```bash
git cherry-pick a1b2c3d
```

Git may report:

```text
The previous cherry-pick is now empty
```

You can inspect the state:

```bash
git status
```

If the commit is unnecessary:

```bash
git cherry-pick --skip
```

If you intentionally want to create an empty commit, use:

```bash
git commit --allow-empty -m "Record backport"
```

---

# 16.26 Detect Already Applied Changes

Before cherry-picking, inspect the target branch:

```bash
git log --oneline main
```

Search by message:

```bash
git log --all --oneline --grep="authentication fix"
```

Search the patch content:

```bash
git log -p --all
```

Compare branches:

```bash
git log --oneline main..feature
```

You can also inspect patch equivalence using:

```bash
git range-diff
```

or patch IDs:

```bash
git show <commit> --pretty=email | git patch-id
```

---

# 16.27 Cherry-Pick and Duplicate Commits

After cherry-picking:

```text
Original:
A --- B --- C

Target:
A --- B --- C --- C'
```

`C` and `C'` can have:

* different commit IDs;
* equivalent changes;
* different parents;
* different metadata.

This is normal.

The commit hash changes because Git commits include information such as:

* Parent commit
* Author
* Committer
* Timestamp
* Commit message
* Tree

Changing the parent alone is enough to produce a different commit ID.

---

# 16.28 Cherry-Pick vs Merge

## Merge

```bash
git merge feature
```

Transfers the branch history as a whole.

Example:

```text
A --- B --- C -------- M
           \          /
            D --- E ---
```

Advantages:

* Preserves branch topology
* Transfers all relevant commits
* Appropriate for integrating a complete branch

---

## Cherry-Pick

```bash
git cherry-pick D
```

Transfers selected changes.

Example:

```text
A --- B --- C --- D'
           \
            D
```

Advantages:

* Selective
* Useful for backports
* Useful for hotfixes
* Useful for release branches

---

# 16.29 Cherry-Pick vs Rebase

## Rebase

```bash
git rebase main
```

Replays the commits of the current branch onto another base.

Use when:

* Updating a feature branch
* Cleaning branch history
* Preparing a branch for review

---

## Cherry-Pick

```bash
git cherry-pick <commit>
```

Copies selected changes to another branch.

Use when:

* Backporting fixes
* Moving individual commits
* Applying selected changes to release branches

---

# 16.30 Backporting a Bug Fix

A common enterprise workflow:

```text
main
 |
 v
Bug fix commit F

release/2.0
 |
 v
Older production code
```

Identify the fix:

```bash
git log --oneline main
```

Switch to release branch:

```bash
git switch release/2.0
```

Update it:

```bash
git pull --ff-only
```

Cherry-pick:

```bash
git cherry-pick F
```

Run tests:

```bash
./run-tests.sh
```

Push:

```bash
git push origin release/2.0
```

---

# 16.31 Hotfix Workflow

Example:

```text
main
 |
 F = production bug fix
 |
 v
```

A release branch also needs the same fix.

```bash
git switch release/2.0
git pull --ff-only
git cherry-pick F
```

If there is a conflict:

```bash
git status
```

Resolve:

```bash
git add .
git cherry-pick --continue
```

Test:

```bash
./run-tests.sh
```

Then:

```bash
git push origin release/2.0
```

---

# 16.32 Release Branch Workflow

Typical structure:

```text
main
 |
 +--- feature commits
 |
 +--- release/2.0
          |
          +--- stabilization
          +--- hotfix
```

A fix created on `main` may need to be backported:

```bash
git switch release/2.0
git fetch origin
git cherry-pick <fix-commit>
```

After validation:

```bash
git push origin release/2.0
```

---

# 16.33 DevOps and CI/CD Use Cases

Cherry-picking is useful in automated release workflows.

Example:

```text
Development
     |
     v
main
     |
     +---- bug fix
     |
     v
release branch
```

A CI/CD pipeline may:

1. Identify approved fix commit.
2. Create a temporary release branch.
3. Cherry-pick the fix.
4. Run automated tests.
5. Build artifacts.
6. Deploy to staging.
7. Promote to production.

Example shell sequence:

```bash
git fetch origin
git switch release/2.0
git pull --ff-only
git cherry-pick "$FIX_COMMIT"
git push origin release/2.0
```

For production automation, always validate that the commit is intended for the target release.

---

# 16.34 Automation-Friendly Commands

Dry-run-style inspection is not provided as a universal `git cherry-pick --dry-run` workflow, so inspect before applying.

List commits:

```bash
git log --oneline main..feature
```

Check current state:

```bash
git status --porcelain=v1
```

Apply without committing:

```bash
git cherry-pick --no-commit <commit>
```

Inspect:

```bash
git diff
git diff --cached
```

Then either commit:

```bash
git commit
```

or reset the uncommitted changes if appropriate.

Check unresolved files:

```bash
git diff --name-only --diff-filter=U
```

---

# 16.35 Dangerous Cherry-Pick Scenarios

Cherry-picking can create problems when used without understanding dependencies.

## Cherry-picking only part of a feature

Suppose:

```text
A = database migration
B = application code
C = tests
```

Cherry-picking only:

```bash
git cherry-pick B
```

may produce broken code because `B` depends on `A`.

Always inspect:

```bash
git show B
```

and:

```bash
git log --oneline B^..B
```

---

## Cherry-picking merge commits

Be careful with:

```bash
git cherry-pick -m 1 <merge-commit>
```

The selected parent determines which changes Git considers introduced by the merge.

---

## Repeated cherry-picking

Repeatedly moving commits between long-lived branches can produce:

* duplicate changes;
* complex histories;
* difficult conflict resolution;
* maintenance overhead.

Prefer a clear branch strategy.

---

# 16.36 Practical Cherry-Pick Workflow

## Step 1 — Identify the commit

```bash
git log --oneline --all
```

Example:

```text
a1b2c3d Fix authentication timeout
```

---

## Step 2 — Inspect it

```bash
git show a1b2c3d
```

---

## Step 3 — Inspect the target branch

```bash
git switch release/2.0
git status
```

The working tree should normally be clean.

---

## Step 4 — Update the branch

```bash
git fetch origin
git pull --ff-only
```

---

## Step 5 — Cherry-pick

```bash
git cherry-pick a1b2c3d
```

---

## Step 6 — Resolve conflicts if necessary

```bash
git status
git diff
```

Edit files, then:

```bash
git add <resolved-file>
```

Continue:

```bash
git cherry-pick --continue
```

---

## Step 7 — Verify the result

```bash
git show HEAD
```

Check history:

```bash
git log --oneline -5
```

Run tests:

```bash
./run-tests.sh
```

---

## Step 8 — Push

```bash
git push origin release/2.0
```

---

# 16.37 High-Value Cherry-Pick Commands

| Command                                | Description                             | Example                                | Branch State Before and After command | Output                    |
| -------------------------------------- | --------------------------------------- | -------------------------------------- | ------------------------------------- | ------------------------- |
| `git cherry-pick COMMIT`               | Apply one commit                        | `git cherry-pick a1b2c3d`              | `main -> C` becomes `main -> C'`      | New commit                |
| `git cherry-pick A B C`                | Apply several commits                   | `git cherry-pick a1b2c3d d4e5f6a`      | Branch advances for each commit       | New commits               |
| `git cherry-pick A^..B`                | Apply inclusive range                   | `git cherry-pick a1b2c3d^..d4e5f6a`    | Branch advances through range         | New commits               |
| `git cherry-pick -n COMMIT`            | Apply without creating commit           | `git cherry-pick -n a1b2c3d`           | Branch pointer unchanged              | Changes staged/applied    |
| `git cherry-pick -e COMMIT`            | Edit commit message                     | `git cherry-pick -e a1b2c3d`           | Branch advances after commit          | Commit editor             |
| `git cherry-pick -m 1 COMMIT`          | Cherry-pick merge commit using parent 1 | `git cherry-pick -m 1 a1b2c3d`         | Branch advances if successful         | New commit                |
| `git cherry-pick --continue`           | Continue after conflict                 | `git cherry-pick --continue`           | Operation resumes                     | Commit created/next step  |
| `git cherry-pick --skip`               | Skip current commit                     | `git cherry-pick --skip`               | Current commit omitted                | Operation resumes         |
| `git cherry-pick --abort`              | Cancel operation                        | `git cherry-pick --abort`              | Returns to pre-operation state        | Operation cancelled       |
| `git cherry-pick -s COMMIT`            | Add sign-off                            | `git cherry-pick -s a1b2c3d`           | Branch advances                       | Signed-off commit         |
| `git cherry-pick -S COMMIT`            | GPG-sign result                         | `git cherry-pick -S a1b2c3d`           | Branch advances                       | Signed commit             |
| `git cherry-pick -X OPTION COMMIT`     | Pass merge strategy option              | `git cherry-pick -X theirs a1b2c3d`    | Branch advances if successful         | Applied commit            |
| `git status`                           | Inspect operation state                 | `git status`                           | Branch unchanged                      | Current cherry-pick state |
| `git diff --name-only --diff-filter=U` | Show unresolved files                   | `git diff --name-only --diff-filter=U` | Branch unchanged                      | Conflict files            |
| `git show COMMIT`                      | Inspect source commit                   | `git show a1b2c3d`                     | Branch unchanged                      | Commit and patch          |
| `git reflog`                           | Find previous commits                   | `git reflog`                           | Branch unchanged                      | Reference history         |

---

# 16.38 Cherry-Pick Cheat Sheet

```bash
# Inspect commit
git show <commit>

# Inspect history
git log --oneline --all

# Switch to target branch
git switch <target-branch>

# Update target branch
git fetch origin
git pull --ff-only

# Apply one commit
git cherry-pick <commit>

# Apply multiple commits
git cherry-pick <commit1> <commit2> <commit3>

# Apply an inclusive range
git cherry-pick <oldest>^..<newest>

# Apply changes without committing
git cherry-pick --no-commit <commit>

# Edit resulting commit message
git cherry-pick --edit <commit>

# Cherry-pick a merge commit using first parent
git cherry-pick -m 1 <merge-commit>

# Check conflict state
git status

# Show unresolved files
git diff --name-only --diff-filter=U

# Show conflict
git diff

# Keep current branch version
git checkout --ours <file>

# Keep cherry-picked version
git checkout --theirs <file>

# Mark conflict resolved
git add <file>

# Continue
git cherry-pick --continue

# Skip current commit
git cherry-pick --skip

# Abort
git cherry-pick --abort

# Add sign-off
git cherry-pick --signoff <commit>

# Sign resulting commit
git cherry-pick --gpg-sign <commit>

# Apply strategy option
git cherry-pick -X <option> <commit>
```

---

# Cherry-Pick Mental Model

The most important concept is:

```text
Original commit:

A --- B --- C
          \
           D

Target branch:

A --- B --- X
```

After:

```bash
git cherry-pick D
```

the target becomes conceptually:

```text
A --- B --- X --- D'
          \
           D
```

Where:

```text
D  = original commit
D' = new commit
```

Therefore:

```text
Cherry-pick does NOT move the original commit.
Cherry-pick does NOT merge the branches.
Cherry-pick applies the changes and creates a new commit.
```

---

# Cherry-Pick Decision Guide

Use **cherry-pick** when:

```text
"I need this specific change on another branch."
```

Use **merge** when:

```text
"I want to integrate this branch."
```

Use **rebase** when:

```text
"I want to replay my branch on top of another base."
```

Use **cherry-pick --no-commit** when:

```text
"I want the changes but I need to modify or combine them before committing."
```

Use:

```bash
git cherry-pick --abort
```

when:

```text
"I no longer want to perform this cherry-pick operation."
```

---

# Final Quick Reference

```bash
# One commit
git cherry-pick <commit>

# Several commits
git cherry-pick <commit1> <commit2>

# Inclusive range
git cherry-pick <oldest>^..<newest>

# Without automatic commit
git cherry-pick -n <commit>

# Edit commit message
git cherry-pick -e <commit>

# Merge commit
git cherry-pick -m 1 <merge-commit>

# Inspect
git show <commit>

# Conflict status
git status

# Unresolved files
git diff --name-only --diff-filter=U

# Resolve
git add <file>
git cherry-pick --continue

# Skip
git cherry-pick --skip

# Abort
git cherry-pick --abort

# Sign-off
git cherry-pick -s <commit>

# GPG sign
git cherry-pick -S <commit>
```

---

## Next Part

**Next file:** `17-reflog-and-recovery.md`

[Next: Reflog & Recovery](17-reflog-and-recovery.md)
