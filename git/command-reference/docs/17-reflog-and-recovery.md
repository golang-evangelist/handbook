# 17. Reflog & Recovery

`git reflog` records updates to local Git references such as `HEAD` and branch references.

It is one of the most important Git recovery mechanisms because commits that appear to be "lost" are often still reachable through the reflog.

Typical recovery scenarios include:

* Recovering a deleted branch
* Recovering a reset commit
* Recovering commits after an accidental rebase
* Recovering commits after an accidental merge
* Finding previous `HEAD` positions
* Recovering from `git reset --hard`
* Finding dangling commits
* Inspecting reference movement
* Recovering accidentally deleted work

> **Important:** The reflog is primarily a **local repository mechanism**. It is not a replacement for a remote repository or backup system.

---

# Table of Contents

* [17.1 What Is Reflog?](#171-what-is-reflog)
* [17.2 HEAD Reflog](#172-head-reflog)
* [17.3 Branch Reflog](#173-branch-reflog)
* [17.4 View Reflog](#174-view-reflog)
* [17.5 Reflog Entry Format](#175-reflog-entry-format)
* [17.6 HEAD@{N} Syntax](#176-headn-syntax)
* [17.7 Time-Based Reflog References](#177-time-based-reflog-references)
* [17.8 Recovering a Deleted Branch](#178-recovering-a-deleted-branch)
* [17.9 Recovering After git reset --hard](#179-recovering-after-git-reset---hard)
* [17.10 Recovering After git reset --soft](#1710-recovering-after-git-reset---soft)
* [17.11 Recovering After git reset --mixed](#1711-recovering-after-git-reset---mixed)
* [17.12 Recovering a Lost Commit](#1712-recovering-a-lost-commit)
* [17.13 Recovering After Rebase](#1713-recovering-after-rebase)
* [17.14 Recovering After Amend](#1714-recovering-after-amend)
* [17.15 Recovering After Force Push](#1715-recovering-after-force-push)
* [17.16 Inspecting Reflog Entries](#1716-inspecting-reflog-entries)
* [17.17 Using git show for Recovery](#1717-using-git-show-for-recovery)
* [17.18 Using git branch for Recovery](#1718-using-git-branch-for-recovery)
* [17.19 Using git reset for Recovery](#1719-using-git-reset-for-recovery)
* [17.20 Using git switch for Recovery](#1720-using-git-switch-for-recovery)
* [17.21 Recovering a Commit With Cherry-Pick](#1721-recovering-a-commit-with-cherry-pick)
* [17.22 Dangling Objects](#1722-dangling-objects)
* [17.23 git fsck](#1723-git-fsck)
* [17.24 Finding Dangling Commits](#1724-finding-dangling-commits)
* [17.25 Lost and Found Objects](#1725-lost-and-found-objects)
* [17.26 Reflog Expiration](#1726-reflog-expiration)
* [17.27 Reflog and Garbage Collection](#1727-reflog-and-garbage-collection)
* [17.28 Recovering Files](#1728-recovering-files)
* [17.29 Recovering a Deleted Branch With Remote Information](#1729-recovering-a-deleted-branch-with-remote-information)
* [17.30 Recovery Workflow](#1730-recovery-workflow)
* [17.31 Recovery Decision Tree](#1731-recovery-decision-tree)
* [17.32 Dangerous Recovery Commands](#1732-dangerous-recovery-commands)
* [17.33 DevOps Recovery Scenarios](#1733-devops-recovery-scenarios)
* [17.34 Automation and Diagnostics](#1734-automation-and-diagnostics)
* [17.35 High-Value Recovery Commands](#1735-high-value-recovery-commands)
* [17.36 Recovery Cheat Sheet](#1736-recovery-cheat-sheet)

---

# 17.1 What Is Reflog?

Git references move over time.

For example:

```text
A --- B --- C
          ^
          main
```

After:

```bash
git reset --hard B
```

the branch becomes:

```text
A --- B
      ^
      main

      C
```

Commit `C` may no longer be reachable from `main`.

However, the reflog can contain the previous position:

```text
HEAD@{1}: reset: moving to B
HEAD@{2}: commit: C
```

This allows you to find `C` again.

---

# 17.2 HEAD Reflog

Display the `HEAD` reflog:

```bash
git reflog
```

Example:

```text
e4f7a11 HEAD@{0}: reset: moving to HEAD~1
8c92b30 HEAD@{1}: commit: Add authentication
5d31a44 HEAD@{2}: commit: Add API endpoint
```

The newest entry is:

```text
HEAD@{0}
```

The previous entry is:

```text
HEAD@{1}
```

and so on.

---

# 17.3 Branch Reflog

You can inspect the reflog for a specific branch:

```bash
git reflog show main
```

or:

```bash
git reflog main
```

Example:

```text
e4f7a11 main@{0}: commit: Fix deployment
8c92b30 main@{1}: commit: Add monitoring
```

This is useful when investigating the history of a particular branch reference.

---

# 17.4 View Reflog

Basic:

```bash
git reflog
```

More explicit:

```bash
git reflog show HEAD
```

Show a branch:

```bash
git reflog show main
```

Limit the number of entries:

```bash
git reflog -n 10
```

Example:

```text
e4f7a11 HEAD@{0}: commit: Fix API
8c92b30 HEAD@{1}: commit: Add endpoint
5d31a44 HEAD@{2}: checkout: moving from feature to main
```

---

| Command                         | Description               | Example                         | Branch State Before and After command | Output                   |
| ------------------------------- | ------------------------- | ------------------------------- | ------------------------------------- | ------------------------ |
| `git reflog`                    | Show HEAD reflog          | `git reflog`                    | Unchanged                             | Reference history        |
| `git reflog show BRANCH`        | Show branch reflog        | `git reflog show main`          | Unchanged                             | Branch reference history |
| `git reflog -n N`               | Limit entries             | `git reflog -n 20`              | Unchanged                             | Last N entries           |
| `git reflog expire`             | Remove old reflog entries | `git reflog expire --all`       | Branch usually unchanged              | Expiration result        |
| `git reflog delete`             | Delete a reflog entry     | `git reflog delete HEAD@{5}`    | Branch usually unchanged              | Entry removed            |
| `git show HEAD@{N}`             | Inspect previous state    | `git show HEAD@{2}`             | Unchanged                             | Commit details           |
| `git branch recovered HEAD@{N}` | Create recovery branch    | `git branch recovered HEAD@{2}` | New branch created                    | Branch reference         |
| `git reset --hard HEAD@{N}`     | Move current branch back  | `git reset --hard HEAD@{2}`     | Branch moves to historical state      | Updated HEAD             |
| `git fsck --lost-found`         | Find unreachable objects  | `git fsck --lost-found`         | Unchanged                             | Dangling objects         |

---

# 17.5 Reflog Entry Format

A typical entry:

```text
e4f7a11 HEAD@{0}: commit: Fix API endpoint
```

It contains:

```text
e4f7a11
   |
   +-- Commit/reference target

HEAD@{0}
   |
   +-- Reflog position

commit
   |
   +-- Operation

Fix API endpoint
   |
   +-- Description
```

Other examples:

```text
HEAD@{0}: commit: Add logging
HEAD@{1}: checkout: moving from feature to main
HEAD@{2}: reset: moving to HEAD~2
HEAD@{3}: rebase (finish): returning to refs/heads/main
HEAD@{4}: rebase (start): checkout main
HEAD@{5}: commit (amend): Update API
```

---

# 17.6 HEAD@{N} Syntax

Git supports references such as:

```bash
HEAD@{0}
HEAD@{1}
HEAD@{2}
```

Inspect:

```bash
git show HEAD@{1}
```

Compare:

```bash
git diff HEAD@{1} HEAD@{0}
```

Create a branch:

```bash
git branch recovery HEAD@{1}
```

This is one of the safest recovery techniques because it preserves the recovered commit by creating a new reference.

---

# 17.7 Time-Based Reflog References

Reflog references can also use time expressions.

Examples:

```bash
git show HEAD@{"1 hour ago"}
```

```bash
git show HEAD@{"yesterday"}
```

```bash
git show HEAD@{"2 days ago"}
```

```bash
git show HEAD@{"2026-08-01 12:00"}
```

For scripts, quote expressions carefully because shells interpret special characters differently.

Example:

```bash
git diff HEAD@{"yesterday"} HEAD
```

---

# 17.8 Recovering a Deleted Branch

Suppose:

```bash
git branch -D feature
```

The branch reference is deleted.

The commits may still exist.

First inspect the reflog:

```bash
git reflog
```

Find:

```text
a1b2c3d HEAD@{7}: checkout: moving from feature to main
```

Create a recovery branch:

```bash
git branch feature-recovered a1b2c3d
```

Or:

```bash
git branch feature-recovered HEAD@{7}
```

Verify:

```bash
git log --oneline feature-recovered
```

If correct, rename:

```bash
git branch -m feature-recovered feature
```

---

# 17.9 Recovering After git reset --hard

This is one of the most important recovery scenarios.

Before:

```text
A --- B --- C --- D
              ^
              main
```

Accident:

```bash
git reset --hard B
```

Now:

```text
A --- B
      ^
      main

      C --- D
```

Find the previous state:

```bash
git reflog
```

Example:

```text
b123456 HEAD@{0}: reset: moving to B
d456789 HEAD@{1}: commit: D
```

Inspect:

```bash
git show HEAD@{1}
```

Recover safely:

```bash
git branch recovery HEAD@{1}
```

If you are certain the branch should be restored:

```bash
git reset --hard HEAD@{1}
```

> Prefer creating a recovery branch first. It gives you a safe reference before performing another destructive operation.

---

# 17.10 Recovering After git reset --soft

`git reset --soft` moves `HEAD` but preserves changes in the index and working tree.

Example:

```bash
git reset --soft HEAD~2
```

The commits may still be in the reflog:

```bash
git reflog
```

Recover previous position:

```bash
git branch recovery HEAD@{1}
```

Because `--soft` preserves index and working-tree state, recovery is usually less destructive than `--hard`.

---

# 17.11 Recovering After git reset --mixed

`git reset --mixed` moves `HEAD` and resets the index but leaves working-tree files unchanged.

Find the previous commit:

```bash
git reflog
```

Create a reference:

```bash
git branch recovery HEAD@{1}
```

Then inspect:

```bash
git diff recovery
```

---

# 17.12 Recovering a Lost Commit

Suppose:

```text
A --- B --- C
          ^
          main
```

After an accidental operation:

```text
A --- B
      ^
      main

      C
```

Find `C`:

```bash
git reflog
```

If the SHA is:

```text
c123456
```

inspect:

```bash
git show c123456
```

Create a recovery branch:

```bash
git branch recovered-work c123456
```

Now:

```text
A --- B --- C
      ^     ^
     main  recovered-work
```

You have preserved the commit.

---

# 17.13 Recovering After Rebase

Rebase can rewrite commit IDs.

Before:

```text
A --- B --- C
       \
        D --- E
```

After rebase:

```text
A --- B --- C --- D' --- E'
```

If something goes wrong, inspect:

```bash
git reflog
```

Look for entries such as:

```text
rebase (finish)
rebase (pick)
rebase (start)
```

Example:

```text
e123456 HEAD@{0}: rebase (finish)
f234567 HEAD@{1}: rebase (pick): Commit E
a345678 HEAD@{5}: checkout: moving from feature to main
```

Find the previous branch tip:

```bash
git reflog show feature
```

Create a recovery branch:

```bash
git branch feature-before-rebase feature@{5}
```

Inspect:

```bash
git log --oneline feature-before-rebase
```

---

# 17.14 Recovering After Amend

Suppose:

```bash
git commit --amend
```

creates a new commit.

The previous commit may still be in the reflog.

Run:

```bash
git reflog
```

Example:

```text
a1b2c3d HEAD@{0}: commit (amend): Fix API
e4f5g6h HEAD@{1}: commit: Fix API
```

Recover the original:

```bash
git branch original-commit HEAD@{1}
```

Now both versions are accessible.

---

# 17.15 Recovering After Force Push

Force pushing changes a remote branch reference.

For example:

```bash
git push --force-with-lease origin main
```

A previous remote state may still be available in:

* Another developer's local repository
* CI/CD clones
* Server-side reflogs, depending on hosting configuration
* Backup systems
* Local reflogs

If your local repository previously had the old commit:

```bash
git reflog
```

Find it:

```bash
git show HEAD@{N}
```

Create a recovery branch:

```bash
git branch remote-recovery HEAD@{N}
```

Then carefully determine whether the remote branch should be restored.

> Do not immediately force-push the recovered commit. First verify the intended history and coordinate with collaborators.

---

# 17.16 Inspecting Reflog Entries

Inspect a specific entry:

```bash
git show HEAD@{3}
```

Show metadata:

```bash
git reflog show --date=iso
```

Example:

```text
a1b2c3d HEAD@{2026-08-12 10:30:00 +0000}: commit: Fix deployment
```

This makes time-based investigation easier.

---

# 17.17 Using git show for Recovery

Once you find a candidate commit:

```bash
git show <commit>
```

Example:

```bash
git show a1b2c3d
```

Inspect only the commit metadata:

```bash
git show --no-patch a1b2c3d
```

Show the commit and statistics:

```bash
git show --stat a1b2c3d
```

Show the patch:

```bash
git show --patch a1b2c3d
```

Always inspect a recovered commit before changing a branch reference.

---

# 17.18 Using git branch for Recovery

The safest first recovery action is often:

```bash
git branch recovery <commit>
```

Example:

```bash
git branch recovery HEAD@{5}
```

Now the commit is protected by a branch reference.

Inspect:

```bash
git log --oneline --decorate recovery
```

If the branch contains the expected work, you can continue recovery from it.

---

# 17.19 Using git reset for Recovery

After identifying the correct target:

```bash
git reset --hard <commit>
```

Example:

```bash
git reset --hard HEAD@{5}
```

This changes the current branch.

Before doing this, consider creating a safety branch:

```bash
git branch safety-before-recovery
```

Then:

```bash
git reset --hard <commit>
```

This provides an escape route if the selected commit was incorrect.

---

# 17.20 Using git switch for Recovery

To inspect recovered work without moving your main branch:

```bash
git switch --detach <commit>
```

Example:

```bash
git switch --detach HEAD@{5}
```

You can inspect:

```bash
git log --oneline
```

and:

```bash
git show
```

If you decide the commit is correct, create a branch:

```bash
git switch -c recovered-work
```

This converts the detached state into a named branch.

---

# 17.21 Recovering a Commit With Cherry-Pick

If you only want one recovered commit:

```bash
git cherry-pick <commit>
```

Example:

```bash
git cherry-pick HEAD@{5}
```

This creates a new commit containing the recovered change.

This can be preferable to resetting the entire branch when only one commit is needed.

---

# 17.22 Dangling Objects

Git objects can become unreachable from normal references.

Examples:

* Deleted commits
* Abandoned branches
* Interrupted operations
* Rewritten history

Git may report:

```text
dangling commit a1b2c3d
```

The object still exists, but no branch or tag currently points to it.

---

# 17.23 git fsck

Run:

```bash
git fsck
```

For more detailed information:

```bash
git fsck --full
```

Find unreachable objects:

```bash
git fsck --unreachable
```

Find dangling objects and place recovered objects under `.git/lost-found`:

```bash
git fsck --lost-found
```

Example:

```text
dangling commit a1b2c3d
dangling blob b2c3d4e
```

Inspect the commit:

```bash
git show a1b2c3d
```

Recover it:

```bash
git branch recovered a1b2c3d
```

---

# 17.24 Finding Dangling Commits

Run:

```bash
git fsck --no-reflogs --unreachable
```

Or:

```bash
git fsck --full --no-reflogs
```

Filter commits:

```bash
git fsck --full --no-reflogs 2>/dev/null | grep 'dangling commit'
```

Example:

```text
dangling commit a1b2c3d
dangling commit d4e5f6a
```

Inspect candidates:

```bash
git show a1b2c3d
git show d4e5f6a
```

---

# 17.25 Lost and Found Objects

Use:

```bash
git fsck --lost-found
```

Git may create:

```text
.git/lost-found/
```

with references to unreachable objects.

For example:

```text
.git/lost-found/commit/
.git/lost-found/other/
```

Inspect:

```bash
ls .git/lost-found/commit/
```

Then:

```bash
git show <commit>
```

---

# 17.26 Reflog Expiration

Git eventually expires old reflog entries.

Inspect configuration:

```bash
git config --get gc.reflogExpire
```

and:

```bash
git config --get gc.reflogExpireUnreachable
```

Typical configuration can distinguish:

* reachable entries;
* unreachable entries.

View all relevant configuration:

```bash
git config --show-origin --get-regexp 'gc\..*reflog'
```

---

# 17.27 Reflog and Garbage Collection

Garbage collection can remove unreachable objects after they become eligible.

Run:

```bash
git gc
```

Aggressive maintenance:

```bash
git gc --aggressive
```

> Do not use aggressive garbage collection as a routine recovery technique. If you suspect that commits were accidentally deleted, recover important objects before performing unnecessary cleanup.

Once unreachable objects have been garbage-collected, local recovery may become impossible.

---

# 17.28 Recovering Files

Sometimes you do not need an entire commit.

Inspect an old commit:

```bash
git show <commit>:path/to/file
```

Example:

```bash
git show HEAD@{5}:src/app.js
```

Restore a file from a recovered commit:

```bash
git restore --source=HEAD@{5} -- src/app.js
```

Then inspect:

```bash
git diff
```

Stage if correct:

```bash
git add src/app.js
```

Commit:

```bash
git commit -m "Recover app.js"
```

---

# 17.29 Recovering a Deleted Branch With Remote Information

Before relying only on reflog, inspect remote references:

```bash
git fetch --all --prune
```

List remote branches:

```bash
git branch -r
```

Inspect remote history:

```bash
git log --oneline --all --decorate
```

Search all refs:

```bash
git log --all --oneline --decorate
```

If the deleted branch still exists remotely:

```bash
git switch -c feature origin/feature
```

If the remote branch was also deleted, use the local reflog or another clone containing the branch.

---

# 17.30 Recovery Workflow

A safe recovery process:

```text
        Something went wrong
                 |
                 v
            STOP CHANGES
                 |
                 v
            git status
                 |
                 v
            git reflog
                 |
                 v
         Identify candidate
                 |
                 v
            git show
                 |
                 v
       Create recovery branch
                 |
                 v
       Verify recovered history
                 |
                 v
      Choose recovery operation
          /          |          \
         /           |           \
   cherry-pick     reset       switch
         \           |           /
          \          |          /
           v         v         v
             Verify result
                 |
                 v
              Tests
```

---

# 17.31 Recovery Decision Tree

## You accidentally reset the branch

Start with:

```bash
git reflog
```

Then:

```bash
git branch recovery HEAD@{N}
```

---

## You deleted a branch

Try:

```bash
git reflog
```

Then:

```bash
git branch recovered <commit>
```

---

## You amended the wrong commit

Use:

```bash
git reflog
```

Then:

```bash
git branch original HEAD@{1}
```

---

## You rebased incorrectly

Use:

```bash
git reflog show <branch>
```

Find the branch state before the rebase.

Then:

```bash
git branch before-rebase <commit>
```

---

## Reflog does not contain the commit

Try:

```bash
git fsck --full --no-reflogs
```

Search for:

```text
dangling commit
```

Then:

```bash
git show <commit>
```

---

## You only need one recovered change

Use:

```bash
git cherry-pick <commit>
```

---

## You need to restore the complete branch state

Create a safety reference:

```bash
git branch safety
```

Then, if verified:

```bash
git reset --hard <commit>
```

---

# 17.32 Dangerous Recovery Commands

The following commands can make recovery more difficult:

```bash
git reset --hard
```

```bash
git reflog expire --expire=now --all
```

```bash
git gc
```

```bash
git gc --prune=now
```

```bash
git prune
```

```bash
git reflog delete
```

These commands should not be used casually while investigating lost work.

Especially avoid:

```bash
git gc --prune=now
```

until you are confident that no unreachable objects need to be recovered.

---

# 17.33 DevOps Recovery Scenarios

Reflog and recovery techniques are useful in DevOps environments.

## Accidental release reset

Suppose:

```bash
git reset --hard HEAD~3
```

was executed on a release branch.

Find previous state:

```bash
git reflog show release/2.0
```

Create recovery reference:

```bash
git branch release-recovery release/2.0@{1}
```

Inspect:

```bash
git log --oneline --decorate release-recovery
```

Only after validation should the release branch be restored.

---

## CI branch moved unexpectedly

Inspect:

```bash
git reflog
```

Then:

```bash
git show <candidate>
```

Compare:

```bash
git diff <candidate> HEAD
```

---

## Recovering a deployment commit

Find the commit:

```bash
git log --all --oneline --decorate
```

or:

```bash
git reflog
```

Then:

```bash
git branch deployment-recovery <commit>
```

This provides a stable reference for further investigation.

---

# 17.34 Automation and Diagnostics

Use machine-readable status:

```bash
git status --porcelain=v1
```

Show current commit:

```bash
git rev-parse HEAD
```

Show branch:

```bash
git branch --show-current
```

Show all references:

```bash
git show-ref
```

Show reflog in a compact form:

```bash
git reflog --format='%h %gd %gs'
```

Example:

```text
a1b2c3d HEAD@{0} commit: Fix deployment
b2c3d4e HEAD@{1} reset: moving to HEAD~1
```

Find current branch:

```bash
git symbolic-ref --short HEAD
```

These commands are useful in scripts and CI diagnostics.

---

# 17.35 High-Value Recovery Commands

| Command                               | Description                       | Example                                   | Branch State Before and After command | Output               |
| ------------------------------------- | --------------------------------- | ----------------------------------------- | ------------------------------------- | -------------------- |
| `git reflog`                          | Show HEAD movements               | `git reflog`                              | Unchanged                             | Reflog entries       |
| `git reflog show BRANCH`              | Show branch movements             | `git reflog show main`                    | Unchanged                             | Branch reflog        |
| `git reflog -n 20`                    | Show recent entries               | `git reflog -n 20`                        | Unchanged                             | Last 20 entries      |
| `git show HEAD@{1}`                   | Inspect previous state            | `git show HEAD@{1}`                       | Unchanged                             | Commit and patch     |
| `git show HEAD@{"yesterday"}`         | Inspect state by time             | `git show HEAD@{"yesterday"}`             | Unchanged                             | Commit details       |
| `git branch recovery HEAD@{1}`        | Preserve recovered commit         | `git branch recovery HEAD@{1}`            | New branch reference                  | Recovery branch      |
| `git switch --detach HEAD@{1}`        | Inspect old state                 | `git switch --detach HEAD@{1}`            | HEAD becomes detached                 | Old commit           |
| `git switch -c recovered HEAD@{1}`    | Create branch from recovery point | `git switch -c recovered HEAD@{1}`        | New branch becomes current            | New branch           |
| `git reset --hard HEAD@{1}`           | Restore branch state              | `git reset --hard HEAD@{1}`               | Current branch moves                  | Updated HEAD         |
| `git cherry-pick HEAD@{1}`            | Recover one commit                | `git cherry-pick HEAD@{1}`                | New commit created                    | Cherry-picked commit |
| `git fsck --full`                     | Check object database             | `git fsck --full`                         | Unchanged                             | Object diagnostics   |
| `git fsck --unreachable`              | Find unreachable objects          | `git fsck --unreachable`                  | Unchanged                             | Unreachable objects  |
| `git fsck --lost-found`               | Find and expose lost objects      | `git fsck --lost-found`                   | Unchanged                             | Lost-found objects   |
| `git show COMMIT`                     | Inspect candidate                 | `git show a1b2c3d`                        | Unchanged                             | Commit information   |
| `git show COMMIT:path`                | Inspect old file                  | `git show a1b2c3d:src/app.js`             | Unchanged                             | File content         |
| `git restore --source=COMMIT -- FILE` | Recover file                      | `git restore --source=HEAD@{1} -- app.js` | Working tree/index changes            | Restored file        |
| `git log --all --oneline`             | Search reachable history          | `git log --all --oneline`                 | Unchanged                             | Commit list          |
| `git show-ref`                        | Show references                   | `git show-ref`                            | Unchanged                             | Refs and object IDs  |
| `git rev-parse HEAD`                  | Show current commit               | `git rev-parse HEAD`                      | Unchanged                             | Commit ID            |

---

# 17.36 Recovery Cheat Sheet

```bash
# Show reflog
git reflog

# Show branch reflog
git reflog show main

# Show recent entries
git reflog -n 20

# Show timestamps
git reflog --date=iso

# Inspect previous HEAD
git show HEAD@{1}

# Inspect older HEAD
git show HEAD@{5}

# Inspect previous state by time
git show HEAD@{"yesterday"}

# Create a recovery branch
git branch recovery HEAD@{5}

# Switch to recovered state
git switch --detach HEAD@{5}

# Create and switch to recovery branch
git switch -c recovered HEAD@{5}

# Restore branch to recovered state
git reset --hard HEAD@{5}

# Recover one commit
git cherry-pick HEAD@{5}

# Inspect a recovered commit
git show <commit>

# Inspect a file from an old commit
git show <commit>:path/to/file

# Restore a file
git restore --source=<commit> -- path/to/file

# Find unreachable objects
git fsck --full --unreachable

# Find dangling commits
git fsck --full --no-reflogs 2>/dev/null | grep 'dangling commit'

# Find lost objects
git fsck --lost-found

# Show all reachable history
git log --all --oneline --decorate

# Show all references
git show-ref

# Current commit
git rev-parse HEAD

# Current branch
git branch --show-current
```

---

# Recovery Golden Rules

When Git history appears to be lost:

```text
1. STOP.
2. Do not run cleanup commands.
3. Do not run git gc --prune=now.
4. Check git status.
5. Check git reflog.
6. Identify the desired commit.
7. Run git show on the candidate.
8. Create a recovery branch.
9. Verify the recovered history.
10. Only then modify the original branch.
```

The most important command is:

```bash
git reflog
```

The safest first recovery operation is usually:

```bash
git branch recovery <commit>
```

The key idea is:

```text
A branch can forget a commit.
The reflog may still remember it.
```

---

## Next Part

**Next file:** `18-git-bisect.md`

[Next: Git Bisect](18-git-bisect.md)
