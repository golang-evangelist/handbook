# 6. Branching

This chapter covers Git branches, branch creation, deletion, renaming, switching, tracking branches, upstream configuration, branch inspection, and common developer/DevOps branching workflows.

---

## Table of Contents

* [6.1 Branch Model](#61-branch-model)
* [6.2 List Branches](#62-list-branches)
* [6.3 Create Branches](#63-create-branches)
* [6.4 Switch Branches](#64-switch-branches)
* [6.5 Create and Switch](#65-create-and-switch)
* [6.6 Create Branch from a Commit](#66-create-branch-from-a-commit)
* [6.7 Create Branch from Another Branch](#67-create-branch-from-another-branch)
* [6.8 Create Branch from a Tag](#68-create-branch-from-a-tag)
* [6.9 Remote-Tracking Branches](#69-remote-tracking-branches)
* [6.10 Upstream Branches](#610-upstream-branches)
* [6.11 Rename Branches](#611-rename-branches)
* [6.12 Delete Branches](#612-delete-branches)
* [6.13 Force Delete Branches](#613-force-delete-branches)
* [6.14 Copy Branches](#614-copy-branches)
* [6.15 Move Branches](#615-move-branches)
* [6.16 Branch Tracking](#616-branch-tracking)
* [6.17 Branch Configuration](#617-branch-configuration)
* [6.18 Branch Inspection](#618-branch-inspection)
* [6.19 Branch Commit Relationships](#619-branch-commit-relationships)
* [6.20 Branch Containment](#620-branch-containment)
* [6.21 Branch Sorting](#621-branch-sorting)
* [6.22 Branch Formatting](#622-branch-formatting)
* [6.23 Branch Workflows](#623-branch-workflows)
* [6.24 DevOps and CI Workflows](#624-devops-and-ci-workflows)
* [6.25 High-Value Branch Commands](#625-high-value-branch-commands)

---

# 6.1 Branch Model

A Git branch is essentially a movable reference pointing to a commit.

Example:

```text
A---B---C  main
         \
          D---E  feature/login
```

Here:

```text
main
  ↓
  C

feature/login
  ↓
  E
```

The branch names do not contain the complete history.

They point to commits, and Git follows the commit graph to determine history.

---

## HEAD

`HEAD` identifies the currently checked-out state.

Normally:

```text
HEAD
 ↓
main
 ↓
C
```

When switching to a feature branch:

```text
HEAD
 ↓
feature/login
 ↓
E
```

---

## Branch creation does not automatically switch branches

For example:

```bash
git branch feature/login
```

creates the branch but leaves you on the current branch.

To create and switch:

```bash
git switch -c feature/login
```

---

# 6.2 List Branches

## List local branches

```bash
git branch
```

Example:

```text
  develop
* main
  feature/login
```

The `*` identifies the current branch.

| Command             | Description                             | Example             | Branch State Before and After command | Output            |
| ------------------- | --------------------------------------- | ------------------- | ------------------------------------- | ----------------- |
| `git branch`        | List local branches                     | `git branch`        | `main` → `main`                       | Branch names      |
| `git branch --list` | Explicit branch listing                 | `git branch --list` | `main` → `main`                       | Branch names      |
| `git branch -v`     | Show latest commit                      | `git branch -v`     | `main` → `main`                       | Branch + commit   |
| `git branch -vv`    | Show upstream information               | `git branch -vv`    | `main` → `main`                       | Branch + upstream |
| `git branch -a`     | List local and remote-tracking branches | `git branch -a`     | `main` → `main`                       | All branches      |
| `git branch -r`     | List remote-tracking branches           | `git branch -r`     | `main` → `main`                       | Remote branches   |

---

## List branches with latest commits

```bash
git branch -v
```

Example:

```text
  develop        7ab1234 Add API
* main           4cd5678 Update README
  feature/login  9ef0123 Add login
```

---

## List branches with upstream status

```bash
git branch -vv
```

Example:

```text
  develop        7ab1234 [origin/develop] Add API
* main           4cd5678 [origin/main] Update README
  feature/login  9ef0123 [origin/feature/login: ahead 2] Add login
```

The upstream information can show:

```text
ahead
behind
ahead/behind
gone
```

---

# 6.3 Create Branches

## Create a branch

```bash
git branch feature/login
```

Before:

```text
A---B---C  main
```

After:

```text
A---B---C  main
         \
          feature/login
```

Both branches initially point to the same commit.

You remain on `main`.

---

## Create branch at current HEAD

```bash
git branch feature/api
```

---

## Create branch from a specific commit

```bash
git branch feature/api abc1234
```

---

## Create branch from another branch

```bash
git branch feature/api develop
```

The new branch starts at the current tip of `develop`.

---

## Create branch from a tag

```bash
git branch release-fix v2.0.0
```

---

# 6.4 Switch Branches

## Switch to existing branch

```bash
git switch feature/login
```

Before:

```text
HEAD
 ↓
main
 ↓
C
```

After:

```text
HEAD
 ↓
feature/login
 ↓
E
```

Your working tree is updated to match the target branch.

---

## Older syntax: `git checkout`

```bash
git checkout feature/login
```

`git checkout` can perform many operations, but modern Git generally recommends:

```bash
git switch
```

for branch switching.

---

## Switch to previous branch

```bash
git switch -
```

This switches to the previously checked-out branch.

Example:

```text
main
  ↓
git switch feature/login

feature/login
  ↓
git switch -

main
```

---

# 6.5 Create and Switch

## Create and switch using `git switch`

```bash
git switch -c feature/login
```

Equivalent:

```bash
git switch --create feature/login
```

Before:

```text
A---B---C  main
```

After:

```text
A---B---C  main
         \
          D  feature/login
```

`HEAD` points to `feature/login`.

---

## Create and switch using `git checkout`

```bash
git checkout -b feature/login
```

This is the traditional equivalent.

---

| Command                    | Description                  | Example                         | Branch State Before and After command | Output            |
| -------------------------- | ---------------------------- | ------------------------------- | ------------------------------------- | ----------------- |
| `git switch <branch>`      | Switch to branch             | `git switch develop`            | `main` → `develop`                    | Usually no output |
| `git switch -`             | Switch to previous branch    | `git switch -`                  | `feature` → previous branch           | Usually no output |
| `git switch -c <branch>`   | Create and switch            | `git switch -c feature/login`   | `main` → `feature/login`              | Usually no output |
| `git checkout <branch>`    | Traditional branch switching | `git checkout develop`          | `main` → `develop`                    | Usually no output |
| `git checkout -b <branch>` | Traditional create + switch  | `git checkout -b feature/login` | `main` → `feature/login`              | Usually no output |

---

# 6.6 Create Branch from a Commit

```bash
git switch -c feature/fix abc1234
```

This creates:

```text
feature/fix
```

at commit:

```text
abc1234
```

and switches to it.

Example:

```text
A---B---C---D  main
     \
      X  old commit
```

After:

```bash
git switch -c recovery X
```

the graph becomes conceptually:

```text
A---B---C---D  main
     \
      X  recovery
```

---

# 6.7 Create Branch from Another Branch

```bash
git switch -c feature/api develop
```

This:

1. Creates `feature/api`.
2. Starts it at `develop`.
3. Switches `HEAD` to `feature/api`.

Example:

```text
A---B---C  main
     \
      D---E  develop
             \
              feature/api
```

---

# 6.8 Create Branch from a Tag

```bash
git switch -c hotfix v2.0.0
```

This creates a new branch starting at the commit referenced by `v2.0.0`.

Useful for:

```text
release maintenance
hotfixes
legacy support
patch releases
```

---

# 6.9 Remote-Tracking Branches

Remote-tracking branches look like:

```text
origin/main
origin/develop
origin/feature/login
```

They represent your local knowledge of branches on a remote repository.

---

## List remote branches

```bash
git branch -r
```

Example:

```text
origin/HEAD -> origin/main
origin/main
origin/develop
origin/feature/login
```

---

## List local and remote branches

```bash
git branch -a
```

Example:

```text
* main
  develop
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/develop
```

---

## Remote-tracking branch is not a local branch

This:

```text
origin/main
```

is not the same object as:

```text
main
```

You can have:

```text
main
origin/main
```

pointing to different commits.

Example:

```text
A---B---C  main
     \
      D---E  origin/main
```

This means your local branch and your local remote-tracking reference have diverged.

---

# 6.10 Upstream Branches

An upstream branch associates a local branch with a remote-tracking branch.

For example:

```text
feature/login
        │
        └── origin/feature/login
```

This allows commands such as:

```bash
git pull
git push
git status
```

to determine the appropriate remote branch automatically.

---

## Set upstream while pushing

```bash
git push -u origin feature/login
```

The `-u` option establishes the upstream relationship.

Equivalent:

```bash
git push --set-upstream origin feature/login
```

Afterward:

```bash
git push
```

and:

```bash
git pull
```

can normally operate without explicitly specifying the branch.

---

## Set upstream to an existing remote branch

```bash
git branch --set-upstream-to=origin/main main
```

Short form:

```bash
git branch -u origin/main main
```

---

## Set upstream for current branch

```bash
git branch -u origin/main
```

Git uses the current branch as the local branch.

---

## Remove upstream

```bash
git branch --unset-upstream
```

---

| Command                             | Description                 | Example                             | Branch State Before and After command | Output               |
| ----------------------------------- | --------------------------- | ----------------------------------- | ------------------------------------- | -------------------- |
| `git push -u origin <branch>`       | Push and set upstream       | `git push -u origin feature/login`  | `feature/login` → `feature/login`     | Push result          |
| `git branch -u origin/main`         | Set current branch upstream | `git branch -u origin/main`         | `main` → `main`                       | Tracking information |
| `git branch -u origin/main feature` | Set branch upstream         | `git branch -u origin/main feature` | `feature` → `feature`                 | Tracking information |
| `git branch --unset-upstream`       | Remove upstream             | `git branch --unset-upstream`       | `feature` → `feature`                 | Usually no output    |

---

# 6.11 Rename Branches

## Rename current branch

```bash
git branch -m new-name
```

Example:

```bash
git branch -m feature/login-api
```

Before:

```text
* feature/login
```

After:

```text
* feature/login-api
```

---

## Rename another local branch

```bash
git branch -m old-name new-name
```

---

## Force rename

```bash
git branch -M new-name
```

or:

```bash
git branch -M old-name new-name
```

`-M` forces the rename if the destination branch already exists.

Use carefully.

---

## Rename `master` to `main`

```bash
git branch -m master main
```

This changes the local branch name.

If the remote repository also needs updating, the remote operation is separate:

```bash
git push -u origin main
```

Then, depending on the hosting platform and repository configuration, the default branch should be updated before removing the old remote branch.

---

# 6.12 Delete Branches

## Delete merged local branch

```bash
git branch -d feature/login
```

`-d` performs a safety check.

Git normally refuses to delete a branch if its commits have not been merged into another branch.

Example:

```text
Deleted branch feature/login (was abc1234).
```

---

## Delete multiple branches

```bash
git branch -d feature/login feature/api feature/docs
```

---

## Delete remote-tracking reference

```bash
git branch -d -r origin/feature/login
```

This removes your local remote-tracking reference.

It does not necessarily delete the branch on the remote server.

---

## Delete a remote branch

Use:

```bash
git push origin --delete feature/login
```

This requests deletion of the branch on the remote.

Equivalent traditional syntax:

```bash
git push origin :feature/login
```

The `--delete` form is much clearer and preferred.

---

# 6.13 Force Delete Branches

## Force-delete local branch

```bash
git branch -D feature/login
```

This deletes the branch even if Git believes it contains unmerged commits.

### Warning

The commits may become difficult to reach if no other reference points to them.

Before force-deleting, inspect:

```bash
git log feature/login
```

or:

```bash
git log --oneline --decorate feature/login
```

---

## Force-delete multiple branches

```bash
git branch -D feature/login feature/api
```

---

# 6.14 Copy Branches

Git can copy a branch reference.

## Copy a branch

```bash
git branch -c feature/login feature/login-backup
```

This creates:

```text
feature/login-backup
```

at the same commit as:

```text
feature/login
```

It also copies relevant branch configuration.

---

## Force copy

```bash
git branch -C feature/login feature/login-backup
```

This overwrites an existing destination branch.

Use with caution.

---

# 6.15 Move Branches

A branch can be moved using:

```bash
git branch -m
```

Example:

```bash
git branch -m feature/login feature/authentication
```

Before:

```text
feature/login → C
```

After:

```text
feature/authentication → C
```

The commit graph is unchanged.

Only the branch reference name changes.

---

# 6.16 Branch Tracking

## Inspect tracking information

```bash
git branch -vv
```

Example:

```text
* feature/login  abc1234 [origin/feature/login: ahead 2, behind 1] Add login
  main           def5678 [origin/main] Update docs
```

---

## Show upstream branch

```bash
git rev-parse --abbrev-ref --symbolic-full-name '@{upstream}'
```

Possible output:

```text
origin/feature/login
```

---

## Show upstream commit

```bash
git rev-parse '@{upstream}'
```

Possible output:

```text
abc123456789...
```

---

## Check whether branch has upstream

```bash
git rev-parse --abbrev-ref '@{upstream}'
```

If no upstream exists, Git returns an error.

---

# 6.17 Branch Configuration

## Show current branch configuration

```bash
git config --get-regexp '^branch\.'
```

Example:

```text
branch.main.remote origin
branch.main.merge refs/heads/main
branch.feature/login.remote origin
branch.feature/login.merge refs/heads/feature/login
```

---

## Show remote of current branch

```bash
git config --get branch.$(git branch --show-current).remote
```

Example:

```text
origin
```

---

## Show merge target

```bash
git config --get branch.$(git branch --show-current).merge
```

Example:

```text
refs/heads/main
```

---

# 6.18 Branch Inspection

## Show current branch

```bash
git branch --show-current
```

Example:

```text
feature/login
```

This is often preferable to parsing:

```bash
git branch
```

because it directly returns the branch name.

---

## Show branch containing HEAD

```bash
git symbolic-ref --short HEAD
```

Example:

```text
feature/login
```

---

## Show branch names with commits

```bash
git branch -v
```

---

## Show branches merged into current branch

```bash
git branch --merged
```

Example:

```text
* main
  feature/docs
  feature/login
```

These branches are candidates for deletion if they are no longer needed.

---

## Show branches not merged

```bash
git branch --no-merged
```

Example:

```text
  feature/payment
  feature/search
```

These branches contain commits not merged into the current branch.

---

# 6.19 Branch Commit Relationships

## Show branches containing a commit

```bash
git branch --contains abc1234
```

This answers:

> Which local branches contain this commit?

---

## Show remote branches containing a commit

```bash
git branch -r --contains abc1234
```

---

## Show all branches containing a commit

```bash
git branch -a --contains abc1234
```

---

## Show branches that do not contain a commit

```bash
git branch --no-contains abc1234
```

---

## Find branch containing current commit

```bash
git branch --contains HEAD
```

---

# 6.20 Branch Containment

Suppose the graph is:

```text
A---B---C---D  main
     \
      E---F  feature
```

Then:

```bash
git branch --contains F
```

will show:

```text
feature
```

but not necessarily `main`.

Conversely:

```bash
git branch --contains C
```

can show:

```text
main
```

because `C` is an ancestor of `main`.

This is useful when determining whether a commit has already been incorporated into a branch.

---

# 6.21 Branch Sorting

## Sort by latest commit

```bash
git branch --sort=-committerdate
```

Newest branches appear first.

---

## Sort oldest first

```bash
git branch --sort=committerdate
```

---

## Sort alphabetically

```bash
git branch --sort=refname
```

---

## Sort by author date

```bash
git branch --sort=-authordate
```

---

## Useful branch cleanup view

```bash
git branch --sort=-committerdate -vv
```

This shows:

```text
branch
latest commit
upstream
tracking status
```

with recently updated branches first.

---

# 6.22 Branch Formatting

Git provides `--format` for custom branch output.

## Branch names only

```bash
git branch --format='%(refname:short)'
```

Example:

```text
main
develop
feature/api
feature/login
```

---

## Branch and commit

```bash
git branch --format='%(refname:short) %(objectname:short)'
```

Example:

```text
main abc1234
develop def5678
feature/login 123abcd
```

---

## Branch and subject

```bash
git branch --format='%(refname:short) - %(subject)'
```

Example:

```text
main - Update documentation
feature/login - Add authentication
```

---

## Branch, commit, and date

```bash
git branch --format='%(refname:short) %(objectname:short) %(committerdate:short)'
```

This is useful for scripts and branch maintenance.

---

# 6.23 Branch Workflows

## Workflow A — Start a feature

```bash
git switch main
git pull --ff-only
git switch -c feature/login
```

Result:

```text
main
  \
   feature/login
```

`HEAD` is now on:

```text
feature/login
```

---

## Workflow B — Start a feature from updated main

```bash
git fetch origin
git switch main
git pull --ff-only
git switch -c feature/login
```

This reduces the chance of starting new work from stale local `main`.

---

## Workflow C — Publish feature branch

```bash
git switch -c feature/login
git push -u origin feature/login
```

After this:

```text
local:
feature/login

remote:
origin/feature/login

upstream:
feature/login → origin/feature/login
```

---

## Workflow D — Switch between branches

```bash
git switch main
git switch feature/login
git switch -
```

---

## Workflow E — Check branch state

```bash
git status -sb
git branch -vv
```

---

## Workflow F — Find merged branches

```bash
git switch main
git branch --merged
```

Potential cleanup:

```bash
git branch -d feature/login
```

---

## Workflow G — Find unmerged branches

```bash
git branch --no-merged
```

Before deleting anything:

```bash
git log --oneline feature/payment
```

---

## Workflow H — Create a branch from a release tag

```bash
git switch -c hotfix/2.0 v2.0.0
```

Then:

```bash
git commit -am "Fix release issue"
```

---

## Workflow I — Rename a feature branch

```bash
git branch -m feature/login feature/authentication
```

If the old branch has already been pushed:

```bash
git push origin --delete feature/login
git push -u origin feature/authentication
```

---

# 6.24 DevOps and CI Workflows

## Determine current branch in CI

```bash
git branch --show-current
```

However, some CI systems check out commits in detached `HEAD` state. In that situation:

```bash
git branch --show-current
```

may produce no output.

For the current commit:

```bash
git rev-parse HEAD
```

is more reliable.

---

## Determine branch reference

```bash
git symbolic-ref --short -q HEAD
```

If `HEAD` is detached, this can fail.

---

## Determine whether current branch is ahead of upstream

```bash
git status -sb
```

Example:

```text
## feature/login...origin/feature/login [ahead 2]
```

---

## Determine branch divergence

```bash
git rev-list --left-right --count HEAD...@{upstream}
```

Example:

```text
2    1
```

Meaning:

```text
2 commits only on HEAD
1 commit only on upstream
```

---

## Check whether branch is merged

```bash
git merge-base --is-ancestor feature/login main
```

Exit status:

```text
0 → feature/login is an ancestor of main
1 → it is not
```

This is useful in automation.

Example:

```bash
if git merge-base --is-ancestor feature/login main; then
    echo "Feature is merged"
else
    echo "Feature is not merged"
fi
```

---

## Determine whether current branch contains another branch

```bash
git merge-base --is-ancestor origin/main HEAD
```

If successful, `origin/main` is an ancestor of the current `HEAD`.

---

## List branches sorted by activity

```bash
git branch --sort=-committerdate -vv
```

This can help identify stale development branches.

---

# 6.25 High-Value Branch Commands

| Command                                    | Description                     | Example                                    | Branch State Before and After command | Output           |
| ------------------------------------------ | ------------------------------- | ------------------------------------------ | ------------------------------------- | ---------------- |
| `git branch`                               | List local branches             | `git branch`                               | `main` → `main`                       | Branch list      |
| `git branch -v`                            | Branches + latest commits       | `git branch -v`                            | `main` → `main`                       | Branch details   |
| `git branch -vv`                           | Branches + upstream status      | `git branch -vv`                           | `main` → `main`                       | Tracking details |
| `git branch -a`                            | Local + remote branches         | `git branch -a`                            | `main` → `main`                       | All branches     |
| `git branch -r`                            | Remote-tracking branches        | `git branch -r`                            | `main` → `main`                       | Remote branches  |
| `git branch <name>`                        | Create branch                   | `git branch feature/api`                   | `main` → `main`                       | Usually none     |
| `git switch <name>`                        | Switch branch                   | `git switch develop`                       | `main` → `develop`                    | Usually none     |
| `git switch -c <name>`                     | Create + switch                 | `git switch -c feature/api`                | `main` → `feature/api`                | Usually none     |
| `git switch -c <name> <start>`             | Create from starting point      | `git switch -c hotfix v2.0.0`              | `main` → `hotfix`                     | Usually none     |
| `git switch -`                             | Switch to previous branch       | `git switch -`                             | `feature` → previous branch           | Usually none     |
| `git checkout <name>`                      | Traditional switch              | `git checkout develop`                     | `main` → `develop`                    | Usually none     |
| `git checkout -b <name>`                   | Traditional create + switch     | `git checkout -b feature/api`              | `main` → `feature/api`                | Usually none     |
| `git branch --show-current`                | Show current branch             | `git branch --show-current`                | `main` → `main`                       | Branch name      |
| `git branch -m <new>`                      | Rename current branch           | `git branch -m main`                       | `master` → `main`                     | Usually none     |
| `git branch -m <old> <new>`                | Rename branch                   | `git branch -m old new`                    | `old` → `new`                         | Usually none     |
| `git branch -M <new>`                      | Force rename                    | `git branch -M main`                       | `master` → `main`                     | Usually none     |
| `git branch -d <name>`                     | Delete merged branch            | `git branch -d feature/api`                | `main` → `main`                       | Deletion message |
| `git branch -D <name>`                     | Force-delete branch             | `git branch -D feature/api`                | `main` → `main`                       | Deletion message |
| `git branch --merged`                      | List merged branches            | `git branch --merged`                      | `main` → `main`                       | Branch list      |
| `git branch --no-merged`                   | List unmerged branches          | `git branch --no-merged`                   | `main` → `main`                       | Branch list      |
| `git branch --contains <commit>`           | Find branches containing commit | `git branch --contains abc1234`            | `main` → `main`                       | Branch list      |
| `git branch --no-contains <commit>`        | Find branches without commit    | `git branch --no-contains abc1234`         | `main` → `main`                       | Branch list      |
| `git branch --set-upstream-to`             | Set upstream                    | `git branch -u origin/main`                | `main` → `main`                       | Tracking info    |
| `git branch --unset-upstream`              | Remove upstream                 | `git branch --unset-upstream`              | `main` → `main`                       | Usually none     |
| `git push -u origin <branch>`              | Publish + track branch          | `git push -u origin feature/api`           | `feature/api` → `feature/api`         | Push result      |
| `git push origin --delete <branch>`        | Delete remote branch            | `git push origin --delete feature/api`     | `feature/api` → `feature/api`         | Remote deletion  |
| `git branch -c <old> <new>`                | Copy branch                     | `git branch -c feature/api backup/api`     | `feature/api` → `feature/api`         | Usually none     |
| `git branch -C <old> <new>`                | Force-copy branch               | `git branch -C feature/api backup/api`     | `feature/api` → `feature/api`         | Usually none     |
| `git branch --sort=-committerdate`         | Sort branches by activity       | `git branch --sort=-committerdate`         | `main` → `main`                       | Sorted branches  |
| `git rev-parse --abbrev-ref '@{upstream}'` | Show upstream                   | `git rev-parse --abbrev-ref '@{upstream}'` | `feature` → `feature`                 | Upstream name    |
| `git merge-base --is-ancestor A B`         | Test ancestry                   | `git merge-base --is-ancestor main HEAD`   | `feature` → `feature`                 | Exit status      |

---

# Branch State Examples

## Before creating a feature

```text
A---B---C  main
         ↑
        HEAD
```

Command:

```bash
git switch -c feature/login
```

After:

```text
A---B---C  main
         \
          feature/login
          ↑
         HEAD
```

---

## After feature commits

```text
A---B---C  main
         \
          D---E  feature/login
               ↑
              HEAD
```

---

## After switching back to main

```bash
git switch main
```

State:

```text
A---B---C  main
         ↑
        HEAD
         \
          D---E  feature/login
```

The feature branch remains intact.

---

# Recommended Branch Naming

Common conventions include:

```text
feature/<name>
bugfix/<name>
hotfix/<name>
release/<version>
chore/<name>
docs/<name>
refactor/<name>
test/<name>
```

Examples:

```text
feature/user-authentication
bugfix/payment-timeout
hotfix/security-patch
release/2.4.0
chore/update-dependencies
docs/api-reference
refactor/database-layer
test/login-flow
```

The exact convention should follow the repository's team standards.

---

# Recommended Feature Branch Workflow

```bash
git fetch origin
git switch main
git pull --ff-only
git switch -c feature/user-authentication
```

Work:

```bash
git add -p
git commit -m "Add user authentication"
```

Publish:

```bash
git push -u origin feature/user-authentication
```

Review:

```bash
git fetch origin
git diff origin/main...HEAD
```

After the branch has been merged and is no longer needed:

```bash
git switch main
git pull --ff-only
git branch -d feature/user-authentication
```

If the remote branch still exists:

```bash
git push origin --delete feature/user-authentication
```

---

# Important Branch Safety Rules

### 1. Do not force-delete blindly

Before:

```bash
git branch -D feature/payment
```

inspect:

```bash
git log --oneline feature/payment
```

and:

```bash
git branch --contains <important-commit>
```

---

### 2. Do not assume `origin/main` is current

Update remote-tracking references first:

```bash
git fetch origin
```

Then compare:

```bash
git diff origin/main...HEAD
```

---

### 3. Do not confuse a local branch with a remote-tracking branch

These are separate references:

```text
main
origin/main
```

---

### 4. Use `git switch` for normal branch switching

Prefer:

```bash
git switch feature/login
```

and:

```bash
git switch -c feature/login
```

over the older:

```bash
git checkout feature/login
git checkout -b feature/login
```

when working with modern Git.

---

### 5. Check the current branch before destructive operations

```bash
git branch --show-current
```

Then inspect:

```bash
git status -sb
```

This simple habit prevents many accidental operations on the wrong branch.

---

## Next Part

**Next file:** `07-merging.md`

[Next: Merging](07-merging.md)
