# 34. Dangerous Commands

Git is powerful because many operations are reversible or can be recovered through history. However, several commands can permanently delete working-tree data, rewrite commit history, remove references, or alter repository state in ways that are difficult to recover from.

This chapter identifies the Git commands and command combinations that require the most caution.

> **Safety rule:** Before executing a destructive Git command, run `git status`, inspect the affected commits/files, and make sure you understand exactly what will change.

---

# Table of Contents

* [34.1 Safety Principles](#341-safety-principles)
* [34.2 `git reset --hard`](#342-git-reset---hard)
* [34.3 `git clean`](#343-git-clean)
* [34.4 `git clean -fd`](#344-git-clean--fd)
* [34.5 `git clean -fdx`](#345-git-clean--fdx)
* [34.6 `git restore`](#346-git-restore)
* [34.7 `git restore --source`](#347-git-restore---source)
* [34.8 `git checkout --`](#348-git-checkout--)
* [34.9 `git reset`](#349-git-reset)
* [34.10 `git reset --soft`](#3410-git-reset---soft)
* [34.11 `git reset --mixed`](#3411-git-reset---mixed)
* [34.12 `git reset --hard HEAD~N`](#3412-git-reset---hard-headn)
* [34.13 Force Push](#3413-force-push)
* [34.14 `git push --force`](#3414-git-push---force)
* [34.15 `git push --force-with-lease`](#3415-git-push---force-with-lease)
* [34.16 `git push --mirror`](#3416-git-push---mirror)
* [34.17 Deleting Remote Branches](#3417-deleting-remote-branches)
* [34.18 Deleting Tags](#3418-deleting-tags)
* [34.19 `git branch -D`](#3419-git-branch--d)
* [34.20 `git stash clear`](#3420-git-stash-clear)
* [34.21 `git stash drop`](#3421-git-stash-drop)
* [34.22 `git reflog expire`](#3422-git-reflog-expire)
* [34.23 `git gc`](#3423-git-gc)
* [34.24 `git prune`](#3424-git-prune)
* [34.25 `git fsck --no-reflogs`](#3425-git-fsck---no-reflogs)
* [34.26 History Rewriting](#3426-history-rewriting)
* [34.27 `git filter-repo`](#3427-git-filter-repo)
* [34.28 Removing Secrets from History](#3428-removing-secrets-from-history)
* [34.29 `git rebase`](#3429-git-rebase)
* [34.30 Interactive Rebase](#3430-interactive-rebase)
* [34.31 `git commit --amend`](#3431-git-commit---amend)
* [34.32 `git cherry-pick`](#3432-git-cherry-pick)
* [34.33 `git merge --strategy`](#3433-git-merge---strategy)
* [34.34 Detached HEAD](#3434-detached-head)
* [34.35 Overwriting Local Changes](#3435-overwriting-local-changes)
* [34.36 Dangerous Command Combinations](#3436-dangerous-command-combinations)
* [34.37 Safer Alternatives](#3437-safer-alternatives)
* [34.38 Recovery Before Destruction](#3438-recovery-before-destruction)
* [34.39 Pre-Destruction Checklist](#3439-pre-destruction-checklist)
* [34.40 Emergency Recovery](#3440-emergency-recovery)
* [34.41 Dangerous Command Quick Reference](#3441-dangerous-command-quick-reference)
* [34.42 Golden Rules](#3442-golden-rules)

---

# 34.1 Safety Principles

Before executing a destructive Git command:

```bash
git status
```

Then inspect the repository:

```bash
git log --oneline --graph --decorate --all
```

If the operation concerns a specific commit:

```bash
git show <commit>
```

If the operation concerns a branch:

```bash
git branch -vv
```

If the operation concerns remote state:

```bash
git remote -v
git fetch --prune
```

For potentially destructive filesystem operations, preview first.

For example:

```bash
git clean -nd
```

instead of immediately executing:

```bash
git clean -fd
```

---

# 34.2 `git reset --hard`

## Command

```bash
git reset --hard <commit>
```

This moves the current branch and resets the index and working tree to the specified commit.

Example:

```bash
git reset --hard HEAD~1
```

### Why it is dangerous

It can discard:

* staged changes;
* unstaged changes;
* local commits from the current branch position.

Example:

```text
Before:

A---B---C  main
        |
      HEAD

Working tree:
modified files
```

After:

```bash
git reset --hard B
```

the branch becomes:

```text
A---B  main
    |
   HEAD
```

Commit `C` is no longer referenced by `main`, and working-tree changes are removed.

### Safer inspection

```bash
git status
git log --oneline --decorate -5
git reflog
```

### Important

`git reset --hard` does **not** mean "undo safely."

It means:

> Make the current branch, index, and working tree match the specified commit.

---

# 34.3 `git clean`

`git clean` removes untracked files from the working tree.

Preview:

```bash
git clean -n
```

Equivalent short form:

```bash
git clean -nd
```

Actual removal:

```bash
git clean -fd
```

### Why it is dangerous

Untracked files are not part of normal Git history.

If Git deletes them, they may not be recoverable through:

```bash
git reflog
```

because they were never committed.

### Safe pattern

Always:

```bash
git clean -nd
```

first.

Then, if the output is exactly what you expect:

```bash
git clean -fd
```

---

# 34.4 `git clean -fd`

Command:

```bash
git clean -fd
```

Meaning:

```text
-f = force
-d = include untracked directories
```

Example:

```bash
git clean -fd
```

Possible result:

```text
Removing build/
Removing tmp/
Removing generated-file.txt
```

### Danger

The command permanently removes matching untracked filesystem content.

### Safer:

```bash
git clean -nd
```

Review first.

---

# 34.5 `git clean -fdx`

Command:

```bash
git clean -fdx
```

This is significantly more dangerous.

`-x` includes ignored files.

That can include:

```text
.env
node_modules/
build/
dist/
.cache/
IDE files
generated files
local configuration
```

Preview:

```bash
git clean -ndx
```

Only execute:

```bash
git clean -fdx
```

when you intentionally want a completely clean working directory.

### Typical CI usage

A CI environment may intentionally use:

```bash
git clean -ffdx
```

But this should be avoided casually on a developer workstation.

---

# 34.6 `git restore`

Command:

```bash
git restore file.txt
```

This discards unstaged changes to the specified file.

Before:

```text
HEAD version:
A

Working tree:
B
```

After:

```bash
git restore file.txt
```

the working tree returns to the index/HEAD version.

### Danger

Uncommitted modifications can be lost.

### Safer alternative

Inspect first:

```bash
git diff -- file.txt
```

If the changes might be useful:

```bash
git stash push -m "backup before restore"
```

Then restore.

---

# 34.7 `git restore --source`

Example:

```bash
git restore --source=HEAD~1 -- file.txt
```

This replaces the working-tree version with the version from another commit.

It can also be used with the staging area:

```bash
git restore --source=HEAD~1 --staged --worktree -- file.txt
```

### Danger

The command can overwrite both staged and working-tree versions.

Before executing:

```bash
git diff
git diff --cached
git show HEAD~1:file.txt
```

---

# 34.8 `git checkout --`

Older Git syntax:

```bash
git checkout -- file.txt
```

This discards working-tree modifications.

Modern Git generally makes the intent clearer with:

```bash
git restore -- file.txt
```

Prefer:

```bash
git restore file.txt
```

for new workflows.

---

# 34.9 `git reset`

`git reset` changes the relationship between:

* `HEAD`;
* the current branch;
* the index;
* the working tree.

The three major modes are:

```text
--soft
--mixed
--hard
```

The default is:

```bash
git reset --mixed
```

---

# 34.10 `git reset --soft`

Example:

```bash
git reset --soft HEAD~1
```

The branch moves backward, but changes remain staged.

Example:

```text
Before:

A---B---C
        ^
       HEAD

After:

A---B
    ^
   HEAD

Changes represented by C remain staged.
```

This is useful for:

* combining commits;
* fixing the last commit;
* restructuring local history.

It is comparatively safe when commits have not been shared.

---

# 34.11 `git reset --mixed`

Example:

```bash
git reset HEAD~1
```

The branch moves backward and the index is reset, while working-tree changes remain.

Typical use:

```bash
git reset HEAD~1
```

to undo the latest local commit while keeping its content as unstaged working-tree changes.

### State transition

```text
Commit
  ↓
Branch moved backward
  ↓
Index reset
  ↓
Working tree preserved
```

---

# 34.12 `git reset --hard HEAD~N`

Example:

```bash
git reset --hard HEAD~3
```

This removes the latest three commits from the current branch reference and resets tracked files.

### Extremely important

Do not use this casually on a shared branch.

Before:

```text
A---B---C---D---E
            ^
           HEAD
```

After:

```bash
git reset --hard HEAD~3
```

the branch becomes:

```text
A---B
    ^
   HEAD
```

Commits `C`, `D`, and `E` are no longer referenced by the branch.

They may still be recoverable through the reflog:

```bash
git reflog
```

but recovery should not be treated as guaranteed indefinitely.

---

# 34.13 Force Push

A normal push rejects non-fast-forward updates.

A force push allows a remote branch to be rewritten.

The general operation is:

```bash
git push --force
```

or:

```bash
git push --force-with-lease
```

Force pushing is dangerous because it changes remote history.

---

# 34.14 `git push --force`

Command:

```bash
git push --force
```

Short form:

```bash
git push -f
```

This can overwrite the remote branch even when the remote contains commits not present in the local branch.

### Example

Remote:

```text
A---B---C---D origin/main
```

Local:

```text
A---B---X---Y main
```

Force pushing local `main` can replace:

```text
A---B---C---D
```

with:

```text
A---B---X---Y
```

### Danger

Other developers may lose the remote branch history from their normal branch reference.

### Avoid on shared branches

Especially:

```text
main
master
production
release/*
```

unless the team explicitly requires history rewriting.

---

# 34.15 `git push --force-with-lease`

Preferred alternative:

```bash
git push --force-with-lease
```

This performs a force update while checking that the remote reference has not changed unexpectedly since your local knowledge of it.

Typical use after rewriting a personal feature branch:

```bash
git rebase origin/main
git push --force-with-lease
```

### Why it is safer

It reduces the risk of overwriting someone else's newly pushed work.

### Important

It is still a history rewrite.

It is **not** equivalent to a normal push.

---

# 34.16 `git push --mirror`

Command:

```bash
git push --mirror
```

This can synchronize all refs between repositories.

It can create, update, and delete remote references to match the local repository.

### Danger

This is a repository-wide operation.

It can affect:

* branches;
* tags;
* other refs.

Do not use it casually against a shared remote.

Inspect first:

```bash
git show-ref
```

and verify the destination remote carefully:

```bash
git remote -v
```

---

# 34.17 Deleting Remote Branches

Command:

```bash
git push origin --delete feature/old
```

This removes the remote branch.

Equivalent older syntax:

```bash
git push origin :feature/old
```

Prefer the explicit form:

```bash
git push origin --delete feature/old
```

### Danger

Other users may still rely on the branch.

Before deleting:

```bash
git log origin/feature/old --oneline
```

---

# 34.18 Deleting Tags

Delete local:

```bash
git tag -d v1.0.0
```

Delete remote:

```bash
git push origin --delete v1.0.0
```

### Danger

Tags often represent:

* releases;
* production versions;
* deployment points;
* published artifacts.

Deleting or moving release tags can break automation and reproducibility.

Treat release tags as immutable unless the project explicitly permits otherwise.

---

# 34.19 `git branch -D`

Safe branch deletion:

```bash
git branch -d feature/name
```

Force deletion:

```bash
git branch -D feature/name
```

`-D` bypasses Git's merged-history safety check.

### Safer

Try:

```bash
git branch -d feature/name
```

first.

If Git refuses, investigate:

```bash
git log main..feature/name --oneline
```

This shows commits reachable from the feature branch that are not reachable from `main`.

---

# 34.20 `git stash clear`

Command:

```bash
git stash clear
```

This removes all stash entries.

### Danger

Stashes are local references to otherwise potentially unreachable objects.

After clearing them, recovery becomes substantially harder.

Before clearing:

```bash
git stash list
```

Inspect:

```bash
git stash show -p stash@{0}
```

Delete individually when possible:

```bash
git stash drop stash@{0}
```

---

# 34.21 `git stash drop`

Example:

```bash
git stash drop stash@{0}
```

This removes one stash entry.

It is safer than:

```bash
git stash clear
```

because the operation is targeted.

Still, if the stash contains important uncommitted work, deleting it can result in data loss.

---

# 34.22 `git reflog expire`

Example:

```bash
git reflog expire --expire=now --all
```

This removes reflog entries according to the specified expiration policy.

### Danger

The reflog is one of the most useful recovery mechanisms in Git.

Deleting reflog information makes recovery of previous branch states harder.

Do not manually expire reflogs unless you understand the consequences.

---

# 34.23 `git gc`

Command:

```bash
git gc
```

Git garbage collection optimizes repository storage and can eventually remove unreachable objects according to Git's pruning policies.

A common misconception is:

> `git gc` immediately deletes everything that is unreachable.

That is not generally the correct mental model.

Git uses expiration and reachability rules.

### Dangerous combination

Commands such as:

```bash
git reflog expire --expire=now --all
git gc --prune=now
```

can aggressively eliminate objects that might otherwise have been recoverable.

Do not use this combination as routine cleanup.

---

# 34.24 `git prune`

Command:

```bash
git prune
```

This removes unreachable objects that are eligible for pruning.

It is primarily a low-level maintenance operation.

### Danger

If an object is unreachable and no longer protected by reflogs or other references, pruning can make recovery impossible.

Do not use `git prune` as a casual cleanup command.

Prefer Git's normal maintenance mechanisms unless you have a specific reason to run it manually.

---

# 34.25 `git fsck --no-reflogs`

Command:

```bash
git fsck --no-reflogs
```

This ignores reflog references when determining reachability.

It is useful for repository investigation, but can also make the repository appear to contain more unreachable objects than normal reachability analysis would indicate.

Use it for diagnostics, not routine cleanup.

For recovery investigation:

```bash
git fsck --full
```

may be more useful.

---

# 34.26 History Rewriting

History rewriting changes existing commits rather than creating new commits that preserve the old history.

Common history-rewriting commands include:

```bash
git rebase
git rebase -i
git reset
git commit --amend
git filter-repo
git push --force
```

### Key distinction

Safe-style history correction:

```bash
git revert <commit>
```

creates a new commit.

History rewrite:

```bash
git reset
git rebase
```

changes the existing history structure.

### Shared repositories

Before rewriting history that has already been published, coordinate with everyone consuming the branch.

---

# 34.27 `git filter-repo`

`git filter-repo` is commonly used for large-scale repository history rewriting.

Typical use cases include:

* removing sensitive files;
* removing large files;
* rewriting paths;
* changing author information;
* extracting repository content.

Example:

```bash
git filter-repo --path secrets.txt --invert-paths
```

### Danger

This rewrites history.

Every affected commit can receive a new object ID.

After rewriting, the remote repository may require coordinated force updates.

### Important

Do not treat history rewriting as a simple file deletion.

Deleting a file in the current working tree does not necessarily remove it from previous commits.

---

# 34.28 Removing Secrets from History

Suppose a secret was accidentally committed:

```text
.env
credentials.json
private-key.pem
API token
password
```

Deleting the file in a new commit is not enough.

The secret can remain in Git history.

The proper response should include:

1. Revoke or rotate the exposed credential immediately.
2. Determine where it exists in history.
3. Rewrite history if necessary.
4. Coordinate the rewritten repository.
5. Force-update affected remotes if required.
6. Inform other repository users.
7. Verify the secret is no longer present in reachable history.

Searching:

```bash
git log --all -- path/to/secret
```

Searching content:

```bash
git grep "known-secret"
```

History rewriting must be performed deliberately.

> **Important:** Removing a secret from Git history does not make an already-exposed credential safe. Rotate/revoke the credential first.

---

# 34.29 `git rebase`

Rebase rewrites commits.

Example:

```bash
git rebase main
```

Before:

```text
      C---D  feature
     /
A---B---E---F  main
```

After:

```text
A---B---E---F---C'---D'  feature
```

The changes represented by `C` and `D` are replayed as new commits.

Therefore:

```text
C != C'
D != D'
```

even when the changes are logically equivalent.

### Dangerous situation

Rebasing commits that other developers are already using can cause divergence.

Use rebase primarily for:

* private branches;
* local cleanup;
* coordinated history rewriting.

---

# 34.30 Interactive Rebase

Command:

```bash
git rebase -i HEAD~5
```

This allows operations such as:

```text
pick
reword
edit
squash
fixup
drop
```

Example:

```text
pick   abc123 Add endpoint
squash def456 Fix endpoint
fixup  789abc Typo
reword 123def Update documentation
```

### Dangerous operation

```text
drop
```

removes a commit from the rewritten history.

### Golden rule

Do not casually rewrite commits that have already been shared with other developers.

---

# 34.31 `git commit --amend`

Command:

```bash
git commit --amend
```

This replaces the latest commit.

Example:

```bash
git add file.c
git commit --amend --no-edit
```

The previous commit object is replaced by a new commit.

### Safe when

The commit is local and has not been shared.

### Potentially dangerous when

The commit has already been pushed and other developers have based work on it.

In that case, amending usually requires:

```bash
git push --force-with-lease
```

---

# 34.32 `git cherry-pick`

Cherry-pick creates a new commit containing the changes from another commit.

Example:

```bash
git cherry-pick abc123
```

It is not inherently destructive, but it can create duplicate or conflicting changes.

### Potential danger

Repeated cherry-picking can produce:

* duplicated changes;
* conflicts;
* confusing history;
* difficult future merges.

Before cherry-picking:

```bash
git show abc123
```

After:

```bash
git status
git log --oneline -5
```

---

# 34.33 `git merge --strategy`

Some merge strategies or options can intentionally favor one side during conflicts.

For example:

```bash
git merge -X ours feature
```

or:

```bash
git merge -X theirs feature
```

These options require careful understanding.

### Important distinction

`-X ours` and `-X theirs` are merge strategy options and are **not** the same as:

```bash
git merge -s ours
```

The latter creates a merge commit that treats the current tree as the result while recording the other history as merged.

These commands should not be used merely because conflict resolution is inconvenient.

Resolve the actual conflict when correctness matters.

---

# 34.34 Detached HEAD

Detached HEAD is not inherently dangerous.

Example:

```bash
git switch --detach <commit>
```

You can inspect and test an older commit.

The danger comes from creating commits and then leaving them without a branch reference.

Example:

```text
A---B---C
        |
      HEAD
```

Create commit:

```text
A---B---C---D
             ^
            HEAD
```

If you switch away without creating a branch:

```bash
git switch main
```

commit `D` may become reachable only through the reflog.

### Safe pattern

If you want to continue development:

```bash
git switch -c recovery-work
```

---

# 34.35 Overwriting Local Changes

Several commands may refuse to switch branches because local changes would be overwritten.

Do not automatically force the operation.

First:

```bash
git status
git diff
```

Then decide whether to:

### Commit

```bash
git add .
git commit -m "WIP"
```

### Stash

```bash
git stash push -u -m "Temporary work"
```

### Discard

```bash
git restore .
```

Only discard changes when you are certain they are unnecessary.

---

# 34.36 Dangerous Command Combinations

The most dangerous Git operations are often combinations of commands.

---

## 34.36.1 Reset + Force Push

```bash
git reset --hard HEAD~3
git push --force
```

This can rewrite a remote branch and remove the previous branch tip from the normal branch history.

Safer:

```bash
git fetch origin
git log --oneline --graph --decorate --all
git push --force-with-lease
```

Only if history rewriting is intentional.

---

## 34.36.2 Rebase + Force Push

```bash
git rebase -i HEAD~10
git push --force
```

This rewrites ten commits and then overwrites the remote branch.

Prefer:

```bash
git rebase -i HEAD~10
git push --force-with-lease
```

on a private or coordinated branch.

---

## 34.36.3 Clean + Reset

```bash
git reset --hard
git clean -fd
```

This can remove:

* tracked working-tree changes;
* staged changes;
* untracked files;
* untracked directories.

Together, they can make the working directory look like a clean checkout while destroying local work.

---

## 34.36.4 Clean + `-x`

```bash
git clean -fdx
```

This removes ignored files as well.

Use:

```bash
git clean -ndx
```

first.

---

## 34.36.5 Reflog Expiration + Aggressive Pruning

Potentially dangerous:

```bash
git reflog expire --expire=now --all
git gc --prune=now
```

This can destroy recovery paths for unreachable objects.

Do not use it merely because the repository appears large.

---

## 34.36.6 Mirror Push

```bash
git push --mirror origin
```

This can synchronize deletion of refs as well as creation and updates.

Verify the remote first:

```bash
git remote -v
```

---

# 34.37 Safer Alternatives

| Dangerous Operation             | Safer Alternative                   |
| ------------------------------- | ----------------------------------- |
| `git reset --hard`              | `git reset --soft` or inspect first |
| `git clean -fd`                 | `git clean -nd` first               |
| `git clean -fdx`                | `git clean -ndx` first              |
| `git push --force`              | `git push --force-with-lease`       |
| `git branch -D`                 | `git branch -d`                     |
| `git stash clear`               | `git stash drop stash@{N}`          |
| `git restore file`              | `git diff -- file` first            |
| `git reset` on shared history   | `git revert`                        |
| Rewrite shared branch           | Coordinate with team                |
| Delete release tag              | Verify release process              |
| Manual `git prune`              | Let normal maintenance handle it    |
| Reflog expiration               | Preserve recovery references        |
| Delete secret from current tree | Rotate credential + rewrite history |

---

# 34.38 Recovery Before Destruction

A useful principle is:

> **Create a recovery point before performing an irreversible operation.**

For example:

```bash
git branch backup-before-reset
```

Then:

```bash
git reset --hard HEAD~3
```

Now the old state is still referenced by:

```text
backup-before-reset
```

You can inspect it:

```bash
git log --oneline backup-before-reset
```

This is often much safer than relying solely on the reflog.

---

# 34.39 Pre-Destruction Checklist

Before running a destructive command:

* [ ] Run `git status`
* [ ] Run `git diff`
* [ ] Run `git diff --cached`
* [ ] Inspect recent history
* [ ] Confirm the current branch
* [ ] Confirm the remote
* [ ] Determine whether the branch is shared
* [ ] Determine whether uncommitted work exists
* [ ] Determine whether untracked files matter
* [ ] Create a backup branch when appropriate
* [ ] Preview filesystem deletion commands
* [ ] Prefer `--force-with-lease` over `--force`
* [ ] Prefer `git revert` over rewriting shared history
* [ ] Verify the command before pressing Enter

Useful commands:

```bash
git status -sb
git branch --show-current
git remote -v
git log --oneline --graph --decorate --all -20
git diff
git diff --cached
```

---

# 34.40 Emergency Recovery

If you accidentally executed a dangerous command, **stop**.

Do not immediately run:

```bash
git gc
```

or:

```bash
git prune
```

or:

```bash
git clean
```

or another reset.

First inspect:

```bash
git status
```

Then:

```bash
git reflog
```

Inspect candidate commits:

```bash
git show <commit>
```

Inspect all visible history:

```bash
git log --oneline --graph --decorate --all
```

Create a recovery branch:

```bash
git switch -c recovery <commit>
```

If an old branch position is known:

```bash
git branch recovery <old-commit>
```

Then verify:

```bash
git log --oneline --decorate recovery
```

---

# 34.41 Dangerous Command Quick Reference

| Command                   | Primary Risk                                | Safer Practice                             |
| ------------------------- | ------------------------------------------- | ------------------------------------------ |
| `git reset --hard`        | Discards tracked working-tree/index changes | Inspect with `git diff`; consider `--soft` |
| `git reset --hard HEAD~N` | Removes branch commits from current ref     | Create backup branch first                 |
| `git clean -fd`           | Deletes untracked files/directories         | Run `git clean -nd` first                  |
| `git clean -fdx`          | Deletes untracked + ignored files           | Run `git clean -ndx` first                 |
| `git restore file`        | Discards working-tree changes               | Inspect `git diff` first                   |
| `git restore --source`    | Replaces file content                       | Inspect source with `git show`             |
| `git branch -D`           | Deletes branch regardless of merge status   | Use `git branch -d` first                  |
| `git push --force`        | Can overwrite remote history                | Prefer `--force-with-lease`                |
| `git push --mirror`       | Can create/update/delete many refs          | Verify remote and refs                     |
| `git push --delete`       | Removes remote branch/tag                   | Confirm consumers first                    |
| `git stash clear`         | Deletes all stashes                         | Drop individual entries                    |
| `git stash drop`          | Deletes stash                               | Inspect first                              |
| `git rebase -i`           | Rewrites commits                            | Avoid shared history                       |
| `git commit --amend`      | Rewrites latest commit                      | Use before publishing                      |
| `git filter-repo`         | Rewrites repository history                 | Backup and coordinate                      |
| `git reflog expire`       | Removes recovery references                 | Avoid manual expiration                    |
| `git prune`               | Removes unreachable objects                 | Avoid manual use                           |
| `git gc --prune=now`      | Aggressive object pruning                   | Use only intentionally                     |
| `git merge -s ours`       | Can hide incoming tree changes              | Resolve intentionally                      |
| `git checkout -- file`    | Discards file changes                       | Prefer `git restore` after inspection      |

---

# 34.42 Golden Rules

## Rule 1 — Inspect before destroying

Always start with:

```bash
git status
```

---

## Rule 2 — Preview before deleting

For `git clean`:

```bash
git clean -nd
```

For ignored files:

```bash
git clean -ndx
```

---

## Rule 3 — Prefer reversible operations

When appropriate:

```bash
git revert
```

is safer for shared history than:

```bash
git reset
```

---

## Rule 4 — Prefer `--force-with-lease`

If a force push is genuinely required:

```bash
git push --force-with-lease
```

is generally safer than:

```bash
git push --force
```

---

## Rule 5 — Never assume reflog is permanent

`git reflog` is extremely useful:

```bash
git reflog
```

but recovery information can eventually expire.

Do not intentionally destroy recovery references unless you understand the consequences.

---

## Rule 6 — Untracked files are different

Git cannot recover an untracked file merely because it exists in the working directory.

Therefore:

```bash
git clean -fd
```

can be more dangerous than:

```bash
git reset --hard
```

for files that were never committed.

---

## Rule 7 — Never casually rewrite shared history

Avoid casually using:

```bash
git rebase
git reset
git commit --amend
git push --force
```

on branches used by other developers.

---

## Rule 8 — Backup before major rewriting

A simple backup branch can save significant time:

```bash
git branch backup-before-rewrite
```

For a more explicit backup:

```bash
git branch backup/$(date +%Y%m%d-%H%M%S)
```

On systems where command substitution is appropriate.

---

## Rule 9 — Rotate exposed secrets

If a credential reaches Git history:

```text
DO NOT rely only on deleting the file.
```

Immediately:

```text
1. Revoke/rotate the credential.
2. Assess exposure.
3. Rewrite history if required.
4. Coordinate repository cleanup.
5. Verify.
```

---

## Rule 10 — Stop after an accidental destructive command

If something went wrong:

```text
STOP
 ↓
DO NOT run more cleanup
 ↓
git status
 ↓
git reflog
 ↓
git log
 ↓
git show
 ↓
create recovery branch
 ↓
verify
```

---

# Dangerous Commands to Memorize

The following commands deserve special attention:

```bash
git reset --hard
git reset --hard HEAD~N

git clean -fd
git clean -fdx
git clean -ffdx

git branch -D <branch>

git push --force
git push --mirror

git push origin --delete <branch>
git push origin --delete <tag>

git stash clear
git stash drop

git rebase
git rebase -i

git commit --amend

git filter-repo

git reflog expire
git prune
git gc --prune=now
```

The commands themselves are not inherently "bad." They are powerful tools whose consequences must be understood before execution.

---

# The Most Important Safety Pattern

When in doubt:

```bash
git status
git diff
git diff --cached
git log --oneline --graph --decorate --all
git reflog
```

Then make a deliberate decision.

A good Git engineer does not avoid powerful commands.

A good Git engineer understands:

* what the command changes;
* what it does not change;
* whether the change is reversible;
* whether other developers are affected;
* whether the remote repository is affected;
* how recovery would work;
* and when **not** to execute the command.

---

# Next Part

**Next file:** `35-practical-git-aliases.md`

[Next: Practical Git Aliases](35-practical-git-aliases.md)
