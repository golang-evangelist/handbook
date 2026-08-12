# 9. Remote Repositories

This chapter covers Git remote repositories, remote configuration, fetching, pulling, pushing, remote-tracking branches, upstream branches, synchronization, remote inspection, pruning, renaming, deleting remote branches, authentication-related workflows, and common Developer, Software Engineer, and DevOps operations.

---

## Table of Contents

* [9.1 Remote Repository Concepts](#91-remote-repository-concepts)
* [9.2 Remote Names](#92-remote-names)
* [9.3 Add a Remote](#93-add-a-remote)
* [9.4 List Remotes](#94-list-remotes)
* [9.5 Inspect a Remote](#95-inspect-a-remote)
* [9.6 Change Remote URLs](#96-change-remote-urls)
* [9.7 Fetching](#97-fetching)
* [9.8 Fetch a Specific Branch](#98-fetch-a-specific-branch)
* [9.9 Fetch All Remotes](#99-fetch-all-remotes)
* [9.10 Pruning Remote-Tracking Branches](#910-pruning-remote-tracking-branches)
* [9.11 Pulling](#911-pulling)
* [9.12 Pull with Rebase](#912-pull-with-rebase)
* [9.13 Pull with Fast-Forward Only](#913-pull-with-fast-forward-only)
* [9.14 Pushing](#914-pushing)
* [9.15 Push a New Branch](#915-push-a-new-branch)
* [9.16 Upstream Branches](#916-upstream-branches)
* [9.17 Push Tags](#917-push-tags)
* [9.18 Delete Remote Branches](#918-delete-remote-branches)
* [9.19 Rename Remote Branches](#919-rename-remote-branches)
* [9.20 Remote-Tracking Branches](#920-remote-tracking-branches)
* [9.21 Compare Local and Remote Branches](#921-compare-local-and-remote-branches)
* [9.22 Resetting a Local Branch to Remote](#922-resetting-a-local-branch-to-remote)
* [9.23 Remote Synchronization Workflows](#923-remote-synchronization-workflows)
* [9.24 Force Push](#924-force-push)
* [9.25 Force Push with Lease](#925-force-push-with-lease)
* [9.26 Push Options](#926-push-options)
* [9.27 Fetch Options](#927-fetch-options)
* [9.28 Pull Options](#928-pull-options)
* [9.29 Remote Configuration](#929-remote-configuration)
* [9.30 Multiple Remotes](#930-multiple-remotes)
* [9.31 Fork Workflows](#931-fork-workflows)
* [9.32 Developer Workflows](#932-developer-workflows)
* [9.33 DevOps and CI Workflows](#933-devops-and-ci-workflows)
* [9.34 High-Value Remote Commands](#934-high-value-remote-commands)
* [9.35 Remote Repository Safety](#935-remote-repository-safety)

---

# 9.1 Remote Repository Concepts

A Git remote is a named reference to another Git repository.

The most common remote name is:

```text
origin
```

For example:

```text
Local repository
        |
        | origin
        v
Remote repository
```

A remote usually contains one or more branches:

```text
origin/main
origin/develop
origin/feature/login
```

These are called **remote-tracking branches**.

They are local references representing the state of branches on a remote repository.

---

## Local branch vs remote-tracking branch

A local branch:

```text
main
```

belongs to your local repository.

A remote-tracking branch:

```text
origin/main
```

is your local Git repository's recorded view of the remote's `main` branch.

The command:

```bash
git fetch origin
```

updates:

```text
origin/main
```

It does not normally modify your local:

```text
main
```

---

# 9.2 Remote Names

The default remote created by many clone operations is:

```text
origin
```

You can have multiple remotes:

```text
origin
upstream
company
backup
```

For example:

```text
origin   -> your fork
upstream -> main project
```

---

# 9.3 Add a Remote

Add a remote:

```bash
git remote add origin <URL>
```

Example:

```bash
git remote add origin git@github.com:example/project.git
```

Verify:

```bash
git remote -v
```

Example output:

```text
origin  git@github.com:example/project.git (fetch)
origin  git@github.com:example/project.git (push)
```

---

| Command                       | Description       | Example                                             | Branch State Before and After command | Output            |
| ----------------------------- | ----------------- | --------------------------------------------------- | ------------------------------------- | ----------------- |
| `git remote add <name> <url>` | Add a remote      | `git remote add origin git@github.com:user/app.git` | Local branches unchanged              | Usually no output |
| `git remote -v`               | Show remote URLs  | `git remote -v`                                     | No branch change                      | Remote URLs       |
| `git remote`                  | List remote names | `git remote`                                        | No branch change                      | `origin`          |
| `git remote get-url origin`   | Show remote URL   | `git remote get-url origin`                         | No branch change                      | URL               |

---

# 9.4 List Remotes

List remote names:

```bash
git remote
```

Example:

```text
origin
upstream
```

List URLs:

```bash
git remote -v
```

Example:

```text
origin    git@github.com:user/project.git (fetch)
origin    git@github.com:user/project.git (push)
upstream  git@github.com:company/project.git (fetch)
upstream  git@github.com:company/project.git (push)
```

---

# 9.5 Inspect a Remote

Use:

```bash
git remote show origin
```

Example output may include:

```text
* remote origin
  Fetch URL: git@github.com:user/project.git
  Push  URL: git@github.com:user/project.git
  HEAD branch: main
  Remote branches:
    main tracked
    develop tracked
```

This is useful for understanding:

* remote URLs
* remote HEAD
* tracked branches
* local branches configured for push/pull
* stale remote-tracking information

---

## Show remote references

```bash
git ls-remote origin
```

Example:

```text
abc1234...    HEAD
abc1234...    refs/heads/main
def5678...    refs/heads/develop
```

Unlike `git fetch`, this queries the remote without updating local remote-tracking branches.

---

| Command                     | Description                      | Example                     | Branch State Before and After command | Output                      |
| --------------------------- | -------------------------------- | --------------------------- | ------------------------------------- | --------------------------- |
| `git remote show origin`    | Show detailed remote information | `git remote show origin`    | No branch change                      | Remote configuration/status |
| `git ls-remote origin`      | List refs available on remote    | `git ls-remote origin`      | No branch change                      | Remote refs and object IDs  |
| `git remote get-url origin` | Show fetch/push URL              | `git remote get-url origin` | No branch change                      | URL                         |

---

# 9.6 Change Remote URLs

Change the remote URL:

```bash
git remote set-url origin <URL>
```

Example:

```bash
git remote set-url origin git@github.com:user/project.git
```

Verify:

```bash
git remote -v
```

---

## Change only the push URL

```bash
git remote set-url --push origin <URL>
```

Example:

```bash
git remote set-url --push origin git@github.com:user/project-mirror.git
```

This allows fetch and push to use different destinations.

---

## Add another URL

```bash
git remote set-url --add origin <URL>
```

This is an advanced configuration and should be used intentionally.

---

| Command                                  | Description       | Example                                                          | Branch State Before and After command | Output            |
| ---------------------------------------- | ----------------- | ---------------------------------------------------------------- | ------------------------------------- | ----------------- |
| `git remote set-url origin <url>`        | Change remote URL | `git remote set-url origin git@github.com:user/app.git`          | Branches unchanged                    | Usually no output |
| `git remote set-url --push origin <url>` | Change push URL   | `git remote set-url --push origin git@github.com:mirror/app.git` | Branches unchanged                    | Usually no output |
| `git remote set-url --add origin <url>`  | Add another URL   | `git remote set-url --add origin https://example.com/repo.git`   | Branches unchanged                    | Usually no output |

---

# 9.7 Fetching

The fundamental remote synchronization command is:

```bash
git fetch
```

Fetch downloads objects and updates remote-tracking references.

Example:

```bash
git fetch origin
```

Suppose:

```text
Remote:
A---B---C---D  main

Local:
A---B---C      main
              origin/main
```

After:

```bash
git fetch origin
```

the local remote-tracking branch becomes:

```text
A---B---C---D  origin/main
        |
        C      main
```

Your local `main` does not automatically move.

---

| Command             | Description                 | Example                    | Branch State Before and After command | Output        |
| ------------------- | --------------------------- | -------------------------- | ------------------------------------- | ------------- |
| `git fetch`         | Fetch default remote        | `git fetch`                | Local branches unchanged              | Fetch summary |
| `git fetch origin`  | Fetch from origin           | `git fetch origin`         | `origin/*` updated                    | Fetch summary |
| `git fetch --all`   | Fetch all remotes           | `git fetch --all`          | Remote-tracking branches updated      | Fetch summary |
| `git fetch --prune` | Fetch and remove stale refs | `git fetch --prune origin` | Remote-tracking refs cleaned          | Fetch summary |
| `git fetch --tags`  | Fetch tags                  | `git fetch --tags origin`  | Tags updated locally                  | Fetch summary |

---

# 9.8 Fetch a Specific Branch

Fetch one remote branch:

```bash
git fetch origin main
```

This updates the relevant remote-tracking information for that branch.

You can explicitly map a remote branch:

```bash
git fetch origin main:refs/remotes/origin/main
```

For normal development, the simpler form is preferred:

```bash
git fetch origin main
```

---

## Fetch a branch into a local branch

You can explicitly specify a destination:

```bash
git fetch origin main:main
```

This attempts to update the local `main` reference.

This is more advanced than normal fetch usage and can be subject to fast-forward restrictions.

---

# 9.9 Fetch All Remotes

If multiple remotes exist:

```bash
git fetch --all
```

Example:

```text
origin
upstream
backup
```

Git fetches from each configured remote.

---

## Fetch all and prune stale references

```bash
git fetch --all --prune
```

This is a useful periodic maintenance command.

---

# 9.10 Pruning Remote-Tracking Branches

Suppose a remote branch was deleted:

```text
origin/feature/old-login
```

but your local repository still contains:

```text
origin/feature/old-login
```

Run:

```bash
git fetch --prune
```

Git removes stale remote-tracking references.

---

## Prune one remote

```bash
git fetch origin --prune
```

or:

```bash
git remote prune origin
```

---

| Command                   | Description                                 | Example                   | Branch State Before and After command | Output              |
| ------------------------- | ------------------------------------------- | ------------------------- | ------------------------------------- | ------------------- |
| `git fetch --prune`       | Fetch and remove stale remote-tracking refs | `git fetch --prune`       | Stale `origin/*` refs removed         | Fetch/prune summary |
| `git remote prune origin` | Remove stale refs for origin                | `git remote prune origin` | Stale `origin/*` refs removed         | Deleted refs        |
| `git fetch --all --prune` | Fetch all remotes and prune                 | `git fetch --all --prune` | All remote-tracking refs updated      | Fetch summary       |

---

# 9.11 Pulling

`git pull` is essentially a higher-level synchronization operation.

Conceptually:

```text
git pull
    =
git fetch
+
integration operation
```

Depending on configuration, the integration step may be a merge or rebase.

Basic:

```bash
git pull
```

Explicit remote and branch:

```bash
git pull origin main
```

---

## Pull with merge

```bash
git pull --no-rebase
```

This integrates fetched changes using merge behavior.

---

## Pull with rebase

```bash
git pull --rebase
```

This rebases your local commits on top of the fetched remote commits.

---

## Pull fast-forward only

```bash
git pull --ff-only
```

This refuses to create a merge commit or perform a rebase.

It is useful when you want a strictly linear update.

---

| Command                | Description                    | Example                | Branch State Before and After command      | Output                   |
| ---------------------- | ------------------------------ | ---------------------- | ------------------------------------------ | ------------------------ |
| `git pull`             | Fetch and integrate changes    | `git pull`             | Local branch integrates remote changes     | Fetch/integration output |
| `git pull origin main` | Pull specific remote branch    | `git pull origin main` | Current branch integrates `origin/main`    | Integration output       |
| `git pull --rebase`    | Fetch and rebase local commits | `git pull --rebase`    | Local commits → rewritten on top of remote | Rebase output            |
| `git pull --no-rebase` | Fetch and merge                | `git pull --no-rebase` | Local branch → merge integration           | Merge output             |
| `git pull --ff-only`   | Allow only fast-forward        | `git pull --ff-only`   | Branch moves only if possible              | Fast-forward or error    |

---

# 9.12 Pull with Rebase

A common configuration is:

```bash
git config --global pull.rebase true
```

Then:

```bash
git pull
```

behaves approximately as:

```bash
git fetch
git rebase
```

Example:

```text
Remote:
A---B---C---D

Local:
A---B---C---E---F
```

After pull with rebase:

```text
A---B---C---D---E'---F'
```

This avoids a pull-generated merge commit.

---

# 9.13 Pull with Fast-Forward Only

Use:

```bash
git pull --ff-only
```

Example:

```text
Remote:
A---B---C---D

Local:
A---B---C
```

Result:

```text
A---B---C---D
```

But if:

```text
Remote:
A---B---C---D

Local:
A---B---C---E
```

the operation fails because a fast-forward is impossible.

This is useful for workflows where accidental merge commits are undesirable.

---

# 9.14 Pushing

Basic push:

```bash
git push
```

Explicit:

```bash
git push origin main
```

The push transfers local commits and updates a remote reference when permitted.

---

## Push current branch

```bash
git push origin HEAD
```

This pushes the currently checked-out branch to the same branch name on the remote in common configurations.

---

## Push current branch and establish upstream

```bash
git push -u origin feature/login
```

Afterward, simple commands such as:

```bash
git push
git pull
```

can use the configured upstream.

---

| Command                            | Description                 | Example                            | Branch State Before and After command     | Output               |
| ---------------------------------- | --------------------------- | ---------------------------------- | ----------------------------------------- | -------------------- |
| `git push`                         | Push configured upstream    | `git push`                         | Local branch → remote branch              | Push summary         |
| `git push origin main`             | Push main to origin         | `git push origin main`             | Local `main` → `origin/main`              | Push summary         |
| `git push -u origin feature/login` | Push and establish upstream | `git push -u origin feature/login` | Local feature → remote feature + tracking | Tracking information |
| `git push origin HEAD`             | Push current branch         | `git push origin HEAD`             | Current branch → same-name remote branch  | Push summary         |

---

# 9.15 Push a New Branch

Create:

```bash
git switch -c feature/login
```

Commit:

```bash
git add .
git commit -m "Add login feature"
```

Push:

```bash
git push -u origin feature/login
```

The `-u` option establishes:

```text
local feature/login
        |
        v
origin/feature/login
```

as the upstream relationship.

---

## Modern shorthand

You can often use:

```bash
git push -u origin HEAD
```

This avoids manually repeating the current branch name.

---

# 9.16 Upstream Branches

An upstream branch tells Git which remote branch a local branch tracks.

Inspect:

```bash
git branch -vv
```

Example:

```text
* feature/login abc1234 [origin/feature/login] Add login
  main         def5678 [origin/main] Add API
```

Set upstream:

```bash
git branch --set-upstream-to=origin/main main
```

Set current branch's upstream:

```bash
git branch --set-upstream-to=origin/feature/login
```

---

## Remove upstream

```bash
git branch --unset-upstream
```

This removes the tracking relationship.

---

| Command                                          | Description                 | Example                                    | Branch State Before and After command | Output                      |
| ------------------------------------------------ | --------------------------- | ------------------------------------------ | ------------------------------------- | --------------------------- |
| `git branch -vv`                                 | Show branches and upstreams | `git branch -vv`                           | No branch change                      | Branch/upstream information |
| `git branch --set-upstream-to=<remote>/<branch>` | Set upstream                | `git branch --set-upstream-to=origin/main` | Tracking relationship added           | Tracking confirmation       |
| `git branch --unset-upstream`                    | Remove upstream             | `git branch --unset-upstream`              | Tracking relationship removed         | Usually none                |
| `git push -u origin <branch>`                    | Push and set upstream       | `git push -u origin feature/login`         | Remote branch created + tracking      | Push result                 |

---

# 9.17 Push Tags

Push one tag:

```bash
git push origin v1.0.0
```

Push all tags:

```bash
git push origin --tags
```

Push a tag explicitly:

```bash
git push origin refs/tags/v1.0.0
```

---

## Annotated release workflow

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

---

# 9.18 Delete Remote Branches

Modern syntax:

```bash
git push origin --delete feature/login
```

Equivalent legacy syntax:

```bash
git push origin :feature/login
```

The preferred modern form is:

```bash
git push origin --delete feature/login
```

---

## Delete stale local remote-tracking reference

After a remote branch is deleted:

```bash
git fetch --prune
```

or:

```bash
git remote prune origin
```

---

# 9.19 Rename Remote Branches

Git does not have a single atomic "rename remote branch" operation.

The typical workflow is:

```bash
git branch -m old-name new-name
git push -u origin new-name
git push origin --delete old-name
```

Example:

```bash
git branch -m feature/auth feature/login
git push -u origin feature/login
git push origin --delete feature/auth
```

Coordinate branch renames with repository hosting policies and other developers.

---

# 9.20 Remote-Tracking Branches

Remote-tracking branches look like:

```text
origin/main
origin/develop
origin/feature/login
```

They are references stored locally.

List them:

```bash
git branch -r
```

List local and remote branches:

```bash
git branch -a
```

Example:

```text
* main
  feature/login
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/develop
```

---

| Command                | Description                    | Example                | Branch State Before and After command | Output          |
| ---------------------- | ------------------------------ | ---------------------- | ------------------------------------- | --------------- |
| `git branch -r`        | List remote-tracking branches  | `git branch -r`        | No branch change                      | Remote branches |
| `git branch -a`        | List local and remote branches | `git branch -a`        | No branch change                      | All branches    |
| `git log origin/main`  | Inspect remote-tracking branch | `git log origin/main`  | No branch change                      | Commit history  |
| `git show origin/main` | Inspect remote-tracking tip    | `git show origin/main` | No branch change                      | Commit/details  |

---

# 9.21 Compare Local and Remote Branches

Check commits that exist locally but not remotely:

```bash
git log origin/main..main --oneline
```

Check commits that exist remotely but not locally:

```bash
git log main..origin/main --oneline
```

Show both directions:

```bash
git log --left-right --oneline main...origin/main
```

Count differences:

```bash
git rev-list --left-right --count main...origin/main
```

Example:

```text
3  2
```

Meaning:

```text
3 commits unique to main
2 commits unique to origin/main
```

---

## Compare file changes

```bash
git diff main..origin/main
```

Compare remote against local:

```bash
git diff origin/main..main
```

Remember that the order of the two references affects the direction of the diff.

---

# 9.22 Resetting a Local Branch to Remote

If you intentionally want the local branch to exactly match its remote-tracking branch:

```bash
git fetch origin
git reset --hard origin/main
```

Before:

```text
origin/main: A---B---C
main:        A---B---C---D---E
```

After:

```text
origin/main: A---B---C
main:        A---B---C
```

Commits `D` and `E` are no longer reachable from `main`.

**Warning:** `--hard` also changes tracked files in the working tree.

A safer backup:

```bash
git branch backup-before-reset
```

then:

```bash
git reset --hard origin/main
```

---

# 9.23 Remote Synchronization Workflows

## Standard daily synchronization

```bash
git fetch origin
git status
git log --oneline --decorate --graph --all
```

Then integrate using your team's preferred method:

```bash
git merge origin/main
```

or:

```bash
git rebase origin/main
```

---

## Update main

```bash
git switch main
git fetch origin
git pull --ff-only
```

---

## Update a feature branch with rebase

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

---

## Push a new feature

```bash
git switch -c feature/login
git add .
git commit -m "Add login feature"
git push -u origin feature/login
```

---

## Push an existing feature

```bash
git push
```

---

## Verify remote state

```bash
git fetch origin
git status
git branch -vv
```

---

# 9.24 Force Push

Force push:

```bash
git push --force
```

or:

```bash
git push -f
```

This permits a non-fast-forward update.

Typical reason:

```bash
git rebase origin/main
git push --force
```

However, prefer:

```bash
git push --force-with-lease
```

---

# 9.25 Force Push with Lease

Recommended after rebasing a branch:

```bash
git push --force-with-lease
```

Explicit:

```bash
git push --force-with-lease origin feature/login
```

Conceptually:

```text
Local rewritten branch
        |
        v
Does remote still have the expected old state?
        |
       yes
        |
        v
Update remote
```

If someone else pushed changes that you have not incorporated, Git can reject the force push.

This provides a useful safety check.

---

# 9.26 Push Options

## Set upstream

```bash
git push -u origin feature/login
```

## Delete remote branch

```bash
git push origin --delete feature/login
```

## Push all local branches

```bash
git push --all origin
```

Use carefully because this can publish branches you did not intend to publish.

---

## Push all tags

```bash
git push --tags origin
```

---

## Dry-run push

```bash
git push --dry-run origin main
```

This lets you inspect what would happen without actually updating the remote.

---

## Push atomic updates

```bash
git push --atomic origin main feature/login
```

When supported by the remote, either all specified ref updates succeed or none are applied.

---

## Push with signed push

If your Git environment and remote support it:

```bash
git push --signed origin main
```

This is distinct from commit signing.

---

| Command              | Description                           | Example                                       | Branch State Before and After command           | Output       |
| -------------------- | ------------------------------------- | --------------------------------------------- | ----------------------------------------------- | ------------ |
| `git push --dry-run` | Simulate push                         | `git push --dry-run origin main`              | No remote change                                | Push preview |
| `git push --all`     | Push all local branches               | `git push --all origin`                       | Multiple remote branches may update             | Push summary |
| `git push --tags`    | Push all tags                         | `git push --tags origin`                      | Remote tags created/updated                     | Push summary |
| `git push --atomic`  | Apply multiple ref updates atomically | `git push --atomic origin main feature/login` | All-or-nothing ref update                       | Push result  |
| `git push --signed`  | Request signed push                   | `git push --signed origin main`               | Remote ref may update with signed push metadata | Push result  |

---

# 9.27 Fetch Options

## Fetch and prune

```bash
git fetch --prune origin
```

## Fetch all remotes

```bash
git fetch --all
```

## Fetch all remotes and prune

```bash
git fetch --all --prune
```

## Fetch tags

```bash
git fetch --tags
```

## Fetch without changing tags

```bash
git fetch --no-tags
```

## Dry-run fetch

```bash
git fetch --dry-run origin
```

## Verbose fetch

```bash
git fetch --verbose origin
```

---

# 9.28 Pull Options

Useful forms include:

```bash
git pull --rebase
git pull --no-rebase
git pull --ff-only
git pull --autostash
git pull --no-autostash
git pull --verbose
```

A useful production/team default can be:

```bash
git pull --ff-only
```

when the project requires explicit integration rather than automatic merges.

For teams that prefer rebasing:

```bash
git pull --rebase
```

---

# 9.29 Remote Configuration

Git stores remote configuration in:

```text
.git/config
```

Inspect it:

```bash
git config --local --get-regexp '^remote\.'
```

Example:

```text
remote.origin.url git@github.com:user/project.git
remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
```

---

## Get remote URL

```bash
git config --get remote.origin.url
```

## Get push URL

```bash
git config --get remote.origin.pushurl
```

## Inspect all Git configuration

```bash
git config --list --show-origin
```

---

# 9.30 Multiple Remotes

A repository can have multiple remotes.

Example:

```text
origin
    ↓
your fork

upstream
    ↓
main project
```

Add upstream:

```bash
git remote add upstream git@github.com:company/project.git
```

Verify:

```bash
git remote -v
```

Fetch upstream:

```bash
git fetch upstream
```

Update your local branch:

```bash
git switch main
git rebase upstream/main
```

Push your updated branch to your fork:

```bash
git push origin main
```

---

## Typical fork architecture

```text
                  +----------------+
                  |    upstream    |
                  | main project   |
                  +-------+--------+
                          |
                       fetch
                          |
                          v
+----------------+   local repo   +----------------+
|     origin     | <------------> |     local      |
|   your fork    |                |    branches    |
+----------------+                +----------------+
```

---

# 9.31 Fork Workflows

Typical fork workflow:

```bash
git clone git@github.com:your-user/project.git
cd project
git remote add upstream git@github.com:organization/project.git
```

Verify:

```bash
git remote -v
```

Fetch upstream:

```bash
git fetch upstream
```

Update main:

```bash
git switch main
git rebase upstream/main
```

Push:

```bash
git push origin main
```

Create feature:

```bash
git switch -c feature/login
```

Develop:

```bash
git add .
git commit -m "Add login feature"
```

Push:

```bash
git push -u origin feature/login
```

---

# 9.32 Developer Workflows

## Workflow A — Clone a repository

```bash
git clone <URL>
cd <repository>
git status
```

---

## Workflow B — Clone into a specific directory

```bash
git clone <URL> my-project
cd my-project
```

---

## Workflow C — Start working on a remote branch

```bash
git fetch origin
git switch --track origin/feature/login
```

---

## Workflow D — Update feature branch

```bash
git fetch origin
git rebase origin/main
```

---

## Workflow E — Merge remote changes

```bash
git fetch origin
git merge origin/main
```

---

## Workflow F — Publish a branch

```bash
git push -u origin feature/login
```

---

## Workflow G — Delete completed remote branch

```bash
git push origin --delete feature/login
git fetch --prune
```

---

## Workflow H — Check divergence

```bash
git fetch origin
git rev-list --left-right --count HEAD...origin/main
```

---

## Workflow I — Replace local branch with remote

Only when intentional:

```bash
git fetch origin
git branch backup-before-reset
git reset --hard origin/main
```

---

# 9.33 DevOps and CI Workflows

## CI: fetch a specific branch

```bash
git fetch origin main
```

Then inspect:

```bash
git rev-parse origin/main
```

---

## CI: verify repository state

```bash
git status --short
git branch --show-current
git rev-parse HEAD
git rev-parse origin/main
```

---

## CI: determine current commit

```bash
git rev-parse HEAD
```

Example:

```text
abc123456789...
```

---

## CI: determine remote repository

```bash
git remote get-url origin
```

---

## CI: determine whether branch is synchronized

```bash
git fetch origin
git merge-base --is-ancestor origin/main HEAD
```

Exit status:

```text
0 = origin/main is an ancestor of HEAD
1 = origin/main is not an ancestor of HEAD
```

---

## CI: avoid implicit pull merges

Instead of:

```bash
git pull
```

CI can explicitly perform:

```bash
git fetch origin
git checkout main
git reset --hard origin/main
```

This makes the desired synchronization behavior explicit.

---

## CI: shallow fetch

A CI job can use a shallow clone:

```bash
git clone --depth 1 <URL>
```

or fetch additional history when needed:

```bash
git fetch --deepen=50 origin
```

This can reduce clone time and network usage.

---

# 9.34 High-Value Remote Commands

| Command                                                | Description                            | Example                                                | Branch State Before and After command        | Output                  |
| ------------------------------------------------------ | -------------------------------------- | ------------------------------------------------------ | -------------------------------------------- | ----------------------- |
| `git remote -v`                                        | Show remote URLs                       | `git remote -v`                                        | No branch change                             | Fetch/push URLs         |
| `git remote show origin`                               | Inspect remote                         | `git remote show origin`                               | No branch change                             | Remote details          |
| `git remote add origin <url>`                          | Add remote                             | `git remote add origin <url>`                          | Branches unchanged                           | Usually none            |
| `git remote set-url origin <url>`                      | Change remote URL                      | `git remote set-url origin <url>`                      | Branches unchanged                           | Usually none            |
| `git remote remove origin`                             | Remove remote configuration            | `git remote remove origin`                             | Local branches remain; remote config removed | Usually none            |
| `git fetch origin`                                     | Download remote updates                | `git fetch origin`                                     | `origin/*` updated                           | Fetch summary           |
| `git fetch --all --prune`                              | Fetch all remotes and clean stale refs | `git fetch --all --prune`                              | Remote-tracking refs updated                 | Fetch/prune summary     |
| `git pull`                                             | Fetch and integrate                    | `git pull`                                             | Current branch integrates remote changes     | Integration output      |
| `git pull --rebase`                                    | Fetch and rebase                       | `git pull --rebase`                                    | Local commits rewritten onto remote          | Rebase output           |
| `git pull --ff-only`                                   | Fast-forward only                      | `git pull --ff-only`                                   | Branch moves only if possible                | Fast-forward/error      |
| `git push`                                             | Push upstream                          | `git push`                                             | Local → remote                               | Push summary            |
| `git push -u origin <branch>`                          | Publish and track branch               | `git push -u origin feature/login`                     | Remote branch created + upstream set         | Push result             |
| `git push --force-with-lease`                          | Safely force rewritten history         | `git push --force-with-lease`                          | Remote branch rewritten if lease valid       | Push result             |
| `git push --dry-run`                                   | Preview push                           | `git push --dry-run`                                   | No remote change                             | Push preview            |
| `git push --delete origin <branch>`                    | Delete remote branch                   | `git push origin --delete feature/login`               | Remote branch removed                        | Deletion result         |
| `git branch -r`                                        | List remote-tracking branches          | `git branch -r`                                        | No branch change                             | Remote branches         |
| `git branch -a`                                        | List all branches                      | `git branch -a`                                        | No branch change                             | Local + remote branches |
| `git branch -vv`                                       | Show upstream tracking                 | `git branch -vv`                                       | No branch change                             | Branch tracking info    |
| `git ls-remote origin`                                 | Inspect remote refs directly           | `git ls-remote origin`                                 | No local ref changes                         | Remote refs             |
| `git remote prune origin`                              | Remove stale remote refs               | `git remote prune origin`                              | Stale `origin/*` refs removed                | Prune summary           |
| `git rev-list --left-right --count HEAD...origin/main` | Count branch divergence                | `git rev-list --left-right --count HEAD...origin/main` | No branch change                             | Two counts              |
| `git merge-base --is-ancestor origin/main HEAD`        | Test ancestry                          | `git merge-base --is-ancestor origin/main HEAD`        | No branch change                             | Exit status             |

---

# 9.35 Remote Repository Safety

Remote operations can affect other developers, CI systems, release pipelines, and production workflows.

Use extra care with:

```bash
git push --force
```

```bash
git push --force-with-lease
```

```bash
git push --all
```

```bash
git push --tags
```

```bash
git reset --hard origin/main
```

---

## Recommended safe sequence before destructive remote operations

```bash
git status
git fetch origin
git branch -vv
git log --graph --oneline --decorate --all
git diff origin/main...HEAD
```

Create a backup if necessary:

```bash
git branch backup-before-remote-operation
```

Then perform the intended operation.

---

## Never assume `origin/main` is current

This:

```bash
git log origin/main
```

only shows the latest state known to your local repository.

Refresh it first:

```bash
git fetch origin
```

Then:

```bash
git log origin/main
```

---

## Prefer explicit synchronization

Instead of blindly running:

```bash
git pull
```

consider:

```bash
git fetch origin
git status
git log --graph --oneline --decorate --all
```

Then explicitly choose:

```bash
git merge origin/main
```

or:

```bash
git rebase origin/main
```

This makes history changes more predictable.

---

# Remote Synchronization Cheat Sheet

### Download remote changes

```bash
git fetch origin
```

### Download and clean deleted remote branches

```bash
git fetch --prune origin
```

### Download from every remote

```bash
git fetch --all
```

### Integrate remote changes using merge

```bash
git pull --no-rebase
```

### Integrate using rebase

```bash
git pull --rebase
```

### Allow only fast-forward

```bash
git pull --ff-only
```

### Publish a new branch

```bash
git push -u origin feature/name
```

### Push existing branch

```bash
git push
```

### Safely force-push rewritten history

```bash
git push --force-with-lease
```

### Delete remote branch

```bash
git push origin --delete feature/name
```

### List remote branches

```bash
git branch -r
```

### List all branches

```bash
git branch -a
```

### Show tracking relationships

```bash
git branch -vv
```

### Inspect remote

```bash
git remote show origin
```

### Inspect remote refs

```bash
git ls-remote origin
```

### Compare local branch with remote

```bash
git log HEAD..origin/main --oneline
git log origin/main..HEAD --oneline
```

### Count divergence

```bash
git rev-list --left-right --count HEAD...origin/main
```

### Update local branch exactly to remote

```bash
git fetch origin
git reset --hard origin/main
```

Use the last command only when intentionally discarding local branch state.

---

# Recommended Daily Remote Workflow

For a normal feature-development workflow:

```bash
git fetch origin
git switch main
git pull --ff-only

git switch feature/login
git rebase origin/main

git status
git log --oneline --graph --decorate -10

git push --force-with-lease
```

If the branch has never been pushed:

```bash
git push -u origin feature/login
```

---

# Essential Mental Model

Remember these three layers:

```text
Remote repository
        |
        | fetch
        v
Remote-tracking branch
(origin/main)
        |
        | merge / rebase
        v
Local branch
(main)
        |
        | push
        v
Remote repository
```

The most important commands are:

```bash
git fetch
git pull
git push
git branch -r
git branch -vv
git remote -v
git remote show origin
git push -u origin <branch>
git push --force-with-lease
git fetch --prune
```

A particularly important distinction is:

```bash
git fetch
```

**downloads and updates remote-tracking references**, while:

```bash
git pull
```

**downloads and integrates changes into the current branch**.

For maximum control, use:

```bash
git fetch
```

first and explicitly choose the subsequent merge or rebase operation.

---

## Next Part

**Next file:** `10-undoing-changes.md`

[Next: Undoing Changes](10-undoing-changes.md)
