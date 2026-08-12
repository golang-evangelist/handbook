# 24. Repository Diagnostics

Repository diagnostics are used to determine:

* Whether a repository is valid
* Whether Git metadata is damaged
* Which branch is checked out
* Whether the working tree is clean
* Which files are staged or unstaged
* Which commits are reachable
* Which objects are unreachable
* Where branches and tags point
* Which remotes are configured
* Whether a repository is shallow
* Which Git configuration is active
* Why a Git operation behaves unexpectedly
* How large the repository is
* Whether objects and packfiles are healthy

This chapter focuses on **inspection and troubleshooting** rather than changing repository history.

---

# Table of Contents

* [24.1 Diagnostic Philosophy](#241-diagnostic-philosophy)
* [24.2 git status](#242-git-status)
* [24.3 git status --short](#243-git-status---short)
* [24.4 git status --branch](#244-git-status---branch)
* [24.5 git branch](#245-git-branch)
* [24.6 git branch -vv](#246-git-branch--vv)
* [24.7 git show-ref](#247-git-show-ref)
* [24.8 git rev-parse](#248-git-rev-parse)
* [24.9 git symbolic-ref](#249-git-symbolic-ref)
* [24.10 git rev-list](#2410-git-rev-list)
* [24.11 git merge-base](#2411-git-merge-base)
* [24.12 git log Diagnostics](#2412-git-log-diagnostics)
* [24.13 git diff Diagnostics](#2413-git-diff-diagnostics)
* [24.14 git ls-files](#2414-git-ls-files)
* [24.15 git ls-tree](#2415-git-ls-tree)
* [24.16 git cat-file](#2416-git-cat-file)
* [24.17 git fsck](#2417-git-fsck)
* [24.18 git count-objects](#2418-git-count-objects)
* [24.19 git verify-pack](#2419-git-verify-pack)
* [24.20 git remote Diagnostics](#2420-git-remote-diagnostics)
* [24.21 git fetch Diagnostics](#2421-git-fetch-diagnostics)
* [24.22 git ls-remote](#2422-git-ls-remote)
* [24.23 git config Diagnostics](#2423-git-config-diagnostics)
* [24.24 git version](#2424-git-version)
* [24.25 git rev-parse Repository Detection](#2425-git-rev-parse-repository-detection)
* [24.26 Shallow Repository Diagnostics](#2426-shallow-repository-diagnostics)
* [24.27 Worktree Diagnostics](#2427-worktree-diagnostics)
* [24.28 Submodule Diagnostics](#2428-submodule-diagnostics)
* [24.29 Repository Size Diagnostics](#2429-repository-size-diagnostics)
* [24.30 Diagnosing Detached HEAD](#2430-diagnosing-detached-head)
* [24.31 Diagnosing Diverged Branches](#2431-diagnosing-diverged-branches)
* [24.32 Diagnosing Missing Commits](#2432-diagnosing-missing-commits)
* [24.33 Diagnosing Corruption](#2433-diagnosing-corruption)
* [24.34 Diagnostic Workflows](#2434-diagnostic-workflows)
* [24.35 High-Value Diagnostic Commands](#2435-high-value-diagnostic-commands)
* [24.36 Diagnostic Best Practices](#2436-diagnostic-best-practices)

---

# 24.1 Diagnostic Philosophy

Git diagnostics should generally follow this order:

```text
Observe
   |
   v
Identify repository state
   |
   v
Inspect references
   |
   v
Inspect history
   |
   v
Inspect objects
   |
   v
Inspect configuration
   |
   v
Change something only when the cause is understood
```

Do not immediately run destructive commands when diagnosing a problem.

For example, if a branch appears to have disappeared, first inspect:

```bash
git status
git branch --all
git reflog --all
git fsck --full
```

before attempting cleanup or history rewriting.

---

# 24.2 git status

The first diagnostic command to run in most repositories is:

```bash
git status
```

It shows:

* Current branch
* Working-tree changes
* Staged changes
* Untracked files
* Relationship with the upstream branch

Example:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

For a repository with changes:

```text
On branch feature/login
Changes to be committed:
  modified:   src/login.js

Changes not staged for commit:
  modified:   README.md

Untracked files:
  test-login.sh
```

---

| Command                  | Description                      | Example                  | Branch State Before and After command | Output                 |
| ------------------------ | -------------------------------- | ------------------------ | ------------------------------------- | ---------------------- |
| `git status`             | Show complete working-tree state | `git status`             | Unchanged                             | Branch and file status |
| `git status --short`     | Show compact status              | `git status --short`     | Unchanged                             | Short status codes     |
| `git status --branch`    | Include branch information       | `git status --branch`    | Unchanged                             | Branch status          |
| `git status --porcelain` | Machine-readable status          | `git status --porcelain` | Unchanged                             | Stable status format   |

---

# 24.3 git status --short

Use:

```bash
git status --short
```

Example:

```text
 M README.md
M  src/app.js
?? test.sh
```

Interpretation:

```text
XY filename
```

where:

```text
X = index/staging area state
Y = working tree state
```

Common values:

```text
 M
M
A
D
R
C
??
```

Examples:

```text
 M file.txt
```

means the working-tree copy differs from the index.

```text
M  file.txt
```

means the change is staged.

```text
?? file.txt
```

means the file is untracked.

---

# 24.4 git status --branch

Use:

```bash
git status --branch
```

or:

```bash
git status -sb
```

Example:

```text
## feature/login...origin/feature/login
 M src/login.js
```

If ahead:

```text
## feature/login...origin/feature/login [ahead 2]
```

If behind:

```text
## feature/login...origin/feature/login [behind 3]
```

If diverged:

```text
## feature/login...origin/feature/login [ahead 2, behind 1]
```

This is especially useful for fast diagnostics.

---

# 24.5 git branch

List local branches:

```bash
git branch
```

List all branches:

```bash
git branch --all
```

List remote-tracking branches:

```bash
git branch --remotes
```

Show branches with their latest commits:

```bash
git branch -v
```

Sort branches by recent commit:

```bash
git branch --sort=-committerdate
```

---

| Command                            | Description                    | Example                            | Branch State Before and After command | Output                    |
| ---------------------------------- | ------------------------------ | ---------------------------------- | ------------------------------------- | ------------------------- |
| `git branch`                       | List local branches            | `git branch`                       | Unchanged                             | Local branches            |
| `git branch --all`                 | List local and remote branches | `git branch --all`                 | Unchanged                             | All refs                  |
| `git branch --remotes`             | List remote-tracking branches  | `git branch --remotes`             | Unchanged                             | Remote branches           |
| `git branch -v`                    | Show branch tip commits        | `git branch -v`                    | Unchanged                             | Branch/commit information |
| `git branch --sort=-committerdate` | Sort by latest commit          | `git branch --sort=-committerdate` | Unchanged                             | Sorted branches           |

---

# 24.6 git branch -vv

Use:

```bash
git branch -vv
```

This displays:

* Branch names
* Current branch
* Latest commit
* Upstream branch
* Ahead/behind information

Example:

```text
* main  abc1234 [origin/main] Update documentation
  dev   def5678 [origin/dev: ahead 2] Add feature
```

This is one of the most useful commands for diagnosing branch/upstream relationships.

---

# 24.7 git show-ref

Show references:

```bash
git show-ref
```

Example:

```text
abc123 refs/heads/main
def456 refs/heads/develop
abc123 refs/remotes/origin/main
987654 refs/tags/v1.0.0
```

Show only heads:

```bash
git show-ref --heads
```

Show only tags:

```bash
git show-ref --tags
```

Verify whether a specific reference exists:

```bash
git show-ref --verify refs/heads/main
```

This is useful when debugging unusual reference states.

---

# 24.8 git rev-parse

`git rev-parse` is one of the most important diagnostic commands in Git.

Current branch:

```bash
git rev-parse --abbrev-ref HEAD
```

Current commit:

```bash
git rev-parse HEAD
```

Short commit:

```bash
git rev-parse --short HEAD
```

Repository root:

```bash
git rev-parse --show-toplevel
```

Git directory:

```bash
git rev-parse --git-dir
```

Common directory:

```bash
git rev-parse --git-common-dir
```

Object directory:

```bash
git rev-parse --git-path objects
```

Show whether repository is bare:

```bash
git rev-parse --is-bare-repository
```

Show whether repository is shallow:

```bash
git rev-parse --is-shallow-repository
```

---

| Command                                 | Description               | Example                                 | Branch State Before and After command | Output            |
| --------------------------------------- | ------------------------- | --------------------------------------- | ------------------------------------- | ----------------- |
| `git rev-parse HEAD`                    | Show current commit ID    | `git rev-parse HEAD`                    | Unchanged                             | Full object ID    |
| `git rev-parse --short HEAD`            | Show short commit ID      | `git rev-parse --short HEAD`            | Unchanged                             | Short object ID   |
| `git rev-parse --abbrev-ref HEAD`       | Show current branch       | `git rev-parse --abbrev-ref HEAD`       | Unchanged                             | Branch name       |
| `git rev-parse --show-toplevel`         | Show repository root      | `git rev-parse --show-toplevel`         | Unchanged                             | Absolute path     |
| `git rev-parse --git-dir`               | Show Git directory        | `git rev-parse --git-dir`               | Unchanged                             | Git directory     |
| `git rev-parse --is-bare-repository`    | Detect bare repository    | `git rev-parse --is-bare-repository`    | Unchanged                             | `true` or `false` |
| `git rev-parse --is-shallow-repository` | Detect shallow repository | `git rev-parse --is-shallow-repository` | Unchanged                             | `true` or `false` |

---

# 24.9 git symbolic-ref

Show the symbolic reference for `HEAD`:

```bash
git symbolic-ref HEAD
```

Example:

```text
refs/heads/main
```

Show only branch name:

```bash
git symbolic-ref --short HEAD
```

Output:

```text
main
```

If `HEAD` is detached, the command may fail because `HEAD` points directly to a commit rather than a branch reference.

---

# 24.10 git rev-list

`git rev-list` is useful for investigating commit reachability.

List commits:

```bash
git rev-list HEAD
```

Count commits:

```bash
git rev-list --count HEAD
```

Count commits between branches:

```bash
git rev-list --count main..feature
```

Count commits on each side of divergence:

```bash
git rev-list --left-right --count main...feature
```

Example:

```text
2    5
```

This can mean:

```text
2 commits unique to main
5 commits unique to feature
```

depending on argument ordering.

---

# 24.11 git merge-base

Find the common ancestor of two commits:

```bash
git merge-base main feature
```

Example:

```text
abc123456789...
```

Find whether branches are already related:

```bash
git merge-base --is-ancestor main feature
```

Check exit status:

```bash
git merge-base --is-ancestor main feature
echo $?
```

Output:

```text
0
```

means `main` is an ancestor of `feature`.

A non-zero status means it is not.

---

# 24.12 git log Diagnostics

Basic history:

```bash
git log
```

Compact history:

```bash
git log --oneline
```

Graph:

```bash
git log --oneline --graph --decorate --all
```

Show recent commits:

```bash
git log -10 --oneline
```

Show commits affecting a file:

```bash
git log -- path/to/file
```

Show patch:

```bash
git log -p -1
```

Find commits containing a string:

```bash
git log -S"someFunction"
```

Find regex changes:

```bash
git log -G"somePattern"
```

Inspect all branches:

```bash
git log --all --oneline --graph --decorate
```

A particularly useful diagnostic command:

```bash
git log --oneline --graph --decorate --all
```

It provides a visual representation of branch topology.

---

# 24.13 git diff Diagnostics

Working tree versus index:

```bash
git diff
```

Index versus `HEAD`:

```bash
git diff --cached
```

Working tree versus `HEAD`:

```bash
git diff HEAD
```

Compare branches:

```bash
git diff main...feature
```

Check whitespace errors:

```bash
git diff --check
```

Check staged whitespace:

```bash
git diff --cached --check
```

Statistics:

```bash
git diff --stat
```

Name-only changes:

```bash
git diff --name-only
```

Name and status:

```bash
git diff --name-status
```

---

| Command                  | Description                  | Example                  | Branch State Before and After command | Output              |
| ------------------------ | ---------------------------- | ------------------------ | ------------------------------------- | ------------------- |
| `git diff`               | Working tree vs index        | `git diff`               | Unchanged                             | Unstaged diff       |
| `git diff --cached`      | Index vs HEAD                | `git diff --cached`      | Unchanged                             | Staged diff         |
| `git diff HEAD`          | Working tree + index vs HEAD | `git diff HEAD`          | Unchanged                             | Complete local diff |
| `git diff --check`       | Check whitespace errors      | `git diff --check`       | Unchanged                             | Whitespace warnings |
| `git diff --stat`        | Show change statistics       | `git diff --stat`        | Unchanged                             | File statistics     |
| `git diff --name-only`   | List changed files           | `git diff --name-only`   | Unchanged                             | File names          |
| `git diff --name-status` | List file names and status   | `git diff --name-status` | Unchanged                             | Status/file pairs   |

---

# 24.14 git ls-files

`git ls-files` shows files tracked by the index.

Basic:

```bash
git ls-files
```

Show staged files:

```bash
git ls-files --cached
```

Show untracked files:

```bash
git ls-files --others
```

Show ignored files:

```bash
git ls-files --ignored --exclude-standard
```

Show deleted files:

```bash
git ls-files --deleted
```

Show files with staging information:

```bash
git ls-files --stage
```

This is particularly useful when `git status` does not provide enough detail.

---

# 24.15 git ls-tree

Inspect the tree associated with a commit:

```bash
git ls-tree HEAD
```

Recursive:

```bash
git ls-tree -r HEAD
```

Show names only:

```bash
git ls-tree -r --name-only HEAD
```

Inspect a specific path:

```bash
git ls-tree HEAD -- src/
```

This helps determine what a commit actually contains.

---

# 24.16 git cat-file

`git cat-file` provides low-level object inspection.

Determine object type:

```bash
git cat-file -t HEAD
```

Output:

```text
commit
```

Show object size:

```bash
git cat-file -s HEAD
```

Show object content:

```bash
git cat-file -p HEAD
```

Inspect a tree:

```bash
git cat-file -p HEAD^{tree}
```

Inspect a commit:

```bash
git cat-file -p HEAD
```

Example commit output:

```text
tree abc123...
parent def456...
author ...
committer ...

Commit message
```

---

# 24.17 git fsck

Run a full integrity check:

```bash
git fsck --full
```

Show unreachable objects:

```bash
git fsck --full --unreachable
```

Show dangling objects:

```bash
git fsck --full --dangling
```

Check connectivity:

```bash
git fsck --connectivity-only
```

`git fsck` is one of the most important commands when diagnosing possible repository corruption.

---

# 24.18 git count-objects

Inspect object storage:

```bash
git count-objects -vH
```

Typical output:

```text
count: 12
size: 48
in-pack: 1000
packs: 2
size-pack: 2048
prune-packable: 0
garbage: 0
size-garbage: 0
```

This can help answer:

* Is the repository mostly packed?
* Are there many loose objects?
* Are there garbage objects?
* How large are the packfiles?

---

# 24.19 git verify-pack

Find pack indexes:

```bash
ls .git/objects/pack/*.idx
```

Verify a pack:

```bash
git verify-pack -v .git/objects/pack/pack-XXXX.idx
```

This is useful for advanced investigation of:

* Pack integrity
* Object sizes
* Large objects
* Delta relationships

---

# 24.20 git remote Diagnostics

List remotes:

```bash
git remote
```

Verbose:

```bash
git remote -v
```

Show detailed information:

```bash
git remote show origin
```

Show remote URLs:

```bash
git remote get-url origin
```

Show push URL:

```bash
git remote get-url --push origin
```

Show all remote URLs:

```bash
git remote get-url --all origin
```

Example:

```text
origin  git@github.com:example/project.git (fetch)
origin  git@github.com:example/project.git (push)
```

---

# 24.21 git fetch Diagnostics

Fetch without changing the current branch:

```bash
git fetch
```

Fetch all remotes:

```bash
git fetch --all
```

Prune stale remote-tracking branches:

```bash
git fetch --prune
```

Fetch with pruning:

```bash
git fetch --all --prune
```

Dry-run:

```bash
git fetch --dry-run
```

Verbose:

```bash
git fetch -v
```

These commands are useful when determining whether local remote-tracking information is current.

---

# 24.22 git ls-remote

Inspect references directly from a remote:

```bash
git ls-remote origin
```

Show heads:

```bash
git ls-remote --heads origin
```

Show tags:

```bash
git ls-remote --tags origin
```

Check a specific branch:

```bash
git ls-remote origin refs/heads/main
```

This is useful because it queries the remote without requiring a local remote-tracking update.

---

| Command                                | Description                 | Example                                | Branch State Before and After command | Output             |
| -------------------------------------- | --------------------------- | -------------------------------------- | ------------------------------------- | ------------------ |
| `git ls-remote origin`                 | List remote references      | `git ls-remote origin`                 | Unchanged                             | Remote refs        |
| `git ls-remote --heads origin`         | List remote branches        | `git ls-remote --heads origin`         | Unchanged                             | Remote branch refs |
| `git ls-remote --tags origin`          | List remote tags            | `git ls-remote --tags origin`          | Unchanged                             | Remote tag refs    |
| `git ls-remote origin refs/heads/main` | Inspect specific remote ref | `git ls-remote origin refs/heads/main` | Unchanged                             | Object ID/ref      |

---

# 24.23 git config Diagnostics

Show all configuration:

```bash
git config --list
```

Show origins:

```bash
git config --list --show-origin
```

Show scopes:

```bash
git config --list --show-scope
```

Combine:

```bash
git config --list --show-origin --show-scope
```

Show configuration for a specific key:

```bash
git config --get user.name
```

Show aliases:

```bash
git config --get-regexp '^alias\.'
```

Show remote configuration:

```bash
git config --get-regexp '^remote\.'
```

Show branch configuration:

```bash
git config --get-regexp '^branch\.'
```

This is extremely useful when Git behaves differently from what you expect.

---

# 24.24 git version

Show Git version:

```bash
git --version
```

Example:

```text
git version 2.x.y
```

Show more version information:

```bash
git version --build-options
```

Git version matters because commands and features vary between Git releases.

---

# 24.25 git rev-parse Repository Detection

Determine whether the current directory is inside a Git repository:

```bash
git rev-parse --is-inside-work-tree
```

Typical output:

```text
true
```

For a bare repository:

```bash
git rev-parse --is-bare-repository
```

Output:

```text
false
```

or:

```text
true
```

Find the repository root:

```bash
git rev-parse --show-toplevel
```

Find the Git directory:

```bash
git rev-parse --git-dir
```

Find the common Git directory:

```bash
git rev-parse --git-common-dir
```

These commands are extremely useful in shell scripts.

---

# 24.26 Shallow Repository Diagnostics

Check whether the repository is shallow:

```bash
git rev-parse --is-shallow-repository
```

Inspect shallow boundaries:

```bash
cat .git/shallow
```

For a normal repository, `.git/shallow` may not exist.

List shallow commits:

```bash
git rev-list --boundary HEAD
```

Convert to full repository:

```bash
git fetch --unshallow
```

Deepen by a specific number of commits:

```bash
git fetch --deepen=50
```

Shallow repositories can cause confusing results when analyzing history.

---

# 24.27 Worktree Diagnostics

List worktrees:

```bash
git worktree list
```

Verbose:

```bash
git worktree list --verbose
```

Example:

```text
/home/user/project    abc1234 [main]
/home/user/project-feature    def5678 [feature]
```

Inspect worktree metadata:

```bash
git worktree list --porcelain
```

This is important when branches appear to be locked or unavailable because they are checked out in another worktree.

---

# 24.28 Submodule Diagnostics

Show submodule status:

```bash
git submodule status
```

Recursive:

```bash
git submodule status --recursive
```

Show configured submodules:

```bash
git config --file .gitmodules --list
```

Inspect:

```bash
cat .gitmodules
```

Check submodule differences:

```bash
git diff --submodule
```

Check recursive status:

```bash
git status --short --ignore-submodules
```

For more detailed submodule diagnostics:

```bash
git submodule summary
```

---

# 24.29 Repository Size Diagnostics

Check Git directory size:

```bash
du -sh .git
```

Check object directory:

```bash
du -sh .git/objects
```

Check packfiles:

```bash
du -sh .git/objects/pack
```

List largest packfiles:

```bash
ls -lhS .git/objects/pack/
```

Check Git object statistics:

```bash
git count-objects -vH
```

For advanced investigation, identify large objects through pack inspection or history-analysis tooling.

---

# 24.30 Diagnosing Detached HEAD

Check current branch:

```bash
git branch --show-current
```

If output is empty, inspect:

```bash
git status
```

You may see:

```text
HEAD detached at abc1234
```

Confirm:

```bash
git symbolic-ref --short HEAD
```

A detached `HEAD` means:

```text
HEAD
 |
 v
commit
```

instead of:

```text
HEAD
 |
 v
refs/heads/main
 |
 v
commit
```

Find the commit:

```bash
git rev-parse HEAD
```

Recover work by creating a branch:

```bash
git switch -c recovery-branch
```

This changes branch state and should be done only after confirming the commit you want to preserve.

---

# 24.31 Diagnosing Diverged Branches

Start with:

```bash
git status -sb
```

Then:

```bash
git branch -vv
```

Determine ahead/behind counts:

```bash
git rev-list --left-right --count HEAD...@{upstream}
```

Example:

```text
2    3
```

This means the local branch and upstream have commits not present on the other side.

Inspect graph:

```bash
git log --oneline --graph --decorate --all
```

Find common ancestor:

```bash
git merge-base HEAD @{upstream}
```

Compare changes:

```bash
git diff HEAD...@{upstream}
```

Do not immediately reset or rebase until the divergence is understood.

---

# 24.32 Diagnosing Missing Commits

If a commit appears to have disappeared:

```bash
git reflog --all
```

Search all refs:

```bash
git log --all --oneline --decorate
```

Inspect unreachable commits:

```bash
git fsck --full --unreachable
```

Look for dangling commits:

```bash
git fsck --full --no-reflogs
```

If you find the desired commit:

```bash
git show <commit>
```

Preserve it by creating a branch:

```bash
git switch -c recovery <commit>
```

This is much safer than immediately running garbage collection.

---

# 24.33 Diagnosing Corruption

If Git reports errors such as:

```text
fatal: loose object ... is corrupt
fatal: bad object ...
fatal: object file ... is empty
```

start with:

```bash
git fsck --full
```

Then:

```bash
git count-objects -vH
```

Inspect:

```bash
git reflog --all
```

Check packfiles:

```bash
ls -lh .git/objects/pack/
```

Verify pack indexes where appropriate:

```bash
git verify-pack -v .git/objects/pack/pack-XXXX.idx
```

Do **not** start with:

```bash
git gc
```

when corruption is suspected.

Garbage collection is not a general corruption-repair mechanism.

For important repositories, restore from a known-good clone or backup when necessary.

---

# 24.34 Diagnostic Workflows

## Workflow 1 — "What is my current Git state?"

```bash
git status -sb
git branch -vv
git rev-parse --short HEAD
```

---

## Workflow 2 — "Which branch am I on?"

```bash
git branch --show-current
```

or:

```bash
git symbolic-ref --short HEAD
```

---

## Workflow 3 — "Where is the repository?"

```bash
git rev-parse --show-toplevel
git rev-parse --git-dir
```

---

## Workflow 4 — "Is my repository shallow?"

```bash
git rev-parse --is-shallow-repository
```

---

## Workflow 5 — "Is my repository healthy?"

```bash
git status
git fsck --full
git count-objects -vH
```

---

## Workflow 6 — "Why did a branch disappear?"

```bash
git branch --all
git reflog --all
git fsck --full --unreachable
git log --all --oneline --decorate
```

---

## Workflow 7 — "Why is my branch diverged?"

```bash
git status -sb
git branch -vv
git rev-list --left-right --count HEAD...@{upstream}
git merge-base HEAD @{upstream}
git log --oneline --graph --decorate --all
```

---

## Workflow 8 — "What does the remote actually contain?"

```bash
git ls-remote origin
```

Then:

```bash
git fetch --prune
```

and:

```bash
git branch --remotes
```

---

## Workflow 9 — "Why is the repository so large?"

```bash
git count-objects -vH
du -sh .git
du -sh .git/objects
du -sh .git/objects/pack
```

Then inspect packfiles:

```bash
ls -lhS .git/objects/pack/
```

---

## Workflow 10 — "Why is a file considered tracked?"

```bash
git ls-files -- path/to/file
```

Check staged state:

```bash
git ls-files --stage -- path/to/file
```

Check differences:

```bash
git diff -- path/to/file
git diff --cached -- path/to/file
```

---

# 24.35 High-Value Diagnostic Commands

| Command                                        | Description                 | Example                                        | Branch State Before and After command | Output            |
| ---------------------------------------------- | --------------------------- | ---------------------------------------------- | ------------------------------------- | ----------------- |
| `git status`                                   | Show repository state       | `git status`                                   | Unchanged                             | Status            |
| `git status -sb`                               | Compact branch status       | `git status -sb`                               | Unchanged                             | Branch + changes  |
| `git branch --all`                             | Show all branches           | `git branch --all`                             | Unchanged                             | Branch list       |
| `git branch -vv`                               | Show upstream relationships | `git branch -vv`                               | Unchanged                             | Branch tracking   |
| `git rev-parse HEAD`                           | Show current commit         | `git rev-parse HEAD`                           | Unchanged                             | Object ID         |
| `git rev-parse --show-toplevel`                | Show repository root        | `git rev-parse --show-toplevel`                | Unchanged                             | Path              |
| `git rev-parse --git-dir`                      | Show Git directory          | `git rev-parse --git-dir`                      | Unchanged                             | Path              |
| `git rev-parse --is-shallow-repository`        | Detect shallow clone        | `git rev-parse --is-shallow-repository`        | Unchanged                             | Boolean           |
| `git symbolic-ref --short HEAD`                | Show symbolic branch        | `git symbolic-ref --short HEAD`                | Unchanged                             | Branch name       |
| `git show-ref`                                 | Show refs                   | `git show-ref`                                 | Unchanged                             | Refs              |
| `git merge-base main feature`                  | Find common ancestor        | `git merge-base main feature`                  | Unchanged                             | Commit ID         |
| `git rev-list --count HEAD`                    | Count commits               | `git rev-list --count HEAD`                    | Unchanged                             | Number            |
| `git log --oneline --graph --decorate --all`   | Visualize history           | `git log --oneline --graph --decorate --all`   | Unchanged                             | Graph             |
| `git diff`                                     | Inspect unstaged changes    | `git diff`                                     | Unchanged                             | Diff              |
| `git diff --cached`                            | Inspect staged changes      | `git diff --cached`                            | Unchanged                             | Diff              |
| `git ls-files`                                 | List tracked files          | `git ls-files`                                 | Unchanged                             | File list         |
| `git ls-tree -r --name-only HEAD`              | Inspect commit tree         | `git ls-tree -r --name-only HEAD`              | Unchanged                             | File paths        |
| `git cat-file -t HEAD`                         | Determine object type       | `git cat-file -t HEAD`                         | Unchanged                             | Object type       |
| `git cat-file -p HEAD`                         | Inspect object              | `git cat-file -p HEAD`                         | Unchanged                             | Object contents   |
| `git fsck --full`                              | Verify repository           | `git fsck --full`                              | Unchanged                             | Diagnostics       |
| `git count-objects -vH`                        | Inspect object storage      | `git count-objects -vH`                        | Unchanged                             | Statistics        |
| `git remote -v`                                | Show remote URLs            | `git remote -v`                                | Unchanged                             | URLs              |
| `git remote show origin`                       | Inspect remote tracking     | `git remote show origin`                       | Unchanged                             | Remote details    |
| `git fetch --dry-run`                          | Preview fetch               | `git fetch --dry-run`                          | Unchanged                             | Fetch information |
| `git ls-remote origin`                         | Inspect remote refs         | `git ls-remote origin`                         | Unchanged                             | Remote refs       |
| `git config --list --show-origin --show-scope` | Diagnose configuration      | `git config --list --show-origin --show-scope` | Unchanged                             | Configuration     |
| `git --version`                                | Show Git version            | `git --version`                                | Unchanged                             | Version           |
| `git worktree list`                            | Show worktrees              | `git worktree list`                            | Unchanged                             | Worktrees         |
| `git submodule status --recursive`             | Diagnose submodules         | `git submodule status --recursive`             | Unchanged                             | Submodule states  |

---

# 24.36 Diagnostic Best Practices

## 1. Start with `git status`

When something unexpected happens:

```bash
git status -sb
```

is usually the best first command.

---

## 2. Inspect before modifying

Prefer:

```text
status
log
diff
branch
reflog
fsck
```

before:

```text
reset
rebase
restore
gc
prune
```

---

## 3. Use `reflog` for local recovery

If history appears to have disappeared:

```bash
git reflog --all
```

is one of the first commands to try.

---

## 4. Use `fsck` for object-level problems

For corruption or unreachable objects:

```bash
git fsck --full
```

provides much more useful information than simply running `git gc`.

---

## 5. Diagnose remote state separately

Local remote-tracking information may be stale.

Compare:

```bash
git branch --remotes
```

with:

```bash
git ls-remote origin
```

when necessary.

---

## 6. Check configuration when behavior is unexpected

Use:

```bash
git config --list --show-origin --show-scope
```

This can reveal:

* Global configuration
* Repository configuration
* Conditional includes
* Aliases
* Hooks paths
* Credential configuration
* Merge/rebase settings
* Remote configuration

---

## 7. Preserve evidence before recovery operations

When investigating a serious repository problem, first capture:

```bash
git status
git branch --all
git log --oneline --graph --decorate --all
git reflog --all
git fsck --full
git count-objects -vH
```

This gives you a diagnostic snapshot before making changes.

---

# Diagnostic Command Cheat Sheet

```bash
# Current repository state
git status -sb

# Current branch
git branch --show-current

# Current commit
git rev-parse HEAD

# Short current commit
git rev-parse --short HEAD

# Repository root
git rev-parse --show-toplevel

# Git directory
git rev-parse --git-dir

# Is this a Git work tree?
git rev-parse --is-inside-work-tree

# Is this a bare repository?
git rev-parse --is-bare-repository

# Is this a shallow repository?
git rev-parse --is-shallow-repository

# Local and remote branches
git branch --all

# Branch tracking information
git branch -vv

# Visual history
git log --oneline --graph --decorate --all

# Common ancestor
git merge-base main feature

# Ahead/behind count
git rev-list --left-right --count HEAD...@{upstream}

# Working-tree changes
git diff

# Staged changes
git diff --cached

# All local changes
git diff HEAD

# Tracked files
git ls-files

# Commit tree
git ls-tree -r --name-only HEAD

# Object type
git cat-file -t HEAD

# Object contents
git cat-file -p HEAD

# Repository integrity
git fsck --full

# Unreachable objects
git fsck --full --unreachable

# Repository object statistics
git count-objects -vH

# Remote URLs
git remote -v

# Remote details
git remote show origin

# Remote references
git ls-remote origin

# Preview fetch
git fetch --dry-run

# All configuration with source
git config --list --show-origin --show-scope

# Worktrees
git worktree list

# Submodules
git submodule status --recursive

# Git version
git --version
```

---

# Diagnostic Decision Tree

```text
Something is wrong
       |
       v
git status -sb
       |
       +---- Working-tree problem
       |          |
       |          +--> git diff
       |          +--> git diff --cached
       |          +--> git ls-files
       |
       +---- Branch problem
       |          |
       |          +--> git branch -vv
       |          +--> git reflog --all
       |          +--> git log --all --graph --decorate
       |
       +---- Remote problem
       |          |
       |          +--> git remote -v
       |          +--> git remote show origin
       |          +--> git ls-remote origin
       |
       +---- History problem
       |          |
       |          +--> git log --all
       |          +--> git reflog --all
       |          +--> git fsck --full
       |
       +---- Repository corruption
                  |
                  +--> git fsck --full
                  +--> git count-objects -vH
                  +--> inspect backups
```

---

# Final Diagnostic Checklist

```text
[ ] Run git status -sb
[ ] Identify current branch
[ ] Identify current commit
[ ] Inspect branch tracking information
[ ] Inspect recent history
[ ] Inspect reflogs when history appears missing
[ ] Inspect remotes when synchronization is suspicious
[ ] Check repository configuration
[ ] Check whether repository is shallow
[ ] Check worktrees when a branch appears locked
[ ] Check submodules when nested repositories are involved
[ ] Run git fsck when object integrity is suspected
[ ] Run git count-objects -vH when repository size is suspicious
[ ] Avoid destructive commands until the problem is understood
[ ] Preserve diagnostic information before recovery operations
```

---

# Key Concepts to Memorize

```text
git status -sb
    Fast repository/branch state

git branch -vv
    Branch/upstream relationships

git rev-parse
    Resolve Git repository and reference information

git show-ref
    Inspect references

git log --graph --all
    Visualize history

git merge-base
    Find common ancestors

git rev-list
    Analyze commit reachability/counts

git ls-files
    Inspect index/tracked files

git ls-tree
    Inspect commit trees

git cat-file
    Inspect Git objects

git fsck
    Check repository integrity

git count-objects
    Inspect object storage

git remote show
    Diagnose remote tracking

git ls-remote
    Inspect actual remote references

git reflog
    Recover local reference history

git config --show-origin --show-scope
    Diagnose configuration
```

---

## Next Part

**Next file:** `25-git-objects-internals.md`

[Next: Git Objects / Internals](25-git-objects-internals.md)
