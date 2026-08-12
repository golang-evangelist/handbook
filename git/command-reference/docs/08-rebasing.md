# 8. Rebasing

This chapter covers Git rebasing from basic rebases to interactive rebasing, conflict resolution, autosquashing, rebase recovery, rebasing onto remote branches, preserving merge commits, automation, and practical Developer, Software Engineer, and DevOps workflows.

---

## Table of Contents

* [8.1 Rebase Concepts](#81-rebase-concepts)
* [8.2 Basic Rebase](#82-basic-rebase)
* [8.3 Rebase Onto Another Branch](#83-rebase-onto-another-branch)
* [8.4 Rebase Onto a Remote Branch](#84-rebase-onto-a-remote-branch)
* [8.5 Interactive Rebase](#85-interactive-rebase)
* [8.6 Interactive Rebase Commands](#86-interactive-rebase-commands)
* [8.7 Reordering Commits](#87-reordering-commits)
* [8.8 Squashing Commits](#88-squashing-commits)
* [8.9 Fixup Commits](#89-fixup-commits)
* [8.10 Autosquash](#810-autosquash)
* [8.11 Editing Commits](#811-editing-commits)
* [8.12 Splitting Commits](#812-splitting-commits)
* [8.13 Dropping Commits](#813-dropping-commits)
* [8.14 Rewording Commits](#814-rewording-commits)
* [8.15 Rebase Conflicts](#815-rebase-conflicts)
* [8.16 Inspecting Rebase Conflicts](#816-inspecting-rebase-conflicts)
* [8.17 Resolving Rebase Conflicts](#817-resolving-rebase-conflicts)
* [8.18 Continue a Rebase](#818-continue-a-rebase)
* [8.19 Skip a Commit](#819-skip-a-commit)
* [8.20 Abort a Rebase](#820-abort-a-rebase)
* [8.21 Quit a Rebase](#821-quit-a-rebase)
* [8.22 Rebase with Autostash](#822-rebase-with-autostash)
* [8.23 Preserve Merge Commits](#823-preserve-merge-commits)
* [8.24 Rebase Strategies](#824-rebase-strategies)
* [8.25 Rebase Options](#825-rebase-options)
* [8.26 Rebase and Remote Branches](#826-rebase-and-remote-branches)
* [8.27 Force Push After Rebase](#827-force-push-after-rebase)
* [8.28 Rebase Recovery](#828-rebase-recovery)
* [8.29 Rebase Safety](#829-rebase-safety)
* [8.30 Developer Workflows](#830-developer-workflows)
* [8.31 DevOps and CI Workflows](#831-devops-and-ci-workflows)
* [8.32 High-Value Rebase Commands](#832-high-value-rebase-commands)

---

# 8.1 Rebase Concepts

Rebase moves or replays commits from one base onto another.

Consider:

```text
A---B---C  main
     \
      D---E  feature
```

If you run:

```bash
git switch feature
git rebase main
```

Git effectively takes commits `D` and `E`, temporarily removes them, moves `feature` to `C`, and replays those commits:

```text
A---B---C---D'---E'  feature
            ↑
           HEAD
```

The commits have new identities because their parent relationships changed.

The original:

```text
D
E
```

become:

```text
D'
E'
```

---

## Rebase versus merge

Merge:

```text
A---B---C---M  main
     \       /
      D---E
```

Rebase:

```text
A---B---C---D'---E'  feature
```

Rebase creates a linear history, while merge preserves the original branch topology.

---

# 8.2 Basic Rebase

The basic syntax is:

```bash
git rebase <upstream>
```

Example:

```bash
git switch feature/login
git rebase main
```

Before:

```text
A---B---C  main
     \
      D---E  feature/login
```

After:

```text
A---B---C---D'---E'  feature/login
```

The `main` branch itself does not move.

---

| Command                                           | Description                                       | Example                                   | Branch State Before and After command      | Output             |
| ------------------------------------------------- | ------------------------------------------------- | ----------------------------------------- | ------------------------------------------ | ------------------ |
| `git rebase <branch>`                             | Replay current branch commits onto another branch | `git rebase main`                         | `feature` → `feature`                      | Rebase result      |
| `git rebase <commit>`                             | Replay commits onto a commit                      | `git rebase abc1234`                      | Current branch → current branch            | Rebase result      |
| `git rebase --continue`                           | Continue after resolving conflicts                | `git rebase --continue`                   | Rebase in progress → next step/completed   | Rebase status      |
| `git rebase --skip`                               | Skip current commit                               | `git rebase --skip`                       | Rebase in progress → next step             | Rebase status      |
| `git rebase --abort`                              | Abort rebase                                      | `git rebase --abort`                      | Rebase in progress → pre-rebase state      | Usually none       |
| `git rebase --quit`                               | Stop rebase bookkeeping                           | `git rebase --quit`                       | Rebase in progress → current working state | Usually none       |
| `git rebase -i <base>`                            | Interactive rebase                                | `git rebase -i main`                      | Current branch → current branch            | Rebase todo editor |
| `git rebase --onto <newbase> <upstream> <branch>` | Move selected commit range                        | `git rebase --onto main old-base feature` | `feature` → rewritten `feature`            | Rebase result      |
| `git rebase --autostash <branch>`                 | Automatically stash local changes                 | `git rebase --autostash main`             | Current branch → current branch            | Rebase result      |
| `git rebase --rebase-merges <base>`               | Preserve merge topology                           | `git rebase --rebase-merges main`         | Current branch → rewritten branch          | Rebase result      |

---

# 8.3 Rebase Onto Another Branch

Suppose:

```text
A---B---C  main
     \
      D---E---F  feature
```

Run:

```bash
git switch feature
git rebase main
```

Result:

```text
A---B---C---D'---E'---F'  feature
```

This is the most common rebase workflow.

---

## Rebase a feature onto development

```bash
git switch feature
git rebase develop
```

Before:

```text
A---B---C  develop
     \
      D---E  feature
```

After:

```text
A---B---C---D'---E'  feature
```

---

# 8.4 Rebase Onto a Remote Branch

A common workflow is:

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

This updates the feature branch on top of the latest remote-tracking `main`.

Before:

```text
origin/main:
A---B---C

feature:
     \
      D---E
```

After:

```text
A---B---C---D'---E'  feature
```

---

## Why `fetch` first?

Without fetching:

```bash
git rebase main
```

uses the local `main` reference.

With:

```bash
git fetch origin
git rebase origin/main
```

you explicitly rebase onto the current remote-tracking branch.

---

# 8.5 Interactive Rebase

Interactive rebase is one of the most powerful Git history-editing commands.

Basic syntax:

```bash
git rebase -i <base>
```

Example:

```bash
git rebase -i HEAD~5
```

This opens an editor containing something similar to:

```text
pick abc1111 Add API endpoint
pick abc2222 Fix typo
pick abc3333 Add tests
pick abc4444 Fix tests
pick abc5555 Update documentation
```

You can modify the commands before Git executes them.

---

# 8.6 Interactive Rebase Commands

The most important interactive rebase actions are:

```text
pick
reword
edit
squash
fixup
exec
break
drop
```

---

| Command  | Description                                   | Example                   | Branch State Before and After command | Output             |
| -------- | --------------------------------------------- | ------------------------- | ------------------------------------- | ------------------ |
| `pick`   | Keep commit unchanged                         | `pick abc1234 Add API`    | Feature history → same logical commit | Commit retained    |
| `reword` | Keep changes but edit message                 | `reword abc1234 Add API`  | Feature history → rewritten commit    | Editor             |
| `edit`   | Pause to modify commit                        | `edit abc1234 Add API`    | Rebase → paused at commit             | Shell/rebase state |
| `squash` | Combine with previous commit and edit message | `squash abc1234 Fix API`  | Two commits → one                     | Commit editor      |
| `fixup`  | Combine with previous commit, discard message | `fixup abc1234 Fix typo`  | Two commits → one                     | Rebased history    |
| `exec`   | Run shell command                             | `exec npm test`           | Rebase → command execution            | Command output     |
| `break`  | Pause rebase                                  | `break`                   | Rebase → paused                       | Rebase paused      |
| `drop`   | Remove commit                                 | `drop abc1234 Debug code` | Commit removed from rewritten history | Rebased history    |

---

# 8.7 Reordering Commits

Given:

```text
A---B---C---D---E
```

Interactive rebase:

```bash
git rebase -i HEAD~4
```

You may see:

```text
pick B Commit B
pick C Commit C
pick D Commit D
pick E Commit E
```

Change to:

```text
pick D Commit D
pick B Commit B
pick C Commit C
pick E Commit E
```

Git attempts to recreate the commits in the new order.

Result:

```text
A---D'---B'---C'---E'
```

This is useful for cleaning up a feature branch before sharing it.

---

# 8.8 Squashing Commits

Suppose:

```text
A---B---C---D---E
```

Interactive rebase:

```bash
git rebase -i HEAD~4
```

Change:

```text
pick B Commit B
pick C Commit C
pick D Commit D
pick E Commit E
```

to:

```text
pick B Commit B
squash C Commit C
squash D Commit D
squash E Commit E
```

Result:

```text
A---B'
```

The four commits become one commit.

Git opens an editor allowing you to create the final commit message.

---

# 8.9 Fixup Commits

A fixup commit is intended to be automatically combined with another commit.

Create one:

```bash
git commit --fixup=<commit>
```

Example:

```bash
git commit --fixup=abc1234
```

Suppose:

```text
A---B---C
```

where `B` is the original feature commit.

A fixup commit creates:

```text
A---B---C---F
```

where `F` has a message such as:

```text
fixup! Original feature
```

---

## Interactive autosquash

```bash
git rebase -i --autosquash HEAD~4
```

Git automatically positions the fixup commit next to its target and marks it as `fixup`.

---

# 8.10 Autosquash

The command:

```bash
git rebase -i --autosquash HEAD~5
```

automatically processes commits created with:

```bash
git commit --fixup=<commit>
```

or:

```bash
git commit --squash=<commit>
```

Example:

```text
A---B---C---F
```

where:

```text
F = fixup! B
```

Running:

```bash
git rebase -i --autosquash HEAD~3
```

automatically rearranges the todo list so the fixup is applied to `B`.

---

## Enable autosquash by default

```bash
git config --global rebase.autoSquash true
```

Then:

```bash
git rebase -i HEAD~5
```

automatically enables autosquash behavior.

---

# 8.11 Editing Commits

Interactive rebase allows you to stop at a commit.

Example:

```text
pick abc1234 Add feature
edit def5678 Add incorrect implementation
pick 789abcd Add tests
```

When Git stops:

```bash
git status
```

You can modify files.

Then:

```bash
git add .
git commit --amend
git rebase --continue
```

---

## Amend the current commit

```bash
git commit --amend
```

Change files and then:

```bash
git add path/to/file
git commit --amend
```

Continue:

```bash
git rebase --continue
```

---

# 8.12 Splitting Commits

Suppose one commit contains unrelated changes.

Interactive rebase:

```bash
git rebase -i HEAD~3
```

Change:

```text
pick abc1234 Large combined commit
```

to:

```text
edit abc1234 Large combined commit
```

When Git stops:

```bash
git reset HEAD^
```

This unstages the commit's changes while leaving the files in the working tree.

Now create smaller commits:

```bash
git add file1
git commit -m "Add API implementation"

git add file2
git commit -m "Add API tests"
```

Then:

```bash
git rebase --continue
```

The original commit has effectively been split into multiple commits.

---

# 8.13 Dropping Commits

Interactive rebase:

```bash
git rebase -i HEAD~5
```

Suppose:

```text
pick A Add API
pick B Debug logging
pick C Add tests
pick D Fix tests
pick E Documentation
```

Change:

```text
pick B Debug logging
```

to:

```text
drop B Debug logging
```

or delete the line.

Result:

```text
A---C'---D'---E'
```

The selected commit is removed from the rewritten branch history.

---

# 8.14 Rewording Commits

Interactive rebase:

```bash
git rebase -i HEAD~4
```

Change:

```text
pick abc1234 bad commit message
```

to:

```text
reword abc1234 bad commit message
```

Git pauses and opens the commit-message editor.

You can replace:

```text
bad commit message
```

with:

```text
Add authentication middleware
```

The content remains unchanged, but the commit receives a new identity because its message changed.

---

# 8.15 Rebase Conflicts

Rebase conflicts happen when a commit being replayed cannot be applied cleanly.

Example:

```text
A---B---C  main
     \
      D---E  feature
```

Run:

```bash
git switch feature
git rebase main
```

Git may stop at commit `D`.

Typical output:

```text
CONFLICT (content): Merge conflict in app.js
error: could not apply abc1234... Update application
```

The repository is now in a rebase-in-progress state.

---

# 8.16 Inspecting Rebase Conflicts

First:

```bash
git status
```

Typical output:

```text
interactive rebase in progress
You are currently rebasing branch 'feature' on 'main'.

Unmerged paths:
  both modified: app.js
```

List conflicted files:

```bash
git diff --name-only --diff-filter=U
```

Inspect changes:

```bash
git diff
```

Inspect staged changes:

```bash
git diff --cached
```

Inspect unmerged index entries:

```bash
git ls-files -u
```

---

# 8.17 Resolving Rebase Conflicts

Suppose the conflicted file contains:

```text
<<<<<<< HEAD
const port = 3000;
=======
const port = 8080;
>>>>>>> abc1234
```

Edit it into the desired final state:

```text
const port = process.env.PORT || 3000;
```

Then stage it:

```bash
git add app.js
```

Continue:

```bash
git rebase --continue
```

If another conflict occurs, repeat the process.

---

# 8.18 Continue a Rebase

After resolving conflicts:

```bash
git add <resolved-files>
git rebase --continue
```

Git then proceeds to the next commit.

If no conflicts remain, the rebase eventually completes.

Verify:

```bash
git status
```

Expected state:

```text
On branch feature
nothing to commit, working tree clean
```

---

# 8.19 Skip a Commit

If the current commit should not be replayed:

```bash
git rebase --skip
```

Example:

```text
rebase in progress
currently applying commit X
```

Run:

```bash
git rebase --skip
```

Git abandons that particular commit and moves to the next one.

Use this carefully because the skipped commit's changes will not be included in the rebased branch.

---

# 8.20 Abort a Rebase

To completely abandon the rebase:

```bash
git rebase --abort
```

Before:

```text
A---B---C  main
     \
      D---E  feature
```

After starting rebase and encountering conflicts:

```text
rebase in progress
```

Then:

```bash
git rebase --abort
```

Git attempts to restore the branch to the state it had before the rebase began.

Verify:

```bash
git status
git log --graph --oneline --decorate
```

---

# 8.21 Quit a Rebase

Use:

```bash
git rebase --quit
```

This stops the rebase process without resetting the current working state.

Conceptually:

```text
--abort
    ↓
cancel + restore pre-rebase state

--quit
    ↓
stop rebase bookkeeping
keep current working state
```

`--quit` is more specialized than `--abort`.

---

# 8.22 Rebase with Autostash

If you have local modifications, Git may refuse to start a rebase.

For example:

```bash
git rebase main
```

may fail because of local changes.

You can use:

```bash
git rebase --autostash main
```

Git temporarily creates a stash, performs the rebase, then attempts to reapply the stash.

---

## Enable autostash globally

```bash
git config --global rebase.autoStash true
```

Then:

```bash
git rebase main
```

can automatically stash and restore compatible local changes.

---

## Caution

Autostash does not guarantee conflict-free restoration.

The stashed changes may themselves conflict when reapplied after the rebase.

Always inspect:

```bash
git status
git diff
```

after the operation.

---

# 8.23 Preserve Merge Commits

By default, ordinary rebase flattens the branch history being replayed.

To preserve merge topology, use:

```bash
git rebase --rebase-merges main
```

Older Git documentation may refer to:

```bash
git rebase --preserve-merges
```

but `--rebase-merges` is the modern interface.

---

## Example

Original history:

```text
A---B---C---------F  feature
     \           /
      D---E-----/
```

A normal rebase may flatten the merge structure.

With:

```bash
git rebase --rebase-merges main
```

Git attempts to recreate the merge topology on the new base.

This is useful when the internal merge structure is meaningful.

---

# 8.24 Rebase Strategies

Rebase uses merge machinery internally when replaying commits.

You can specify strategy options such as:

```bash
git rebase -Xours main
```

or:

```bash
git rebase -Xtheirs main
```

However, the meaning of `ours` and `theirs` during rebase can be surprising.

During a rebase, Git is effectively applying the old branch commits onto the new base. Therefore, the intuitive "ours = my feature branch" assumption can be wrong.

For this reason, do not use:

```bash
git rebase -Xours
```

or:

```bash
git rebase -Xtheirs
```

without understanding which side Git considers which during the replay.

When in doubt, resolve the conflict manually.

---

# 8.25 Rebase Options

## Rebase onto a specific commit

```bash
git rebase abc1234
```

---

## Rebase interactively

```bash
git rebase -i HEAD~5
```

---

## Rebase with autostash

```bash
git rebase --autostash main
```

---

## Preserve merges

```bash
git rebase --rebase-merges main
```

---

## Update references

```bash
git rebase --update-refs main
```

`--update-refs` asks Git to update references that point to commits being rewritten when appropriate.

This can be useful when multiple local branches point into the history being rebased.

---

## Rebase only a specific branch

```bash
git rebase main feature/login
```

This tells Git to rebase the specified branch rather than necessarily requiring you to check it out first.

The exact effect should be understood before using it in automation.

---

# 8.26 Rebase and Remote Branches

A standard developer workflow is:

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

Then inspect:

```bash
git log --oneline --graph --decorate origin/main..HEAD
```

If everything is correct:

```bash
git push --force-with-lease origin feature/login
```

This is necessary because rebasing changed the feature branch's commit IDs.

---

# 8.27 Force Push After Rebase

After:

```bash
git rebase origin/main
```

your local branch history may no longer be a descendant of the remote branch.

A normal:

```bash
git push
```

may fail with:

```text
! [rejected] feature -> feature (non-fast-forward)
```

The safer force-push form is:

```bash
git push --force-with-lease origin feature/login
```

---

## Why `--force-with-lease`?

It checks that the remote reference is still what you expect.

This helps protect against overwriting another person's newly pushed work.

---

## Dangerous alternative

```bash
git push --force origin feature/login
```

This unconditionally forces the remote reference and can overwrite remote history.

Prefer:

```bash
git push --force-with-lease
```

when a rewritten remote branch is genuinely necessary.

---

# 8.28 Rebase Recovery

Rebase rewrites commits, but the original commits may remain temporarily recoverable through the reflog.

Inspect:

```bash
git reflog
```

Example:

```text
abc9999 HEAD@{0}: rebase finished
def8888 HEAD@{1}: rebase: ...
abc7777 HEAD@{2}: checkout: moving from main to feature
```

You can inspect previous states:

```bash
git show HEAD@{1}
```

or:

```bash
git log --oneline HEAD@{5}
```

---

## Recover a branch after an incorrect rebase

First inspect:

```bash
git reflog
```

Find the commit representing the pre-rebase branch tip.

Then create a safety branch:

```bash
git branch recovery <commit>
```

For example:

```bash
git branch recovery abc1234
```

You can then inspect the recovered history before changing anything further.

---

# 8.29 Rebase Safety

Before rebasing:

```bash
git status
```

Create a backup branch if the history is important:

```bash
git branch backup-before-rebase
```

Fetch current remote state:

```bash
git fetch origin
```

Inspect:

```bash
git log --graph --oneline --decorate --all
```

Then rebase:

```bash
git rebase origin/main
```

After completion:

```bash
git status
git log --graph --oneline --decorate --all
```

Before force-pushing:

```bash
git push --force-with-lease
```

---

## Never casually rebase shared history

Avoid rebasing branches that other developers are actively using unless the team explicitly agrees.

Rebase changes commit IDs.

For example:

```text
Before:

A---B---C---D  origin/feature
```

After rebase:

```text
A---B---X---Y  local feature
```

`C` and `D` have effectively been replaced by `X` and `Y`.

Anyone who based work on `C` or `D` now has a divergent history.

---

# 8.30 Developer Workflows

## Workflow A — Update feature branch

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

---

## Workflow B — Clean up recent commits

```bash
git switch feature/login
git rebase -i HEAD~5
```

Then use:

```text
pick
reword
edit
squash
fixup
drop
```

---

## Workflow C — Create a fixup commit

Find the target:

```bash
git log --oneline -10
```

Create fixup:

```bash
git commit --fixup=<commit>
```

Then:

```bash
git rebase -i --autosquash HEAD~5
```

---

## Workflow D — Squash a feature before review

```bash
git rebase -i origin/main
```

Use:

```text
pick
squash
fixup
```

Then:

```bash
git push --force-with-lease origin feature/login
```

---

## Workflow E — Resolve rebase conflict

```bash
git rebase origin/main
git status
git diff --name-only --diff-filter=U
```

Edit files.

Then:

```bash
git add <resolved-file>
git rebase --continue
```

Repeat until complete.

---

## Workflow F — Abandon rebase

```bash
git rebase origin/main
```

If the result is not desired:

```bash
git rebase --abort
```

---

## Workflow G — Split a commit

```bash
git rebase -i HEAD~3
```

Mark the target:

```text
edit <commit>
```

Then:

```bash
git reset HEAD^
```

Create multiple commits:

```bash
git add file1
git commit -m "Add feature"

git add file2
git commit -m "Add tests"
```

Continue:

```bash
git rebase --continue
```

---

# 8.31 DevOps and CI Workflows

## Check whether branch is behind main

```bash
git fetch origin
git rev-list --left-right --count origin/main...HEAD
```

Example:

```text
5  3
```

This can mean:

```text
5 commits only on origin/main
3 commits only on HEAD
```

---

## Check whether main is an ancestor

```bash
git merge-base --is-ancestor origin/main HEAD
```

Exit code:

```text
0 = origin/main is an ancestor of HEAD
1 = it is not
```

---

## Validate branch in CI

A CI system can create a temporary branch and attempt:

```bash
git fetch origin
git rebase origin/main
```

If the rebase exits non-zero, the pipeline can fail before integration.

---

## Verify clean working tree before automation

```bash
git diff --quiet
git diff --cached --quiet
```

These commands can be used in scripts to test whether unstaged or staged changes exist.

---

## Automated rebase

A script may use:

```bash
git fetch origin
git switch feature/login
git rebase origin/main
git push --force-with-lease origin feature/login
```

This should only be automated when the branch ownership and rewrite policy are well understood.

---

# 8.32 High-Value Rebase Commands

| Command                                           | Description                                   | Example                                      | Branch State Before and After command            | Output             |
| ------------------------------------------------- | --------------------------------------------- | -------------------------------------------- | ------------------------------------------------ | ------------------ |
| `git rebase <branch>`                             | Rebase current branch onto another branch     | `git rebase main`                            | `feature` → rewritten `feature`                  | Rebased commits    |
| `git rebase <commit>`                             | Rebase onto specific commit                   | `git rebase abc1234`                         | Current branch → rewritten branch                | Rebased history    |
| `git rebase -i <base>`                            | Interactive history editing                   | `git rebase -i HEAD~5`                       | Current branch → rewritten branch                | Rebase todo        |
| `git rebase --continue`                           | Continue rebase                               | `git rebase --continue`                      | Rebase in progress → next step/completed         | Rebase output      |
| `git rebase --skip`                               | Skip current commit                           | `git rebase --skip`                          | Rebase in progress → next step                   | Rebase output      |
| `git rebase --abort`                              | Cancel rebase                                 | `git rebase --abort`                         | Rebase in progress → pre-rebase state            | Usually none       |
| `git rebase --quit`                               | Stop rebase bookkeeping                       | `git rebase --quit`                          | Rebase in progress → current state               | Usually none       |
| `git rebase --onto <newbase> <upstream> <branch>` | Move selected commit range                    | `git rebase --onto main old feature`         | `feature` → rewritten feature                    | Rebase result      |
| `git rebase --autostash <base>`                   | Temporarily stash local changes               | `git rebase --autostash main`                | Current branch → current branch                  | Rebase result      |
| `git rebase --rebase-merges <base>`               | Preserve merge topology                       | `git rebase --rebase-merges main`            | Branch → rewritten branch                        | Rebase result      |
| `git rebase --update-refs <base>`                 | Update eligible refs                          | `git rebase --update-refs main`              | Multiple refs may move                           | Rebase result      |
| `git commit --fixup=<commit>`                     | Create fixup commit                           | `git commit --fixup=abc1234`                 | Branch → branch + fixup                          | New commit         |
| `git commit --squash=<commit>`                    | Create squash commit                          | `git commit --squash=abc1234`                | Branch → branch + squash                         | New commit         |
| `git rebase -i --autosquash <base>`               | Automatically arrange fixup/squash commits    | `git rebase -i --autosquash HEAD~5`          | Branch → cleaned history                         | Rebase todo/result |
| `git commit --amend`                              | Modify current commit                         | `git commit --amend`                         | Current commit → rewritten commit                | New commit ID      |
| `git reset HEAD^`                                 | Uncommit current commit while keeping changes | `git reset HEAD^`                            | Commit removed from branch tip                   | Status             |
| `git push --force-with-lease`                     | Safely force rewritten history                | `git push --force-with-lease`                | Local rewritten branch → remote rewritten branch | Push result        |
| `git reflog`                                      | Locate previous branch states                 | `git reflog`                                 | No branch change                                 | Reference history  |
| `git branch backup-before-rebase`                 | Create recovery pointer                       | `git branch backup-before-rebase`            | Current branch unchanged                         | New branch         |
| `git log --graph --oneline --decorate --all`      | Inspect rewritten history                     | `git log --graph --oneline --decorate --all` | No branch change                                 | Graph              |

---

# Rebase Decision Guide

Use:

```bash
git rebase main
```

when you want to replay your current branch's commits on top of `main`.

Use:

```bash
git rebase -i HEAD~N
```

when you need to clean up local commit history.

Use:

```bash
git rebase -i --autosquash HEAD~N
```

when using `fixup!` or `squash!` commits.

Use:

```bash
git rebase --continue
```

after resolving a conflict.

Use:

```bash
git rebase --skip
```

when the current commit should intentionally not be replayed.

Use:

```bash
git rebase --abort
```

when you want to abandon the entire rebase.

Use:

```bash
git rebase --rebase-merges main
```

when preserving merge topology is important.

Use:

```bash
git push --force-with-lease
```

after rewriting a branch that must be updated on the remote.

---

# Rebase Safety Checklist

Before rebase:

```bash
git status
git fetch origin
git log --graph --oneline --decorate --all
git branch backup-before-rebase
```

During conflict:

```bash
git status
git diff
git diff --name-only --diff-filter=U
```

After resolving:

```bash
git add <resolved-files>
git rebase --continue
```

After completion:

```bash
git status
git log --graph --oneline --decorate --all
```

Before rewriting remote history:

```bash
git push --force-with-lease
```

If the rebase goes wrong:

```bash
git reflog
```

and recover the previous branch tip if necessary.

---

# Merge vs Rebase Quick Reference

| Operation               | Typical command                   | Result                                  |
| ----------------------- | --------------------------------- | --------------------------------------- |
| Merge feature           | `git merge feature`               | Preserves branch topology               |
| Fast-forward merge      | `git merge --ff-only feature`     | Linear history if possible              |
| Explicit merge commit   | `git merge --no-ff feature`       | Creates merge commit                    |
| Squash integration      | `git merge --squash feature`      | One new non-merge commit                |
| Rebase feature          | `git rebase main`                 | Replays feature commits                 |
| Interactive cleanup     | `git rebase -i main`              | Edit/reorder/squash/drop commits        |
| Preserve merge topology | `git rebase --rebase-merges main` | Replays history while preserving merges |
| Abort merge             | `git merge --abort`               | Restore pre-merge state                 |
| Abort rebase            | `git rebase --abort`              | Restore pre-rebase state                |

---

# Essential Mental Model

A merge says:

```text
"Combine these histories."
```

A rebase says:

```text
"Take my commits and replay them somewhere else."
```

Merge:

```text
A---B---C---M
     \     /
      D---E
```

Rebase:

```text
A---B---C---D'---E'
```

Rebase changes commit identities.

Therefore:

```text
local history
      ↓
rewrite
      ↓
new commit IDs
      ↓
force-with-lease may be required
```

The safest default for a rewritten remote branch is:

```bash
git push --force-with-lease
```

rather than:

```bash
git push --force
```

---

## Next Part

**Next file:** `09-remote-repositories.md`

[Next: Remote Repositories](09-remote-repositories.md)
