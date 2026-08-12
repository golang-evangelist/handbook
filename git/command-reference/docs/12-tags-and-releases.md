# 12. Tags & Releases

Git tags provide permanent, human-readable references to specific commits.

Tags are commonly used for:

* software releases;
* semantic versioning;
* production deployments;
* milestones;
* release candidates;
* hotfix versions;
* CI/CD triggers;
* deployment automation.

Typical release history:

```text
main
 |
 A---B---C---D---E
         |       |
       v1.0.0  v1.1.0
```

A tag points to a Git object, normally a commit:

```text
v1.0.0
   |
   v
 commit C
```

Unlike branches, tags normally do not move automatically when new commits are created.

---

## Table of Contents

* [12.1 Tag Mental Model](#121-tag-mental-model)
* [12.2 List Tags](#122-list-tags)
* [12.3 Create a Lightweight Tag](#123-create-a-lightweight-tag)
* [12.4 Create an Annotated Tag](#124-create-an-annotated-tag)
* [12.5 Tag a Specific Commit](#125-tag-a-specific-commit)
* [12.6 Show Tag Information](#126-show-tag-information)
* [12.7 Inspect a Tag](#127-inspect-a-tag)
* [12.8 Compare Tags](#128-compare-tags)
* [12.9 Search Tags](#129-search-tags)
* [12.10 Sort Tags by Version](#1210-sort-tags-by-version)
* [12.11 Delete a Local Tag](#1211-delete-a-local-tag)
* [12.12 Delete a Remote Tag](#1212-delete-a-remote-tag)
* [12.13 Push One Tag](#1213-push-one-tag)
* [12.14 Push Multiple Tags](#1214-push-multiple-tags)
* [12.15 Push All Tags](#1215-push-all-tags)
* [12.16 Fetch Tags](#1216-fetch-tags)
* [12.17 Fetch a Specific Tag](#1217-fetch-a-specific-tag)
* [12.18 Checkout a Tag](#1218-checkout-a-tag)
* [12.19 Switch to a Branch from a Tag](#1219-switch-to-a-branch-from-a-tag)
* [12.20 Create a Release Branch from a Tag](#1220-create-a-release-branch-from-a-tag)
* [12.21 Tag Naming Conventions](#1221-tag-naming-conventions)
* [12.22 Semantic Versioning](#1222-semantic-versioning)
* [12.23 Release Candidate Tags](#1223-release-candidate-tags)
* [12.24 Hotfix Tags](#1224-hotfix-tags)
* [12.25 Release Workflow](#1225-release-workflow)
* [12.26 Verify Release Commit](#1226-verify-release-commit)
* [12.27 Signed Tags](#1227-signed-tags)
* [12.28 Verify Signed Tags](#1228-verify-signed-tags)
* [12.29 Tagging a Merge Commit](#1229-tagging-a-merge-commit)
* [12.30 Tags in CI/CD](#1230-tags-in-cicd)
* [12.31 Remote Tag Management](#1231-remote-tag-management)
* [12.32 Moving an Existing Tag](#1232-moving-an-existing-tag)
* [12.33 Recovering Deleted Tags](#1233-recovering-deleted-tags)
* [12.34 Dangerous Tag Operations](#1234-dangerous-tag-operations)
* [12.35 High-Value Tag Commands](#1235-high-value-tag-commands)

---

# 12.1 Tag Mental Model

A branch is normally a movable reference:

```text
main
 |
 v
A---B---C---D
         ^
        branch advances
```

A tag is normally intended to remain fixed:

```text
v1.0.0
  |
  v
A---B---C---D
      ^
     tag
```

If new commits are added:

```text
v1.0.0
  |
  v
A---B---C---D---E---F
```

`v1.0.0` still points to `C`.

This makes tags ideal for identifying exact release versions.

---

# 12.2 List Tags

List all local tags:

```bash
git tag
```

Example:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

List tags with references:

```bash
git tag -n
```

Example:

```text
v1.0.0          Initial production release
v1.1.0          Add authentication
v2.0.0          API redesign
```

List tags matching a pattern:

```bash
git tag -l "v1.*"
```

Example:

```text
v1.0.0
v1.1.0
v1.2.0
```

---

| Command             | Description             | Example             | Branch State Before and After command | Output                     |
| ------------------- | ----------------------- | ------------------- | ------------------------------------- | -------------------------- |
| `git tag`           | List tags               | `git tag`           | Branch unchanged                      | Tag names                  |
| `git tag -n`        | List tags with messages | `git tag -n`        | Branch unchanged                      | Tags and short annotations |
| `git tag -l "v1.*"` | List matching tags      | `git tag -l "v1.*"` | Branch unchanged                      | Matching tags              |

---

# 12.3 Create a Lightweight Tag

A lightweight tag is simply a reference to an object, commonly a commit.

Create one on `HEAD`:

```bash
git tag v1.0.0
```

Before:

```text
main
 |
 A---B---C
         ^
        HEAD
```

After:

```text
main
 |
 A---B---C
         ^
        HEAD
         |
       v1.0.0
```

The branch does not move.

No commit is created.

---

# 12.4 Create an Annotated Tag

For releases, annotated tags are generally preferable:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

Annotated tags contain metadata such as:

* tag name;
* tagger;
* date;
* message;
* referenced object.

Example:

```bash
git tag -a v2.0.0 -m "Release v2.0.0"
```

Inspect:

```bash
git show v2.0.0
```

For production releases, a typical pattern is:

```bash
git tag -a v1.4.0 -m "Release v1.4.0"
```

---

# 12.5 Tag a Specific Commit

You do not have to tag `HEAD`.

Use:

```bash
git tag -a v1.0.0 <commit> -m "Release v1.0.0"
```

Example:

```bash
git tag -a v1.0.0 a1b2c3d -m "Release v1.0.0"
```

Before:

```text
A---B---C---D---E
    ^
 commit a1b2c3d
```

After:

```text
v1.0.0
   |
   v
A---B---C---D---E
    ^
 tagged commit
```

The current branch and `HEAD` do not move.

---

# 12.6 Show Tag Information

Show a tag:

```bash
git show v1.0.0
```

For an annotated tag, Git displays the tag metadata and the referenced commit.

Useful:

```bash
git show --stat v1.0.0
```

or:

```bash
git show --summary v1.0.0
```

---

# 12.7 Inspect a Tag

Determine what a tag points to:

```bash
git rev-parse v1.0.0
```

Show the tag reference:

```bash
git show-ref --tags
```

Example:

```text
a1b2c3d refs/tags/v1.0.0
b2c3d4e refs/tags/v1.1.0
```

Inspect object type:

```bash
git cat-file -t v1.0.0
```

For an annotated tag:

```text
tag
```

For a lightweight tag:

```text
commit
```

This distinction is useful when auditing repositories.

---

# 12.8 Compare Tags

Compare two releases:

```bash
git diff v1.0.0 v1.1.0
```

Show commits between releases:

```bash
git log v1.0.0..v1.1.0
```

Compact form:

```bash
git log --oneline v1.0.0..v1.1.0
```

Show changed files:

```bash
git diff --stat v1.0.0 v1.1.0
```

Show only file names:

```bash
git diff --name-only v1.0.0 v1.1.0
```

---

| Command                        | Description                                | Example                            | Branch State Before and After command | Output           |
| ------------------------------ | ------------------------------------------ | ---------------------------------- | ------------------------------------- | ---------------- |
| `git diff TAG1 TAG2`           | Compare release contents                   | `git diff v1.0.0 v1.1.0`           | Branch unchanged                      | Patch            |
| `git diff --stat TAG1 TAG2`    | Show release statistics                    | `git diff --stat v1.0.0 v1.1.0`    | Branch unchanged                      | File statistics  |
| `git log TAG1..TAG2`           | Show commits after one tag through another | `git log v1.0.0..v1.1.0`           | Branch unchanged                      | Commit history   |
| `git log --oneline TAG1..TAG2` | Compact release history                    | `git log --oneline v1.0.0..v1.1.0` | Branch unchanged                      | One-line commits |

---

# 12.9 Search Tags

List tags matching a pattern:

```bash
git tag -l "v2.*"
```

Examples:

```bash
git tag -l "v1.*"
git tag -l "v2.*"
git tag -l "*-rc.*"
```

Search release candidates:

```bash
git tag -l "*rc*"
```

Example:

```text
v2.0.0-rc.1
v2.0.0-rc.2
```

---

# 12.10 Sort Tags by Version

Git can sort tags using version-aware sorting:

```bash
git tag --sort=-v:refname
```

Example:

```text
v2.10.0
v2.9.0
v2.8.0
v2.1.0
v2.0.0
```

Ascending:

```bash
git tag --sort=v:refname
```

This is preferable to plain lexical sorting when using semantic versions.

---

## Find the latest version tag

```bash
git describe --tags --abbrev=0
```

Example output:

```text
v2.4.1
```

This is particularly useful in scripts and CI/CD.

---

# 12.11 Delete a Local Tag

Delete a local tag:

```bash
git tag -d v1.0.0
```

Example:

```bash
git tag -d v1.0.0
```

Output:

```text
Deleted tag 'v1.0.0' (was a1b2c3d)
```

This only removes the local tag reference.

It does not delete the commit.

It does not automatically delete the remote tag.

---

# 12.12 Delete a Remote Tag

Modern syntax:

```bash
git push origin --delete v1.0.0
```

Alternative syntax:

```bash
git push origin :refs/tags/v1.0.0
```

The first form is easier to understand and generally preferable.

Check remote tags:

```bash
git ls-remote --tags origin
```

---

# 12.13 Push One Tag

Tags are not automatically pushed by a normal:

```bash
git push
```

Push one tag explicitly:

```bash
git push origin v1.0.0
```

For example:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

Afterward, the remote repository has the tag.

---

# 12.14 Push Multiple Tags

Push multiple tags:

```bash
git push origin v1.0.0 v1.1.0
```

Example:

```bash
git push origin v1.0.0 v1.1.0 v2.0.0
```

This is useful when several locally created tags need to be published.

---

# 12.15 Push All Tags

Push all local tags:

```bash
git push origin --tags
```

This pushes tags that are not already present on the remote.

Use carefully on shared repositories.

Before:

```bash
git tag
```

Review the tags first:

```bash
git tag --sort=v:refname
```

Then:

```bash
git push origin --tags
```

---

# 12.16 Fetch Tags

Fetch tags from a remote:

```bash
git fetch --tags
```

From a specific remote:

```bash
git fetch origin --tags
```

This updates local tag references from the remote.

A normal fetch may also obtain tags that are reachable from fetched objects, depending on Git's fetch behavior and configuration.

For explicitly synchronizing tags:

```bash
git fetch origin --tags
```

---

# 12.17 Fetch a Specific Tag

Fetch a particular tag:

```bash
git fetch origin tag v1.2.0
```

Example:

```bash
git fetch origin tag v2.0.0
```

Then inspect:

```bash
git show v2.0.0
```

This is useful when you need a specific release without broadly updating all tag references.

---

# 12.18 Checkout a Tag

Older syntax:

```bash
git checkout v1.0.0
```

If the tag points directly to a commit, Git normally places you in detached `HEAD` state.

Example:

```text
HEAD
 |
 v
v1.0.0
 |
 commit C

main
 |
 A---B---C---D
```

You are not on a normal branch.

Check:

```bash
git status
```

You may see:

```text
HEAD detached at v1.0.0
```

---

# 12.19 Switch to a Branch from a Tag

If you want to create a branch starting at a tag:

```bash
git switch -c release-maintenance v1.0.0
```

Equivalent older syntax:

```bash
git checkout -b release-maintenance v1.0.0
```

Before:

```text
v1.0.0
   |
A--B--C--D
      ^
     tag
```

After:

```text
release-maintenance
       |
A--B---C--D
      ^
     tag
```

The new branch points to the commit tagged `v1.0.0`.

---

# 12.20 Create a Release Branch from a Tag

Example:

```bash
git switch -c release/1.4 v1.4.0
```

This creates:

```text
release/1.4
```

starting at:

```text
v1.4.0
```

Typical maintenance workflow:

```bash
git switch -c release/1.4 v1.4.0
```

Make fixes:

```bash
git add .
git commit -m "Fix release 1.4 issue"
```

Create a patch release:

```bash
git tag -a v1.4.1 -m "Release v1.4.1"
```

Push:

```bash
git push origin release/1.4
git push origin v1.4.1
```

---

# 12.21 Tag Naming Conventions

Common conventions include:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

or:

```text
1.0.0
1.1.0
2.0.0
```

Both are valid.

The important requirement is consistency.

A practical convention is:

```text
vMAJOR.MINOR.PATCH
```

Examples:

```text
v1.0.0
v1.2.3
v2.0.0
```

Pre-release versions:

```text
v2.0.0-alpha.1
v2.0.0-beta.1
v2.0.0-rc.1
```

---

# 12.22 Semantic Versioning

A common versioning scheme is:

```text
MAJOR.MINOR.PATCH
```

For example:

```text
2.4.1
```

means:

```text
MAJOR = 2
MINOR = 4
PATCH = 1
```

Typical interpretation:

```text
MAJOR
Breaking/incompatible API changes

MINOR
Backward-compatible functionality

PATCH
Backward-compatible bug fixes
```

Examples:

```text
v1.0.0
v1.1.0
v1.1.1
v1.2.0
v2.0.0
```

Create:

```bash
git tag -a v1.2.0 -m "Release v1.2.0"
```

---

# 12.23 Release Candidate Tags

Release candidates can be represented as:

```text
v2.0.0-rc.1
v2.0.0-rc.2
v2.0.0-rc.3
```

Create:

```bash
git tag -a v2.0.0-rc.1 -m "Release candidate 1 for v2.0.0"
```

Push:

```bash
git push origin v2.0.0-rc.1
```

After validation:

```bash
git tag -a v2.0.0 -m "Release v2.0.0"
git push origin v2.0.0
```

---

# 12.24 Hotfix Tags

Suppose production is running:

```text
v2.3.0
```

A critical fix is developed.

After the fix:

```bash
git tag -a v2.3.1 -m "Hotfix v2.3.1"
git push origin v2.3.1
```

Typical history:

```text
v2.3.0
   |
   v
A---B---C
         \
          H
          |
        v2.3.1
```

The exact branch topology depends on the team's workflow.

---

# 12.25 Release Workflow

A practical release sequence:

```bash
git status
git switch main
git pull --ff-only
```

Verify the commit:

```bash
git log -1 --oneline
```

Run tests:

```bash
./run-tests.sh
```

Create annotated tag:

```bash
git tag -a v1.5.0 -m "Release v1.5.0"
```

Inspect:

```bash
git show v1.5.0
```

Push:

```bash
git push origin v1.5.0
```

Verify remote:

```bash
git ls-remote --tags origin
```

---

# 12.26 Verify Release Commit

Before tagging, inspect:

```bash
git status
git log -1 --oneline
git show --stat HEAD
```

Verify the branch:

```bash
git branch --show-current
```

Verify remote synchronization:

```bash
git fetch origin
git status
```

A useful check:

```bash
git rev-parse HEAD
git rev-parse origin/main
```

If they match, the local `HEAD` and `origin/main` point to the same commit.

Then tag:

```bash
git tag -a v1.5.0 -m "Release v1.5.0"
```

---

# 12.27 Signed Tags

For cryptographically signed tags:

```bash
git tag -s v1.0.0 -m "Release v1.0.0"
```

This requires an appropriate signing setup, commonly GPG or another supported signing mechanism depending on Git configuration.

Check signing configuration:

```bash
git config --get user.signingkey
```

You may also inspect:

```bash
git config --get commit.gpgsign
```

A signed release tag can provide stronger provenance than an unsigned tag.

---

# 12.28 Verify Signed Tags

Verify a signed tag:

```bash
git tag -v v1.0.0
```

Git will attempt to validate the tag signature using the configured trust/key infrastructure.

A successful verification provides information about:

* signature;
* signer;
* key;
* referenced object.

A failed verification should be treated as a release-integrity warning.

---

# 12.29 Tagging a Merge Commit

Release tags are often placed on merge commits.

Example:

```text
feature
   \
    B---C
         \
main -----M
           ^
         HEAD
```

Tag the merge commit:

```bash
git tag -a v2.0.0 -m "Release v2.0.0"
```

The tag points to `M`.

Inspect:

```bash
git show --summary v2.0.0
```

This is common when releases are created from the protected/default branch after feature integration.

---

# 12.30 Tags in CI/CD

CI/CD systems commonly use Git tags as release triggers.

Example condition:

```text
push tag v*
```

A pipeline may:

```text
Git tag
   |
   v
CI pipeline
   |
   +-- build
   +-- test
   +-- package
   +-- publish
   +-- deploy
```

Useful commands for scripts:

```bash
git describe --tags --always
```

Example:

```text
v1.5.0
```

Get latest tag:

```bash
git describe --tags --abbrev=0
```

Get exact current tag:

```bash
git describe --tags --exact-match HEAD
```

If `HEAD` is not exactly tagged, Git returns a non-zero status.

---

# 12.31 Remote Tag Management

List remote tags:

```bash
git ls-remote --tags origin
```

Example:

```text
a1b2c3d refs/tags/v1.0.0
b2c3d4e refs/tags/v1.1.0
```

Fetch remote tags:

```bash
git fetch origin --tags
```

Push tag:

```bash
git push origin v1.2.0
```

Delete remote tag:

```bash
git push origin --delete v1.2.0
```

---

# 12.32 Moving an Existing Tag

Moving an existing tag should generally be avoided for published releases.

If absolutely necessary, move the local tag:

```bash
git tag -f v1.0.0 <new-commit>
```

Example:

```bash
git tag -f v1.0.0 HEAD
```

Then force-update the remote tag:

```bash
git push --force origin v1.0.0
```

A more explicit form:

```bash
git push --force origin refs/tags/v1.0.0
```

This rewrites what the tag means for users who already fetched it.

For published production releases, prefer creating a new version instead:

```text
v1.0.0
v1.0.1
```

rather than moving `v1.0.0`.

---

# 12.33 Recovering Deleted Tags

If a tag was deleted locally but the commit is still known:

```bash
git tag v1.0.0 <commit>
```

If the commit can be found through history:

```bash
git log --all --oneline
```

or:

```bash
git reflog
```

Then recreate:

```bash
git tag -a v1.0.0 <commit> -m "Release v1.0.0"
```

If the remote still has the tag:

```bash
git fetch origin --tags
```

may restore the local tag reference.

Check:

```bash
git tag -l "v1.0.0"
```

---

# 12.34 Dangerous Tag Operations

## Delete local tag

```bash
git tag -d v1.0.0
```

This removes the local reference.

---

## Delete remote tag

```bash
git push origin --delete v1.0.0
```

This removes the remote tag.

---

## Move an existing tag

```bash
git tag -f v1.0.0 HEAD
```

This changes the local meaning of the tag.

---

## Force-push a moved tag

```bash
git push --force origin v1.0.0
```

This changes the remote meaning of a published tag.

Avoid this for established releases unless there is a documented incident/recovery procedure.

---

# 12.35 High-Value Tag Commands

| Command                                  | Description                        | Example                                  | Branch State Before and After command | Output                  |
| ---------------------------------------- | ---------------------------------- | ---------------------------------------- | ------------------------------------- | ----------------------- |
| `git tag`                                | List local tags                    | `git tag`                                | Branch unchanged                      | Tag names               |
| `git tag -n`                             | List tags with messages            | `git tag -n`                             | Branch unchanged                      | Tag names/messages      |
| `git tag v1.0.0`                         | Create lightweight tag             | `git tag v1.0.0`                         | Branch unchanged                      | Usually no output       |
| `git tag -a v1.0.0 -m "..."`             | Create annotated tag               | `git tag -a v1.0.0 -m "Release v1.0.0"`  | Branch unchanged                      | Usually no output       |
| `git tag -s v1.0.0 -m "..."`             | Create signed tag                  | `git tag -s v1.0.0 -m "Release v1.0.0"`  | Branch unchanged                      | Signing output          |
| `git tag <tag> <commit>`                 | Tag specific commit                | `git tag v1.0.0 a1b2c3d`                 | Branch unchanged                      | Usually no output       |
| `git show v1.0.0`                        | Show tag/commit                    | `git show v1.0.0`                        | Branch unchanged                      | Tag metadata and commit |
| `git show-ref --tags`                    | Show tag references                | `git show-ref --tags`                    | Branch unchanged                      | Object IDs and refs     |
| `git rev-parse v1.0.0`                   | Resolve tag to object ID           | `git rev-parse v1.0.0`                   | Branch unchanged                      | SHA-1/SHA-256 object ID |
| `git tag -l "v1.*"`                      | Filter tags                        | `git tag -l "v1.*"`                      | Branch unchanged                      | Matching tags           |
| `git tag --sort=-v:refname`              | Sort versions descending           | `git tag --sort=-v:refname`              | Branch unchanged                      | Version-sorted tags     |
| `git describe --tags`                    | Describe current commit using tags | `git describe --tags`                    | Branch unchanged                      | Tag-based description   |
| `git describe --tags --abbrev=0`         | Find nearest tag                   | `git describe --tags --abbrev=0`         | Branch unchanged                      | Tag                     |
| `git describe --tags --exact-match HEAD` | Check whether HEAD is tagged       | `git describe --tags --exact-match HEAD` | Branch unchanged                      | Exact tag or error      |
| `git diff v1.0.0 v1.1.0`                 | Compare releases                   | `git diff v1.0.0 v1.1.0`                 | Branch unchanged                      | Patch                   |
| `git log v1.0.0..v1.1.0`                 | Show commits between releases      | `git log v1.0.0..v1.1.0`                 | Branch unchanged                      | Commit history          |
| `git tag -d v1.0.0`                      | Delete local tag                   | `git tag -d v1.0.0`                      | Branch unchanged                      | Deletion confirmation   |
| `git push origin v1.0.0`                 | Push one tag                       | `git push origin v1.0.0`                 | Branch unchanged                      | Push output             |
| `git push origin --tags`                 | Push all tags                      | `git push origin --tags`                 | Branch unchanged                      | Push output             |
| `git fetch origin --tags`                | Fetch tags                         | `git fetch origin --tags`                | Branch unchanged                      | Fetch output            |
| `git fetch origin tag v1.0.0`            | Fetch specific tag                 | `git fetch origin tag v1.0.0`            | Branch unchanged                      | Fetch output            |
| `git ls-remote --tags origin`            | List remote tags                   | `git ls-remote --tags origin`            | Branch unchanged                      | Remote refs             |
| `git push origin --delete v1.0.0`        | Delete remote tag                  | `git push origin --delete v1.0.0`        | Branch unchanged                      | Remote deletion output  |
| `git switch -c release/1.0 v1.0.0`       | Create branch from tag             | `git switch -c release/1.0 v1.0.0`       | New branch created at tag             | Switch output           |
| `git tag -v v1.0.0`                      | Verify signed tag                  | `git tag -v v1.0.0`                      | Branch unchanged                      | Signature verification  |
| `git tag -f v1.0.0 HEAD`                 | Move existing local tag            | `git tag -f v1.0.0 HEAD`                 | Branch unchanged; tag moves           | Force-update message    |
| `git push --force origin v1.0.0`         | Force-update remote tag            | `git push --force origin v1.0.0`         | Branch unchanged; remote tag moves    | Push output             |

---

# Release Checklist

```text
1. Ensure working tree is clean.
2. Ensure the correct branch is checked out.
3. Synchronize with the remote.
4. Verify the exact commit to release.
5. Run tests.
6. Create an annotated tag.
7. Inspect the tag.
8. Push the tag.
9. Verify the remote tag.
10. Trigger or monitor CI/CD.
```

Command sequence:

```bash
git status
git switch main
git pull --ff-only
git log -1 --oneline
git show --stat HEAD

# Run project tests
./run-tests.sh

git tag -a v1.5.0 -m "Release v1.5.0"
git show v1.5.0
git push origin v1.5.0

git ls-remote --tags origin
```

---

# Recommended Production Release Pattern

For production software, a clean approach is:

```text
main
 |
 A---B---C---D---E
     |       |
   v1.0.0  v1.1.0
```

Create:

```bash
git tag -a v1.1.0 -m "Release v1.1.0"
```

Verify:

```bash
git show v1.1.0
```

Publish:

```bash
git push origin v1.1.0
```

Then allow CI/CD to use the tag as the immutable release identifier.

---

# Tag vs Branch

```text
BRANCH

main
 |
 A---B---C---D---E
             ^
            main

main can move to E.
```

```text
TAG

v1.0.0
   |
   v
A---B---C---D---E

v1.0.0 remains attached to its original commit.
```

Use:

```text
branches -> development lines
tags     -> fixed milestones/releases
```

---

# Essential Commands to Memorize

```bash
# List tags
git tag

# Create lightweight tag
git tag v1.0.0

# Create annotated release tag
git tag -a v1.0.0 -m "Release v1.0.0"

# Tag a specific commit
git tag -a v1.0.0 <commit> -m "Release v1.0.0"

# Show tag
git show v1.0.0

# Sort versions
git tag --sort=-v:refname

# Find latest tag
git describe --tags --abbrev=0

# Compare releases
git diff v1.0.0 v1.1.0

# Show commits between releases
git log --oneline v1.0.0..v1.1.0

# Push one tag
git push origin v1.0.0

# Push all tags
git push origin --tags

# Fetch tags
git fetch origin --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Verify signed tag
git tag -v v1.0.0
```

---

# Important Rules

1. **Use annotated tags for formal releases.**
2. **Use semantic versioning consistently if your project follows SemVer.**
3. **Verify the exact commit before tagging.**
4. **Push release tags explicitly.**
5. **Do not casually move published tags.**
6. **Do not force-update production tags without a documented reason.**
7. **Use tags as immutable release identifiers whenever possible.**
8. **Use signed tags when release provenance matters.**
9. **Use `git describe` in automation when a tag-based version is required.**
10. **Treat deletion or rewriting of remote release tags as potentially disruptive operations.**

---

## Next Part

**Next file:** `13-searching-git-history.md`

[Next: Searching Git History](13-searching-git-history.md)
