# 13. Searching Git History

Git history is one of the most important sources of information for developers, software engineers, reviewers, and DevOps engineers.

Git provides powerful commands for finding:

* commits;
* authors;
* changed files;
* deleted code;
* added code;
* specific strings;
* regular expressions;
* changes to a particular file;
* changes between dates;
* merge commits;
* branches containing a commit;
* tags containing a commit;
* the introduction of a bug;
* the commit that introduced a particular line;
* historical versions of files.

The primary command is:

```bash
git log
```

For practical work, however, `git log` is usually combined with options such as:

```bash
--oneline
--graph
--decorate
--all
--author
--grep
--since
--until
-- file
-S
-G
-L
--follow
```

---

## Table of Contents

* [13.1 Basic History](#131-basic-history)
* [13.2 Compact History](#132-compact-history)
* [13.3 Graph History](#133-graph-history)
* [13.4 Complete Repository History](#134-complete-repository-history)
* [13.5 Show Commit Details](#135-show-commit-details)
* [13.6 Show a Specific Commit](#136-show-a-specific-commit)
* [13.7 Show Changed Files](#137-show-changed-files)
* [13.8 Show File Statistics](#138-show-file-statistics)
* [13.9 Search by Author](#139-search-by-author)
* [13.10 Search by Commit Message](#1310-search-by-commit-message)
* [13.11 Search by Date](#1311-search-by-date)
* [13.12 Search a Specific File](#1312-search-a-specific-file)
* [13.13 Follow File History](#1313-follow-file-history)
* [13.14 Search for Added or Removed Text](#1314-search-for-added-or-removed-text)
* [13.15 Search by Regular Expression](#1315-search-by-regular-expression)
* [13.16 Find Who Changed a Line](#1316-find-who-changed-a-line)
* [13.17 Blame a File](#1317-blame-a-file)
* [13.18 Blame a Specific Range](#1318-blame-a-specific-range)
* [13.19 Search Merge Commits](#1319-search-merge-commits)
* [13.20 Search First-Parent History](#1320-search-first-parent-history)
* [13.21 Search Branch History](#1321-search-branch-history)
* [13.22 Search Commits Not on Another Branch](#1322-search-commits-not-on-another-branch)
* [13.23 Find Common Ancestors](#1323-find-common-ancestors)
* [13.24 Find Branches Containing a Commit](#1324-find-branches-containing-a-commit)
* [13.25 Find Tags Containing a Commit](#1325-find-tags-containing-a-commit)
* [13.26 Search Commit Ranges](#1326-search-commit-ranges)
* [13.27 Search Between Releases](#1327-search-between-releases)
* [13.28 Search by Commit Count](#1328-search-by-commit-count)
* [13.29 Search by File Type](#1329-search-by-file-type)
* [13.30 Search Deleted Files](#1330-search-deleted-files)
* [13.31 Search Renamed Files](#1331-search-renamed-files)
* [13.32 Search History Across All References](#1332-search-history-across-all-references)
* [13.33 Machine-Friendly History](#1333-machine-friendly-history)
* [13.34 Advanced History Investigation](#1334-advanced-history-investigation)
* [13.35 High-Value History Commands](#1335-high-value-history-commands)

---

# 13.1 Basic History

Display commit history:

```bash
git log
```

Example:

```text
commit a1b2c3d
Author: Developer <developer@example.com>
Date:   Wed Aug 12 10:00:00 2026 +0000

    Add authentication
```

`git log` normally starts from the current `HEAD` and walks backward through reachable commits.

The branch does not change.

---

# 13.2 Compact History

For everyday development, `--oneline` is much easier to read:

```bash
git log --oneline
```

Example:

```text
a1b2c3d Add authentication
b2c3d4e Fix login validation
c3d4e5f Add user model
d4e5f6a Initial project setup
```

Limit the number of commits:

```bash
git log --oneline -10
```

This displays the most recent 10 commits.

---

| Command                 | Description             | Example                 | Branch State Before and After command | Output                  |
| ----------------------- | ----------------------- | ----------------------- | ------------------------------------- | ----------------------- |
| `git log`               | Show commit history     | `git log`               | Branch unchanged                      | Full commit information |
| `git log --oneline`     | Compact history         | `git log --oneline`     | Branch unchanged                      | One line per commit     |
| `git log -10`           | Show last 10 commits    | `git log -10`           | Branch unchanged                      | Ten commits             |
| `git log --oneline -10` | Compact last 10 commits | `git log --oneline -10` | Branch unchanged                      | Ten compact commits     |

---

# 13.3 Graph History

Visualize branch and merge structure:

```bash
git log --graph
```

A more useful form:

```bash
git log --oneline --graph --decorate
```

Example:

```text
* a1b2c3d (HEAD -> main) Release v2.0.0
*   b2c3d4e Merge branch 'feature/auth'
|\
| * c3d4e5f Add authentication
| * d4e5f6a Fix login validation
|/
* e5f6a7b Update dependencies
* f6a7b8c Initial commit
```

For all branches:

```bash
git log --oneline --graph --decorate --all
```

This is one of the most useful history commands for understanding repository topology.

---

# 13.4 Complete Repository History

The default `git log` follows the current branch.

To inspect history reachable from all local references:

```bash
git log --all
```

Compact:

```bash
git log --oneline --all
```

Graph:

```bash
git log --oneline --graph --decorate --all
```

This is especially useful when investigating:

* old branches;
* unmerged work;
* merge history;
* commits that are not reachable from the current branch.

---

# 13.5 Show Commit Details

Show the latest commit:

```bash
git show HEAD
```

Show the previous commit:

```bash
git show HEAD~1
```

Show two commits before `HEAD`:

```bash
git show HEAD~2
```

Show a commit by SHA:

```bash
git show a1b2c3d
```

Show only the summary:

```bash
git show --summary a1b2c3d
```

Show statistics:

```bash
git show --stat a1b2c3d
```

---

# 13.6 Show a Specific Commit

Use:

```bash
git show <commit>
```

Example:

```bash
git show a1b2c3d
```

Show only the commit message:

```bash
git log -1 --format=%B a1b2c3d
```

Show the author:

```bash
git show -s --format='%an <%ae>' a1b2c3d
```

Show the commit timestamp:

```bash
git show -s --format='%ci' a1b2c3d
```

Show the commit hash:

```bash
git show -s --format='%H' a1b2c3d
```

---

# 13.7 Show Changed Files

Show names of files changed by a commit:

```bash
git show --name-only --format= a1b2c3d
```

Example:

```text
src/auth/login.js
src/auth/session.js
tests/auth.test.js
```

Show status of changed files:

```bash
git show --name-status --format= a1b2c3d
```

Example:

```text
M       src/auth/login.js
A       src/auth/session.js
D       src/auth/legacy.js
```

Status letters commonly include:

```text
A = Added
M = Modified
D = Deleted
R = Renamed
C = Copied
```

---

# 13.8 Show File Statistics

Show statistics for a commit:

```bash
git show --stat a1b2c3d
```

Example:

```text
 src/auth/login.js   | 20 ++++++++++++++++
 src/auth/session.js | 12 +++++++++
 2 files changed, 32 insertions(+)
```

For history:

```bash
git log --stat
```

Compact statistics:

```bash
git log --shortstat
```

---

| Command                         | Description                 | Example                               | Branch State Before and After command | Output                 |
| ------------------------------- | --------------------------- | ------------------------------------- | ------------------------------------- | ---------------------- |
| `git show --name-only COMMIT`   | List changed files          | `git show --name-only --format= HEAD` | Branch unchanged                      | File names             |
| `git show --name-status COMMIT` | Show file change status     | `git show --name-status HEAD`         | Branch unchanged                      | Status and file names  |
| `git show --stat COMMIT`        | Show change statistics      | `git show --stat HEAD`                | Branch unchanged                      | Insertions/deletions   |
| `git log --stat`                | Show statistics for history | `git log --stat`                      | Branch unchanged                      | Per-commit statistics  |
| `git log --shortstat`           | Show compact statistics     | `git log --shortstat`                 | Branch unchanged                      | Short change summaries |

---

# 13.9 Search by Author

Search commits by author:

```bash
git log --author="Alice"
```

Case-insensitive regular expression matching can be used:

```bash
git log --author="alice"
```

Search a specific email:

```bash
git log --author="alice@example.com"
```

Compact:

```bash
git log --oneline --author="Alice"
```

Combine with a file:

```bash
git log --author="Alice" -- src/auth/login.js
```

The branch remains unchanged.

---

# 13.10 Search by Commit Message

Search commit messages with:

```bash
git log --grep="authentication"
```

Example:

```bash
git log --oneline --grep="authentication"
```

Possible output:

```text
a1b2c3d Add authentication
c3d4e5f Fix authentication timeout
```

Case-insensitive:

```bash
git log --grep="authentication" -i
```

Search multiple concepts:

```bash
git log --grep="auth" --grep="login"
```

Note that multiple `--grep` expressions are OR-ed by default.

---

# 13.11 Search by Date

Commits after a date:

```bash
git log --since="2026-08-01"
```

Commits before a date:

```bash
git log --until="2026-08-12"
```

Date range:

```bash
git log --since="2026-08-01" --until="2026-08-12"
```

Natural language is also commonly supported:

```bash
git log --since="2 weeks ago"
```

or:

```bash
git log --since="yesterday"
```

Compact:

```bash
git log --oneline --since="1 month ago"
```

---

# 13.12 Search a Specific File

Show history for one file:

```bash
git log -- src/auth/login.js
```

Compact:

```bash
git log --oneline -- src/auth/login.js
```

Show patches:

```bash
git log -p -- src/auth/login.js
```

The `--` separates Git revisions/options from paths.

This is particularly important when a branch name could be confused with a file name.

---

# 13.13 Follow File History

If a file has been renamed, use:

```bash
git log --follow -- src/auth/login.js
```

Example:

```text
src/login.js
    |
    | renamed
    v
src/auth/login.js
```

Without `--follow`, history may appear to begin at the rename boundary.

With:

```bash
git log --follow -- src/auth/login.js
```

Git attempts to follow the file through renames.

---

# 13.14 Search for Added or Removed Text

The `-S` option searches for changes in the number of occurrences of a string.

Example:

```bash
git log -S"TODO"
```

Search within a file:

```bash
git log -S"authenticateUser" -- src/auth/login.js
```

This is useful when you know the exact text that appeared or disappeared.

For example:

```bash
git log -S"MAX_RETRIES" -- src/config.js
```

This can help identify the commit that introduced or removed `MAX_RETRIES`.

---

# 13.15 Search by Regular Expression

The `-G` option searches added/removed lines using a regular expression:

```bash
git log -G"TODO|FIXME"
```

Search for function definitions:

```bash
git log -G"function[[:space:]]+login"
```

Search a specific file:

```bash
git log -G"authenticate" -- src/auth/login.js
```

### `-S` vs `-G`

Use:

```bash
-S"exact text"
```

when you want to find commits that changed the number of occurrences of exact text.

Use:

```bash
-G"regex"
```

when you want to find commits whose patches contain lines matching a regular expression.

---

# 13.16 Find Who Changed a Line

Use `git blame`:

```bash
git blame src/auth/login.js
```

Example:

```text
a1b2c3d (Alice 2026-08-01 10:20:00 +0000 42) return authenticate(user);
```

This tells you which commit last changed each line.

To investigate that commit:

```bash
git show a1b2c3d
```

A useful workflow is:

```bash
git blame src/auth/login.js
git show <commit>
git log <commit>^..<commit>
```

---

# 13.17 Blame a File

Basic:

```bash
git blame <file>
```

Example:

```bash
git blame src/main.js
```

Ignore whitespace changes:

```bash
git blame -w src/main.js
```

Show line numbers:

```bash
git blame -L 20,40 src/main.js
```

Ignore revisions listed in a blame-ignore file:

```bash
git blame --ignore-revs-file=.git-blame-ignore-revs src/main.js
```

This can be useful when large formatting commits would otherwise obscure meaningful history.

---

# 13.18 Blame a Specific Range

Blame only lines 100 through 120:

```bash
git blame -L 100,120 -- src/main.js
```

Or a function/range supported by Git's line-range syntax:

```bash
git blame -L :functionName -- src/main.js
```

Example:

```bash
git blame -L :authenticateUser -- src/auth/login.js
```

This is useful during debugging when only a particular function or section matters.

---

# 13.19 Search Merge Commits

List merge commits:

```bash
git log --merges
```

Compact:

```bash
git log --oneline --merges
```

Graph:

```bash
git log --graph --oneline --merges
```

Search merge commits containing a phrase:

```bash
git log --merges --grep="feature"
```

Merge commits are especially useful when reconstructing integration history.

---

# 13.20 Search First-Parent History

When a repository uses merge-based development, first-parent history provides a clean view of the main integration line:

```bash
git log --first-parent
```

Compact:

```bash
git log --oneline --first-parent
```

Graph:

```bash
git log --oneline --graph --first-parent
```

This is particularly useful for release history.

Example:

```text
* Release v2.0.0
* Merge pull request #42
* Merge pull request #41
* Release v1.9.0
```

Instead of displaying every commit from every merged feature branch.

---

# 13.21 Search Branch History

Show history of a branch:

```bash
git log feature/auth
```

Compact:

```bash
git log --oneline feature/auth
```

Graph:

```bash
git log --oneline --graph feature/auth
```

Compare branch history against current branch:

```bash
git log main..feature/auth
```

This displays commits reachable from `feature/auth` but not from `main`.

---

# 13.22 Search Commits Not on Another Branch

Find commits on `feature` that are not on `main`:

```bash
git log --oneline main..feature
```

Reverse:

```bash
git log --oneline feature..main
```

Both directions:

```bash
git log --oneline --left-right main...feature
```

The triple-dot form identifies commits reachable from either side but not both.

Example:

```text
< a1b2c3d commit only on main
> d4e5f6a commit only on feature
```

---

# 13.23 Find Common Ancestors

Find the merge base of two branches:

```bash
git merge-base main feature
```

Example output:

```text
a1b2c3d
```

This identifies their best common ancestor.

Inspect it:

```bash
git show a1b2c3d
```

A common workflow:

```bash
BASE=$(git merge-base main feature)
git diff "$BASE" feature
```

This compares the feature branch against the common ancestor.

---

# 13.24 Find Branches Containing a Commit

Find local branches containing a commit:

```bash
git branch --contains a1b2c3d
```

Find remote-tracking branches:

```bash
git branch -r --contains a1b2c3d
```

Find all branches:

```bash
git branch -a --contains a1b2c3d
```

This is useful when asking:

> "Has this fix already reached the release branch?"

---

# 13.25 Find Tags Containing a Commit

Find tags that contain a commit:

```bash
git tag --contains a1b2c3d
```

This is useful for release verification.

Example:

```text
v1.4.0
v1.5.0
v2.0.0
```

You can answer:

> "Which releases contain this fix?"

without manually inspecting every release.

---

# 13.26 Search Commit Ranges

Two-dot range:

```bash
git log A..B
```

Meaning:

```text
commits reachable from B
but not reachable from A
```

Example:

```bash
git log --oneline v1.0.0..v1.1.0
```

Three-dot range:

```bash
git log A...B
```

This shows commits reachable from either side but not both.

Example:

```bash
git log --oneline main...feature
```

For symmetric difference with side markers:

```bash
git log --oneline --left-right main...feature
```

---

# 13.27 Search Between Releases

A very common release-analysis command:

```bash
git log --oneline v1.0.0..v1.1.0
```

Show statistics:

```bash
git diff --stat v1.0.0 v1.1.0
```

Show changed files:

```bash
git diff --name-only v1.0.0 v1.1.0
```

Show complete patch:

```bash
git diff v1.0.0 v1.1.0
```

Search only commits:

```bash
git log --oneline v1.0.0..v1.1.0
```

---

# 13.28 Search by Commit Count

Show the last N commits:

```bash
git log -n 20
```

Equivalent:

```bash
git log -20
```

Compact:

```bash
git log --oneline -20
```

Search the last 20 commits for a message:

```bash
git log --oneline -20 --grep="fix"
```

This is useful when quickly inspecting recent development activity.

---

# 13.29 Search by File Type

Git pathspecs can be used to restrict history.

For Markdown files:

```bash
git log -- '*.md'
```

For shell scripts:

```bash
git log -- '*.sh'
```

For Java files:

```bash
git log -- '*.java'
```

For a directory:

```bash
git log -- src/
```

For multiple paths:

```bash
git log -- src/ tests/
```

Use quotes around wildcard pathspecs so that the shell does not expand them before Git receives them.

---

# 13.30 Search Deleted Files

Find commits that deleted files:

```bash
git log --diff-filter=D --summary
```

List deleted file names:

```bash
git log --diff-filter=D --name-only --format=
```

Search history for a specific deleted file:

```bash
git log --all --full-history -- path/to/deleted-file
```

Once the commit is identified:

```bash
git show <commit>:path/to/deleted-file
```

This can recover the contents without restoring the file to the working tree.

---

# 13.31 Search Renamed Files

Git can detect renames in history.

Show rename information:

```bash
git log --follow -- path/to/file
```

Show rename status:

```bash
git log --name-status --find-renames
```

Increase rename detection sensitivity:

```bash
git log --find-renames=50% -- path/to/file
```

The exact rename detection result depends on repository history and similarity.

---

# 13.32 Search History Across All References

Search every reachable local reference:

```bash
git log --all
```

Compact:

```bash
git log --oneline --all
```

Graph:

```bash
git log --oneline --graph --decorate --all
```

This is one of the first commands to use when you suspect:

* a commit exists on another branch;
* a feature branch contains an old fix;
* a commit was accidentally left outside the current branch;
* a release branch contains the desired commit.

---

# 13.33 Machine-Friendly History

For scripting and automation, use explicit formats.

Full commit hash:

```bash
git log -1 --format=%H
```

Short hash:

```bash
git log -1 --format=%h
```

Author:

```bash
git log -1 --format=%an
```

Author email:

```bash
git log -1 --format=%ae
```

Commit subject:

```bash
git log -1 --format=%s
```

Commit date:

```bash
git log -1 --format=%ci
```

Multiple fields:

```bash
git log -1 --format='%H|%an|%ae|%ci|%s'
```

Example:

```text
a1b2c3d4...|Alice|alice@example.com|2026-08-12 10:00:00 +0000|Add authentication
```

This is useful for shell scripts and CI/CD systems.

---

# 13.34 Advanced History Investigation

## Find when a string was introduced

```bash
git log --all -S"authenticateUser" --oneline
```

---

## Find when a regular expression changed

```bash
git log --all -G"authenticate.*user" --oneline
```

---

## Find who last changed a line

```bash
git blame -L 42,42 -- src/auth/login.js
```

---

## Inspect that commit

```bash
git show <commit>
```

---

## Find branches containing it

```bash
git branch -a --contains <commit>
```

---

## Find releases containing it

```bash
git tag --contains <commit>
```

---

## Find commits between two releases

```bash
git log --oneline v1.0.0..v2.0.0
```

---

## Find changes in one file between releases

```bash
git log --oneline v1.0.0..v2.0.0 -- src/auth/login.js
```

---

## Find the commit that introduced a line of code

```bash
git log -S"someUniqueLine" --all --oneline -- path/to/file
```

---

## Inspect old contents

```bash
git show <commit>:path/to/file
```

---

## Search complete repository history

```bash
git log --all --full-history -- path/to/file
```

These commands form a powerful investigation chain:

```text
Search
  |
  v
Identify commit
  |
  v
git show
  |
  v
git branch --contains
  |
  v
git tag --contains
```

---

# 13.35 High-Value History Commands

| Command                                      | Description                           | Example                                      | Branch State Before and After command | Output                  |
| -------------------------------------------- | ------------------------------------- | -------------------------------------------- | ------------------------------------- | ----------------------- |
| `git log`                                    | Show history                          | `git log`                                    | Branch unchanged                      | Commit history          |
| `git log --oneline`                          | Compact history                       | `git log --oneline`                          | Branch unchanged                      | One-line commits        |
| `git log --graph --oneline --decorate --all` | Visualize complete history            | `git log --graph --oneline --decorate --all` | Branch unchanged                      | Commit graph            |
| `git show COMMIT`                            | Show commit details                   | `git show HEAD`                              | Branch unchanged                      | Commit and patch        |
| `git show --stat COMMIT`                     | Show commit statistics                | `git show --stat HEAD`                       | Branch unchanged                      | File statistics         |
| `git show --name-only COMMIT`                | List changed files                    | `git show --name-only --format= HEAD`        | Branch unchanged                      | File names              |
| `git log --author="NAME"`                    | Search author                         | `git log --author="Alice"`                   | Branch unchanged                      | Matching commits        |
| `git log --grep="TEXT"`                      | Search commit messages                | `git log --grep="authentication"`            | Branch unchanged                      | Matching commits        |
| `git log --since="DATE"`                     | Search after date                     | `git log --since="2026-08-01"`               | Branch unchanged                      | Matching commits        |
| `git log --until="DATE"`                     | Search before date                    | `git log --until="2026-08-12"`               | Branch unchanged                      | Matching commits        |
| `git log -- FILE`                            | Search file history                   | `git log -- src/main.js`                     | Branch unchanged                      | File history            |
| `git log --follow -- FILE`                   | Follow file through renames           | `git log --follow -- src/main.js`            | Branch unchanged                      | File history            |
| `git log -S"TEXT"`                           | Search exact text changes             | `git log -S"authenticateUser"`               | Branch unchanged                      | Matching commits        |
| `git log -G"REGEX"`                          | Search regex changes                  | `git log -G"authenticate.*"`                 | Branch unchanged                      | Matching commits        |
| `git blame FILE`                             | Find last changer of each line        | `git blame src/main.js`                      | Branch unchanged                      | Commit/author per line  |
| `git blame -L START,END FILE`                | Blame line range                      | `git blame -L 20,40 src/main.js`             | Branch unchanged                      | Line attribution        |
| `git log --merges`                           | Show merge commits                    | `git log --merges`                           | Branch unchanged                      | Merge history           |
| `git log --first-parent`                     | Follow first-parent integration line  | `git log --first-parent`                     | Branch unchanged                      | Mainline history        |
| `git log main..feature`                      | Commits only on feature               | `git log --oneline main..feature`            | Branch unchanged                      | Feature-only commits    |
| `git log main...feature`                     | Symmetric branch difference           | `git log --oneline main...feature`           | Branch unchanged                      | Unique commits          |
| `git merge-base main feature`                | Find common ancestor                  | `git merge-base main feature`                | Branch unchanged                      | Commit ID               |
| `git branch --contains COMMIT`               | Find local branches containing commit | `git branch --contains a1b2c3d`              | Branch unchanged                      | Branch names            |
| `git branch -a --contains COMMIT`            | Find all branches containing commit   | `git branch -a --contains a1b2c3d`           | Branch unchanged                      | Local/remote branches   |
| `git tag --contains COMMIT`                  | Find releases containing commit       | `git tag --contains a1b2c3d`                 | Branch unchanged                      | Tags                    |
| `git log A..B`                               | Search range                          | `git log --oneline v1.0.0..v1.1.0`           | Branch unchanged                      | Commits in range        |
| `git log --all`                              | Search all reachable refs             | `git log --all`                              | Branch unchanged                      | Repository history      |
| `git log --diff-filter=D`                    | Find deletions                        | `git log --diff-filter=D --summary`          | Branch unchanged                      | Deleted files           |
| `git log --name-status`                      | Show file statuses                    | `git log --name-status`                      | Branch unchanged                      | A/M/D/R changes         |
| `git log --format=%H`                        | Output full commit hash               | `git log -1 --format=%H`                     | Branch unchanged                      | Commit hash             |
| `git log --format=%s`                        | Output commit subject                 | `git log -1 --format=%s`                     | Branch unchanged                      | Subject                 |
| `git show COMMIT:FILE`                       | Show historical file contents         | `git show HEAD~1:src/main.js`                | Branch unchanged                      | Historical file content |

---

# Practical Investigation Workflows

## Find who introduced a suspicious line

```bash
git blame -L 100,100 -- src/main.js
git show <commit>
```

---

## Find when a configuration value was introduced

```bash
git log --all -S"MAX_RETRIES" --oneline -- src/config.js
```

Then:

```bash
git show <commit>
```

---

## Find which release contains a fix

```bash
git tag --contains <commit>
```

---

## Find which branches contain a fix

```bash
git branch -a --contains <commit>
```

---

## Compare two releases

```bash
git log --oneline v1.0.0..v1.1.0
git diff --stat v1.0.0 v1.1.0
```

---

## Find changes to one file between releases

```bash
git log --oneline v1.0.0..v1.1.0 -- src/main.js
```

---

## Inspect complete branch topology

```bash
git log --oneline --graph --decorate --all
```

---

## Investigate a deleted file

```bash
git log --all --full-history -- path/to/file
```

Then:

```bash
git show <commit>:path/to/file
```

---

# History Search Cheat Sheet

```bash
# Full history
git log

# Compact history
git log --oneline

# Graph
git log --oneline --graph --decorate --all

# Last 20 commits
git log --oneline -20

# Search author
git log --author="Alice"

# Search message
git log --grep="authentication"

# Search date
git log --since="2026-08-01" --until="2026-08-12"

# File history
git log -- path/to/file

# Follow renames
git log --follow -- path/to/file

# Search exact text changes
git log -S"someText" -- path/to/file

# Search regex changes
git log -G"someRegex" -- path/to/file

# Find who changed lines
git blame path/to/file

# Blame specific lines
git blame -L 20,40 -- path/to/file

# Merge commits
git log --merges

# First-parent history
git log --first-parent

# Commits on feature but not main
git log --oneline main..feature

# Symmetric branch difference
git log --oneline main...feature

# Find merge base
git merge-base main feature

# Branches containing commit
git branch -a --contains <commit>

# Tags containing commit
git tag --contains <commit>

# Search all refs
git log --all

# Deleted files
git log --diff-filter=D --summary

# Historical file contents
git show <commit>:path/to/file
```

---

# Key Concepts to Remember

```text
git log
    |
    +-- history
    |
    +-- --author
    |      -> who
    |
    +-- --grep
    |      -> commit message
    |
    +-- --since / --until
    |      -> when
    |
    +-- -- FILE
    |      -> where
    |
    +-- -S
    |      -> exact text change
    |
    +-- -G
    |      -> regex change
    |
    +-- git blame
           -> who changed this line
```

The most important distinction is:

```text
git log
    = "Which commits are relevant?"

git show
    = "What exactly did this commit change?"

git blame
    = "Which commit last changed this line?"

git log -S
    = "When did this exact text change?"

git log -G
    = "When did matching code change?"

git branch --contains
    = "Which branches contain this commit?"

git tag --contains
    = "Which releases contain this commit?"
```

---

## Next Part

**Next file:** `14-comparing-branches-and-commits.md`

[Next: Comparing Branches & Commits](14-comparing-branches-and-commits.md)
