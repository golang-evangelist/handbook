# 4. Staging & Committing

This chapter covers commands used to inspect, stage, unstage, commit, amend, sign, and prepare changes for Git history.

---

## Table of Contents

* [4.1 Git Staging Model](#41-git-staging-model)
* [4.2 `git add`](#42-git-add)
* [4.3 Adding Specific Files](#43-adding-specific-files)
* [4.4 Adding Directories](#44-adding-directories)
* [4.5 Adding All Changes](#45-adding-all-changes)
* [4.6 Interactive Staging](#46-interactive-staging)
* [4.7 Patch Staging](#47-patch-staging)
* [4.8 Inspecting the Staging Area](#48-inspecting-the-staging-area)
* [4.9 Unstaging Changes](#49-unstaging-changes)
* [4.10 Restoring Staged and Unstaged Files](#410-restoring-staged-and-unstaged-files)
* [4.11 `git commit`](#411-git-commit)
* [4.12 Commit Messages](#412-commit-messages)
* [4.13 Commit Selected Changes](#413-commit-selected-changes)
* [4.14 Amend Commits](#414-amend-commits)
* [4.15 Empty Commits](#415-empty-commits)
* [4.16 Commit Metadata](#416-commit-metadata)
* [4.17 Commit Signing](#417-commit-signing)
* [4.18 Commit Verification](#418-commit-verification)
* [4.19 Commit Templates](#419-commit-templates)
* [4.20 Cleanup Before Commit](#420-cleanup-before-commit)
* [4.21 Developer Workflows](#421-developer-workflows)
* [4.22 DevOps and CI Workflows](#422-devops-and-ci-workflows)
* [4.23 Staging & Committing Command Summary](#423-staging--committing-command-summary)
* [4.24 High-Value Examples](#424-high-value-examples)

---

# 4.1 Git Staging Model

Git separates local file changes into several states:

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
```

For example:

```text
README.md
   │
   ├── modified in Working Tree
   │
   ├── git add README.md
   │
   ├── staged in Index
   │
   └── git commit
          │
          ▼
       Commit
```

This distinction is fundamental.

A file can simultaneously have:

```text
staged changes
+
unstaged changes
```

For example:

```text
MM src/app.js
```

means:

```text
M = staged modification
M = unstaged modification
```

---

# 4.2 `git add`

## Stage a file

```bash
git add file.txt
```

| Command             | Description                        | Example             | Branch State Before and After command | Output                    |
| ------------------- | ---------------------------------- | ------------------- | ------------------------------------- | ------------------------- |
| `git add <file>`    | Stages a file                      | `git add app.js`    | `main` → `main`                       | Usually no output         |
| `git add -- <file>` | Explicitly treats argument as path | `git add -- app.js` | `main` → `main`                       | Usually no output         |
| `git add -n <file>` | Dry-run addition                   | `git add -n app.js` | `main` → `main`                       | Files that would be added |
| `git add -v <file>` | Verbose staging                    | `git add -v app.js` | `main` → `main`                       | Staging information       |

`git add` does **not** create a commit.

It changes the staging area only.

---

# 4.3 Adding Specific Files

Multiple files can be staged:

```bash
git add app.js package.json README.md
```

Example:

```bash
git status -sb
```

Before:

```text
## main
 M app.js
 M package.json
 M README.md
```

After:

```bash
git add app.js package.json
```

the status may become:

```text
## main
M  app.js
M  package.json
 M README.md
```

Only the first two files are staged.

---

## Stage files with spaces

Use quotes:

```bash
git add "my file.txt"
```

or:

```bash
git add -- "my file.txt"
```

---

## Stage files matching a pattern

Shell expansion can be used:

```bash
git add src/*.js
```

For Git pathspec behavior, more advanced forms can be used:

```bash
git add ':(glob)src/**/*.js'
```

---

# 4.4 Adding Directories

## Stage a directory

```bash
git add src/
```

This stages changes under the specified directory.

| Command                | Description                       | Example         | Branch State Before and After command | Output            |
| ---------------------- | --------------------------------- | --------------- | ------------------------------------- | ----------------- |
| `git add <directory>/` | Stages changes in directory       | `git add src/`  | `main` → `main`                       | Usually no output |
| `git add .`            | Stages changes under current path | `git add .`     | `main` → `main`                       | Usually no output |
| `git add -A`           | Stages all changes in repository  | `git add -A`    | `main` → `main`                       | Usually no output |
| `git add --all`        | Long form of `-A`                 | `git add --all` | `main` → `main`                       | Usually no output |

---

# 4.5 Adding All Changes

## `git add .`

```bash
git add .
```

This stages changes visible from the current directory according to Git's path rules.

## `git add -A`

```bash
git add -A
```

This stages all changes in the repository.

That includes:

```text
modified files
deleted files
untracked files
```

when they are within the repository.

## `git add --all`

```bash
git add --all
```

Equivalent to:

```bash
git add -A
```

### Important distinction

For modern Git versions, the difference between:

```bash
git add .
```

and:

```bash
git add -A
```

is primarily the scope from which the command is executed.

When you want to explicitly stage the entire repository:

```bash
git add -A
```

is the clearest choice.

---

# 4.6 Interactive Staging

```bash
git add -i
```

Interactive mode allows you to choose which changes should enter the staging area.

Example:

```text
           staged     unstaged path
1:        +3/-1        +2/-0 src/app.js
2:        +5/-0        +0/-3 src/api.js

*** Commands ***
  1: status
  2: update
  3: revert
  4: add untracked
  5: patch
  6: diff
  7: quit
```

| Command                 | Description                   | Example                 | Branch State Before and After command | Output           |
| ----------------------- | ----------------------------- | ----------------------- | ------------------------------------- | ---------------- |
| `git add -i`            | Interactive staging interface | `git add -i`            | `main` → `main`                       | Interactive menu |
| `git add --interactive` | Long form                     | `git add --interactive` | `main` → `main`                       | Interactive menu |

Interactive staging is especially useful when one working-tree change contains multiple logical commits.

---

# 4.7 Patch Staging

## `git add -p`

```bash
git add -p
```

or:

```bash
git add --patch
```

Git displays individual hunks and asks whether to stage them.

Example:

```text
@@ -10,6 +10,8 @@
 const app = express();

+app.use(auth);
+
 app.listen(PORT);

Stage this hunk [y,n,q,a,d,s,e,?]?
```

Common responses:

| Key | Meaning                                      |
| --- | -------------------------------------------- |
| `y` | Stage this hunk                              |
| `n` | Do not stage this hunk                       |
| `q` | Quit                                         |
| `a` | Stage this and all remaining hunks in file   |
| `d` | Do not stage this or remaining hunks in file |
| `s` | Split hunk                                   |
| `e` | Manually edit hunk                           |
| `?` | Help                                         |

This is one of the most important commands for creating clean, focused commits.

---

## Patch staging for one file

```bash
git add -p src/app.js
```

Only selected hunks of the file become staged.

---

# 4.8 Inspecting the Staging Area

## View staged changes

```bash
git diff --cached
```

Equivalent:

```bash
git diff --staged
```

| Command                           | Description                     | Example                           | Branch State Before and After command | Output             |
| --------------------------------- | ------------------------------- | --------------------------------- | ------------------------------------- | ------------------ |
| `git diff --cached`               | Shows staged changes            | `git diff --cached`               | `main` → `main`                       | Staged patch       |
| `git diff --staged`               | Equivalent staged diff          | `git diff --staged`               | `main` → `main`                       | Staged patch       |
| `git diff --cached --stat`        | Shows staged statistics         | `git diff --cached --stat`        | `main` → `main`                       | File statistics    |
| `git diff --cached --name-only`   | Shows staged filenames          | `git diff --cached --name-only`   | `main` → `main`                       | Filenames          |
| `git diff --cached --name-status` | Shows staged names and statuses | `git diff --cached --name-status` | `main` → `main`                       | Status + filenames |

---

## View unstaged changes

```bash
git diff
```

This compares:

```text
Working Tree
     ↓
Index
```

It does not show changes that are already staged.

---

## View both staged and unstaged changes

```bash
git diff HEAD
```

This compares:

```text
Working Tree
     ↓
HEAD
```

Therefore it includes both:

```text
staged changes
+
unstaged changes
```

---

## Show staged statistics

```bash
git diff --cached --stat
```

Example:

```text
 src/app.js       | 12 ++++++++----
 src/auth.js      |  8 ++++++++
 2 files changed, 15 insertions(+), 5 deletions(-)
```

---

# 4.9 Unstaging Changes

## Unstage a file

Modern Git:

```bash
git restore --staged app.js
```

Older/common alternative:

```bash
git reset HEAD -- app.js
```

`git restore --staged` is generally clearer when the intention is simply to unstage.

| Command                       | Description                    | Example                       | Branch State Before and After command | Output            |
| ----------------------------- | ------------------------------ | ----------------------------- | ------------------------------------- | ----------------- |
| `git restore --staged <file>` | Removes file from staging area | `git restore --staged app.js` | `main` → `main`                       | Usually no output |
| `git reset HEAD -- <file>`    | Older/common way to unstage    | `git reset HEAD -- app.js`    | `main` → `main`                       | Usually no output |
| `git restore --staged :/`     | Unstages all staged paths      | `git restore --staged :/`     | `main` → `main`                       | Usually no output |

Unstaging does not delete the working-tree changes.

For example:

```text
Before:

M  app.js

After:

 M app.js
```

The modification remains in the working tree.

---

# 4.10 Restoring Staged and Unstaged Files

## Discard unstaged changes

```bash
git restore app.js
```

This restores the working-tree version from the index.

The unstaged modifications are discarded.

### Warning

This operation can destroy local uncommitted changes.

---

## Restore file from HEAD

```bash
git restore --source=HEAD -- app.js
```

This restores the file from the current commit.

---

## Restore both index and working tree

```bash
git restore --source=HEAD --staged --worktree -- app.js
```

This resets the specified file's staged and working-tree contents to `HEAD`.

A shorter equivalent in common situations is:

```bash
git restore --source=HEAD --staged --worktree -- app.js
```

Use with care.

---

# 4.11 `git commit`

## Basic commit

```bash
git commit
```

Git opens the configured editor for the commit message.

Example:

```text
# Please enter the commit message for your changes.
```

After saving the message:

```text
[main abc1234] Add authentication
 2 files changed, 18 insertions(+), 3 deletions(-)
```

| Command                   | Description                            | Example                               | Branch State Before and After command | Output         |
| ------------------------- | -------------------------------------- | ------------------------------------- | ------------------------------------- | -------------- |
| `git commit`              | Creates a commit from staged changes   | `git commit`                          | `main@old` → `main@new`               | New commit     |
| `git commit -m "message"` | Creates commit with inline message     | `git commit -m "Add authentication"`  | `main@old` → `main@new`               | Commit summary |
| `git commit -v`           | Includes diff in commit-message editor | `git commit -v`                       | `main@old` → `main@new`               | Editor + diff  |
| `git commit -n`           | Skips pre-commit and commit-msg hooks  | `git commit -n -m "message"`          | `main@old` → `main@new`               | Commit         |
| `git commit --no-verify`  | Long form of `-n`                      | `git commit --no-verify -m "message"` | `main@old` → `main@new`               | Commit         |

The branch moves forward only if the commit succeeds.

---

# 4.12 Commit Messages

## Inline commit message

```bash
git commit -m "Add authentication"
```

For multi-line messages:

```bash
git commit -m "Add authentication" \
           -m "Validate bearer tokens before processing requests."
```

The first `-m` becomes the subject.

The second `-m` becomes the body.

---

## Use an editor

```bash
git commit
```

This is useful for longer commit messages.

A typical structure:

```text
Add authentication middleware

Validate bearer tokens before processing requests.

This prevents unauthenticated access to protected endpoints.
```

---

## Multiple `-m` arguments

```bash
git commit \
  -m "Add authentication middleware" \
  -m "Validate bearer tokens before processing protected requests."
```

---

# 4.13 Commit Selected Changes

## Commit only staged files

```bash
git commit
```

Only staged changes are included.

Example:

```text
Working Tree:
    app.js       modified
    README.md    modified

Staging Area:
    app.js       staged

git commit

Result:
    app.js       committed
    README.md    remains modified
```

---

## Stage and commit tracked files

```bash
git commit -am "Fix authentication"
```

This stages modifications and deletions to **already tracked files**, then creates the commit.

It does **not** automatically stage new untracked files.

Example:

```text
app.js        modified
README.md     modified
notes.txt     untracked
```

After:

```bash
git commit -am "Update application"
```

`app.js` and `README.md` can be committed, but `notes.txt` remains untracked.

---

# 4.14 Amend Commits

## Amend latest commit

```bash
git commit --amend
```

This replaces the latest commit with a new commit containing the current staged changes.

Typical workflow:

```bash
git add forgotten-file.js
git commit --amend
```

---

## Amend message only

```bash
git commit --amend -m "Correct commit message"
```

---

## Amend without changing message

```bash
git commit --amend --no-edit
```

Example:

```bash
git add missing-file.js
git commit --amend --no-edit
```

This adds the staged file to the previous commit while keeping its message.

| Command                        | Description                       | Example                             | Branch State Before and After command | Output        |
| ------------------------------ | --------------------------------- | ----------------------------------- | ------------------------------------- | ------------- |
| `git commit --amend`           | Replaces latest commit            | `git commit --amend`                | `main@A` → `main@B`                   | New commit ID |
| `git commit --amend --no-edit` | Amends without changing message   | `git commit --amend --no-edit`      | `main@A` → `main@B`                   | New commit ID |
| `git commit --amend -m "..."`  | Amends commit and changes message | `git commit --amend -m "Fix login"` | `main@A` → `main@B`                   | New commit ID |

### Important

Amending creates a new commit object.

If the original commit has already been pushed and shared, amending changes history and may require a force push.

---

# 4.15 Empty Commits

## Create an empty commit

```bash
git commit --allow-empty -m "Trigger CI"
```

This creates a commit even when there are no staged file changes.

Example output:

```text
[main abc1234] Trigger CI
```

This is sometimes useful for:

```text
CI/CD triggers
deployment markers
automation tests
repository events
```

| Command                                          | Description                               | Example                                                | Branch State Before and After command | Output     |
| ------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------ | ------------------------------------- | ---------- |
| `git commit --allow-empty -m "..."`              | Creates commit without file changes       | `git commit --allow-empty -m "Trigger CI"`             | `main@A` → `main@B`                   | New commit |
| `git commit --allow-empty --allow-empty-message` | Allows an empty commit with empty message | `git commit --allow-empty --allow-empty-message -m ""` | `main@A` → `main@B`                   | New commit |

---

# 4.16 Commit Metadata

Git commits contain metadata including:

```text
author
committer
author timestamp
committer timestamp
commit message
parent commit(s)
tree
```

## Show complete commit metadata

```bash
git show --format=fuller --no-patch HEAD
```

Example:

```text
commit abc1234
Author:     Developer <dev@example.com>
AuthorDate: Wed Aug 12 10:00:00 2026 +0200
Commit:     Developer <dev@example.com>
CommitDate: Wed Aug 12 10:01:00 2026 +0200

    Add authentication
```

---

## Configure author identity

```bash
git config user.name "Developer"
git config user.email "developer@example.com"
```

These commands are configuration commands and are covered in detail in Chapter 1.

---

## Override author for one commit

```bash
git commit --author="Developer <developer@example.com>" -m "Add authentication"
```

This changes the author information for that commit.

---

## Override committer identity

Environment variables can be used:

```bash
GIT_COMMITTER_NAME="CI Bot" \
GIT_COMMITTER_EMAIL="ci@example.com" \
git commit -m "Automated update"
```

---

# 4.17 Commit Signing

Git can sign commits using supported signing mechanisms such as:

```text
GPG
SSH
```

## Sign a commit

```bash
git commit -S -m "Add authentication"
```

Equivalent long form:

```bash
git commit --gpg-sign -m "Add authentication"
```

| Command                 | Description             | Example                                  | Branch State Before and After command | Output        |
| ----------------------- | ----------------------- | ---------------------------------------- | ------------------------------------- | ------------- |
| `git commit -S`         | Creates a signed commit | `git commit -S -m "Add feature"`         | `main@old` → `main@new`               | Signed commit |
| `git commit --gpg-sign` | Long signing option     | `git commit --gpg-sign -m "Add feature"` | `main@old` → `main@new`               | Signed commit |

---

## Sign with a specific key

```bash
git commit -S<key-id> -m "Add feature"
```

For example:

```bash
git commit -SABC12345 -m "Add feature"
```

---

# 4.18 Commit Verification

## Verify a commit signature

```bash
git verify-commit HEAD
```

Example:

```text
gpg: Signature made ...
gpg: Good signature from "Developer <developer@example.com>"
```

## Verify a tag signature

```bash
git verify-tag v1.0.0
```

| Command                  | Description               | Example                  | Branch State Before and After command | Output                     |
| ------------------------ | ------------------------- | ------------------------ | ------------------------------------- | -------------------------- |
| `git verify-commit HEAD` | Verifies commit signature | `git verify-commit HEAD` | `main` → `main`                       | Signature verification     |
| `git verify-tag v1.0.0`  | Verifies signed tag       | `git verify-tag v1.0.0`  | `main` → `main`                       | Tag signature verification |

---

# 4.19 Commit Templates

Git can use a commit-message template.

Configure:

```bash
git config commit.template ~/.gitmessage
```

Then:

```bash
git commit
```

opens the template in the editor.

Example template:

```text
# What changed?

# Why was it changed?

# Testing performed:
```

The template helps enforce consistent commit messages.

---

# 4.20 Cleanup Before Commit

## Check unstaged changes

```bash
git diff
```

## Check staged changes

```bash
git diff --cached
```

## Check all changes relative to HEAD

```bash
git diff HEAD
```

## Check repository status

```bash
git status -sb
```

A reliable pre-commit inspection sequence is:

```bash
git status -sb
git diff
git diff --cached
```

---

# 4.21 Developer Workflows

## Workflow A — Normal feature commit

```bash
git status
git add src/app.js
git diff --cached
git commit -m "Add authentication"
```

Branch transition:

```text
main@A
   │
   ├── working-tree changes
   │
   ├── git add
   │
   ├── git commit
   │
   ▼
main@B
```

---

## Workflow B — Stage multiple files

```bash
git add src/app.js src/auth.js
git diff --cached
git commit -m "Add authentication middleware"
```

---

## Workflow C — Stage everything

```bash
git add -A
git diff --cached
git commit -m "Update application"
```

---

## Workflow D — Select only certain hunks

```bash
git add -p
git diff --cached
git commit -m "Fix authentication validation"
```

This is preferred when a file contains unrelated changes.

---

## Workflow E — Correct the previous commit

```bash
git add forgotten-file.js
git commit --amend --no-edit
```

---

## Workflow F — Correct the previous commit message

```bash
git commit --amend -m "Correct commit message"
```

---

## Workflow G — Commit tracked changes quickly

```bash
git commit -am "Fix API validation"
```

Remember:

```text
tracked modified files → included
tracked deleted files  → included
untracked files        → NOT included
```

---

## Workflow H — Create a CI trigger commit

```bash
git commit --allow-empty -m "Trigger CI"
```

---

# 4.22 DevOps and CI Workflows

## Validate clean working tree

```bash
if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is dirty"
    exit 1
fi
```

---

## Get commit SHA for a build

```bash
COMMIT_SHA="$(git rev-parse HEAD)"
echo "$COMMIT_SHA"
```

Short version:

```bash
SHORT_SHA="$(git rev-parse --short HEAD)"
echo "$SHORT_SHA"
```

---

## Generate build version

```bash
VERSION="$(git describe --tags --always --dirty)"
echo "$VERSION"
```

Possible output:

```text
v2.1.0-4-gabc1234
```

If there are uncommitted changes, the output can include:

```text
-dirty
```

---

## Fail CI if repository contains uncommitted changes

```bash
git diff --exit-code
```

This checks unstaged changes.

To include staged changes as well:

```bash
git diff HEAD --exit-code
```

Example:

```bash
if ! git diff HEAD --exit-code; then
    echo "Repository contains uncommitted changes"
    exit 1
fi
```

---

# 4.23 Staging & Committing Command Summary

| Command                               | Description                      | Example                                                | Branch State Before and After command | Output           |
| ------------------------------------- | -------------------------------- | ------------------------------------------------------ | ------------------------------------- | ---------------- |
| `git add <file>`                      | Stage file                       | `git add app.js`                                       | `main` → `main`                       | Usually none     |
| `git add <directory>`                 | Stage directory                  | `git add src/`                                         | `main` → `main`                       | Usually none     |
| `git add .`                           | Stage changes from current path  | `git add .`                                            | `main` → `main`                       | Usually none     |
| `git add -A`                          | Stage all repository changes     | `git add -A`                                           | `main` → `main`                       | Usually none     |
| `git add -n`                          | Preview staging                  | `git add -n .`                                         | `main` → `main`                       | Preview          |
| `git add -v`                          | Verbose staging                  | `git add -v app.js`                                    | `main` → `main`                       | Staging details  |
| `git add -i`                          | Interactive staging              | `git add -i`                                           | `main` → `main`                       | Interactive menu |
| `git add -p`                          | Interactive hunk staging         | `git add -p`                                           | `main` → `main`                       | Hunk prompts     |
| `git diff`                            | Show unstaged changes            | `git diff`                                             | `main` → `main`                       | Unstaged diff    |
| `git diff --cached`                   | Show staged changes              | `git diff --cached`                                    | `main` → `main`                       | Staged diff      |
| `git diff HEAD`                       | Show all uncommitted changes     | `git diff HEAD`                                        | `main` → `main`                       | Full local diff  |
| `git diff --cached --stat`            | Show staged statistics           | `git diff --cached --stat`                             | `main` → `main`                       | Statistics       |
| `git restore --staged <file>`         | Unstage file                     | `git restore --staged app.js`                          | `main` → `main`                       | Usually none     |
| `git restore <file>`                  | Discard unstaged changes         | `git restore app.js`                                   | `main` → `main`                       | Usually none     |
| `git restore --source=HEAD -- <file>` | Restore file from HEAD           | `git restore --source=HEAD -- app.js`                  | `main` → `main`                       | Usually none     |
| `git commit`                          | Create commit                    | `git commit`                                           | `main@A` → `main@B`                   | Commit           |
| `git commit -m "..."`                 | Create commit with message       | `git commit -m "Add feature"`                          | `main@A` → `main@B`                   | Commit summary   |
| `git commit -am "..."`                | Stage tracked changes and commit | `git commit -am "Fix API"`                             | `main@A` → `main@B`                   | Commit summary   |
| `git commit -v`                       | Show diff in editor              | `git commit -v`                                        | `main@A` → `main@B`                   | Editor + diff    |
| `git commit --no-verify`              | Skip commit hooks                | `git commit --no-verify -m "Fix"`                      | `main@A` → `main@B`                   | Commit           |
| `git commit --amend`                  | Replace latest commit            | `git commit --amend`                                   | `main@A` → `main@B`                   | New commit       |
| `git commit --amend --no-edit`        | Amend without changing message   | `git commit --amend --no-edit`                         | `main@A` → `main@B`                   | New commit       |
| `git commit --allow-empty`            | Create empty commit              | `git commit --allow-empty -m "Trigger CI"`             | `main@A` → `main@B`                   | New commit       |
| `git commit --author="..."`           | Override author                  | `git commit --author="Dev <dev@example.com>" -m "Fix"` | `main@A` → `main@B`                   | Commit           |
| `git commit -S`                       | Create signed commit             | `git commit -S -m "Release fix"`                       | `main@A` → `main@B`                   | Signed commit    |
| `git verify-commit HEAD`              | Verify commit signature          | `git verify-commit HEAD`                               | `main` → `main`                       | Signature result |
| `git verify-tag <tag>`                | Verify tag signature             | `git verify-tag v1.0.0`                                | `main` → `main`                       | Signature result |

---

# 4.24 High-Value Examples

## Example 1 — Standard commit

```bash
git status -sb
git add src/app.js
git diff --cached
git commit -m "Add authentication"
```

State:

```text
Before:

main@A
Working Tree: modified app.js
Index: clean

After git add:

main@A
Working Tree: clean relative to index
Index: app.js staged

After git commit:

main@B
Index: clean
Working Tree: clean
```

---

## Example 2 — Carefully review everything before committing

```bash
git status -sb
git diff
git add -p
git diff --cached
git commit -m "Fix authentication"
```

This workflow is recommended for complex changes.

---

## Example 3 — Commit only one file

```bash
git add src/auth.js
git diff --cached -- src/auth.js
git commit -m "Add authentication service"
```

Other modified files remain outside the commit.

---

## Example 4 — Stage everything and commit

```bash
git add -A
git diff --cached
git commit -m "Update application"
```

---

## Example 5 — Fix previous commit

```bash
git add forgotten-file.js
git commit --amend --no-edit
```

---

## Example 6 — Create an empty CI trigger commit

```bash
git commit --allow-empty -m "Trigger CI"
```

---

## Example 7 — Verify exactly what will be committed

```bash
git diff --cached --name-status
git diff --cached
git status -sb
```

Then:

```bash
git commit -m "Implement feature"
```

---

# Final Practical Checklist

Before committing:

```bash
git status -sb
git diff
git diff --cached
```

Stage:

```bash
git add <files>
```

Or selectively:

```bash
git add -p
```

Review:

```bash
git diff --cached
```

Commit:

```bash
git commit -m "Clear description of change"
```

Verify:

```bash
git status -sb
git log -1 --oneline
```

A compact professional workflow is:

```bash
git status -sb &&
git add -p &&
git diff --cached &&
git commit -m "Describe the change" &&
git status -sb
```

---

## Next Part

**Next file:** `05-diff-and-code-review.md`

[Next: Diff & Code Review](05-diff-and-code-review.md)
