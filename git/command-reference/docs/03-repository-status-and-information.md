# 3. Repository Status & Information

This chapter covers commands used to inspect the current state of a Git repository, working tree, staging area, branches, commits, remotes, repository paths, object databases, references, and tracking information.

---

## Table of Contents

* [3.1 Repository Status Overview](#31-repository-status-overview)
* [3.2 `git status`](#32-git-status)
* [3.3 Short Status](#33-short-status)
* [3.4 Porcelain Status](#34-porcelain-status)
* [3.5 Branch Information](#35-branch-information)
* [3.6 Current Branch](#36-current-branch)
* [3.7 Commit Information](#37-commit-information)
* [3.8 Repository Root and Git Directory](#38-repository-root-and-git-directory)
* [3.9 Repository Type](#39-repository-type)
* [3.10 Remote Information](#310-remote-information)
* [3.11 Upstream Tracking Information](#311-upstream-tracking-information)
* [3.12 HEAD Information](#312-head-information)
* [3.13 References](#313-references)
* [3.14 Tags](#314-tags)
* [3.15 Object Information](#315-object-information)
* [3.16 Repository Statistics](#316-repository-statistics)
* [3.17 Repository Connectivity and State](#317-repository-connectivity-and-state)
* [3.18 Repository Diagnostics](#318-repository-diagnostics)
* [3.19 Useful Inspection Commands](#319-useful-inspection-commands)
* [3.20 Developer Inspection Workflows](#320-developer-inspection-workflows)
* [3.21 DevOps Inspection Workflows](#321-devops-inspection-workflows)
* [3.22 Repository Status Command Summary](#322-repository-status-command-summary)

---

## 3.1 Repository Status Overview

Git maintains several important states:

```text
Working Tree
     │
     │ git add
     ▼
Staging Area / Index
     │
     │ git commit
     ▼
Repository History
     │
     │ git push
     ▼
Remote Repository
```

The most important command for inspecting the current working state is:

```bash
git status
```

Other commands answer more specific questions:

```bash
git branch
git log
git show
git remote
git rev-parse
git symbolic-ref
git describe
git count-objects
git fsck
```

These commands generally **inspect** the repository rather than modifying its history.

---

# 3.2 `git status`

## Basic status

```bash
git status
```

| Command      | Description                                  | Example      | Branch State Before and After command | Output                           |
| ------------ | -------------------------------------------- | ------------ | ------------------------------------- | -------------------------------- |
| `git status` | Displays working-tree and staging-area state | `git status` | `main` → `main`                       | Human-readable repository status |

Example:

```bash
git status
```

Possible output:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

If a file has been modified:

```text
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  modified:   app.js

no changes added to commit
```

If a file is staged:

```text
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  modified:   app.js
```

If a file is untracked:

```text
Untracked files:
  notes.txt
```

---

## Status with branch information

```bash
git status -b
```

This explicitly displays branch information.

Example:

```text
## main...origin/main
```

---

## Status with short output

```bash
git status -s
```

or:

```bash
git status --short
```

Example:

```text
 M app.js
M  README.md
?? notes.txt
```

The two columns have meaning:

```text
XY filename
││
│└── Working-tree status
└─── Index/staging status
```

Examples:

```text
 M file.txt
```

means the working tree changed.

```text
M  file.txt
```

means the file is staged.

```text
MM file.txt
```

means the file has both staged and unstaged changes.

```text
?? file.txt
```

means the file is untracked.

---

# 3.3 Short Status

Short status is particularly useful for scripts and quick terminal inspection.

| Command                       | Description                | Example                       | Branch State Before and After command | Output            |
| ----------------------------- | -------------------------- | ----------------------------- | ------------------------------------- | ----------------- |
| `git status -s`               | Compact status             | `git status -s`               | `main` → `main`                       | Two-column status |
| `git status --short`          | Long option equivalent     | `git status --short`          | `main` → `main`                       | Two-column status |
| `git status -sb`              | Compact status plus branch | `git status -sb`              | `main` → `main`                       | Branch + changes  |
| `git status --short --branch` | Explicit long form         | `git status --short --branch` | `main` → `main`                       | Branch + changes  |

Example:

```bash
git status -sb
```

Output:

```text
## main...origin/main
 M src/app.js
A  src/config.js
?? debug.log
```

This is one of the most useful commands for daily Git work.

---

# 3.4 Porcelain Status

Porcelain output is intended to be stable and suitable for scripts.

## Porcelain v1

```bash
git status --porcelain
```

Example:

```text
 M app.js
A  README.md
?? notes.txt
```

## Porcelain v2

```bash
git status --porcelain=v2
```

Example:

```text
1 .M N... 100644 100644 100644 abc123... def456... app.js
```

The exact output depends on repository state.

| Command                        | Description                       | Example                        | Branch State Before and After command | Output                                |
| ------------------------------ | --------------------------------- | ------------------------------ | ------------------------------------- | ------------------------------------- |
| `git status --porcelain`       | Machine-readable status           | `git status --porcelain`       | `main` → `main`                       | Stable compact status                 |
| `git status --porcelain=v2`    | Extended machine-readable status  | `git status --porcelain=v2`    | `main` → `main`                       | Detailed structured status            |
| `git status --porcelain -z`    | NUL-terminated output             | `git status --porcelain -z`    | `main` → `main`                       | Machine-readable NUL-separated output |
| `git status --porcelain=v2 -z` | Porcelain v2 with NUL termination | `git status --porcelain=v2 -z` | `main` → `main`                       | Structured NUL-separated output       |

For shell scripts, prefer porcelain formats instead of parsing human-readable `git status`.

---

# 3.5 Branch Information

## List local branches

```bash
git branch
```

Example:

```text
* main
  develop
  feature/login
```

The `*` identifies the current branch.

| Command             | Description                         | Example             | Branch State Before and After command | Output                     |
| ------------------- | ----------------------------------- | ------------------- | ------------------------------------- | -------------------------- |
| `git branch`        | Lists local branches                | `git branch`        | `main` → `main`                       | Local branch list          |
| `git branch --list` | Explicit branch listing             | `git branch --list` | `main` → `main`                       | Local branches             |
| `git branch -v`     | Shows latest commit per branch      | `git branch -v`     | `main` → `main`                       | Branch + commit            |
| `git branch -vv`    | Shows upstream tracking information | `git branch -vv`    | `main` → `main`                       | Branch + upstream + commit |

Example:

```bash
git branch -vv
```

Possible output:

```text
* main    abc1234 [origin/main] Add authentication
  develop def5678 [origin/develop] Refactor API
  feature 123abcd Add login form
```

---

## List remote-tracking branches

```bash
git branch -r
```

Example:

```text
origin/HEAD -> origin/main
origin/main
origin/develop
origin/feature/login
```

---

## List all branches

```bash
git branch -a
```

Example:

```text
* main
  develop
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/develop
```

---

## Show merged branches

```bash
git branch --merged
```

Example:

```text
* main
  feature/login
```

This lists branches whose tips are reachable from the current branch.

---

## Show unmerged branches

```bash
git branch --no-merged
```

Example:

```text
  feature/payment
  feature/api-v2
```

---

# 3.6 Current Branch

## `git branch --show-current`

```bash
git branch --show-current
```

Output:

```text
main
```

| Command                           | Description                         | Example                           | Branch State Before and After command | Output |
| --------------------------------- | ----------------------------------- | --------------------------------- | ------------------------------------- | ------ |
| `git branch --show-current`       | Displays current branch name        | `git branch --show-current`       | `main` → `main`                       | `main` |
| `git symbolic-ref --short HEAD`   | Resolves HEAD to branch name        | `git symbolic-ref --short HEAD`   | `main` → `main`                       | `main` |
| `git rev-parse --abbrev-ref HEAD` | Displays abbreviated HEAD reference | `git rev-parse --abbrev-ref HEAD` | `main` → `main`                       | `main` |

### Detached HEAD

If HEAD is detached:

```bash
git branch --show-current
```

may produce no branch name.

This is different from:

```bash
git rev-parse --short HEAD
```

which still displays the current commit.

---

# 3.7 Commit Information

## `git log`

```bash
git log
```

Displays commit history.

Example:

```text
commit abc1234...
Author: Developer <dev@example.com>
Date:   Wed Aug 12 2026

    Add authentication
```

| Command                                      | Description             | Example                                      | Branch State Before and After command | Output                |
| -------------------------------------------- | ----------------------- | -------------------------------------------- | ------------------------------------- | --------------------- |
| `git log`                                    | Displays commit history | `git log`                                    | `main` → `main`                       | Commit history        |
| `git log --oneline`                          | Compact commit history  | `git log --oneline`                          | `main` → `main`                       | Hash + subject        |
| `git log -n 5`                               | Shows last five commits | `git log -n 5`                               | `main` → `main`                       | Five commits          |
| `git log --decorate`                         | Shows refs in history   | `git log --decorate`                         | `main` → `main`                       | Commit history + refs |
| `git log --graph`                            | Displays ASCII graph    | `git log --graph`                            | `main` → `main`                       | Graph                 |
| `git log --all`                              | Includes all refs       | `git log --all`                              | `main` → `main`                       | All reachable history |
| `git log --oneline --graph --decorate --all` | Compact complete graph  | `git log --oneline --graph --decorate --all` | `main` → `main`                       | Graph + refs          |

A highly useful daily command:

```bash
git log --oneline --graph --decorate --all
```

---

## Show latest commit

```bash
git log -1
```

Compact:

```bash
git log -1 --oneline
```

Example:

```text
abc1234 Add authentication
```

---

## `git show`

```bash
git show
```

Displays the latest commit and its patch.

| Command                    | Description                 | Example                    | Branch State Before and After command | Output                      |
| -------------------------- | --------------------------- | -------------------------- | ------------------------------------- | --------------------------- |
| `git show`                 | Shows current `HEAD` commit | `git show`                 | `main` → `main`                       | Commit metadata + diff      |
| `git show HEAD`            | Explicitly shows HEAD       | `git show HEAD`            | `main` → `main`                       | Commit metadata + diff      |
| `git show --stat`          | Shows commit statistics     | `git show --stat`          | `main` → `main`                       | File statistics             |
| `git show --name-only`     | Shows changed filenames     | `git show --name-only`     | `main` → `main`                       | Filenames                   |
| `git show --format=fuller` | Shows detailed metadata     | `git show --format=fuller` | `main` → `main`                       | Detailed commit information |

---

# 3.8 Repository Root and Git Directory

## Show repository root

```bash
git rev-parse --show-toplevel
```

Example:

```text
/home/user/projects/application
```

This is extremely useful when your shell is somewhere deep inside the project:

```text
application/
└── src/
    └── services/
        └── auth/
```

From `auth/`:

```bash
git rev-parse --show-toplevel
```

still returns:

```text
/home/user/projects/application
```

---

## Show Git directory

```bash
git rev-parse --git-dir
```

Typical output:

```text
.git
```

From a subdirectory it may still resolve to:

```text
.git
```

depending on repository configuration.

---

## Show common Git directory

```bash
git rev-parse --git-common-dir
```

This is particularly relevant to linked worktrees, where the Git administrative directory can be shared.

---

## Show path relative to repository root

```bash
git rev-parse --show-prefix
```

From:

```text
project/src/api/
```

possible output:

```text
src/api/
```

---

## Show relative path to root

```bash
git rev-parse --show-cdup
```

If the current directory is:

```text
project/src/api/
```

the output can be:

```text
../../
```

| Command                          | Description                     | Example                          | Branch State Before and After command | Output                   |
| -------------------------------- | ------------------------------- | -------------------------------- | ------------------------------------- | ------------------------ |
| `git rev-parse --show-toplevel`  | Shows repository root           | `git rev-parse --show-toplevel`  | `main` → `main`                       | Absolute repository path |
| `git rev-parse --git-dir`        | Shows Git directory             | `git rev-parse --git-dir`        | `main` → `main`                       | Git directory            |
| `git rev-parse --git-common-dir` | Shows common Git directory      | `git rev-parse --git-common-dir` | `main` → `main`                       | Common Git directory     |
| `git rev-parse --show-prefix`    | Shows path from repository root | `git rev-parse --show-prefix`    | `main` → `main`                       | Relative prefix          |
| `git rev-parse --show-cdup`      | Shows path back to root         | `git rev-parse --show-cdup`      | `main` → `main`                       | Relative parent path     |

---

# 3.9 Repository Type

## Check working-tree state

```bash
git rev-parse --is-inside-work-tree
```

Output:

```text
true
```

## Check bare repository

```bash
git rev-parse --is-bare-repository
```

Normal repository:

```text
false
```

Bare repository:

```text
true
```

## Check Git directory

```bash
git rev-parse --is-inside-git-dir
```

Possible output:

```text
false
```

when inside the normal working tree.

| Command                               | Description                                             | Example                               | Branch State Before and After command | Output           |
| ------------------------------------- | ------------------------------------------------------- | ------------------------------------- | ------------------------------------- | ---------------- |
| `git rev-parse --is-inside-work-tree` | Tests whether current location is inside a working tree | `git rev-parse --is-inside-work-tree` | `main` → `main`                       | `true` / `false` |
| `git rev-parse --is-bare-repository`  | Tests whether repository is bare                        | `git rev-parse --is-bare-repository`  | No branch → No working branch         | `true` / `false` |
| `git rev-parse --is-inside-git-dir`   | Tests whether current location is inside Git directory  | `git rev-parse --is-inside-git-dir`   | `main` → `main`                       | `true` / `false` |

---

# 3.10 Remote Information

## List remotes

```bash
git remote
```

Example:

```text
origin
upstream
```

## List remote URLs

```bash
git remote -v
```

Example:

```text
origin  git@github.com:user/project.git (fetch)
origin  git@github.com:user/project.git (push)
```

| Command                           | Description                          | Example                           | Branch State Before and After command | Output                       |
| --------------------------------- | ------------------------------------ | --------------------------------- | ------------------------------------- | ---------------------------- |
| `git remote`                      | Lists remote names                   | `git remote`                      | `main` → `main`                       | Remote names                 |
| `git remote -v`                   | Lists remote URLs                    | `git remote -v`                   | `main` → `main`                       | Fetch/push URLs              |
| `git remote show origin`          | Displays detailed remote information | `git remote show origin`          | `main` → `main`                       | Remote branches and tracking |
| `git remote get-url origin`       | Displays remote URL                  | `git remote get-url origin`       | `main` → `main`                       | URL                          |
| `git remote get-url --all origin` | Displays all configured URLs         | `git remote get-url --all origin` | `main` → `main`                       | URLs                         |

---

## `git remote show`

```bash
git remote show origin
```

Typical information includes:

```text
* remote origin
  Fetch URL: ...
  Push  URL: ...
  HEAD branch: main
  Remote branches:
    main tracked
    develop tracked
  Local branch configured for 'git pull':
    main merges with remote main
```

This command is useful for understanding the relationship between local and remote branches.

---

# 3.11 Upstream Tracking Information

A local branch can track a remote-tracking branch.

Example:

```text
main
  ↓ tracks
origin/main
```

## `git branch -vv`

```bash
git branch -vv
```

Example:

```text
* main abc1234 [origin/main] Add API
```

If local and remote branches differ:

```text
* main abc1234 [origin/main: ahead 2, behind 1] Add API
```

---

## `git rev-parse @{upstream}`

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{upstream}
```

Output:

```text
origin/main
```

This is useful in scripts.

---

## Get upstream branch

```bash
git rev-parse @{upstream}
```

Example output:

```text
def456789...
```

This returns the commit ID of the upstream reference.

---

## Get remote-tracking branch name

```bash
git rev-parse --abbrev-ref @{upstream}
```

Output:

```text
origin/main
```

| Command                                  | Description              | Example                                  | Branch State Before and After command | Output            |
| ---------------------------------------- | ------------------------ | ---------------------------------------- | ------------------------------------- | ----------------- |
| `git branch -vv`                         | Shows upstream tracking  | `git branch -vv`                         | `main` → `main`                       | Branch + upstream |
| `git rev-parse @{upstream}`              | Resolves upstream commit | `git rev-parse @{upstream}`              | `main` → `main`                       | Commit hash       |
| `git rev-parse --abbrev-ref @{upstream}` | Shows upstream branch    | `git rev-parse --abbrev-ref @{upstream}` | `main` → `main`                       | `origin/main`     |

---

# 3.12 HEAD Information

`HEAD` identifies the currently checked-out commit or branch reference.

## Display HEAD

```bash
git rev-parse HEAD
```

Output:

```text
abc123456789...
```

## Short HEAD

```bash
git rev-parse --short HEAD
```

Output:

```text
abc1234
```

## Show symbolic HEAD

```bash
git symbolic-ref HEAD
```

Output:

```text
refs/heads/main
```

## Short symbolic HEAD

```bash
git symbolic-ref --short HEAD
```

Output:

```text
main
```

| Command                         | Description                   | Example                         | Branch State Before and After command | Output            |
| ------------------------------- | ----------------------------- | ------------------------------- | ------------------------------------- | ----------------- |
| `git rev-parse HEAD`            | Shows full HEAD commit ID     | `git rev-parse HEAD`            | `main` → `main`                       | Full SHA          |
| `git rev-parse --short HEAD`    | Shows abbreviated HEAD ID     | `git rev-parse --short HEAD`    | `main` → `main`                       | Short SHA         |
| `git symbolic-ref HEAD`         | Shows symbolic HEAD reference | `git symbolic-ref HEAD`         | `main` → `main`                       | `refs/heads/main` |
| `git symbolic-ref --short HEAD` | Shows short branch name       | `git symbolic-ref --short HEAD` | `main` → `main`                       | `main`            |

---

# 3.13 References

Git references point to commits.

Common reference namespaces include:

```text
refs/heads/
refs/remotes/
refs/tags/
```

Examples:

```text
refs/heads/main
refs/remotes/origin/main
refs/tags/v1.0.0
```

## List references

```bash
git show-ref
```

Example:

```text
abc123... refs/heads/main
def456... refs/remotes/origin/main
987abc... refs/tags/v1.0.0
```

## List heads

```bash
git show-ref --heads
```

## List remotes

```bash
git show-ref --remotes
```

## List tags

```bash
git show-ref --tags
```

| Command                  | Description             | Example                  | Branch State Before and After command | Output          |
| ------------------------ | ----------------------- | ------------------------ | ------------------------------------- | --------------- |
| `git show-ref`           | Lists references        | `git show-ref`           | `main` → `main`                       | SHA + reference |
| `git show-ref --heads`   | Lists local branch refs | `git show-ref --heads`   | `main` → `main`                       | Branch refs     |
| `git show-ref --remotes` | Lists remote refs       | `git show-ref --remotes` | `main` → `main`                       | Remote refs     |
| `git show-ref --tags`    | Lists tag refs          | `git show-ref --tags`    | `main` → `main`                       | Tag refs        |

---

# 3.14 Tags

## List tags

```bash
git tag
```

Example:

```text
v1.0.0
v1.1.0
v2.0.0
```

## List tags with patterns

```bash
git tag -l "v1.*"
```

Example:

```text
v1.0.0
v1.1.0
v1.2.0
```

## Show tag details

```bash
git show v1.0.0
```

## Find the latest tag

```bash
git describe --tags
```

Example:

```text
v1.2.0-3-gabc1234
```

This means:

```text
v1.2.0
   ↓
3 commits after tag
   ↓
commit abc1234
```

| Command               | Description                               | Example               | Branch State Before and After command | Output                |
| --------------------- | ----------------------------------------- | --------------------- | ------------------------------------- | --------------------- |
| `git tag`             | Lists tags                                | `git tag`             | `main` → `main`                       | Tag names             |
| `git tag -l "v1.*"`   | Lists matching tags                       | `git tag -l "v1.*"`   | `main` → `main`                       | Matching tags         |
| `git show <tag>`      | Shows tag/commit details                  | `git show v1.0.0`     | `main` → `main`                       | Tag metadata + commit |
| `git describe --tags` | Describes current commit relative to tags | `git describe --tags` | `main` → `main`                       | Tag-based description |

---

# 3.15 Object Information

Git stores objects such as:

```text
commit
tree
blob
tag
```

## Count objects

```bash
git count-objects
```

Example:

```text
42 objects, 168 kilobytes
```

With verbose output:

```bash
git count-objects -v
```

Example:

```text
count: 42
size: 168
in-pack: 1200
packs: 3
size-pack: 10240
prune-packable: 0
garbage: 0
size-garbage: 0
```

| Command                | Description                | Example                | Branch State Before and After command | Output                     |
| ---------------------- | -------------------------- | ---------------------- | ------------------------------------- | -------------------------- |
| `git count-objects`    | Counts loose objects       | `git count-objects`    | `main` → `main`                       | Object statistics          |
| `git count-objects -v` | Detailed object statistics | `git count-objects -v` | `main` → `main`                       | Object/database statistics |

---

## Check an object type

```bash
git cat-file -t HEAD
```

Output:

```text
commit
```

Check another object:

```bash
git cat-file -t HEAD^{tree}
```

Output:

```text
tree
```

---

## Display object contents

```bash
git cat-file -p HEAD
```

For a commit this displays commit metadata and message.

Example:

```text
tree abcdef...
parent 123456...
author Developer <dev@example.com>
committer Developer <dev@example.com>

Add authentication
```

| Command                    | Description          | Example                | Branch State Before and After command | Output          |
| -------------------------- | -------------------- | ---------------------- | ------------------------------------- | --------------- |
| `git cat-file -t <object>` | Displays object type | `git cat-file -t HEAD` | `main` → `main`                       | `commit`        |
| `git cat-file -p <object>` | Pretty-prints object | `git cat-file -p HEAD` | `main` → `main`                       | Object contents |
| `git cat-file -s <object>` | Displays object size | `git cat-file -s HEAD` | `main` → `main`                       | Size in bytes   |

---

# 3.16 Repository Statistics

## Count commits

```bash
git rev-list --count HEAD
```

Example:

```text
127
```

This indicates that 127 commits are reachable from `HEAD`.

## Count commits between branches

```bash
git rev-list --count main..feature
```

This counts commits reachable from `feature` but not from `main`.

## Count commits in both directions

```bash
git rev-list --left-right --count main...feature
```

Example:

```text
3 7
```

Interpretation:

```text
3 commits only on main
7 commits only on feature
```

| Command                                            | Description                        | Example                                            | Branch State Before and After command | Output       |
| -------------------------------------------------- | ---------------------------------- | -------------------------------------------------- | ------------------------------------- | ------------ |
| `git rev-list --count HEAD`                        | Counts commits reachable from HEAD | `git rev-list --count HEAD`                        | `main` → `main`                       | Commit count |
| `git rev-list --count main..feature`               | Counts feature-only commits        | `git rev-list --count main..feature`               | `main` → `main`                       | Number       |
| `git rev-list --left-right --count main...feature` | Counts unique commits on each side | `git rev-list --left-right --count main...feature` | `main` → `main`                       | Two counts   |

---

# 3.17 Repository Connectivity and State

## Check whether working tree is clean

A simple approach:

```bash
git status --porcelain
```

If there is no output, the working tree and index are clean.

Example:

```bash
if [ -z "$(git status --porcelain)" ]; then
    echo "Clean"
else
    echo "Changes detected"
fi
```

---

## Check whether branch has upstream

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{u}
```

Possible output:

```text
origin/main
```

If no upstream exists, Git reports an error.

---

## Check ahead/behind state

```bash
git status -sb
```

Possible output:

```text
## main...origin/main [ahead 2, behind 1]
```

Interpretation:

```text
ahead 2
```

means two local commits are not on the upstream branch.

```text
behind 1
```

means one upstream commit is not in the local branch.

---

# 3.18 Repository Diagnostics

## `git fsck`

`git fsck` checks repository object connectivity and validity.

```bash
git fsck
```

Example output:

```text
Checking connectivity: 100% (123/123), done.
```

It can report unreachable or dangling objects.

For more detailed output:

```bash
git fsck --full
```

| Command                  | Description                           | Example                  | Branch State Before and After command | Output                         |
| ------------------------ | ------------------------------------- | ------------------------ | ------------------------------------- | ------------------------------ |
| `git fsck`               | Checks repository object connectivity | `git fsck`               | `main` → `main`                       | Integrity information          |
| `git fsck --full`        | Performs full object checking         | `git fsck --full`        | `main` → `main`                       | Detailed integrity information |
| `git fsck --unreachable` | Shows unreachable objects             | `git fsck --unreachable` | `main` → `main`                       | Unreachable objects            |
| `git fsck --dangling`    | Shows dangling objects                | `git fsck --dangling`    | `main` → `main`                       | Dangling objects               |

These commands inspect repository data and normally do not alter branch history.

---

# 3.19 Useful Inspection Commands

## Show configured Git directory

```bash
git rev-parse --git-dir
```

## Show repository root

```bash
git rev-parse --show-toplevel
```

## Show current branch

```bash
git branch --show-current
```

## Show current commit

```bash
git rev-parse HEAD
```

## Show short current commit

```bash
git rev-parse --short HEAD
```

## Show current branch and commit

```bash
git status -sb
git log -1 --oneline
```

## Show remote

```bash
git remote -v
```

## Show upstream

```bash
git rev-parse --abbrev-ref @{upstream}
```

## Show latest commit

```bash
git log -1 --oneline
```

## Show recent history

```bash
git log --oneline -10
```

## Show complete graph

```bash
git log --oneline --graph --decorate --all
```

---

# 3.20 Developer Inspection Workflows

## Workflow A — Quickly inspect repository state

```bash
git status -sb
git branch -vv
git remote -v
```

This gives:

1. current branch and changes
2. upstream tracking
3. remote URLs

---

## Workflow B — Identify exactly where you are

```bash
git rev-parse --show-toplevel
git branch --show-current
git rev-parse --short HEAD
```

Example:

```text
/home/user/project
main
abc1234
```

---

## Workflow C — Inspect latest commit

```bash
git log -1 --oneline
git show --stat
```

---

## Workflow D — Inspect complete branch structure

```bash
git branch -a
git log --oneline --graph --decorate --all
```

---

## Workflow E — Determine whether the repository is clean

```bash
git status --porcelain
```

No output:

```text
clean
```

Any output:

```text
changes exist
```

---

## Workflow F — Determine whether local branch is ahead or behind

```bash
git status -sb
```

Possible:

```text
## main...origin/main
```

Clean and synchronized.

Or:

```text
## main...origin/main [ahead 2]
```

Local branch is ahead.

Or:

```text
## main...origin/main [behind 3]
```

Local branch is behind.

Or:

```text
## main...origin/main [ahead 2, behind 1]
```

Branches have diverged.

---

# 3.21 DevOps Inspection Workflows

## CI: Verify repository state

A CI script can use:

```bash
git status --porcelain
```

and:

```bash
git rev-parse --short HEAD
```

Example:

```bash
echo "Commit: $(git rev-parse --short HEAD)"

if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is not clean"
    exit 1
fi
```

---

## CI: Get repository root

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
echo "$REPO_ROOT"
```

Useful when scripts may be executed from arbitrary subdirectories.

---

## CI: Get branch name

```bash
BRANCH="$(git branch --show-current)"
echo "$BRANCH"
```

For detached HEAD environments, use:

```bash
git rev-parse --abbrev-ref HEAD
```

which may return:

```text
HEAD
```

when detached.

---

## CI: Get commit SHA

Full SHA:

```bash
git rev-parse HEAD
```

Short SHA:

```bash
git rev-parse --short HEAD
```

Example:

```bash
COMMIT_SHA="$(git rev-parse HEAD)"
SHORT_SHA="$(git rev-parse --short HEAD)"

echo "Commit: $COMMIT_SHA"
echo "Short commit: $SHORT_SHA"
```

---

## CI: Get version from Git

If the repository uses tags:

```bash
git describe --tags --always
```

Example:

```text
v2.1.0-4-gabc1234
```

If no tag is reachable, `--always` provides an object name instead.

---

# 3.22 Repository Status Command Summary

| Command                                            | Description                      | Example                                            | Branch State Before and After command | Output                         |
| -------------------------------------------------- | -------------------------------- | -------------------------------------------------- | ------------------------------------- | ------------------------------ |
| `git status`                                       | Full repository status           | `git status`                                       | `main` → `main`                       | Human-readable status          |
| `git status -s`                                    | Short status                     | `git status -s`                                    | `main` → `main`                       | Compact changes                |
| `git status -sb`                                   | Short status + branch            | `git status -sb`                                   | `main` → `main`                       | Branch + changes               |
| `git status --porcelain`                           | Script-friendly status           | `git status --porcelain`                           | `main` → `main`                       | Stable machine-readable output |
| `git status --porcelain=v2`                        | Extended machine-readable status | `git status --porcelain=v2`                        | `main` → `main`                       | Structured output              |
| `git branch`                                       | List local branches              | `git branch`                                       | `main` → `main`                       | Branch list                    |
| `git branch -a`                                    | List local and remote branches   | `git branch -a`                                    | `main` → `main`                       | All branches                   |
| `git branch -r`                                    | List remote-tracking branches    | `git branch -r`                                    | `main` → `main`                       | Remote branches                |
| `git branch -vv`                                   | Show branch tracking information | `git branch -vv`                                   | `main` → `main`                       | Branch + upstream + commit     |
| `git branch --merged`                              | List merged branches             | `git branch --merged`                              | `main` → `main`                       | Merged branches                |
| `git branch --no-merged`                           | List unmerged branches           | `git branch --no-merged`                           | `main` → `main`                       | Unmerged branches              |
| `git branch --show-current`                        | Show current branch              | `git branch --show-current`                        | `main` → `main`                       | `main`                         |
| `git log`                                          | Show commit history              | `git log`                                          | `main` → `main`                       | Commit history                 |
| `git log --oneline`                                | Compact history                  | `git log --oneline`                                | `main` → `main`                       | Short history                  |
| `git log --graph --all --decorate --oneline`       | Show complete commit graph       | `git log --graph --all --decorate --oneline`       | `main` → `main`                       | Graph                          |
| `git log -1 --oneline`                             | Show latest commit               | `git log -1 --oneline`                             | `main` → `main`                       | Latest commit                  |
| `git show`                                         | Show current commit              | `git show`                                         | `main` → `main`                       | Commit + patch                 |
| `git show --stat`                                  | Show commit statistics           | `git show --stat`                                  | `main` → `main`                       | File statistics                |
| `git rev-parse HEAD`                               | Show current commit SHA          | `git rev-parse HEAD`                               | `main` → `main`                       | Full SHA                       |
| `git rev-parse --short HEAD`                       | Show abbreviated SHA             | `git rev-parse --short HEAD`                       | `main` → `main`                       | Short SHA                      |
| `git rev-parse --show-toplevel`                    | Show repository root             | `git rev-parse --show-toplevel`                    | `main` → `main`                       | Absolute path                  |
| `git rev-parse --git-dir`                          | Show Git directory               | `git rev-parse --git-dir`                          | `main` → `main`                       | Git directory                  |
| `git rev-parse --git-common-dir`                   | Show common Git directory        | `git rev-parse --git-common-dir`                   | `main` → `main`                       | Common directory               |
| `git rev-parse --is-inside-work-tree`              | Check working tree               | `git rev-parse --is-inside-work-tree`              | `main` → `main`                       | `true` / `false`               |
| `git rev-parse --is-bare-repository`               | Check bare repository            | `git rev-parse --is-bare-repository`               | `main` → `main`                       | `true` / `false`               |
| `git symbolic-ref HEAD`                            | Show HEAD reference              | `git symbolic-ref HEAD`                            | `main` → `main`                       | `refs/heads/main`              |
| `git symbolic-ref --short HEAD`                    | Show HEAD branch                 | `git symbolic-ref --short HEAD`                    | `main` → `main`                       | `main`                         |
| `git remote`                                       | List remotes                     | `git remote`                                       | `main` → `main`                       | Remote names                   |
| `git remote -v`                                    | List remote URLs                 | `git remote -v`                                    | `main` → `main`                       | Fetch/push URLs                |
| `git remote show origin`                           | Show remote details              | `git remote show origin`                           | `main` → `main`                       | Remote state                   |
| `git remote get-url origin`                        | Get remote URL                   | `git remote get-url origin`                        | `main` → `main`                       | URL                            |
| `git rev-parse @{upstream}`                        | Resolve upstream commit          | `git rev-parse @{upstream}`                        | `main` → `main`                       | Commit SHA                     |
| `git rev-parse --abbrev-ref @{upstream}`           | Show upstream branch             | `git rev-parse --abbrev-ref @{upstream}`           | `main` → `main`                       | `origin/main`                  |
| `git show-ref`                                     | List references                  | `git show-ref`                                     | `main` → `main`                       | SHA + refs                     |
| `git show-ref --heads`                             | List local branch refs           | `git show-ref --heads`                             | `main` → `main`                       | Head refs                      |
| `git show-ref --remotes`                           | List remote refs                 | `git show-ref --remotes`                           | `main` → `main`                       | Remote refs                    |
| `git show-ref --tags`                              | List tag refs                    | `git show-ref --tags`                              | `main` → `main`                       | Tag refs                       |
| `git tag`                                          | List tags                        | `git tag`                                          | `main` → `main`                       | Tags                           |
| `git tag -l "v1.*"`                                | List matching tags               | `git tag -l "v1.*"`                                | `main` → `main`                       | Matching tags                  |
| `git show <tag>`                                   | Show tag information             | `git show v1.0.0`                                  | `main` → `main`                       | Tag + commit                   |
| `git describe --tags`                              | Describe commit using tags       | `git describe --tags`                              | `main` → `main`                       | Version description            |
| `git count-objects`                                | Count loose objects              | `git count-objects`                                | `main` → `main`                       | Object statistics              |
| `git count-objects -v`                             | Detailed object statistics       | `git count-objects -v`                             | `main` → `main`                       | Object/database information    |
| `git cat-file -t HEAD`                             | Show object type                 | `git cat-file -t HEAD`                             | `main` → `main`                       | `commit`                       |
| `git cat-file -p HEAD`                             | Display object                   | `git cat-file -p HEAD`                             | `main` → `main`                       | Object contents                |
| `git rev-list --count HEAD`                        | Count reachable commits          | `git rev-list --count HEAD`                        | `main` → `main`                       | Count                          |
| `git rev-list --left-right --count main...feature` | Compare unique commit counts     | `git rev-list --left-right --count main...feature` | `main` → `main`                       | Two counts                     |
| `git fsck`                                         | Check object connectivity        | `git fsck`                                         | `main` → `main`                       | Integrity information          |
| `git fsck --full`                                  | Full repository integrity check  | `git fsck --full`                                  | `main` → `main`                       | Detailed integrity information |

---

# Quick Reference

## Most important status commands

```bash
git status
git status -sb
git status --porcelain
```

## Most important branch commands

```bash
git branch
git branch -a
git branch -vv
git branch --show-current
```

## Most important history commands

```bash
git log --oneline
git log --oneline --graph --decorate --all
git log -1 --oneline
git show
```

## Most important repository-location commands

```bash
git rev-parse --show-toplevel
git rev-parse --git-dir
git rev-parse --git-common-dir
```

## Most important commit identity commands

```bash
git rev-parse HEAD
git rev-parse --short HEAD
git symbolic-ref --short HEAD
```

## Most important remote commands

```bash
git remote -v
git remote show origin
git remote get-url origin
```

## Most important upstream commands

```bash
git branch -vv
git rev-parse --abbrev-ref @{upstream}
```

## Most important repository diagnostics

```bash
git count-objects -v
git fsck --full
```

---

# Practical Repository Inspection Sequence

When entering an unfamiliar Git repository, run:

```bash
git status -sb
git branch -a
git branch -vv
git remote -v
git log --oneline --graph --decorate --all -20
git rev-parse --show-toplevel
git rev-parse --short HEAD
```

This gives a compact overview of:

```text
Current branch
      ↓
Working-tree state
      ↓
Local branches
      ↓
Remote branches
      ↓
Upstream tracking
      ↓
Remote URLs
      ↓
Recent repository history
      ↓
Repository root
      ↓
Current commit
```

---

# Important Notes

## Inspection commands normally do not change history

The commands in this chapter are primarily read-only:

```text
git status
git branch
git log
git show
git remote
git rev-parse
git symbolic-ref
git describe
git count-objects
git fsck
```

They inspect repository state rather than creating commits or changing branches.

## `git status` is the first command to use when unsure

When you do not know the current repository state:

```bash
git status
```

is generally the safest starting point.

For a faster daily view:

```bash
git status -sb
```

## Use porcelain output in automation

Avoid parsing:

```bash
git status
```

in scripts.

Prefer:

```bash
git status --porcelain
```

or:

```bash
git status --porcelain=v2
```

because porcelain formats are intended for machine consumption.

---

## Next Part

**Next file:** `04-staging-and-committing.md`

[Next: Staging & Committing](04-staging-and-committing.md)
