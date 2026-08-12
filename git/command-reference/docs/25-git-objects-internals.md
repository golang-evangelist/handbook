# 25. Git Objects / Internals

Git is fundamentally a **content-addressable object database**.

Most high-level Git operations ultimately manipulate four primary object types:

```text
blob
tree
commit
tag
```

Understanding these objects is extremely valuable for:

* Advanced Git troubleshooting
* Repository recovery
* Understanding commits
* Understanding branches and tags
* Debugging corrupted repositories
* Understanding `HEAD`
* Understanding the index
* Inspecting packed objects
* Git internals
* Git tooling
* CI/CD automation
* Git hosting systems
* Repository maintenance

This chapter focuses on the internal structures and commands used to inspect them.

---

# Table of Contents

* [25.1 Git Object Model](#251-git-object-model)
* [25.2 Object Types](#252-object-types)
* [25.3 SHA-1 and SHA-256 Object IDs](#253-sha-1-and-sha-256-object-ids)
* [25.4 The `.git` Directory](#254-the-git-directory)
* [25.5 HEAD](#255-head)
* [25.6 References](#256-references)
* [25.7 Branch References](#257-branch-references)
* [25.8 Remote-Tracking References](#258-remote-tracking-references)
* [25.9 Tags](#259-tags)
* [25.10 Commits](#2510-commits)
* [25.11 Trees](#2511-trees)
* [25.12 Blobs](#2512-blobs)
* [25.13 Annotated Tags](#2513-annotated-tags)
* [25.14 Object Names](#2514-object-names)
* [25.15 git rev-parse](#2515-git-rev-parse)
* [25.16 git cat-file](#2516-git-cat-file)
* [25.17 git hash-object](#2517-git-hash-object)
* [25.18 git mktree](#2518-git-mktree)
* [25.19 git commit-tree](#2519-git-commit-tree)
* [25.20 git write-tree](#2520-git-write-tree)
* [25.21 git read-tree](#2521-git-read-tree)
* [25.22 git update-index](#2522-git-update-index)
* [25.23 git update-ref](#2523-git-update-ref)
* [25.24 git symbolic-ref](#2524-git-symbolic-ref)
* [25.25 git for-each-ref](#2525-git-for-each-ref)
* [25.26 git show-ref](#2526-git-show-ref)
* [25.27 git ls-tree](#2527-git-ls-tree)
* [25.28 git ls-files](#2528-git-ls-files)
* [25.29 Object Reachability](#2529-object-reachability)
* [25.30 Dangling and Unreachable Objects](#2530-dangling-and-unreachable-objects)
* [25.31 git fsck](#2531-git-fsck)
* [25.32 Loose Objects](#2532-loose-objects)
* [25.33 Packfiles](#2533-packfiles)
* [25.34 git count-objects](#2534-git-count-objects)
* [25.35 git verify-pack](#2535-git-verify-pack)
* [25.36 Commit Graph](#2536-commit-graph)
* [25.37 Index Internals](#2537-index-internals)
* [25.38 Git Object Database Workflow](#2538-git-object-database-workflow)
* [25.39 Low-Level Object Inspection](#2539-low-level-object-inspection)
* [25.40 Recovery Using Git Objects](#2540-recovery-using-git-objects)
* [25.41 Dangerous Plumbing Commands](#2541-dangerous-plumbing-commands)
* [25.42 High-Value Internals Commands](#2542-high-value-internals-commands)

---

# 25.1 Git Object Model

At the lowest level, Git stores objects rather than "files" and "commits" in the way a traditional database might.

The basic structure is:

```text
                    commit
                      |
          +-----------+-----------+
          |                       |
        tree                    parent
          |
    +-----+-----+
    |     |     |
  blob  tree   blob
```

A commit normally references:

* A tree
* Zero or more parent commits
* Author
* Committer
* Commit message

A tree references:

* Blobs
* Other trees

A blob contains file contents.

An annotated tag references another Git object and stores metadata.

---

# 25.2 Object Types

Git has four fundamental object types.

| Object   | Purpose             | Typical Contents                 |
| -------- | ------------------- | -------------------------------- |
| `blob`   | File contents       | Raw file data                    |
| `tree`   | Directory snapshot  | Names, modes, object IDs         |
| `commit` | Project history     | Tree, parents, metadata, message |
| `tag`    | Annotated reference | Object ID, tagger, message       |

Inspect an object type:

```bash
git cat-file -t <object>
```

Example:

```bash
git cat-file -t HEAD
```

Output:

```text
commit
```

---

# 25.3 SHA-1 and SHA-256 Object IDs

Git historically used SHA-1 object IDs.

A traditional Git object ID looks like:

```text
40 hexadecimal characters
```

Example:

```text
a3f5c8d7e6b4a2910f1234567890abcdef123456
```

Modern Git also supports SHA-256 repositories.

The important concept is:

```text
object content
      |
      v
object ID
      |
      v
content-addressable storage
```

An object ID identifies the contents of an object.

You can inspect the repository's object format with:

```bash
git rev-parse --show-object-format
```

Example:

```text
sha1
```

---

# 25.4 The `.git` Directory

A normal Git repository contains a `.git` directory.

Typical structure:

```text
.git/
├── HEAD
├── config
├── description
├── hooks/
├── index
├── info/
├── logs/
├── objects/
├── refs/
└── packed-refs
```

Depending on repository configuration and Git version, additional files and directories may exist.

Inspect:

```bash
ls -la .git
```

Inspect Git directory path:

```bash
git rev-parse --git-dir
```

Inspect object directory:

```bash
git rev-parse --git-path objects
```

Inspect refs:

```bash
git rev-parse --git-path refs
```

---

# 25.5 HEAD

`HEAD` identifies the currently checked-out state.

Normally:

```text
HEAD
 |
 v
refs/heads/main
 |
 v
commit
```

Inspect:

```bash
cat .git/HEAD
```

Typical output:

```text
ref: refs/heads/main
```

Or:

```bash
git symbolic-ref HEAD
```

Output:

```text
refs/heads/main
```

Short form:

```bash
git symbolic-ref --short HEAD
```

Output:

```text
main
```

In detached HEAD state:

```text
HEAD
 |
 v
a3f5c8...
```

Instead of:

```text
HEAD
 |
 v
refs/heads/main
```

---

# 25.6 References

A Git reference is a human-readable name pointing to an object.

Examples:

```text
refs/heads/main
refs/heads/develop
refs/tags/v1.0.0
refs/remotes/origin/main
```

Inspect references:

```bash
git show-ref
```

List all references:

```bash
git for-each-ref
```

Resolve a reference:

```bash
git rev-parse refs/heads/main
```

Example:

```text
a3f5c8d7e6b4...
```

The conceptual structure is:

```text
reference
    |
    v
object ID
    |
    v
Git object
```

---

# 25.7 Branch References

A branch is essentially a reference to a commit.

For example:

```text
refs/heads/main
        |
        v
    abc1234
        |
        v
      commit
```

Inspect:

```bash
git rev-parse refs/heads/main
```

Inspect branch reference:

```bash
git show-ref --heads
```

List branches:

```bash
git branch
```

Inspect branch references directly:

```bash
git for-each-ref refs/heads/
```

A branch does not contain an entire copy of the repository.

It points to a commit.

---

# 25.8 Remote-Tracking References

Remote-tracking branches use references such as:

```text
refs/remotes/origin/main
```

Inspect:

```bash
git rev-parse refs/remotes/origin/main
```

List:

```bash
git show-ref --heads --dereference
```

Or:

```bash
git for-each-ref refs/remotes/
```

Conceptually:

```text
origin/main
    |
    v
commit
```

Remote-tracking references are local references representing the last known state of a remote repository.

---

# 25.9 Tags

Lightweight tags can directly point to an object.

Example:

```text
refs/tags/v1.0.0
        |
        v
      commit
```

Inspect:

```bash
git rev-parse refs/tags/v1.0.0
```

List tags:

```bash
git tag
```

Inspect references:

```bash
git show-ref --tags
```

Annotated tags are Git objects themselves and are covered later in this chapter.

---

# 25.10 Commits

A commit object contains information similar to:

```text
tree <tree-object-id>
parent <parent-object-id>
author <author-information>
committer <committer-information>

commit message
```

Inspect:

```bash
git cat-file -p HEAD
```

Example:

```text
tree 1234567890abcdef...
parent abcdef1234567890...
author Developer <developer@example.com> 1234567890 +0000
committer Developer <developer@example.com> 1234567890 +0000

Implement authentication
```

A root commit has no `parent`.

A normal commit has one parent.

A merge commit can have multiple parents:

```text
commit
 |
 +-- parent 1
 |
 +-- parent 2
```

---

# 25.11 Trees

A tree represents a directory snapshot.

Inspect the root tree:

```bash
git ls-tree HEAD
```

Example:

```text
100644 blob a1b2c3...    README.md
040000 tree d4e5f6...    src
```

Interpretation:

```text
100644  = file mode
blob    = object type
a1b2c3  = object ID
README.md = filename
```

A directory is represented by another tree object.

Recursive inspection:

```bash
git ls-tree -r HEAD
```

Names only:

```bash
git ls-tree -r --name-only HEAD
```

---

# 25.12 Blobs

A blob stores file contents.

It does not normally store the filename.

The filename belongs to the tree.

Conceptually:

```text
tree
 |
 +---- README.md ----> blob
 |
 +---- src ----------> tree
                         |
                         +---- main.c ----> blob
```

Find the blob for a file:

```bash
git ls-tree HEAD -- README.md
```

Example:

```text
100644 blob a3f5c8...    README.md
```

Inspect blob:

```bash
git cat-file -p a3f5c8...
```

Output:

```text
# Project

Project documentation.
```

---

# 25.13 Annotated Tags

An annotated tag is a Git object.

Inspect tag type:

```bash
git cat-file -t v1.0.0
```

Output:

```text
tag
```

Inspect tag object:

```bash
git cat-file -p v1.0.0
```

Example:

```text
object abc123...
type commit
tag v1.0.0
tagger Developer <developer@example.com> 1234567890 +0000

Release v1.0.0
```

The structure is:

```text
tag reference
      |
      v
    tag object
      |
      v
    commit
```

This differs from a lightweight tag.

---

# 25.14 Object Names

Git accepts many ways to identify objects.

Examples:

```bash
HEAD
HEAD~1
HEAD^
main
origin/main
v1.0.0
abc1234
abc123456789...
```

Useful syntax includes:

```text
HEAD
HEAD~1
HEAD~5
HEAD^
HEAD^2
main
main~3
main:path/to/file
HEAD:path/to/file
```

Examples:

```bash
git rev-parse HEAD~1
```

```bash
git rev-parse HEAD^
```

```bash
git rev-parse main~5
```

Inspect a file from a commit:

```bash
git show HEAD:README.md
```

This is called **revision/path syntax**.

---

# 25.15 git rev-parse

`git rev-parse` resolves revision expressions.

Current commit:

```bash
git rev-parse HEAD
```

Parent:

```bash
git rev-parse HEAD^
```

Grandparent:

```bash
git rev-parse HEAD~2
```

Branch:

```bash
git rev-parse main
```

Remote branch:

```bash
git rev-parse origin/main
```

Short object ID:

```bash
git rev-parse --short HEAD
```

Verify an object:

```bash
git rev-parse --verify HEAD
```

Verify a branch:

```bash
git rev-parse --verify refs/heads/main
```

Show object format:

```bash
git rev-parse --show-object-format
```

---

# 25.16 git cat-file

`git cat-file` is one of the most important Git plumbing commands.

Determine type:

```bash
git cat-file -t HEAD
```

Determine size:

```bash
git cat-file -s HEAD
```

Pretty-print object:

```bash
git cat-file -p HEAD
```

Inspect a tree:

```bash
git cat-file -p HEAD^{tree}
```

Inspect a parent:

```bash
git cat-file -p HEAD^
```

Inspect a blob:

```bash
git cat-file -p <blob-id>
```

---

| Command                      | Description           | Example                      | Branch State Before and After command | Output             |
| ---------------------------- | --------------------- | ---------------------------- | ------------------------------------- | ------------------ |
| `git cat-file -t`            | Show object type      | `git cat-file -t HEAD`       | Unchanged                             | `commit`           |
| `git cat-file -s`            | Show object size      | `git cat-file -s HEAD`       | Unchanged                             | Size in bytes      |
| `git cat-file -p`            | Pretty-print object   | `git cat-file -p HEAD`       | Unchanged                             | Object contents    |
| `git cat-file --batch-check` | Batch object metadata | `git cat-file --batch-check` | Unchanged                             | Object information |
| `git cat-file --batch`       | Batch object contents | `git cat-file --batch`       | Unchanged                             | Object data        |

---

# 25.17 git hash-object

Calculate an object ID from file contents.

```bash
git hash-object file.txt
```

This normally calculates the blob ID without storing it.

Store the object:

```bash
git hash-object -w file.txt
```

Read from standard input:

```bash
printf 'hello\n' | git hash-object --stdin
```

Store standard input:

```bash
printf 'hello\n' | git hash-object -w --stdin
```

Check an existing file:

```bash
git hash-object README.md
```

The important distinction is:

```text
git hash-object file
    |
    +--> calculate object ID

git hash-object -w file
    |
    +--> calculate object ID
    |
    +--> write object to database
```

---

# 25.18 git mktree

`git mktree` creates a tree object from tree-format input.

Example input:

```text
100644 blob a3f5c8... README.md
100644 blob b4e6d9... main.c
```

Command:

```bash
git mktree < tree.txt
```

Output:

```text
<tree-object-id>
```

This is a plumbing command.

It is mainly useful for:

* Git internals
* Repository tooling
* Object manipulation
* Advanced automation
* Git experimentation

It should not normally be used during everyday development.

---

# 25.19 git commit-tree

`git commit-tree` creates a commit object from a tree.

Basic form:

```bash
git commit-tree <tree-id>
```

With a parent:

```bash
git commit-tree <tree-id> -p <parent-id>
```

With a commit message:

```bash
echo "Commit message" | git commit-tree <tree-id> -p <parent-id>
```

Output:

```text
<new-commit-id>
```

Conceptually:

```text
tree
 |
 v
commit-tree
 |
 +---- parent
 |
 v
new commit
```

This command directly creates commit objects.

It does **not** automatically move a branch reference.

---

# 25.20 git write-tree

`git write-tree` creates a tree object from the current index.

Command:

```bash
git write-tree
```

Output:

```text
<tree-object-id>
```

Conceptually:

```text
working tree
      |
      v
    index
      |
      v
 git write-tree
      |
      v
    tree
```

This is a fundamental operation behind commit creation.

---

# 25.21 git read-tree

`git read-tree` reads tree information into the index.

Inspect a tree into the index:

```bash
git read-tree <tree-id>
```

Read `HEAD` tree:

```bash
git read-tree HEAD
```

This is a low-level command.

It can affect the index and therefore should not be used casually in an active working tree.

---

# 25.22 git update-index

`git update-index` manipulates the Git index directly.

Refresh index:

```bash
git update-index --refresh
```

Add a file:

```bash
git update-index --add file.txt
```

Remove a file:

```bash
git update-index --remove file.txt
```

Mark file as assume-unchanged:

```bash
git update-index --assume-unchanged file.txt
```

Clear assume-unchanged:

```bash
git update-index --no-assume-unchanged file.txt
```

Mark skip-worktree:

```bash
git update-index --skip-worktree file.txt
```

Clear skip-worktree:

```bash
git update-index --no-skip-worktree file.txt
```

These flags are advanced features and can cause confusing behavior if misused.

---

# 25.23 git update-ref

`git update-ref` updates references.

Show current branch reference:

```bash
git rev-parse refs/heads/main
```

Update a branch reference:

```bash
git update-ref refs/heads/main <commit-id>
```

Delete a reference:

```bash
git update-ref -d refs/heads/old-branch
```

Safely update only if the old value matches:

```bash
git update-ref refs/heads/main <new-id> <old-id>
```

This is powerful because branch names are references.

Conceptually:

```text
refs/heads/main
       |
       v
   commit A
```

can be changed to:

```text
refs/heads/main
       |
       v
   commit B
```

without creating a new commit.

---

# 25.24 git symbolic-ref

Show symbolic reference:

```bash
git symbolic-ref HEAD
```

Output:

```text
refs/heads/main
```

Change symbolic reference:

```bash
git symbolic-ref HEAD refs/heads/develop
```

This is an advanced plumbing operation.

Normally use:

```bash
git switch develop
```

instead of manipulating `HEAD` directly.

---

# 25.25 git for-each-ref

List references:

```bash
git for-each-ref
```

List branches:

```bash
git for-each-ref refs/heads/
```

List remote branches:

```bash
git for-each-ref refs/remotes/
```

List tags:

```bash
git for-each-ref refs/tags/
```

Show formatted output:

```bash
git for-each-ref --format='%(refname) %(objectname)' refs/heads/
```

Show branch and commit:

```bash
git for-each-ref --format='%(refname:short) %(objectname:short)' refs/heads/
```

This command is extremely useful for scripts and automation.

---

# 25.26 git show-ref

Show references:

```bash
git show-ref
```

Branches:

```bash
git show-ref --heads
```

Tags:

```bash
git show-ref --tags
```

Verify:

```bash
git show-ref --verify refs/heads/main
```

Output resembles:

```text
<object-id> refs/heads/main
```

---

# 25.27 git ls-tree

Inspect tree contents:

```bash
git ls-tree HEAD
```

Recursive:

```bash
git ls-tree -r HEAD
```

Names only:

```bash
git ls-tree -r --name-only HEAD
```

Object IDs:

```bash
git ls-tree --object-only HEAD
```

Inspect a directory:

```bash
git ls-tree HEAD src/
```

This command is one of the best tools for understanding how Git represents directories.

---

# 25.28 git ls-files

Inspect the index:

```bash
git ls-files
```

Show staging information:

```bash
git ls-files --stage
```

Example:

```text
100644 a3f5c8... 0 README.md
```

Show deleted files:

```bash
git ls-files --deleted
```

Show modified files:

```bash
git ls-files --modified
```

Show untracked files:

```bash
git ls-files --others
```

Show ignored files:

```bash
git ls-files --ignored --exclude-standard
```

---

# 25.29 Object Reachability

Git objects form a graph.

For example:

```text
refs/heads/main
       |
       v
    commit C
       |
       v
    commit B
       |
       v
    commit A
```

If a reference points to an object, the object is reachable.

Reachability extends through object relationships:

```text
branch
  |
  v
commit
  |
  +--> tree
  |     |
  |     +--> blob
  |     +--> tree
  |
  +--> parent commit
```

Therefore, a branch indirectly keeps a large number of objects reachable.

When no references point to an object and it cannot be reached through another reachable object, it may eventually become unreachable.

---

# 25.30 Dangling and Unreachable Objects

A dangling commit can occur after operations such as:

```bash
git reset
git rebase
git commit --amend
```

The old commit may still exist in the object database.

Find unreachable objects:

```bash
git fsck --full --unreachable
```

Find dangling objects:

```bash
git fsck --full --dangling
```

Example:

```text
dangling commit abc123...
```

That commit may still be recoverable.

Inspect it:

```bash
git show abc123...
```

Create a recovery branch:

```bash
git switch -c recovery abc123...
```

---

# 25.31 git fsck

Run full object database verification:

```bash
git fsck --full
```

Connectivity-only check:

```bash
git fsck --connectivity-only
```

Show unreachable objects:

```bash
git fsck --unreachable
```

Show dangling objects:

```bash
git fsck --dangling
```

Use all together:

```bash
git fsck --full --unreachable --dangling
```

Typical output:

```text
dangling commit abc123...
dangling blob def456...
```

No output often means no problems were found under the requested checks.

---

# 25.32 Loose Objects

Loose objects are individual files stored under:

```text
.git/objects/
```

A traditional object ID such as:

```text
a3f5c8d7e6b4...
```

can correspond to:

```text
.git/objects/a3/f5c8d7e6b4...
```

Inspect object directory:

```bash
find .git/objects -type f
```

Count loose objects:

```bash
git count-objects
```

Detailed statistics:

```bash
git count-objects -vH
```

Git may later pack these objects into packfiles.

---

# 25.33 Packfiles

Packfiles compress multiple Git objects into a more efficient storage format.

Typical files:

```text
.git/objects/pack/
├── pack-<hash>.pack
└── pack-<hash>.idx
```

Inspect:

```bash
ls -lh .git/objects/pack/
```

Git can automatically create packs through maintenance and garbage collection operations.

Packfiles are especially important in large repositories.

---

# 25.34 git count-objects

Basic:

```bash
git count-objects
```

Detailed:

```bash
git count-objects -v
```

Human-readable:

```bash
git count-objects -vH
```

Example:

```text
count: 12
size: 48
in-pack: 15000
packs: 3
size-pack: 10240
prune-packable: 0
garbage: 0
size-garbage: 0
```

This is useful when diagnosing repository size and object storage.

---

# 25.35 git verify-pack

Verify a pack index:

```bash
git verify-pack -v .git/objects/pack/pack-XXXX.idx
```

The output can contain:

```text
<object-id> <type> <size> <size-in-pack> <offset>
```

For delta-compressed objects, additional information may be displayed.

This command is useful for advanced packfile analysis.

---

# 25.36 Commit Graph

Git can maintain a commit-graph to accelerate history traversal.

Inspect:

```bash
git commit-graph verify
```

Write/update:

```bash
git commit-graph write
```

Write with reachable commits:

```bash
git commit-graph write --reachable
```

Verify with progress information:

```bash
git commit-graph verify --progress
```

The commit graph is an optimization.

It does not replace commit objects.

Conceptually:

```text
Commit objects
      |
      v
Commit graph
      |
      v
Faster history traversal
```

---

# 25.37 Index Internals

The Git index is sometimes called the **staging area**.

It stores information about the next tree Git can create.

Conceptually:

```text
Working Tree
     |
     | git add
     v
   Index
     |
     | git write-tree
     v
   Tree
     |
     | git commit-tree
     v
  Commit
```

Inspect index entries:

```bash
git ls-files --stage
```

Example:

```text
100644 a3f5c8... 0 README.md
```

The index contains:

* File path
* Object ID
* File mode
* Stage number
* Metadata

For conflict states, multiple index stages may exist.

---

# 25.38 Git Object Database Workflow

A simplified commit workflow is:

```text
1. Modify file
        |
        v
2. Working tree
        |
        | git add
        v
3. Index
        |
        | git write-tree
        v
4. Tree object
        |
        | git commit-tree
        v
5. Commit object
        |
        | update reference
        v
6. Branch
```

High-level:

```bash
git add file.txt
git commit -m "Update file"
```

Low-level conceptual equivalent:

```text
update index
    |
    v
write tree
    |
    v
create commit
    |
    v
move branch reference
```

This model explains much of Git's architecture.

---

# 25.39 Low-Level Object Inspection

Inspect current commit:

```bash
git cat-file -p HEAD
```

Inspect current tree:

```bash
git cat-file -p HEAD^{tree}
```

List tree:

```bash
git ls-tree HEAD
```

Inspect parent:

```bash
git cat-file -p HEAD^
```

Inspect file:

```bash
git show HEAD:path/to/file
```

Find file object:

```bash
git ls-tree HEAD -- path/to/file
```

Inspect blob:

```bash
git cat-file -p <blob-id>
```

Determine type:

```bash
git cat-file -t <object-id>
```

Determine size:

```bash
git cat-file -s <object-id>
```

---

# 25.40 Recovery Using Git Objects

Suppose a branch was accidentally deleted.

First:

```bash
git reflog --all
```

Find the old commit:

```bash
git show <commit-id>
```

Inspect:

```bash
git cat-file -t <commit-id>
```

If it is a commit:

```text
commit
```

Recover it:

```bash
git switch -c recovered <commit-id>
```

Alternatively, create a reference directly:

```bash
git update-ref refs/heads/recovered <commit-id>
```

The high-level approach is preferred:

```bash
git switch -c recovered <commit-id>
```

---

# 25.41 Dangerous Plumbing Commands

The following commands can directly manipulate Git's internal state:

```bash
git update-ref
git update-index
git read-tree
git write-tree
git commit-tree
git mktree
git symbolic-ref
```

These commands are powerful but should not be used casually.

For everyday development prefer:

```bash
git switch
git restore
git add
git commit
git merge
git rebase
```

Use plumbing commands when:

* Building Git tooling
* Writing advanced automation
* Recovering repositories
* Studying Git internals
* Implementing custom workflows
* Debugging Git behavior

---

# 25.42 High-Value Internals Commands

| Command                              | Description                 | Example                               | Branch State Before and After command | Output                   |
| ------------------------------------ | --------------------------- | ------------------------------------- | ------------------------------------- | ------------------------ |
| `git rev-parse HEAD`                 | Resolve current commit      | `git rev-parse HEAD`                  | Unchanged                             | Object ID                |
| `git rev-parse HEAD^`                | Resolve parent              | `git rev-parse HEAD^`                 | Unchanged                             | Parent object ID         |
| `git rev-parse HEAD~3`               | Resolve ancestor            | `git rev-parse HEAD~3`                | Unchanged                             | Ancestor object ID       |
| `git rev-parse --show-object-format` | Show object format          | `git rev-parse --show-object-format`  | Unchanged                             | `sha1` or `sha256`       |
| `git cat-file -t HEAD`               | Show object type            | `git cat-file -t HEAD`                | Unchanged                             | `commit`                 |
| `git cat-file -s HEAD`               | Show object size            | `git cat-file -s HEAD`                | Unchanged                             | Byte count               |
| `git cat-file -p HEAD`               | Inspect commit              | `git cat-file -p HEAD`                | Unchanged                             | Commit data              |
| `git hash-object file.txt`           | Calculate blob ID           | `git hash-object file.txt`            | Unchanged                             | Object ID                |
| `git hash-object -w file.txt`        | Store blob                  | `git hash-object -w file.txt`         | Unchanged                             | Object ID                |
| `git write-tree`                     | Create tree from index      | `git write-tree`                      | Unchanged                             | Tree ID                  |
| `git read-tree HEAD`                 | Load tree into index        | `git read-tree HEAD`                  | Index changes; branch unchanged       | No output or diagnostics |
| `git commit-tree`                    | Create commit object        | `git commit-tree <tree> -p <parent>`  | Branch unchanged                      | Commit ID                |
| `git update-ref`                     | Move/delete reference       | `git update-ref refs/heads/main <id>` | **Can change branch reference**       | Usually no output        |
| `git symbolic-ref`                   | Inspect/change symbolic ref | `git symbolic-ref HEAD`               | Usually unchanged when reading        | Reference                |
| `git for-each-ref`                   | Enumerate refs              | `git for-each-ref`                    | Unchanged                             | References               |
| `git show-ref`                       | Show refs                   | `git show-ref`                        | Unchanged                             | References               |
| `git ls-tree`                        | Inspect tree                | `git ls-tree HEAD`                    | Unchanged                             | Tree entries             |
| `git ls-files --stage`               | Inspect index               | `git ls-files --stage`                | Unchanged                             | Index entries            |
| `git fsck --full`                    | Verify object database      | `git fsck --full`                     | Unchanged                             | Integrity information    |
| `git count-objects -vH`              | Inspect object storage      | `git count-objects -vH`               | Unchanged                             | Object statistics        |
| `git verify-pack -v`                 | Inspect packfile            | `git verify-pack -v pack.idx`         | Unchanged                             | Pack/object data         |
| `git commit-graph verify`            | Verify commit graph         | `git commit-graph verify`             | Unchanged                             | Verification output      |

---

# Git Object Reference Diagram

```text
                         HEAD
                          |
                          v
                   refs/heads/main
                          |
                          v
                       Commit
                    /          \
                   /            \
                Tree           Parent
               /   \
              /     \
           Blob     Tree
                    / \
                   /   \
                Blob   Blob
```

A commit therefore does not directly contain every file.

Instead:

```text
Commit
  |
  +--> Tree
         |
         +--> Blob
         |
         +--> Tree
                |
                +--> Blob
```

---

# Branches Are References

A branch is not a separate copy of the repository.

For example:

```text
refs/heads/main
       |
       v
   commit C
       |
       v
   commit B
       |
       v
   commit A
```

Creating another branch:

```bash
git branch feature
```

creates another reference:

```text
refs/heads/main     ----> C
refs/heads/feature  ----> C
```

Both branches initially point to the same commit.

After another commit on `feature`:

```text
refs/heads/main     ----> C
                         /
                        /
refs/heads/feature ----> D
```

This is why creating branches is cheap.

---

# Tags Are References or Objects

Lightweight tag:

```text
refs/tags/v1.0
       |
       v
    commit
```

Annotated tag:

```text
refs/tags/v1.0
       |
       v
    tag object
       |
       v
    commit
```

Inspect the difference:

```bash
git cat-file -t v1.0
```

Possible outputs:

```text
commit
```

or:

```text
tag
```

---

# Commit Graph Example

Suppose history is:

```text
A---B---C---D    main
     \
      E---F      feature
```

References:

```text
refs/heads/main    -> D
refs/heads/feature -> F
```

Commit `D` contains:

```text
tree <tree-D>
parent C
```

Commit `F` contains:

```text
tree <tree-F>
parent E
```

And:

```text
E -> B
F -> E
```

The common ancestor is:

```text
B
```

Find it:

```bash
git merge-base main feature
```

---

# Object Inspection Cheat Sheet

```bash
# Current commit
git rev-parse HEAD

# Parent commit
git rev-parse HEAD^

# Grandparent
git rev-parse HEAD~2

# Current branch
git symbolic-ref --short HEAD

# Object type
git cat-file -t HEAD

# Object size
git cat-file -s HEAD

# Object contents
git cat-file -p HEAD

# Commit tree
git rev-parse HEAD^{tree}

# Inspect tree
git ls-tree HEAD

# Recursive tree
git ls-tree -r HEAD

# Find a file in a tree
git ls-tree HEAD -- README.md

# Show file contents from commit
git show HEAD:README.md

# Calculate blob ID
git hash-object README.md

# Store blob object
git hash-object -w README.md

# Inspect index
git ls-files --stage

# Create tree from index
git write-tree

# Inspect references
git show-ref

# Enumerate references
git for-each-ref

# Inspect object database
git fsck --full

# Find unreachable objects
git fsck --full --unreachable

# Object statistics
git count-objects -vH

# Verify pack
git verify-pack -v .git/objects/pack/pack-XXXX.idx

# Verify commit graph
git commit-graph verify

# Show object format
git rev-parse --show-object-format
```

---

# Important Mental Model

The most useful Git internals model to memorize is:

```text
WORKING TREE
     |
     | git add
     v
   INDEX
     |
     | git write-tree
     v
    TREE
     |
     | git commit-tree
     v
   COMMIT
     |
     | reference points to commit
     v
  BRANCH
```

And:

```text
COMMIT
  |
  +--> TREE
  |      |
  |      +--> BLOB
  |      |
  |      +--> TREE
  |             |
  |             +--> BLOB
  |
  +--> PARENT COMMIT
```

Finally:

```text
BRANCH
  |
  v
COMMIT
  |
  v
TREE
  |
  +--> BLOB
  +--> TREE
         |
         +--> BLOB
```

Once this model is understood, many Git commands become much easier to reason about.

---

# Next Part

**Next file:** `26-ignoring-files.md`

[Next: Ignoring Files](26-ignoring-files.md)
