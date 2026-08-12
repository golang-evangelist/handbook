# 5. Diff & Code Review

This chapter covers Git commands for inspecting, comparing, reviewing, filtering, formatting, and validating changes before they become part of project history.

---

## Table of Contents

* [5.1 Git Diff Model](#51-git-diff-model)
* [5.2 `git diff`](#52-git-diff)
* [5.3 Working Tree vs Index](#53-working-tree-vs-index)
* [5.4 Index vs HEAD](#54-index-vs-head)
* [5.5 Working Tree vs HEAD](#55-working-tree-vs-head)
* [5.6 Compare Commits](#56-compare-commits)
* [5.7 Compare Branches](#57-compare-branches)
* [5.8 Diff Statistics](#58-diff-statistics)
* [5.9 File Names and Status](#59-file-names-and-status)
* [5.10 Word-Level Differences](#510-word-level-differences)
* [5.11 Ignore Whitespace](#511-ignore-whitespace)
* [5.12 Diff Algorithms](#512-diff-algorithms)
* [5.13 Rename and Copy Detection](#513-rename-and-copy-detection)
* [5.14 Search in Diffs](#514-search-in-diffs)
* [5.15 Reviewing a Commit](#515-reviewing-a-commit)
* [5.16 Reviewing a Range of Commits](#516-reviewing-a-range-of-commits)
* [5.17 Review Against Remote Branch](#517-review-against-remote-branch)
* [5.18 Patch Files](#518-patch-files)
* [5.19 Applying Patches](#519-applying-patches)
* [5.20 Diff Formatting](#520-diff-formatting)
* [5.21 Review Automation](#521-review-automation)
* [5.22 Developer Workflows](#522-developer-workflows)
* [5.23 DevOps and CI Workflows](#523-devops-and-ci-workflows)
* [5.24 High-Value Diff Commands](#524-high-value-diff-commands)

---

# 5.1 Git Diff Model

Git commonly compares three important states:

```text
                 git add
Working Tree ----------------> Index
     │                           │
     │                           │
     │ git diff                 │ git diff --cached
     │                           │
     ▼                           ▼
   changes                    staged changes

                  git commit
Index ------------------------> HEAD
```

The three primary comparisons are:

```text
git diff
```

Compares:

```text
Working Tree ↔ Index
```

---

```bash
git diff --cached
```

Compares:

```text
Index ↔ HEAD
```

---

```bash
git diff HEAD
```

Compares:

```text
Working Tree ↔ HEAD
```

Understanding these three commands is essential for Git code review.

---

# 5.2 `git diff`

## Show unstaged changes

```bash
git diff
```

This displays changes that exist in the working tree but have not been staged.

Example:

```text
diff --git a/app.js b/app.js
index 1234567..abcdef0 100644
--- a/app.js
+++ b/app.js
@@ -10,6 +10,7 @@
 const app = express();

+app.use(auth());
 app.listen(PORT);
```

| Command                  | Description                    | Example                  | Branch State Before and After command | Output            |
| ------------------------ | ------------------------------ | ------------------------ | ------------------------------------- | ----------------- |
| `git diff`               | Shows unstaged changes         | `git diff`               | `main` → `main`                       | Unified diff      |
| `git diff -- app.js`     | Shows changes for one file     | `git diff -- app.js`     | `main` → `main`                       | Diff for file     |
| `git diff -- src/`       | Shows changes under directory  | `git diff -- src/`       | `main` → `main`                       | Directory diff    |
| `git diff --no-ext-diff` | Disables external diff helpers | `git diff --no-ext-diff` | `main` → `main`                       | Standard Git diff |

The `--` separator is useful because it explicitly separates revisions/options from file paths.

---

# 5.3 Working Tree vs Index

The default:

```bash
git diff
```

compares:

```text
Working Tree
     ↓
Index
```

Example state:

```text
app.js
  │
  ├── staged modification
  │
  └── additional unstaged modification
```

Then:

```bash
git diff
```

shows only the unstaged portion.

To see the staged portion:

```bash
git diff --cached
```

---

## Diff one file

```bash
git diff -- app.js
```

## Diff multiple files

```bash
git diff -- app.js auth.js
```

## Diff a directory

```bash
git diff -- src/
```

---

# 5.4 Index vs HEAD

## Show staged changes

```bash
git diff --cached
```

Equivalent:

```bash
git diff --staged
```

This compares:

```text
HEAD
 ↓
Index
```

It answers:

> What exactly will be included in the next commit?

This should normally be reviewed immediately before committing.

---

## Staged statistics

```bash
git diff --cached --stat
```

Example:

```text
 src/app.js  | 12 +++++++++---
 src/auth.js |  8 ++++++++
 2 files changed, 17 insertions(+), 3 deletions(-)
```

---

## Staged filenames

```bash
git diff --cached --name-only
```

Example:

```text
src/app.js
src/auth.js
```

---

# 5.5 Working Tree vs HEAD

## Show all uncommitted changes

```bash
git diff HEAD
```

This includes both:

```text
staged changes
+
unstaged changes
```

Comparison:

```text
HEAD
 │
 ├── Index
 │     │
 │     └── staged changes
 │
 └── Working Tree
       │
       └── unstaged changes
```

---

## Include untracked files

`git diff HEAD` does not show untracked files.

Use:

```bash
git status --short
```

to identify them.

Git does not consider an untracked file part of a diff until it is staged.

---

# 5.6 Compare Commits

## Compare two commits

```bash
git diff <commit1> <commit2>
```

Example:

```bash
git diff abc1234 def5678
```

This shows changes required to transform the first commit's tree into the second commit's tree.

---

## Compare current HEAD with previous commit

```bash
git diff HEAD~1 HEAD
```

---

## Compare HEAD with two commits ago

```bash
git diff HEAD~2 HEAD
```

---

## Compare a commit with its parent

```bash
git diff HEAD^ HEAD
```

or:

```bash
git diff HEAD~1 HEAD
```

---

## Show only names

```bash
git diff --name-only HEAD~1 HEAD
```

---

## Show statistics

```bash
git diff --stat HEAD~1 HEAD
```

| Command                | Description                     | Example                  | Branch State Before and After command | Output |
| ---------------------- | ------------------------------- | ------------------------ | ------------------------------------- | ------ |
| `git diff A B`         | Compare two commits             | `git diff abc123 def456` | `main` → `main`                       | Patch  |
| `git diff HEAD~1 HEAD` | Compare latest commit to parent | `git diff HEAD~1 HEAD`   | `main` → `main`                       | Patch  |
| `git diff HEAD^ HEAD`  | Same parent comparison          | `git diff HEAD^ HEAD`    | `main` → `main`                       | Patch  |
| `git diff A..B`        | Two-endpoint diff               | `git diff A..B`          | `main` → `main`                       | Patch  |
| `git diff A...B`       | Diff from merge base to B       | `git diff A...B`         | `main` → `main`                       | Patch  |

### Important: `..` vs `...`

For `git diff`:

```bash
git diff A B
```

is normally the clearest two-endpoint comparison.

With three dots:

```bash
git diff A...B
```

Git compares:

```text
merge-base(A,B)
          ↓
          B
```

This is particularly useful when reviewing what branch `B` introduced since it diverged from `A`.

---

# 5.7 Compare Branches

## Compare two branches

```bash
git diff main feature/login
```

This compares the tips of the two branches.

---

## Review feature branch against main

```bash
git diff main...feature/login
```

This is often preferable for code review because it shows changes introduced on `feature/login` since its merge base with `main`.

---

## Review current branch against main

```bash
git diff main...HEAD
```

---

## Compare remote branch

```bash
git diff origin/main...HEAD
```

This is useful before opening a pull request.

---

## Branch comparison table

| Command                       | Description                             | Example                         | Branch State Before and After command | Output          |
| ----------------------------- | --------------------------------------- | ------------------------------- | ------------------------------------- | --------------- |
| `git diff main feature`       | Compare branch tips                     | `git diff main feature/login`   | `feature/login` → `feature/login`     | Diff            |
| `git diff main...feature`     | Compare feature against merge base      | `git diff main...feature/login` | `feature/login` → `feature/login`     | Feature changes |
| `git diff main...HEAD`        | Compare current branch against main     | `git diff main...HEAD`          | `feature` → `feature`                 | Feature changes |
| `git diff origin/main...HEAD` | Compare current branch with remote main | `git diff origin/main...HEAD`   | `feature` → `feature`                 | Feature changes |

No branch is changed by these commands.

---

# 5.8 Diff Statistics

## `--stat`

```bash
git diff --stat
```

Example:

```text
 src/app.js    | 15 +++++++++++----
 src/auth.js   | 20 ++++++++++++++++++++
 README.md     |  4 ++++
 3 files changed, 35 insertions(+), 4 deletions(-)
```

---

## `--shortstat`

```bash
git diff --shortstat
```

Example:

```text
3 files changed, 35 insertions(+), 4 deletions(-)
```

---

## `--numstat`

```bash
git diff --numstat
```

Example:

```text
12       3       src/app.js
20       0       src/auth.js
4        0       README.md
```

The columns are:

```text
added
deleted
file
```

This format is useful for scripts.

---

## `--dirstat`

```bash
git diff --dirstat
```

Example:

```text
 75.0% src/
 25.0% docs/
```

---

# 5.9 File Names and Status

## Show changed file names

```bash
git diff --name-only
```

---

## Show names and statuses

```bash
git diff --name-status
```

Example:

```text
M       src/app.js
A       src/auth.js
D       old.js
R100    old-name.js     new-name.js
```

Common status codes:

| Code | Meaning  |
| ---- | -------- |
| `A`  | Added    |
| `M`  | Modified |
| `D`  | Deleted  |
| `R`  | Renamed  |
| `C`  | Copied   |

---

## Show raw diff information

```bash
git diff --raw
```

This is useful for scripts and lower-level inspection.

---

# 5.10 Word-Level Differences

For prose, configuration files, JSON, or lines with small changes:

```bash
git diff --word-diff
```

Example:

```text
The application uses {+JWT+} authentication.
```

---

## Colorized word diff

```bash
git diff --word-diff=color
```

---

## Word diff with custom regular expression

```bash
git diff --word-diff-regex='[^[:space:]]+'
```

This defines what Git considers a "word".

Useful for:

```text
JSON
YAML
Markdown
configuration files
documentation
```

---

# 5.11 Ignore Whitespace

Whitespace-only changes can make reviews noisy.

## Ignore all whitespace changes

```bash
git diff -w
```

Equivalent:

```bash
git diff --ignore-all-space
```

---

## Ignore changes in amount of whitespace

```bash
git diff -b
```

Equivalent:

```bash
git diff --ignore-space-change
```

---

## Ignore whitespace at end of line

```bash
git diff --ignore-space-at-eol
```

---

## Ignore blank lines

```bash
git diff --ignore-blank-lines
```

| Command                          | Description                         | Example                          | Branch State Before and After command | Output             |
| -------------------------------- | ----------------------------------- | -------------------------------- | ------------------------------------- | ------------------ |
| `git diff -w`                    | Ignore whitespace differences       | `git diff -w`                    | `main` → `main`                       | Reduced-noise diff |
| `git diff -b`                    | Ignore changes in whitespace amount | `git diff -b`                    | `main` → `main`                       | Reduced-noise diff |
| `git diff --ignore-space-at-eol` | Ignore EOL whitespace               | `git diff --ignore-space-at-eol` | `main` → `main`                       | Reduced-noise diff |
| `git diff --ignore-blank-lines`  | Ignore blank-line changes           | `git diff --ignore-blank-lines`  | `main` → `main`                       | Reduced-noise diff |

These options affect the comparison output only; they do not modify files.

---

# 5.12 Diff Algorithms

Git supports different diff algorithms.

## Default algorithm

```bash
git diff
```

---

## Minimal diff

```bash
git diff --minimal
```

Attempts to produce a smaller edit set.

---

## Patience algorithm

```bash
git diff --patience
```

Can produce more readable diffs for certain source-code changes.

---

## Histogram algorithm

```bash
git diff --histogram
```

Often useful for source-code review.

---

## Myers algorithm

```bash
git diff --diff-algorithm=myers
```

The Myers algorithm is Git's traditional default.

---

## Example

```bash
git diff --histogram main...feature/login
```

This changes only how Git calculates the displayed diff.

---

# 5.13 Rename and Copy Detection

Git can detect renamed or copied files.

## Enable rename detection

```bash
git diff -M
```

Equivalent:

```bash
git diff --find-renames
```

---

## Configure rename similarity

```bash
git diff -M90%
```

This asks Git to detect renames with approximately 90% similarity.

---

## Detect copies

```bash
git diff -C
```

Equivalent:

```bash
git diff --find-copies
```

---

## More aggressive copy detection

```bash
git diff -C -C
```

or:

```bash
git diff --find-copies-harder
```

This can be more computationally expensive.

---

## Example output

```text
similarity index 95%
rename from src/old-auth.js
rename to src/auth.js
```

---

# 5.14 Search in Diffs

## Search diff output

```bash
git diff | grep "TODO"
```

This uses standard shell tools to filter the diff.

---

## Search case-insensitively

```bash
git diff | grep -i "password"
```

---

## Search changed lines only

A useful technique is:

```bash
git diff | grep '^+'
```

But note that diff metadata and `+++` file headers also begin with `+`.

A more precise pattern is:

```bash
git diff | grep '^+' | grep -v '^+++'
```

This shows added lines.

---

## Search deleted lines

```bash
git diff | grep '^-' | grep -v '^---'
```

---

## Search staged additions

```bash
git diff --cached | grep '^+' | grep -v '^+++'
```

These commands are useful in quick reviews and automation scripts.

---

# 5.15 Reviewing a Commit

## Show the latest commit

```bash
git show HEAD
```

This displays:

```text
commit metadata
+
commit message
+
patch
```

---

## Show commit without patch

```bash
git show --no-patch HEAD
```

---

## Show commit statistics

```bash
git show --stat HEAD
```

---

## Show changed filenames

```bash
git show --name-only HEAD
```

---

## Show names and statuses

```bash
git show --name-status HEAD
```

---

## Show only patch

```bash
git show --format= HEAD
```

---

## Review a specific commit

```bash
git show abc1234
```

| Command                       | Description                  | Example                       | Branch State Before and After command | Output          |
| ----------------------------- | ---------------------------- | ----------------------------- | ------------------------------------- | --------------- |
| `git show HEAD`               | Show latest commit and patch | `git show HEAD`               | `main` → `main`                       | Commit + diff   |
| `git show --stat HEAD`        | Show commit statistics       | `git show --stat HEAD`        | `main` → `main`                       | Statistics      |
| `git show --name-only HEAD`   | Show changed files           | `git show --name-only HEAD`   | `main` → `main`                       | Filenames       |
| `git show --name-status HEAD` | Show statuses and files      | `git show --name-status HEAD` | `main` → `main`                       | Status + files  |
| `git show --no-patch HEAD`    | Show metadata/message only   | `git show --no-patch HEAD`    | `main` → `main`                       | Commit metadata |

---

# 5.16 Reviewing a Range of Commits

## Show changes between commits

```bash
git diff HEAD~5 HEAD
```

---

## Review commits individually

```bash
git log -p HEAD~5..HEAD
```

This shows each commit and its patch.

---

## Compact review

```bash
git log --stat HEAD~5..HEAD
```

---

## Show filenames

```bash
git log --name-only HEAD~5..HEAD
```

---

## Show status

```bash
git log --name-status HEAD~5..HEAD
```

---

# 5.17 Review Against Remote Branch

A common pull-request workflow:

```bash
git fetch origin
git diff origin/main...HEAD
```

This answers:

> What changes does my current branch contain relative to the remote `main` branch since the branches diverged?

Statistics:

```bash
git diff --stat origin/main...HEAD
```

Changed files:

```bash
git diff --name-status origin/main...HEAD
```

Full review:

```bash
git diff --histogram origin/main...HEAD
```

---

# 5.18 Patch Files

Git can generate patch files from diffs.

## Save a diff to a file

```bash
git diff > changes.patch
```

The output file contains the patch.

---

## Save staged changes

```bash
git diff --cached > staged.patch
```

---

## Save a branch comparison

```bash
git diff main...feature/login > feature-login.patch
```

---

## Review patch

```bash
less changes.patch
```

or:

```bash
cat changes.patch
```

---

# 5.19 Applying Patches

## Apply a patch

```bash
git apply changes.patch
```

This modifies the working tree but does not create a commit.

---

## Check whether patch applies

```bash
git apply --check changes.patch
```

No output generally means the patch can be applied cleanly.

---

## Apply and stage

```bash
git apply --index changes.patch
```

This applies the patch and updates the index.

---

## Reverse a patch

```bash
git apply -R changes.patch
```

or:

```bash
git apply --reverse changes.patch
```

---

## Apply with whitespace checking

```bash
git apply --check --whitespace=error changes.patch
```

---

# 5.20 Diff Formatting

## Full patch

```bash
git diff
```

## Patch without color

```bash
git -c color.ui=false diff
```

## Force color

```bash
git diff --color=always
```

## Suppress color

```bash
git diff --color=never
```

---

## Context lines

Git normally displays several lines of context.

Change the amount with:

```bash
git diff -U10
```

or:

```bash
git diff --unified=10
```

Example:

```text
@@ -10,10 +10,12 @@
```

---

## Zero context

```bash
git diff -U0
```

This is useful for automation and compact output.

---

# 5.21 Review Automation

## Check whether changes exist

```bash
git diff --quiet
```

Exit status:

```text
0 → no differences
1 → differences exist
```

This is particularly useful in shell scripts.

Example:

```bash
if git diff --quiet; then
    echo "Working tree is clean"
else
    echo "Working tree has changes"
fi
```

---

## Check staged changes

```bash
git diff --cached --quiet
```

---

## Check all changes against HEAD

```bash
git diff HEAD --quiet
```

Example:

```bash
if git diff HEAD --quiet; then
    echo "No tracked changes"
else
    echo "Tracked changes detected"
fi
```

---

## CI check for accidental changes

```bash
git diff --exit-code
```

This returns a non-zero status when unstaged changes exist.

For staged and unstaged tracked changes:

```bash
git diff HEAD --exit-code
```

---

# 5.22 Developer Workflows

## Workflow A — Review before commit

```bash
git status -sb
git diff
git add -p
git diff --cached
git commit -m "Add authentication"
```

---

## Workflow B — Review a feature branch

```bash
git fetch origin
git diff origin/main...HEAD
```

Then:

```bash
git diff --stat origin/main...HEAD
git diff --name-status origin/main...HEAD
```

---

## Workflow C — Review latest commit

```bash
git show --stat HEAD
git show HEAD
```

---

## Workflow D — Review previous commit

```bash
git show HEAD~1
```

---

## Workflow E — Review several commits

```bash
git diff HEAD~5 HEAD
```

or:

```bash
git log -p HEAD~5..HEAD
```

Use `git diff` when you want the final tree-to-tree change.

Use `git log -p` when you want to understand each individual commit.

---

## Workflow F — Find only added lines

```bash
git diff | grep '^+' | grep -v '^+++'
```

---

## Workflow G — Review with whitespace ignored

```bash
git diff -w
```

This is particularly useful after formatting changes.

---

# 5.23 DevOps and CI Workflows

## Verify generated files are unchanged

A CI job can run:

```bash
git diff --exit-code
```

For example:

```bash
npm run build
git diff --exit-code
```

If the build modifies tracked files unexpectedly, CI fails.

---

## Verify formatting

```bash
npm run format
git diff --exit-code
```

This can ensure formatting is reproducible.

---

## Detect generated documentation changes

```bash
./generate-docs.sh
git diff --exit-code docs/
```

---

## Create a release-change summary

```bash
git diff --stat "$PREVIOUS_TAG" "$CURRENT_TAG"
```

---

## Export release changes

```bash
git diff "$PREVIOUS_TAG" "$CURRENT_TAG" > release.patch
```

---

## Generate changed-file list for CI

```bash
git diff --name-only "$BASE_SHA" "$HEAD_SHA"
```

This can be used to determine which components require testing.

---

## Generate machine-readable change statistics

```bash
git diff --numstat "$BASE_SHA" "$HEAD_SHA"
```

This is useful for scripts that need:

```text
added lines
deleted lines
file names
```

---

# 5.24 High-Value Diff Commands

| Command                        | Description                            | Example                           | Branch State Before and After command | Output               |
| ------------------------------ | -------------------------------------- | --------------------------------- | ------------------------------------- | -------------------- |
| `git diff`                     | Review unstaged changes                | `git diff`                        | `main` → `main`                       | Unstaged patch       |
| `git diff --cached`            | Review staged changes                  | `git diff --cached`               | `main` → `main`                       | Staged patch         |
| `git diff HEAD`                | Review all tracked uncommitted changes | `git diff HEAD`                   | `main` → `main`                       | Full local patch     |
| `git diff A B`                 | Compare two commits                    | `git diff HEAD~1 HEAD`            | `main` → `main`                       | Patch                |
| `git diff A...B`               | Compare B with merge base of A/B       | `git diff main...feature`         | `feature` → `feature`                 | Feature patch        |
| `git diff --stat`              | Show change statistics                 | `git diff --stat`                 | `main` → `main`                       | Statistics           |
| `git diff --shortstat`         | Show compact statistics                | `git diff --shortstat`            | `main` → `main`                       | Summary              |
| `git diff --numstat`           | Machine-friendly line statistics       | `git diff --numstat`              | `main` → `main`                       | Added/deleted counts |
| `git diff --name-only`         | Show changed files                     | `git diff --name-only`            | `main` → `main`                       | Filenames            |
| `git diff --name-status`       | Show status and filenames              | `git diff --name-status`          | `main` → `main`                       | Status + filenames   |
| `git diff -w`                  | Ignore whitespace                      | `git diff -w`                     | `main` → `main`                       | Reduced-noise patch  |
| `git diff --word-diff`         | Show word-level changes                | `git diff --word-diff`            | `main` → `main`                       | Word-level diff      |
| `git diff --minimal`           | Attempt minimal edit diff              | `git diff --minimal`              | `main` → `main`                       | Diff                 |
| `git diff --patience`          | Use patience algorithm                 | `git diff --patience`             | `main` → `main`                       | Diff                 |
| `git diff --histogram`         | Use histogram algorithm                | `git diff --histogram`            | `main` → `main`                       | Diff                 |
| `git diff -M`                  | Detect renames                         | `git diff -M`                     | `main` → `main`                       | Rename-aware diff    |
| `git diff -C`                  | Detect copies                          | `git diff -C`                     | `main` → `main`                       | Copy-aware diff      |
| `git show HEAD`                | Review latest commit                   | `git show HEAD`                   | `main` → `main`                       | Commit + patch       |
| `git show --stat HEAD`         | Review commit statistics               | `git show --stat HEAD`            | `main` → `main`                       | Statistics           |
| `git show --name-status HEAD`  | Show files changed by commit           | `git show --name-status HEAD`     | `main` → `main`                       | Status + files       |
| `git log -p`                   | Review commit history with patches     | `git log -p -5`                   | `main` → `main`                       | Commit patches       |
| `git diff > file.patch`        | Export diff                            | `git diff > changes.patch`        | `main` → `main`                       | Patch file           |
| `git apply file.patch`         | Apply patch                            | `git apply changes.patch`         | `main` → `main`                       | Working-tree changes |
| `git apply --check file.patch` | Validate patch                         | `git apply --check changes.patch` | `main` → `main`                       | Validation result    |
| `git diff --quiet`             | Test whether unstaged diff exists      | `git diff --quiet`                | `main` → `main`                       | Usually no output    |
| `git diff HEAD --exit-code`    | Fail if tracked changes exist          | `git diff HEAD --exit-code`       | `main` → `main`                       | Exit status          |

---

# Recommended Code Review Sequence

For a normal local feature:

```bash
git status -sb
git diff
git diff --stat
```

After staging:

```bash
git add -p
git diff --cached
git diff --cached --stat
git diff --cached --name-status
```

Before committing:

```bash
git diff --cached
git status -sb
```

After committing:

```bash
git show --stat HEAD
git show HEAD
```

For a feature branch:

```bash
git fetch origin
git diff --stat origin/main...HEAD
git diff --name-status origin/main...HEAD
git diff origin/main...HEAD
```

---

# Quick Mental Model

Memorize these four commands:

```bash
git diff
```

> What have I changed but **not staged**?

```bash
git diff --cached
```

> What have I staged for the **next commit**?

```bash
git diff HEAD
```

> What has changed since the **last commit**?

```bash
git diff main...HEAD
```

> What did my **current branch introduce since it diverged from main**?

For code review, the most important habit is:

```bash
git diff --cached
```

**before every commit**, and:

```bash
git diff origin/main...HEAD
```

**before reviewing or submitting a feature branch**.

---

## Next Part

**Next file:** `06-branching.md`

[Next: Branching](06-branching.md)
