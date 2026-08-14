# 35. Practical Git Aliases

Git aliases allow frequently used commands to be shortened, standardized, and combined into practical developer workflows.

Aliases are especially useful for:

* daily Git operations;
* repository inspection;
* branch management;
* history visualization;
* staging and committing;
* reviewing changes;
* searching history;
* DevOps and CI workflows;
* reducing repetitive command typing.

This chapter provides practical aliases suitable for **Developers, Software Engineers, and DevOps Engineers**.

---

# Table of Contents

* [35.1 Alias Basics](#351-alias-basics)
* [35.2 Viewing Aliases](#352-viewing-aliases)
* [35.3 Creating an Alias](#353-creating-an-alias)
* [35.4 Removing an Alias](#354-removing-an-alias)
* [35.5 Local vs Global Aliases](#355-local-vs-global-aliases)
* [35.6 Status Aliases](#356-status-aliases)
* [35.7 Log and History Aliases](#357-log-and-history-aliases)
* [35.8 Branch Aliases](#358-branch-aliases)
* [35.9 Diff Aliases](#359-diff-aliases)
* [35.10 Staging Aliases](#3510-staging-aliases)
* [35.11 Commit Aliases](#3511-commit-aliases)
* [35.12 Remote Aliases](#3512-remote-aliases)
* [35.13 Fetch and Pull Aliases](#3513-fetch-and-pull-aliases)
* [35.14 Push Aliases](#3514-push-aliases)
* [35.15 Stash Aliases](#3515-stash-aliases)
* [35.16 Tag Aliases](#3516-tag-aliases)
* [35.17 Search Aliases](#3517-search-aliases)
* [35.18 Recovery Aliases](#3518-recovery-aliases)
* [35.19 Cleanup Aliases](#3519-cleanup-aliases)
* [35.20 Conflict Resolution Aliases](#3520-conflict-resolution-aliases)
* [35.21 Rebase Aliases](#3521-rebase-aliases)
* [35.22 Cherry-Pick Aliases](#3522-cherry-pick-aliases)
* [35.23 Worktree Aliases](#3523-worktree-aliases)
* [35.24 Submodule Aliases](#3524-submodule-aliases)
* [35.25 Repository Diagnostics Aliases](#3525-repository-diagnostics-aliases)
* [35.26 Object and Internals Aliases](#3526-object-and-internals-aliases)
* [35.27 DevOps and CI Aliases](#3527-devops-and-ci-aliases)
* [35.28 Useful Shell-Style Git Aliases](#3528-useful-shell-style-git-aliases)
* [35.29 Advanced Aliases](#3529-advanced-aliases)
* [35.30 Safe Aliases](#3530-safe-aliases)
* [35.31 Dangerous Aliases](#3531-dangerous-aliases)
* [35.32 Recommended Developer Alias Set](#3532-recommended-developer-alias-set)
* [35.33 Recommended DevOps Alias Set](#3533-recommended-devops-alias-set)
* [35.34 Complete Practical Alias Configuration](#3534-complete-practical-alias-configuration)
* [35.35 High-Value Aliases to Memorize](#3535-high-value-aliases-to-memorize)

---

# 35.1 Alias Basics

The general syntax is:

```bash
git config --global alias.<name> '<command>'
```

For example:

```bash
git config --global alias.st status
```

After this:

```bash
git st
```

is equivalent to:

```bash
git status
```

Aliases are Git configuration entries.

They can be viewed with:

```bash
git config --global --get-regexp '^alias\.'
```

---

# 35.2 Viewing Aliases

List all configured aliases:

```bash
git config --get-regexp '^alias\.'
```

List global aliases:

```bash
git config --global --get-regexp '^alias\.'
```

Show one alias:

```bash
git config --global --get alias.st
```

Show the complete Git configuration:

```bash
git config --list
```

Show configuration including its source files:

```bash
git config --list --show-origin
```

---

# 35.3 Creating an Alias

Basic alias:

```bash
git config --global alias.st status
```

Alias with options:

```bash
git config --global alias.br 'branch -vv'
```

Alias with multiple Git arguments:

```bash
git config --global alias.co checkout
```

Modern branch switching:

```bash
git config --global alias.sw switch
```

Create a descriptive alias:

```bash
git config --global alias.last 'log -1 HEAD'
```

---

# 35.4 Removing an Alias

Remove an alias:

```bash
git config --global --unset alias.st
```

Remove a local repository alias:

```bash
git config --unset alias.st
```

Verify:

```bash
git config --global --get alias.st
```

If the alias no longer exists, Git produces no value.

---

# 35.5 Local vs Global Aliases

## Global

Available in all repositories:

```bash
git config --global alias.st status
```

Stored in the user's global Git configuration.

## Local

Available only in the current repository:

```bash
git config alias.st status
```

Stored in:

```text
.git/config
```

## System

A system-level configuration can also exist:

```bash
git config --system
```

Global aliases are usually the most practical choice for personal workflows.

---

# 35.6 Status Aliases

## `st`

```bash
git config --global alias.st 'status -sb'
```

Usage:

```bash
git st
```

Example output:

```text
## feature/login...origin/feature/login
 M src/login.js
?? test/login.test.js
```

---

## `s`

Short status:

```bash
git config --global alias.s 'status --short --branch'
```

Usage:

```bash
git s
```

---

## `stat`

```bash
git config --global alias.stat status
```

Usage:

```bash
git stat
```

---

# 35.7 Log and History Aliases

## `lg`

A highly useful history visualization alias:

```bash
git config --global alias.lg 'log --oneline --graph --decorate --all'
```

Usage:

```bash
git lg
```

Example:

```text
* 91ab123 (HEAD -> feature/login) Add login validation
* 82cd456 Add login endpoint
| * 71ef890 (origin/main, main) Update dependencies
|/
* 60ab123 Initial implementation
```

---

## `l`

Compact recent history:

```bash
git config --global alias.l 'log --oneline --decorate -20'
```

---

## `last`

Show the latest commit:

```bash
git config --global alias.last 'log -1 HEAD'
```

---

## `last-commit`

```bash
git config --global alias.last-commit 'show --stat --oneline HEAD'
```

---

## `hist`

More detailed history:

```bash
git config --global alias.hist 'log --graph --pretty=format:"%h %ad | %s%d [%an]" --date=short'
```

---

## `today`

Show today's commits:

```bash
git config --global alias.today 'log --since="today" --oneline --decorate'
```

---

# 35.8 Branch Aliases

## `br`

```bash
git config --global alias.br 'branch -vv'
```

Usage:

```bash
git br
```

Example:

```text
* feature/login  91ab123 [origin/feature/login] Add login validation
  main           71ef890 [origin/main] Update dependencies
```

---

## `branches`

Show all branches:

```bash
git config --global alias.branches 'branch -a'
```

---

## `recent-branches`

Show branches sorted by recent commit:

```bash
git config --global alias.recent-branches 'for-each-ref --sort=-committerdate refs/heads/ --format="%(committerdate:short) %(refname:short) %(objectname:short) %(subject)"'
```

---

## `current`

Show the current branch:

```bash
git config --global alias.current 'branch --show-current'
```

Usage:

```bash
git current
```

---

## `new`

Create and switch to a branch:

```bash
git config --global alias.new 'switch -c'
```

Usage:

```bash
git new feature/payment
```

---

# 35.9 Diff Aliases

## `df`

```bash
git config --global alias.df diff
```

---

## `dc`

Show staged changes:

```bash
git config --global alias.dc 'diff --cached'
```

Usage:

```bash
git dc
```

---

## `ds`

Show working-tree changes:

```bash
git config --global alias.ds 'diff --stat'
```

---

## `dname`

Show changed filenames:

```bash
git config --global alias.dname 'diff --name-only'
```

---

## `dsummary`

Show a summary of changes:

```bash
git config --global alias.dsummary 'diff --stat --summary'
```

---

# 35.10 Staging Aliases

## `aa`

Stage all tracked and untracked files:

```bash
git config --global alias.aa 'add --all'
```

Usage:

```bash
git aa
```

Equivalent to:

```bash
git add --all
```

---

## `ap`

Interactive patch staging:

```bash
git config --global alias.ap 'add --patch'
```

Usage:

```bash
git ap
```

This is useful when a working-tree change contains multiple logical changes.

---

## `unstage`

Modern syntax:

```bash
git config --global alias.unstage 'restore --staged'
```

Usage:

```bash
git unstage file.txt
```

---

# 35.11 Commit Aliases

## `cm`

```bash
git config --global alias.cm commit
```

Usage:

```bash
git cm -m "Add authentication"
```

---

## `ci`

Traditional short form:

```bash
git config --global alias.ci commit
```

---

## `amend`

```bash
git config --global alias.amend 'commit --amend --no-edit'
```

Usage:

```bash
git amend
```

### Warning

This rewrites the latest commit.

Use it primarily before the commit has been shared.

---

## `wip`

Create a work-in-progress commit:

```bash
git config --global alias.wip 'commit -am "WIP"'
```

Usage:

```bash
git wip
```

Note that `commit -am` does not stage untracked files.

---

# 35.12 Remote Aliases

## `remotes`

```bash
git config --global alias.remotes 'remote -v'
```

---

## `remote-branches`

```bash
git config --global alias.remote-branches 'branch -r'
```

---

## `upstream`

Show upstream information:

```bash
git config --global alias.upstream 'rev-parse --abbrev-ref --symbolic-full-name @{u}'
```

---

# 35.13 Fetch and Pull Aliases

## `f`

```bash
git config --global alias.f fetch
```

---

## `fa`

Fetch all remotes:

```bash
git config --global alias.fa 'fetch --all --prune'
```

Usage:

```bash
git fa
```

---

## `fp`

Fetch and prune:

```bash
git config --global alias.fp 'fetch --prune'
```

---

## `pl`

```bash
git config --global alias.pl pull
```

---

## `plr`

Pull with rebase:

```bash
git config --global alias.plr 'pull --rebase'
```

Usage:

```bash
git plr
```

Whether `pull --rebase` is appropriate depends on the team's workflow.

---

# 35.14 Push Aliases

## `ps`

```bash
git config --global alias.ps push
```

---

## `psu`

Push and establish upstream:

```bash
git config --global alias.psu 'push -u origin HEAD'
```

Usage:

```bash
git psu
```

This is convenient for a newly created branch.

---

## `pf`

Force push with lease:

```bash
git config --global alias.pf 'push --force-with-lease'
```

### Warning

Even though `--force-with-lease` is safer than `--force`, it still rewrites remote history.

Do not use this casually on shared branches.

---

# 35.15 Stash Aliases

## `sl`

```bash
git config --global alias.sl 'stash list'
```

---

## `ss`

```bash
git config --global alias.ss stash
```

Usage:

```bash
git ss push -u -m "Temporary work"
```

---

## `sp`

Stash including untracked files:

```bash
git config --global alias.sp 'stash push -u'
```

---

## `pop`

```bash
git config --global alias.pop 'stash pop'
```

---

## `apply-stash`

```bash
git config --global alias.apply-stash 'stash apply'
```

---

# 35.16 Tag Aliases

## `tags`

```bash
git config --global alias.tags 'tag --list'
```

---

## `tag-details`

```bash
git config --global alias.tag-details 'tag -n'
```

---

## `latest-tag`

```bash
git config --global alias.latest-tag 'describe --tags --abbrev=0'
```

Usage:

```bash
git latest-tag
```

---

# 35.17 Search Aliases

## `find`

Search commit messages:

```bash
git config --global alias.find 'log --all --oneline --grep'
```

Usage:

```bash
git find "authentication"
```

---

## `pickaxe`

Search commits that changed a string:

```bash
git config --global alias.pickaxe 'log --all -S'
```

Usage:

```bash
git pickaxe "calculateTotal"
```

---

## `grep-all`

Search tracked files across the repository:

```bash
git config --global alias.grep-all 'grep -n --break --heading'
```

Usage:

```bash
git grep-all "TODO"
```

---

## `file-history`

Show history for a file:

```bash
git config --global alias.file-history 'log --follow --'
```

Usage:

```bash
git file-history src/app.js
```

---

# 35.18 Recovery Aliases

## `reflog`

A short alias:

```bash
git config --global alias.ref 'reflog'
```

Usage:

```bash
git ref
```

---

## `recover`

Create a branch from a known commit:

```bash
git config --global alias.recover 'switch -c recovery'
```

Usage:

```bash
git recover <commit>
```

---

## `fsck-all`

```bash
git config --global alias.fsck-all 'fsck --full --no-reflogs'
```

Use this primarily for repository investigation.

---

# 35.19 Cleanup Aliases

## `prune-remote`

```bash
git config --global alias.prune-remote 'fetch --prune'
```

---

## `clean-preview`

Always preview before cleaning:

```bash
git config --global alias.clean-preview 'clean -nd'
```

Usage:

```bash
git clean-preview
```

---

## `clean-preview-all`

Preview ignored files as well:

```bash
git config --global alias.clean-preview-all 'clean -ndx'
```

### Important

Avoid creating aliases that silently execute:

```bash
git clean -fdx
```

A destructive operation should ideally require an explicit command.

---

# 35.20 Conflict Resolution Aliases

## `conflicts`

Show unmerged files:

```bash
git config --global alias.conflicts 'diff --name-only --diff-filter=U'
```

Usage:

```bash
git conflicts
```

---

## `conflict-status`

```bash
git config --global alias.conflict-status 'status --short'
```

---

## `ours`

During a conflict, restore our side:

```bash
git config --global alias.ours 'checkout --ours --'
```

---

## `theirs`

During a conflict:

```bash
git config --global alias.theirs 'checkout --theirs --'
```

These aliases should be used carefully.

Choosing one side blindly can discard legitimate changes.

---

# 35.21 Rebase Aliases

## `rb`

```bash
git config --global alias.rb rebase
```

---

## `rbi`

Interactive rebase:

```bash
git config --global alias.rbi 'rebase -i'
```

Usage:

```bash
git rbi HEAD~5
```

---

## `rbc`

Continue a rebase:

```bash
git config --global alias.rbc 'rebase --continue'
```

---

## `rba`

Abort a rebase:

```bash
git config --global alias.rba 'rebase --abort'
```

---

## `rbs`

Skip the current commit:

```bash
git config --global alias.rbs 'rebase --skip'
```

---

# 35.22 Cherry-Pick Aliases

## `cp`

```bash
git config --global alias.cp cherry-pick
```

---

## `cpc`

Continue cherry-pick:

```bash
git config --global alias.cpc 'cherry-pick --continue'
```

---

## `cpa`

Abort cherry-pick:

```bash
git config --global alias.cpa 'cherry-pick --abort'
```

---

# 35.23 Worktree Aliases

## `wt`

```bash
git config --global alias.wt worktree
```

Usage:

```bash
git wt list
```

---

## `wt-list`

```bash
git config --global alias.wt-list 'worktree list'
```

---

## `wt-add`

```bash
git config --global alias.wt-add 'worktree add'
```

---

# 35.24 Submodule Aliases

## `sub`

```bash
git config --global alias.sub submodule
```

---

## `sub-status`

```bash
git config --global alias.sub-status 'submodule status'
```

---

## `sub-update`

```bash
git config --global alias.sub-update 'submodule update --init --recursive'
```

---

# 35.25 Repository Diagnostics Aliases

## `info`

Show repository information:

```bash
git config --global alias.info 'status --short --branch'
```

---

## `root`

Show repository root:

```bash
git config --global alias.root 'rev-parse --show-toplevel'
```

Usage:

```bash
git root
```

---

## `git-dir`

Show Git directory:

```bash
git config --global alias.git-dir 'rev-parse --git-dir'
```

---

## `object-format`

Show object format:

```bash
git config --global alias.object-format 'rev-parse --show-object-format'
```

---

## `version`

```bash
git config --global alias.version 'version --build-options'
```

---

# 35.26 Object and Internals Aliases

## `objects`

Show object count and storage:

```bash
git config --global alias.objects 'count-objects -vH'
```

Usage:

```bash
git objects
```

---

## `show-ref`

```bash
git config --global alias.refs 'show-ref'
```

---

## `head`

Show current HEAD:

```bash
git config --global alias.head 'rev-parse HEAD'
```

---

## `short-head`

```bash
git config --global alias.short-head 'rev-parse --short HEAD'
```

---

# 35.27 DevOps and CI Aliases

Aliases can make CI diagnostics easier.

## `ci-status`

```bash
git config --global alias.ci-status 'status --porcelain=v1'
```

This is useful when scripts need machine-readable status information.

---

## `ci-head`

```bash
git config --global alias.ci-head 'rev-parse HEAD'
```

---

## `ci-branch`

```bash
git config --global alias.ci-branch 'rev-parse --abbrev-ref HEAD'
```

---

## `ci-version`

```bash
git config --global alias.ci-version 'describe --tags --always --dirty'
```

This can be useful for generating build identifiers.

Example:

```text
v2.4.1-12-g91ab123
```

or:

```text
91ab123-dirty
```

---

## `ci-changes`

Show changed files between two revisions:

```bash
git config --global alias.ci-changes 'diff --name-only'
```

Usage:

```bash
git ci-changes origin/main...HEAD
```

This can help determine whether a particular CI job needs to run.

---

# 35.28 Useful Shell-Style Git Aliases

Git aliases can execute shell commands when the alias starts with `!`.

Example:

```bash
git config --global alias.root '!git rev-parse --show-toplevel'
```

A shell alias can combine multiple commands.

For example:

```bash
git config --global alias.publish '!git push -u origin HEAD'
```

Usage:

```bash
git publish
```

This pushes the current branch and establishes the upstream branch.

### Important

Shell aliases are more powerful than normal Git aliases.

They can execute arbitrary shell commands.

Therefore, review them carefully before installing them from external sources.

---

# 35.29 Advanced Aliases

## `unstaged`

Show only unstaged files:

```bash
git config --global alias.unstaged 'diff --name-only'
```

---

## `staged`

Show staged files:

```bash
git config --global alias.staged 'diff --cached --name-only'
```

---

## `changed`

Show changed files relative to HEAD:

```bash
git config --global alias.changed 'diff HEAD --name-only'
```

---

## `contributors`

Show commit authors:

```bash
git config --global alias.contributors 'shortlog -sn --all'
```

Example:

```text
120 Alice
 87 Bob
 41 Charlie
```

---

## `branches-by-date`

```bash
git config --global alias.branches-by-date 'for-each-ref --sort=-committerdate refs/heads/ --format="%(committerdate:short) %(refname:short)"'
```

---

## `merged`

Show merged branches:

```bash
git config --global alias.merged 'branch --merged'
```

---

## `unmerged`

Show branches not merged into the current branch:

```bash
git config --global alias.unmerged 'branch --no-merged'
```

---

# 35.30 Safe Aliases

Good aliases should generally make common commands:

* shorter;
* easier to remember;
* more informative;
* less error-prone;
* explicit about destructive behavior.

Recommended safe aliases include:

```bash
git config --global alias.st 'status -sb'
git config --global alias.lg 'log --oneline --graph --decorate --all'
git config --global alias.br 'branch -vv'
git config --global alias.df diff
git config --global alias.dc 'diff --cached'
git config --global alias.aa 'add --all'
git config --global alias.ap 'add --patch'
git config --global alias.f 'fetch'
git config --global alias.fa 'fetch --all --prune'
git config --global alias.plr 'pull --rebase'
git config --global alias.psu 'push -u origin HEAD'
git config --global alias.sl 'stash list'
git config --global alias.ref reflog
git config --global alias.conflicts 'diff --name-only --diff-filter=U'
git config --global alias.root 'rev-parse --show-toplevel'
git config --global alias.current 'branch --show-current'
```

These aliases mostly improve visibility or reduce typing.

---

# 35.31 Dangerous Aliases

Avoid aliases that hide destructive behavior behind short names.

For example, this is a poor alias:

```bash
git config --global alias.cleanup 'clean -fdx'
```

A command such as:

```bash
git cleanup
```

does not clearly communicate that files will be permanently deleted.

Another dangerous example:

```bash
git config --global alias.force 'push --force'
```

Then:

```bash
git force
```

can rewrite remote history without the command itself communicating what is happening.

A better approach is to keep destructive commands explicit:

```bash
git push --force-with-lease
git clean -ndx
git clean -fdx
git reset --hard
```

The extra typing is a useful safety mechanism.

---

# 35.32 Recommended Developer Alias Set

The following is a compact set suitable for everyday software development:

```bash
git config --global alias.st 'status -sb'
git config --global alias.lg 'log --oneline --graph --decorate --all'
git config --global alias.br 'branch -vv'
git config --global alias.current 'branch --show-current'
git config --global alias.new 'switch -c'

git config --global alias.df diff
git config --global alias.dc 'diff --cached'
git config --global alias.dname 'diff --name-only'

git config --global alias.aa 'add --all'
git config --global alias.ap 'add --patch'
git config --global alias.unstage 'restore --staged'

git config --global alias.cm commit
git config --global alias.amend 'commit --amend --no-edit'

git config --global alias.f 'fetch'
git config --global alias.fa 'fetch --all --prune'
git config --global alias.plr 'pull --rebase'
git config --global alias.psu 'push -u origin HEAD'

git config --global alias.sl 'stash list'
git config --global alias.sp 'stash push -u'
git config --global alias.pop 'stash pop'

git config --global alias.ref reflog
git config --global alias.conflicts 'diff --name-only --diff-filter=U'
git config --global alias.root 'rev-parse --show-toplevel'
```

---

# 35.33 Recommended DevOps Alias Set

For DevOps work, repository inspection and deterministic state reporting are especially useful.

```bash
git config --global alias.st 'status -sb'
git config --global alias.root 'rev-parse --show-toplevel'
git config --global alias.head 'rev-parse HEAD'
git config --global alias.short-head 'rev-parse --short HEAD'
git config --global alias.current 'branch --show-current'

git config --global alias.ci-status 'status --porcelain=v1'
git config --global alias.ci-head 'rev-parse HEAD'
git config --global alias.ci-branch 'rev-parse --abbrev-ref HEAD'
git config --global alias.ci-version 'describe --tags --always --dirty'

git config --global alias.fa 'fetch --all --prune'
git config --global alias.remotes 'remote -v'
git config --global alias.tags 'tag --list'
git config --global alias.latest-tag 'describe --tags --abbrev=0'

git config --global alias.objects 'count-objects -vH'
git config --global alias.refs 'show-ref'
git config --global alias.conflicts 'diff --name-only --diff-filter=U'
```

---

# 35.34 Complete Practical Alias Configuration

The following configuration provides a practical baseline for Developers, Software Engineers, and DevOps Engineers.

```bash
# Status
git config --global alias.st 'status -sb'
git config --global alias.s 'status --short --branch'

# History
git config --global alias.l 'log --oneline --decorate -20'
git config --global alias.lg 'log --oneline --graph --decorate --all'
git config --global alias.last 'log -1 HEAD'
git config --global alias.hist 'log --graph --pretty=format:"%h %ad | %s%d [%an]" --date=short'

# Branches
git config --global alias.br 'branch -vv'
git config --global alias.branches 'branch -a'
git config --global alias.current 'branch --show-current'
git config --global alias.new 'switch -c'
git config --global alias.merged 'branch --merged'
git config --global alias.unmerged 'branch --no-merged'

# Diff
git config --global alias.df diff
git config --global alias.dc 'diff --cached'
git config --global alias.ds 'diff --stat'
git config --global alias.dname 'diff --name-only'
git config --global alias.dsummary 'diff --stat --summary'

# Staging
git config --global alias.aa 'add --all'
git config --global alias.ap 'add --patch'
git config --global alias.unstage 'restore --staged'

# Commits
git config --global alias.cm commit
git config --global alias.ci commit
git config --global alias.amend 'commit --amend --no-edit'

# Remote
git config --global alias.remotes 'remote -v'
git config --global alias.remote-branches 'branch -r'

# Fetch / Pull / Push
git config --global alias.f fetch
git config --global alias.fa 'fetch --all --prune'
git config --global alias.fp 'fetch --prune'
git config --global alias.plr 'pull --rebase'
git config --global alias.psu 'push -u origin HEAD'

# Stash
git config --global alias.sl 'stash list'
git config --global alias.sp 'stash push -u'
git config --global alias.pop 'stash pop'

# Tags
git config --global alias.tags 'tag --list'
git config --global alias.tag-details 'tag -n'
git config --global alias.latest-tag 'describe --tags --abbrev=0'

# Search
git config --global alias.find 'log --all --oneline --grep'
git config --global alias.pickaxe 'log --all -S'
git config --global alias.file-history 'log --follow --'

# Recovery
git config --global alias.ref reflog
git config --global alias.fsck-all 'fsck --full --no-reflogs'

# Conflicts
git config --global alias.conflicts 'diff --name-only --diff-filter=U'
git config --global alias.conflict-status 'status --short'

# Rebase
git config --global alias.rb rebase
git config --global alias.rbi 'rebase -i'
git config --global alias.rbc 'rebase --continue'
git config --global alias.rba 'rebase --abort'
git config --global alias.rbs 'rebase --skip'

# Cherry-pick
git config --global alias.cp cherry-pick
git config --global alias.cpc 'cherry-pick --continue'
git config --global alias.cpa 'cherry-pick --abort'

# Worktree
git config --global alias.wt worktree
git config --global alias.wt-list 'worktree list'
git config --global alias.wt-add 'worktree add'

# Submodules
git config --global alias.sub submodule
git config --global alias.sub-status 'submodule status'
git config --global alias.sub-update 'submodule update --init --recursive'

# Diagnostics
git config --global alias.root 'rev-parse --show-toplevel'
git config --global alias.git-dir 'rev-parse --git-dir'
git config --global alias.object-format 'rev-parse --show-object-format'
git config --global alias.objects 'count-objects -vH'
git config --global alias.refs 'show-ref'

# DevOps / CI
git config --global alias.ci-status 'status --porcelain=v1'
git config --global alias.ci-head 'rev-parse HEAD'
git config --global alias.ci-branch 'rev-parse --abbrev-ref HEAD'
git config --global alias.ci-version 'describe --tags --always --dirty'
git config --global alias.ci-changes 'diff --name-only'
```

---

# 35.35 High-Value Aliases to Memorize

If you only want to memorize a small number of aliases, these provide the highest practical value.

## Status

```bash
git st
```

Equivalent:

```bash
git status -sb
```

---

## History

```bash
git lg
```

Equivalent:

```bash
git log --oneline --graph --decorate --all
```

---

## Branches

```bash
git br
```

Equivalent:

```bash
git branch -vv
```

---

## Current branch

```bash
git current
```

Equivalent:

```bash
git branch --show-current
```

---

## Add everything

```bash
git aa
```

Equivalent:

```bash
git add --all
```

---

## Interactive staging

```bash
git ap
```

Equivalent:

```bash
git add --patch
```

---

## Staged diff

```bash
git dc
```

Equivalent:

```bash
git diff --cached
```

---

## Fetch everything

```bash
git fa
```

Equivalent:

```bash
git fetch --all --prune
```

---

## Pull with rebase

```bash
git plr
```

Equivalent:

```bash
git pull --rebase
```

---

## Push current branch

```bash
git psu
```

Equivalent:

```bash
git push -u origin HEAD
```

---

## Stash list

```bash
git sl
```

Equivalent:

```bash
git stash list
```

---

## Reflog

```bash
git ref
```

Equivalent:

```bash
git reflog
```

---

## Conflicts

```bash
git conflicts
```

Equivalent:

```bash
git diff --name-only --diff-filter=U
```

---

## Repository root

```bash
git root
```

Equivalent:

```bash
git rev-parse --show-toplevel
```

---

# Recommended Minimal Alias Set

For most developers, the following aliases provide an excellent balance between convenience and safety:

```bash
git config --global alias.st 'status -sb'
git config --global alias.lg 'log --oneline --graph --decorate --all'
git config --global alias.br 'branch -vv'
git config --global alias.current 'branch --show-current'

git config --global alias.df diff
git config --global alias.dc 'diff --cached'

git config --global alias.aa 'add --all'
git config --global alias.ap 'add --patch'
git config --global alias.unstage 'restore --staged'

git config --global alias.cm commit
git config --global alias.amend 'commit --amend --no-edit'

git config --global alias.fa 'fetch --all --prune'
git config --global alias.plr 'pull --rebase'
git config --global alias.psu 'push -u origin HEAD'

git config --global alias.sl 'stash list'
git config --global alias.ref reflog

git config --global alias.conflicts 'diff --name-only --diff-filter=U'
git config --global alias.root 'rev-parse --show-toplevel'
```

After installing them, verify:

```bash
git config --global --get-regexp '^alias\.'
```

Then test:

```bash
git st
git lg
git br
git current
git root
```

---

# Final Git Alias Recommendations

A good Git alias configuration should follow these principles:

1. **Shorten commands you use frequently.**
2. **Prefer aliases that improve visibility.**
3. **Keep destructive operations explicit.**
4. **Do not hide `reset --hard`, `clean -fdx`, or force pushes behind vague names.**
5. **Use `--force-with-lease` instead of `--force` when rewriting remote history is genuinely necessary.**
6. **Use descriptive names for complex operations.**
7. **Keep CI aliases deterministic and machine-friendly.**
8. **Review shell aliases carefully.**
9. **Avoid copying arbitrary alias configurations without understanding them.**
10. **Use aliases to improve workflow—not to hide Git's behavior.**

The most useful everyday aliases are:

```text
git st
git lg
git br
git current
git aa
git ap
git df
git dc
git fa
git plr
git psu
git sl
git ref
git conflicts
git root
```

These provide a compact command vocabulary for everyday development while preserving the explicitness of Git's more dangerous operations.

---

# Complete 35-Part Git Command Reference

This completes the full **35-part Git Command Reference**:

1. Configuration
2. Creating Repositories
3. Repository Status & Information
4. Staging & Committing
5. Diff & Code Review
6. Branching
7. Merging
8. Rebasing
9. Remote Repositories
10. Undoing Changes
11. Stash
12. Tags & Releases
13. Searching Git History
14. Comparing Branches & Commits
15. Conflict Resolution
16. Cherry-Pick
17. Reflog & Recovery
18. Git Bisect
19. Submodules
20. Worktrees
21. Git LFS
22. Git Hooks
23. Repository Maintenance
24. Repository Diagnostics
25. Git Objects / Internals
26. Ignoring Files
27. File Tracking
28. Sparse Checkout
29. Git Archives
30. Environment & Automation
31. Common DevOps / CI Commands
32. Common Developer Workflows
33. High-Value Commands to Memorize
34. Dangerous Commands
35. Practical Git Aliases
