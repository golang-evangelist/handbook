# 21. Git LFS

Git LFS (Large File Storage) is an extension for Git designed to handle large binary files efficiently.

Instead of storing the complete contents of large files directly inside normal Git commits, Git stores a small **pointer file**, while the actual binary content is stored through Git LFS.

Typical Git LFS use cases include:

* `.psd` Photoshop files
* `.zip` archives
* Videos
* Audio files
* Large images
* Game assets
* Machine-learning datasets
* Compiled binaries
* CAD files
* Large documents

---

# Table of Contents

* [21.1 What Is Git LFS?](#211-what-is-git-lfs)
* [21.2 Why Use Git LFS?](#212-why-use-git-lfs)
* [21.3 Git LFS Architecture](#213-git-lfs-architecture)
* [21.4 Checking Git LFS Installation](#214-checking-git-lfs-installation)
* [21.5 Installing Git LFS](#215-installing-git-lfs)
* [21.6 Initializing Git LFS](#216-initializing-git-lfs)
* [21.7 Checking Git LFS Version](#217-checking-git-lfs-version)
* [21.8 Tracking Files With Git LFS](#218-tracking-files-with-git-lfs)
* [21.9 Tracking File Extensions](#219-tracking-file-extensions)
* [21.10 Tracking Directories](#2110-tracking-directories)
* [21.11 Checking LFS Tracking Rules](#2111-checking-lfs-tracking-rules)
* [21.12 Committing LFS Files](#2112-committing-lfs-files)
* [21.13 Pushing LFS Objects](#2113-pushing-lfs-objects)
* [21.14 Fetching LFS Objects](#2114-fetching-lfs-objects)
* [21.15 Pulling LFS Objects](#2115-pulling-lfs-objects)
* [21.16 Downloading LFS Objects](#2116-downloading-lfs-objects)
* [21.17 Uploading LFS Objects](#2117-uploading-lfs-objects)
* [21.18 Listing LFS Files](#2118-listing-lfs-files)
* [21.19 Inspecting LFS Environment](#2119-inspecting-lfs-environment)
* [21.20 Inspecting LFS Logs](#2120-inspecting-lfs-logs)
* [21.21 Checking LFS Storage](#2121-checking-lfs-storage)
* [21.22 Migrating Existing Files to LFS](#2122-migrating-existing-files-to-lfs)
* [21.23 Migrating History](#2123-migrating-history)
* [21.24 Converting Existing LFS Files](#2124-converting-existing-lfs-files)
* [21.25 Untracking Files](#2125-untracking-files)
* [21.26 Removing LFS Tracking Rules](#2126-removing-lfs-tracking-rules)
* [21.27 Fetching Specific LFS Objects](#2127-fetching-specific-lfs-objects)
* [21.28 Working With LFS and Branches](#2128-working-with-lfs-and-branches)
* [21.29 Working With LFS and Remotes](#2129-working-with-lfs-and-remotes)
* [21.30 LFS and CI/CD](#2130-lfs-and-cicd)
* [21.31 LFS and Shallow Clones](#2131-lfs-and-shallow-clones)
* [21.32 LFS and Worktrees](#2132-lfs-and-worktrees)
* [21.33 LFS Troubleshooting](#2133-lfs-troubleshooting)
* [21.34 Dangerous and Expensive LFS Operations](#2134-dangerous-and-expensive-lfs-operations)
* [21.35 High-Value Git LFS Commands](#2135-high-value-git-lfs-commands)

---

# 21.1 What Is Git LFS?

Git LFS replaces large files in Git's normal object database with small pointer files.

For example, a large file:

```text
model.zip
```

might be represented in Git by a small text pointer similar to:

```text
version https://git-lfs.github.com/spec/v1
oid sha256:...
size 524288000
```

The actual binary content is stored in LFS storage.

The Git repository therefore contains:

```text
Git repository
    |
    +-- Git commits
    +-- Git trees
    +-- LFS pointer files
```

while LFS storage contains:

```text
Git LFS storage
    |
    +-- Large binary objects
```

---

# 21.2 Why Use Git LFS?

Normal Git is excellent for source code and relatively small text files.

Large binary files can create problems because Git stores historical versions of files as repository objects.

For example:

```text
video.mp4
```

changes from:

```text
500 MB
```

to:

```text
510 MB
```

to:

```text
520 MB
```

Without LFS, the repository can become very large.

With LFS, Git stores lightweight pointer files while the binary data is handled separately.

---

# 21.3 Git LFS Architecture

The basic architecture is:

```text
Working Tree
     |
     v
Git LFS filter
     |
     +------------------+
     |                  |
     v                  v
Git Repository       LFS Storage
(pointer)            (binary)
```

When you checkout an LFS file, Git LFS replaces the pointer with the actual content in the working directory.

When you commit it, Git stores the pointer in Git.

---

# 21.4 Checking Git LFS Installation

Check whether Git LFS is installed:

```bash
git lfs version
```

Example:

```text
git-lfs/3.x.x (GitHub; linux amd64; go ...)
```

You can also use:

```bash
git lfs --version
```

If Git LFS is not installed, the command may fail with a message indicating that the command is unavailable.

---

| Command             | Description             | Example             | Branch State Before and After command | Output                |
| ------------------- | ----------------------- | ------------------- | ------------------------------------- | --------------------- |
| `git lfs version`   | Display Git LFS version | `git lfs version`   | Unchanged                             | Installed LFS version |
| `git lfs --version` | Display Git LFS version | `git lfs --version` | Unchanged                             | Installed LFS version |

---

# 21.5 Installing Git LFS

Installation depends on the Linux distribution.

On Debian/Ubuntu:

```bash
sudo apt update
sudo apt install git-lfs
```

On Fedora:

```bash
sudo dnf install git-lfs
```

On Arch Linux:

```bash
sudo pacman -S git-lfs
```

Verify:

```bash
git lfs version
```

Git LFS installation is separate from the core Git package on many Linux distributions.

---

# 21.6 Initializing Git LFS

After installation:

```bash
git lfs install
```

This configures Git LFS filters for the current user.

Example:

```bash
git lfs install
```

Typical output:

```text
Updated Git hooks.
Git LFS initialized.
```

You can inspect configuration:

```bash
git config --list | grep lfs
```

Depending on the Git LFS version and configuration, the exact output can vary.

---

| Command                   | Description                          | Example                   | Branch State Before and After command | Output                     |
| ------------------------- | ------------------------------------ | ------------------------- | ------------------------------------- | -------------------------- |
| `git lfs install`         | Initialize Git LFS                   | `git lfs install`         | Unchanged                             | LFS initialization message |
| `git lfs install --force` | Force LFS installation/configuration | `git lfs install --force` | Unchanged                             | Updated LFS configuration  |

---

# 21.7 Checking LFS Version

Use:

```bash
git lfs version
```

Example:

```text
git-lfs/3.6.1
```

This is useful for diagnosing:

* Version compatibility
* CI environments
* Developer workstation configuration
* LFS authentication problems

---

# 21.8 Tracking Files With Git LFS

Track a specific file:

```bash
git lfs track "model.bin"
```

This updates `.gitattributes`.

Check:

```bash
cat .gitattributes
```

Example:

```text
model.bin filter=lfs diff=lfs merge=lfs -text
```

Then:

```bash
git add .gitattributes model.bin
git commit -m "Track model with Git LFS"
```

---

# 21.9 Tracking File Extensions

Track all files with an extension:

```bash
git lfs track "*.psd"
```

Example:

```bash
git lfs track "*.zip"
```

Multiple extensions:

```bash
git lfs track "*.zip"
git lfs track "*.tar.gz"
git lfs track "*.mp4"
git lfs track "*.bin"
```

The resulting `.gitattributes` contains the corresponding patterns.

---

| Command                 | Description           | Example                   | Branch State Before and After command | Output                   |
| ----------------------- | --------------------- | ------------------------- | ------------------------------------- | ------------------------ |
| `git lfs track FILE`    | Track one file        | `git lfs track model.bin` | Branch unchanged                      | `.gitattributes` updated |
| `git lfs track PATTERN` | Track matching files  | `git lfs track "*.zip"`   | Branch unchanged                      | `.gitattributes` updated |
| `git lfs track "*.psd"` | Track Photoshop files | `git lfs track "*.psd"`   | Branch unchanged                      | LFS pattern added        |
| `git lfs track "*.mp4"` | Track videos          | `git lfs track "*.mp4"`   | Branch unchanged                      | LFS pattern added        |

---

# 21.10 Tracking Directories

Track files under a directory:

```bash
git lfs track "assets/**"
```

For example:

```bash
git lfs track "models/**"
```

This is useful for repositories containing large binary assets.

Inspect:

```bash
cat .gitattributes
```

Remember that LFS tracking rules are Git attributes and should normally be committed:

```bash
git add .gitattributes
git commit -m "Configure Git LFS tracking"
```

---

# 21.11 Checking LFS Tracking Rules

List configured patterns:

```bash
git lfs track
```

Example:

```text
Listing tracked patterns
    *.psd (.gitattributes)
    *.zip (.gitattributes)
    models/** (.gitattributes)
```

Check whether a specific file is tracked:

```bash
git check-attr filter -- model.bin
```

Example:

```text
model.bin: filter: lfs
```

You can also inspect:

```bash
git check-attr diff merge text -- model.bin
```

---

# 21.12 Committing LFS Files

Suppose:

```text
model.bin
```

is tracked by LFS.

Stage:

```bash
git add model.bin
```

Check:

```bash
git status
```

Commit:

```bash
git commit -m "Add model"
```

Git stores the pointer in the commit.

The actual LFS object must also be uploaded to the LFS server.

---

# 21.13 Pushing LFS Objects

Normally:

```bash
git push
```

Git LFS uploads required objects as part of the push process.

For example:

```bash
git push origin main
```

You may see output indicating LFS object uploads before or alongside normal Git push output.

A useful explicit command is:

```bash
git lfs push origin main
```

This uploads LFS objects associated with the specified ref.

---

| Command                     | Description                               | Example                     | Branch State Before and After command  | Output                 |
| --------------------------- | ----------------------------------------- | --------------------------- | -------------------------------------- | ---------------------- |
| `git push`                  | Push Git commits and required LFS objects | `git push origin main`      | Local branch unchanged; remote updated | Push/LFS upload output |
| `git lfs push REMOTE REF`   | Explicitly push LFS objects               | `git lfs push origin main`  | Branch unchanged                       | LFS upload information |
| `git lfs push --all REMOTE` | Push all reachable LFS objects            | `git lfs push --all origin` | Branch unchanged                       | Uploaded LFS objects   |

---

# 21.14 Fetching LFS Objects

Fetch Git refs:

```bash
git fetch origin
```

To fetch LFS objects:

```bash
git lfs fetch origin
```

Fetch all LFS objects reachable from refs:

```bash
git lfs fetch --all origin
```

Unlike a normal Git fetch, LFS object downloads can be controlled separately.

---

# 21.15 Pulling LFS Objects

Normal workflow:

```bash
git pull
```

Git LFS downloads required objects for the checked-out revision.

You can also explicitly run:

```bash
git lfs pull
```

This is useful when:

* A repository was cloned without downloading all LFS objects.
* LFS files are represented by pointers.
* You need to synchronize LFS content.

---

# 21.16 Downloading LFS Objects

Download LFS objects:

```bash
git lfs pull
```

Download without updating Git refs:

```bash
git lfs fetch
```

Download specific files using include filters:

```bash
git lfs fetch --include="models/**"
```

Exclude:

```bash
git lfs fetch --exclude="videos/**"
```

Include and exclude:

```bash
git lfs fetch --include="models/**" --exclude="models/archive/**"
```

---

# 21.17 Uploading LFS Objects

Explicit upload:

```bash
git lfs push origin main
```

Upload all reachable LFS objects:

```bash
git lfs push --all origin
```

This can be useful after:

* Repository migration
* Remote reconstruction
* Server-side LFS migration
* Recovering missing LFS objects

`--all` can upload a large amount of data, so use it intentionally.

---

# 21.18 Listing LFS Files

List LFS-managed files:

```bash
git lfs ls-files
```

Example:

```text
abc1234567 * model.bin
def9876543 * assets/video.mp4
```

Show more information:

```bash
git lfs ls-files --long
```

Show file names only:

```bash
git lfs ls-files --name-only
```

Show files in another ref:

```bash
git lfs ls-files main
```

---

| Command                        | Description             | Example                        | Branch State Before and After command | Output               |
| ------------------------------ | ----------------------- | ------------------------------ | ------------------------------------- | -------------------- |
| `git lfs ls-files`             | List LFS files          | `git lfs ls-files`             | Unchanged                             | LFS files            |
| `git lfs ls-files --long`      | Show full object IDs    | `git lfs ls-files --long`      | Unchanged                             | Detailed LFS objects |
| `git lfs ls-files --name-only` | Show names only         | `git lfs ls-files --name-only` | Unchanged                             | File paths           |
| `git lfs ls-files REF`         | List LFS files from ref | `git lfs ls-files main`        | Unchanged                             | LFS files from ref   |

---

# 21.19 Inspecting LFS Environment

Use:

```bash
git lfs env
```

This is one of the most useful troubleshooting commands.

It can show information about:

* Git version
* Git LFS version
* LFS endpoint
* Git directory
* Working directory
* Local configuration
* Transfer configuration

Example:

```bash
git lfs env
```

Use it when investigating:

```text
LFS upload failed
LFS download failed
authentication problems
incorrect LFS endpoint
CI configuration problems
```

---

# 21.20 Inspecting LFS Logs

Git LFS provides logging information through:

```bash
git lfs logs last
```

This displays information from the most recent LFS log.

Useful when diagnosing:

* HTTP errors
* Authentication failures
* Transfer failures
* Endpoint problems
* Batch API errors

You can inspect the most recent log after a failed command:

```bash
git lfs pull
git lfs logs last
```

---

# 21.21 Checking LFS Storage

Git LFS stores objects locally in its object cache.

A common location is:

```text
.git/lfs/objects/
```

Inspect:

```bash
du -sh .git/lfs
```

Inspect objects:

```bash
find .git/lfs/objects -type f
```

Check repository size:

```bash
du -sh .git
```

Check working tree size:

```bash
du -sh .
```

Be careful when manually modifying `.git/lfs`.

Git LFS should normally manage its object storage.

---

# 21.22 Migrating Existing Files to LFS

Suppose a large file was already committed:

```text
model.bin
```

and you now want Git LFS to manage it.

First configure tracking:

```bash
git lfs track "*.bin"
```

Then commit `.gitattributes`:

```bash
git add .gitattributes
git commit -m "Track binary files with Git LFS"
```

This does **not** automatically rewrite old commits.

The historical versions still remain in normal Git history.

To migrate existing history, use:

```bash
git lfs migrate
```

---

# 21.23 Migrating History

Import existing files into LFS:

```bash
git lfs migrate import --include="*.bin"
```

For a specific file:

```bash
git lfs migrate import --include="model.bin"
```

For multiple patterns:

```bash
git lfs migrate import --include="*.bin,*.zip,*.psd"
```

Migrate all branches:

```bash
git lfs migrate import --everything --include="*.bin"
```

This rewrites Git history.

After history rewriting, commits receive new IDs.

You should understand the consequences before using these commands on shared repositories.

---

# 21.24 Converting Existing LFS Files

Git LFS migration can also be used to convert existing Git objects into LFS-managed objects.

For example:

```bash
git lfs migrate import --include="assets/**"
```

Inspect migration behavior before changing a shared repository.

A safer approach is to test in a clone:

```bash
git clone <repository>
cd <repository>
git lfs migrate import --include="assets/**"
```

Then inspect:

```bash
git log --oneline
git lfs ls-files
```

---

# 21.25 Untracking Files

Stop tracking a pattern with Git LFS:

```bash
git lfs untrack "*.bin"
```

This updates `.gitattributes`.

Then:

```bash
git add .gitattributes
git commit -m "Stop tracking binary files with Git LFS"
```

Important:

`git lfs untrack` does not automatically rewrite existing history.

Existing commits remain unchanged.

---

# 21.26 Removing LFS Tracking Rules

Inspect:

```bash
git lfs track
```

Untrack:

```bash
git lfs untrack "*.zip"
```

Check:

```bash
cat .gitattributes
```

Commit:

```bash
git add .gitattributes
git commit -m "Remove ZIP LFS tracking"
```

If you need to convert historical LFS content back to normal Git objects, that is a separate history-rewriting operation and should not be confused with `git lfs untrack`.

---

# 21.27 Fetching Specific LFS Objects

Fetch selected paths:

```bash
git lfs fetch --include="models/**"
```

Exclude paths:

```bash
git lfs fetch --exclude="videos/**"
```

Fetch a specific remote and ref:

```bash
git lfs fetch origin main
```

Combine filters:

```bash
git lfs fetch origin main --include="models/**" --exclude="models/archive/**"
```

This is especially useful for repositories containing very large collections of binary assets.

---

# 21.28 Working With LFS and Branches

Suppose:

```text
main
feature/model
```

Track:

```bash
git lfs track "*.bin"
```

Commit:

```bash
git add .gitattributes model.bin
git commit -m "Add model"
```

Create branch:

```bash
git switch -c feature/model
```

Add another LFS object:

```bash
git add model-v2.bin
git commit -m "Add model v2"
```

Push:

```bash
git push -u origin feature/model
```

The Git branch contains LFS pointer files while LFS storage contains the corresponding binary objects.

---

# 21.29 Working With LFS and Remotes

Inspect remotes:

```bash
git remote -v
```

Inspect LFS configuration:

```bash
git lfs env
```

Fetch:

```bash
git fetch origin
git lfs fetch origin
```

Pull:

```bash
git pull
git lfs pull
```

Push:

```bash
git push origin main
```

If Git LFS authentication fails, inspect:

```bash
git lfs env
git lfs logs last
```

Do not expose authentication tokens, passwords, or private credentials when sharing diagnostic output.

---

# 21.30 LFS and CI/CD

CI systems frequently need LFS content.

A typical workflow is:

```bash
git clone <repository>
cd <repository>
git lfs install
git lfs pull
```

Many CI systems support LFS directly during checkout.

Verify:

```bash
git lfs ls-files
```

Then run:

```bash
./build.sh
```

For a build that needs specific LFS assets:

```bash
git lfs fetch --include="models/**"
git lfs checkout
```

This can reduce unnecessary downloads.

---

# 21.31 LFS and Shallow Clones

A shallow clone:

```bash
git clone --depth 1 <repository>
```

can be combined with LFS.

After cloning:

```bash
git lfs pull
```

If you need additional Git history:

```bash
git fetch --deepen=100
```

or:

```bash
git fetch --unshallow
```

Then retrieve required LFS objects:

```bash
git lfs fetch
```

Remember that Git history depth and LFS object availability are related but separate concerns.

---

# 21.32 LFS and Worktrees

Git LFS works with Git worktrees.

Suppose:

```text
project-main/
project-model/
```

where:

```text
project-main -> main
project-model -> feature/model
```

Create:

```bash
git worktree add -b feature/model ../project-model
```

Then:

```bash
cd ../project-model
git lfs pull
```

LFS objects are shared through repository-level LFS storage, while each worktree has its own working directory.

Check:

```bash
git worktree list
git lfs ls-files
```

This combination is useful when working on multiple branches containing large binary assets.

---

# 21.33 LFS Troubleshooting

## LFS Command Not Found

Check:

```bash
git lfs version
```

Install Git LFS using your distribution's package manager.

Then:

```bash
git lfs install
```

---

## File Is a Pointer Instead of Real Content

Check:

```bash
git lfs ls-files
```

Then:

```bash
git lfs pull
```

You can also use:

```bash
git lfs checkout
```

---

## LFS Upload Failed

Run:

```bash
git lfs env
```

Then:

```bash
git lfs logs last
```

Check the remote:

```bash
git remote -v
```

Retry:

```bash
git push
```

---

## LFS Object Missing

Fetch:

```bash
git lfs fetch
```

Then:

```bash
git lfs checkout
```

Or:

```bash
git lfs pull
```

---

## Check Which Files Are LFS Managed

```bash
git lfs ls-files
```

Check attributes:

```bash
git check-attr filter -- path/to/file
```

---

# 21.34 Dangerous and Expensive LFS Operations

Some Git LFS operations can be disruptive.

## History Rewrite

```bash
git lfs migrate import --everything --include="*.bin"
```

This rewrites history.

Consequences include:

* New commit IDs
* Divergent branches
* Required force pushes
* Existing clones becoming incompatible with rewritten history

Never run this casually on a shared repository.

---

## Force Push After Migration

After history rewriting, you may need:

```bash
git push --force-with-lease
```

This should be coordinated with everyone using the repository.

Prefer:

```bash
git push --force-with-lease
```

over:

```bash
git push --force
```

when a force push is genuinely necessary.

---

## Uploading Everything

```bash
git lfs push --all origin
```

This can upload a very large amount of data.

Use it deliberately.

---

# 21.35 High-Value Git LFS Commands

| Command                               | Description                             | Example                                                 | Branch State Before and After command | Output                   |
| ------------------------------------- | --------------------------------------- | ------------------------------------------------------- | ------------------------------------- | ------------------------ |
| `git lfs version`                     | Show LFS version                        | `git lfs version`                                       | Unchanged                             | LFS version              |
| `git lfs install`                     | Initialize LFS                          | `git lfs install`                                       | Unchanged                             | Initialization message   |
| `git lfs env`                         | Show LFS environment                    | `git lfs env`                                           | Unchanged                             | LFS configuration        |
| `git lfs track PATTERN`               | Track files with LFS                    | `git lfs track "*.zip"`                                 | Branch unchanged                      | `.gitattributes` changed |
| `git lfs untrack PATTERN`             | Stop tracking pattern                   | `git lfs untrack "*.zip"`                               | Branch unchanged                      | `.gitattributes` changed |
| `git lfs track`                       | List tracking rules                     | `git lfs track`                                         | Unchanged                             | Configured patterns      |
| `git lfs ls-files`                    | List LFS files                          | `git lfs ls-files`                                      | Unchanged                             | LFS-managed files        |
| `git lfs ls-files --long`             | Show detailed LFS objects               | `git lfs ls-files --long`                               | Unchanged                             | Object IDs and files     |
| `git lfs ls-files --name-only`        | Show LFS file names                     | `git lfs ls-files --name-only`                          | Unchanged                             | File names               |
| `git lfs fetch`                       | Download LFS objects                    | `git lfs fetch`                                         | Branch unchanged                      | Download information     |
| `git lfs fetch origin`                | Fetch from remote                       | `git lfs fetch origin`                                  | Branch unchanged                      | Download information     |
| `git lfs fetch --all origin`          | Fetch all reachable LFS objects         | `git lfs fetch --all origin`                            | Branch unchanged                      | LFS objects downloaded   |
| `git lfs fetch --include=PATTERN`     | Fetch selected objects                  | `git lfs fetch --include="models/**"`                   | Branch unchanged                      | Matching objects         |
| `git lfs fetch --exclude=PATTERN`     | Exclude objects                         | `git lfs fetch --exclude="videos/**"`                   | Branch unchanged                      | Filtered download        |
| `git lfs pull`                        | Fetch and checkout LFS content          | `git lfs pull`                                          | Branch unchanged                      | LFS download/update      |
| `git lfs checkout`                    | Replace pointers with local LFS content | `git lfs checkout`                                      | Branch unchanged                      | Files restored           |
| `git lfs push REMOTE REF`             | Upload LFS objects                      | `git lfs push origin main`                              | Branch unchanged                      | Uploaded objects         |
| `git lfs push --all REMOTE`           | Upload all reachable LFS objects        | `git lfs push --all origin`                             | Branch unchanged                      | Uploaded objects         |
| `git lfs logs last`                   | Show latest LFS log                     | `git lfs logs last`                                     | Unchanged                             | Diagnostic log           |
| `git lfs migrate import`              | Convert history to LFS                  | `git lfs migrate import --include="*.bin"`              | History rewritten                     | Migration output         |
| `git lfs migrate import --everything` | Rewrite all refs for migration          | `git lfs migrate import --everything --include="*.bin"` | History rewritten                     | Migration output         |

---

# Recommended Git LFS Workflow

## 1. Install

```bash
git lfs version
```

If necessary, install Git LFS.

## 2. Initialize

```bash
git lfs install
```

## 3. Configure tracking

```bash
git lfs track "*.bin"
git lfs track "*.zip"
git lfs track "*.psd"
```

## 4. Commit `.gitattributes`

```bash
git add .gitattributes
git commit -m "Configure Git LFS"
```

## 5. Add large files

```bash
git add model.bin
git commit -m "Add model"
```

## 6. Push

```bash
git push -u origin main
```

## 7. Verify

```bash
git lfs ls-files
git lfs env
```

---

# Recommended LFS Debugging Workflow

When an LFS operation fails:

```bash
git lfs version
git lfs env
git remote -v
git lfs logs last
```

Then test:

```bash
git lfs fetch
```

or:

```bash
git lfs pull
```

For upload problems:

```bash
git lfs push origin <branch>
```

---

# Git LFS vs Normal Git

| Feature              | Normal Git             | Git LFS                           |
| -------------------- | ---------------------- | --------------------------------- |
| Source code          | Excellent              | Usually unnecessary               |
| Small text files     | Excellent              | Usually unnecessary               |
| Large binary files   | Can become expensive   | Designed for this                 |
| Videos               | Poor fit               | Good fit                          |
| Large datasets       | Poor fit               | Good fit                          |
| Photoshop files      | Can become large       | Good fit                          |
| CAD assets           | Can become large       | Good fit                          |
| History size         | Can grow significantly | Binary content handled separately |
| Pointer files        | No                     | Yes                               |
| Separate LFS storage | No                     | Yes                               |

---

# Practical Rules

### Use Git for:

```text
*.c
*.cpp
*.h
*.py
*.js
*.ts
*.java
*.go
*.rs
*.md
*.json
*.yaml
*.yml
```

### Consider Git LFS for:

```text
*.psd
*.mp4
*.mov
*.wav
*.zip
*.7z
*.bin
*.iso
*.model
*.onnx
*.pt
*.stl
```

The appropriate choice depends on repository size, file-change frequency, hosting limits, and team workflow.

---

# Essential Commands to Memorize

```bash
git lfs install

git lfs track "*.bin"

git lfs untrack "*.bin"

git lfs track

git lfs ls-files

git lfs fetch

git lfs pull

git lfs push origin main

git lfs env

git lfs logs last
```

For history migration:

```bash
git lfs migrate import --include="*.bin"
```

Use history migration only when you intentionally want to rewrite existing Git history.

---

## Next Part

**Next file:** `22-git-hooks.md`

[Next: Git Hooks](22-git-hooks.md)
