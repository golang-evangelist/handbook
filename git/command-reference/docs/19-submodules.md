# 19. Git Submodules

Git submodules allow one Git repository to include another Git repository as a dependency while keeping the two repositories as separate projects.

A submodule is represented in the parent repository by a **gitlink** pointing to a specific commit of another repository.

Typical structure:

```text
my-project/
├── .git/
├── .gitmodules
├── src/
└── dependencies/
    └── library/
        └── .git
```

The parent repository records:

```text
dependencies/library -> <specific commit>
```

It does **not** directly record all files of the submodule as normal files.

---

# Table of Contents

* [19.1 What Is a Git Submodule?](#191-what-is-a-git-submodule)
* [19.2 Why Use Submodules?](#192-why-use-submodules)
* [19.3 Submodule Architecture](#193-submodule-architecture)
* [19.4 `.gitmodules`](#194-gitmodules)
* [19.5 Adding a Submodule](#195-adding-a-submodule)
* [19.6 Cloning a Repository With Submodules](#196-cloning-a-repository-with-submodules)
* [19.7 Initializing Submodules](#197-initializing-submodules)
* [19.8 Updating Submodules](#198-updating-submodules)
* [19.9 Checking Submodule Status](#199-checking-submodule-status)
* [19.10 Entering a Submodule](#1910-entering-a-submodule)
* [19.11 Pulling Changes With Submodules](#1911-pulling-changes-with-submodules)
* [19.12 Updating a Submodule to a New Commit](#1912-updating-a-submodule-to-a-new-commit)
* [19.13 Updating a Submodule to a Branch](#1913-updating-a-submodule-to-a-branch)
* [19.14 Tracking a Remote Branch](#1914-tracking-a-remote-branch)
* [19.15 Recursive Submodules](#1915-recursive-submodules)
* [19.16 Nested Submodules](#1916-nested-submodules)
* [19.17 Removing a Submodule](#1917-removing-a-submodule)
* [19.18 Deinitializing a Submodule](#1918-deinitializing-a-submodule)
* [19.19 Synchronizing Submodule URLs](#1919-synchronizing-submodule-urls)
* [19.20 Submodule Summary](#1920-submodule-summary)
* [19.21 Inspecting Submodule Changes](#1921-inspecting-submodule-changes)
* [19.22 Fetching Submodule Repositories](#1922-fetching-submodule-repositories)
* [19.23 Pushing With Submodules](#1923-pushing-with-submodules)
* [19.24 `push.recurseSubmodules`](#1924-pushrecursesubmodules)
* [19.25 `fetch.recurseSubmodules`](#1925-fetchrecursesubmodules)
* [19.26 `submodule.recurse`](#1926-submodulerecurse)
* [19.27 Submodule Configuration](#1927-submodule-configuration)
* [19.28 Relative Submodule URLs](#1928-relative-submodule-urls)
* [19.29 Local Development With Submodules](#1929-local-development-with-submodules)
* [19.30 CI/CD With Submodules](#1930-cicd-with-submodules)
* [19.31 Troubleshooting Submodules](#1931-troubleshooting-submodules)
* [19.32 Common Submodule Problems](#1932-common-submodule-problems)
* [19.33 Submodule Security Considerations](#1933-submodule-security-considerations)
* [19.34 High-Value Submodule Commands](#1934-high-value-submodule-commands)
* [19.35 Submodule Cheat Sheet](#1935-submodule-cheat-sheet)

---

# 19.1 What Is a Git Submodule?

A submodule is another Git repository embedded inside a parent Git repository.

For example:

```text
application/
├── src/
├── tests/
├── .gitmodules
└── libs/
    └── authentication/
```

The `authentication` directory is itself a Git repository.

The parent repository records the exact commit of that repository.

Conceptually:

```text
Parent repository
        |
        +---- libs/authentication
                    |
                    +---- commit abc123
```

This makes the dependency version explicit and reproducible.

---

# 19.2 Why Use Submodules?

Submodules are useful when:

* A dependency is maintained independently.
* The dependency has its own release cycle.
* You need to pin an exact dependency commit.
* Multiple projects share the same repository.
* The dependency should remain independently versioned.
* You need reproducible builds.

Example:

```text
Company
├── application-a
├── application-b
└── shared-library
```

Both applications can reference a specific commit of:

```text
shared-library
```

---

# 19.3 Submodule Architecture

A parent repository contains:

```text
.gitmodules
```

and a gitlink entry:

```text
libs/library
```

The `.gitmodules` file contains configuration such as:

```ini
[submodule "libs/library"]
    path = libs/library
    url = https://example.com/library.git
```

The parent repository stores the exact commit:

```text
160000 commit abc123...
```

The `160000` mode identifies a Git submodule entry.

---

# 19.4 `.gitmodules`

Display:

```bash
cat .gitmodules
```

Example:

```ini
[submodule "libs/library"]
    path = libs/library
    url = https://github.com/example/library.git
```

The file is committed to the parent repository.

Typical properties include:

```text
path
url
branch
update
fetchRecurseSubmodules
ignore
```

The `.gitmodules` file should normally be version-controlled.

---

# 19.5 Adding a Submodule

Basic syntax:

```bash
git submodule add <repository-url> <path>
```

Example:

```bash
git submodule add https://github.com/example/library.git libs/library
```

Git creates:

```text
libs/library/
.gitmodules
```

Check:

```bash
git status
```

You will typically see:

```text
new file:   .gitmodules
new file:   libs/library
```

Commit:

```bash
git add .gitmodules libs/library
git commit -m "Add library submodule"
```

Push:

```bash
git push
```

---

| Command                      | Description                | Example                                                  | Branch State Before and After command | Output                            |
| ---------------------------- | -------------------------- | -------------------------------------------------------- | ------------------------------------- | --------------------------------- |
| `git submodule add URL PATH` | Add a submodule            | `git submodule add https://example.com/lib.git libs/lib` | Parent branch unchanged until commit  | `.gitmodules` and gitlink created |
| `git status`                 | Show submodule addition    | `git status`                                             | Working tree modified                 | New submodule                     |
| `git add .gitmodules PATH`   | Stage submodule metadata   | `git add .gitmodules libs/lib`                           | Working tree → index                  | Staged changes                    |
| `git commit`                 | Record submodule reference | `git commit -m "Add library"`                            | Index → new parent commit             | Commit created                    |
| `git push`                   | Publish parent commit      | `git push`                                               | Local branch → remote branch          | Push result                       |

---

# 19.6 Cloning a Repository With Submodules

A normal clone:

```bash
git clone https://example.com/application.git
```

does not necessarily initialize all submodules.

Use:

```bash
git clone --recurse-submodules https://example.com/application.git
```

Example:

```bash
git clone --recurse-submodules https://github.com/example/application.git
```

This:

1. Clones the parent repository.
2. Reads `.gitmodules`.
3. Initializes submodules.
4. Clones the submodule repositories.
5. Checks out the commits recorded by the parent.

For projects using nested submodules:

```bash
git clone --recurse-submodules --depth 1 <URL>
```

Be aware that shallow cloning and submodules can require additional configuration depending on repository history.

---

# 19.7 Initializing Submodules

If the repository was cloned normally:

```bash
git clone <URL>
cd <repository>
```

Initialize:

```bash
git submodule init
```

Then fetch and checkout the recorded commits:

```bash
git submodule update
```

Combined:

```bash
git submodule update --init
```

For nested submodules:

```bash
git submodule update --init --recursive
```

The recursive form is generally the safest choice for repositories containing nested submodules.

---

# 19.8 Updating Submodules

Update initialized submodules to the commits recorded by the parent repository:

```bash
git submodule update
```

Initialize missing ones at the same time:

```bash
git submodule update --init
```

Recursively:

```bash
git submodule update --init --recursive
```

Update to the latest configured remote-tracking commit:

```bash
git submodule update --remote
```

Recursively:

```bash
git submodule update --remote --recursive
```

---

# 19.9 Checking Submodule Status

Use:

```bash
git submodule status
```

Example:

```text
 abc1234 libs/library
```

A prefix can indicate special state.

Common indicators include:

```text
-   Submodule not initialized
+   Submodule HEAD differs from recorded commit
U   Merge conflict
```

Check recursively:

```bash
git submodule status --recursive
```

---

| Command                            | Description                   | Example                            | Branch State Before and After command | Output             |
| ---------------------------------- | ----------------------------- | ---------------------------------- | ------------------------------------- | ------------------ |
| `git submodule status`             | Show submodule commits        | `git submodule status`             | Unchanged                             | Commit and path    |
| `git submodule status --recursive` | Include nested submodules     | `git submodule status --recursive` | Unchanged                             | Recursive status   |
| `git status`                       | Show parent/submodule changes | `git status`                       | Unchanged                             | Working-tree state |

---

# 19.10 Entering a Submodule

A submodule is a separate Git repository.

Enter it:

```bash
cd libs/library
```

Check:

```bash
git status
```

Check branch:

```bash
git branch --show-current
```

Check commit:

```bash
git rev-parse HEAD
```

Important: a submodule checked out by `git submodule update` is commonly in a **detached HEAD** state.

Example:

```text
HEAD detached at abc1234
```

This is normal for a submodule pinned to a specific parent-repository commit.

---

# 19.11 Pulling Changes With Submodules

A normal pull:

```bash
git pull
```

updates the parent repository.

To also update submodules:

```bash
git pull --recurse-submodules
```

You can configure this behavior:

```bash
git config submodule.recurse true
```

Then many Git commands automatically recurse into submodules where supported.

After pulling, check:

```bash
git submodule status
```

---

# 19.12 Updating a Submodule to a New Commit

Enter the submodule:

```bash
cd libs/library
```

Fetch:

```bash
git fetch origin
```

Checkout the desired branch:

```bash
git switch main
```

Update:

```bash
git pull
```

Return to the parent:

```bash
cd ../..
```

Check:

```bash
git status
```

The parent repository now sees that the submodule points to a different commit.

Stage the new gitlink:

```bash
git add libs/library
```

Commit:

```bash
git commit -m "Update library submodule"
```

Push:

```bash
git push
```

The parent repository now records the new submodule commit.

---

# 19.13 Updating a Submodule to a Branch

You can explicitly request a branch:

```bash
git submodule update --remote --merge
```

To configure a branch in `.gitmodules`:

```ini
[submodule "libs/library"]
    path = libs/library
    url = https://example.com/library.git
    branch = main
```

Then:

```bash
git submodule update --remote
```

This updates the submodule according to its configured remote-tracking branch.

Afterward:

```bash
git status
```

If the gitlink changed:

```bash
git add libs/library
git commit -m "Update library submodule"
```

---

# 19.14 Tracking a Remote Branch

Set a branch:

```bash
git config -f .gitmodules submodule.libs/library.branch main
```

Synchronize local configuration:

```bash
git submodule sync
```

Then:

```bash
git submodule update --remote
```

Check:

```bash
git diff --submodule
```

Commit the resulting gitlink update:

```bash
git add .gitmodules libs/library
git commit -m "Track library main branch"
```

---

# 19.15 Recursive Submodules

For nested repositories:

```text
application
└── library-a
    └── library-b
```

Use:

```bash
git submodule update --init --recursive
```

Status:

```bash
git submodule status --recursive
```

Fetch:

```bash
git fetch --recurse-submodules
```

Pull:

```bash
git pull --recurse-submodules
```

Update:

```bash
git submodule update --remote --recursive
```

---

# 19.16 Nested Submodules

A nested submodule is a submodule inside another submodule.

Example:

```text
main/
└── libs/
    └── framework/
        └── dependencies/
            └── utility/
```

Initialize:

```bash
git submodule update --init --recursive
```

Inspect:

```bash
git submodule status --recursive
```

A recursive workflow is preferable when the dependency tree contains multiple submodule levels.

---

# 19.17 Removing a Submodule

Modern Git can remove a submodule with:

```bash
git submodule deinit -f -- libs/library
git rm -f libs/library
```

Then remove any remaining configuration if necessary.

Inspect:

```bash
git status
```

Commit:

```bash
git commit -m "Remove library submodule"
```

The operation should remove:

```text
libs/library
```

and update:

```text
.gitmodules
```

---

# 19.18 Deinitializing a Submodule

Deinitialize:

```bash
git submodule deinit libs/library
```

Force:

```bash
git submodule deinit -f -- libs/library
```

This removes the submodule's working-tree checkout/configuration while retaining the repository data in Git's internal storage.

Reinitialize later:

```bash
git submodule update --init libs/library
```

---

# 19.19 Synchronizing Submodule URLs

If `.gitmodules` changes:

```bash
git submodule sync
```

Recursively:

```bash
git submodule sync --recursive
```

Then:

```bash
git submodule update --init --recursive
```

Example:

```bash
git submodule sync --recursive
git submodule update --init --recursive
```

This is especially useful after changing:

```text
HTTPS → SSH
old host → new host
old repository URL → new repository URL
```

---

# 19.20 Submodule Summary

Use:

```bash
git submodule summary
```

Example:

```bash
git submodule summary
```

This summarizes commits between the parent repository's recorded submodule commit and the current submodule state.

Useful with:

```bash
git diff --submodule
```

---

# 19.21 Inspecting Submodule Changes

Normal diff:

```bash
git diff
```

Submodule-aware diff:

```bash
git diff --submodule
```

More detailed:

```bash
git diff --submodule=log
```

Example:

```bash
git diff --submodule=log
```

This can show commits that the submodule moved between.

For staged changes:

```bash
git diff --cached --submodule
```

---

# 19.22 Fetching Submodule Repositories

Fetch parent and submodules:

```bash
git fetch --recurse-submodules
```

Fetch recursively:

```bash
git fetch --recurse-submodules=on-demand
```

Fetch all configured submodules:

```bash
git submodule foreach 'git fetch'
```

Recursive:

```bash
git submodule foreach --recursive 'git fetch'
```

The `foreach` command executes a shell command inside each checked-out submodule.

---

# 19.23 Pushing With Submodules

A common problem occurs when the parent repository points to a submodule commit that has not been pushed to the submodule's remote.

Before pushing the parent:

```bash
git submodule status
```

Push the submodule:

```bash
cd libs/library
git push
```

Return:

```bash
cd ../..
```

Then push the parent:

```bash
git push
```

Git can also help detect this situation with recursive push configuration.

---

# 19.24 `push.recurseSubmodules`

Configure:

```bash
git config push.recurseSubmodules check
```

Possible values include:

```text
check
on-demand
no
```

`check` verifies that submodule commits referenced by the superproject are available from a remote.

`on-demand` attempts to push required submodule commits before pushing the superproject.

Example:

```bash
git config push.recurseSubmodules on-demand
```

Then:

```bash
git push
```

---

# 19.25 `fetch.recurseSubmodules`

Configure recursive fetching:

```bash
git config fetch.recurseSubmodules true
```

Or:

```bash
git config fetch.recurseSubmodules on-demand
```

Then:

```bash
git fetch
```

Git can fetch submodule repositories according to the configured policy.

---

# 19.26 `submodule.recurse`

Enable recursive behavior:

```bash
git config submodule.recurse true
```

Check:

```bash
git config --get submodule.recurse
```

Disable:

```bash
git config --unset submodule.recurse
```

This can simplify workflows in repositories where submodules are an integral part of the project.

---

# 19.27 Submodule Configuration

List configuration:

```bash
git config --list
```

Filter:

```bash
git config --list | grep submodule
```

Read a specific value:

```bash
git config --get submodule.recurse
```

Read `.gitmodules`:

```bash
git config -f .gitmodules --list
```

Example:

```bash
git config -f .gitmodules --get-regexp '^submodule\.'
```

---

# 19.28 Relative Submodule URLs

A submodule can use a relative URL.

Example:

```ini
[submodule "libs/library"]
    path = libs/library
    url = ../library.git
```

This can be useful when multiple repositories are hosted under the same server or organization.

However, relative URLs should be used deliberately because changing the parent repository's remote location can change how the relative URL resolves.

After URL changes:

```bash
git submodule sync
```

---

# 19.29 Local Development With Submodules

A recommended workflow:

```bash
git clone --recurse-submodules <URL>
cd <project>
```

Check:

```bash
git status
git submodule status
```

Work inside the submodule:

```bash
cd libs/library
git switch main
```

Make changes:

```bash
git add .
git commit -m "Implement feature"
git push
```

Return to parent:

```bash
cd ../..
```

The parent now sees a changed submodule commit:

```bash
git status
```

Record it:

```bash
git add libs/library
git commit -m "Update library dependency"
git push
```

---

# 19.30 CI/CD With Submodules

CI systems must explicitly support submodules.

A typical clone operation is:

```bash
git clone --recurse-submodules <repository-url>
```

Or after cloning:

```bash
git submodule update --init --recursive
```

Before building:

```bash
git submodule status --recursive
```

For deterministic builds, prefer the commits recorded by the parent repository rather than automatically following the latest branch.

A CI pipeline should generally use:

```bash
git submodule update --init --recursive
```

rather than:

```bash
git submodule update --remote
```

unless the pipeline intentionally tests the latest dependency versions.

---

# 19.31 Troubleshooting Submodules

## Submodule Directory Is Empty

Run:

```bash
git submodule update --init
```

For nested submodules:

```bash
git submodule update --init --recursive
```

---

## Wrong Submodule URL

Check:

```bash
cat .gitmodules
```

Synchronize:

```bash
git submodule sync --recursive
```

Then:

```bash
git submodule update --init --recursive
```

---

## Submodule Shows Modified

Check:

```bash
git status
```

Then enter:

```bash
cd libs/library
git status
```

The submodule itself may contain uncommitted changes.

---

## Submodule Is on the Wrong Commit

From the parent:

```bash
git submodule update
```

This checks out the commit recorded by the parent repository.

---

## Parent Shows a Changed Submodule

Check:

```bash
git diff --submodule
```

If the new commit is intentional:

```bash
git add libs/library
git commit -m "Update library submodule"
```

---

# 19.32 Common Submodule Problems

### Problem: Clone Does Not Include Dependencies

Solution:

```bash
git clone --recurse-submodules <URL>
```

or:

```bash
git submodule update --init --recursive
```

### Problem: Submodule URL Changed

Solution:

```bash
git submodule sync --recursive
```

### Problem: Submodule Has Detached HEAD

This is often normal:

```text
HEAD detached at <commit>
```

The parent repository intentionally pins the submodule to a commit.

If you want to develop inside the submodule:

```bash
cd libs/library
git switch main
```

### Problem: Parent Cannot Push

Check whether the referenced submodule commit exists on its remote.

Configure:

```bash
git config push.recurseSubmodules on-demand
```

Then:

```bash
git push
```

### Problem: Submodule Contains Uncommitted Work

Enter:

```bash
cd libs/library
git status
```

Commit, stash, or discard the changes as appropriate.

---

# 19.33 Submodule Security Considerations

Submodules introduce another source of repository configuration.

Before executing:

```bash
git submodule update --init --recursive
```

review:

```bash
cat .gitmodules
```

Pay attention to:

* Repository URLs
* Unexpected hosts
* SSH vs HTTPS
* Relative URLs
* Nested submodules
* Repository ownership
* CI credentials
* Authentication requirements

A submodule can introduce external code into a build.

For security-sensitive projects:

```bash
git submodule status --recursive
```

and:

```bash
git diff --submodule
```

should be part of dependency review.

Do not automatically trust an updated submodule simply because the parent repository changed its gitlink.

---

# 19.34 High-Value Submodule Commands

| Command                                       | Description                            | Example                                                  | Branch State Before and After command          | Output                    |
| --------------------------------------------- | -------------------------------------- | -------------------------------------------------------- | ---------------------------------------------- | ------------------------- |
| `git submodule add URL PATH`                  | Add repository as submodule            | `git submodule add https://example.com/lib.git libs/lib` | Parent working tree modified                   | New submodule             |
| `git submodule status`                        | Show current submodule commits         | `git submodule status`                                   | Unchanged                                      | Commit/path information   |
| `git submodule status --recursive`            | Show nested submodules                 | `git submodule status --recursive`                       | Unchanged                                      | Recursive status          |
| `git submodule init`                          | Initialize registered submodules       | `git submodule init`                                     | Submodule configuration initialized            | Initialization output     |
| `git submodule update`                        | Checkout recorded commits              | `git submodule update`                                   | Submodule HEADs updated                        | Checkout output           |
| `git submodule update --init`                 | Initialize and update                  | `git submodule update --init`                            | Missing submodules populated                   | Update output             |
| `git submodule update --init --recursive`     | Initialize nested submodules           | `git submodule update --init --recursive`                | Dependency tree populated                      | Recursive update          |
| `git submodule update --remote`               | Update toward configured remote branch | `git submodule update --remote`                          | Submodule commit may change                    | Update output             |
| `git submodule update --remote --merge`       | Update remote and merge                | `git submodule update --remote --merge`                  | Submodule branch updated                       | Merge output              |
| `git submodule sync`                          | Synchronize URLs                       | `git submodule sync`                                     | Config updated                                 | Synchronization output    |
| `git submodule sync --recursive`              | Synchronize nested URLs                | `git submodule sync --recursive`                         | Config updated                                 | Recursive synchronization |
| `git submodule foreach CMD`                   | Run command in each submodule          | `git submodule foreach 'git status'`                     | Depends on command                             | Per-submodule output      |
| `git submodule foreach --recursive CMD`       | Run recursively                        | `git submodule foreach --recursive 'git fetch'`          | Depends on command                             | Recursive output          |
| `git submodule summary`                       | Summarize submodule changes            | `git submodule summary`                                  | Unchanged                                      | Commit summary            |
| `git submodule deinit PATH`                   | Deinitialize submodule                 | `git submodule deinit libs/lib`                          | Working copy removed from active configuration | Deinitialization          |
| `git submodule deinit -f PATH`                | Force deinitialization                 | `git submodule deinit -f libs/lib`                       | Submodule deinitialized                        | Deinitialization          |
| `git rm PATH`                                 | Remove submodule path                  | `git rm libs/lib`                                        | Parent working tree/index modified             | Removal                   |
| `git fetch --recurse-submodules`              | Fetch parent and submodules            | `git fetch --recurse-submodules`                         | Repositories receive objects                   | Fetch output              |
| `git pull --recurse-submodules`               | Pull parent and submodules             | `git pull --recurse-submodules`                          | Parent and submodule states updated            | Pull output               |
| `git diff --submodule`                        | Show submodule differences             | `git diff --submodule`                                   | Unchanged                                      | Submodule diff            |
| `git diff --submodule=log`                    | Show submodule commits                 | `git diff --submodule=log`                               | Unchanged                                      | Commit log                |
| `git config push.recurseSubmodules on-demand` | Push required submodule commits        | `git config push.recurseSubmodules on-demand`            | Configuration changed                          | No output                 |
| `git config fetch.recurseSubmodules true`     | Recursively fetch submodules           | `git config fetch.recurseSubmodules true`                | Configuration changed                          | No output                 |
| `git config submodule.recurse true`           | Enable recursive Git behavior          | `git config submodule.recurse true`                      | Configuration changed                          | No output                 |

---

# 19.35 Submodule Cheat Sheet

## Clone With Submodules

```bash
git clone --recurse-submodules <URL>
```

## Initialize Existing Clone

```bash
git submodule update --init --recursive
```

## Check Status

```bash
git submodule status --recursive
```

## Update Recorded Commits

```bash
git submodule update --recursive
```

## Update Remote Branches

```bash
git submodule update --remote --recursive
```

## Synchronize URLs

```bash
git submodule sync --recursive
```

## Fetch Submodules

```bash
git fetch --recurse-submodules
```

## Pull Submodules

```bash
git pull --recurse-submodules
```

## Show Submodule Changes

```bash
git diff --submodule
```

## Show Submodule Commit Log

```bash
git diff --submodule=log
```

## Execute Command in Every Submodule

```bash
git submodule foreach 'git status'
```

## Execute Recursively

```bash
git submodule foreach --recursive 'git status'
```

## Add a Submodule

```bash
git submodule add <URL> <PATH>
git add .gitmodules <PATH>
git commit -m "Add submodule"
git push
```

## Update a Submodule

```bash
cd <submodule>
git switch main
git pull

cd ../..

git add <submodule>
git commit -m "Update submodule"
git push
```

## Configure Recursive Push

```bash
git config push.recurseSubmodules on-demand
```

## Configure Recursive Fetch

```bash
git config fetch.recurseSubmodules true
```

## Enable Recursive Submodule Operations

```bash
git config submodule.recurse true
```

## Remove a Submodule

```bash
git submodule deinit -f -- <PATH>
git rm -f <PATH>
git commit -m "Remove submodule"
```

---

# Essential Mental Model

The most important concept is:

```text
                 Parent Repository
                        |
                        |
                  .gitmodules
                        |
                        v
                 libs/library
                        |
                        v
                 Specific Commit
                     abc123
```

The parent repository does **not** automatically track the latest state of the submodule.

It tracks a specific commit:

```text
Parent
  |
  +-- libs/library ---> abc123
```

If the submodule moves to:

```text
def456
```

the parent repository sees:

```text
libs/library
    abc123 -> def456
```

You must commit that new gitlink in the parent repository:

```bash
git add libs/library
git commit -m "Update library submodule"
```

This distinction is fundamental to working correctly with Git submodules.

---

# Recommended Daily Workflow

```bash
# Clone
git clone --recurse-submodules <URL>

cd <project>

# Check dependencies
git submodule status --recursive

# Update according to parent repository
git submodule update --init --recursive

# Work on the project
git status

# Before committing
git diff --submodule

# Commit parent changes
git add .
git commit -m "Implement feature"

# Push
git push
```

For dependency updates:

```bash
git submodule update --remote --recursive
git diff --submodule
git add .
git commit -m "Update dependencies"
git push
```

---

## Next Part

**Next file:** `20-worktrees.md`

[Next: Worktrees](20-worktrees.md)
