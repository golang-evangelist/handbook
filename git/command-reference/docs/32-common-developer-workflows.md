# 32. Common Developer Workflows

This chapter presents practical Git workflows commonly used by software developers and software engineers.

Each workflow is designed to be:

* predictable;
* easy to understand;
* suitable for Linux terminals;
* safe for everyday development;
* compatible with team-based Git workflows;
* adaptable to GitHub, GitLab, Bitbucket, and other Git hosting platforms.

> **Important:** The commands below assume that `origin` is the primary remote and `main` is the primary integration branch unless stated otherwise.

---

# Table of Contents

* [32.1 Create a New Repository](#321-create-a-new-repository)
* [32.2 Clone an Existing Repository](#322-clone-an-existing-repository)
* [32.3 Clone and Enter Repository](#323-clone-and-enter-repository)
* [32.4 Inspect a Newly Cloned Repository](#324-inspect-a-newly-cloned-repository)
* [32.5 Start Working on Main](#325-start-working-on-main)
* [32.6 Update Main](#326-update-main)
* [32.7 Create a Feature Branch](#327-create-a-feature-branch)
* [32.8 Create a Feature Branch from Updated Main](#328-create-a-feature-branch-from-updated-main)
* [32.9 Switch to an Existing Branch](#329-switch-to-an-existing-branch)
* [32.10 Create a Branch from a Specific Commit](#3210-create-a-branch-from-a-specific-commit)
* [32.11 Create a Branch from a Tag](#3211-create-a-branch-from-a-tag)
* [32.12 Work on a Feature](#3212-work-on-a-feature)
* [32.13 Inspect Changes](#3213-inspect-changes)
* [32.14 Review Changes Before Staging](#3214-review-changes-before-staging)
* [32.15 Stage Specific Files](#3215-stage-specific-files)
* [32.16 Stage Everything](#3216-stage-everything)
* [32.17 Review Staged Changes](#3217-review-staged-changes)
* [32.18 Commit Changes](#3218-commit-changes)
* [32.19 Make Multiple Commits](#3219-make-multiple-commits)
* [32.20 Amend the Last Commit](#3220-amend-the-last-commit)
* [32.21 Push a New Branch](#3221-push-a-new-branch)
* [32.22 Set Upstream Branch](#3222-set-upstream-branch)
* [32.23 Update Feature Branch](#3223-update-feature-branch)
* [32.24 Rebase Feature Branch onto Main](#3224-rebase-feature-branch-onto-main)
* [32.25 Merge Main into Feature Branch](#3225-merge-main-into-feature-branch)
* [32.26 Prepare a Pull Request](#3226-prepare-a-pull-request)
* [32.27 Review a Pull Request Locally](#3227-review-a-pull-request-locally)
* [32.28 Inspect Remote Branches](#3228-inspect-remote-branches)
* [32.29 Fetch Another Developer's Branch](#3229-fetch-another-developers-branch)
* [32.30 Check Branch Divergence](#3230-check-branch-divergence)
* [32.31 Keep a Branch Up to Date](#3231-keep-a-branch-up-to-date)
* [32.32 Resolve a Merge Conflict](#3232-resolve-a-merge-conflict)
* [32.33 Resolve a Rebase Conflict](#3233-resolve-a-rebase-conflict)
* [32.34 Abort a Merge](#3234-abort-a-merge)
* [32.35 Abort a Rebase](#3235-abort-a-rebase)
* [32.36 Continue a Rebase](#3236-continue-a-rebase)
* [32.37 Continue a Cherry-Pick](#3237-continue-a-cherry-pick)
* [32.38 Temporarily Save Work](#3238-temporarily-save-work)
* [32.39 Restore Stashed Work](#3239-restore-stashed-work)
* [32.40 Create a Clean Experimental Branch](#3240-create-a-clean-experimental-branch)
* [32.41 Compare Feature Branch with Main](#3241-compare-feature-branch-with-main)
* [32.42 Find Commits Introduced by a Branch](#3242-find-commits-introduced-by-a-branch)
* [32.43 Inspect Commit History](#3243-inspect-commit-history)
* [32.44 Find a Commit by Message](#3244-find-a-commit-by-message)
* [32.45 Find a Commit by Author](#3245-find-a-commit-by-author)
* [32.46 Find Changes to a File](#3246-find-changes-to-a-file)
* [32.47 Find Who Changed a Line](#3247-find-who-changed-a-line)
* [32.48 Search the Entire Repository](#3248-search-the-entire-repository)
* [32.49 Search Tracked Files](#3249-search-tracked-files)
* [32.50 Review a Release Tag](#3250-review-a-release-tag)
* [32.51 Create a Release Tag](#3251-create-a-release-tag)
* [32.52 Push a Release Tag](#3252-push-a-release-tag)
* [32.53 Delete a Local Branch](#3253-delete-a-local-branch)
* [32.54 Delete a Remote Branch](#3254-delete-a-remote-branch)
* [32.55 Rename a Branch](#3255-rename-a-branch)
* [32.56 Recover a Deleted Branch](#3256-recover-a-deleted-branch)
* [32.57 Recover Lost Work](#3257-recover-lost-work)
* [32.58 Temporarily Test an Old Commit](#3258-temporarily-test-an-old-commit)
* [32.59 Start a Bug Investigation](#3259-start-a-bug-investigation)
* [32.60 Use Git Bisect](#3260-use-git-bisect)
* [32.61 Create a Hotfix Branch](#3261-create-a-hotfix-branch)
* [32.62 Prepare a Hotfix](#3262-prepare-a-hotfix)
* [32.63 Create a Release Branch](#3263-create-a-release-branch)
* [32.64 Maintain a Release Branch](#3264-maintain-a-release-branch)
* [32.65 Backport a Fix with Cherry-Pick](#3265-backport-a-fix-with-cherry-pick)
* [32.66 Apply a Specific Commit](#3266-apply-a-specific-commit)
* [32.67 Create a Temporary Worktree](#3267-create-a-temporary-worktree)
* [32.68 Work on Two Features Simultaneously](#3268-work-on-two-features-simultaneously)
* [32.69 Review Code Without Changing Current Work](#3269-review-code-without-changing-current-work)
* [32.70 Build from an Exact Commit](#3270-build-from-an-exact-commit)
* [32.71 Generate a Source Archive](#3271-generate-a-source-archive)
* [32.72 Inspect Repository Health](#3272-inspect-repository-health)
* [32.73 Clean Generated Files](#3273-clean-generated-files)
* [32.74 Remove Ignored Build Artifacts](#3274-remove-ignored-build-artifacts)
* [32.75 Handle Generated Files](#3275-handle-generated-files)
* [32.76 Update `.gitignore`](#3276-update-gitignore)
* [32.77 Check What Git Ignores](#3277-check-what-git-ignores)
* [32.78 Recover from an Incorrect Commit](#3278-recover-from-an-incorrect-commit)
* [32.79 Undo a Local Commit](#3279-undo-a-local-commit)
* [32.80 Revert a Published Commit](#3280-revert-a-published-commit)
* [32.81 Split a Commit](#3281-split-a-commit)
* [32.82 Squash Local Commits](#3282-squash-local-commits)
* [32.83 Clean Up Commit History](#3283-clean-up-commit-history)
* [32.84 Prepare a Branch for Code Review](#3284-prepare-a-branch-for-code-review)
* [32.85 Verify a Pull Request Before Push](#3285-verify-a-pull-request-before-push)
* [32.86 Synchronize After Pull Request Merge](#3286-synchronize-after-pull-request-merge)
* [32.87 Remove Merged Branches](#3287-remove-merged-branches)
* [32.88 Start the Next Feature](#3288-start-the-next-feature)
* [32.89 Work with Multiple Remotes](#3289-work-with-multiple-remotes)
* [32.90 Fork-Based Development](#3290-fork-based-development)
* [32.91 Inspect Fork Remotes](#3291-inspect-fork-remotes)
* [32.92 Sync a Fork with Upstream](#3292-sync-a-fork-with-upstream)
* [32.93 Contribute to an Open-Source Project](#3293-contribute-to-an-open-source-project)
* [32.94 Maintain a Long-Lived Feature Branch](#3294-maintain-a-long-lived-feature-branch)
* [32.95 Handle a Force-Pushed Remote Branch](#3295-handle-a-force-pushed-remote-branch)
* [32.96 Recover from a Diverged Branch](#3296-recover-from-a-diverged-branch)
* [32.97 Prepare a Deployment Branch](#3297-prepare-a-deployment-branch)
* [32.98 Deploy an Exact Git Commit](#3298-deploy-an-exact-git-commit)
* [32.99 Roll Back to a Previous Release](#3299-roll-back-to-a-previous-release)
* [32.100 Final Developer Workflow](#32100-final-developer-workflow)

---

# 32.1 Create a New Repository

```bash
mkdir my-project
cd my-project
git init
```

| Command            | Description              | Example            | Branch State Before and After command      | Output                                |
| ------------------ | ------------------------ | ------------------ | ------------------------------------------ | ------------------------------------- |
| `mkdir my-project` | Create project directory | `mkdir my-project` | No repository → directory exists           | Usually none                          |
| `cd my-project`    | Enter directory          | `cd my-project`    | Shell outside directory → inside           | Usually none                          |
| `git init`         | Initialize repository    | `git init`         | No Git repository → repository initialized | `Initialized empty Git repository...` |

Modern Git may create `main` depending on configuration.

Verify:

```bash
git status
```

---

# 32.2 Clone an Existing Repository

```bash
git clone git@example.com:team/project.git
cd project
```

| Command         | Description      | Example                                      | Branch State Before and After command   | Output         |
| --------------- | ---------------- | -------------------------------------------- | --------------------------------------- | -------------- |
| `git clone URL` | Clone repository | `git clone git@example.com:team/project.git` | No local repository → cloned repository | Clone progress |
| `cd project`    | Enter clone      | `cd project`                                 | Outside → inside repository             | Usually none   |

---

# 32.3 Clone and Enter Repository

One command sequence:

```bash
git clone "$REPOSITORY" project &&
cd project
```

This uses `&&` so the second command runs only if cloning succeeds.

---

# 32.4 Inspect a Newly Cloned Repository

```bash
git status
git branch --show-current
git remote -v
git log -1 --oneline
```

Typical output:

```text
On branch main
Your branch is up to date with 'origin/main'.

origin  git@example.com:team/project.git (fetch)
origin  git@example.com:team/project.git (push)

7f8a9c1 Initial project setup
```

---

# 32.5 Start Working on Main

Check branch:

```bash
git switch main
```

Update it:

```bash
git pull --ff-only
```

Preferred explicit form:

```bash
git fetch origin
git merge --ff-only origin/main
```

Branch state:

```text
Before: main may be behind origin/main
After:  main == origin/main
```

---

# 32.6 Update Main

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
```

This avoids creating an unnecessary merge commit.

---

# 32.7 Create a Feature Branch

```bash
git switch -c feature/login
```

Branch state:

```text
Before:

main -> A

After:

main -> A
          \
           feature/login -> A
```

Both names initially point to the same commit.

---

# 32.8 Create a Feature Branch from Updated Main

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git switch -c feature/login
```

This is a good default workflow before starting new work.

---

# 32.9 Switch to an Existing Branch

```bash
git switch feature/login
```

Verify:

```bash
git branch --show-current
```

Output:

```text
feature/login
```

---

# 32.10 Create a Branch from a Specific Commit

```bash
git switch -c experiment 7f8a9c1
```

Branch state:

```text
Before:

A -> B -> C

After:

A -> B -> C
          \
           experiment -> C
```

---

# 32.11 Create a Branch from a Tag

```bash
git switch -c hotfix/v2.4 v2.4.0
```

This starts the branch from the commit referenced by the tag.

---

# 32.12 Work on a Feature

Typical cycle:

```bash
git status
git diff
git add src/login.c
git diff --cached
git commit -m "Add login validation"
```

Repeat as development progresses.

---

# 32.13 Inspect Changes

```bash
git status
git diff
git diff --cached
```

Use:

```text
git status
```

for repository state.

Use:

```text
git diff
```

for unstaged changes.

Use:

```text
git diff --cached
```

for staged changes.

---

# 32.14 Review Changes Before Staging

```bash
git diff
```

Check whitespace:

```bash
git diff --check
```

This is a useful pre-staging review.

---

# 32.15 Stage Specific Files

```bash
git add src/login.c tests/login.test.c
```

Then:

```bash
git status
git diff --cached
```

This provides precise control over the next commit.

---

# 32.16 Stage Everything

```bash
git add .
```

Review:

```bash
git diff --cached
```

Do not blindly commit without checking what was staged.

---

# 32.17 Review Staged Changes

```bash
git diff --cached
```

Summary:

```bash
git diff --cached --stat
```

File names:

```bash
git diff --cached --name-only
```

---

# 32.18 Commit Changes

```bash
git commit -m "Add login validation"
```

Branch state:

```text
Before:

A -> B

After:

A -> B -> C
```

The current branch moves from `B` to `C`.

---

# 32.19 Make Multiple Commits

Example:

```bash
git add src/login.c
git commit -m "Add login validation"

git add tests/login.test.c
git commit -m "Add login validation tests"
```

History:

```text
A -> B -> C -> D
```

Multiple focused commits can make code review and later troubleshooting easier.

---

# 32.20 Amend the Last Commit

Change the message:

```bash
git commit --amend -m "Improve login validation"
```

Add forgotten changes:

```bash
git add forgotten-file.c
git commit --amend --no-edit
```

The commit ID changes.

Avoid amending commits that have already been shared unless the team workflow explicitly allows history rewriting.

---

# 32.21 Push a New Branch

```bash
git push origin feature/login
```

Preferred first push:

```bash
git push -u origin feature/login
```

The `-u` option establishes the upstream relationship.

---

# 32.22 Set Upstream Branch

If the branch already exists remotely:

```bash
git branch --set-upstream-to=origin/feature/login
```

Then:

```bash
git pull
git push
```

can use the configured upstream.

---

# 32.23 Update Feature Branch

```bash
git switch feature/login
git fetch origin
git rebase origin/main
```

Alternative:

```bash
git merge origin/main
```

Choose one strategy according to the team's branching policy.

---

# 32.24 Rebase Feature Branch onto Main

```bash
git fetch origin
git switch feature/login
git rebase origin/main
```

Before:

```text
A -> B -> C       main
      \
       D -> E     feature
```

After:

```text
A -> B -> C -> D' -> E'    feature
               ^
               main
```

The feature commits receive new commit IDs.

---

# 32.25 Merge Main into Feature Branch

```bash
git fetch origin
git switch feature/login
git merge origin/main
```

This preserves the existing feature commits and may create a merge commit.

---

# 32.26 Prepare a Pull Request

Before pushing:

```bash
git fetch origin
git rebase origin/main
git diff origin/main...HEAD
git diff --check
git status
```

Then:

```bash
git push --force-with-lease
```

Use `--force-with-lease` only when the branch was intentionally rebased and history rewriting is expected.

---

# 32.27 Review a Pull Request Locally

Fetch the branch:

```bash
git fetch origin feature/login
```

Inspect:

```bash
git log --oneline origin/main..origin/feature/login
```

Compare:

```bash
git diff origin/main...origin/feature/login
```

---

# 32.28 Inspect Remote Branches

```bash
git branch -r
```

All branches:

```bash
git branch -a
```

Remote references:

```bash
git ls-remote --heads origin
```

---

# 32.29 Fetch Another Developer's Branch

```bash
git fetch origin feature/alice-login
```

Inspect:

```bash
git log --oneline origin/feature/alice-login
```

Create a local branch:

```bash
git switch -c review/alice-login --track origin/feature/alice-login
```

---

# 32.30 Check Branch Divergence

```bash
git fetch origin
git status -sb
```

More detailed:

```bash
git rev-list --left-right --count origin/main...HEAD
```

Example:

```text
3       5
```

Meaning the two sides contain different numbers of commits since their common history.

---

# 32.31 Keep a Branch Up to Date

Recommended rebase workflow:

```bash
git fetch origin
git rebase origin/main
```

Alternative merge workflow:

```bash
git fetch origin
git merge origin/main
```

Do not alternate between strategies unpredictably on the same branch.

---

# 32.32 Resolve a Merge Conflict

Start merge:

```bash
git merge origin/main
```

If conflicts occur:

```bash
git status
```

Edit conflicted files.

Then:

```bash
git add path/to/file
git commit
```

The conflict is resolved when all conflicted paths have been staged and the merge is completed.

---

# 32.33 Resolve a Rebase Conflict

Start:

```bash
git rebase origin/main
```

If a conflict occurs:

```bash
git status
```

Edit files.

Stage:

```bash
git add path/to/file
```

Continue:

```bash
git rebase --continue
```

Repeat until complete.

---

# 32.34 Abort a Merge

```bash
git merge --abort
```

This attempts to restore the state before the merge started.

---

# 32.35 Abort a Rebase

```bash
git rebase --abort
```

This returns the branch to the state it had before the rebase began, assuming Git can restore it cleanly.

---

# 32.36 Continue a Rebase

After resolving a conflict:

```bash
git add path/to/file
git rebase --continue
```

Skip the current commit if appropriate:

```bash
git rebase --skip
```

Use `--skip` carefully because it discards the current rebased commit from the resulting history.

---

# 32.37 Continue a Cherry-Pick

After conflict resolution:

```bash
git add path/to/file
git cherry-pick --continue
```

Abort:

```bash
git cherry-pick --abort
```

---

# 32.38 Temporarily Save Work

```bash
git stash push -m "WIP login"
```

Include untracked files:

```bash
git stash push -u -m "WIP login"
```

Check:

```bash
git stash list
```

---

# 32.39 Restore Stashed Work

Apply without deleting stash:

```bash
git stash apply
```

Apply latest stash and remove it:

```bash
git stash pop
```

Apply a specific stash:

```bash
git stash apply stash@{2}
```

---

# 32.40 Create a Clean Experimental Branch

Save current work:

```bash
git stash push -u -m "temporary work"
```

Update main:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
```

Create experiment:

```bash
git switch -c experiment/new-approach
```

---

# 32.41 Compare Feature Branch with Main

Summary:

```bash
git diff --stat origin/main...HEAD
```

Full diff:

```bash
git diff origin/main...HEAD
```

Files:

```bash
git diff --name-only origin/main...HEAD
```

Commits:

```bash
git log --oneline origin/main..HEAD
```

---

# 32.42 Find Commits Introduced by a Branch

```bash
git log --oneline origin/main..HEAD
```

Equivalent conceptual meaning:

```text
Commits reachable from HEAD
but not reachable from origin/main
```

---

# 32.43 Inspect Commit History

Compact:

```bash
git log --oneline --decorate --graph --all
```

Detailed:

```bash
git log --stat
```

Patch:

```bash
git log -p
```

---

# 32.44 Find a Commit by Message

```bash
git log --all --grep="authentication"
```

Case-insensitive:

```bash
git log --all --grep="authentication" -i
```

---

# 32.45 Find a Commit by Author

```bash
git log --all --author="Alice"
```

Email:

```bash
git log --all --author="alice@example.com"
```

---

# 32.46 Find Changes to a File

```bash
git log -- path/to/file
```

Follow file history across renames:

```bash
git log --follow -- path/to/file
```

Show patches:

```bash
git log -p -- path/to/file
```

---

# 32.47 Find Who Changed a Line

```bash
git blame path/to/file
```

Specific range:

```bash
git blame -L 50,80 path/to/file
```

Blame identifies the commit associated with each line.

---

# 32.48 Search the Entire Repository

```bash
git grep "TODO"
```

Case-insensitive:

```bash
git grep -n -i "deprecated"
```

Specific branch:

```bash
git grep "TODO" origin/main
```

---

# 32.49 Search Tracked Files

```bash
git grep "function_name"
```

This searches files tracked by Git rather than arbitrary generated files in the working directory.

---

# 32.50 Review a Release Tag

```bash
git show v2.4.0
```

List commits since previous tag:

```bash
git log v2.3.0..v2.4.0 --oneline
```

Compare releases:

```bash
git diff v2.3.0..v2.4.0
```

---

# 32.51 Create a Release Tag

Ensure clean state:

```bash
git status
```

Create annotated tag:

```bash
git tag -a v2.4.0 -m "Release v2.4.0"
```

Inspect:

```bash
git show v2.4.0
```

---

# 32.52 Push a Release Tag

```bash
git push origin v2.4.0
```

Verify:

```bash
git ls-remote --tags origin v2.4.0
```

---

# 32.53 Delete a Local Branch

Safe deletion:

```bash
git branch -d feature/login
```

Force deletion:

```bash
git branch -D feature/login
```

Prefer `-d` because Git refuses to delete branches with potentially unmerged commits.

---

# 32.54 Delete a Remote Branch

```bash
git push origin --delete feature/login
```

Then clean stale references:

```bash
git fetch --prune
```

---

# 32.55 Rename a Branch

Rename current branch:

```bash
git branch -m feature/login feature/authentication
```

Push new name:

```bash
git push -u origin feature/authentication
```

Delete old remote branch:

```bash
git push origin --delete feature/login
```

---

# 32.56 Recover a Deleted Branch

Find previous branch tip:

```bash
git reflog
```

Identify the required commit:

```text
7f8a9c1 HEAD@{3}: commit: Add authentication
```

Recover:

```bash
git switch -c feature/recovered 7f8a9c1
```

---

# 32.57 Recover Lost Work

Inspect reflog:

```bash
git reflog --all
```

Find the lost commit:

```bash
git show 7f8a9c1
```

Create a recovery branch:

```bash
git switch -c recovery 7f8a9c1
```

This is safer than immediately resetting an existing branch.

---

# 32.58 Temporarily Test an Old Commit

```bash
git switch --detach 7f8a9c1
```

Run tests:

```bash
./run-tests.sh
```

Return to branch:

```bash
git switch feature/login
```

No branch is changed by the detached checkout.

---

# 32.59 Start a Bug Investigation

Record current state:

```bash
git status
git rev-parse HEAD
```

Update history:

```bash
git fetch --all --prune
```

Inspect recent commits:

```bash
git log --oneline --decorate --graph --all -30
```

Identify known-good and known-bad commits.

Then use `git bisect`.

---

# 32.60 Use Git Bisect

Start:

```bash
git bisect start
```

Mark current commit bad:

```bash
git bisect bad
```

Mark known-good commit:

```bash
git bisect good v2.3.0
```

Git checks out a candidate commit.

Test:

```bash
./run-tests.sh
```

Mark result:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Repeat until Git identifies the first bad commit.

Finish:

```bash
git bisect reset
```

---

# 32.61 Create a Hotfix Branch

Start from the production release:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main

git switch -c hotfix/login-timeout
```

Alternatively start from a release tag:

```bash
git switch -c hotfix/login-timeout v2.4.0
```

---

# 32.62 Prepare a Hotfix

Typical workflow:

```bash
git switch hotfix/login-timeout
git add .
git commit -m "Fix login timeout"
git push -u origin hotfix/login-timeout
```

After review and deployment, tag the resulting release as appropriate.

---

# 32.63 Create a Release Branch

Example:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git switch -c release/2.5
```

Push:

```bash
git push -u origin release/2.5
```

---

# 32.64 Maintain a Release Branch

Update:

```bash
git switch release/2.5
git fetch origin
git pull --ff-only
```

Apply approved fixes:

```bash
git cherry-pick <commit>
```

Run tests:

```bash
./run-tests.sh
```

---

# 32.65 Backport a Fix with Cherry-Pick

Find the fix:

```bash
git log --oneline main
```

Switch to maintenance branch:

```bash
git switch release/2.4
```

Apply:

```bash
git cherry-pick 7f8a9c1
```

Resolve conflicts if necessary.

Then:

```bash
git push
```

---

# 32.66 Apply a Specific Commit

```bash
git cherry-pick 7f8a9c1
```

Multiple commits:

```bash
git cherry-pick 7f8a9c1 4e2d123
```

Range:

```bash
git cherry-pick A..B
```

Review the resulting commit before pushing.

---

# 32.67 Create a Temporary Worktree

```bash
git worktree add --detach ../project-review HEAD
```

Enter:

```bash
cd ../project-review
```

Remove later:

```bash
cd ../project
git worktree remove ../project-review
```

---

# 32.68 Work on Two Features Simultaneously

Create worktrees:

```bash
git worktree add ../feature-a feature/a
git worktree add ../feature-b feature/b
```

Now:

```text
project/
feature-a/
feature-b/
```

Each worktree can have a different branch checked out.

This avoids repeatedly stashing or switching branches.

---

# 32.69 Review Code Without Changing Current Work

Create a worktree:

```bash
git worktree add --detach ../review origin/feature/someone-else
```

Review:

```bash
cd ../review
git diff origin/main...HEAD
```

Return to the original project:

```bash
cd ../project
```

Remove review worktree:

```bash
git worktree remove ../review
```

---

# 32.70 Build from an Exact Commit

```bash
git fetch origin
git checkout --detach "$COMMIT"
```

Verify:

```bash
test "$(git rev-parse HEAD)" = "$COMMIT"
```

Then:

```bash
./build.sh
```

This produces a build from a known immutable Git state.

---

# 32.71 Generate a Source Archive

```bash
VERSION="$(git describe --tags --always)"

git archive \
    --format=tar.gz \
    --prefix="project-${VERSION}/" \
    --output="project-${VERSION}.tar.gz" \
    HEAD
```

Verify:

```bash
tar -tzf "project-${VERSION}.tar.gz" >/dev/null
```

---

# 32.72 Inspect Repository Health

```bash
git status
git fsck --full
git count-objects -vH
git worktree list
```

This provides a basic health overview.

---

# 32.73 Clean Generated Files

Preview:

```bash
git clean -nd
```

Delete untracked files:

```bash
git clean -fd
```

The preview step is strongly recommended before destructive cleanup.

---

# 32.74 Remove Ignored Build Artifacts

Preview:

```bash
git clean -ndX
```

Delete ignored files:

```bash
git clean -fdX
```

This can remove:

* build output;
* caches;
* generated files;
* ignored temporary data.

Use with caution.

---

# 32.75 Handle Generated Files

If generated files should not be committed:

```bash
printf '%s\n' "build/" >> .gitignore
```

If already tracked:

```bash
git rm -r --cached build/
```

Then:

```bash
git add .gitignore
git commit -m "Ignore generated build files"
```

---

# 32.76 Update `.gitignore`

Example:

```bash
cat >> .gitignore <<'EOF'
build/
dist/
*.log
.env
EOF
```

Review:

```bash
git diff -- .gitignore
```

Then:

```bash
git add .gitignore
git commit -m "Update Git ignore rules"
```

---

# 32.77 Check What Git Ignores

```bash
git status --ignored
```

Check a specific path:

```bash
git check-ignore -v build/output.bin
```

This shows which ignore rule caused the file to be ignored.

---

# 32.78 Recover from an Incorrect Commit

If the commit has not been pushed, inspect:

```bash
git log --oneline -5
```

Possible options include:

```bash
git reset --soft HEAD~1
```

or:

```bash
git reset --mixed HEAD~1
```

or:

```bash
git reset --hard HEAD~1
```

Use `--hard` only when you are certain that the affected working-tree changes can be discarded.

---

# 32.79 Undo a Local Commit

Keep changes staged:

```bash
git reset --soft HEAD~1
```

Keep changes unstaged:

```bash
git reset HEAD~1
```

Discard commit and changes:

```bash
git reset --hard HEAD~1
```

Branch state:

```text
Before:

A -> B -> C

After reset:

A -> B
```

The old `C` may still be recoverable through the reflog.

---

# 32.80 Revert a Published Commit

For a commit already shared with others:

```bash
git revert 7f8a9c1
```

This creates a new commit that reverses the selected change.

History:

```text
A -> B -> C -> D
          ^    ^
       bad    revert
```

This is generally safer for shared branches than rewriting history.

---

# 32.81 Split a Commit

Start interactive rebase:

```bash
git rebase -i HEAD~3
```

Mark the target commit as:

```text
edit <commit>
```

Then:

```bash
git reset HEAD^
```

Now create separate commits:

```bash
git add file-a
git commit -m "Implement feature"

git add file-b
git commit -m "Add tests"
```

Continue:

```bash
git rebase --continue
```

---

# 32.82 Squash Local Commits

```bash
git rebase -i HEAD~3
```

Example:

```text
pick A First implementation
squash B Fix implementation
squash C Add tests
```

Save and edit the resulting commit message.

---

# 32.83 Clean Up Commit History

Before pushing a feature branch:

```bash
git rebase -i origin/main
```

Possible operations:

```text
pick
reword
edit
squash
fixup
drop
```

After rewriting:

```bash
git push --force-with-lease
```

Never use history rewriting casually on a shared branch.

---

# 32.84 Prepare a Branch for Code Review

Recommended sequence:

```bash
git status
git fetch origin
git rebase origin/main
git diff origin/main...HEAD
git diff --check
git log --oneline origin/main..HEAD
```

Run project checks:

```bash
./build.sh
./run-tests.sh
```

Then push:

```bash
git push --force-with-lease
```

Use normal `git push` if no history rewrite occurred.

---

# 32.85 Verify a Pull Request Before Push

```bash
git status
git diff --check
git log --oneline origin/main..HEAD
git diff --stat origin/main...HEAD
```

Check for accidental files:

```bash
git status --short
```

Check the final staged/committed state before publishing.

---

# 32.86 Synchronize After Pull Request Merge

After the feature is merged:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
```

Delete local feature branch:

```bash
git branch -d feature/login
```

Prune stale remote references:

```bash
git fetch --prune
```

---

# 32.87 Remove Merged Branches

List merged branches:

```bash
git branch --merged main
```

Delete a merged branch:

```bash
git branch -d feature/login
```

Never automatically delete branches merely because they appear in a broad listing without checking their state.

---

# 32.88 Start the Next Feature

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git switch -c feature/next-feature
```

This provides a clean starting point.

---

# 32.89 Work with Multiple Remotes

Add another remote:

```bash
git remote add upstream git@example.com:upstream/project.git
```

List:

```bash
git remote -v
```

Fetch:

```bash
git fetch upstream
```

Now:

```text
origin
upstream
```

can represent different repositories.

---

# 32.90 Fork-Based Development

Typical structure:

```text
origin   -> your fork
upstream -> original project
```

Add upstream:

```bash
git remote add upstream git@example.com:original/project.git
```

Fetch:

```bash
git fetch upstream
```

Create feature:

```bash
git switch -c feature/improvement upstream/main
```

---

# 32.91 Inspect Fork Remotes

```bash
git remote -v
```

Example:

```text
origin    git@example.com:alice/project.git (fetch)
origin    git@example.com:alice/project.git (push)
upstream  git@example.com:company/project.git (fetch)
upstream  git@example.com:company/project.git (push)
```

---

# 32.92 Sync a Fork with Upstream

```bash
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
```

Alternative rebase-based approach:

```bash
git fetch upstream
git rebase upstream/main
git push --force-with-lease origin main
```

Use the approach appropriate to your fork policy.

---

# 32.93 Contribute to an Open-Source Project

Typical workflow:

```bash
git clone "$FORK_URL"
cd project

git remote add upstream "$UPSTREAM_URL"

git fetch upstream
git switch -c feature/improvement upstream/main

# edit files

git add .
git commit -m "Improve documentation"

git push -u origin feature/improvement
```

Then create a pull request through the hosting platform.

---

# 32.94 Maintain a Long-Lived Feature Branch

Regularly update:

```bash
git fetch origin
git rebase origin/main
```

Resolve conflicts early rather than allowing a large divergence to accumulate.

After rebase:

```bash
git push --force-with-lease
```

---

# 32.95 Handle a Force-Pushed Remote Branch

Fetch:

```bash
git fetch origin
```

Inspect:

```bash
git log --oneline --graph --decorate HEAD origin/feature/login
```

Compare:

```bash
git rev-list --left-right --count HEAD...origin/feature/login
```

If the remote history was intentionally rewritten, coordinate with the branch owner before resetting local work.

---

# 32.96 Recover from a Diverged Branch

Inspect:

```bash
git status
git log --oneline --graph --decorate --all
```

Determine whether you want:

```text
merge
```

or:

```text
rebase
```

Merge:

```bash
git merge origin/main
```

Rebase:

```bash
git rebase origin/main
```

Do not reset or force-push until the intended history is understood.

---

# 32.97 Prepare a Deployment Branch

Update:

```bash
git fetch origin
git switch deployment
git merge --ff-only origin/main
```

Verify:

```bash
git rev-parse HEAD
git status --porcelain
```

Tag if the deployment process requires a release reference:

```bash
git tag -a v2.5.0 -m "Release v2.5.0"
```

---

# 32.98 Deploy an Exact Git Commit

Resolve:

```bash
COMMIT="$(git rev-parse HEAD)"
```

Verify:

```bash
git rev-parse --verify "$COMMIT^{commit}"
```

Deploy:

```bash
./deploy.sh "$COMMIT"
```

The deployment system should record the exact Git SHA.

---

# 32.99 Roll Back to a Previous Release

Find releases:

```bash
git tag --sort=-version:refname
```

Inspect:

```bash
git show v2.4.0
```

Checkout exact release:

```bash
git checkout --detach v2.4.0
```

For production systems, the actual rollback mechanism should be defined by the deployment platform.

A Git checkout alone is not necessarily a production rollback.

---

# 32.100 Final Developer Workflow

A strong general-purpose developer workflow can be summarized as:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main

git switch -c feature/my-feature

# Work
git status
git diff

# Stage
git add path/to/files

# Review
git diff --cached
git diff --cached --check

# Commit
git commit -m "Implement my feature"

# Synchronize
git fetch origin
git rebase origin/main

# Verify
git status
git diff origin/main...HEAD
git log --oneline origin/main..HEAD

# Publish
git push -u origin feature/my-feature
```

For subsequent updates:

```bash
git fetch origin
git rebase origin/main
git push --force-with-lease
```

After the pull request is merged:

```bash
git switch main
git fetch origin
git merge --ff-only origin/main
git branch -d feature/my-feature
git fetch --prune
```

---

# Recommended Developer Workflow

```text
                    origin/main
                         |
                         v
                 Update local main
                         |
                         v
                 Create feature branch
                         |
                         v
                    Write code
                         |
                         v
                   git status
                         |
                         v
                    git diff
                         |
                         v
                     git add
                         |
                         v
                git diff --cached
                         |
                         v
                    git commit
                         |
                         v
                 git fetch origin
                         |
                         v
                 rebase/merge main
                         |
                         v
                    Run tests
                         |
                         v
                  Review complete
                         |
                         v
                      git push
                         |
                         v
                    Pull Request
                         |
                         v
                       Merge
                         |
                         v
                    Update main
                         |
                         v
                 Delete feature branch
```

---

# Developer Workflow Rules

## Rule 1 — Start from an Updated Base

Prefer:

```bash
git fetch origin
git merge --ff-only origin/main
```

before creating a new feature branch.

---

## Rule 2 — Inspect Before Committing

Use:

```bash
git status
git diff
git diff --cached
```

Do not treat `git add .` as equivalent to reviewing the changes.

---

## Rule 3 — Keep Commits Focused

Prefer:

```text
Add authentication validation
Add authentication tests
Update authentication documentation
```

over one large unrelated commit containing all three categories.

---

## Rule 4 — Use Rebase Carefully

Rebase is useful for local feature branches:

```bash
git rebase origin/main
```

But remember:

> Rebase rewrites commit IDs.

Do not rewrite shared history without an agreed team workflow.

---

## Rule 5 — Prefer `--force-with-lease`

When a force push is genuinely required:

```bash
git push --force-with-lease
```

Prefer this over:

```bash
git push --force
```

`--force-with-lease` provides an additional safety check against overwriting unexpected remote updates.

---

## Rule 6 — Use Revert for Published History

For shared branches:

```bash
git revert <commit>
```

is generally preferable to:

```bash
git reset --hard
git push --force
```

---

## Rule 7 — Use Reflog for Recovery

When a branch or commit appears to have disappeared:

```bash
git reflog
```

is one of the first commands to try.

---

## Rule 8 — Use Worktrees for Parallel Work

Instead of:

```text
stash
switch
work
stash
switch
```

consider:

```bash
git worktree add ../feature-a feature/a
git worktree add ../feature-b feature/b
```

This is particularly useful for:

* code reviews;
* hotfixes;
* parallel features;
* release preparation;
* testing multiple versions.

---

## Rule 9 — Use Exact Commits for Builds and Deployments

Instead of relying solely on:

```text
main
```

record:

```bash
git rev-parse HEAD
```

The SHA identifies the exact repository state.

---

## Rule 10 — Keep the Working Tree Understandable

Before switching contexts:

```bash
git status
```

If necessary:

```bash
git stash push -u -m "temporary work"
```

or create a worktree.

---

# Common Workflow Decision Table

| Situation                        | Recommended approach | Main command                  |
| -------------------------------- | -------------------- | ----------------------------- |
| New feature                      | Feature branch       | `git switch -c feature/name`  |
| Update feature from main         | Rebase               | `git rebase origin/main`      |
| Shared branch needs undo         | Revert               | `git revert <commit>`         |
| Local unpushed commit needs undo | Reset                | `git reset`                   |
| Temporary unfinished work        | Stash                | `git stash push -u`           |
| Two tasks simultaneously         | Worktree             | `git worktree add`            |
| Backport one fix                 | Cherry-pick          | `git cherry-pick <commit>`    |
| Find regression                  | Bisect               | `git bisect start`            |
| Recover deleted branch           | Reflog               | `git reflog`                  |
| Review another branch            | Worktree             | `git worktree add`            |
| Create release                   | Annotated tag        | `git tag -a`                  |
| Publish release                  | Push tag             | `git push origin <tag>`       |
| Compare feature with main        | Diff                 | `git diff origin/main...HEAD` |
| Find changed files               | Diff name-only       | `git diff --name-only`        |
| Update fork                      | Upstream fetch/merge | `git fetch upstream`          |
| Build exact version              | Detached checkout    | `git checkout --detach <SHA>` |
| Remove stale remotes             | Prune                | `git fetch --prune`           |
| Clean generated files            | Clean                | `git clean -fd`               |
| Verify repository                | FSCK                 | `git fsck --full`             |

---

# Next Part

**Next file:** `33-high-value-commands-to-memorize.md`

[Next: High-Value Commands to Memorize](33-high-value-commands-to-memorize.md)
