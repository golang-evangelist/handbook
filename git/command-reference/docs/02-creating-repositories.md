# 2. Creating Repositories

This chapter covers commands used to create, initialize, clone, configure, and inspect Git repositories.

---

## Table of Contents

* [2.1 Repository Creation Overview](#21-repository-creation-overview)
* [2.2 Initialize a New Repository](#22-initialize-a-new-repository)
* [2.3 Initialize With a Specific Branch](#23-initialize-with-a-specific-branch)
* [2.4 Initialize a Bare Repository](#24-initialize-a-bare-repository)
* [2.5 Clone a Repository](#25-clone-a-repository)
* [2.6 Clone Into a Specific Directory](#26-clone-into-a-specific-directory)
* [2.7 Clone a Specific Branch](#27-clone-a-specific-branch)
* [2.8 Clone a Specific Commit Depth](#28-clone-a-specific-commit-depth)
* [2.9 Clone Without Checking Out Files](#29-clone-without-checking-out-files)
* [2.10 Clone Using Different Protocols](#210-clone-using-different-protocols)
* [2.11 Mirror a Repository](#211-mirror-a-repository)
* [2.12 Create a Repository From an Existing Directory](#212-create-a-repository-from-an-existing-directory)
* [2.13 Create an Initial Commit](#213-create-an-initial-commit)
* [2.14 Connect an Existing Repository to a Remote](#214-connect-an-existing-repository-to-a-remote)
* [2.15 Verify Repository Creation](#215-verify-repository-creation)
* [2.16 Bare vs Non-Bare Repositories](#216-bare-vs-non-bare-repositories)
* [2.17 Common Repository Creation Workflows](#217-common-repository-creation-workflows)
* [2.18 Repository Creation Command Summary](#218-repository-creation-command-summary)

---

## 2.1 Repository Creation Overview

There are two fundamental ways to obtain a Git repository:

1. Initialize a new repository.
2. Clone an existing repository.

### Initialize a new repository

```bash
git init
```

Typical workflow:

```text
Directory
   ↓
git init
   ↓
.git/
   ↓
Git repository
```

### Clone an existing repository

```bash
git clone <repository-url>
```

Typical workflow:

```text
Remote repository
       ↓
   git clone
       ↓
Local repository
       ↓
Working tree
```

---

## 2.2 Initialize a New Repository

### `git init`

| Command                | Description                                                | Example               | Branch State Before and After command | Output                                          |
| ---------------------- | ---------------------------------------------------------- | --------------------- | ------------------------------------- | ----------------------------------------------- |
| `git init`             | Initializes a Git repository in the current directory      | `git init`            | No branch → Initial/default branch    | `Initialized empty Git repository in .../.git/` |
| `git init <directory>` | Creates a directory and initializes a repository inside it | `git init my-project` | No branch → Initial/default branch    | Initialization message                          |
| `git init --quiet`     | Initializes without normal informational output            | `git init --quiet`    | No branch → Initial/default branch    | Usually no output                               |

Example:

```bash
mkdir my-project
cd my-project
git init
```

Typical output:

```text
Initialized empty Git repository in /home/user/my-project/.git/
```

The repository now contains a `.git` directory.

Check:

```bash
ls -la
```

Typical structure:

```text
.
..
.git
```

### What `git init` does

It creates the internal Git repository structure, including information used for:

* references
* objects
* configuration
* HEAD
* index
* hooks
* repository metadata

It does **not** automatically create a commit.

---

## 2.3 Initialize With a Specific Branch

Use `-b` or `--initial-branch` to specify the initial branch name.

| Command                              | Description                                           | Example                          | Branch State Before and After command | Output                 |
| ------------------------------------ | ----------------------------------------------------- | -------------------------------- | ------------------------------------- | ---------------------- |
| `git init -b <branch>`               | Initializes repository with a specific initial branch | `git init -b main`               | No branch → `main`                    | Initialization message |
| `git init --initial-branch=<branch>` | Long form                                             | `git init --initial-branch=main` | No branch → `main`                    | Initialization message |

Example:

```bash
git init -b main
```

Typical output:

```text
Initialized empty Git repository in /home/user/project/.git/
```

Check:

```bash
git branch --show-current
```

Possible output:

```text
main
```

This is preferable to initializing with one branch name and renaming it afterward.

---

## 2.4 Initialize a Bare Repository

A bare repository contains Git data without a normal working tree.

Use:

```bash
git init --bare
```

| Command                   | Description                                              | Example                            | Branch State Before and After command | Output                         |
| ------------------------- | -------------------------------------------------------- | ---------------------------------- | ------------------------------------- | ------------------------------ |
| `git init --bare`         | Creates a bare repository                                | `git init --bare repo.git`         | No working branch → No working branch | Bare repository initialization |
| `git init --bare -b main` | Creates a bare repository with an initial default branch | `git init --bare -b main repo.git` | No working branch → No working branch | Initialization message         |

Example:

```bash
mkdir project.git
git init --bare project.git
```

Typical output:

```text
Initialized empty Git repository in /home/user/project.git/
```

A bare repository does not have:

```text
working-tree files
```

Instead, it contains Git repository data directly.

Typical bare repository structure:

```text
HEAD
config
description
hooks/
info/
objects/
refs/
```

Bare repositories are commonly used as:

* central Git servers
* self-hosted remotes
* deployment repositories
* repositories used by CI/CD infrastructure

---

## 2.5 Clone a Repository

### `git clone`

`git clone` creates a local copy of an existing repository.

| Command                       | Description                       | Example                                             | Branch State Before and After command   | Output         |
| ----------------------------- | --------------------------------- | --------------------------------------------------- | --------------------------------------- | -------------- |
| `git clone <url>`             | Clones a repository               | `git clone https://example.com/project.git`         | No local branch → Default remote branch | Clone progress |
| `git clone <url> <directory>` | Clones into a specified directory | `git clone https://example.com/project.git app`     | No local branch → Default remote branch | Clone progress |
| `git clone --quiet <url>`     | Suppresses normal progress        | `git clone --quiet https://example.com/project.git` | No local branch → Default remote branch | Minimal output |

Example:

```bash
git clone https://example.com/project.git
```

Typical output:

```text
Cloning into 'project'...
remote: Enumerating objects: 100, done.
remote: Counting objects: 100% (100/100), done.
Receiving objects: 100% (100/100), done.
Resolving deltas: 100% (50/50), done.
```

Git normally:

1. creates the destination directory
2. initializes `.git`
3. configures the remote named `origin`
4. downloads repository objects
5. creates remote-tracking references
6. checks out the default branch

---

## 2.6 Clone Into a Specific Directory

Syntax:

```bash
git clone <url> <directory>
```

Example:

```bash
git clone https://example.com/project.git my-project
```

The repository is created in:

```text
my-project/
```

| Command                       | Description                 | Example                                             | Branch State Before and After command | Output         |
| ----------------------------- | --------------------------- | --------------------------------------------------- | ------------------------------------- | -------------- |
| `git clone <url> <directory>` | Specifies local destination | `git clone https://example.com/project.git backend` | No local branch → Default branch      | Clone progress |

Useful when you want a custom local directory name.

---

## 2.7 Clone a Specific Branch

Use:

```bash
git clone --branch <branch> <url>
```

or:

```bash
git clone -b <branch> <url>
```

Example:

```bash
git clone --branch develop https://example.com/project.git
```

| Command                             | Description                             | Example                                                      | Branch State Before and After command | Output         |
| ----------------------------------- | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------- | -------------- |
| `git clone -b <branch> <url>`       | Clones and checks out a specific branch | `git clone -b develop https://example.com/project.git`       | No branch → `develop`                 | Clone progress |
| `git clone --branch <branch> <url>` | Long form                               | `git clone --branch release https://example.com/project.git` | No branch → `release`                 | Clone progress |

Verify:

```bash
git branch --show-current
```

Output:

```text
develop
```

### Clone a tag

`--branch` can also select a tag.

Example:

```bash
git clone --branch v1.0.0 https://example.com/project.git
```

When a tag is checked out directly, Git normally places the repository into detached HEAD state.

---

## 2.8 Clone a Specific Commit Depth

A shallow clone downloads limited history.

### Clone only the latest commit

```bash
git clone --depth 1 https://example.com/project.git
```

| Command                      | Description                                        | Example                                                | Branch State Before and After command | Output         |
| ---------------------------- | -------------------------------------------------- | ------------------------------------------------------ | ------------------------------------- | -------------- |
| `git clone --depth 1 <url>`  | Creates a shallow clone containing limited history | `git clone --depth 1 https://example.com/project.git`  | No branch → Default branch            | Clone progress |
| `git clone --depth 10 <url>` | Retrieves approximately the specified depth        | `git clone --depth 10 https://example.com/project.git` | No branch → Default branch            | Clone progress |

Shallow clones are useful for:

* CI jobs
* Docker builds
* temporary builds
* large repositories where full history is unnecessary

Example:

```bash
git clone --depth 1 --branch main https://example.com/project.git
```

### Fetch more history later

A shallow repository can often be deepened:

```bash
git fetch --deepen=50
```

Or converted toward a full history:

```bash
git fetch --unshallow
```

---

## 2.9 Clone Without Checking Out Files

Use:

```bash
git clone --no-checkout <url>
```

or:

```bash
git clone -n <url>
```

Example:

```bash
git clone --no-checkout https://example.com/project.git
```

| Command                         | Description                                             | Example                                                   | Branch State Before and After command                       | Output         |
| ------------------------------- | ------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------- | -------------- |
| `git clone --no-checkout <url>` | Clones repository without populating working tree files | `git clone --no-checkout https://example.com/project.git` | No branch → Default branch reference, files not checked out | Clone progress |
| `git clone -n <url>`            | Short form                                              | `git clone -n https://example.com/project.git`            | No branch → Default branch reference                        | Clone progress |

This is useful when preparing for:

* sparse checkout
* large repositories
* specialized working-tree setups

---

## 2.10 Clone Using Different Protocols

Git supports several transport mechanisms.

### HTTPS

```bash
git clone https://github.com/user/project.git
```

### SSH

```bash
git clone git@github.com:user/project.git
```

### Git protocol

Some public repositories may support:

```bash
git clone git://example.com/project.git
```

### Local filesystem path

```bash
git clone /home/user/project.git
```

or:

```bash
git clone file:///home/user/project.git
```

| Command                           | Description               | Example                                     | Branch State Before and After command | Output         |
| --------------------------------- | ------------------------- | ------------------------------------------- | ------------------------------------- | -------------- |
| `git clone https://...`           | Clones over HTTPS         | `git clone https://example.com/project.git` | No branch → Default branch            | Clone progress |
| `git clone git@...`               | Clones over SSH           | `git clone git@example.com:project.git`     | No branch → Default branch            | Clone progress |
| `git clone git://...`             | Clones using Git protocol | `git clone git://example.com/project.git`   | No branch → Default branch            | Clone progress |
| `git clone /path/repo.git`        | Clones from local path    | `git clone /srv/git/project.git`            | No branch → Default branch            | Clone progress |
| `git clone file:///path/repo.git` | Clones using file URL     | `git clone file:///srv/git/project.git`     | No branch → Default branch            | Clone progress |

For development and server administration, SSH and HTTPS are the most common remote transports.

---

## 2.11 Mirror a Repository

Use:

```bash
git clone --mirror <url>
```

Example:

```bash
git clone --mirror https://example.com/project.git
```

A mirror clone is intended for mirroring repositories and includes repository references beyond an ordinary working clone.

It is commonly used for:

* repository migration
* backup
* mirroring
* server-to-server synchronization

Typical follow-up:

```bash
git push --mirror <destination>
```

### Warning

`git push --mirror` can update or delete references on the destination to exactly match the source. It should be used carefully.

---

## 2.12 Create a Repository From an Existing Directory

Suppose you already have:

```text
my-application/
├── src/
├── tests/
├── README.md
└── package.json
```

Initialize it:

```bash
cd my-application
git init
```

Then inspect:

```bash
git status
```

Typical output:

```text
On branch main

No commits yet

Untracked files:
  README.md
  package.json
  src/
  tests/
```

The files are not automatically committed.

---

## 2.13 Create an Initial Commit

After initialization:

```bash
git add .
git commit -m "Initial commit"
```

Complete example:

```bash
mkdir my-project
cd my-project
git init -b main

echo "# My Project" > README.md

git add README.md
git commit -m "Initial commit"
```

Typical branch transition:

```text
No commits
    ↓
Initial commit
    ↓
main
```

Typical output:

```text
[main (root-commit) abc1234] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

| Command                     | Description          | Example                          | Branch State Before and After command         | Output            |
| --------------------------- | -------------------- | -------------------------------- | --------------------------------------------- | ----------------- |
| `git add <file>`            | Stages initial files | `git add README.md`              | `main` → `main`                               | Usually no output |
| `git commit -m "<message>"` | Creates first commit | `git commit -m "Initial commit"` | `main` at no commits → `main` at first commit | Commit summary    |

---

## 2.14 Connect an Existing Repository to a Remote

Suppose you created a local repository:

```bash
git init -b main
```

Add a remote:

```bash
git remote add origin git@github.com:user/project.git
```

Verify:

```bash
git remote -v
```

Typical output:

```text
origin  git@github.com:user/project.git (fetch)
origin  git@github.com:user/project.git (push)
```

Push:

```bash
git push -u origin main
```

| Command                       | Description                            | Example                                                 | Branch State Before and After command              | Output             |
| ----------------------------- | -------------------------------------- | ------------------------------------------------------- | -------------------------------------------------- | ------------------ |
| `git remote add origin <url>` | Adds a remote repository               | `git remote add origin git@github.com:user/project.git` | `main` → `main`                                    | Usually no output  |
| `git remote -v`               | Lists remote URLs                      | `git remote -v`                                         | `main` → `main`                                    | Remote information |
| `git push -u origin main`     | Pushes branch and establishes upstream | `git push -u origin main`                               | Local `main` → Local `main` tracking `origin/main` | Push progress      |

Complete workflow:

```bash
mkdir project
cd project

git init -b main

echo "# Project" > README.md

git add README.md
git commit -m "Initial commit"

git remote add origin git@github.com:user/project.git
git push -u origin main
```

---

## 2.15 Verify Repository Creation

### Check repository status

```bash
git status
```

### Show current branch

```bash
git branch --show-current
```

### Check whether the directory is a Git repository

```bash
git rev-parse --is-inside-work-tree
```

Output:

```text
true
```

### Show repository root

```bash
git rev-parse --show-toplevel
```

Example:

```text
/home/user/project
```

### Show Git directory

```bash
git rev-parse --git-dir
```

Typical output:

```text
.git
```

| Command                               | Description                                               | Example                               | Branch State Before and After command | Output             |
| ------------------------------------- | --------------------------------------------------------- | ------------------------------------- | ------------------------------------- | ------------------ |
| `git status`                          | Shows repository state                                    | `git status`                          | `main` → `main`                       | Status information |
| `git branch --show-current`           | Shows current branch                                      | `git branch --show-current`           | `main` → `main`                       | `main`             |
| `git rev-parse --is-inside-work-tree` | Checks whether current directory is inside a working tree | `git rev-parse --is-inside-work-tree` | `main` → `main`                       | `true`             |
| `git rev-parse --show-toplevel`       | Shows repository root                                     | `git rev-parse --show-toplevel`       | `main` → `main`                       | Repository path    |
| `git rev-parse --git-dir`             | Shows Git directory                                       | `git rev-parse --git-dir`             | `main` → `main`                       | `.git`             |

---

## 2.16 Bare vs Non-Bare Repositories

### Normal repository

Created with:

```bash
git init
```

Structure:

```text
project/
├── .git/
├── README.md
├── src/
└── tests/
```

It has:

* Git database
* working tree
* current branch
* index

### Bare repository

Created with:

```bash
git init --bare project.git
```

Structure:

```text
project.git/
├── HEAD
├── config
├── hooks/
├── objects/
├── refs/
└── ...
```

It does not have a normal working tree.

### Comparison

| Repository Type | Command           | Working Tree | Typical Use              |
| --------------- | ----------------- | -----------: | ------------------------ |
| Normal          | `git init`        |          Yes | Development              |
| Bare            | `git init --bare` |           No | Server/remote repository |

---

## 2.17 Common Repository Creation Workflows

### Workflow A — New local project

```bash
mkdir my-project
cd my-project

git init -b main

echo "# My Project" > README.md

git add README.md
git commit -m "Initial commit"
```

Result:

```text
main
└── Initial commit
```

---

### Workflow B — New local project connected to remote

```bash
mkdir my-project
cd my-project

git init -b main

echo "# My Project" > README.md

git add README.md
git commit -m "Initial commit"

git remote add origin git@github.com:user/my-project.git
git push -u origin main
```

Result:

```text
Local:
main ──────────────┐
                   │
Remote:
origin/main ───────┘
```

---

### Workflow C — Clone an existing project

```bash
git clone git@github.com:user/project.git
cd project
```

Inspect:

```bash
git status
git branch --show-current
git remote -v
```

---

### Workflow D — Shallow CI clone

```bash
git clone --depth 1 --branch main https://example.com/project.git
```

This minimizes downloaded history.

---

### Workflow E — Clone without checkout

```bash
git clone --no-checkout https://example.com/project.git
cd project
```

This is useful before configuring specialized working-tree behavior.

---

### Workflow F — Repository mirror

```bash
git clone --mirror https://source.example.com/project.git
cd project.git
git push --mirror https://destination.example.com/project.git
```

Use this only when the destination is intended to mirror the source references.

---

### Workflow G — Create a bare server repository

On a server:

```bash
mkdir -p /srv/git/project.git
git init --bare --initial-branch=main /srv/git/project.git
```

Developers can then use:

```bash
git clone user@server:/srv/git/project.git
```

---

## 2.18 Repository Creation Command Summary

| Command                               | Description                                | Example                                                   | Branch State Before and After command  | Output                 |
| ------------------------------------- | ------------------------------------------ | --------------------------------------------------------- | -------------------------------------- | ---------------------- |
| `git init`                            | Initialize repository                      | `git init`                                                | No repository → Repository initialized | Initialization message |
| `git init -b main`                    | Initialize with `main`                     | `git init -b main`                                        | No branch → `main`                     | Initialization message |
| `git init --initial-branch=main`      | Long form for initial branch               | `git init --initial-branch=main`                          | No branch → `main`                     | Initialization message |
| `git init --bare repo.git`            | Create bare repository                     | `git init --bare repo.git`                                | No working branch → No working branch  | Initialization message |
| `git clone <url>`                     | Clone repository                           | `git clone https://example.com/project.git`               | No local branch → Default branch       | Clone progress         |
| `git clone <url> <dir>`               | Clone to directory                         | `git clone https://example.com/project.git app`           | No branch → Default branch             | Clone progress         |
| `git clone -b <branch> <url>`         | Clone specific branch                      | `git clone -b develop https://example.com/project.git`    | No branch → `develop`                  | Clone progress         |
| `git clone --depth 1 <url>`           | Shallow clone                              | `git clone --depth 1 https://example.com/project.git`     | No branch → Default branch             | Clone progress         |
| `git clone --no-checkout <url>`       | Clone without checkout                     | `git clone --no-checkout https://example.com/project.git` | No branch → Default branch reference   | Clone progress         |
| `git clone --mirror <url>`            | Create repository mirror                   | `git clone --mirror https://example.com/project.git`      | No branch → Reference mirror           | Clone progress         |
| `git remote add origin <url>`         | Add remote                                 | `git remote add origin git@github.com:user/project.git`   | `main` → `main`                        | Usually no output      |
| `git remote -v`                       | Display remotes                            | `git remote -v`                                           | `main` → `main`                        | Remote URLs            |
| `git push -u origin main`             | Push initial branch and configure upstream | `git push -u origin main`                                 | Local `main` → Tracking `origin/main`  | Push progress          |
| `git status`                          | Inspect repository state                   | `git status`                                              | `main` → `main`                        | Status                 |
| `git branch --show-current`           | Display current branch                     | `git branch --show-current`                               | `main` → `main`                        | `main`                 |
| `git rev-parse --show-toplevel`       | Display repository root                    | `git rev-parse --show-toplevel`                           | `main` → `main`                        | Repository path        |
| `git rev-parse --git-dir`             | Display Git directory                      | `git rev-parse --git-dir`                                 | `main` → `main`                        | `.git`                 |
| `git rev-parse --is-inside-work-tree` | Test working-tree status                   | `git rev-parse --is-inside-work-tree`                     | `main` → `main`                        | `true`                 |

---

## Quick Reference

### Create a new repository

```bash
mkdir project
cd project
git init -b main
```

### Create first commit

```bash
echo "# Project" > README.md
git add README.md
git commit -m "Initial commit"
```

### Add remote

```bash
git remote add origin git@github.com:user/project.git
```

### Push

```bash
git push -u origin main
```

### Clone

```bash
git clone git@github.com:user/project.git
```

### Clone a specific branch

```bash
git clone --branch develop git@github.com:user/project.git
```

### Shallow clone

```bash
git clone --depth 1 git@github.com:user/project.git
```

### Create bare repository

```bash
git init --bare --initial-branch=main project.git
```

### Verify repository

```bash
git status
git branch --show-current
git remote -v
git rev-parse --show-toplevel
```

---

## Important Notes

### `git init` does not create a commit

After:

```bash
git init -b main
```

there may be no commits yet.

You must explicitly create one:

```bash
git add .
git commit -m "Initial commit"
```

### `git clone` is more than a download

A clone normally creates:

```text
.git/
working tree
origin remote
remote-tracking references
local branch
```

### Bare repositories are not normal development directories

Do not normally edit application files directly inside:

```text
project.git/
```

A bare repository is designed primarily to act as a Git repository endpoint.

### Shallow clones have incomplete history

With:

```bash
git clone --depth 1 ...
```

commands that require older history may behave differently because the complete history is not available locally.

---

## Next Part

**Next file:** `03-repository-status-and-information.md`

[Next: Repository Status & Information](../03-repository-status-and-information.md)
