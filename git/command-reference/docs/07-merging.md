# 7. Merging

This chapter covers Git merging from basic fast-forward merges to three-way merges, merge strategies, conflict handling, merge previews, aborting merges, merge commits, squash merges, and common Developer, Software Engineer, and DevOps workflows.

---

## Table of Contents

* [7.1 Merge Concepts](#71-merge-concepts)
* [7.2 Basic Merge](#72-basic-merge)
* [7.3 Fast-Forward Merge](#73-fast-forward-merge)
* [7.4 Three-Way Merge](#74-three-way-merge)
* [7.5 Merge Commit](#75-merge-commit)
* [7.6 Force a Merge Commit](#76-force-a-merge-commit)
* [7.7 Fast-Forward Only](#77-fast-forward-only)
* [7.8 Merge Without Commit](#78-merge-without-commit)
* [7.9 Merge with a Custom Message](#79-merge-with-a-custom-message)
* [7.10 Squash Merge](#710-squash-merge)
* [7.11 Merge Conflicts](#711-merge-conflicts)
* [7.12 Inspect Conflicts](#712-inspect-conflicts)
* [7.13 Resolve Conflicts](#713-resolve-conflicts)
* [7.14 Continue a Merge](#714-continue-a-merge)
* [7.15 Abort a Merge](#715-abort-a-merge)
* [7.16 Quit a Merge](#716-quit-a-merge)
* [7.17 Take Ours or Theirs](#717-take-ours-or-theirs)
* [7.18 Merge Strategies](#718-merge-strategies)
* [7.19 Merge Strategy Options](#719-merge-strategy-options)
* [7.20 Merge Drivers](#720-merge-drivers)
* [7.21 Merge Tools](#721-merge-tools)
* [7.22 Merge Preview](#722-merge-preview)
* [7.23 Merge Base](#723-merge-base)
* [7.24 Verify Merge History](#724-verify-merge-history)
* [7.25 Revert a Merge](#725-revert-a-merge)
* [7.26 Remote Branch Merging](#726-remote-branch-merging)
* [7.27 Pull and Merge](#727-pull-and-merge)
* [7.28 Branch Protection Considerations](#728-branch-protection-considerations)
* [7.29 Developer Workflows](#729-developer-workflows)
* [7.30 DevOps and CI Workflows](#730-devops-and-ci-workflows)
* [7.31 High-Value Merge Commands](#731-high-value-merge-commands)

---

# 7.1 Merge Concepts

Git merging combines the histories of two development lines.

A typical feature branch starts like this:

```text
A---B---C  main
         \
          D---E  feature/login
```

If you are on `main` and execute:

```bash
git merge feature/login
```

Git can produce:

```text
A---B---C---D---E  main
             \
              feature/login
```

This is a **fast-forward merge** because `main` had no commits after the point where `feature/login` diverged.

If both branches have new commits:

```text
A---B---C---F  main
         \
          D---E  feature/login
```

a normal merge creates:

```text
A---B---C---F-------M  main
         \         /
          D---E---/
```

`M` is a merge commit with two parents.

---

# 7.2 Basic Merge

The fundamental command is:

```bash
git merge <branch>
```

Example:

```bash
git switch main
git merge feature/login
```

The branch named in the command is merged **into the current branch**.

This distinction is extremely important.

If you run:

```bash
git switch main
git merge feature/login
```

you are saying:

> Merge `feature/login` into `main`.

You are **not** switching branches.

---

| Command                         | Description                              | Example                              | Branch State Before and After command           | Output            |
| ------------------------------- | ---------------------------------------- | ------------------------------------ | ----------------------------------------------- | ----------------- |
| `git merge <branch>`            | Merge branch into current branch         | `git merge feature/login`            | `main` → `main`                                 | Merge result      |
| `git merge <commit>`            | Merge a commit/reference                 | `git merge abc1234`                  | `main` → `main`                                 | Merge result      |
| `git merge <branch1> <branch2>` | Merge multiple heads                     | `git merge feature/api feature/auth` | `main` → `main`                                 | Merge result      |
| `git merge --no-edit <branch>`  | Merge using generated message            | `git merge --no-edit feature/login`  | `main` → `main`                                 | Merge result      |
| `git merge --edit <branch>`     | Edit merge message                       | `git merge --edit feature/login`     | `main` → `main`                                 | Editor/message    |
| `git merge --abort`             | Abort active merge                       | `git merge --abort`                  | `main` with merge in progress → pre-merge state | Usually no output |
| `git merge --continue`          | Continue merge after conflict resolution | `git merge --continue`               | Merge in progress → completed merge             | Commit/output     |

---

# 7.3 Fast-Forward Merge

A fast-forward merge happens when the current branch is an ancestor of the branch being merged.

Before:

```text
A---B---C  main
         \
          D---E  feature/login
```

Command:

```bash
git switch main
git merge feature/login
```

After:

```text
A---B---C---D---E  main, feature/login
                 ↑
                HEAD
```

No new merge commit is created.

The `main` reference simply moves forward.

Typical output:

```text
Updating abc1234..def5678
Fast-forward
 file.txt | 10 +++++++---
 1 file changed, 7 insertions(+), 3 deletions(-)
```

---

## Why fast-forward merges are useful

They produce linear history:

```text
A---B---C---D---E
```

instead of:

```text
A---B---C---M
     \       /
      D---E
```

They are commonly used when the target branch has not changed since the feature branch was created.

---

# 7.4 Three-Way Merge

When both branches contain unique commits, Git cannot simply move the target branch pointer.

Example:

```text
        D---E  feature
       /
A---B---C---F  main
```

Git identifies three important commits:

```text
common ancestor = B
ours            = F
theirs          = E
```

Git performs a three-way merge.

Result:

```text
        D---E
       /     \
A---B---C---F---M  main
```

`M` is the merge commit.

---

## Identify the merge base

```bash
git merge-base main feature
```

Example:

```text
abc123456789...
```

This is the common ancestor Git can use for a three-way merge.

---

# 7.5 Merge Commit

A merge commit has at least two parents.

Example:

```text
A---B---C---F-------M  main
     \             /
      D---E-------/
```

Inspect it:

```bash
git show --summary M
```

or:

```bash
git log --graph --oneline --decorate
```

Typical log:

```text
*   abc1234 (HEAD -> main) Merge branch 'feature/login'
|\
| * def5678 (feature/login) Add login validation
| * 123abcd Add login endpoint
* | 4567890 Update API
|/
* 789abcd Initial implementation
```

---

# 7.6 Force a Merge Commit

Use:

```bash
git merge --no-ff feature/login
```

`--no-ff` prevents a fast-forward merge.

Before:

```text
A---B---C  main
         \
          D---E  feature
```

Command:

```bash
git switch main
git merge --no-ff feature
```

After:

```text
A---B---C-------M  main
         \     /
          D---E  feature
```

This preserves an explicit merge point.

---

## Why use `--no-ff`?

It can make feature integration easier to identify:

```text
Merge feature/login
Merge feature/payment
Merge feature/search
```

This is particularly useful in repositories that prefer a visible feature-branch history.

---

# 7.7 Fast-Forward Only

Use:

```bash
git merge --ff-only feature/login
```

This tells Git:

> Merge only if the operation can be completed as a fast-forward.

If a merge commit would be required, Git stops instead of automatically creating one.

Example:

```text
A---B---C---F  main
     \
      D---E  feature
```

Running:

```bash
git merge --ff-only feature
```

fails because the histories have diverged.

Typical output:

```text
fatal: Not possible to fast-forward, aborting.
```

This is particularly useful for automation and maintaining strictly linear branches.

---

# 7.8 Merge Without Commit

Use:

```bash
git merge --no-commit feature/login
```

This performs the merge but stops before creating the merge commit when a commit would normally be created.

Useful when you want to inspect or modify the result before committing.

Example:

```bash
git switch main
git merge --no-commit --no-ff feature/login
```

Then inspect:

```bash
git status
git diff --cached
```

If everything is correct:

```bash
git commit
```

---

## Important limitation

`--no-commit` cannot prevent a fast-forward from occurring.

If you require a merge commit to stop before committing, use:

```bash
git merge --no-commit --no-ff feature/login
```

---

# 7.9 Merge with a Custom Message

Use:

```bash
git merge -m "Merge login feature" feature/login
```

Example:

```bash
git switch main
git merge --no-ff -m "Merge login feature" feature/login
```

This creates a merge commit with:

```text
Merge login feature
```

as its commit message.

---

## Edit the generated merge message

```bash
git merge --edit feature/login
```

Short form:

```bash
git merge -e feature/login
```

---

## Accept generated merge message

```bash
git merge --no-edit feature/login
```

This is useful when scripting or when the generated message is acceptable.

---

# 7.10 Squash Merge

Use:

```bash
git merge --squash feature/login
```

A squash merge takes the changes introduced by the feature branch and applies them to the current working tree/index without creating a normal merge commit.

Example:

Before:

```text
A---B---C  main
         \
          D---E---F  feature
```

Command:

```bash
git switch main
git merge --squash feature
```

The resulting state is approximately:

```text
A---B---C  main
         \
          D---E---F  feature
```

but the changes from `D`, `E`, and `F` are staged on `main`.

Then:

```bash
git commit -m "Add login feature"
```

creates:

```text
A---B---C---S  main

         D---E---F  feature
```

`S` contains the combined changes but is not a merge commit.

---

## Check staged squash result

```bash
git status
git diff --cached
```

---

## Complete squash workflow

```bash
git switch main
git merge --squash feature/login
git diff --cached
git commit -m "Add login feature"
```

---

# 7.11 Merge Conflicts

A merge conflict occurs when Git cannot automatically reconcile changes.

Example:

```text
A---B---C---F  main
     \
      D---E  feature
```

Suppose both branches modified the same lines differently.

Running:

```bash
git merge feature
```

may produce:

```text
Auto-merging app.js
CONFLICT (content): Merge conflict in app.js
Automatic merge failed; fix conflicts and then commit the result.
```

The repository enters a merge-in-progress state.

---

# 7.12 Inspect Conflicts

## Check status

```bash
git status
```

Typical output:

```text
You have unmerged paths.

Unmerged paths:
  both modified:   app.js
```

---

## List conflicted files

```bash
git diff --name-only --diff-filter=U
```

Example:

```text
app.js
config.yml
```

---

## Show conflict details

```bash
git diff
```

---

## Show only unresolved differences

```bash
git diff --diff-filter=U
```

---

## Check unresolved files

```bash
git ls-files -u
```

This shows unmerged index entries.

Example:

```text
100644 abc1234 1 app.js
100644 def5678 2 app.js
100644 789abcd 3 app.js
```

The stages generally represent:

```text
stage 1 = common ancestor
stage 2 = ours
stage 3 = theirs
```

---

# 7.13 Resolve Conflicts

Suppose Git places this in a file:

```text
<<<<<<< HEAD
const port = 3000;
=======
const port = 8080;
>>>>>>> feature/config
```

You must manually decide the desired final content.

For example:

```text
const port = process.env.PORT || 3000;
```

Then stage the resolved file:

```bash
git add app.js
```

Check:

```bash
git status
```

Once all conflicts are resolved:

```bash
git commit
```

or:

```bash
git merge --continue
```

---

## Resolve all files

```bash
git add .
```

This stages all changes, including unrelated changes.

A safer approach during conflict resolution is often:

```bash
git add path/to/resolved-file
```

for each intentionally resolved file.

---

# 7.14 Continue a Merge

After resolving conflicts:

```bash
git add <resolved-file>
git merge --continue
```

Depending on the Git version and merge state, Git may open an editor for the merge commit message.

You can also complete the merge with:

```bash
git commit
```

---

## Verify merge state

```bash
git status
```

If the output says all conflicts are fixed and a merge commit is required, the merge is ready to complete.

---

# 7.15 Abort a Merge

If the merge should be completely abandoned:

```bash
git merge --abort
```

This attempts to return the working tree and index to their state before the merge began.

Before:

```text
A---B---C  main
     \
      D---E  feature
```

After starting a conflicting merge:

```text
merge in progress
```

Then:

```bash
git merge --abort
```

returns the repository to the pre-merge state.

---

## Check status after abort

```bash
git status
```

---

## Important caution

`git merge --abort` is intended for an active merge.

If you had uncommitted changes before starting the merge, Git may not always be able to reconstruct your exact original state if those changes interacted with the merge.

For maximum safety, inspect:

```bash
git status
git diff
```

before beginning complex merges.

---

# 7.16 Quit a Merge

Git also provides:

```bash
git merge --quit
```

This stops the merge operation's metadata handling without necessarily restoring the pre-merge working tree.

Conceptually:

```text
--abort
    ↓
stop merge + attempt to restore pre-merge state

--quit
    ↓
stop merge bookkeeping without resetting files
```

Use `--quit` only when you understand the resulting working-tree state.

---

# 7.17 Take Ours or Theirs

During a conflict, Git provides conflict stages.

You can choose one side for a specific file.

## Take our version

```bash
git checkout --ours -- path/to/file
```

Then:

```bash
git add path/to/file
```

Modern alternative:

```bash
git restore --ours -- path/to/file
git add path/to/file
```

---

## Take their version

```bash
git checkout --theirs -- path/to/file
```

Modern alternative:

```bash
git restore --theirs -- path/to/file
git add path/to/file
```

---

## Important meaning of ours/theirs

During:

```bash
git switch main
git merge feature
```

generally:

```text
ours   = main
theirs = feature
```

Do not interpret "theirs" as "the remote branch" in every Git operation.

The meaning depends on the operation being performed.

---

# 7.18 Merge Strategies

Git supports merge strategies.

A commonly encountered strategy is:

```bash
git merge -s ort feature/login
```

`ort` is the modern default strategy for ordinary two-head merges in current Git versions.

---

## Specify recursive/older strategy

Older repositories and documentation may show:

```bash
git merge -s recursive feature/login
```

The `recursive` strategy was historically used for ordinary two-head merges.

Modern Git uses `ort` for the common two-head case.

---

## Octopus strategy

When merging multiple branches:

```bash
git merge feature/api feature/docs feature/test
```

Git can use the octopus strategy when possible.

Octopus merges are generally intended for combining multiple heads when there are no complex conflicts.

---

## Resolve strategy

The historical `resolve` strategy can be selected with:

```bash
git merge -s resolve feature/login
```

It has more limited capabilities than modern `ort`.

---

# 7.19 Merge Strategy Options

Strategy options can be passed using:

```bash
git merge -X<option> <branch>
```

For example:

```bash
git merge -Xours feature/login
```

or:

```bash
git merge -Xtheirs feature/login
```

### Important distinction

This is **not the same** as:

```bash
git merge -s ours
```

`-Xours` is a merge strategy option that prefers our side when resolving certain conflicting hunks.

`-s ours` is a completely different merge strategy that records the merge while effectively ignoring the other tree's content.

---

## Prefer ours for conflicting hunks

```bash
git merge -Xours feature/login
```

---

## Prefer theirs for conflicting hunks

```bash
git merge -Xtheirs feature/login
```

These options should be used carefully because automatic conflict preference can silently discard intended changes.

---

# 7.20 Merge Drivers

Git can use merge drivers for particular file types.

Configuration is commonly defined through `.gitattributes`.

Example:

```text
*.lock merge=ours
```

A repository can define custom merge behavior for files that need specialized handling.

Inspect configured attributes:

```bash
git check-attr merge -- path/to/file
```

Example:

```text
path/to/file: merge: ours
```

---

# 7.21 Merge Tools

Git can invoke an external merge tool.

## Launch configured merge tool

```bash
git mergetool
```

Git may open the configured graphical or terminal merge application.

---

## Launch merge tool for a specific file

```bash
git mergetool -- path/to/file
```

---

## List available tools

```bash
git mergetool --tool-help
```

---

## Configure a merge tool

For example:

```bash
git config --global merge.tool vimdiff
```

Then:

```bash
git mergetool
```

The exact available tools depend on the installed software and Git configuration.

---

# 7.22 Merge Preview

Before merging, inspect what would be introduced.

## Show commits unique to feature

```bash
git log main..feature/login --oneline
```

---

## Show changes

```bash
git diff main...feature/login
```

The three-dot form compares the feature branch with the merge base and is often useful for reviewing what a branch introduces relative to the target branch.

---

## Show statistics

```bash
git diff --stat main...feature/login
```

---

## Show file names

```bash
git diff --name-only main...feature/login
```

---

## Graph the history

```bash
git log --graph --oneline --decorate main...feature/login
```

---

## Inspect merge base

```bash
git merge-base main feature/login
```

---

# 7.23 Merge Base

The merge base is the best common ancestor between two commits for many Git operations.

Command:

```bash
git merge-base main feature/login
```

Example output:

```text
abc123456789
```

Graph:

```text
        D---E  feature
       /
A---B---C---F  main
```

Here:

```text
merge-base(main, feature) = B
```

---

## Show merge base commit

```bash
git show $(git merge-base main feature)
```

---

## Check ancestry

```bash
git merge-base --is-ancestor main feature
```

Exit status:

```text
0 → main is an ancestor of feature
1 → main is not an ancestor of feature
```

This can determine whether a fast-forward is possible.

---

# 7.24 Verify Merge History

## Visualize merges

```bash
git log --graph --oneline --decorate --all
```

Example:

```text
*   abc1234 (HEAD -> main) Merge branch 'feature/login'
|\
| * def5678 (feature/login) Add validation
| * 123abcd Add login
* | 4567890 Update API
|/
* 789abcd Initial commit
```

---

## Show merge commits

```bash
git log --merges
```

---

## Show merge commits in one line

```bash
git log --merges --oneline
```

---

## Show first-parent history

```bash
git log --first-parent --oneline
```

This is particularly useful for release branches because it follows the main integration line and does not descend into every merged feature branch.

---

## Show merge commit details

```bash
git show --summary <merge-commit>
```

---

# 7.25 Revert a Merge

A merge commit can be reverted, but Git needs to know which parent should be considered the mainline.

Example:

```text
A---B---C---M  main
     \       /
      D---E
```

To revert `M`:

```bash
git revert -m 1 M
```

`-m 1` means:

> Treat parent 1 of the merge commit as the mainline.

If parent 1 represents `main`, Git creates a new commit that reverses the changes introduced by the merged branch.

---

## Find merge parents

```bash
git rev-list --parents -n 1 M
```

Example:

```text
abc1234 parent1 parent2
```

---

## Important warning

Reverting a merge is not the same as deleting the merge commit.

Git history remains:

```text
A---B---C---M---R
     \       /
      D---E
```

`R` is a new commit that reverses the merge's effective changes.

---

# 7.26 Remote Branch Merging

A common workflow is:

```bash
git fetch origin
git switch main
git merge origin/feature/login
```

This merges the remote-tracking reference into the local `main`.

Before:

```text
local:
main → C

remote-tracking:
origin/feature/login → E
```

After:

```text
main → merge result
origin/feature/login → E
```

---

## Merge remote main into feature

```bash
git fetch origin
git switch feature/login
git merge origin/main
```

This is a common way to integrate current `main` changes into a feature branch.

---

# 7.27 Pull and Merge

`git pull` generally performs:

```text
fetch
+
integration
```

Depending on configuration, the integration can be a merge or rebase.

To explicitly request merge behavior:

```bash
git pull --no-rebase
```

To require fast-forward-only behavior:

```bash
git pull --ff-only
```

---

## Pull with explicit merge

```bash
git switch main
git pull --no-rebase origin main
```

---

## Pull with fast-forward-only

```bash
git switch main
git pull --ff-only origin main
```

This prevents Git from automatically creating a merge commit during the pull.

---

# 7.28 Branch Protection Considerations

In production repositories, direct merging into protected branches may be controlled by the hosting platform.

Typical protected branches:

```text
main
master
production
release/*
```

A common workflow is:

```text
feature branch
      ↓
pull request / merge request
      ↓
CI
      ↓
code review
      ↓
protected branch
```

Local Git commands still provide the underlying operations:

```bash
git fetch
git merge
git diff
git log
```

but the final integration may be performed by the hosting platform.

---

# 7.29 Developer Workflows

## Workflow A — Merge a feature into main

```bash
git fetch origin
git switch main
git pull --ff-only
git merge --no-ff feature/login
git push origin main
```

History:

```text
A---B---C-------M  main
         \     /
          D---E
```

---

## Workflow B — Fast-forward-only integration

```bash
git switch main
git pull --ff-only
git merge --ff-only feature/login
```

If the branches diverged, Git stops.

---

## Workflow C — Inspect before merge

```bash
git fetch origin
git log --oneline main..feature/login
git diff main...feature/login
git diff --stat main...feature/login
git merge feature/login
```

---

## Workflow D — Merge without immediate commit

```bash
git switch main
git merge --no-commit --no-ff feature/login
git status
git diff --cached
git commit
```

---

## Workflow E — Squash feature branch

```bash
git switch main
git merge --squash feature/login
git diff --cached
git commit -m "Add login feature"
```

---

## Workflow F — Update feature branch from main

```bash
git fetch origin
git switch feature/login
git merge origin/main
```

This produces a merge commit if the histories have diverged.

---

## Workflow G — Abort conflicted merge

```bash
git merge feature/login
```

If conflicts occur:

```bash
git status
git merge --abort
```

---

## Workflow H — Resolve merge manually

```bash
git merge feature/login
git status
git diff --name-only --diff-filter=U
```

Edit the conflicted files.

Then:

```bash
git add path/to/file
git merge --continue
```

Verify:

```bash
git status
git log --graph --oneline --decorate -10
```

---

# 7.30 DevOps and CI Workflows

## Validate that a branch can be fast-forwarded

```bash
git fetch origin
git merge-base --is-ancestor origin/main HEAD
```

A successful exit code means:

```text
origin/main
      ↓
   ancestor
      ↓
HEAD
```

---

## Check whether feature is already integrated

```bash
git fetch origin
git merge-base --is-ancestor origin/feature/login origin/main
```

Exit code:

```text
0 → feature is contained in main
1 → feature is not contained in main
```

This is useful in automation.

---

## Generate merge preview for CI

```bash
git fetch origin
git diff --stat origin/main...HEAD
git diff --name-only origin/main...HEAD
```

---

## Verify no merge conflict before integration

A temporary integration branch can be used:

```bash
git switch -c ci-merge-test origin/main
git merge --no-commit --no-ff origin/feature/login
```

If successful:

```bash
git merge --abort
```

If conflicts occur:

```bash
git merge --abort
```

Then remove the temporary branch if desired:

```bash
git switch main
git branch -D ci-merge-test
```

---

## Enforce fast-forward policy in scripts

```bash
git merge --ff-only origin/main
```

This is preferable to allowing an unexpected merge commit when a linear history is required.

---

# 7.31 High-Value Merge Commands

| Command                                  | Description                                 | Example                                           | Branch State Before and After command      | Output                |
| ---------------------------------------- | ------------------------------------------- | ------------------------------------------------- | ------------------------------------------ | --------------------- |
| `git merge <branch>`                     | Merge branch into current branch            | `git merge feature/login`                         | `main` → `main`                            | Merge result          |
| `git merge --ff-only <branch>`           | Allow only fast-forward                     | `git merge --ff-only feature/login`               | `main` → `main`                            | Fast-forward or error |
| `git merge --no-ff <branch>`             | Always create merge commit                  | `git merge --no-ff feature/login`                 | `main` → `main`                            | Merge commit          |
| `git merge --no-commit <branch>`         | Merge without committing immediately        | `git merge --no-commit feature/login`             | `main` → `main`                            | Merge state           |
| `git merge --no-commit --no-ff <branch>` | Force merge commit but stop before commit   | `git merge --no-commit --no-ff feature/login`     | `main` → `main`                            | Staged merge          |
| `git merge --squash <branch>`            | Apply combined changes without merge commit | `git merge --squash feature/login`                | `main` → `main`                            | Staged changes        |
| `git merge -m <msg> <branch>`            | Set merge message                           | `git merge -m "Merge login" feature/login`        | `main` → `main`                            | Merge commit          |
| `git merge --edit <branch>`              | Edit merge message                          | `git merge --edit feature/login`                  | `main` → `main`                            | Editor                |
| `git merge --no-edit <branch>`           | Use generated message                       | `git merge --no-edit feature/login`               | `main` → `main`                            | Merge result          |
| `git merge --abort`                      | Abort merge                                 | `git merge --abort`                               | Merge in progress → pre-merge state        | Usually none          |
| `git merge --continue`                   | Continue after conflict resolution          | `git merge --continue`                            | Merge in progress → completed merge        | Commit result         |
| `git merge --quit`                       | Stop merge bookkeeping                      | `git merge --quit`                                | Merge in progress → working state retained | Usually none          |
| `git merge -Xours <branch>`              | Prefer ours for eligible conflicts          | `git merge -Xours feature/login`                  | `main` → `main`                            | Merge result          |
| `git merge -Xtheirs <branch>`            | Prefer theirs for eligible conflicts        | `git merge -Xtheirs feature/login`                | `main` → `main`                            | Merge result          |
| `git diff A...B`                         | Preview changes relative to merge base      | `git diff main...feature/login`                   | `main` → `main`                            | Diff                  |
| `git log A..B`                           | Show commits in B not A                     | `git log main..feature/login`                     | `main` → `main`                            | Commit list           |
| `git merge-base A B`                     | Find common ancestor                        | `git merge-base main feature/login`               | `main` → `main`                            | Commit ID             |
| `git merge-base --is-ancestor A B`       | Test ancestry                               | `git merge-base --is-ancestor main feature/login` | `main` → `main`                            | Exit status           |
| `git log --merges`                       | Show merge commits                          | `git log --merges`                                | `main` → `main`                            | Merge history         |
| `git log --first-parent`                 | Follow integration line                     | `git log --first-parent --oneline`                | `main` → `main`                            | Simplified history    |
| `git mergetool`                          | Open merge conflict tool                    | `git mergetool`                                   | Merge in progress → merge in progress      | Tool UI               |
| `git diff --name-only --diff-filter=U`   | List unresolved files                       | `git diff --name-only --diff-filter=U`            | Merge in progress → merge in progress      | File list             |
| `git ls-files -u`                        | Show unmerged index entries                 | `git ls-files -u`                                 | Merge in progress → merge in progress      | Index stages          |
| `git restore --ours -- <file>`           | Restore our side                            | `git restore --ours -- app.js`                    | Conflict → conflict                        | File restored         |
| `git restore --theirs -- <file>`         | Restore their side                          | `git restore --theirs -- app.js`                  | Conflict → conflict                        | File restored         |
| `git revert -m 1 <merge>`                | Revert merge commit                         | `git revert -m 1 abc1234`                         | `main` → `main`                            | New revert commit     |
| `git pull --no-rebase`                   | Pull using merge integration                | `git pull --no-rebase origin main`                | `main` → `main`                            | Fetch + merge         |
| `git pull --ff-only`                     | Pull only if fast-forward possible          | `git pull --ff-only origin main`                  | `main` → `main`                            | Fetch + fast-forward  |

---

# Merge Decision Guide

Use:

```bash
git merge feature
```

when a normal merge is appropriate.

Use:

```bash
git merge --ff-only feature
```

when you **must not create a merge commit**.

Use:

```bash
git merge --no-ff feature
```

when you want an explicit merge commit even if a fast-forward is possible.

Use:

```bash
git merge --squash feature
```

when you want the feature's changes as one new commit without preserving the feature branch's individual commits in the target branch.

Use:

```bash
git merge --abort
```

when a merge is in progress and you want to abandon it.

Use:

```bash
git merge --continue
```

after resolving conflicts and staging the resolved files.

---

# Merge Safety Checklist

Before merging:

```bash
git status
git branch --show-current
git fetch origin
git log --oneline main..feature
git diff main...feature
```

During a conflict:

```bash
git status
git diff --name-only --diff-filter=U
```

After resolving:

```bash
git add <resolved-files>
git merge --continue
```

After completing:

```bash
git status
git log --graph --oneline --decorate -20
```

Before pushing:

```bash
git diff origin/main...HEAD
git log origin/main..HEAD --oneline
```

Then:

```bash
git push origin main
```

---

# Key Concepts to Remember

```text
git merge <branch>
```

means:

```text
merge <branch>
    ↓
into CURRENT branch
```

Fast-forward:

```text
A---B---C  main
         \
          D---E  feature

        ↓ merge

A---B---C---D---E  main
```

Three-way merge:

```text
        D---E  feature
       /     \
A---B---C---F---M  main
```

Force merge commit:

```bash
git merge --no-ff feature
```

Fast-forward only:

```bash
git merge --ff-only feature
```

Squash:

```bash
git merge --squash feature
```

Abort:

```bash
git merge --abort
```

Continue:

```bash
git merge --continue
```

Inspect conflicts:

```bash
git diff --name-only --diff-filter=U
```

Find merge base:

```bash
git merge-base main feature
```

Check ancestry:

```bash
git merge-base --is-ancestor main feature
```

Revert a merge:

```bash
git revert -m 1 <merge-commit>
```

---

## Next Part

**Next file:** `08-rebasing.md`

[Next: Rebasing](08-rebasing.md)
