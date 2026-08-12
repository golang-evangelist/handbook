# 14. Comparing Branches & Commits

Git provides several commands for determining exactly how branches, commits, tags, and files differ.

The most important commands in this area are:

```bash
git diff
git log
git merge-base
git show
git range-diff
git rev-list
```

They answer different questions:

```text
git diff
    What changed in the content?

git log
    Which commits are different?

git show
    What did this particular commit change?

git merge-base
    Where did these histories last converge?

git range-diff
    How do two series of commits differ?

git rev-list
    Which commits are reachable from one reference but not another?
```

---

## Table of Contents

* [14.1 Git Comparison Fundamentals](#141-git-comparison-fundamentals)
* [14.2 Compare Working Tree with Index](#142-compare-working-tree-with-index)
* [14.3 Compare Index with HEAD](#143-compare-index-with-head)
* [14.4 Compare Working Tree with HEAD](#144-compare-working-tree-with-head)
* [14.5 Compare Two Commits](#145-compare-two-commits)
* [14.6 Compare Two Branches](#146-compare-two-branches)
* [14.7 Compare a Branch with Main](#147-compare-a-branch-with-main)
* [14.8 Compare Branches from Their Common Ancestor](#148-compare-branches-from-their-common-ancestor)
* [14.9 Two-Dot Revision Ranges](#149-two-dot-revision-ranges)
* [14.10 Three-Dot Revision Ranges](#1410-three-dot-revision-ranges)
* [14.11 Show Only Changed Files](#1411-show-only-changed-files)
* [14.12 Show File Status](#1412-show-file-status)
* [14.13 Show Statistics](#1413-show-statistics)
* [14.14 Show Word-Level Differences](#1414-show-word-level-differences)
* [14.15 Ignore Whitespace](#1415-ignore-whitespace)
* [14.16 Ignore Blank Lines](#1416-ignore-blank-lines)
* [14.17 Detect Renames](#1417-detect-renames)
* [14.18 Detect Copies](#1418-detect-copies)
* [14.19 Compare a Specific File](#1419-compare-a-specific-file)
* [14.20 Compare a Directory](#1420-compare-a-directory)
* [14.21 Compare Tags](#1421-compare-tags)
* [14.22 Compare Releases](#1422-compare-releases)
* [14.23 Compare Remote and Local Branches](#1423-compare-remote-and-local-branches)
* [14.24 Compare Local Branch with Remote-Tracking Branch](#1424-compare-local-branch-with-remote-tracking-branch)
* [14.25 Find Commits Missing from a Branch](#1425-find-commits-missing-from-a-branch)
* [14.26 Find Commits Unique to a Branch](#1426-find-commits-unique-to-a-branch)
* [14.27 Show Symmetric Commit Differences](#1427-show-symmetric-commit-differences)
* [14.28 Find the Merge Base](#1428-find-the-merge-base)
* [14.29 Compare Against the Merge Base](#1429-compare-against-the-merge-base)
* [14.30 Compare Commit Series with range-diff](#1430-compare-commit-series-with-range-diff)
* [14.31 Compare Patch Content](#1431-compare-patch-content)
* [14.32 Compare File Names](#1432-compare-file-names)
* [14.33 Compare Subdirectories](#1433-compare-subdirectories)
* [14.34 Automation-Friendly Comparisons](#1434-automation-friendly-comparisons)
* [14.35 High-Value Comparison Commands](#1435-high-value-comparison-commands)

---

# 14.1 Git Comparison Fundamentals

Git has three important states in a normal working repository:

```text
HEAD
  |
  v
Last committed snapshot

Index / Staging Area
  |
  v
Proposed next commit

Working Tree
  |
  v
Current files on disk
```

Therefore:

```bash
git diff
```

compares:

```text
Working Tree
      vs
Index
```

while:

```bash
git diff --cached
```

compares:

```text
Index
      vs
HEAD
```

and:

```bash
git diff HEAD
```

compares:

```text
Working Tree + Index
      vs
HEAD
```

This distinction is fundamental to understanding Git.

---

# 14.2 Compare Working Tree with Index

Run:

```bash
git diff
```

This shows changes that:

* exist in the working tree;
* have not been staged.

Example:

```text
diff --git a/app.js b/app.js
index 1234567..89abcde 100644
--- a/app.js
+++ b/app.js
@@ -10,6 +10,7 @@
 function start() {
+    initializeDatabase();
 }
```

The branch state does not change.

Only the diff is displayed.

---

| Command             | Description                                 | Example             | Branch State Before and After command | Output                  |
| ------------------- | ------------------------------------------- | ------------------- | ------------------------------------- | ----------------------- |
| `git diff`          | Compare working tree against index          | `git diff`          | Branch unchanged                      | Unstaged changes        |
| `git diff --cached` | Compare index against HEAD                  | `git diff --cached` | Branch unchanged                      | Staged changes          |
| `git diff HEAD`     | Compare working tree and index against HEAD | `git diff HEAD`     | Branch unchanged                      | All uncommitted changes |

---

# 14.3 Compare Index with HEAD

Use:

```bash
git diff --cached
```

Equivalent long form:

```bash
git diff --staged
```

This shows changes that are staged for the next commit.

Example:

```text
diff --git a/app.js b/app.js
index 1234567..89abcde 100644
--- a/app.js
+++ b/app.js
@@ -10,6 +10,7 @@
 function start() {
+    initializeDatabase();
 }
```

No branch movement occurs.

The staging area remains unchanged.

---

# 14.4 Compare Working Tree with HEAD

Use:

```bash
git diff HEAD
```

This includes:

* unstaged changes;
* staged changes.

It compares the current repository contents against the latest commit.

Example:

```bash
git diff HEAD
```

This is useful when you want to answer:

> "What has changed in my repository since the last commit?"

without caring whether changes are staged.

---

# 14.5 Compare Two Commits

Compare two commits:

```bash
git diff <commit1> <commit2>
```

Example:

```bash
git diff a1b2c3d d4e5f6a
```

The output represents the content difference between the two snapshots.

The commits themselves are not changed.

Compare `HEAD` with its parent:

```bash
git diff HEAD~1 HEAD
```

Compare two ancestors:

```bash
git diff HEAD~3 HEAD~1
```

---

# 14.6 Compare Two Branches

Compare branch tips:

```bash
git diff main feature
```

This compares the snapshots pointed to by:

```text
main
```

and:

```text
feature
```

It does not mean:

> "Show all commits that feature contains."

It means:

> "Show the content difference between the two branch tips."

This distinction is extremely important.

For commit differences, use:

```bash
git log main..feature
```

or:

```bash
git log main...feature
```

---

# 14.7 Compare a Branch with Main

Suppose you are currently developing:

```text
feature/auth
```

To compare its current content with `main`:

```bash
git diff main feature/auth
```

If the current branch is `feature/auth`, this can also be written:

```bash
git diff main HEAD
```

Show only statistics:

```bash
git diff --stat main HEAD
```

Show only changed files:

```bash
git diff --name-only main HEAD
```

Show file status:

```bash
git diff --name-status main HEAD
```

---

# 14.8 Compare Branches from Their Common Ancestor

The most common review comparison is:

```bash
git diff main...feature
```

The three-dot form has special meaning for `git diff`.

It compares:

```text
merge-base(main, feature)
              |
              v
            feature
```

In other words, it shows changes introduced by `feature` since it diverged from `main`.

This is often more useful for code review than:

```bash
git diff main feature
```

because `main` may have received unrelated changes after the feature branch was created.

---

# 14.9 Two-Dot Revision Ranges

For `git log`, the two-dot syntax:

```bash
git log main..feature
```

means:

```text
Commits reachable from feature
but not from main
```

Example:

```bash
git log --oneline main..feature
```

Possible output:

```text
a1b2c3d Add authentication
b2c3d4e Add login tests
c3d4e5f Add session handling
```

Reverse comparison:

```bash
git log --oneline feature..main
```

This shows commits on `main` that are not on `feature`.

---

# 14.10 Three-Dot Revision Ranges

Use:

```bash
git log --oneline main...feature
```

This shows commits reachable from either branch but not both.

With:

```bash
git log --oneline --left-right main...feature
```

Git marks the side:

```text
< commit only on main
> commit only on feature
```

Example:

```text
< d4e5f6a Fix production configuration
> a1b2c3d Add authentication
> b2c3d4e Add login tests
```

This is useful for understanding divergence.

For `git diff`, however:

```bash
git diff main...feature
```

has different semantics: it compares the merge base of `main` and `feature` with `feature`.

---

# 14.11 Show Only Changed Files

Use:

```bash
git diff --name-only main feature
```

Example:

```text
src/auth/login.js
src/auth/session.js
tests/auth.test.js
```

This is useful when you only need to know:

> "Which files changed?"

without reading the actual patch.

---

# 14.12 Show File Status

Use:

```bash
git diff --name-status main feature
```

Example:

```text
M       src/auth/login.js
A       src/auth/session.js
D       src/auth/legacy.js
R100    src/auth/old.js        src/auth/new.js
```

Common statuses:

```text
A = Added
M = Modified
D = Deleted
R = Renamed
C = Copied
```

---

# 14.13 Show Statistics

Use:

```bash
git diff --stat main feature
```

Example:

```text
 src/auth/login.js    | 20 ++++++++++++++++
 src/auth/session.js  | 12 +++++++++
 tests/auth.test.js   | 18 +++++++++++++
 3 files changed, 50 insertions(+)
```

Useful alternatives:

```bash
git diff --shortstat main feature
```

and:

```bash
git diff --numstat main feature
```

Example:

```text
20      0       src/auth/login.js
12      0       src/auth/session.js
18      0       tests/auth.test.js
```

The columns represent:

```text
added lines
deleted lines
file
```

---

# 14.14 Show Word-Level Differences

Normal Git diffs operate primarily at line level.

For more precise textual comparison:

```bash
git diff --word-diff main feature
```

Example:

```text
The application uses {+OAuth2+} {-OAuth1-} authentication.
```

Another useful mode:

```bash
git diff --word-diff=porcelain main feature
```

This can be easier for scripts to process.

For Markdown, documentation, configuration, and prose, word-level differences can be particularly useful.

---

# 14.15 Ignore Whitespace

Ignore all whitespace changes:

```bash
git diff -w main feature
```

Equivalent:

```bash
git diff --ignore-all-space main feature
```

Ignore changes in the amount of whitespace:

```bash
git diff -b main feature
```

Ignore whitespace at end of lines:

```bash
git diff --ignore-space-at-eol main feature
```

These options are useful after:

* formatting;
* indentation changes;
* line-ending changes;
* automated code formatting.

Be careful when reviewing code: ignoring whitespace can hide meaningful changes in some languages.

---

# 14.16 Ignore Blank Lines

Use:

```bash
git diff --ignore-blank-lines main feature
```

This is useful when a commit contains large formatting or documentation changes involving blank lines.

Combine options:

```bash
git diff -w --ignore-blank-lines main feature
```

---

# 14.17 Detect Renames

Git can detect renamed files:

```bash
git diff --find-renames main feature
```

Short form:

```bash
git diff -M main feature
```

Example:

```text
R100    src/login.js    src/auth/login.js
```

The percentage indicates similarity.

You can specify a threshold:

```bash
git diff -M90% main feature
```

A lower threshold allows Git to identify less-similar files as renames.

---

# 14.18 Detect Copies

Copy detection:

```bash
git diff --find-copies main feature
```

Short form:

```bash
git diff -C main feature
```

More aggressive copy detection:

```bash
git diff -C -C main feature
```

Copy detection can be computationally expensive on large repositories.

---

# 14.19 Compare a Specific File

Compare a file between two commits:

```bash
git diff main feature -- src/auth/login.js
```

Compare a file between two tags:

```bash
git diff v1.0.0 v2.0.0 -- src/auth/login.js
```

Compare working tree against HEAD:

```bash
git diff HEAD -- src/auth/login.js
```

Compare staged version:

```bash
git diff --cached -- src/auth/login.js
```

The `--` separator explicitly identifies the following argument as a path.

---

# 14.20 Compare a Directory

Compare a directory between branches:

```bash
git diff main feature -- src/
```

Example:

```bash
git diff main feature -- src/auth/
```

Show only changed files:

```bash
git diff --name-only main feature -- src/auth/
```

Show statistics:

```bash
git diff --stat main feature -- src/auth/
```

This is useful for large monorepos where only one application or service is relevant.

---

# 14.21 Compare Tags

Compare two tags:

```bash
git diff v1.0.0 v1.1.0
```

Show commits:

```bash
git log --oneline v1.0.0..v1.1.0
```

Show changed files:

```bash
git diff --name-only v1.0.0 v1.1.0
```

Show statistics:

```bash
git diff --stat v1.0.0 v1.1.0
```

A useful release-analysis combination is:

```bash
git log --oneline v1.0.0..v1.1.0
git diff --stat v1.0.0 v1.1.0
```

---

# 14.22 Compare Releases

For release analysis:

```bash
git diff v2.0.0 v2.1.0
```

For a concise summary:

```bash
git diff --stat v2.0.0 v2.1.0
```

For changed files:

```bash
git diff --name-status v2.0.0 v2.1.0
```

For commits:

```bash
git log --oneline v2.0.0..v2.1.0
```

For first-parent release history:

```bash
git log --oneline --first-parent v2.0.0..v2.1.0
```

---

# 14.23 Compare Remote and Local Branches

First update remote-tracking references:

```bash
git fetch origin
```

Then compare:

```bash
git diff main origin/main
```

Compare commits:

```bash
git log --oneline main..origin/main
```

Reverse:

```bash
git log --oneline origin/main..main
```

Show both directions:

```bash
git log --oneline --left-right main...origin/main
```

This is useful for answering:

> "Is my local branch ahead of or behind the remote-tracking branch?"

---

# 14.24 Compare Local Branch with Remote-Tracking Branch

For the current branch:

```bash
git diff HEAD origin/main
```

Show commits local branch does not have:

```bash
git log --oneline origin/main..HEAD
```

Show commits remote branch has that local branch does not:

```bash
git log --oneline HEAD..origin/main
```

Show both:

```bash
git log --oneline --left-right HEAD...origin/main
```

Example:

```text
< 1234567 Commit exists only on origin/main
> 89abcde Commit exists only locally
```

---

# 14.25 Find Commits Missing from a Branch

Suppose a bug fix exists on:

```text
feature/fix-login
```

and you want to know whether `main` contains it.

First identify the commit:

```bash
git log --oneline feature/fix-login
```

Then:

```bash
git branch --contains <commit>
```

or:

```bash
git branch -a --contains <commit>
```

You can also inspect commits unique to the feature branch:

```bash
git log --oneline main..feature/fix-login
```

---

# 14.26 Find Commits Unique to a Branch

Find commits unique to `feature`:

```bash
git log --oneline main..feature
```

Find commits unique to `main`:

```bash
git log --oneline feature..main
```

Show only commit hashes:

```bash
git rev-list main..feature
```

Show short hashes:

```bash
git rev-list --abbrev-commit main..feature
```

This is useful in automation.

---

# 14.27 Show Symmetric Commit Differences

Use:

```bash
git log --left-right --oneline main...feature
```

Example:

```text
< a1b2c3d Fix production configuration
< b2c3d4e Update dependencies
> c3d4e5f Add authentication
> d4e5f6a Add authentication tests
```

Interpretation:

```text
< = reachable only from the left reference
> = reachable only from the right reference
```

This provides a compact view of branch divergence.

---

# 14.28 Find the Merge Base

Use:

```bash
git merge-base main feature
```

Example:

```text
a1b2c3d4e5f6...
```

This commit is the common ancestor Git considers the best merge base.

Show it:

```bash
git show $(git merge-base main feature)
```

Store it in a shell variable:

```bash
BASE=$(git merge-base main feature)
```

Then:

```bash
git diff "$BASE" feature
```

This is a standard technique for branch comparison.

---

# 14.29 Compare Against the Merge Base

Manual equivalent of the common three-dot diff:

```bash
BASE=$(git merge-base main feature)
git diff "$BASE" feature
```

Equivalent practical form:

```bash
git diff main...feature
```

This answers:

> "What changes did `feature` introduce since it diverged from `main`?"

For code review, this is often the most meaningful comparison.

---

# 14.30 Compare Commit Series with range-diff

`git range-diff` compares two versions of a series of commits.

This is especially useful when reviewing a rebased or amended branch.

Example:

```bash
git range-diff main..feature-v1 main..feature-v2
```

Suppose:

```text
feature-v1
    A
    B
    C

feature-v2
    A'
    B'
    C'
```

`range-diff` attempts to match corresponding commits and show how the series changed.

Typical use case:

```text
Version 1 of a pull request
        |
        v
Reviewer feedback
        |
        v
Reworked commits
        |
        v
Version 2
```

Then:

```bash
git range-diff old-range new-range
```

can show what changed between the two patch series.

---

# 14.31 Compare Patch Content

For a single commit:

```bash
git show <commit>
```

Compare two commits:

```bash
git diff <commit1> <commit2>
```

Compare commit patch IDs:

```bash
git patch-id
```

A common pipeline is:

```bash
git show <commit> --pretty=email | git patch-id
```

Patch IDs can help determine whether two patches have equivalent content even when their commit IDs differ.

This is useful after rebasing or cherry-picking.

---

# 14.32 Compare File Names

List changed files:

```bash
git diff --name-only main feature
```

Show status:

```bash
git diff --name-status main feature
```

Show only names with rename detection:

```bash
git diff --name-status -M main feature
```

Show names with directory statistics:

```bash
git diff --dirstat main feature
```

Example:

```text
 35.0% src/auth/
 25.0% src/api/
 15.0% tests/
```

---

# 14.33 Compare Subdirectories

Compare only backend code:

```bash
git diff main feature -- backend/
```

Compare only frontend code:

```bash
git diff main feature -- frontend/
```

Compare only CI configuration:

```bash
git diff main feature -- .github/
```

Compare only documentation:

```bash
git diff main feature -- docs/
```

This is especially useful in monorepos.

---

# 14.34 Automation-Friendly Comparisons

For scripts, prefer machine-readable output.

Check whether differences exist:

```bash
git diff --quiet main feature
```

Exit status:

```text
0 = no differences
1 = differences exist
```

This is useful in shell scripts:

```bash
if git diff --quiet main feature; then
    echo "Branches are identical"
else
    echo "Branches differ"
fi
```

Check staged changes:

```bash
git diff --cached --quiet
```

Check working-tree changes:

```bash
git diff --quiet
```

List changed files:

```bash
git diff --name-only main feature
```

List changed files with NUL separators:

```bash
git diff -z --name-only main feature
```

This is safer for scripts because file names can contain spaces and other special characters.

---

# 14.35 High-Value Comparison Commands

| Command                             | Description                               | Example                                            | Branch State Before and After command | Output                           |
| ----------------------------------- | ----------------------------------------- | -------------------------------------------------- | ------------------------------------- | -------------------------------- |
| `git diff`                          | Compare working tree with index           | `git diff`                                         | Branch unchanged                      | Unstaged patch                   |
| `git diff --cached`                 | Compare index with HEAD                   | `git diff --cached`                                | Branch unchanged                      | Staged patch                     |
| `git diff HEAD`                     | Compare all uncommitted changes with HEAD | `git diff HEAD`                                    | Branch unchanged                      | Staged + unstaged patch          |
| `git diff A B`                      | Compare two snapshots                     | `git diff main feature`                            | Branch unchanged                      | Content differences              |
| `git diff A...B`                    | Compare B with merge base of A and B      | `git diff main...feature`                          | Branch unchanged                      | Feature changes since divergence |
| `git diff --stat A B`               | Show change statistics                    | `git diff --stat main feature`                     | Branch unchanged                      | Files/insertions/deletions       |
| `git diff --name-only A B`          | Show changed file names                   | `git diff --name-only main feature`                | Branch unchanged                      | File names                       |
| `git diff --name-status A B`        | Show file statuses                        | `git diff --name-status main feature`              | Branch unchanged                      | A/M/D/R/C status                 |
| `git diff --numstat A B`            | Show numeric statistics                   | `git diff --numstat main feature`                  | Branch unchanged                      | Added/deleted lines              |
| `git diff --word-diff A B`          | Show word-level changes                   | `git diff --word-diff main feature`                | Branch unchanged                      | Word-level patch                 |
| `git diff -w A B`                   | Ignore whitespace                         | `git diff -w main feature`                         | Branch unchanged                      | Whitespace-ignored patch         |
| `git diff -b A B`                   | Ignore whitespace amount                  | `git diff -b main feature`                         | Branch unchanged                      | Reduced whitespace differences   |
| `git diff --ignore-blank-lines A B` | Ignore blank-line changes                 | `git diff --ignore-blank-lines main feature`       | Branch unchanged                      | Patch without blank-line changes |
| `git diff -M A B`                   | Detect renames                            | `git diff -M main feature`                         | Branch unchanged                      | Rename detection                 |
| `git diff -C A B`                   | Detect copies                             | `git diff -C main feature`                         | Branch unchanged                      | Copy detection                   |
| `git diff A B -- FILE`              | Compare one file                          | `git diff main feature -- src/app.js`              | Branch unchanged                      | File-specific patch              |
| `git diff A B -- DIR/`              | Compare directory                         | `git diff main feature -- src/`                    | Branch unchanged                      | Directory-specific patch         |
| `git log A..B`                      | Commits on B not on A                     | `git log --oneline main..feature`                  | Branch unchanged                      | Unique commits                   |
| `git log A...B`                     | Symmetric commit difference               | `git log --oneline main...feature`                 | Branch unchanged                      | Commits unique to either side    |
| `git log --left-right A...B`        | Mark which side owns commits              | `git log --left-right --oneline main...feature`    | Branch unchanged                      | `<` and `>` markers              |
| `git merge-base A B`                | Find common ancestor                      | `git merge-base main feature`                      | Branch unchanged                      | Merge-base SHA                   |
| `git branch -a --contains COMMIT`   | Find branches containing commit           | `git branch -a --contains a1b2c3d`                 | Branch unchanged                      | Branch names                     |
| `git tag --contains COMMIT`         | Find tags containing commit               | `git tag --contains a1b2c3d`                       | Branch unchanged                      | Tag names                        |
| `git range-diff A B`                | Compare two commit series                 | `git range-diff main..feature-v1 main..feature-v2` | Branch unchanged                      | Commit-series differences        |
| `git rev-list A..B`                 | List commits reachable only from B        | `git rev-list main..feature`                       | Branch unchanged                      | Commit IDs                       |
| `git diff --quiet A B`              | Test whether snapshots differ             | `git diff --quiet main feature`                    | Branch unchanged                      | No normal output; exit status    |
| `git diff --dirstat A B`            | Show changes by directory                 | `git diff --dirstat main feature`                  | Branch unchanged                      | Directory percentages            |

---

# Branch Comparison Cheat Sheet

```bash
# Working tree vs index
git diff

# Index vs HEAD
git diff --cached

# Working tree + index vs HEAD
git diff HEAD

# Two branch snapshots
git diff main feature

# Feature changes since divergence from main
git diff main...feature

# Changed files
git diff --name-only main feature

# Changed files + statuses
git diff --name-status main feature

# Statistics
git diff --stat main feature

# Numeric statistics
git diff --numstat main feature

# Word-level diff
git diff --word-diff main feature

# Ignore whitespace
git diff -w main feature

# Ignore whitespace amount
git diff -b main feature

# Ignore blank lines
git diff --ignore-blank-lines main feature

# Detect renames
git diff -M main feature

# Detect copies
git diff -C main feature

# Compare one file
git diff main feature -- path/to/file

# Compare one directory
git diff main feature -- src/

# Commits on feature but not main
git log --oneline main..feature

# Commits on main but not feature
git log --oneline feature..main

# Commits unique to either side
git log --oneline --left-right main...feature

# Find common ancestor
git merge-base main feature

# Compare commit series
git range-diff main..feature-v1 main..feature-v2

# Test whether two snapshots differ
git diff --quiet main feature
```

---

# Practical Comparison Workflows

## Code Review

For a feature branch:

```bash
git fetch origin
git diff origin/main...HEAD
```

Show changed files:

```bash
git diff --name-only origin/main...HEAD
```

Show statistics:

```bash
git diff --stat origin/main...HEAD
```

Show complete patch:

```bash
git diff origin/main...HEAD
```

---

## Determine Whether a Branch Is Ahead or Behind

```bash
git fetch origin
git log --oneline --left-right HEAD...origin/main
```

Interpret:

```text
< = local-only commit
> = remote-only commit
```

---

## Find Exactly What a Feature Introduced

```bash
git diff main...feature
```

This compares the feature branch with the common ancestor.

---

## Find Commits Introduced by a Feature

```bash
git log --oneline main..feature
```

This is different from the previous command:

```text
git diff main...feature
    = content changes

git log main..feature
    = commits
```

---

## Compare Two Releases

```bash
git log --oneline v1.0.0..v2.0.0
git diff --stat v1.0.0 v2.0.0
git diff --name-status v1.0.0 v2.0.0
```

---

## Compare a Pull Request Before and After Rework

If you have two commit series:

```bash
git range-diff main..feature-v1 main..feature-v2
```

This is particularly useful after responding to code-review comments.

---

## Check Whether Two Branches Have Identical Content

```bash
git diff --quiet main feature
```

Check the shell exit code:

```bash
echo $?
```

Result:

```text
0
```

means there are no content differences.

A non-zero result means differences exist.

---

# Important Distinctions

## `git diff A B`

Answers:

> What content differs between these two snapshots?

---

## `git log A..B`

Answers:

> Which commits are reachable from B but not A?

---

## `git log A...B`

Answers:

> Which commits are unique to either side?

---

## `git diff A...B`

For `git diff`, this means:

> Compare B with the merge base of A and B.

---

## `git merge-base A B`

Answers:

> What is the common ancestor used as the comparison/merge base?

---

## `git range-diff`

Answers:

> How did one series of commits change compared with another series?

---

# Mental Model

```text
                    main
                     |
                     v
A ---- B ---- C ---- D
             \
              E ---- F ---- G
                         ^
                         |
                       feature
```

For:

```bash
git diff main feature
```

Git compares:

```text
D <--------> G
```

For:

```bash
git diff main...feature
```

Git compares:

```text
C <--------> G
^
|
merge-base
```

For:

```bash
git log main..feature
```

Git finds:

```text
E
F
G
```

For:

```bash
git log feature..main
```

Git finds:

```text
D
```

For:

```bash
git log --left-right main...feature
```

Git identifies:

```text
< D
> E
> F
> G
```

This distinction is one of the most important concepts for effective Git usage.

---

# Final Quick Reference

```bash
# Unstaged changes
git diff

# Staged changes
git diff --cached

# All uncommitted changes
git diff HEAD

# Compare two branches
git diff main feature

# Feature changes since branch point
git diff main...feature

# Changed files
git diff --name-only main feature

# File statuses
git diff --name-status main feature

# Statistics
git diff --stat main feature

# Added/deleted line counts
git diff --numstat main feature

# Word-level comparison
git diff --word-diff main feature

# Ignore whitespace
git diff -w main feature

# Detect renames
git diff -M main feature

# Compare a specific file
git diff main feature -- path/to/file

# Commits on feature only
git log --oneline main..feature

# Commits on main only
git log --oneline feature..main

# Commits unique to either branch
git log --left-right --oneline main...feature

# Find common ancestor
git merge-base main feature

# Compare two commit series
git range-diff main..feature-v1 main..feature-v2

# Check if snapshots are identical
git diff --quiet main feature
```

---

## Next Part

**Next file:** `15-conflict-resolution.md`

[Next: Conflict Resolution](15-conflict-resolution.md)
