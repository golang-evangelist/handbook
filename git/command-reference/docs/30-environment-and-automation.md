# 30. Environment & Automation

Git is designed to work well in automated environments such as:

* Shell scripts
* Local development tooling
* CI/CD pipelines
* Build systems
* Deployment scripts
* Release automation
* Git hooks
* Docker containers
* Kubernetes jobs
* Cron jobs
* Infrastructure automation

This chapter focuses on commands and techniques that make Git predictable and scriptable.

The key principle is:

> Automation should prefer explicit, machine-readable, non-interactive Git commands.

---

# Table of Contents

* [30.1 Git Environment Variables](#301-git-environment-variables)
* [30.2 Inspect the Git Version](#302-inspect-the-git-version)
* [30.3 Identify the Git Executable](#303-identify-the-git-executable)
* [30.4 Inspect Git Configuration](#304-inspect-git-configuration)
* [30.5 Configuration Precedence](#305-configuration-precedence)
* [30.6 Override Configuration Temporarily](#306-override-configuration-temporarily)
* [30.7 Set Repository-Local Configuration](#307-set-repository-local-configuration)
* [30.8 Set Global Configuration](#308-set-global-configuration)
* [30.9 Git Environment Variables for Identity](#309-git-environment-variables-for-identity)
* [30.10 Git Environment Variables for Repository Location](#3010-git-environment-variables-for-repository-location)
* [30.11 GIT_DIR](#3011-git_dir)
* [30.12 GIT_WORK_TREE](#3012-git_work_tree)
* [30.13 GIT_INDEX_FILE](#3013-git_index_file)
* [30.14 GIT_CEILING_DIRECTORIES](#3014-git_ceiling_directories)
* [30.15 GIT_CONFIG_NOSYSTEM](#3015-git_config_nosystem)
* [30.16 GIT_CONFIG_GLOBAL](#3016-git_config_global)
* [30.17 GIT_CONFIG_SYSTEM](#3017-git_config_system)
* [30.18 GIT_CONFIG_COUNT / KEY / VALUE](#3018-git_config_count--key--value)
* [30.19 Automation-Safe Git Commands](#3019-automation-safe-git-commands)
* [30.20 Non-Interactive Commands](#3020-non-interactive-commands)
* [30.21 Git Exit Codes](#3021-git-exit-codes)
* [30.22 Check Whether a Directory Is a Git Repository](#3022-check-whether-a-directory-is-a-git-repository)
* [30.23 Find the Repository Root](#3023-find-the-repository-root)
* [30.24 Obtain the Current Branch](#3024-obtain-the-current-branch)
* [30.25 Obtain the Current Commit](#3025-obtain-the-current-commit)
* [30.26 Check Whether the Working Tree Is Clean](#3026-check-whether-the-working-tree-is-clean)
* [30.27 Machine-Readable Status](#3027-machine-readable-status)
* [30.28 Machine-Readable Log](#3028-machine-readable-log)
* [30.29 Machine-Readable References](#3029-machine-readable-references)
* [30.30 Verify a Reference](#3030-verify-a-reference)
* [30.31 Check Whether a Commit Exists](#3031-check-whether-a-commit-exists)
* [30.32 Check Whether a File Is Tracked](#3032-check-whether-a-file-is-tracked)
* [30.33 Obtain Files Changed by a Commit](#3033-obtain-files-changed-by-a-commit)
* [30.34 Obtain Changed Files Between References](#3034-obtain-changed-files-between-references)
* [30.35 Detect Whether Two References Differ](#3035-detect-whether-two-references-differ)
* [30.36 Automation with git diff](#3036-automation-with-git-diff)
* [30.37 Automation with git merge-base](#3037-automation-with-git-merge-base)
* [30.38 Automation with git rev-list](#3038-automation-with-git-rev-list)
* [30.39 Automation with git for-each-ref](#3039-automation-with-git-for-each-ref)
* [30.40 Automation with git ls-files](#3040-automation-with-git-ls-files)
* [30.41 Automation with git ls-tree](#3041-automation-with-git-ls-tree)
* [30.42 Automation with git cat-file](#3042-automation-with-git-cat-file)
* [30.43 Git Input from Standard Input](#3043-git-input-from-standard-input)
* [30.44 Git Output to Standard Output](#3044-git-output-to-standard-output)
* [30.45 Git Output to Standard Error](#3045-git-output-to-standard-error)
* [30.46 Git Quiet Mode](#3046-git-quiet-mode)
* [30.47 Git Verbose Mode](#3047-git-verbose-mode)
* [30.48 Git Trace](#3048-git-trace)
* [30.49 Git Trace2](#3049-git-trace2)
* [30.50 Git Performance Diagnostics](#3050-git-performance-diagnostics)
* [30.51 Automation with Temporary Configuration](#3051-automation-with-temporary-configuration)
* [30.52 Automation with Temporary Environment](#3052-automation-with-temporary-environment)
* [30.53 Git in Docker](#3053-git-in-docker)
* [30.54 Git in CI/CD](#3054-git-in-cicd)
* [30.55 Automated Commit Creation](#3055-automated-commit-creation)
* [30.56 Automated Tag Creation](#3056-automated-tag-creation)
* [30.57 Automated Branch Operations](#3057-automated-branch-operations)
* [30.58 Automated Fetching](#3058-automated-fetching)
* [30.59 Automated Pulling](#3059-automated-pulling)
* [30.60 Automated Pushing](#3060-automated-pushing)
* [30.61 Automation with Worktrees](#3061-automation-with-worktrees)
* [30.62 Automation with Sparse Checkout](#3062-automation-with-sparse-checkout)
* [30.63 Automation with Archives](#3063-automation-with-archives)
* [30.64 Automation with Bundles](#3064-automation-with-bundles)
* [30.65 Automation Error Handling](#3065-automation-error-handling)
* [30.66 Shell Scripting Patterns](#3066-shell-scripting-patterns)
* [30.67 Safe Automation Principles](#3067-safe-automation-principles)
* [30.68 Complete Environment & Automation Command Reference](#3068-complete-environment--automation-command-reference)
* [30.69 High-Value Commands to Memorize](#3069-high-value-commands-to-memorize)

---

# 30.1 Git Environment Variables

Git recognizes many environment variables that can modify its behavior.

Common examples include:

```text
GIT_AUTHOR_NAME
GIT_AUTHOR_EMAIL
GIT_COMMITTER_NAME
GIT_COMMITTER_EMAIL
GIT_DIR
GIT_WORK_TREE
GIT_INDEX_FILE
GIT_CONFIG_GLOBAL
GIT_CONFIG_SYSTEM
GIT_CONFIG_NOSYSTEM
GIT_TERMINAL_PROMPT
GIT_SSH_COMMAND
GIT_ASKPASS
GIT_CEILING_DIRECTORIES
```

Inspect an environment variable:

```bash
echo "$GIT_DIR"
```

Set one for a single command:

```bash
GIT_TERMINAL_PROMPT=0 git fetch
```

The variable applies only to that command.

---

# 30.2 Inspect the Git Version

Check Git version:

```bash
git --version
```

Example output:

```text
git version 2.51.0
```

This is important in automation because Git behavior and available commands can vary between versions.

A script can verify the installed version before executing version-specific commands.

For example:

```bash
git --version
```

Branch state:

```text
Before: unchanged
After:  unchanged
```

---

# 30.3 Identify the Git Executable

Use:

```bash
command -v git
```

Example:

```text
/usr/bin/git
```

You can also use:

```bash
which git
```

but `command -v` is generally preferable in POSIX shell scripts.

Check Git's executable directory:

```bash
git --exec-path
```

Example:

```text
/usr/lib/git-core
```

---

# 30.4 Inspect Git Configuration

Show effective configuration:

```bash
git config --list
```

Show origin of each setting:

```bash
git config --list --show-origin
```

Show scope:

```bash
git config --list --show-scope
```

Both:

```bash
git config --list --show-origin --show-scope
```

This is extremely useful when debugging automation because a setting may come from:

```text
system
global
local
worktree
command
environment
```

---

# 30.5 Configuration Precedence

Git configuration can come from multiple levels.

A simplified model is:

```text
system
   |
global
   |
local
   |
worktree
   |
command/environment overrides
```

Inspect configuration with scope:

```bash
git config --list --show-scope
```

Example:

```text
global user.name=Developer
local  remote.origin.url=...
local  core.repositoryformatversion=0
```

Repository-local settings normally override global settings for that repository.

---

# 30.6 Override Configuration Temporarily

Use `-c`:

```bash
git -c core.pager=cat log
```

Another example:

```bash
git -c advice.detachedHead=false switch --detach HEAD~1
```

The configuration applies only to that invocation.

Example:

```bash
git -c user.name="CI Bot" -c user.email="ci@example.com" commit -m "Automated update"
```

This avoids permanently changing the user's Git configuration.

---

# 30.7 Set Repository-Local Configuration

Set configuration for the current repository:

```bash
git config --local user.name "Developer"
```

Set email:

```bash
git config --local user.email "developer@example.com"
```

Inspect:

```bash
git config --local --list
```

These settings affect only the current repository.

---

# 30.8 Set Global Configuration

Set global identity:

```bash
git config --global user.name "Developer"
git config --global user.email "developer@example.com"
```

Inspect:

```bash
git config --global --list
```

Automation should generally avoid modifying a user's global configuration unless that is explicitly the purpose of the script.

Prefer temporary configuration for CI:

```bash
git -c user.name="CI Bot" \
    -c user.email="ci@example.com" \
    commit -m "Automated update"
```

---

# 30.9 Git Environment Variables for Identity

Git recognizes:

```text
GIT_AUTHOR_NAME
GIT_AUTHOR_EMAIL
GIT_AUTHOR_DATE
GIT_COMMITTER_NAME
GIT_COMMITTER_EMAIL
GIT_COMMITTER_DATE
```

Example:

```bash
GIT_AUTHOR_NAME="CI Bot" \
GIT_AUTHOR_EMAIL="ci@example.com" \
GIT_COMMITTER_NAME="CI Bot" \
GIT_COMMITTER_EMAIL="ci@example.com" \
git commit --allow-empty -m "Automated commit"
```

The commit is created with the supplied author and committer identity.

This is useful for automation.

Use deterministic timestamps only when your build/release process specifically requires them.

---

# 30.10 Git Environment Variables for Repository Location

Git can locate repositories using:

```text
GIT_DIR
GIT_WORK_TREE
```

Example:

```bash
GIT_DIR=/path/to/repository/.git git status
```

This tells Git where the repository metadata is located.

Another example:

```bash
GIT_DIR=/path/to/repository/.git \
GIT_WORK_TREE=/path/to/repository \
git status
```

This explicitly connects the Git directory and working tree.

---

# 30.11 GIT_DIR

`GIT_DIR` specifies the location of the Git repository metadata.

Example:

```bash
GIT_DIR=/srv/project/.git git status
```

This is useful when:

* Working from scripts
* Running Git outside the repository directory
* Using bare repositories
* Managing multiple repository locations

For a bare repository:

```bash
GIT_DIR=/srv/git/project.git git branch
```

No normal working tree is required.

---

# 30.12 GIT_WORK_TREE

`GIT_WORK_TREE` specifies the working-tree directory.

Example:

```bash
GIT_DIR=/srv/project/.git \
GIT_WORK_TREE=/srv/project \
git status
```

This is useful when repository metadata and working files are stored separately.

For example:

```text
/srv/project/.git
/srv/project/
```

---

# 30.13 GIT_INDEX_FILE

Git normally uses:

```text
.git/index
```

You can specify an alternative index:

```bash
GIT_INDEX_FILE=/tmp/test-index git status
```

This is powerful for automation because scripts can manipulate an isolated index without changing the normal staging area.

For example:

```bash
GIT_INDEX_FILE=/tmp/test-index git add .
```

The normal `.git/index` remains unaffected.

This technique is useful for advanced automation and testing.

---

# 30.14 GIT_CEILING_DIRECTORIES

Git normally searches parent directories to find a repository.

You can limit this search with:

```bash
GIT_CEILING_DIRECTORIES=/home git status
```

This can improve performance or prevent Git from searching beyond known boundaries.

Multiple directories can be separated appropriately for the operating system.

This variable is particularly useful in shell environments and prompt integrations.

---

# 30.15 GIT_CONFIG_NOSYSTEM

Disable system-level Git configuration:

```bash
GIT_CONFIG_NOSYSTEM=1 git config --list --show-origin
```

This is useful for isolated environments where system configuration should not affect behavior.

For example, a test environment can use:

```bash
GIT_CONFIG_NOSYSTEM=1 \
git -c user.name="Test User" \
    -c user.email="test@example.com" \
    status
```

---

# 30.16 GIT_CONFIG_GLOBAL

Specify an alternative global configuration file:

```bash
GIT_CONFIG_GLOBAL=/tmp/gitconfig git config --global --list
```

This is useful for:

* Testing
* Containers
* CI/CD
* Isolated automation

Example:

```bash
printf '[user]\nname = CI Bot\nemail = ci@example.com\n' > /tmp/gitconfig
GIT_CONFIG_GLOBAL=/tmp/gitconfig git config --global --list
```

---

# 30.17 GIT_CONFIG_SYSTEM

Specify an alternative system configuration file:

```bash
GIT_CONFIG_SYSTEM=/tmp/git-system-config \
git config --system --list
```

This is useful for controlled test environments.

Most ordinary development workflows do not need to manipulate this variable.

---

# 30.18 GIT_CONFIG_COUNT / KEY / VALUE

Git supports injecting multiple configuration values through environment variables.

Example:

```bash
GIT_CONFIG_COUNT=2 \
GIT_CONFIG_KEY_0=user.name \
GIT_CONFIG_VALUE_0="CI Bot" \
GIT_CONFIG_KEY_1=user.email \
GIT_CONFIG_VALUE_1="ci@example.com" \
git config --get user.name
```

Output:

```text
CI Bot
```

This can be useful in environments where command-line arguments are inconvenient or when configuration must be supplied entirely through the environment.

---

# 30.19 Automation-Safe Git Commands

Prefer commands designed to provide deterministic output.

Examples:

```bash
git rev-parse HEAD
```

```bash
git symbolic-ref --short HEAD
```

```bash
git status --porcelain
```

```bash
git diff --name-only
```

```bash
git diff --quiet
```

```bash
git rev-parse --verify HEAD
```

```bash
git for-each-ref
```

These are generally better for scripts than parsing human-oriented output.

---

# 30.20 Non-Interactive Commands

CI/CD jobs should not unexpectedly wait for user input.

Disable terminal prompting:

```bash
GIT_TERMINAL_PROMPT=0 git fetch
```

This causes Git to fail instead of waiting for credentials through terminal prompts.

For example:

```bash
GIT_TERMINAL_PROMPT=0 git ls-remote origin
```

If authentication is unavailable, the command should fail rather than hang indefinitely.

This is particularly important in CI.

---

# 30.21 Git Exit Codes

Shell automation should check exit codes.

Example:

```bash
git diff --quiet
```

If there are no differences, the command exits successfully.

If differences exist, it exits with a non-zero status.

Example:

```bash
if git diff --quiet; then
    echo "No changes"
else
    echo "Changes detected"
fi
```

This is often preferable to parsing command output.

Use:

```bash
echo $?
```

to inspect the previous command's exit status.

---

# 30.22 Check Whether a Directory Is a Git Repository

Use:

```bash
git rev-parse --is-inside-work-tree
```

Example output:

```text
true
```

A script can use:

```bash
if git rev-parse --is-inside-work-tree >/dev/null 2>&1; then
    echo "Git repository"
fi
```

For bare repositories:

```bash
git rev-parse --is-bare-repository
```

Output:

```text
false
```

or:

```text
true
```

---

# 30.23 Find the Repository Root

Use:

```bash
git rev-parse --show-toplevel
```

Example:

```text
/home/developer/project
```

This is one of the most useful Git commands in shell automation.

Example:

```bash
ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"
```

Now the script operates relative to the repository root regardless of the directory from which it was invoked.

---

# 30.24 Obtain the Current Branch

Use:

```bash
git branch --show-current
```

Example:

```text
main
```

Another machine-friendly form:

```bash
git symbolic-ref --short HEAD
```

Detached HEAD produces no normal branch name.

Check detached state:

```bash
git symbolic-ref --short -q HEAD
```

---

# 30.25 Obtain the Current Commit

Use:

```bash
git rev-parse HEAD
```

Example:

```text
7f8a9c1234567890abcdef1234567890abcdef12
```

Short SHA:

```bash
git rev-parse --short HEAD
```

Example:

```text
7f8a9c1
```

This is preferable to parsing:

```bash
git log -1
```

in scripts.

---

# 30.26 Check Whether the Working Tree Is Clean

Use:

```bash
git diff --quiet
```

to test unstaged changes.

Check staged changes:

```bash
git diff --cached --quiet
```

Check all tracked changes:

```bash
git diff --quiet && git diff --cached --quiet
```

For untracked files, use:

```bash
git status --porcelain
```

A complete clean-tree test:

```bash
if [ -z "$(git status --porcelain)" ]; then
    echo "Clean"
else
    echo "Dirty"
fi
```

---

# 30.27 Machine-Readable Status

Use:

```bash
git status --porcelain
```

Example:

```text
 M src/main.c
A  src/new.c
?? build/
```

For scripts, prefer:

```bash
git status --porcelain=v1
```

or:

```bash
git status --porcelain=v2
```

Porcelain v2 provides a more structured format.

Example:

```bash
git status --porcelain=v2
```

Output may look like:

```text
1 .M N... 100644 100644 100644 abc123 abc123 file.txt
```

Do not rely on human-readable `git status` output in automation.

---

# 30.28 Machine-Readable Log

Use formatting options:

```bash
git log --format='%H'
```

Get commit IDs:

```bash
git log --format='%H' -10
```

Get commit and subject:

```bash
git log --format='%H%x09%s' -10
```

Example:

```text
abc123...	Implement login
def456...	Fix configuration
```

This is easier to consume programmatically than the default human-readable log.

---

# 30.29 Machine-Readable References

Use:

```bash
git for-each-ref
```

Example:

```bash
git for-each-ref --format='%(refname) %(objectname)'
```

Output:

```text
refs/heads/main abc123...
refs/heads/develop def456...
refs/tags/v1.0.0 789abc...
```

This is useful for scripts that need to inspect branches and tags.

---

# 30.30 Verify a Reference

Use:

```bash
git rev-parse --verify HEAD
```

Verify a branch:

```bash
git rev-parse --verify refs/heads/main
```

Verify a tag:

```bash
git rev-parse --verify refs/tags/v1.0.0
```

Suppress output and use only the exit status:

```bash
if git rev-parse --verify --quiet refs/heads/main >/dev/null; then
    echo "Branch exists"
fi
```

---

# 30.31 Check Whether a Commit Exists

Use:

```bash
git cat-file -e abc123^{commit}
```

Example:

```bash
if git cat-file -e "$COMMIT^{commit}" 2>/dev/null; then
    echo "Commit exists"
fi
```

This is useful in scripts where you need to validate a commit object.

---

# 30.32 Check Whether a File Is Tracked

Use:

```bash
git ls-files --error-unmatch path/to/file
```

Example:

```bash
git ls-files --error-unmatch README.md
```

Output:

```text
README.md
```

For a boolean test:

```bash
if git ls-files --error-unmatch README.md >/dev/null 2>&1; then
    echo "Tracked"
fi
```

---

# 30.33 Obtain Files Changed by a Commit

Use:

```bash
git diff-tree --no-commit-id --name-only -r HEAD
```

Or:

```bash
git show --format= --name-only HEAD
```

Machine-friendly output:

```bash
git diff-tree --no-commit-id --name-only -r HEAD
```

Example:

```text
src/main.c
README.md
tests/test_main.c
```

---

# 30.34 Obtain Changed Files Between References

Use:

```bash
git diff --name-only main..feature
```

Example:

```text
src/auth.c
src/login.c
tests/auth.test.c
```

For merge-base-aware comparison:

```bash
git diff --name-only main...feature
```

The three-dot form compares the feature branch against the merge base with `main`.

---

# 30.35 Detect Whether Two References Differ

Use:

```bash
git diff --quiet main..feature
```

Example:

```bash
if git diff --quiet main..feature; then
    echo "No differences"
else
    echo "Differences exist"
fi
```

For branch ancestry:

```bash
git merge-base --is-ancestor main feature
```

This answers a different question:

```text
Is main an ancestor of feature?
```

---

# 30.36 Automation with git diff

Useful options include:

```bash
git diff --quiet
git diff --name-only
git diff --name-status
git diff --stat
git diff --check
```

Check whitespace errors:

```bash
git diff --check
```

This is particularly useful in CI.

Example:

```bash
if ! git diff --check; then
    echo "Whitespace errors detected"
    exit 1
fi
```

---

# 30.37 Automation with git merge-base

Check ancestry:

```bash
git merge-base --is-ancestor main feature
```

Example:

```bash
if git merge-base --is-ancestor main feature; then
    echo "feature contains main"
fi
```

Find the common ancestor:

```bash
git merge-base main feature
```

Output:

```text
abc123...
```

This is useful for:

* Release scripts
* Branch validation
* CI logic
* Deployment checks
* Merge automation

---

# 30.38 Automation with git rev-list

List commits:

```bash
git rev-list HEAD
```

Count commits:

```bash
git rev-list --count HEAD
```

Commits unique to a branch:

```bash
git rev-list --count main..feature
```

Example:

```text
5
```

This can be used to determine whether a branch contains unmerged commits.

---

# 30.39 Automation with git for-each-ref

List branches:

```bash
git for-each-ref refs/heads/
```

Custom format:

```bash
git for-each-ref \
    --format='%(refname:short) %(objectname:short)' \
    refs/heads/
```

Example:

```text
main abc1234
develop def5678
feature/login 987abcd
```

List tags:

```bash
git for-each-ref \
    --format='%(refname:short) %(objectname:short)' \
    refs/tags/
```

---

# 30.40 Automation with git ls-files

List tracked files:

```bash
git ls-files
```

List only staged files:

```bash
git diff --cached --name-only
```

List ignored files:

```bash
git ls-files --others --ignored --exclude-standard
```

List untracked files:

```bash
git ls-files --others --exclude-standard
```

This is useful for build scripts and packaging.

---

# 30.41 Automation with git ls-tree

List files in a commit:

```bash
git ls-tree -r --name-only HEAD
```

Example:

```text
README.md
src/main.c
src/util.c
```

List a directory:

```bash
git ls-tree HEAD src/
```

This examines the Git tree without requiring the files to be present in the working tree.

---

# 30.42 Automation with git cat-file

Check object type:

```bash
git cat-file -t HEAD
```

Output:

```text
commit
```

Get object size:

```bash
git cat-file -s HEAD
```

Get object content:

```bash
git cat-file -p HEAD
```

Check existence:

```bash
git cat-file -e HEAD^{commit}
```

This is particularly useful for low-level automation.

---

# 30.43 Git Input from Standard Input

Many Git commands can consume input from standard input.

Example:

```bash
printf '%s\n' file1.txt file2.txt | git update-index --add --stdin
```

Another common pattern:

```bash
git diff --name-only -z | xargs -0 ...
```

Use null-delimited output where filenames may contain whitespace or special characters.

---

# 30.44 Git Output to Standard Output

Many commands can produce data directly to stdout:

```bash
git rev-parse HEAD
```

```bash
git branch --show-current
```

```bash
git diff --name-only
```

```bash
git ls-files
```

This makes them easy to compose:

```bash
COMMIT="$(git rev-parse HEAD)"
```

or:

```bash
ROOT="$(git rev-parse --show-toplevel)"
```

---

# 30.45 Git Output to Standard Error

Diagnostic and error messages are commonly sent to stderr.

Shell scripts can redirect them:

```bash
git status >/tmp/status.out 2>/tmp/status.err
```

Suppress output:

```bash
git status >/dev/null 2>&1
```

Use this carefully.

Suppressing all output can make CI failures difficult to diagnose.

A better approach is often:

```bash
if ! git fetch origin; then
    echo "git fetch failed" >&2
    exit 1
fi
```

---

# 30.46 Git Quiet Mode

Many commands support:

```bash
--quiet
```

Examples:

```bash
git fetch --quiet
```

```bash
git push --quiet
```

```bash
git diff --quiet
```

Quiet mode is useful when the script only cares about success or failure.

---

# 30.47 Git Verbose Mode

Some Git commands support:

```bash
--verbose
```

Example:

```bash
git fetch --verbose
```

This can be useful during troubleshooting.

For transport debugging, Git tracing is more powerful.

---

# 30.48 Git Trace

Enable basic tracing:

```bash
GIT_TRACE=1 git status
```

For example:

```bash
GIT_TRACE=1 git fetch origin
```

This can show Git's internal command execution.

Trace output is generally intended for diagnostics, not stable machine parsing.

Do not enable verbose tracing indiscriminately in CI logs because authentication-related or environment-sensitive information may be exposed depending on the trace mode and Git operation.

---

# 30.49 Git Trace2

Git provides Trace2 instrumentation.

Example:

```bash
GIT_TRACE2=1 git status
```

Write Trace2 output to a file:

```bash
GIT_TRACE2=/tmp/git-trace.log git status
```

Trace2 is useful for:

* Performance diagnostics
* Git tooling
* Enterprise Git infrastructure
* Debugging slow operations

Trace output should be handled carefully in CI environments.

---

# 30.50 Git Performance Diagnostics

For performance analysis:

```bash
GIT_TRACE2_PERF=1 git status
```

For example:

```bash
GIT_TRACE2_PERF=/tmp/git-perf.log git fetch origin
```

This can help identify:

* Slow filesystem operations
* Expensive Git commands
* Slow repository discovery
* Expensive object operations
* Remote transport delays

---

# 30.51 Automation with Temporary Configuration

Use:

```bash
git -c key=value command
```

Example:

```bash
git -c core.pager=cat log --oneline
```

CI identity:

```bash
git \
  -c user.name="CI Bot" \
  -c user.email="ci@example.com" \
  commit -m "Automated update"
```

This is usually preferable to changing global configuration.

---

# 30.52 Automation with Temporary Environment

Example:

```bash
GIT_TERMINAL_PROMPT=0 git fetch origin
```

Multiple variables:

```bash
GIT_TERMINAL_PROMPT=0 \
GIT_SSH_COMMAND="ssh -o BatchMode=yes" \
git fetch origin
```

This makes the operation explicitly non-interactive.

---

# 30.53 Git in Docker

A typical Docker build may run:

```dockerfile
RUN git clone --depth 1 https://example.com/project.git /opt/project
```

For a build that only needs source files:

```dockerfile
RUN git clone --depth 1 --sparse https://example.com/project.git /opt/project
```

Then:

```dockerfile
WORKDIR /opt/project
RUN git sparse-checkout set --cone backend
```

In containerized environments, avoid storing long-lived credentials directly in image layers.

Use the CI platform's secret-management mechanism or BuildKit-supported secret/SSH mechanisms where appropriate.

---

# 30.54 Git in CI/CD

A typical CI workflow might be:

```bash
set -e

git fetch --tags --prune
git checkout "$CI_COMMIT_SHA"

git status --short
git rev-parse HEAD
```

A more defensive approach:

```bash
set -euo pipefail

git fetch --tags --prune
git rev-parse --verify "$CI_COMMIT_SHA^{commit}"
git checkout --detach "$CI_COMMIT_SHA"
```

This makes the exact build commit explicit.

---

# 30.55 Automated Commit Creation

Example:

```bash
git add generated/
git \
  -c user.name="CI Bot" \
  -c user.email="ci@example.com" \
  commit -m "Update generated files"
```

Prevent empty commits:

```bash
if git diff --cached --quiet; then
    echo "Nothing to commit"
else
    git commit -m "Update generated files"
fi
```

---

# 30.56 Automated Tag Creation

Create annotated tag:

```bash
git tag -a v1.2.0 -m "Release v1.2.0"
```

Verify:

```bash
git rev-parse v1.2.0
```

Push:

```bash
git push origin v1.2.0
```

Automation should verify that the tag does not already exist if duplicate tags are not expected:

```bash
if git rev-parse --verify --quiet "refs/tags/v1.2.0" >/dev/null; then
    echo "Tag already exists" >&2
    exit 1
fi
```

---

# 30.57 Automated Branch Operations

Create branch:

```bash
git switch -c release/1.2
```

Automation-friendly branch creation:

```bash
git switch -c "$BRANCH"
```

Check branch existence:

```bash
git show-ref --verify --quiet "refs/heads/$BRANCH"
```

Delete local branch:

```bash
git branch -d "$BRANCH"
```

Use `-D` only when forced deletion is intentionally required.

---

# 30.58 Automated Fetching

A common CI command:

```bash
git fetch --prune origin
```

Fetch tags:

```bash
git fetch --tags --prune origin
```

Fetch a specific branch:

```bash
git fetch origin main
```

For deterministic CI, fetch the exact references required by the job instead of relying on whatever happens to exist locally.

---

# 30.59 Automated Pulling

For automation, blindly using:

```bash
git pull
```

is often undesirable because it combines fetching and integration.

Prefer explicit operations:

```bash
git fetch origin
git merge --ff-only origin/main
```

or:

```bash
git fetch origin
git rebase origin/main
```

depending on the workflow.

For a deployment checkout where local changes are not expected:

```bash
git fetch origin
git reset --hard origin/main
```

The last command is destructive to local changes and should be used only when that behavior is intentional.

---

# 30.60 Automated Pushing

Push explicitly:

```bash
git push origin main
```

Set upstream when needed:

```bash
git push -u origin feature/login
```

Push a tag:

```bash
git push origin v1.2.0
```

For automation, avoid ambiguous:

```bash
git push
```

unless the repository configuration is deliberately controlled.

---

# 30.61 Automation with Worktrees

A CI or build system can create isolated worktrees:

```bash
git worktree add --detach /tmp/build "$COMMIT"
```

Run build:

```bash
cd /tmp/build
./build.sh
```

Remove:

```bash
git worktree remove /tmp/build
```

This can be useful when multiple builds need different repository states simultaneously.

---

# 30.62 Automation with Sparse Checkout

For a monorepo:

```bash
git clone --sparse "$REPOSITORY" project
cd project
git sparse-checkout set --cone services/payment
```

This allows automation to populate only the required part of the repository.

For example:

```bash
git sparse-checkout add --cone infrastructure
```

The branch state remains independent of sparse working-tree selection.

---

# 30.63 Automation with Archives

Generate a release artifact:

```bash
git archive \
  --format=tar.gz \
  --prefix="project-${VERSION}/" \
  --output="project-${VERSION}.tar.gz" \
  "$COMMIT"
```

Verify:

```bash
tar -tzf "project-${VERSION}.tar.gz" >/dev/null
```

Calculate checksum:

```bash
sha256sum "project-${VERSION}.tar.gz"
```

This is a common release automation pattern.

---

# 30.64 Automation with Bundles

Create a Git bundle:

```bash
git bundle create repository.bundle --all
```

Verify:

```bash
git bundle verify repository.bundle
```

List references:

```bash
git bundle list-heads repository.bundle
```

A bundle preserves Git objects and can therefore be used to transfer repository history.

This differs from `git archive`, which packages files.

---

# 30.65 Automation Error Handling

A robust shell script commonly starts with:

```bash
set -euo pipefail
```

Then:

```bash
git fetch origin
git rev-parse --verify HEAD
```

If a Git command fails, the script terminates unless the failure is explicitly handled.

For expected non-zero results:

```bash
if git diff --quiet; then
    echo "Clean"
else
    echo "Changes exist"
fi
```

Do not blindly use:

```bash
set -e
```

as a substitute for understanding command exit codes.

Some Git commands intentionally use non-zero exit statuses to communicate meaningful conditions.

---

# 30.66 Shell Scripting Patterns

## Repository root

```bash
ROOT="$(git rev-parse --show-toplevel)"
```

## Current commit

```bash
COMMIT="$(git rev-parse HEAD)"
```

## Current branch

```bash
BRANCH="$(git branch --show-current)"
```

## Check clean tree

```bash
if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is dirty"
    exit 1
fi
```

## Verify branch

```bash
if [ "$BRANCH" != "main" ]; then
    echo "Must run on main" >&2
    exit 1
fi
```

## Verify commit

```bash
git rev-parse --verify "$COMMIT^{commit}" >/dev/null
```

---

# 30.67 Safe Automation Principles

## 1. Prefer explicit references

Prefer:

```bash
git checkout "$COMMIT"
```

over relying on whatever branch happens to be checked out.

## 2. Prefer machine-readable output

Use:

```bash
git status --porcelain=v2
```

instead of parsing ordinary `git status`.

## 3. Avoid interactive authentication

Use:

```bash
GIT_TERMINAL_PROMPT=0
```

when appropriate.

## 4. Check exit codes

Use:

```bash
if git command; then
    ...
fi
```

when the exit status has semantic meaning.

## 5. Avoid destructive commands unless intentional

Be especially careful with:

```bash
git reset --hard
git clean -fd
git push --force
git branch -D
```

## 6. Use temporary configuration

Prefer:

```bash
git -c user.name="CI Bot" ...
```

over changing global configuration.

## 7. Quote shell variables

Prefer:

```bash
git checkout "$COMMIT"
```

instead of:

```bash
git checkout $COMMIT
```

## 8. Use null-delimited output for filenames

Prefer:

```bash
git diff --name-only -z
```

when filenames may contain whitespace or special characters.

## 9. Pin exact commits when reproducibility matters

For builds:

```bash
git checkout --detach "$COMMIT"
```

is often more deterministic than relying on a moving branch.

## 10. Keep CI credentials outside source code

Do not embed credentials in:

```text
.git/config
Dockerfiles
shell scripts
repository URLs
```

unless the environment is explicitly designed for it.

---

# 30.68 Complete Environment & Automation Command Reference

| Command                                            | Description                          | Example                                                   | Branch State Before and After command          | Output                   |
| -------------------------------------------------- | ------------------------------------ | --------------------------------------------------------- | ---------------------------------------------- | ------------------------ |
| `git --version`                                    | Show Git version                     | `git --version`                                           | Branch unchanged                               | Git version              |
| `git --exec-path`                                  | Show Git executable path             | `git --exec-path`                                         | Branch unchanged                               | Git executable directory |
| `command -v git`                                   | Locate Git executable                | `command -v git`                                          | Branch unchanged                               | Executable path          |
| `git config --list`                                | Show effective configuration         | `git config --list`                                       | Branch unchanged                               | Configuration            |
| `git config --list --show-origin`                  | Show configuration source            | `git config --list --show-origin`                         | Branch unchanged                               | Configuration + source   |
| `git config --list --show-scope`                   | Show configuration scope             | `git config --list --show-scope`                          | Branch unchanged                               | Configuration + scope    |
| `git config --local --list`                        | Show repository configuration        | `git config --local --list`                               | Branch unchanged                               | Local configuration      |
| `git config --global --list`                       | Show global configuration            | `git config --global --list`                              | Branch unchanged                               | Global configuration     |
| `git config --local key value`                     | Set repository configuration         | `git config --local user.name "Developer"`                | Branch unchanged                               | Usually no output        |
| `git config --global key value`                    | Set global configuration             | `git config --global user.name "Developer"`               | Branch unchanged                               | Usually no output        |
| `git -c key=value command`                         | Temporarily override configuration   | `git -c core.pager=cat log`                               | Depends on command                             | Command output           |
| `git rev-parse --is-inside-work-tree`              | Check work-tree context              | `git rev-parse --is-inside-work-tree`                     | Branch unchanged                               | `true`/`false`           |
| `git rev-parse --is-bare-repository`               | Check whether repository is bare     | `git rev-parse --is-bare-repository`                      | Branch unchanged                               | `true`/`false`           |
| `git rev-parse --show-toplevel`                    | Find repository root                 | `git rev-parse --show-toplevel`                           | Branch unchanged                               | Absolute path            |
| `git rev-parse HEAD`                               | Get current commit                   | `git rev-parse HEAD`                                      | Branch unchanged                               | Full SHA                 |
| `git rev-parse --short HEAD`                       | Get short commit ID                  | `git rev-parse --short HEAD`                              | Branch unchanged                               | Short SHA                |
| `git branch --show-current`                        | Get current branch                   | `git branch --show-current`                               | Branch unchanged                               | Branch name              |
| `git symbolic-ref --short HEAD`                    | Get symbolic branch reference        | `git symbolic-ref --short HEAD`                           | Branch unchanged                               | Branch name              |
| `git status --porcelain`                           | Machine-readable status              | `git status --porcelain`                                  | Branch unchanged                               | Status records           |
| `git status --porcelain=v2`                        | Structured machine-readable status   | `git status --porcelain=v2`                               | Branch unchanged                               | Porcelain v2 records     |
| `git diff --quiet`                                 | Test for unstaged differences        | `git diff --quiet`                                        | Branch unchanged                               | Usually no output        |
| `git diff --cached --quiet`                        | Test for staged differences          | `git diff --cached --quiet`                               | Branch unchanged                               | Usually no output        |
| `git diff --check`                                 | Check whitespace errors              | `git diff --check`                                        | Branch unchanged                               | Error information        |
| `git diff --name-only`                             | List changed files                   | `git diff --name-only`                                    | Branch unchanged                               | File names               |
| `git diff --name-only -z`                          | Null-delimited changed files         | `git diff --name-only -z`                                 | Branch unchanged                               | NUL-delimited paths      |
| `git log --format='%H'`                            | Machine-readable commit IDs          | `git log --format='%H' -10`                               | Branch unchanged                               | Commit IDs               |
| `git for-each-ref`                                 | Enumerate refs                       | `git for-each-ref`                                        | Branch unchanged                               | References               |
| `git for-each-ref --format=...`                    | Custom ref output                    | `git for-each-ref --format='%(refname:short)'`            | Branch unchanged                               | Formatted refs           |
| `git rev-parse --verify REF`                       | Verify reference                     | `git rev-parse --verify HEAD`                             | Branch unchanged                               | Object ID                |
| `git cat-file -e OBJECT`                           | Check object existence               | `git cat-file -e HEAD^{commit}`                           | Branch unchanged                               | Usually no output        |
| `git cat-file -t OBJECT`                           | Show object type                     | `git cat-file -t HEAD`                                    | Branch unchanged                               | Object type              |
| `git cat-file -p OBJECT`                           | Pretty-print object                  | `git cat-file -p HEAD`                                    | Branch unchanged                               | Object contents          |
| `git ls-files`                                     | List tracked files                   | `git ls-files`                                            | Branch unchanged                               | File paths               |
| `git ls-files --others --exclude-standard`         | List untracked files                 | `git ls-files --others --exclude-standard`                | Branch unchanged                               | File paths               |
| `git ls-files --error-unmatch FILE`                | Check whether file is tracked        | `git ls-files --error-unmatch README.md`                  | Branch unchanged                               | File path                |
| `git ls-tree -r --name-only HEAD`                  | List files in a tree                 | `git ls-tree -r --name-only HEAD`                         | Branch unchanged                               | File paths               |
| `git diff-tree --no-commit-id --name-only -r HEAD` | List files changed by commit         | `git diff-tree --no-commit-id --name-only -r HEAD`        | Branch unchanged                               | File paths               |
| `git merge-base REF1 REF2`                         | Find common ancestor                 | `git merge-base main feature`                             | Branch unchanged                               | Commit ID                |
| `git merge-base --is-ancestor A B`                 | Test ancestry                        | `git merge-base --is-ancestor main feature`               | Branch unchanged                               | Usually no output        |
| `git rev-list HEAD`                                | List reachable commits               | `git rev-list HEAD`                                       | Branch unchanged                               | Commit IDs               |
| `git rev-list --count REF`                         | Count commits                        | `git rev-list --count HEAD`                               | Branch unchanged                               | Integer                  |
| `git fetch --prune`                                | Fetch and remove stale remote refs   | `git fetch --prune origin`                                | Branch unchanged; remote refs may update       | Fetch output             |
| `git fetch --tags`                                 | Fetch tags                           | `git fetch --tags origin`                                 | Branch unchanged; refs may update              | Fetch output             |
| `git switch --detach COMMIT`                       | Checkout exact commit without branch | `git switch --detach "$COMMIT"`                           | Branch -> detached HEAD                        | Switch output            |
| `git checkout --detach COMMIT`                     | Older equivalent                     | `git checkout --detach "$COMMIT"`                         | Branch -> detached HEAD                        | Switch output            |
| `git add PATH`                                     | Stage files                          | `git add generated/`                                      | Branch unchanged                               | Usually no output        |
| `git commit -m MESSAGE`                            | Create commit                        | `git commit -m "Update generated files"`                  | Branch advances                                | Commit summary           |
| `git tag -a TAG -m MESSAGE`                        | Create annotated tag                 | `git tag -a v1.2.0 -m "Release v1.2.0"`                   | Branch unchanged                               | Usually no output        |
| `git push REMOTE BRANCH`                           | Push branch                          | `git push origin main`                                    | Local branch unchanged; remote ref may advance | Push output              |
| `git push REMOTE TAG`                              | Push tag                             | `git push origin v1.2.0`                                  | Branch unchanged; remote tag created           | Push output              |
| `git archive`                                      | Create source archive                | `git archive --format=tar.gz --output=source.tar.gz HEAD` | Branch unchanged                               | Archive file             |
| `git bundle create FILE --all`                     | Create Git bundle                    | `git bundle create repository.bundle --all`               | Branch unchanged                               | Bundle file              |
| `git bundle verify FILE`                           | Verify Git bundle                    | `git bundle verify repository.bundle`                     | Branch unchanged                               | Verification             |
| `git worktree add PATH REF`                        | Create automation worktree           | `git worktree add --detach /tmp/build HEAD`               | Current branch unchanged                       | Worktree information     |
| `git worktree remove PATH`                         | Remove worktree                      | `git worktree remove /tmp/build`                          | Current branch unchanged                       | Usually no output        |

---

# 30.69 High-Value Commands to Memorize

## Git version

```bash
git --version
```

## Repository root

```bash
git rev-parse --show-toplevel
```

## Current branch

```bash
git branch --show-current
```

## Current commit

```bash
git rev-parse HEAD
```

## Short commit

```bash
git rev-parse --short HEAD
```

## Clean working tree

```bash
git status --porcelain
```

## Machine-readable status

```bash
git status --porcelain=v2
```

## Check whether working tree has unstaged changes

```bash
git diff --quiet
```

## Check whether staging area has changes

```bash
git diff --cached --quiet
```

## Check whitespace errors

```bash
git diff --check
```

## Verify reference

```bash
git rev-parse --verify HEAD
```

## Check commit existence

```bash
git cat-file -e "$COMMIT^{commit}"
```

## List tracked files

```bash
git ls-files
```

## List changed files

```bash
git diff --name-only
```

## List files in a commit

```bash
git ls-tree -r --name-only HEAD
```

## List branches programmatically

```bash
git for-each-ref \
    --format='%(refname:short) %(objectname:short)' \
    refs/heads/
```

## Count commits

```bash
git rev-list --count HEAD
```

## Check branch ancestry

```bash
git merge-base --is-ancestor main feature
```

## Find repository root from any subdirectory

```bash
ROOT="$(git rev-parse --show-toplevel)"
```

## Disable terminal prompting

```bash
GIT_TERMINAL_PROMPT=0 git fetch
```

## Temporary configuration

```bash
git -c key=value command
```

## CI identity

```bash
git \
  -c user.name="CI Bot" \
  -c user.email="ci@example.com" \
  commit -m "Automated update"
```

## Exact CI checkout

```bash
git fetch origin
git checkout --detach "$CI_COMMIT_SHA"
```

## Sparse CI checkout

```bash
git clone --sparse "$REPOSITORY" project
cd project
git sparse-checkout set --cone path/to/project
```

## Generate release archive

```bash
git archive \
  --format=tar.gz \
  --prefix="project-${VERSION}/" \
  --output="project-${VERSION}.tar.gz" \
  "$COMMIT"
```

## Create isolated build worktree

```bash
git worktree add --detach /tmp/build "$COMMIT"
```

---

# Recommended Automation Template

A practical Linux shell script can begin with:

```bash
#!/usr/bin/env bash

set -euo pipefail

ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"

COMMIT="$(git rev-parse HEAD)"
BRANCH="$(git branch --show-current)"

echo "Repository: $ROOT"
echo "Branch: ${BRANCH:-DETACHED}"
echo "Commit: $COMMIT"

if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is dirty" >&2
    exit 1
fi

git diff --check
```

This provides:

* Strict shell behavior
* Repository-root detection
* Exact commit identification
* Branch detection
* Clean-working-tree validation
* Whitespace validation

---

# CI/CD Deterministic Checkout Pattern

For a build that must use an exact commit:

```bash
#!/usr/bin/env bash

set -euo pipefail

git fetch --prune origin

git rev-parse --verify "$CI_COMMIT_SHA^{commit}"

git checkout --detach "$CI_COMMIT_SHA"

git status --porcelain=v2

git rev-parse HEAD
```

The resulting state is:

```text
HEAD
 |
 v
Exact CI commit
```

rather than:

```text
HEAD
 |
 v
Moving branch
```

This makes builds easier to reproduce.

---

# Automation Architecture

A useful mental model is:

```text
                 Git Repository
                       |
          +------------+------------+
          |            |            |
       Refs         Objects      Working Tree
          |            |            |
          +------------+------------+
                       |
                  Automation
                       |
       +---------------+---------------+
       |               |               |
    Validate         Build          Release
       |               |               |
   rev-parse        worktree       archive
   status           checkout       bundle
   diff             sparse         tag
   merge-base       checkout       push
```

Git's command-line interface is particularly powerful for automation because many operations can be expressed through:

```text
exit status
+
machine-readable output
+
explicit references
+
temporary configuration
+
environment variables
```

---

# Final Summary

For Git automation, prioritize these concepts:

```text
1. Explicit references
2. Machine-readable output
3. Correct exit-code handling
4. Non-interactive authentication
5. Temporary configuration
6. Deterministic checkouts
7. Isolated worktrees
8. Sparse checkout where appropriate
9. Clean release archives
10. Careful error handling
```

The most important commands are:

```bash
git rev-parse --show-toplevel
git branch --show-current
git rev-parse HEAD
git status --porcelain=v2
git diff --quiet
git diff --check
git rev-parse --verify REF
git merge-base --is-ancestor A B
git for-each-ref
git ls-files
git ls-tree
git rev-list
git cat-file
git -c key=value command
GIT_TERMINAL_PROMPT=0 git command
```

These commands form the foundation for reliable Git-based shell automation, CI/CD pipelines, build systems, release systems, and DevOps tooling.

---

# Next Part

**Next file:** `31-common-devops-ci-commands.md`

[Next: Common DevOps / CI Commands](31-common-devops-ci-commands.md)
