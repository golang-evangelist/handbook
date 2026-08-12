# 23. Repository Maintenance

Repository maintenance consists of operations that keep a Git repository:

* Efficient
* Compact
* Consistent
* Fast
* Diagnosable
* Safe to archive
* Safe to transfer
* Healthy over time

Git normally performs maintenance automatically in the background in many environments, but developers and DevOps engineers should understand the commands used to inspect and maintain repositories manually.

---

# Table of Contents

* [23.1 Maintenance Overview](#231-maintenance-overview)
* [23.2 git maintenance](#232-git-maintenance)
* [23.3 git maintenance start](#233-git-maintenance-start)
* [23.4 git maintenance stop](#234-git-maintenance-stop)
* [23.5 git maintenance run](#235-git-maintenance-run)
* [23.6 git gc](#236-git-gc)
* [23.7 git gc --auto](#237-git-gc---auto)
* [23.8 git prune](#238-git-prune)
* [23.9 git repack](#239-git-repack)
* [23.10 git pack-refs](#2310-git-pack-refs)
* [23.11 git reflog expire](#2311-git-reflog-expire)
* [23.12 git rerere gc](#2312-git-rerere-gc)
* [23.13 git count-objects](#2313-git-count-objects)
* [23.14 git fsck](#2314-git-fsck)
* [23.15 git verify-pack](#2315-git-verify-pack)
* [23.16 git multi-pack-index](#2316-git-multi-pack-index)
* [23.17 git commit-graph](#2317-git-commit-graph)
* [23.18 git repack -Ad](#2318-git-repack--ad)
* [23.19 Maintenance After Large History Changes](#2319-maintenance-after-large-history-changes)
* [23.20 Maintenance After Removing Large Files](#2320-maintenance-after-removing-large-files)
* [23.21 Maintenance of Bare Repositories](#2321-maintenance-of-bare-repositories)
* [23.22 Maintenance in CI/CD](#2322-maintenance-in-cicd)
* [23.23 Maintenance in Large Repositories](#2323-maintenance-in-large-repositories)
* [23.24 Maintenance Diagnostics](#2324-maintenance-diagnostics)
* [23.25 Maintenance Safety](#2325-maintenance-safety)
* [23.26 Recommended Maintenance Workflows](#2326-recommended-maintenance-workflows)
* [23.27 High-Value Maintenance Commands](#2327-high-value-maintenance-commands)
* [23.28 Maintenance Best Practices](#2328-maintenance-best-practices)

---

# 23.1 Maintenance Overview

Git stores repository data as objects:

```text
Blob
Tree
Commit
Tag
```

Objects are initially written as loose objects and may later be stored in packfiles.

Conceptually:

```text
Working Tree
     |
     v
Git Objects
     |
     +--> Loose Objects
     |
     +--> Packfiles
     |
     +--> Indexes
     |
     +--> Commit-Graph
```

Maintenance optimizes these structures.

Important maintenance commands include:

```bash
git maintenance
git gc
git prune
git repack
git pack-refs
git reflog expire
git count-objects
git fsck
git multi-pack-index
git commit-graph
```

---

# 23.2 git maintenance

The `git maintenance` command manages scheduled repository maintenance.

Show help:

```bash
git maintenance -h
```

Run maintenance:

```bash
git maintenance run
```

List configured schedules:

```bash
git maintenance start
```

The exact scheduling mechanism depends on the operating system and Git installation.

---

| Command               | Description                   | Example               | Branch State Before and After command | Output             |
| --------------------- | ----------------------------- | --------------------- | ------------------------------------- | ------------------ |
| `git maintenance`     | Manage repository maintenance | `git maintenance`     | Unchanged                             | Help/usage         |
| `git maintenance -h`  | Show maintenance help         | `git maintenance -h`  | Unchanged                             | Command help       |
| `git maintenance run` | Run maintenance tasks         | `git maintenance run` | Unchanged                             | Maintenance output |

---

# 23.3 git maintenance start

Enable scheduled background maintenance:

```bash
git maintenance start
```

This configures Git's maintenance scheduling mechanism for the repository.

Check configuration:

```bash
git config --get maintenance.auto
```

List maintenance configuration:

```bash
git config --get-regexp '^maintenance\.'
```

Example:

```text
maintenance.auto
maintenance.strategy
```

The exact output depends on the Git version and configured strategy.

---

# 23.4 git maintenance stop

Disable scheduled maintenance:

```bash
git maintenance stop
```

This removes the configured scheduled maintenance for the repository.

Verify:

```bash
git config --get-regexp '^maintenance\.'
```

Be aware that stopping scheduled maintenance does not delete repository objects or undo previous maintenance.

---

# 23.5 git maintenance run

Run maintenance manually:

```bash
git maintenance run
```

Run a specific task:

```bash
git maintenance run --task=gc
```

Other maintenance tasks can include:

```text
commit-graph
prefetch
loose-objects
incremental-repack
pack-refs
```

List supported tasks for your installed Git version:

```bash
git maintenance run -h
```

---

| Command                         | Description                | Example                         | Branch State Before and After command | Output             |
| ------------------------------- | -------------------------- | ------------------------------- | ------------------------------------- | ------------------ |
| `git maintenance run`           | Run configured maintenance | `git maintenance run`           | Unchanged                             | Maintenance output |
| `git maintenance run --task=gc` | Run GC maintenance task    | `git maintenance run --task=gc` | Unchanged                             | GC output          |
| `git maintenance run -h`        | Show available tasks       | `git maintenance run -h`        | Unchanged                             | Help               |

---

# 23.6 git gc

`git gc` performs repository housekeeping.

Run:

```bash
git gc
```

It may perform operations such as:

* Packing loose objects
* Repacking objects
* Pruning unreachable objects according to applicable expiration rules
* Updating repository metadata

Typical output may be empty if Git completes successfully.

Inspect repository size before and after:

```bash
git count-objects -vH
```

---

| Command          | Description                           | Example          | Branch State Before and After command | Output                   |
| ---------------- | ------------------------------------- | ---------------- | ------------------------------------- | ------------------------ |
| `git gc`         | Perform repository garbage collection | `git gc`         | Branch refs unchanged                 | Usually little/no output |
| `git gc --quiet` | Run GC quietly                        | `git gc --quiet` | Branch refs unchanged                 | Minimal output           |
| `git gc --auto`  | Run automatic GC if necessary         | `git gc --auto`  | Branch refs unchanged                 | Usually no output        |

---

# 23.7 git gc --auto

Git can automatically perform maintenance when repository thresholds indicate it is useful.

Run:

```bash
git gc --auto
```

This allows Git to decide whether maintenance is necessary.

It is safer for routine use than manually forcing aggressive repacking.

Many normal Git commands can trigger automatic maintenance behavior.

---

# 23.8 git prune

`git prune` removes unreachable loose objects.

Preview candidates without deleting:

```bash
git prune --dry-run
```

Run:

```bash
git prune
```

This command should be used carefully.

An object may be unreachable from current refs but still be useful for recovery through reflogs or other references.

Inspect repository state first:

```bash
git fsck --full
```

and:

```bash
git reflog
```

---

| Command               | Description                            | Example               | Branch State Before and After command | Output                 |
| --------------------- | -------------------------------------- | --------------------- | ------------------------------------- | ---------------------- |
| `git prune --dry-run` | Show objects that could be pruned      | `git prune --dry-run` | Unchanged                             | Object IDs             |
| `git prune`           | Remove eligible unreachable objects    | `git prune`           | Branch refs unchanged                 | Usually object IDs     |
| `git fsck --full`     | Inspect repository object connectivity | `git fsck --full`     | Unchanged                             | Repository diagnostics |

---

# 23.9 git repack

`git repack` creates packfiles from repository objects.

Basic command:

```bash
git repack
```

Inspect resulting packs:

```bash
ls .git/objects/pack/
```

A common maintenance operation is:

```bash
git repack -d
```

The `-d` option removes redundant packs after successful repacking where appropriate.

For more aggressive maintenance:

```bash
git repack -Ad
```

This command should be used carefully on large repositories.

---

# 23.10 git pack-refs

Git can store refs in a packed format.

Run:

```bash
git pack-refs --all
```

This can reduce the number of loose reference files.

Prune obsolete packed refs:

```bash
git pack-refs --all --prune
```

Important:

Do not manually edit:

```text
.git/packed-refs
```

unless you fully understand Git's reference storage mechanisms.

---

| Command                       | Description                           | Example                       | Branch State Before and After command | Output            |
| ----------------------------- | ------------------------------------- | ----------------------------- | ------------------------------------- | ----------------- |
| `git pack-refs --all`         | Pack refs                             | `git pack-refs --all`         | Branch names/targets unchanged        | Usually no output |
| `git pack-refs --all --prune` | Pack refs and remove obsolete entries | `git pack-refs --all --prune` | Branch refs unchanged                 | Usually no output |

---

# 23.11 git reflog expire

Reflogs record updates to local references.

Inspect expiration configuration:

```bash
git config --get gc.reflogExpire
```

For unreachable objects:

```bash
git config --get gc.reflogExpireUnreachable
```

Manually expire reflog entries:

```bash
git reflog expire --all
```

This is an advanced maintenance command.

Do not expire reflogs simply to make a repository smaller unless you understand the recovery consequences.

---

# 23.12 git rerere gc

`rerere` means:

```text
reuse recorded resolution
```

Git can remember how merge conflicts were resolved.

Run cleanup:

```bash
git rerere gc
```

Inspect rerere state:

```bash
git rerere status
```

Show recorded resolutions:

```bash
git rerere diff
```

Rerere data is generally stored under:

```text
.git/rr-cache/
```

---

| Command             | Description                                   | Example             | Branch State Before and After command | Output            |
| ------------------- | --------------------------------------------- | ------------------- | ------------------------------------- | ----------------- |
| `git rerere status` | Show paths with recorded conflict resolutions | `git rerere status` | Unchanged                             | File paths        |
| `git rerere diff`   | Show recorded resolution                      | `git rerere diff`   | Unchanged                             | Diff              |
| `git rerere gc`     | Remove old rerere data                        | `git rerere gc`     | Unchanged                             | Usually no output |

---

# 23.13 git count-objects

`git count-objects` is one of the most useful repository-maintenance diagnostics.

Basic:

```bash
git count-objects
```

Verbose:

```bash
git count-objects -v
```

Human-readable sizes:

```bash
git count-objects -vH
```

Example:

```text
count: 42
size: 176
in-pack: 15432
packs: 3
size-pack: 98765
prune-packable: 0
garbage: 0
size-garbage: 0
```

Important fields include:

```text
count
in-pack
packs
size-pack
garbage
size-garbage
```

This is useful before and after maintenance.

---

# 23.14 git fsck

`git fsck` checks repository object connectivity and validity.

Basic:

```bash
git fsck
```

Full check:

```bash
git fsck --full
```

Show unreachable objects:

```bash
git fsck --unreachable
```

Show dangling objects:

```bash
git fsck --dangling
```

Example:

```bash
git fsck --full --unreachable
```

Potential output:

```text
dangling commit abc123...
dangling blob def456...
```

These objects may be recoverable.

Do not immediately delete them.

---

| Command                  | Description                  | Example                  | Branch State Before and After command | Output              |
| ------------------------ | ---------------------------- | ------------------------ | ------------------------------------- | ------------------- |
| `git fsck`               | Check object connectivity    | `git fsck`               | Unchanged                             | Diagnostics         |
| `git fsck --full`        | Perform full integrity check | `git fsck --full`        | Unchanged                             | Object diagnostics  |
| `git fsck --unreachable` | Show unreachable objects     | `git fsck --unreachable` | Unchanged                             | Unreachable objects |
| `git fsck --dangling`    | Show dangling objects        | `git fsck --dangling`    | Unchanged                             | Dangling objects    |

---

# 23.15 git verify-pack

`git verify-pack` verifies packfiles.

Find packfiles:

```bash
ls .git/objects/pack/*.idx
```

Verify a specific index:

```bash
git verify-pack -v .git/objects/pack/pack-*.idx
```

For a specific pack index:

```bash
git verify-pack -v .git/objects/pack/pack-123456.idx
```

This is particularly useful for investigating large objects inside packfiles.

---

# 23.16 git multi-pack-index

Large repositories may contain multiple packfiles.

Git's multi-pack-index can improve object lookup across multiple packs.

Write an MIDX:

```bash
git multi-pack-index write
```

Verify:

```bash
git multi-pack-index verify
```

Expire unnecessary objects:

```bash
git multi-pack-index expire
```

Repack using the multi-pack-index:

```bash
git multi-pack-index repack
```

These are advanced operations and are especially useful for large repositories and repositories receiving frequent incremental updates.

---

| Command                       | Description                      | Example                       | Branch State Before and After command | Output              |
| ----------------------------- | -------------------------------- | ----------------------------- | ------------------------------------- | ------------------- |
| `git multi-pack-index write`  | Create/update multi-pack index   | `git multi-pack-index write`  | Unchanged                             | Usually no output   |
| `git multi-pack-index verify` | Verify multi-pack index          | `git multi-pack-index verify` | Unchanged                             | Verification output |
| `git multi-pack-index expire` | Remove redundant pack references | `git multi-pack-index expire` | Unchanged                             | Usually no output   |
| `git multi-pack-index repack` | Repack using MIDX                | `git multi-pack-index repack` | Unchanged                             | Repack output       |

---

# 23.17 git commit-graph

The commit-graph accelerates commit-related operations.

Write a commit-graph:

```bash
git commit-graph write
```

Write for reachable commits:

```bash
git commit-graph write --reachable
```

Verify:

```bash
git commit-graph verify
```

A repository may contain:

```text
.git/objects/info/commit-graph
```

or a commit-graph chain.

Commit-graphs can improve operations such as:

```text
log traversal
reachability queries
merge-base calculations
branch comparisons
```

---

# 23.18 git repack -Ad

A commonly known aggressive repacking command is:

```bash
git repack -Ad
```

Meaning approximately:

```text
-A = repack all objects into a new pack
-d = delete redundant old packs
```

This can consolidate repository objects.

Inspect size:

```bash
git count-objects -vH
```

before:

```bash
git repack -Ad
```

and after.

For routine maintenance, prefer:

```bash
git maintenance run
```

or:

```bash
git gc
```

unless you have a specific reason to perform an aggressive repack.

---

# 23.19 Maintenance After Large History Changes

History rewriting can leave unreachable objects.

Examples include:

```bash
git rebase
git filter-repo
git filter-branch
git commit --amend
git reset --hard
```

After rewriting history, first verify the repository:

```bash
git fsck --full
```

Inspect reflogs:

```bash
git reflog --all
```

Inspect object statistics:

```bash
git count-objects -vH
```

Only after the recovery window is no longer required should aggressive pruning be considered.

A safe conceptual sequence is:

```text
Rewrite history
      |
      v
Verify references
      |
      v
Verify repository
      |
      v
Keep recovery window
      |
      v
Expire/prune when appropriate
```

---

# 23.20 Maintenance After Removing Large Files

Deleting a large file from the working tree does not necessarily remove it from Git history.

For example:

```bash
git rm large.iso
git commit -m "Remove large file"
```

does not remove previous versions from repository history.

For historical cleanup, specialized history-rewriting tools may be required.

After such a rewrite:

```bash
git count-objects -vH
```

then:

```bash
git fsck --full
```

and only later consider:

```bash
git gc
```

Be especially careful when the repository has already been pushed to shared remotes.

---

# 23.21 Maintenance of Bare Repositories

A bare repository has no working tree.

Typical structure:

```text
repository.git/
├── HEAD
├── objects/
├── refs/
├── hooks/
├── config
└── packed-refs
```

Maintenance can be run directly:

```bash
git --git-dir=/srv/git/project.git gc
```

Or:

```bash
cd /srv/git/project.git
git gc
```

Check statistics:

```bash
git --git-dir=/srv/git/project.git count-objects -vH
```

Run maintenance:

```bash
git --git-dir=/srv/git/project.git maintenance run
```

For Git hosting infrastructure, maintenance should normally be automated rather than performed manually on production repositories.

---

# 23.22 Maintenance in CI/CD

CI systems often clone repositories repeatedly.

Useful strategies include:

```text
shallow clones
reference repositories
caching
incremental fetching
commit-graph
multi-pack-index
Git maintenance
```

Inspect clone depth:

```bash
git rev-parse --is-shallow-repository
```

Output:

```text
true
```

or:

```text
false
```

Convert a shallow repository into a complete repository:

```bash
git fetch --unshallow
```

This can significantly increase repository size and network traffic, so it should be intentional.

---

# 23.23 Maintenance in Large Repositories

Large repositories require more careful maintenance.

Potential indicators:

```text
very large .git directory
many packfiles
slow git log
slow git status
slow branch comparisons
large numbers of unreachable objects
frequent CI cloning
large binary files
large history
```

Useful commands:

```bash
git count-objects -vH
git fsck --full
git maintenance run
git multi-pack-index verify
git commit-graph verify
```

For repositories with large histories, maintenance should be scheduled rather than performed reactively.

---

# 23.24 Maintenance Diagnostics

## Repository size

```bash
git count-objects -vH
```

## Object integrity

```bash
git fsck --full
```

## Packfiles

```bash
ls -lh .git/objects/pack/
```

## Commit graph

```bash
git commit-graph verify
```

## Multi-pack index

```bash
git multi-pack-index verify
```

## Reflogs

```bash
git reflog --all
```

## Git directory

```bash
du -sh .git
```

## Working tree plus repository

```bash
du -sh .
```

---

# 23.25 Maintenance Safety

Maintenance commands can affect object storage and recovery.

Especially dangerous commands include:

```bash
git prune
git reflog expire
git gc
git repack
```

The commands are not inherently unsafe, but their effects can become destructive when combined with aggressive expiration or history rewriting.

Before destructive maintenance:

```bash
git status
git branch --all
git tag
git reflog --all
git fsck --full
git count-objects -vH
```

For an important repository, ensure a reliable backup exists.

---

# 23.26 Recommended Maintenance Workflows

## Normal developer repository

Use:

```bash
git maintenance run
```

or:

```bash
git gc
```

occasionally when appropriate.

Inspect:

```bash
git count-objects -vH
```

---

## Large repository

Prefer scheduled maintenance:

```bash
git maintenance start
```

Then:

```bash
git maintenance run
```

Inspect:

```bash
git count-objects -vH
```

---

## Repository with suspected corruption

Do not immediately run cleanup.

Start with:

```bash
git fsck --full
```

Then:

```bash
git reflog --all
```

Then inspect:

```bash
git count-objects -vH
```

Recovery should come before cleanup.

---

## After history rewriting

Use:

```bash
git reflog --all
git fsck --full
git count-objects -vH
```

Keep a recovery period.

Only later consider pruning or aggressive repacking.

---

# 23.27 High-Value Maintenance Commands

| Command                              | Description                                  | Example                              | Branch State Before and After command | Output                   |
| ------------------------------------ | -------------------------------------------- | ------------------------------------ | ------------------------------------- | ------------------------ |
| `git maintenance run`                | Run repository maintenance                   | `git maintenance run`                | Unchanged                             | Maintenance output       |
| `git maintenance start`              | Enable scheduled maintenance                 | `git maintenance start`              | Unchanged                             | Scheduling output        |
| `git maintenance stop`               | Disable scheduled maintenance                | `git maintenance stop`               | Unchanged                             | Scheduling output        |
| `git gc`                             | Garbage collect repository                   | `git gc`                             | Branch refs unchanged                 | Usually little/no output |
| `git gc --auto`                      | Automatically decide whether GC is necessary | `git gc --auto`                      | Unchanged                             | Usually no output        |
| `git count-objects -vH`              | Inspect object statistics                    | `git count-objects -vH`              | Unchanged                             | Repository statistics    |
| `git fsck --full`                    | Verify repository integrity                  | `git fsck --full`                    | Unchanged                             | Diagnostics              |
| `git fsck --unreachable`             | Find unreachable objects                     | `git fsck --unreachable`             | Unchanged                             | Object IDs               |
| `git prune --dry-run`                | Preview pruning                              | `git prune --dry-run`                | Unchanged                             | Candidate objects        |
| `git repack -d`                      | Repack and remove redundant packs            | `git repack -d`                      | Unchanged                             | Repack output            |
| `git repack -Ad`                     | Aggressively consolidate packs               | `git repack -Ad`                     | Unchanged                             | Repack output            |
| `git pack-refs --all --prune`        | Pack refs                                    | `git pack-refs --all --prune`        | Ref targets unchanged                 | Usually no output        |
| `git commit-graph write --reachable` | Build commit graph                           | `git commit-graph write --reachable` | Unchanged                             | Usually no output        |
| `git commit-graph verify`            | Verify commit graph                          | `git commit-graph verify`            | Unchanged                             | Verification output      |
| `git multi-pack-index write`         | Build/update MIDX                            | `git multi-pack-index write`         | Unchanged                             | Usually no output        |
| `git multi-pack-index verify`        | Verify MIDX                                  | `git multi-pack-index verify`        | Unchanged                             | Verification output      |
| `git rerere gc`                      | Clean old rerere data                        | `git rerere gc`                      | Unchanged                             | Usually no output        |

---

# 23.28 Maintenance Best Practices

## 1. Prefer automatic maintenance

For normal repositories:

```bash
git maintenance run
```

is generally preferable to manually forcing complex maintenance operations.

---

## 2. Inspect before deleting

Before:

```bash
git prune
```

inspect:

```bash
git fsck --full
git reflog --all
```

---

## 3. Keep recovery windows

Reflogs can help recover:

```text
deleted branches
reset commits
amended commits
rebased commits
```

Do not aggressively expire them immediately after rewriting history.

---

## 4. Measure repository size

Use:

```bash
git count-objects -vH
```

This gives a much better picture than simply looking at:

```bash
du -sh .git
```

---

## 5. Do not confuse deletion with history cleanup

This:

```bash
git rm secret.txt
git commit -m "Remove secret"
```

does not remove the file from historical commits.

If a secret was committed, treat it as compromised and rotate the secret first.

History rewriting is a separate operation.

---

## 6. Use server-side automation for Git hosting

Production Git servers should normally have scheduled maintenance.

Typical architecture:

```text
Git Server
    |
    +-- scheduled maintenance
    +-- object packing
    +-- commit-graph
    +-- multi-pack-index
    +-- integrity monitoring
    +-- backups
```

---

## 7. Back up before destructive maintenance

For critical repositories:

```text
Backup
  |
  v
Verify backup
  |
  v
Maintenance
  |
  v
Verify repository
```

---

# Maintenance Command Cheat Sheet

```bash
# Inspect repository objects
git count-objects -vH

# Check repository integrity
git fsck --full

# Find unreachable objects
git fsck --unreachable

# Preview pruning
git prune --dry-run

# Run normal garbage collection
git gc

# Allow Git to decide whether GC is necessary
git gc --auto

# Run scheduled maintenance
git maintenance run

# Enable scheduled maintenance
git maintenance start

# Disable scheduled maintenance
git maintenance stop

# Pack refs
git pack-refs --all --prune

# Repack repository
git repack -d

# Aggressively repack
git repack -Ad

# Write commit graph
git commit-graph write --reachable

# Verify commit graph
git commit-graph verify

# Write multi-pack index
git multi-pack-index write

# Verify multi-pack index
git multi-pack-index verify

# Clean rerere cache
git rerere gc
```

---

# Maintenance Decision Tree

```text
Is the repository slow or large?
        |
        +-- No --> Normal Git maintenance
        |
        +-- Yes
             |
             v
       git count-objects -vH
             |
             v
       Is repository integrity OK?
             |
          +-- No --> git fsck --full
          |
          +-- Yes
               |
               v
       Are there many packfiles?
               |
          +-- Yes --> multi-pack-index
          |
          +-- No
               |
               v
       Is commit traversal slow?
               |
          +-- Yes --> commit-graph
          |
          +-- No
               |
               v
       Run scheduled maintenance
```

---

# Branch State Summary

Most repository-maintenance commands do **not** modify:

```text
HEAD
current branch
branch names
commit history
working-tree files
staging area
```

They primarily modify internal repository storage.

For example:

```text
Before:

main
 |
 A---B---C
```

After:

```bash
git gc
```

the logical history remains:

```text
main
 |
 A---B---C
```

The internal representation may change:

```text
Loose objects
      |
      v
Packfiles
      |
      v
Optimized storage
```

Therefore maintenance should normally be transparent to the logical Git history.

---

# Key Concepts to Memorize

```text
git maintenance
    Scheduled repository maintenance

git gc
    Garbage collection and housekeeping

git prune
    Remove eligible unreachable loose objects

git repack
    Reorganize objects into packfiles

git pack-refs
    Pack references

git count-objects
    Repository object statistics

git fsck
    Repository integrity/connectivity check

git commit-graph
    Accelerate commit graph operations

git multi-pack-index
    Efficient lookup across multiple packfiles

git rerere gc
    Clean old recorded conflict resolutions
```

---

## Next Part

**Next file:** `24-repository-diagnostics.md`

[Next: Repository Diagnostics](24-repository-diagnostics.md)
