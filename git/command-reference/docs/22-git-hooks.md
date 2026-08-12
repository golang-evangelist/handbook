# 22. Git Hooks

Git hooks are executable scripts that Git runs automatically at specific points in the Git lifecycle.

They are useful for:

* Validation
* Code formatting
* Linting
* Tests
* Commit policy enforcement
* Branch policy enforcement
* Release automation
* CI/CD integration
* Deployment automation
* Repository administration

Git hooks are divided into two major categories:

```text
Client-side hooks
Server-side hooks
```

Client-side hooks run on the developer's machine.

Server-side hooks run on the Git server.

---

# Table of Contents

* [22.1 What Are Git Hooks?](#221-what-are-git-hooks)
* [22.2 Git Hooks Directory](#222-git-hooks-directory)
* [22.3 Listing Hooks](#223-listing-hooks)
* [22.4 Checking the Hooks Path](#224-checking-the-hooks-path)
* [22.5 Creating a Hook](#225-creating-a-hook)
* [22.6 Making a Hook Executable](#226-making-a-hook-executable)
* [22.7 pre-commit](#227-pre-commit)
* [22.8 prepare-commit-msg](#228-prepare-commit-msg)
* [22.9 commit-msg](#229-commit-msg)
* [22.10 post-commit](#2210-post-commit)
* [22.11 pre-rebase](#2211-pre-rebase)
* [22.12 post-checkout](#2212-post-checkout)
* [22.13 post-merge](#2213-post-merge)
* [22.14 pre-push](#2214-pre-push)
* [22.15 post-rewrite](#2215-post-rewrite)
* [22.16 applypatch Hooks](#2216-applypatch-hooks)
* [22.17 Server-Side Hooks](#2217-server-side-hooks)
* [22.18 pre-receive](#2218-pre-receive)
* [22.19 update](#2219-update)
* [22.20 post-receive](#2220-post-receive)
* [22.21 post-update](#2221-post-update)
* [22.22 push-to-checkout](#2222-push-to-checkout)
* [22.23 receive.denyCurrentBranch](#2223-receivedenycurrentbranch)
* [22.24 Disabling Hooks](#2224-disabling-hooks)
* [22.25 Bypassing Hooks](#2225-bypassing-hooks)
* [22.26 Custom Hooks Directory](#2226-custom-hooks-directory)
* [22.27 Sharing Hooks With a Team](#2227-sharing-hooks-with-a-team)
* [22.28 Hooks and CI/CD](#2228-hooks-and-cicd)
* [22.29 Hooks and Commit Validation](#2229-hooks-and-commit-validation)
* [22.30 Hooks and Branch Protection](#2230-hooks-and-branch-protection)
* [22.31 Debugging Hooks](#2231-debugging-hooks)
* [22.32 Hook Security](#2232-hook-security)
* [22.33 Common Hook Workflows](#2233-common-hook-workflows)
* [22.34 High-Value Hook Commands](#2234-high-value-hook-commands)
* [22.35 Hook Best Practices](#2235-hook-best-practices)

---

# 22.1 What Are Git Hooks?

A Git hook is an executable file placed in Git's hooks directory.

Git executes the hook when a corresponding event occurs.

Conceptually:

```text
Git command
    |
    v
Git lifecycle event
    |
    v
Hook
    |
    +---- exit 0 ----> Continue
    |
    +---- non-zero --> Abort
```

For example:

```text
git commit
    |
    v
pre-commit
    |
    +---- tests pass ------> continue
    |
    +---- tests fail ------> abort
```

A hook can therefore enforce local policies before Git completes an operation.

---

# 22.2 Git Hooks Directory

The default hooks directory is:

```text
.git/hooks/
```

Inspect it:

```bash
ls -la .git/hooks/
```

Typical output may include:

```text
applypatch-msg.sample
commit-msg.sample
fsmonitor-watchman.sample
post-update.sample
pre-applypatch.sample
pre-commit.sample
pre-merge-commit.sample
pre-push.sample
pre-rebase.sample
pre-receive.sample
prepare-commit-msg.sample
push-to-checkout.sample
update.sample
```

The `.sample` files are examples.

Git does not normally execute files ending in `.sample`.

---

# 22.3 Listing Hooks

List hooks:

```bash
ls -la .git/hooks/
```

Find executable hooks:

```bash
find .git/hooks -maxdepth 1 -type f -executable -print
```

List all hook files:

```bash
find .git/hooks -maxdepth 1 -type f -print
```

---

| Command                               | Description           | Example                               | Branch State Before and After command | Output           |
| ------------------------------------- | --------------------- | ------------------------------------- | ------------------------------------- | ---------------- |
| `ls -la .git/hooks/`                  | List hooks            | `ls -la .git/hooks/`                  | Unchanged                             | Hook files       |
| `find .git/hooks -type f`             | Find hook files       | `find .git/hooks -type f`             | Unchanged                             | Hook paths       |
| `find .git/hooks -type f -executable` | Find executable hooks | `find .git/hooks -type f -executable` | Unchanged                             | Executable hooks |

---

# 22.4 Checking the Hooks Path

Git can use a custom hooks directory.

Check:

```bash
git config core.hooksPath
```

If nothing is returned, Git normally uses:

```text
.git/hooks
```

Show all relevant configuration:

```bash
git config --show-origin --get core.hooksPath
```

Check global configuration:

```bash
git config --global --get core.hooksPath
```

Check repository configuration:

```bash
git config --local --get core.hooksPath
```

---

| Command                                         | Description                        | Example                                         | Branch State Before and After command | Output                    |
| ----------------------------------------------- | ---------------------------------- | ----------------------------------------------- | ------------------------------------- | ------------------------- |
| `git config core.hooksPath`                     | Show configured hooks path         | `git config core.hooksPath`                     | Unchanged                             | Hooks directory           |
| `git config --global --get core.hooksPath`      | Show global hooks path             | `git config --global --get core.hooksPath`      | Unchanged                             | Global path               |
| `git config --local --get core.hooksPath`       | Show repository hooks path         | `git config --local --get core.hooksPath`       | Unchanged                             | Local path                |
| `git config --show-origin --get core.hooksPath` | Show path and configuration source | `git config --show-origin --get core.hooksPath` | Unchanged                             | Configuration source/path |

---

# 22.5 Creating a Hook

Create a `pre-commit` hook:

```bash
nano .git/hooks/pre-commit
```

Example:

```bash
#!/bin/sh

echo "Running pre-commit checks..."

exit 0
```

Save the file.

The hook must be executable.

---

# 22.6 Making a Hook Executable

Use:

```bash
chmod +x .git/hooks/pre-commit
```

Verify:

```bash
ls -l .git/hooks/pre-commit
```

Expected permissions include:

```text
-rwxr-xr-x
```

Test:

```bash
.git/hooks/pre-commit
```

Example output:

```text
Running pre-commit checks...
```

---

| Command                 | Description           | Example                          | Branch State Before and After command | Output            |
| ----------------------- | --------------------- | -------------------------------- | ------------------------------------- | ----------------- |
| `chmod +x HOOK`         | Make hook executable  | `chmod +x .git/hooks/pre-commit` | Unchanged                             | Usually no output |
| `.git/hooks/pre-commit` | Execute hook manually | `.git/hooks/pre-commit`          | Unchanged                             | Hook output       |

---

# 22.7 pre-commit

`pre-commit` executes before Git creates a commit.

Typical uses:

* Linting
* Formatting
* Unit tests
* Secret scanning
* Static analysis
* Checking staged files

Example:

```bash
#!/bin/sh

echo "Running tests..."

npm test || exit 1

exit 0
```

When:

```bash
git commit -m "Update application"
```

runs:

```text
pre-commit
    |
    +---- success ----> commit created
    |
    +---- failure ----> commit aborted
```

A non-zero exit status prevents the commit.

---

| Command                 | Description                           | Example                 | Branch State Before and After command       | Output                 |
| ----------------------- | ------------------------------------- | ----------------------- | ------------------------------------------- | ---------------------- |
| `git commit`            | Triggers `pre-commit` unless bypassed | `git commit -m "Fix"`   | No new commit → new commit if hook succeeds | Hook and commit output |
| `.git/hooks/pre-commit` | Run hook directly                     | `.git/hooks/pre-commit` | Unchanged                                   | Hook output            |

---

# 22.8 prepare-commit-msg

`prepare-commit-msg` executes before the commit message editor is started.

It can automatically modify or prepare the commit message.

Example:

```bash
#!/bin/sh

FILE="$1"

echo "Commit preparation hook"

exit 0
```

Arguments supplied by Git can include:

```text
$1 = commit message file
$2 = commit message source
$3 = commit object name
```

The exact arguments depend on how the commit was initiated.

Useful for:

* Adding issue IDs
* Adding branch information
* Preparing standardized commit messages

---

# 22.9 commit-msg

`commit-msg` executes after the commit message is prepared but before the commit is finalized.

It receives the path to the commit message file as its first argument.

Example:

```bash
#!/bin/sh

FILE="$1"

if ! grep -qE '^([A-Z]+-[0-9]+): ' "$FILE"; then
    echo "Commit message must start with an issue ID."
    echo "Example: DEV-123: Fix login validation"
    exit 1
fi

exit 0
```

Valid:

```text
DEV-123: Fix login validation
```

Invalid:

```text
Fix login validation
```

This hook is commonly used for commit-message validation.

---

# 22.10 post-commit

`post-commit` runs after a successful commit.

Unlike `pre-commit`, it cannot prevent the commit because the commit has already been created.

Example:

```bash
#!/bin/sh

echo "Commit completed: $(git rev-parse --short HEAD)"
```

Typical uses:

* Notifications
* Local automation
* Logging
* Developer tooling

Avoid using `post-commit` for mandatory validation because it is too late to prevent the commit.

---

# 22.11 pre-rebase

`pre-rebase` executes before Git starts a rebase.

Example:

```bash
#!/bin/sh

branch="$(git branch --show-current)"

if [ "$branch" = "main" ]; then
    echo "Rebasing main is prohibited."
    exit 1
fi

exit 0
```

This can enforce local policies around rebasing.

Typical uses:

* Preventing rebases of protected branches
* Checking repository state
* Running validation before history rewriting

---

# 22.12 post-checkout

`post-checkout` runs after:

```bash
git checkout
```

or:

```bash
git switch
```

depending on the operation.

Useful for:

* Regenerating local files
* Switching environment configuration
* Installing dependencies
* Updating generated resources

Example:

```bash
#!/bin/sh

echo "Checkout completed."
```

Git provides information about the previous and new HEAD and whether the operation was a branch checkout.

---

# 22.13 post-merge

`post-merge` executes after a successful merge.

Example:

```bash
#!/bin/sh

echo "Merge completed."

exit 0
```

Typical uses:

* Reinstalling dependencies
* Regenerating files
* Updating local development resources

Example:

```bash
#!/bin/sh

if [ -f package-lock.json ]; then
    npm install
fi
```

Be careful with hooks that automatically modify the working tree.

---

# 22.14 pre-push

`pre-push` runs before Git sends objects to a remote repository.

It can therefore prevent a push.

Typical uses:

* Running tests
* Running linters
* Checking branch names
* Checking credentials
* Preventing accidental pushes

Example:

```bash
#!/bin/sh

echo "Running tests before push..."

npm test || {
    echo "Tests failed. Push aborted."
    exit 1
}

exit 0
```

If the test command fails:

```text
Push
 |
 v
pre-push
 |
 +---- failure ---> push aborted
```

---

# 22.15 post-rewrite

`post-rewrite` runs after commands that rewrite commits, such as:

```bash
git commit --amend
git rebase
```

It can be used by tools that need to update references after rewritten history.

Example:

```bash
#!/bin/sh

echo "History was rewritten."

exit 0
```

This hook is especially relevant to advanced Git tooling.

---

# 22.16 applypatch Hooks

Git provides hooks associated with applying patches:

```text
pre-applypatch
applypatch-msg
post-applypatch
```

They are useful when working with:

```bash
git am
```

For example:

```bash
git am patch.mbox
```

The lifecycle is approximately:

```text
applypatch-msg
      |
      v
pre-applypatch
      |
      v
Patch applied
      |
      v
post-applypatch
```

These hooks are less frequently used in modern application-development workflows but are important when maintaining patch-based workflows.

---

# 22.17 Server-Side Hooks

Server-side hooks execute on the Git server.

Common server-side hooks include:

```text
pre-receive
update
post-receive
post-update
push-to-checkout
```

They can enforce repository policies centrally.

Example:

```text
Developer
    |
    | git push
    v
Git Server
    |
    v
pre-receive
    |
    v
update
    |
    v
References updated
    |
    v
post-receive
```

Unlike client-side hooks, server-side hooks can enforce rules regardless of which client is used.

---

# 22.18 pre-receive

`pre-receive` runs once before the server accepts a push.

It can inspect:

* Old object ID
* New object ID
* Reference name

Input is provided through standard input.

A typical line has the form:

```text
<old-value> <new-value> <ref-name>
```

Example conceptual hook:

```bash
#!/bin/sh

while read oldrev newrev refname
do
    echo "Checking $refname"
done

exit 0
```

A non-zero exit status rejects the entire push.

---

# 22.19 update

`update` runs once for each reference being updated.

It receives:

```text
$1 = ref name
$2 = old object
$3 = new object
```

Example:

```bash
#!/bin/sh

refname="$1"
oldrev="$2"
newrev="$3"

echo "Updating $refname"

exit 0
```

It can enforce branch-specific policies.

Example:

```bash
#!/bin/sh

if [ "$1" = "refs/heads/main" ]; then
    echo "Direct updates to main are prohibited."
    exit 1
fi

exit 0
```

---

# 22.20 post-receive

`post-receive` executes after all references have been successfully updated.

It is commonly used for:

* Deployment
* Notifications
* CI triggers
* Repository synchronization
* Logging

Example:

```bash
#!/bin/sh

echo "Push accepted."
```

Unlike `pre-receive`, it cannot reject an already accepted push.

---

# 22.21 post-update

`post-update` runs after all refs have been updated.

A common historical use is to update server-side information such as:

```bash
git update-server-info
```

Example:

```bash
#!/bin/sh

exec git update-server-info
```

This is mainly relevant to certain Git transport/server configurations.

---

# 22.22 push-to-checkout

`push-to-checkout` is used when a push updates a branch that is associated with a working tree.

This is particularly relevant to deployment-style bare/non-bare repository configurations.

Example conceptual hook:

```bash
#!/bin/sh

echo "Preparing working tree for pushed commit."

exit 0
```

Use carefully because server-side deployment logic can introduce production risks.

---

# 22.23 receive.denyCurrentBranch

For a non-bare repository, pushing directly into the currently checked-out branch can be dangerous.

Inspect:

```bash
git config receive.denyCurrentBranch
```

Configure:

```bash
git config receive.denyCurrentBranch updateInstead
```

Possible policies include:

```text
refuse
warn
ignore
updateInstead
```

For deployment repositories, `updateInstead` can sometimes be useful.

However, a dedicated deployment mechanism is usually safer for production systems.

---

# 22.24 Disabling Hooks

Git supports a global hook bypass:

```bash
git -c core.hooksPath=/dev/null commit -m "Commit without hooks"
```

This prevents hooks configured through the normal hooks path from running for that command.

You can inspect:

```bash
git config core.hooksPath
```

Do not disable hooks casually when they provide important validation.

---

# 22.25 Bypassing Hooks

For supported commands, Git provides:

```bash
git commit --no-verify
```

This bypasses:

```text
pre-commit
commit-msg
```

For pushing:

```bash
git push --no-verify
```

This bypasses the `pre-push` hook.

Examples:

```bash
git commit --no-verify -m "Emergency fix"
```

```bash
git push --no-verify
```

Use these only when you understand what validation is being skipped.

---

| Command                               | Description                           | Example                                           | Branch State Before and After command       | Output         |
| ------------------------------------- | ------------------------------------- | ------------------------------------------------- | ------------------------------------------- | -------------- |
| `git commit --no-verify`              | Skip applicable commit hooks          | `git commit --no-verify -m "Fix"`                 | Working tree → new commit if successful     | Commit output  |
| `git push --no-verify`                | Skip pre-push hook                    | `git push --no-verify`                            | Branch unchanged locally; remote may update | Push output    |
| `git -c core.hooksPath=/dev/null ...` | Run command without normal hooks path | `git -c core.hooksPath=/dev/null commit -m "Fix"` | Depends on command                          | Command output |

---

# 22.26 Custom Hooks Directory

Git can use a custom hooks directory.

Configure:

```bash
git config core.hooksPath .githooks
```

Create:

```bash
mkdir -p .githooks
```

Create:

```bash
nano .githooks/pre-commit
```

Make executable:

```bash
chmod +x .githooks/pre-commit
```

Check:

```bash
git config core.hooksPath
```

Output:

```text
.githooks
```

This makes it easier to store hook scripts inside the repository.

---

# 22.27 Sharing Hooks With a Team

A common project structure is:

```text
project/
├── .git/
├── .githooks/
│   ├── pre-commit
│   └── commit-msg
├── src/
├── tests/
└── README.md
```

Configure:

```bash
git config core.hooksPath .githooks
```

The `.githooks` directory can then be committed:

```bash
git add .githooks
git commit -m "Add repository hooks"
```

However, `core.hooksPath` itself is normally a local configuration setting.

A new developer may need:

```bash
git config core.hooksPath .githooks
```

after cloning.

---

# 22.28 Hooks and CI/CD

Hooks should not replace CI/CD.

A useful architecture is:

```text
Developer
   |
   v
pre-commit
   |
   +---- fast local checks
   |
   v
commit
   |
   v
pre-push
   |
   +---- broader local checks
   |
   v
remote
   |
   v
CI
   |
   +---- authoritative tests
   |
   v
Deployment
```

Local hooks improve developer feedback.

CI provides centralized validation.

---

# 22.29 Hooks and Commit Validation

A common `commit-msg` policy is:

```text
TYPE: description
```

For example:

```text
feat: add authentication
fix: correct login validation
docs: update Git reference
refactor: simplify repository service
```

A hook can validate this pattern.

Example:

```bash
#!/bin/sh

commit_msg="$1"

if ! grep -qE '^(feat|fix|docs|refactor|test|chore): .+' "$commit_msg"; then
    echo "Invalid commit message."
    echo "Expected: type: description"
    exit 1
fi
```

This is useful for teams using Conventional Commits-style conventions.

---

# 22.30 Hooks and Branch Protection

Client-side hooks cannot provide reliable security enforcement.

A developer can bypass them:

```bash
git commit --no-verify
```

or:

```bash
git push --no-verify
```

Therefore:

```text
Client hooks
    =
developer convenience + early feedback
```

while:

```text
Server-side hooks / hosted branch protection
    =
central enforcement
```

Critical security and compliance rules should not depend exclusively on client-side hooks.

---

# 22.31 Debugging Hooks

When a hook fails, first determine which hook is executing.

Inspect:

```bash
git config core.hooksPath
```

List:

```bash
ls -la .git/hooks/
```

or:

```bash
ls -la .githooks/
```

Check executable permissions:

```bash
ls -l .git/hooks/pre-commit
```

Run manually:

```bash
.git/hooks/pre-commit
```

Use shell tracing:

```bash
sh -x .git/hooks/pre-commit
```

For Bash hooks:

```bash
bash -x .git/hooks/pre-commit
```

Check the shebang:

```bash
head -n 1 .git/hooks/pre-commit
```

A typical shebang:

```bash
#!/bin/sh
```

---

# 22.32 Hook Security

Git hooks execute code.

Therefore, treat hooks as executable software.

Never blindly execute hooks from an untrusted repository.

Potential risks include:

```text
credential theft
environment-variable theft
filesystem modification
network communication
malicious commands
supply-chain attacks
```

Inspect a hook before enabling it:

```bash
cat .githooks/pre-commit
```

Check permissions:

```bash
ls -l .githooks/
```

Check whether the repository config points somewhere unexpected:

```bash
git config --show-origin --get core.hooksPath
```

Never configure:

```text
core.hooksPath
```

to an untrusted directory without understanding what Git will execute.

---

# 22.33 Common Hook Workflows

## Workflow 1 — Simple pre-commit validation

Create:

```bash
mkdir -p .githooks
```

Configure:

```bash
git config core.hooksPath .githooks
```

Create:

```bash
nano .githooks/pre-commit
```

Example:

```bash
#!/bin/sh

echo "Running validation..."

git diff --cached --check || exit 1

exit 0
```

Make executable:

```bash
chmod +x .githooks/pre-commit
```

Commit:

```bash
git add .
git commit -m "Add validation hook"
```

---

## Workflow 2 — Commit message validation

Create:

```bash
nano .githooks/commit-msg
```

Example:

```bash
#!/bin/sh

FILE="$1"

if ! grep -qE '^(feat|fix|docs|refactor|test|chore): .+' "$FILE"; then
    echo "Invalid commit message."
    exit 1
fi
```

Make executable:

```bash
chmod +x .githooks/commit-msg
```

Test:

```bash
git commit -m "Invalid message"
```

The commit should be rejected.

Valid example:

```bash
git commit -m "fix: correct login validation"
```

---

## Workflow 3 — Pre-push tests

Create:

```bash
nano .githooks/pre-push
```

Example:

```bash
#!/bin/sh

echo "Running test suite..."

./run-tests.sh || exit 1

exit 0
```

Make executable:

```bash
chmod +x .githooks/pre-push
```

Then:

```bash
git push
```

If the tests fail, the push is rejected.

---

# 22.34 High-Value Hook Commands

| Command                                         | Description                | Example                                         | Branch State Before and After command    | Output               |
| ----------------------------------------------- | -------------------------- | ----------------------------------------------- | ---------------------------------------- | -------------------- |
| `ls -la .git/hooks/`                            | List default hooks         | `ls -la .git/hooks/`                            | Unchanged                                | Hook files           |
| `git config core.hooksPath`                     | Show hooks path            | `git config core.hooksPath`                     | Unchanged                                | Hooks path           |
| `git config --show-origin --get core.hooksPath` | Show hook path and source  | `git config --show-origin --get core.hooksPath` | Unchanged                                | Configuration source |
| `git config core.hooksPath .githooks`           | Set repository hooks path  | `git config core.hooksPath .githooks`           | Unchanged                                | Usually no output    |
| `chmod +x HOOK`                                 | Make hook executable       | `chmod +x .githooks/pre-commit`                 | Unchanged                                | Usually no output    |
| `git commit`                                    | Trigger commit hooks       | `git commit -m "Fix"`                           | New commit if hooks succeed              | Hook/commit output   |
| `git commit --no-verify`                        | Bypass commit hooks        | `git commit --no-verify -m "Fix"`               | New commit if successful                 | Commit output        |
| `git push`                                      | Trigger pre-push hook      | `git push origin main`                          | Remote updated if hooks and push succeed | Hook/push output     |
| `git push --no-verify`                          | Bypass pre-push hook       | `git push --no-verify`                          | Remote may update                        | Push output          |
| `sh -x HOOK`                                    | Trace shell hook execution | `sh -x .githooks/pre-commit`                    | Unchanged                                | Shell trace          |
| `cat HOOK`                                      | Inspect hook               | `cat .githooks/pre-commit`                      | Unchanged                                | Hook source          |
| `head -n 1 HOOK`                                | Inspect hook interpreter   | `head -n 1 .githooks/pre-commit`                | Unchanged                                | Shebang              |

---

# 22.35 Hook Best Practices

## Keep hooks fast

A developer should receive feedback quickly.

Prefer:

```text
pre-commit
    |
    +-- formatting
    +-- linting
    +-- staged-file checks
```

instead of running a 30-minute test suite on every commit.

---

## Validate staged files

For `pre-commit`, focus on staged content when possible.

Useful command:

```bash
git diff --cached --check
```

This checks staged changes for whitespace errors.

---

## Keep authoritative validation in CI

Hooks can be bypassed.

Therefore:

```text
Local hooks = convenience
CI = authoritative validation
Server-side enforcement = centralized policy
```

---

## Version-control shared hooks

A project-level directory such as:

```text
.githooks/
```

can be committed.

Example:

```bash
git add .githooks
git commit -m "Add Git hooks"
```

---

## Make scripts portable

Prefer:

```bash
#!/bin/sh
```

when Bash-specific features are unnecessary.

Avoid assumptions about:

```text
shell
OS
PATH
working directory
installed tools
```

---

## Never store secrets in hooks

Do not hard-code:

```text
passwords
API keys
access tokens
private keys
credentials
```

Hooks are source code and may be committed to repositories.

---

# Git Hook Lifecycle Summary

A typical commit:

```text
git commit
    |
    v
pre-commit
    |
    v
prepare-commit-msg
    |
    v
commit-msg
    |
    v
Commit created
    |
    v
post-commit
```

A typical push:

```text
git push
    |
    v
pre-push
    |
    v
Objects transferred
    |
    v
Remote hooks
    |
    +-- pre-receive
    |
    +-- update
    |
    +-- reference update
    |
    +-- post-receive
    |
    +-- post-update
```

---

# Hook State Model

Hooks generally do **not** change branch state by themselves.

Instead, they influence whether the Git operation proceeds.

For example:

```text
Before:

main
  |
  A---B---C
```

Run:

```bash
git commit -m "D"
```

Successful hook:

```text
main
  |
  A---B---C---D
```

Failed hook:

```text
main
  |
  A---B---C
```

The commit does not occur.

Similarly:

```text
git push
```

with a failing `pre-push` hook leaves the remote unchanged.

---

# Essential Commands to Memorize

```bash
git config core.hooksPath

git config --show-origin --get core.hooksPath

ls -la .git/hooks/

chmod +x .git/hooks/pre-commit

git commit

git commit --no-verify

git push

git push --no-verify

git diff --cached --check

sh -x .git/hooks/pre-commit
```

For repository-managed hooks:

```bash
git config core.hooksPath .githooks
```

Then:

```bash
chmod +x .githooks/pre-commit
chmod +x .githooks/commit-msg
chmod +x .githooks/pre-push
```

---

# Client-Side vs Server-Side Hooks

| Category       | Runs On           | Can Reject Operation? | Typical Use              |
| -------------- | ----------------- | --------------------: | ------------------------ |
| `pre-commit`   | Developer machine |                   Yes | Validation               |
| `commit-msg`   | Developer machine |                   Yes | Message validation       |
| `pre-push`     | Developer machine |                   Yes | Tests                    |
| `post-commit`  | Developer machine |                    No | Notifications            |
| `pre-rebase`   | Developer machine |                   Yes | Rebase policy            |
| `pre-receive`  | Git server        |                   Yes | Central policy           |
| `update`       | Git server        |                   Yes | Per-ref policy           |
| `post-receive` | Git server        |                    No | Deployment/notifications |
| `post-update`  | Git server        |                    No | Server maintenance       |

---

# Final Hook Checklist

```text
[ ] Git hooks are executable
[ ] core.hooksPath points to the intended directory
[ ] Hooks do not contain secrets
[ ] Hooks are reviewed before execution
[ ] Hooks are fast enough for developers
[ ] CI performs authoritative validation
[ ] Critical server-side policies are enforced centrally
[ ] Hooks are portable across developer environments
[ ] Bypass behavior is understood
[ ] Hook failures provide useful error messages
```

---

## Next Part

**Next file:** `23-repository-maintenance.md`

[Next: Repository Maintenance](23-repository-maintenance.md)
