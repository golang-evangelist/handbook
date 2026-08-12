# 27. File Tracking

Git file tracking is the process by which Git determines which files belong to the repository's version-controlled state.

Understanding file tracking is essential for working effectively with:

* `git add`
* `git rm`
* `git mv`
* `git restore`
* `git ls-files`
* `.gitignore`
* The index/staging area
* Commits
* Renames
* Deleted files
* Untracked files
* Ignored files

The most important concept is that Git maintains several distinct states for files:

```text
Working Tree
     |
     v
Staging Area / Index
     |
     v
Repository / Commit
```

A file can therefore be:

```text
Untracked
Tracked and unchanged
Tracked and modified
Staged
Deleted
Renamed
Copied
Ignored
```

---

# Table of Contents

* [27.1 Git File Lifecycle](#271-git-file-lifecycle)
* [27.2 Working Tree, Index and Repository](#272-working-tree-index-and-repository)
* [27.3 Untracked Files](#273-untracked-files)
* [27.4 Tracked Files](#274-tracked-files)
* [27.5 Check File Status](#275-check-file-status)
* [27.6 Short Status Format](#276-short-status-format)
* [27.7 Stage a File](#277-stage-a-file)
* [27.8 Stage Multiple Files](#278-stage-multiple-files)
* [27.9 Stage All Changes](#279-stage-all-changes)
* [27.10 Stage New and Modified Files](#2710-stage-new-and-modified-files)
* [27.11 Stage Deleted Files](#2711-stage-deleted-files)
* [27.12 Stage a Directory](#2712-stage-a-directory)
* [27.13 Stage Selected Changes](#2713-stage-selected-changes)
* [27.14 Stage Part of a File](#2714-stage-part-of-a-file)
* [27.15 Unstage a File](#2715-unstage-a-file)
* [27.16 Unstage All Files](#2716-unstage-all-files)
* [27.17 Discard Working Tree Changes](#2717-discard-working-tree-changes)
* [27.18 Restore a File](#2718-restore-a-file)
* [27.19 Restore a Staged File](#2719-restore-a-staged-file)
* [27.20 Delete a Tracked File](#2720-delete-a-tracked-file)
* [27.21 Remove a File from the Index](#2721-remove-a-file-from-the-index)
* [27.22 Move or Rename a File](#2722-move-or-rename-a-file)
* [27.23 Detect Renames](#2723-detect-renames)
* [27.24 Copy a File](#2724-copy-a-file)
* [27.25 List Tracked Files](#2725-list-tracked-files)
* [27.26 List Files in a Commit](#2726-list-files-in-a-commit)
* [27.27 List Staged Files](#2727-list-staged-files)
* [27.28 List Modified Files](#2728-list-modified-files)
* [27.29 List Untracked Files](#2729-list-untracked-files)
* [27.30 List Ignored Files](#2730-list-ignored-files)
* [27.31 Find Files in the Index](#2731-find-files-in-the-index)
* [27.32 Check Whether a File Is Tracked](#2732-check-whether-a-file-is-tracked)
* [27.33 File Names with Spaces](#2733-file-names-with-spaces)
* [27.34 File Tracking and `.gitignore`](#2734-file-tracking-and-gitignore)
* [27.35 Force Tracking of Ignored Files](#2735-force-tracking-of-ignored-files)
* [27.36 Remove All Files from the Index](#2736-remove-all-files-from-the-index)
* [27.37 Refresh the Index](#2737-refresh-the-index)
* [27.38 File Mode Changes](#2738-file-mode-changes)
* [27.39 End-of-Line Handling](#2739-end-of-line-handling)
* [27.40 Large Files](#2740-large-files)
* [27.41 File Tracking in CI/CD](#2741-file-tracking-in-cicd)
* [27.42 File Tracking Troubleshooting](#2742-file-tracking-troubleshooting)
* [27.43 Complete File Tracking Command Reference](#2743-complete-file-tracking-command-reference)
* [27.44 High-Value Commands to Memorize](#2744-high-value-commands-to-memorize)

---

# 27.1 Git File Lifecycle

A typical file progresses through these states:

```text
                     git add
          +--------------------------+
          |                          |
          v                          |
      UNTRACKED -----------------> STAGED
          ^                         |
          |                         | git commit
          |                         v
          |                     COMMITTED
          |                         |
          |                         | edit
          |                         v
          +--------------------- MODIFIED
```

A tracked file can also be:

```text
Modified
Deleted
Renamed
Copied
```

The working tree contains the files you are currently editing.

The index contains what will become the next commit.

The repository contains previously committed snapshots.

---

# 27.2 Working Tree, Index and Repository

Git can be understood as three major areas:

```text
+--------------------+
| Working Tree       |
| Files you edit     |
+--------------------+
          |
       git add
          |
          v
+--------------------+
| Index / Staging    |
| Next commit        |
+--------------------+
          |
      git commit
          |
          v
+--------------------+
| Repository         |
| Commit history     |
+--------------------+
```

For example:

```bash
echo "hello" > file.txt
```

creates or modifies the working tree.

Then:

```bash
git add file.txt
```

copies the current contents into the index.

Then:

```bash
git commit -m "Add file"
```

creates a commit containing the staged version.

---

# 27.3 Untracked Files

A file is untracked when Git does not have it in the index and it is not ignored.

Create one:

```bash
echo "hello" > notes.txt
```

Check:

```bash
git status
```

Typical output:

```text
Untracked files:
  notes.txt
```

Short status:

```bash
git status --short
```

Output:

```text
?? notes.txt
```

The `??` marker means the file is untracked.

---

# 27.4 Tracked Files

A tracked file is known to Git through the index and/or repository history.

List tracked files:

```bash
git ls-files
```

Example:

```text
README.md
src/main.c
src/utils.c
```

A tracked file may be:

```text
Unchanged
Modified
Staged
Deleted
Renamed
```

Tracking does not necessarily mean the file has no changes.

---

# 27.5 Check File Status

The primary command is:

```bash
git status
```

Example:

```text
On branch main

Changes not staged for commit:
  modified:   src/main.c

Untracked files:
  notes.txt
```

Short version:

```bash
git status --short
```

Example:

```text
 M src/main.c
?? notes.txt
```

`git status` is one of the most important commands in Git.

Use it frequently before and after modifying the index.

---

# 27.6 Short Status Format

Run:

```bash
git status --short
```

Example:

```text
 M file1.txt
M  file2.txt
MM file3.txt
A  file4.txt
D  file5.txt
?? file6.txt
```

The two status columns represent different states.

```text
XY filename
```

Where:

```text
X = index/staging status
Y = working tree status
```

Common values:

| Code | Meaning                                                   |
| ---- | --------------------------------------------------------- |
| `??` | Untracked                                                 |
| `A ` | Added to index                                            |
| ` M` | Modified in working tree                                  |
| `M ` | Modified in index                                         |
| `MM` | Staged modification plus additional unstaged modification |
| `D ` | Deleted in index                                          |
| ` D` | Deleted in working tree                                   |
| `R ` | Renamed in index                                          |
| `C ` | Copied in index                                           |

---

# 27.7 Stage a File

Use:

```bash
git add file.txt
```

Example:

```bash
git add src/main.c
```

Before:

```text
?? src/main.c
```

After:

```text
A  src/main.c
```

The file is now staged for the next commit.

No branch pointer changes.

The index changes.

---

# 27.8 Stage Multiple Files

Specify multiple paths:

```bash
git add file1.txt file2.txt file3.txt
```

Example:

```bash
git add src/main.c src/utils.c README.md
```

Check:

```bash
git status --short
```

Example:

```text
A  README.md
A  src/main.c
A  src/utils.c
```

---

# 27.9 Stage All Changes

A common command is:

```bash
git add .
```

This stages changes under the current directory.

Another common form is:

```bash
git add -A
```

`git add -A` stages additions, modifications and deletions throughout the repository.

Example:

```bash
git add -A
```

Then:

```bash
git status
```

Use `git add -A` when you intentionally want the entire repository's changes staged.

---

# 27.10 Stage New and Modified Files

Use:

```bash
git add -u
```

This stages modifications and deletions of already tracked files, but does not stage new untracked files.

Example:

```bash
git add -u
```

Useful when you want to stage changes to existing files without accidentally adding new files.

---

# 27.11 Stage Deleted Files

If a tracked file was deleted manually:

```bash
rm old-file.txt
```

Git reports:

```text
 D old-file.txt
```

Stage the deletion:

```bash
git add old-file.txt
```

or:

```bash
git add -u
```

Then:

```text
D  old-file.txt
```

The deletion is now staged.

---

# 27.12 Stage a Directory

Use:

```bash
git add src/
```

This stages eligible changes inside the directory.

Example:

```bash
git add src/
```

Then:

```bash
git status --short
```

Potential output:

```text
M  src/main.c
A  src/new.c
D  src/old.c
```

Ignored files remain ignored unless explicitly forced.

---

# 27.13 Stage Selected Changes

Interactive staging:

```bash
git add -i
```

This starts Git's interactive staging interface.

Example:

```text
           staged     unstaged path
1:        unchanged        +1/-1 src/main.c
2:        unchanged        +5/-0 src/utils.c
```

Interactive mode is useful when you want to selectively construct a commit.

---

# 27.14 Stage Part of a File

Use:

```bash
git add -p
```

or:

```bash
git add --patch
```

Git presents individual hunks.

Example:

```text
@@ -10,6 +10,10 @@
```

Git asks whether to stage the hunk:

```text
Stage this hunk [y,n,q,a,d,s,e,?]?
```

Common choices:

```text
y = stage this hunk
n = do not stage
q = quit
a = stage this and all remaining hunks
d = do not stage this or remaining hunks
s = split hunk
e = manually edit hunk
```

This is extremely useful for clean commits.

---

# 27.15 Unstage a File

Modern Git:

```bash
git restore --staged file.txt
```

Alternative:

```bash
git reset HEAD -- file.txt
```

The modern preferred form is:

```bash
git restore --staged file.txt
```

Example:

Before:

```text
M  src/main.c
```

After:

```text
 M src/main.c
```

The changes remain in the working tree but are removed from the index.

---

# 27.16 Unstage All Files

Use:

```bash
git restore --staged .
```

This removes staged changes from the index while keeping working tree modifications.

Another form:

```bash
git reset
```

For a modern workflow, prefer:

```bash
git restore --staged .
```

when your intent is specifically to unstage.

---

# 27.17 Discard Working Tree Changes

To restore a tracked file to the version in the index:

```bash
git restore file.txt
```

This discards unstaged modifications.

Example:

Before:

```text
 M file.txt
```

After:

```text
```

The working tree matches the index.

> **WARNING:** This discards local modifications to the file.

---

# 27.18 Restore a File

Restore from the index:

```bash
git restore file.txt
```

Restore from a specific commit:

```bash
git restore --source=HEAD~1 file.txt
```

Restore a directory:

```bash
git restore src/
```

Restore from another branch:

```bash
git restore --source=feature/login -- src/login.c
```

This changes the working tree and/or index depending on the options used.

---

# 27.19 Restore a Staged File

To restore the index version from `HEAD`:

```bash
git restore --staged file.txt
```

This unstages the file.

To restore both index and working tree:

```bash
git restore --source=HEAD --staged --worktree file.txt
```

Equivalent shorthand:

```bash
git restore --source=HEAD --staged --worktree -- file.txt
```

This resets the file to the committed `HEAD` version.

> **WARNING:** Any local modifications to the file are discarded.

---

# 27.20 Delete a Tracked File

Preferred Git command:

```bash
git rm file.txt
```

This:

1. Deletes the file from the working tree.
2. Removes it from the index.

Check:

```bash
git status --short
```

Output:

```text
D  file.txt
```

Then commit:

```bash
git commit -m "Remove obsolete file"
```

---

# 27.21 Remove a File from the Index

To keep the file locally but stop tracking it:

```bash
git rm --cached file.txt
```

Example:

```bash
git rm --cached .env
```

This is commonly used together with `.gitignore`.

Before:

```text
.env
```

is tracked.

After:

```text
.env
```

remains on disk but is no longer in the index.

---

# 27.22 Move or Rename a File

Use:

```bash
git mv old.txt new.txt
```

This moves/renames the file and stages the change.

Example:

```bash
git mv README.txt README.md
```

Status may show:

```text
R  README.txt -> README.md
```

Git's rename detection is based on similarity rather than a special immutable "rename object."

---

# 27.23 Detect Renames

Git can detect renames when comparing changes.

Example:

```bash
git diff --find-renames
```

Short form:

```bash
git diff -M
```

For commits:

```bash
git show -M
```

Git compares deleted and added files and may identify them as a rename if their content is sufficiently similar.

---

# 27.24 Copy a File

Git can stage a copy with:

```bash
git cp
```

There is no standard `git cp` command.

Instead:

```bash
cp original.txt copy.txt
git add original.txt copy.txt
```

Git can detect the copy during diff operations:

```bash
git diff --find-copies
```

or:

```bash
git diff -C
```

Copy detection is based on content similarity.

---

# 27.25 List Tracked Files

Use:

```bash
git ls-files
```

Example:

```text
.gitignore
README.md
src/main.c
src/utils.c
tests/test_main.c
```

This is the primary command for inspecting the contents of the index.

---

# 27.26 List Files in a Commit

Use:

```bash
git ls-tree -r --name-only HEAD
```

Example:

```text
.gitignore
README.md
src/main.c
src/utils.c
```

For another commit:

```bash
git ls-tree -r --name-only HEAD~1
```

For a branch:

```bash
git ls-tree -r --name-only main
```

---

# 27.27 List Staged Files

Use:

```bash
git diff --cached --name-only
```

or:

```bash
git diff --staged --name-only
```

Example:

```text
README.md
src/main.c
```

This shows paths changed in the index relative to `HEAD`.

To see staged statistics:

```bash
git diff --cached --stat
```

---

# 27.28 List Modified Files

Unstaged modifications:

```bash
git diff --name-only
```

Example:

```text
src/main.c
src/utils.c
```

Staged modifications:

```bash
git diff --cached --name-only
```

All changes relative to `HEAD`:

```bash
git diff HEAD --name-only
```

---

# 27.29 List Untracked Files

Use:

```bash
git ls-files --others --exclude-standard
```

Example:

```text
notes.txt
src/new.c
```

The `--exclude-standard` option respects:

* `.gitignore`
* `.git/info/exclude`
* Global excludes

---

# 27.30 List Ignored Files

Use:

```bash
git ls-files --others --ignored --exclude-standard
```

Example:

```text
.env
node_modules/package.json
dist/app.js
```

Another useful command:

```bash
git status --short --ignored
```

---

# 27.31 Find Files in the Index

Use shell filtering:

```bash
git ls-files | grep '\.c$'
```

Example:

```text
src/main.c
src/utils.c
tests/test_main.c
```

Find paths containing `test`:

```bash
git ls-files | grep test
```

On systems without `grep`, you can use other shell tools or Git pathspecs.

---

# 27.32 Check Whether a File Is Tracked

Use:

```bash
git ls-files --error-unmatch path/to/file
```

If tracked:

```text
path/to/file
```

If not tracked, Git exits with a non-zero status.

This is useful in scripts:

```bash
if git ls-files --error-unmatch -- file.txt >/dev/null 2>&1; then
    echo "Tracked"
else
    echo "Not tracked"
fi
```

---

# 27.33 File Names with Spaces

Quote paths containing spaces:

```bash
git add "My File.txt"
```

Similarly:

```bash
git restore -- "My File.txt"
```

```bash
git rm -- "My File.txt"
```

The `--` separates Git options from paths:

```bash
git add -- "My File.txt"
```

This is especially useful when filenames could be confused with command-line options.

---

# 27.34 File Tracking and `.gitignore`

The relationship can be summarized as:

```text
                 File
                  |
          +-------+-------+
          |               |
       Tracked         Untracked
          |               |
          |          +----+----+
          |          |         |
          |       Ignored   Not ignored
          |          |         |
          |       Hidden     git status
          |                  reports it
          |
      .gitignore
      does not
      untrack it
```

Example:

```gitignore
.env
```

If `.env` is untracked:

```text
.gitignore -> ignored
```

If `.env` is already tracked:

```text
.gitignore -> does not stop tracking
```

To stop tracking:

```bash
git rm --cached .env
```

---

# 27.35 Force Tracking of Ignored Files

If a file matches `.gitignore` but must be committed:

```bash
git add -f file.txt
```

Example:

```bash
git add -f config/example.local
```

Check:

```bash
git status --short
```

Potential output:

```text
A  config/example.local
```

Use this only when the file genuinely belongs in version control.

Never use `git add -f` to commit real secrets merely because they are ignored.

---

# 27.36 Remove All Files from the Index

A common command for rebuilding the index is:

```bash
git rm -r --cached .
```

This removes all files from the index while keeping them in the working tree.

Then:

```bash
git add .
```

This causes Git to rebuild the index according to the current ignore rules.

Typical use:

```bash
git rm -r --cached .
git add .
git status
```

This can be useful after substantially changing `.gitignore`.

> **WARNING:** This modifies the index extensively. Review `git status` before committing.

---

# 27.37 Refresh the Index

Git normally detects filesystem changes automatically.

To refresh the index:

```bash
git update-index --refresh
```

Possible output:

```text
src/main.c: needs update
```

This tells you that the index metadata needs to be refreshed because the working tree differs.

---

# 27.38 File Mode Changes

Git can track executable permission changes.

Check:

```bash
git diff --summary
```

Example:

```text
 mode change 100644 => 100755 script.sh
```

Inspect configuration:

```bash
git config core.fileMode
```

Set:

```bash
git config core.fileMode true
```

Disable local file mode detection:

```bash
git config core.fileMode false
```

Be cautious with this setting, particularly in shared Linux development environments.

---

# 27.39 End-of-Line Handling

Git can normalize line endings.

Inspect:

```bash
git config --get core.autocrlf
```

Typical values:

```text
true
input
false
```

Linux developers often use:

```bash
git config --global core.autocrlf input
```

A repository can define normalization using `.gitattributes`.

Example:

```gitattributes
* text=auto
```

Force LF for shell scripts:

```gitattributes
*.sh text eol=lf
```

Force CRLF for specific Windows files:

```gitattributes
*.bat text eol=crlf
```

Check attributes:

```bash
git check-attr text eol -- file.txt
```

---

# 27.40 Large Files

Normal Git stores file contents in repository objects.

Large binary files can make repositories unnecessarily large.

Examples:

```text
Video
Database dumps
Large archives
Machine-learning models
Large binaries
```

Git LFS is commonly used for such files.

Example:

```bash
git lfs track "*.zip"
```

Then:

```bash
git add .gitattributes
git add large-file.zip
```

See the dedicated Git LFS chapter for more details.

---

# 27.41 File Tracking in CI/CD

CI systems frequently need to determine whether specific files changed.

Example:

```bash
git diff --name-only HEAD~1 HEAD
```

Check whether a path changed:

```bash
git diff --name-only HEAD~1 HEAD | grep '^src/'
```

Check changed files between branches:

```bash
git diff --name-only origin/main...HEAD
```

This can be used to conditionally execute:

```text
Frontend tests
Backend tests
Documentation builds
Deployment jobs
Infrastructure validation
```

A CI pipeline can therefore use Git's tracked file information as an input to automation decisions.

---

# 27.42 File Tracking Troubleshooting

## Problem: Git says a file is untracked

Run:

```bash
git status --short
```

If you see:

```text
?? file.txt
```

stage it:

```bash
git add file.txt
```

---

## Problem: Git does not show the file

Check whether it is ignored:

```bash
git check-ignore -v -- file.txt
```

If ignored, inspect the matching rule.

---

## Problem: `.gitignore` does not work

Check whether the file is already tracked:

```bash
git ls-files -- file.txt
```

If tracked:

```bash
git rm --cached file.txt
```

Then commit the change.

---

## Problem: Git detects an unexpected deletion

Run:

```bash
git status
```

If:

```text
 D file.txt
```

the working tree file was deleted.

Restore it:

```bash
git restore file.txt
```

Or intentionally stage the deletion:

```bash
git add file.txt
```

---

## Problem: Git detects a rename

Check:

```bash
git status
```

Git may show:

```text
renamed: old.txt -> new.txt
```

Git's rename detection is similarity-based.

---

## Problem: Git detects only a delete and add

Compare:

```bash
git diff --find-renames
```

or:

```bash
git diff -M
```

Git may identify the pair as a rename if sufficient content is shared.

---

# 27.43 Complete File Tracking Command Reference

| Command                                              | Description                                      | Example                                              | Branch State Before and After command       | Output                     |
| ---------------------------------------------------- | ------------------------------------------------ | ---------------------------------------------------- | ------------------------------------------- | -------------------------- |
| `git status`                                         | Show working tree and index state                | `git status`                                         | Branch unchanged                            | Status report              |
| `git status --short`                                 | Compact status                                   | `git status --short`                                 | Branch unchanged                            | Short status codes         |
| `git status --short --ignored`                       | Include ignored files                            | `git status --short --ignored`                       | Branch unchanged                            | Status including `!!`      |
| `git add file`                                       | Stage a file                                     | `git add README.md`                                  | Branch unchanged; index changes             | Usually no output          |
| `git add file1 file2`                                | Stage multiple files                             | `git add a.txt b.txt`                                | Branch unchanged; index changes             | Usually no output          |
| `git add .`                                          | Stage changes below current directory            | `git add .`                                          | Branch unchanged; index changes             | Usually no output          |
| `git add -A`                                         | Stage all additions, modifications and deletions | `git add -A`                                         | Branch unchanged; index changes             | Usually no output          |
| `git add -u`                                         | Stage tracked modifications and deletions        | `git add -u`                                         | Branch unchanged; index changes             | Usually no output          |
| `git add -p`                                         | Interactively stage hunks                        | `git add -p`                                         | Branch unchanged; selected index changes    | Hunk prompts               |
| `git add -i`                                         | Interactive staging interface                    | `git add -i`                                         | Branch unchanged; selected index changes    | Interactive menu           |
| `git add -f file`                                    | Force-add ignored file                           | `git add -f file`                                    | Branch unchanged; index changes             | Usually no output          |
| `git restore file`                                   | Discard working tree changes                     | `git restore file`                                   | Branch unchanged; working tree changes      | Usually no output          |
| `git restore --staged file`                          | Unstage file                                     | `git restore --staged file`                          | Branch unchanged; index changes             | Usually no output          |
| `git restore --staged .`                             | Unstage all selected paths                       | `git restore --staged .`                             | Branch unchanged; index changes             | Usually no output          |
| `git restore --source=HEAD file`                     | Restore from `HEAD`                              | `git restore --source=HEAD file`                     | Branch unchanged; working tree changes      | Usually no output          |
| `git restore --source=branch file`                   | Restore from another branch                      | `git restore --source=main -- file`                  | Branch unchanged; working tree changes      | Usually no output          |
| `git rm file`                                        | Delete tracked file and stage deletion           | `git rm old.txt`                                     | Branch unchanged; index/worktree change     | `rm 'old.txt'`             |
| `git rm --cached file`                               | Stop tracking but keep local file                | `git rm --cached .env`                               | Branch unchanged; index changes             | `rm '.env'`                |
| `git rm -r --cached dir`                             | Stop tracking directory                          | `git rm -r --cached build/`                          | Branch unchanged; index changes             | Removed paths              |
| `git rm -r --cached .`                               | Remove all paths from index                      | `git rm -r --cached .`                               | Branch unchanged; index changes             | Removed paths              |
| `git mv old new`                                     | Move/rename tracked file                         | `git mv old.txt new.txt`                             | Branch unchanged; index/worktree change     | Usually no output          |
| `git ls-files`                                       | List tracked/index files                         | `git ls-files`                                       | Branch unchanged                            | Tracked paths              |
| `git ls-files --others --exclude-standard`           | List untracked non-ignored files                 | `git ls-files --others --exclude-standard`           | Branch unchanged                            | Untracked paths            |
| `git ls-files --others --ignored --exclude-standard` | List ignored files                               | `git ls-files --others --ignored --exclude-standard` | Branch unchanged                            | Ignored paths              |
| `git ls-files --error-unmatch file`                  | Verify a file is tracked                         | `git ls-files --error-unmatch README.md`             | Branch unchanged                            | Path or error              |
| `git ls-tree -r --name-only HEAD`                    | List files in `HEAD`                             | `git ls-tree -r --name-only HEAD`                    | Branch unchanged                            | Repository paths           |
| `git diff --name-only`                               | List unstaged changed files                      | `git diff --name-only`                               | Branch unchanged                            | Paths                      |
| `git diff --cached --name-only`                      | List staged changed files                        | `git diff --cached --name-only`                      | Branch unchanged                            | Paths                      |
| `git diff HEAD --name-only`                          | List all changes relative to `HEAD`              | `git diff HEAD --name-only`                          | Branch unchanged                            | Paths                      |
| `git diff --summary`                                 | Show file mode and rename summaries              | `git diff --summary`                                 | Branch unchanged                            | Summary                    |
| `git diff -M`                                        | Detect renames                                   | `git diff -M`                                        | Branch unchanged                            | Diff with rename detection |
| `git diff -C`                                        | Detect copies                                    | `git diff -C`                                        | Branch unchanged                            | Diff with copy detection   |
| `git check-ignore -v file`                           | Explain ignore rule                              | `git check-ignore -v .env`                           | Branch unchanged                            | Matching rule              |
| `git check-attr text eol -- file`                    | Show attributes                                  | `git check-attr text eol -- script.sh`               | Branch unchanged                            | Attribute values           |
| `git update-index --refresh`                         | Refresh index metadata                           | `git update-index --refresh`                         | Branch unchanged; index metadata may update | Refresh messages           |
| `git config core.fileMode`                           | Show file mode detection                         | `git config core.fileMode`                           | Branch unchanged                            | Configuration value        |
| `git diff --find-renames`                            | Detect renamed files                             | `git diff --find-renames`                            | Branch unchanged                            | Diff                       |
| `git diff --find-copies`                             | Detect copied files                              | `git diff --find-copies`                             | Branch unchanged                            | Diff                       |

---

# 27.44 High-Value Commands to Memorize

## Check what changed

```bash
git status
```

## Compact status

```bash
git status --short
```

## Stage one file

```bash
git add file
```

## Stage everything

```bash
git add -A
```

## Stage only tracked modifications/deletions

```bash
git add -u
```

## Stage individual hunks

```bash
git add -p
```

## Unstage a file

```bash
git restore --staged file
```

## Unstage everything

```bash
git restore --staged .
```

## Discard unstaged changes

```bash
git restore file
```

## Delete a tracked file

```bash
git rm file
```

## Stop tracking but keep locally

```bash
git rm --cached file
```

## Rename/move

```bash
git mv old new
```

## List tracked files

```bash
git ls-files
```

## List untracked files

```bash
git ls-files --others --exclude-standard
```

## List ignored files

```bash
git ls-files --others --ignored --exclude-standard
```

## List staged files

```bash
git diff --cached --name-only
```

## List unstaged files

```bash
git diff --name-only
```

## Check whether a file is tracked

```bash
git ls-files --error-unmatch file
```

## Explain why a file is ignored

```bash
git check-ignore -v -- file
```

## Force-add an ignored file

```bash
git add -f file
```

---

# File Tracking Mental Model

The most useful model to memorize is:

```text
                         WORKING TREE
                              |
                              | git add
                              v
                           INDEX
                              |
                              | git commit
                              v
                           COMMIT
```

For a modified file:

```text
HEAD
 |
 | previous committed version
 v
INDEX
 |
 | staged version
 v
WORKING TREE
 |
 | current edited version
 v
your filesystem
```

This explains why Git can show:

```text
MM file.txt
```

The first `M` means:

```text
INDEX differs from HEAD
```

The second `M` means:

```text
WORKING TREE differs from INDEX
```

Therefore:

```text
HEAD
  |
  +--> Index: version A
  |
  +--> Working tree: version B
```

The next commit contains the **index**, not every unsaved working-tree modification.

---

# File Tracking Best Practices

## 1. Check status before staging

```bash
git status
```

## 2. Prefer selective staging for meaningful commits

```bash
git add -p
```

## 3. Review the staged diff

```bash
git diff --cached
```

## 4. Verify staged paths

```bash
git diff --cached --name-only
```

## 5. Do not blindly use `git add .`

Understand what will be staged first.

## 6. Use `.gitignore` for files that should not be tracked

Examples:

```text
Dependencies
Build artifacts
Logs
Temporary files
Local configuration
Developer-specific files
```

## 7. Never treat `.gitignore` as a secret-removal mechanism

If a secret was committed, rotate/revoke it and handle the repository history appropriately.

## 8. Use `git rm --cached` when a tracked file should become ignored

```bash
git rm --cached file
```

## 9. Use `git add -p` for precise commits

This is especially useful when several unrelated changes exist in one working-tree file.

## 10. Review before committing

```bash
git status
git diff --cached
```

---

# Typical Developer Workflow

A disciplined file-tracking workflow:

```bash
git status
```

Edit files.

Then:

```bash
git status --short
```

Inspect changes:

```bash
git diff
```

Stage intentionally:

```bash
git add -p
```

Review staged changes:

```bash
git diff --cached
```

Check staged files:

```bash
git diff --cached --name-only
```

Commit:

```bash
git commit -m "Implement requested change"
```

Finally:

```bash
git status
```

Expected result:

```text
nothing to commit, working tree clean
```

---

# Final Summary

Git file tracking revolves around three primary states:

```text
WORKING TREE
     |
     | git add
     v
INDEX
     |
     | git commit
     v
REPOSITORY
```

The highest-value commands are:

```bash
git status
git status --short
git add file
git add -A
git add -p
git restore --staged file
git restore file
git rm file
git rm --cached file
git mv old new
git ls-files
git ls-files --others --exclude-standard
git diff --name-only
git diff --cached --name-only
git diff --cached
git check-ignore -v -- file
```

The critical distinction is:

```text
Working Tree != Index != HEAD
```

and:

```text
Tracked != Staged != Committed
```

Understanding these distinctions makes Git's behavior predictable and provides the foundation for advanced workflows involving commits, branches, rebasing, cherry-picking, recovery and CI/CD automation.

---

# Next Part

**Next file:** `28-sparse-checkout.md`

[Next: Sparse Checkout](28-sparse-checkout.md)
