# 11. Stash

`git stash` temporarily saves local modifications so that the working tree can be returned to a clean state without creating a normal commit.

Stash is particularly useful when:

* you need to switch branches while having unfinished work;
* you need to temporarily remove local changes;
* you need to pull/rebase/merge from a clean working tree;
* you want to save work in progress without committing it;
* you need to create multiple independent work-in-progress snapshots.

A simplified model:

```text
Working Tree
     |
     | git stash push
     v
Stash Stack
     |
     | git stash pop/apply
     v
Working Tree
```

Unlike normal commits, stashes are stored separately from the normal branch history.

---

## Table of Contents

* [11.1 Stash Mental Model](#111-stash-mental-model)
* [11.2 Check Working Tree Before Stashing](#112-check-working-tree-before-stashing)
* [11.3 Create a Stash](#113-create-a-stash)
* [11.4 Create a Named Stash](#114-create-a-named-stash)
* [11.5 Stash Only Unstaged Changes](#115-stash-only-unstaged-changes)
* [11.6 Stash Staged Changes](#116-stash-staged-changes)
* [11.7 Stash Everything](#117-stash-everything)
* [11.8 Include Untracked Files](#118-include-untracked-files)
* [11.9 Include Ignored Files](#119-include-ignored-files)
* [11.10 List Stashes](#1110-list-stashes)
* [11.11 Inspect a Stash](#1111-inspect-a-stash)
* [11.12 Show Stash Diff](#1112-show-stash-diff)
* [11.13 Apply the Latest Stash](#1113-apply-the-latest-stash)
* [11.14 Apply a Specific Stash](#1114-apply-a-specific-stash)
* [11.15 Pop the Latest Stash](#1115-pop-the-latest-stash)
* [11.16 Pop a Specific Stash](#1116-pop-a-specific-stash)
* [11.17 Delete a Stash](#1117-delete-a-stash)
* [11.18 Delete All Stashes](#1118-delete-all-stashes)
* [11.19 Create a Branch from a Stash](#1119-create-a-branch-from-a-stash)
* [11.20 Stash While Switching Branches](#1120-stash-while-switching-branches)
* [11.21 Stash with a Message](#1121-stash-with-a-message)
* [11.22 Keep Staged Changes](#1122-keep-staged-changes)
* [11.23 Stash Selected Files](#1123-stash-selected-files)
* [11.24 Interactive Stashing](#1124-interactive-stashing)
* [11.25 Stash Pathspecs](#1125-stash-pathspecs)
* [11.26 Stash and Untracked Files](#1126-stash-and-untracked-files)
* [11.27 Stash and Ignored Files](#1127-stash-and-ignored-files)
* [11.28 Stash During a Rebase](#1128-stash-during-a-rebase)
* [11.29 Stash During a Merge](#1129-stash-during-a-merge)
* [11.30 Recovering a Dropped Stash](#1130-recovering-a-dropped-stash)
* [11.31 Stash Internals](#1131-stash-internals)
* [11.32 Common Stash Workflows](#1132-common-stash-workflows)
* [11.33 Stash vs Commit](#1133-stash-vs-commit)
* [11.34 Dangerous Stash Operations](#1134-dangerous-stash-operations)
* [11.35 High-Value Stash Commands](#1135-high-value-stash-commands)

---

# 11.1 Stash Mental Model

A stash is a temporary snapshot of local changes.

Suppose the repository is:

```text
main
 |
 A---B---C
         ^
        HEAD
```

and the working tree contains unfinished changes:

```text
HEAD
 |
 +-- modified app.py
 +-- modified config.yaml
 +-- untracked debug.log
```

Running:

```bash
git stash push
```

stores applicable changes in the stash stack and normally leaves the working tree clean.

The branch itself does not move:

```text
main
 |
 A---B---C
         ^
        HEAD
```

The changes are stored separately.

You can inspect them with:

```bash
git stash list
```

and restore them later with:

```bash
git stash pop
```

or:

```bash
git stash apply
```

---

# 11.2 Check Working Tree Before Stashing

Before creating a stash:

```bash
git status
```

For a compact view:

```bash
git status --short
```

Inspect unstaged changes:

```bash
git diff
```

Inspect staged changes:

```bash
git diff --cached
```

A useful sequence:

```bash
git status
git diff
git diff --cached
```

This prevents accidentally stashing the wrong files or misunderstanding what will be preserved.

---

| Command              | Description                   | Example              | Branch State Before and After command | Output                          |
| -------------------- | ----------------------------- | -------------------- | ------------------------------------- | ------------------------------- |
| `git status`         | Show current repository state | `git status`         | Branch unchanged                      | Working tree and staging status |
| `git status --short` | Show compact repository state | `git status --short` | Branch unchanged                      | Short status codes              |
| `git diff`           | Show unstaged changes         | `git diff`           | Branch unchanged                      | Patch                           |
| `git diff --cached`  | Show staged changes           | `git diff --cached`  | Branch unchanged                      | Staged patch                    |

---

# 11.3 Create a Stash

The basic command is:

```bash
git stash push
```

Older Git syntax commonly seen:

```bash
git stash
```

Example:

```bash
git stash push
```

Before:

```text
main
 |
 A---B---C
         ^
        HEAD

Working tree:
 M app.py
 M config.yaml
```

After:

```text
main
 |
 A---B---C
         ^
        HEAD

Working tree:
clean
```

The branch does not move.

The local changes are stored in the stash.

---

## Check the stash

```bash
git stash list
```

Example:

```text
stash@{0}: WIP on main: abc1234 Add API
```

---

# 11.4 Create a Named Stash

Use:

```bash
git stash push -m "message"
```

Example:

```bash
git stash push -m "WIP authentication changes"
```

The stash list may show:

```text
stash@{0}: On main: WIP authentication changes
```

Naming stashes is highly recommended when maintaining more than one stash.

Useful examples:

```bash
git stash push -m "WIP login page"
git stash push -m "WIP database migration"
git stash push -m "temporary debugging changes"
```

---

# 11.5 Stash Only Unstaged Changes

By default, Git stash behavior includes both staged and unstaged tracked changes.

If you want to stash only unstaged changes while keeping staged changes staged, use:

```bash
git stash push --keep-index
```

Example:

```text
Before:

Changes to be committed:
    modified: app.py

Changes not staged for commit:
    modified: config.yaml
```

Run:

```bash
git stash push --keep-index
```

Result:

```text
Changes to be committed:
    modified: app.py
```

The staged change remains staged.

The unstaged change is stashed.

---

# 11.6 Stash Staged Changes

If you specifically want to stash staged changes while leaving the current working tree changes available, use:

```bash
git stash push --staged
```

This option is useful when the index contains changes you want temporarily stored.

Example:

```text
Before:

Changes to be committed:
    modified: app.py

Changes not staged for commit:
    modified: config.yaml
```

Run:

```bash
git stash push --staged
```

The staged changes are included in the stash.

The exact resulting state should be verified with:

```bash
git status
git stash show --stat
```

---

# 11.7 Stash Everything

A normal stash:

```bash
git stash push
```

stashes tracked modifications.

To include untracked files:

```bash
git stash push -u
```

To include ignored files as well:

```bash
git stash push -a
```

The three common forms are:

```text
git stash push
    tracked modifications

git stash push -u
    tracked + untracked

git stash push -a
    tracked + untracked + ignored
```

---

# 11.8 Include Untracked Files

Untracked files are not included by a normal stash.

Example:

```text
 M app.py
?? debug.log
```

Running:

```bash
git stash push
```

normally stashes:

```text
app.py
```

but leaves:

```text
debug.log
```

To include untracked files:

```bash
git stash push -u
```

Equivalent:

```bash
git stash push --include-untracked
```

Afterward:

```bash
git status
```

should show a clean working tree if no other untracked/ignored files remain.

---

# 11.9 Include Ignored Files

To stash ignored files too:

```bash
git stash push -a
```

Equivalent:

```bash
git stash push --all
```

This includes:

```text
tracked files
+
untracked files
+
ignored files
```

Example ignored file:

```text
.env.local
```

if `.env.local` is listed in `.gitignore`.

Use this carefully because ignored files often include:

* build output;
* generated files;
* local configuration;
* caches;
* credentials;
* environment-specific files.

---

# 11.10 List Stashes

Show all stashes:

```bash
git stash list
```

Example:

```text
stash@{0}: On feature/login: WIP login validation
stash@{1}: On main: WIP monitoring changes
stash@{2}: On feature/api: WIP API refactor
```

The newest stash is normally:

```text
stash@{0}
```

Older stashes:

```text
stash@{1}
stash@{2}
stash@{3}
...
```

---

## Branch state

`git stash list` does not change:

* `HEAD`;
* the current branch;
* the working tree;
* the index.

It only displays stash references.

---

# 11.11 Inspect a Stash

Inspect the latest stash:

```bash
git stash show
```

Inspect a specific stash:

```bash
git stash show stash@{1}
```

For statistics:

```bash
git stash show --stat stash@{0}
```

For the complete patch:

```bash
git stash show -p stash@{0}
```

Equivalent:

```bash
git stash show --patch stash@{0}
```

---

| Command                       | Description                  | Example                       | Branch State Before and After command | Output                     |
| ----------------------------- | ---------------------------- | ----------------------------- | ------------------------------------- | -------------------------- |
| `git stash show`              | Show summary of latest stash | `git stash show`              | Branch unchanged                      | File summary               |
| `git stash show --stat`       | Show statistics              | `git stash show --stat`       | Branch unchanged                      | Files/insertions/deletions |
| `git stash show -p`           | Show full stash patch        | `git stash show -p`           | Branch unchanged                      | Patch                      |
| `git stash show -p stash@{2}` | Inspect specific stash       | `git stash show -p stash@{2}` | Branch unchanged                      | Patch                      |

---

# 11.12 Show Stash Diff

For detailed inspection:

```bash
git stash show -p stash@{0}
```

You can also compare the stash with the current state using standard Git diff mechanisms after applying it.

Useful commands:

```bash
git stash show --stat
git stash show -p
git status
git diff
```

A good habit is to inspect a stash before applying it when the stash is old or belongs to another branch.

---

# 11.13 Apply the Latest Stash

Apply the latest stash without deleting it:

```bash
git stash apply
```

Before:

```text
Working tree:
clean

stash:
stash@{0}
```

After:

```text
Working tree:
contains stashed changes

stash:
stash@{0} still exists
```

This is the key difference between `apply` and `pop`.

---

# 11.14 Apply a Specific Stash

Apply a specific stash:

```bash
git stash apply stash@{2}
```

Example:

```bash
git stash list
```

Output:

```text
stash@{0}: On main: WIP docs
stash@{1}: On feature/api: WIP API
stash@{2}: On feature/login: WIP login
```

Apply:

```bash
git stash apply stash@{2}
```

The stash remains in the stash list.

---

# 11.15 Pop the Latest Stash

Apply the latest stash and remove it from the stash stack:

```bash
git stash pop
```

Before:

```text
stash@{0}: WIP changes
```

After successful application:

```text
stash:
empty
```

and the changes are restored to the working tree/index.

---

## `apply` vs `pop`

```text
git stash apply
    |
    +-- restore changes
    +-- keep stash

git stash pop
    |
    +-- restore changes
    +-- remove stash if application succeeds
```

When uncertain, `apply` is safer because the stash remains available.

---

# 11.16 Pop a Specific Stash

Use:

```bash
git stash pop stash@{2}
```

This applies the selected stash and removes it if the operation succeeds.

Example:

```bash
git stash list
git stash pop stash@{2}
```

If conflicts occur, the stash may not be automatically removed.

Check:

```bash
git status
git stash list
```

---

# 11.17 Delete a Stash

Delete one stash:

```bash
git stash drop stash@{0}
```

Example:

```bash
git stash drop stash@{2}
```

Output typically indicates that the stash was dropped.

Before deleting:

```bash
git stash show -p stash@{2}
```

if you are unsure whether it is still needed.

---

# 11.18 Delete All Stashes

Delete the entire stash stack:

```bash
git stash clear
```

This is destructive.

All stash entries become unavailable through normal stash references.

Before doing this, inspect:

```bash
git stash list
```

If there is any doubt, do not run `git stash clear`.

---

# 11.19 Create a Branch from a Stash

If a stash contains work that should become its own branch:

```bash
git stash branch <branch-name>
```

Example:

```bash
git stash branch feature/login-recovery
```

This creates a new branch based on the commit where the stash was originally created and attempts to apply the stash there.

This is particularly useful when:

* you stashed changes on the wrong branch;
* the stash has dependencies on an older branch state;
* applying it to the current branch causes conflicts.

---

## Specific stash

```bash
git stash branch feature/login-recovery stash@{2}
```

Example:

```bash
git stash branch feature/api-fix stash@{1}
```

---

# 11.20 Stash While Switching Branches

Suppose you are working on:

```text
feature/login
```

with unfinished changes:

```text
 M login.py
 M auth.py
```

You need to switch to `main`.

First:

```bash
git stash push -m "WIP login feature"
```

Then:

```bash
git switch main
```

Work on `main`.

Later:

```bash
git switch feature/login
git stash pop
```

The unfinished work is restored.

---

## Complete workflow

```bash
git status
git stash push -m "WIP login feature"
git switch main

# perform other work

git switch feature/login
git stash pop
```

---

# 11.21 Stash with a Message

Recommended for multiple stashes:

```bash
git stash push -m "WIP payment API"
```

Examples:

```bash
git stash push -m "WIP authentication"
git stash push -m "WIP Kubernetes deployment"
git stash push -m "WIP database migration"
```

List:

```bash
git stash list
```

Output:

```text
stash@{0}: On feature/db: WIP database migration
stash@{1}: On feature/k8s: WIP Kubernetes deployment
stash@{2}: On feature/auth: WIP authentication
```

---

# 11.22 Keep Staged Changes

The `--keep-index` option keeps staged changes in the index:

```bash
git stash push --keep-index
```

This is useful when you have prepared a partial commit and want to test the staged state independently.

Example:

```text
Staged:
    feature.py

Unstaged:
    debug.py
```

Run:

```bash
git stash push --keep-index
```

Result:

```text
Staged:
    feature.py

Working tree:
    clean except for staged feature.py
```

You can now test the staged changes:

```bash
git test
```

or:

```bash
git diff --cached
```

Then commit:

```bash
git commit -m "Implement feature"
```

---

# 11.23 Stash Selected Files

You can stash selected paths:

```bash
git stash push -- app.py config.yaml
```

Example:

```bash
git stash push -- src/login.py src/auth.py
```

This is useful when only certain files should be temporarily stored.

Example working tree:

```text
 M src/login.py
 M src/auth.py
 M README.md
```

Run:

```bash
git stash push -- src/login.py src/auth.py
```

The selected files are stashed while the other modification remains.

Check:

```bash
git status
```

---

# 11.24 Interactive Stashing

Use:

```bash
git stash push -p
```

or:

```bash
git stash push --patch
```

Git interactively asks which hunks should be stashed.

This is useful when one file contains multiple unrelated changes.

Example:

```text
app.py

Change A -> stash
Change B -> keep
Change C -> stash
```

Interactive mode lets you selectively choose changes.

This is particularly useful for keeping commits clean.

---

# 11.25 Stash Pathspecs

Git supports pathspecs for selecting files.

Example:

```bash
git stash push -- 'src/*.py'
```

Another example:

```bash
git stash push -- ':(exclude)README.md' .
```

Pathspec syntax can become advanced quickly.

When using complex patterns, verify the resulting state:

```bash
git status
git stash show -p
```

---

# 11.26 Stash and Untracked Files

Normal:

```bash
git stash push
```

does not include untracked files.

Example:

```text
 M app.py
?? test-data.json
```

Run:

```bash
git stash push
```

Result:

```text
?? test-data.json
```

still exists.

To include it:

```bash
git stash push -u
```

Equivalent:

```bash
git stash push --include-untracked
```

---

## Include untracked files with a message

```bash
git stash push -u -m "WIP including test data"
```

---

# 11.27 Stash and Ignored Files

Ignored files require:

```bash
git stash push -a
```

or:

```bash
git stash push --all
```

For example:

```text
.gitignore:
.env.local
build/
*.log
```

If these files are present and ignored, a normal stash does not include them.

Use:

```bash
git stash push -a
```

only when you intentionally need ignored files stored.

---

# 11.28 Stash During a Rebase

A common workflow is:

```bash
git status
git stash push -u -m "WIP before rebase"
git fetch origin
git rebase origin/main
git stash pop
```

However, if a rebase is already in progress, do not casually run stash commands without first understanding the current state.

Check:

```bash
git status
```

If you need to cancel the rebase:

```bash
git rebase --abort
```

If the rebase is successful:

```bash
git rebase --continue
```

Then restore the stash:

```bash
git stash pop
```

---

# 11.29 Stash During a Merge

If a merge is already in progress:

```bash
git status
```

will indicate it.

Do not assume that stash is the appropriate solution to a merge conflict.

If you want to abandon the merge:

```bash
git merge --abort
```

If the merge is valid and you are resolving conflicts:

```bash
git status
```

Then resolve the conflicts and continue the merge.

For a clean working tree before starting a merge:

```bash
git stash push -u -m "WIP before merge"
git merge <branch>
git stash pop
```

---

# 11.30 Recovering a Dropped Stash

A dropped stash may sometimes be recoverable because its underlying Git objects can remain temporarily reachable through Git's object database.

First inspect:

```bash
git reflog
```

The normal stash reference may no longer appear.

You can search unreachable objects with:

```bash
git fsck --no-reflogs --unreachable
```

or:

```bash
git fsck --full --no-reflogs --unreachable
```

Look for unreachable commits.

Then inspect candidates:

```bash
git show <commit>
```

If the commit contains the desired stash state, create a recovery branch:

```bash
git branch recovered-stash <commit>
```

or inspect it first:

```bash
git show --stat <commit>
```

---

> Recovery of a dropped stash is not guaranteed. Garbage collection can eventually remove unreachable objects.

---

# 11.31 Stash Internals

A stash is more than a simple patch file.

Git represents stash state using Git objects and references.

A stash can represent information about:

* the working tree;
* the index;
* the base commit;
* optionally untracked/ignored files.

The stash reference is normally:

```text
refs/stash
```

Inspect it with:

```bash
git show-ref refs/stash
```

Inspect the stash:

```bash
git log --oneline --decorate --all --reflog
```

You can also inspect:

```bash
git cat-file -p refs/stash
```

Depending on Git version and repository state, the exact object structure can involve multiple parent commits.

This is why stash recovery can sometimes be possible even after a stash has been dropped.

---

# 11.32 Common Stash Workflows

## Workflow 1 — Temporarily switch branches

```bash
git stash push -u -m "WIP"
git switch main
```

Later:

```bash
git switch feature/my-work
git stash pop
```

---

## Workflow 2 — Save changes before pulling

```bash
git stash push -u -m "WIP before pull"
git pull --rebase
git stash pop
```

---

## Workflow 3 — Save work before rebase

```bash
git stash push -u -m "WIP before rebase"
git fetch origin
git rebase origin/main
git stash pop
```

---

## Workflow 4 — Apply without deleting the stash

```bash
git stash apply stash@{0}
```

This is preferable when you want to keep the stash as a backup.

---

## Workflow 5 — Move work to a new branch

```bash
git stash push -u -m "WIP misplaced branch"
git stash branch feature/new-work
```

---

## Workflow 6 — Select only some changes

```bash
git stash push -p
```

Choose which hunks to stash interactively.

---

## Workflow 7 — Include untracked files

```bash
git stash push -u -m "WIP with new files"
```

---

## Workflow 8 — Inspect before restoring

```bash
git stash list
git stash show --stat stash@{0}
git stash show -p stash@{0}
git stash apply stash@{0}
```

---

# 11.33 Stash vs Commit

Stash and commit solve different problems.

| Feature                              | `git stash`        | `git commit` |
| ------------------------------------ | ------------------ | ------------ |
| Creates normal branch history        | No                 | Yes          |
| Intended for temporary work          | Yes                | No           |
| Shared with remote by normal push    | No                 | Yes          |
| Has commit object(s) internally      | Yes                | Yes          |
| Good for permanent milestones        | No                 | Yes          |
| Easy to identify semantically        | Depends on message | Yes          |
| Suitable for collaboration           | Limited            | Yes          |
| Preserves work across branch changes | Yes                | Yes          |

---

## Use stash when

You need to temporarily put work aside:

```bash
git stash push -u -m "WIP"
```

## Use commit when

The work represents a meaningful version:

```bash
git add .
git commit -m "Implement authentication"
```

Do not use stash as a replacement for a proper Git history.

---

# 11.34 Dangerous Stash Operations

## `git stash drop`

Deletes one stash:

```bash
git stash drop stash@{0}
```

---

## `git stash clear`

Deletes all stash references:

```bash
git stash clear
```

Use extreme caution.

---

## `git stash pop`

Although convenient:

```bash
git stash pop
```

can cause conflicts if the current branch has diverged from the stash's original context.

If you want to preserve the stash while testing the application:

```bash
git stash apply
```

is safer.

---

## `git stash push -a`

This can include ignored files:

```bash
git stash push -a
```

Ignored files may contain large generated directories or sensitive local configuration.

---

# 11.35 High-Value Stash Commands

| Command                               | Description                                          | Example                                  | Branch State Before and After command                           | Output                          |
| ------------------------------------- | ---------------------------------------------------- | ---------------------------------------- | --------------------------------------------------------------- | ------------------------------- |
| `git stash push`                      | Stash tracked local changes                          | `git stash push`                         | Branch unchanged; tracked changes removed from working tree     | Stash created                   |
| `git stash`                           | Shorthand for stash push                             | `git stash`                              | Branch unchanged; tracked changes stashed                       | Stash created                   |
| `git stash push -m "..."`             | Create named stash                                   | `git stash push -m "WIP login"`          | Branch unchanged                                                | Stash created                   |
| `git stash push -u`                   | Include untracked files                              | `git stash push -u`                      | Branch unchanged; tracked + untracked changes stashed           | Stash created                   |
| `git stash push -a`                   | Include ignored files                                | `git stash push -a`                      | Branch unchanged; tracked + untracked + ignored changes stashed | Stash created                   |
| `git stash push --keep-index`         | Keep staged changes while stashing other changes     | `git stash push --keep-index`            | Branch unchanged; index preserved                               | Stash created                   |
| `git stash push --staged`             | Stash staged changes                                 | `git stash push --staged`                | Branch unchanged                                                | Stash created                   |
| `git stash push -p`                   | Interactively select hunks                           | `git stash push -p`                      | Branch unchanged; selected changes stashed                      | Interactive prompts             |
| `git stash push -- file.txt`          | Stash selected path                                  | `git stash push -- app.py`               | Branch unchanged; selected path stashed                         | Stash created                   |
| `git stash list`                      | List stashes                                         | `git stash list`                         | Branch unchanged                                                | Stash references                |
| `git stash show`                      | Show latest stash summary                            | `git stash show`                         | Branch unchanged                                                | Summary                         |
| `git stash show -p`                   | Show stash patch                                     | `git stash show -p`                      | Branch unchanged                                                | Patch                           |
| `git stash show stash@{2}`            | Inspect specific stash                               | `git stash show stash@{2}`               | Branch unchanged                                                | Summary                         |
| `git stash apply`                     | Apply latest stash and keep it                       | `git stash apply`                        | Branch unchanged; working tree receives changes                 | Usually status/conflict output  |
| `git stash apply stash@{2}`           | Apply specific stash                                 | `git stash apply stash@{2}`              | Branch unchanged; changes applied                               | Usually none or conflict output |
| `git stash pop`                       | Apply latest stash and remove it                     | `git stash pop`                          | Branch unchanged; changes restored                              | Apply/drop output               |
| `git stash pop stash@{2}`             | Apply and remove specific stash                      | `git stash pop stash@{2}`                | Branch unchanged; changes restored                              | Apply/drop output               |
| `git stash drop stash@{0}`            | Delete one stash                                     | `git stash drop stash@{0}`               | Branch unchanged                                                | Drop confirmation               |
| `git stash clear`                     | Delete all stashes                                   | `git stash clear`                        | Branch unchanged; stash stack cleared                           | Usually none                    |
| `git stash branch <name>`             | Create branch from latest stash                      | `git stash branch feature/login`         | New branch created; stash applied                               | Branch checkout/apply output    |
| `git stash branch <name> stash@{2}`   | Create branch from specific stash                    | `git stash branch feature/api stash@{2}` | New branch created; selected stash applied                      | Branch/apply output             |
| `git fsck --no-reflogs --unreachable` | Search for unreachable objects for possible recovery | `git fsck --no-reflogs --unreachable`    | Branch unchanged                                                | Unreachable objects             |

---

# Quick Reference

## Save work

```bash
git stash push
```

## Save work with a message

```bash
git stash push -m "WIP feature"
```

## Save including untracked files

```bash
git stash push -u -m "WIP"
```

## Save everything, including ignored files

```bash
git stash push -a -m "Full local snapshot"
```

## List stashes

```bash
git stash list
```

## Inspect stash

```bash
git stash show -p stash@{0}
```

## Apply and keep stash

```bash
git stash apply stash@{0}
```

## Apply and remove stash

```bash
git stash pop stash@{0}
```

## Delete one stash

```bash
git stash drop stash@{0}
```

## Delete all stashes

```bash
git stash clear
```

## Create branch from stash

```bash
git stash branch feature/recovered-work stash@{0}
```

## Recover a potentially dropped stash

```bash
git fsck --no-reflogs --unreachable
```

---

# Recommended Professional Workflow

For normal development:

```bash
git status
git stash push -u -m "WIP before context switch"
git switch main

# perform urgent work

git switch feature/my-work
git stash list
git stash apply stash@{0}

# inspect the restored changes

git status
git diff
git diff --cached
```

If everything is correct and the stash is no longer needed:

```bash
git stash drop stash@{0}
```

---

# Stash Decision Tree

```text
Need to temporarily save local work?
        |
        +-- Yes
             |
             +-- Tracked changes only
             |      |
             |      +-- git stash push
             |
             +-- Include untracked files
             |      |
             |      +-- git stash push -u
             |
             +-- Include ignored files
             |      |
             |      +-- git stash push -a
             |
             +-- Keep staged changes
             |      |
             |      +-- git stash push --keep-index
             |
             +-- Select individual changes
                    |
                    +-- git stash push -p
```

Restoration decision:

```text
Need to restore stash?
        |
        +-- Keep stash as backup
        |      |
        |      +-- git stash apply
        |
        +-- Restore and remove stash
        |      |
        |      +-- git stash pop
        |
        +-- Work belongs on another/new branch
               |
               +-- git stash branch <branch>
```

---

# Important Rules

1. **Use `git stash apply` when you want a safety copy.**
2. **Use `git stash pop` when you are confident the stash should be consumed.**
3. **Use `-u` when untracked files must be preserved.**
4. **Use `-a` only when ignored files must also be preserved.**
5. **Name important stashes with `-m`.**
6. **Inspect old stashes before applying them.**
7. **Do not use stash as a substitute for meaningful commits.**
8. **Do not run `git stash clear` casually.**
9. **If a stash was dropped accidentally, investigate recovery immediately.**
10. **Always use `git status` after applying a stash.**

---

## Next Part

**Next file:** `12-tags-and-releases.md`

[Next: Tags & Releases](12-tags-and-releases.md)
