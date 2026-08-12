# 15. Conflict Resolution

Conflict resolution is one of the most important Git skills for developers, software engineers, DevOps engineers, and teams working collaboratively.

A Git conflict occurs when Git cannot automatically determine how changes from different commits, branches, rebases, cherry-picks, or stashes should be combined.

This document covers:

* Merge conflicts
* Rebase conflicts
* Cherry-pick conflicts
* Stash conflicts
* Conflict markers
* Conflict detection
* Conflict resolution strategies
* Merge tools
* Advanced merge options
* Conflict prevention
* Team workflows

---

# Table of Contents

* [15.1 What Is a Git Conflict?](#151-what-is-a-git-conflict)
* [15.2 Why Conflicts Happen](#152-why-conflicts-happen)
* [15.3 Merge Conflict Example](#153-merge-conflict-example)
* [15.4 Conflict Markers](#154-conflict-markers)
* [15.5 Detecting Conflicts](#155-detecting-conflicts)
* [15.6 Merge Conflicts](#156-merge-conflicts)
* [15.7 Rebase Conflicts](#157-rebase-conflicts)
* [15.8 Cherry-Pick Conflicts](#158-cherry-pick-conflicts)
* [15.9 Stash Conflicts](#159-stash-conflicts)
* [15.10 Abort Operations](#1510-abort-operations)
* [15.11 Continue Operations](#1511-continue-operations)
* [15.12 Use Ours Strategy](#1512-use-ours-strategy)
* [15.13 Use Theirs Strategy](#1513-use-theirs-strategy)
* [15.14 Checkout Conflict Versions](#1514-checkout-conflict-versions)
* [15.15 Merge Tools](#1515-merge-tools)
* [15.16 mergetool Configuration](#1516-mergetool-configuration)
* [15.17 Conflict Style Configuration](#1517-conflict-style-configuration)
* [15.18 rerere](#1518-rerere)
* [15.19 Binary File Conflicts](#1519-binary-file-conflicts)
* [15.20 Delete/Modify Conflicts](#1520-deletemodify-conflicts)
* [15.21 Rename Conflicts](#1521-rename-conflicts)
* [15.22 Directory Conflicts](#1522-directory-conflicts)
* [15.23 Submodule Conflicts](#1523-submodule-conflicts)
* [15.24 Conflict Resolution Workflow](#1524-conflict-resolution-workflow)
* [15.25 Team Best Practices](#1525-team-best-practices)
* [15.26 Conflict Prevention](#1526-conflict-prevention)
* [15.27 Automation-Friendly Commands](#1527-automation-friendly-commands)
* [15.28 Useful Aliases](#1528-useful-aliases)
* [15.29 High-Value Commands](#1529-high-value-commands)

---

# 15.1 What Is a Git Conflict?

Example history:

```text
main:
A --- B --- C

feature:
      \
       D --- E
```

Both branches modify:

```text
config/app.yml
```

Merge:

```bash
git merge feature
```

Git cannot decide:

* Which line should stay
* Which value is correct
* Whether both changes should be combined

Result:

```text
CONFLICT (content): Merge conflict in config/app.yml
Automatic merge failed
```

---

# 15.2 Why Conflicts Happen

Common reasons:

| Cause                    | Example                                |
| ------------------------ | -------------------------------------- |
| Same line modified       | Two developers edit same function      |
| File renamed differently | Two branch rename same file            |
| Delete vs modify         | One branch deletes file, another edits |
| Binary file changes      | Images, archives, binaries             |
| Rebase conflicts         | Commit replay overlaps                 |
| Cherry-pick conflicts    | Same code already changed              |

---

# 15.3 Merge Conflict Example

Before:

```text
main:
A --- B

feature:
      \
       C
```

Merge:

```bash
git checkout main
git merge feature
```

Conflict:

```text
CONFLICT (content)
```

State:

```text
Before:
main -> B

After:
main -> merge-in-progress
```

---

# 15.4 Conflict Markers

Git inserts markers:

```text
<<<<<<< HEAD
port=8080
=======
port=9090
>>>>>>> feature
```

Meaning:

```text
<<<<<<< HEAD
Current branch

=======
Separator

>>>>>>> feature
Incoming branch
```

Resolved:

```text
port=8080
```

or:

```text
port=9090
```

or:

```text
port=8080
backup_port=9090
```

Remove markers before commit.

---

# 15.5 Detecting Conflicts

Check status:

```bash
git status
```

Output:

```text
both modified: app.js
```

Find unresolved files:

```bash
git diff --name-only --diff-filter=U
```

Example:

```text
src/auth.js
config.yml
```

---

| Command                                | Description      | Example                                | Branch State Before and After command | Output         |
| -------------------------------------- | ---------------- | -------------------------------------- | ------------------------------------- | -------------- |
| `git status`                           | Show conflicts   | `git status`                           | Merge in progress                     | Conflict list  |
| `git diff`                             | Show markers     | `git diff`                             | Unchanged                             | Conflict patch |
| `git diff --name-only --diff-filter=U` | Unresolved files | `git diff --name-only --diff-filter=U` | Unchanged                             | File names     |

---

# 15.6 Merge Conflicts

Example:

```bash
git merge feature
```

Resolve:

```bash
nano app.js
git add app.js
git commit
```

Flow:

```text
Merge
   ↓
Conflict
   ↓
Edit
   ↓
git add
   ↓
git commit
```

---

# 15.7 Rebase Conflicts

Start:

```bash
git rebase main
```

Conflict:

```text
CONFLICT
```

Resolve:

```bash
git add file.js
git rebase --continue
```

Skip commit:

```bash
git rebase --skip
```

Abort:

```bash
git rebase --abort
```

---

# 15.8 Cherry-Pick Conflicts

Example:

```bash
git cherry-pick abc123
```

Resolve:

```bash
git add file.js
git cherry-pick --continue
```

Abort:

```bash
git cherry-pick --abort
```

---

# 15.9 Stash Conflicts

Apply stash:

```bash
git stash pop
```

Conflict:

```text
CONFLICT
```

Resolve:

```bash
git add .
git commit
```

---

# 15.10 Abort Operations

Abort merge:

```bash
git merge --abort
```

Abort rebase:

```bash
git rebase --abort
```

Abort cherry-pick:

```bash
git cherry-pick --abort
```

Abort revert:

```bash
git revert --abort
```

---

# 15.11 Continue Operations

```bash
git merge --continue
git rebase --continue
git cherry-pick --continue
git revert --continue
```

---

# 15.12 Use Ours Strategy

Keep current branch:

```bash
git checkout --ours app.js
```

Then:

```bash
git add app.js
```

Meaning:

```text
Keep current branch version
```

---

# 15.13 Use Theirs Strategy

Keep incoming changes:

```bash
git checkout --theirs app.js
```

Then:

```bash
git add app.js
```

Meaning:

```text
Keep incoming version
```

---

# 15.14 Checkout Conflict Versions

Show stages:

```bash
git ls-files -u
```

Example:

```text
1 base
2 ours
3 theirs
```

Extract:

```bash
git show :1:file.txt
git show :2:file.txt
git show :3:file.txt
```

---

# 15.15 Merge Tools

Start:

```bash
git mergetool
```

Popular tools:

* VSCode
* Meld
* KDiff3
* Beyond Compare
* P4Merge

---

# 15.16 mergetool Configuration

Example:

```bash
git config --global merge.tool vscode
```

Or:

```bash
git config --global merge.tool meld
```

Launch:

```bash
git mergetool
```

---

# 15.17 Conflict Style Configuration

Default:

```bash
git config merge.conflictstyle merge
```

More context:

```bash
git config merge.conflictstyle diff3
```

Example:

```text
base
ours
theirs
```

Modern:

```bash
git config merge.conflictstyle zdiff3
```

---

# 15.18 rerere

Reuse recorded resolutions:

```bash
git config --global rerere.enabled true
```

Git remembers:

```text
Conflict
↓
Resolution
↓
Reuse later
```

Useful for:

* Long-lived branches
* Frequent rebases
* Release branches

---

# 15.19 Binary File Conflicts

Example:

```text
logo.png
```

Git cannot merge binaries.

Choose:

```bash
git checkout --ours logo.png
```

or:

```bash
git checkout --theirs logo.png
```

---

# 15.20 Delete/Modify Conflicts

Example:

```text
main:
delete file

feature:
modify file
```

Decision:

* Keep deletion
* Keep file

Resolve:

```bash
git rm file.txt
```

or:

```bash
git add file.txt
```

---

# 15.21 Rename Conflicts

Example:

```text
main:
config.yml -> app.yml

feature:
config.yml -> settings.yml
```

Git asks:

```text
Which name should remain?
```

Manual resolution required.

---

# 15.22 Directory Conflicts

Example:

```text
backend/
frontend/
```

Mass renames can produce conflicts.

Useful:

```bash
git status
git diff
git mergetool
```

---

# 15.23 Submodule Conflicts

Submodules store commit references.

Conflict:

```text
Subproject commit mismatch
```

Resolve:

```bash
cd submodule
git checkout commit
cd ..
git add submodule
```

---

# 15.24 Conflict Resolution Workflow

```text
Merge/Rebase
      ↓
Conflict
      ↓
git status
      ↓
Edit files
      ↓
git add
      ↓
Continue
      ↓
Commit
```

---

# 15.25 Team Best Practices

* Pull frequently
* Rebase often
* Small commits
* Small PRs
* Avoid long-lived branches
* Communicate changes
* Use feature flags

---

# 15.26 Conflict Prevention

Good:

```text
Small branch lifetime
Frequent sync
Small commits
```

Bad:

```text
3-month branch
1000 changed files
Huge PR
```

---

# 15.27 Automation-Friendly Commands

Check unresolved:

```bash
git diff --name-only --diff-filter=U
```

Count conflicts:

```bash
git diff --name-only --diff-filter=U | wc -l
```

Script:

```bash
if git diff --quiet --diff-filter=U
then
    echo "No conflicts"
fi
```

---

# 15.28 Useful Aliases

```bash
git config --global alias.conflicts "diff --name-only --diff-filter=U"
git config --global alias.unmerged "diff --name-only --diff-filter=U"
```

Usage:

```bash
git conflicts
```

---

# 15.29 High-Value Commands

| Command                                | Description           | Example                                   | Branch State Before and After command | Output                |
| -------------------------------------- | --------------------- | ----------------------------------------- | ------------------------------------- | --------------------- |
| `git status`                           | Show conflicts        | `git status`                              | Merge in progress                     | Conflict list         |
| `git diff --name-only --diff-filter=U` | Unresolved files      | `git diff --name-only --diff-filter=U`    | Unchanged                             | File names            |
| `git checkout --ours FILE`             | Keep local version    | `git checkout --ours app.js`              | Conflict resolved for file            | Local version         |
| `git checkout --theirs FILE`           | Keep incoming version | `git checkout --theirs app.js`            | Conflict resolved for file            | Incoming version      |
| `git add FILE`                         | Mark resolved         | `git add app.js`                          | File resolved                         | Staged file           |
| `git merge --abort`                    | Abort merge           | `git merge --abort`                       | Return before merge                   | Previous state        |
| `git rebase --abort`                   | Abort rebase          | `git rebase --abort`                      | Return before rebase                  | Previous state        |
| `git cherry-pick --abort`              | Abort cherry-pick     | `git cherry-pick --abort`                 | Return before operation               | Previous state        |
| `git rebase --continue`                | Continue rebase       | `git rebase --continue`                   | Next commit replayed                  | Rebase continues      |
| `git cherry-pick --continue`           | Continue cherry-pick  | `git cherry-pick --continue`              | Continue operation                    | Cherry-pick continues |
| `git mergetool`                        | Open merge tool       | `git mergetool`                           | Unchanged                             | GUI tool              |
| `git ls-files -u`                      | Show conflict stages  | `git ls-files -u`                         | Unchanged                             | Base/Ours/Theirs      |
| `git config rerere.enabled true`       | Enable rerere         | `git config --global rerere.enabled true` | Config updated                        | Auto-resolution       |

---

# Quick Reference

```bash
# Show conflicts
git status

# Unresolved files
git diff --name-only --diff-filter=U

# Keep ours
git checkout --ours file.txt

# Keep theirs
git checkout --theirs file.txt

# Mark resolved
git add file.txt

# Continue
git merge --continue
git rebase --continue
git cherry-pick --continue

# Abort
git merge --abort
git rebase --abort
git cherry-pick --abort

# GUI tool
git mergetool

# Show conflict stages
git ls-files -u

# Enable rerere
git config --global rerere.enabled true
```

---

## Next Part

**Next file:** `16-cherry-pick.md`

[Next: Cherry-Pick](16-cherry-pick.md)
