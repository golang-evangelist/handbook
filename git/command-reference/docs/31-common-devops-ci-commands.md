# 31. Common DevOps / CI Commands

This chapter covers Git commands and command patterns commonly used in:

* CI/CD pipelines
* Build automation
* Release automation
* Deployment pipelines
* DevOps tooling
* Continuous integration
* Continuous delivery
* Continuous deployment
* Docker builds
* Infrastructure repositories
* Automated testing
* Git-based release workflows
* Monorepos
* Automated versioning
* Repository validation

The examples assume a Linux shell and use machine-readable Git output wherever practical.

---

# Table of Contents

* [31.1 CI/CD Git Principles](#311-cicd-git-principles)
* [31.2 Identify Git Version](#312-identify-git-version)
* [31.3 Identify Repository Root](#313-identify-repository-root)
* [31.4 Validate Git Repository](#314-validate-git-repository)
* [31.5 Detect Bare Repository](#315-detect-bare-repository)
* [31.6 Get Current Branch](#316-get-current-branch)
* [31.7 Get Current Commit](#317-get-current-commit)
* [31.8 Get Short Commit SHA](#318-get-short-commit-sha)
* [31.9 Get Commit Subject](#319-get-commit-subject)
* [31.10 Get Commit Author](#3110-get-commit-author)
* [31.11 Get Commit Date](#3111-get-commit-date)
* [31.12 Validate a Commit](#3112-validate-a-commit)
* [31.13 Validate a Branch](#3113-validate-a-branch)
* [31.14 Validate a Tag](#3114-validate-a-tag)
* [31.15 Check Working Tree](#3115-check-working-tree)
* [31.16 Check Staged Changes](#3116-check-staged-changes)
* [31.17 Check Untracked Files](#3117-check-untracked-files)
* [31.18 Machine-Readable Status](#3118-machine-readable-status)
* [31.19 Check Whitespace Errors](#3119-check-whitespace-errors)
* [31.20 List Changed Files](#3120-list-changed-files)
* [31.21 Detect Changes Between Commits](#3121-detect-changes-between-commits)
* [31.22 Detect Changes Between Branches](#3122-detect-changes-between-branches)
* [31.23 Count Commits](#3123-count-commits)
* [31.24 Check Branch Ancestry](#3124-check-branch-ancestry)
* [31.25 Find Merge Base](#3125-find-merge-base)
* [31.26 Fetch Repository](#3126-fetch-repository)
* [31.27 Fetch with Pruning](#3127-fetch-with-pruning)
* [31.28 Fetch Tags](#3128-fetch-tags)
* [31.29 Fetch a Specific Branch](#3129-fetch-a-specific-branch)
* [31.30 Fetch a Specific Commit](#3130-fetch-a-specific-commit)
* [31.31 Clone for CI](#3131-clone-for-ci)
* [31.32 Shallow Clone](#3132-shallow-clone)
* [31.33 Clone a Specific Branch](#3133-clone-a-specific-branch)
* [31.34 Clone Without Checkout](#3134-clone-without-checkout)
* [31.35 Partial Clone](#3135-partial-clone)
* [31.36 Sparse Clone](#3136-sparse-clone)
* [31.37 Sparse Checkout](#3137-sparse-checkout)
* [31.38 Checkout Exact Commit](#3138-checkout-exact-commit)
* [31.39 Detached HEAD for CI](#3139-detached-head-for-ci)
* [31.40 Fast-Forward Validation](#3140-fast-forward-validation)
* [31.41 Fast-Forward Merge](#3141-fast-forward-merge)
* [31.42 Fetch and Merge Explicitly](#3142-fetch-and-merge-explicitly)
* [31.43 Fetch and Rebase Explicitly](#3143-fetch-and-rebase-explicitly)
* [31.44 Avoid Interactive Git](#3144-avoid-interactive-git)
* [31.45 Disable Terminal Prompts](#3145-disable-terminal-prompts)
* [31.46 Configure CI Identity](#3146-configure-ci-identity)
* [31.47 Automated Commit](#3147-automated-commit)
* [31.48 Detect Changes Before Commit](#3148-detect-changes-before-commit)
* [31.49 Automated Tag](#3149-automated-tag)
* [31.50 Validate Version Tag](#3150-validate-version-tag)
* [31.51 Push Branch](#3151-push-branch)
* [31.52 Push Tag](#3152-push-tag)
* [31.53 Push All Tags](#3153-push-all-tags)
* [31.54 Push with Explicit Refspec](#3154-push-with-explicit-refspec)
* [31.55 Verify Remote](#3155-verify-remote)
* [31.56 Inspect Remote URLs](#3156-inspect-remote-urls)
* [31.57 Test Remote Access](#3157-test-remote-access)
* [31.58 List Remote References](#3158-list-remote-references)
* [31.59 Check Remote Branch](#3159-check-remote-branch)
* [31.60 Delete Remote Branch](#3160-delete-remote-branch)
* [31.61 Delete Remote Tag](#3161-delete-remote-tag)
* [31.62 Prune Remote References](#3162-prune-remote-references)
* [31.63 CI Build Checkout](#3163-ci-build-checkout)
* [31.64 CI Release Checkout](#3164-ci-release-checkout)
* [31.65 CI Test Checkout](#3165-ci-test-checkout)
* [31.66 CI Diff Against Main](#3166-ci-diff-against-main)
* [31.67 Determine Changed Components](#3167-determine-changed-components)
* [31.68 Detect Documentation Changes](#3168-detect-documentation-changes)
* [31.69 Detect Source Changes](#3169-detect-source-changes)
* [31.70 Detect Infrastructure Changes](#3170-detect-infrastructure-changes)
* [31.71 Detect Changes in a Directory](#3171-detect-changes-in-a-directory)
* [31.72 Generate Changed File List](#3172-generate-changed-file-list)
* [31.73 Git-Based Versioning](#3173-git-based-versioning)
* [31.74 Describe Current Version](#3174-describe-current-version)
* [31.75 Find Latest Tag](#3175-find-latest-tag)
* [31.76 Generate Version from Tag](#3176-generate-version-from-tag)
* [31.77 Generate Build Identifier](#3177-generate-build-identifier)
* [31.78 Generate Release Archive](#3178-generate-release-archive)
* [31.79 Generate Source Archive](#3179-generate-source-archive)
* [31.80 Verify Release Archive](#3180-verify-release-archive)
* [31.81 Generate Checksums](#3181-generate-checksums)
* [31.82 Git Bundle for Backup](#3182-git-bundle-for-backup)
* [31.83 Git Worktree for Builds](#3183-git-worktree-for-builds)
* [31.84 Clean CI Worktree](#3184-clean-ci-worktree)
* [31.85 Git LFS in CI](#3185-git-lfs-in-ci)
* [31.86 Submodules in CI](#3186-submodules-in-ci)
* [31.87 Recursive Submodules](#3187-recursive-submodules)
* [31.88 Verify Submodules](#3188-verify-submodules)
* [31.89 Git Hooks in Automation](#3189-git-hooks-in-automation)
* [31.90 Disable Hooks for Controlled Automation](#3190-disable-hooks-for-controlled-automation)
* [31.91 Repository Maintenance in CI](#3191-repository-maintenance-in-ci)
* [31.92 Garbage Collection](#3192-garbage-collection)
* [31.93 Repository Integrity Check](#3193-repository-integrity-check)
* [31.94 Repository Statistics](#3194-repository-statistics)
* [31.95 Object Count](#3195-object-count)
* [31.96 Git Trace in CI](#3196-git-trace-in-ci)
* [31.97 Git Trace2 in CI](#3197-git-trace2-in-ci)
* [31.98 CI Authentication](#3198-ci-authentication)
* [31.99 Secure Credential Handling](#3199-secure-credential-handling)
* [31.100 CI Failure Handling](#31100-ci-failure-handling)
* [31.101 Reproducible CI Checkout](#31101-reproducible-ci-checkout)
* [31.102 CI Repository Validation Script](#31102-ci-repository-validation-script)
* [31.103 CI Build Script](#31103-ci-build-script)
* [31.104 CI Release Script](#31104-ci-release-script)
* [31.105 Common CI Git Command Reference](#31105-common-ci-git-command-reference)
* [31.106 High-Value DevOps Git Commands](#31106-high-value-devops-git-commands)

---

# 31.1 CI/CD Git Principles

A reliable Git-based CI/CD pipeline should generally:

1. Identify the exact commit.
2. Fetch only what is required.
3. Avoid interactive commands.
4. Avoid relying on human-readable output.
5. Check exit codes.
6. Validate the repository state.
7. Build from an immutable commit.
8. Generate deterministic artifacts where practical.
9. Keep credentials outside source code.
10. Make destructive operations explicit.

A typical pipeline flow is:

```text
Clone / Fetch
     |
     v
Resolve Commit
     |
     v
Validate Repository
     |
     v
Checkout Exact Commit
     |
     v
Run Tests
     |
     v
Build Artifact
     |
     v
Create Release
     |
     v
Publish Artifact
```

---

# 31.2 Identify Git Version

```bash
git --version
```

Example:

```text
git version 2.51.0
```

Branch state:

```text
Before: unchanged
After:  unchanged
```

CI systems should know which Git version is being used, particularly when the pipeline depends on newer Git features.

---

# 31.3 Identify Repository Root

```bash
git rev-parse --show-toplevel
```

Example:

```text
/home/ci/project
```

Recommended:

```bash
ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"
```

This prevents scripts from depending on the directory from which the CI runner invoked them.

---

# 31.4 Validate Git Repository

```bash
git rev-parse --is-inside-work-tree
```

Example:

```text
true
```

CI test:

```bash
if ! git rev-parse --is-inside-work-tree >/dev/null 2>&1; then
    echo "Not a Git repository" >&2
    exit 1
fi
```

Branch state:

```text
Before: unchanged
After:  unchanged
```

---

# 31.5 Detect Bare Repository

```bash
git rev-parse --is-bare-repository
```

Normal repository:

```text
false
```

Bare repository:

```text
true
```

This is useful for automation that must distinguish source checkouts from Git servers or bare repositories.

---

# 31.6 Get Current Branch

```bash
git branch --show-current
```

Example:

```text
main
```

For detached HEAD:

```text
<empty>
```

CI pipelines should not assume that `HEAD` is attached to a branch.

---

# 31.7 Get Current Commit

```bash
git rev-parse HEAD
```

Example:

```text
7f8a9c1234567890abcdef1234567890abcdef12
```

This is one of the most important values in CI.

Store it:

```bash
COMMIT="$(git rev-parse HEAD)"
```

---

# 31.8 Get Short Commit SHA

```bash
git rev-parse --short HEAD
```

Example:

```text
7f8a9c1
```

Useful for:

* Docker image tags
* Build identifiers
* Log messages
* Artifact names

Example:

```bash
BUILD_ID="$(git rev-parse --short HEAD)"
```

---

# 31.9 Get Commit Subject

```bash
git log -1 --format='%s'
```

Example:

```text
Fix authentication timeout
```

For automation, prefer explicit formatting:

```bash
SUBJECT="$(git log -1 --format='%s')"
```

---

# 31.10 Get Commit Author

```bash
git log -1 --format='%an'
```

Email:

```bash
git log -1 --format='%ae'
```

Example:

```text
Developer
developer@example.com
```

---

# 31.11 Get Commit Date

Unix timestamp:

```bash
git log -1 --format='%ct'
```

ISO-like date:

```bash
git log -1 --format='%cI'
```

Example:

```text
2026-08-12T10:30:00+00:00
```

The explicit format is preferable for automation.

---

# 31.12 Validate a Commit

```bash
git rev-parse --verify "$COMMIT^{commit}"
```

Example:

```bash
git rev-parse --verify HEAD^{commit}
```

Boolean test:

```bash
if git rev-parse --verify --quiet "$COMMIT^{commit}" >/dev/null; then
    echo "Valid commit"
else
    echo "Invalid commit" >&2
    exit 1
fi
```

---

# 31.13 Validate a Branch

```bash
git show-ref --verify --quiet "refs/heads/main"
```

Example:

```bash
if git show-ref --verify --quiet refs/heads/main; then
    echo "Local main branch exists"
fi
```

Remote branch:

```bash
git show-ref --verify --quiet refs/remotes/origin/main
```

---

# 31.14 Validate a Tag

```bash
git rev-parse --verify --quiet "refs/tags/v1.2.0"
```

Example:

```bash
if git rev-parse --verify --quiet refs/tags/v1.2.0 >/dev/null; then
    echo "Tag exists"
fi
```

---

# 31.15 Check Working Tree

```bash
git status --porcelain
```

Clean repository:

```text
<empty>
```

Dirty repository:

```text
 M src/main.c
?? build/
```

CI validation:

```bash
if [ -n "$(git status --porcelain)" ]; then
    echo "Unexpected working tree changes" >&2
    exit 1
fi
```

---

# 31.16 Check Staged Changes

```bash
git diff --cached --quiet
```

No staged changes:

```text
exit code 0
```

Staged changes:

```text
exit code 1
```

This is useful when automation creates files and must determine whether a commit is necessary.

---

# 31.17 Check Untracked Files

```bash
git ls-files --others --exclude-standard
```

Example:

```text
build/output.bin
tmp/result.txt
```

A CI pipeline can reject unexpected generated files:

```bash
if [ -n "$(git ls-files --others --exclude-standard)" ]; then
    echo "Unexpected untracked files detected" >&2
    exit 1
fi
```

---

# 31.18 Machine-Readable Status

Preferred:

```bash
git status --porcelain=v2
```

For simpler scripts:

```bash
git status --porcelain
```

Avoid parsing:

```bash
git status
```

because human-oriented output is not intended as a stable scripting interface.

---

# 31.19 Check Whitespace Errors

```bash
git diff --check
```

Check staged changes:

```bash
git diff --cached --check
```

CI:

```bash
if ! git diff --check; then
    echo "Whitespace errors detected" >&2
    exit 1
fi
```

---

# 31.20 List Changed Files

```bash
git diff --name-only
```

Staged:

```bash
git diff --cached --name-only
```

Between commits:

```bash
git diff --name-only "$BASE_SHA" "$HEAD_SHA"
```

Example:

```text
src/main.c
src/auth.c
tests/auth.test.c
```

---

# 31.21 Detect Changes Between Commits

```bash
git diff --quiet "$BASE_SHA" "$HEAD_SHA"
```

Example:

```bash
if git diff --quiet "$BASE_SHA" "$HEAD_SHA"; then
    echo "No changes"
else
    echo "Changes detected"
fi
```

---

# 31.22 Detect Changes Between Branches

```bash
git diff --quiet main...feature
```

List files:

```bash
git diff --name-only main...feature
```

The three-dot syntax compares the feature branch against the merge base with `main`.

---

# 31.23 Count Commits

```bash
git rev-list --count HEAD
```

Count commits introduced by a branch:

```bash
git rev-list --count origin/main..HEAD
```

Example:

```text
12
```

Useful for release metrics and CI reporting.

---

# 31.24 Check Branch Ancestry

```bash
git merge-base --is-ancestor origin/main HEAD
```

Example:

```bash
if git merge-base --is-ancestor origin/main HEAD; then
    echo "HEAD contains origin/main"
fi
```

This is useful for checking whether a branch is up to date.

---

# 31.25 Find Merge Base

```bash
git merge-base origin/main HEAD
```

Example:

```text
abc123456789...
```

This commit can be used as the base for pull-request or feature-branch comparisons.

---

# 31.26 Fetch Repository

```bash
git fetch origin
```

This updates remote-tracking references without modifying the working tree.

Branch state:

```text
Before: current branch unchanged
After:  current branch unchanged
```

Remote-tracking references may change.

---

# 31.27 Fetch with Pruning

```bash
git fetch --prune origin
```

This removes stale remote-tracking references.

Example:

```text
origin/old-feature
```

may disappear if the corresponding remote branch no longer exists.

---

# 31.28 Fetch Tags

```bash
git fetch --tags origin
```

This downloads tags that are available from the remote according to fetch behavior.

Useful before release operations.

---

# 31.29 Fetch a Specific Branch

```bash
git fetch origin main
```

Then inspect:

```bash
git rev-parse origin/main
```

The current branch is not changed.

---

# 31.30 Fetch a Specific Commit

```bash
git fetch origin "$COMMIT"
```

Whether a remote permits fetching an arbitrary commit depends on the server and protocol configuration.

For CI systems, fetching the exact commit through the CI provider's configured refspec is generally preferable.

---

# 31.31 Clone for CI

Basic:

```bash
git clone "$REPOSITORY" project
```

Then:

```bash
cd project
```

CI should generally control:

* Branch
* Commit
* Fetch depth
* Tags
* Submodules
* LFS
* Authentication

---

# 31.32 Shallow Clone

Clone only recent history:

```bash
git clone --depth 1 "$REPOSITORY" project
```

Useful when the pipeline does not need historical commits.

Advantages:

* Faster clone
* Less network traffic
* Less disk usage

Limitations:

* Some history-based operations are unavailable or incomplete.
* `git describe` may not find expected tags.
* Merge-base operations may require additional history.

---

# 31.33 Clone a Specific Branch

```bash
git clone --branch main --single-branch "$REPOSITORY" project
```

This limits the initial checkout to the specified branch.

---

# 31.34 Clone Without Checkout

```bash
git clone --no-checkout "$REPOSITORY" project
```

Useful when the pipeline wants to configure sparse checkout or otherwise control the checkout process.

---

# 31.35 Partial Clone

Use a blob filter:

```bash
git clone --filter=blob:none "$REPOSITORY" project
```

This can reduce initial transfer size for repositories with large files or extensive history.

The required objects can be downloaded later.

---

# 31.36 Sparse Clone

Combine sparse checkout with clone:

```bash
git clone --sparse "$REPOSITORY" project
```

Then:

```bash
cd project
git sparse-checkout set --cone services/payment
```

Useful for monorepos.

---

# 31.37 Sparse Checkout

Enable:

```bash
git sparse-checkout init --cone
```

Select directories:

```bash
git sparse-checkout set --cone src services
```

Add another directory:

```bash
git sparse-checkout add --cone infrastructure
```

Inspect:

```bash
git sparse-checkout list
```

---

# 31.38 Checkout Exact Commit

```bash
git checkout --detach "$COMMIT"
```

Modern equivalent:

```bash
git switch --detach "$COMMIT"
```

Result:

```text
HEAD -> exact commit
```

There is no moving branch involved.

---

# 31.39 Detached HEAD for CI

A deterministic CI checkout:

```bash
git fetch origin
git checkout --detach "$CI_COMMIT_SHA"
```

Verify:

```bash
git rev-parse HEAD
```

Expected:

```text
$CI_COMMIT_SHA
```

This is preferable to assuming that the branch name uniquely identifies the build source.

---

# 31.40 Fast-Forward Validation

Check whether `main` is an ancestor:

```bash
git merge-base --is-ancestor origin/main HEAD
```

If successful:

```text
HEAD contains origin/main
```

This is useful for validating that a deployment branch contains the latest mainline.

---

# 31.41 Fast-Forward Merge

```bash
git merge --ff-only origin/main
```

This refuses to create a merge commit.

If a fast-forward is impossible, the command fails.

This is useful for automated environments where unexpected merge commits are undesirable.

---

# 31.42 Fetch and Merge Explicitly

Instead of:

```bash
git pull
```

prefer:

```bash
git fetch origin
git merge origin/main
```

For strict automation:

```bash
git fetch origin
git merge --ff-only origin/main
```

This separates network operations from integration operations.

---

# 31.43 Fetch and Rebase Explicitly

```bash
git fetch origin
git rebase origin/main
```

This is useful when the workflow explicitly requires a rebased branch.

CI should generally avoid modifying developer branches unless branch mutation is part of the pipeline's purpose.

---

# 31.44 Avoid Interactive Git

Avoid commands that may wait for:

* Passwords
* SSH confirmation
* Editor input
* Merge messages
* Rebase instructions
* User confirmation

CI should use explicit options and environment variables.

---

# 31.45 Disable Terminal Prompts

```bash
GIT_TERMINAL_PROMPT=0 git fetch origin
```

Example:

```bash
if ! GIT_TERMINAL_PROMPT=0 git fetch origin; then
    echo "Fetch failed" >&2
    exit 1
fi
```

This prevents a CI job from hanging while waiting for terminal credentials.

---

# 31.46 Configure CI Identity

Use temporary configuration:

```bash
git \
  -c user.name="CI Bot" \
  -c user.email="ci@example.com" \
  commit -m "Automated update"
```

Alternatively:

```bash
export GIT_AUTHOR_NAME="CI Bot"
export GIT_AUTHOR_EMAIL="ci@example.com"
export GIT_COMMITTER_NAME="CI Bot"
export GIT_COMMITTER_EMAIL="ci@example.com"
```

Temporary configuration is often clearer and more localized.

---

# 31.47 Automated Commit

```bash
git add generated/
git -c user.name="CI Bot" \
    -c user.email="ci@example.com" \
    commit -m "Update generated files"
```

Branch state:

```text
Before: A
After:  A -> B
```

The current branch advances to the new commit.

---

# 31.48 Detect Changes Before Commit

```bash
git add generated/

if git diff --cached --quiet; then
    echo "Nothing to commit"
else
    git -c user.name="CI Bot" \
        -c user.email="ci@example.com" \
        commit -m "Update generated files"
fi
```

This avoids unnecessary empty commits.

---

# 31.49 Automated Tag

```bash
git tag -a "v${VERSION}" -m "Release v${VERSION}"
```

Example:

```bash
git tag -a v2.4.0 -m "Release v2.4.0"
```

Branch state:

```text
Before: A
After:  A + tag v2.4.0
```

The branch itself does not move.

---

# 31.50 Validate Version Tag

```bash
TAG="v2.4.0"

if git rev-parse --verify --quiet "refs/tags/$TAG" >/dev/null; then
    echo "Tag already exists" >&2
    exit 1
fi
```

This prevents accidental duplicate release tags.

---

# 31.51 Push Branch

```bash
git push origin main
```

Explicit refspec:

```bash
git push origin HEAD:refs/heads/main
```

The second form is useful when the current branch name should not be trusted or does not exist because HEAD is detached.

---

# 31.52 Push Tag

```bash
git push origin v2.4.0
```

The branch remains unchanged.

The remote tag is created or updated according to Git's normal push rules.

---

# 31.53 Push All Tags

```bash
git push origin --tags
```

Use with caution in release automation.

It can publish tags that were not intended for the current release.

Prefer pushing explicit tags:

```bash
git push origin "$TAG"
```

---

# 31.54 Push with Explicit Refspec

```bash
git push origin HEAD:refs/heads/main
```

This means:

```text
local HEAD
    |
    v
remote refs/heads/main
```

This is useful in controlled automation.

---

# 31.55 Verify Remote

```bash
git remote -v
```

Example:

```text
origin  git@example.com:team/project.git (fetch)
origin  git@example.com:team/project.git (push)
```

---

# 31.56 Inspect Remote URLs

```bash
git remote get-url origin
```

Push URL:

```bash
git remote get-url --push origin
```

All URLs:

```bash
git remote get-url --all origin
```

---

# 31.57 Test Remote Access

```bash
git ls-remote origin
```

This contacts the remote and lists references.

It is useful for validating:

* Authentication
* Network connectivity
* Remote availability
* Repository access

---

# 31.58 List Remote References

```bash
git ls-remote origin
```

Only heads:

```bash
git ls-remote --heads origin
```

Only tags:

```bash
git ls-remote --tags origin
```

---

# 31.59 Check Remote Branch

```bash
git ls-remote --exit-code --heads origin main
```

A successful result indicates that the remote branch exists.

---

# 31.60 Delete Remote Branch

```bash
git push origin --delete feature/old-feature
```

Equivalent refspec form:

```bash
git push origin :refs/heads/feature/old-feature
```

This is destructive at the remote reference level.

---

# 31.61 Delete Remote Tag

```bash
git push origin --delete v1.0.0
```

This deletes the remote tag.

It does not automatically delete the local tag.

---

# 31.62 Prune Remote References

```bash
git fetch --prune origin
```

Or:

```bash
git remote prune origin
```

`fetch --prune` is generally preferable when you also want to update remote-tracking references.

---

# 31.63 CI Build Checkout

A typical build pipeline:

```bash
#!/usr/bin/env bash

set -euo pipefail

git fetch --prune origin
git checkout --detach "$CI_COMMIT_SHA"

git status --porcelain=v2
git rev-parse HEAD

./build.sh
```

Branch state:

```text
Before: depends on runner
After:  detached HEAD at CI_COMMIT_SHA
```

---

# 31.64 CI Release Checkout

Release pipeline:

```bash
#!/usr/bin/env bash

set -euo pipefail

git fetch --tags --prune origin

git rev-parse --verify "$RELEASE_COMMIT^{commit}"

git checkout --detach "$RELEASE_COMMIT"

git describe --tags --always
```

This ensures the release is built from an exact commit.

---

# 31.65 CI Test Checkout

For testing a feature branch:

```bash
git fetch origin "$BRANCH"
git checkout --detach "origin/$BRANCH"
```

Then:

```bash
git status --porcelain=v2
```

Run tests:

```bash
./run-tests.sh
```

---

# 31.66 CI Diff Against Main

Fetch main:

```bash
git fetch origin main
```

Find changed files:

```bash
git diff --name-only origin/main...HEAD
```

Check whether there are changes:

```bash
git diff --quiet origin/main...HEAD
```

This is commonly used in pull-request pipelines.

---

# 31.67 Determine Changed Components

Example:

```bash
CHANGED="$(git diff --name-only origin/main...HEAD)"
```

Then inspect:

```bash
printf '%s\n' "$CHANGED"
```

A pipeline can use this to decide which components require testing.

For example:

```text
services/api/
services/frontend/
```

may trigger separate jobs.

---

# 31.68 Detect Documentation Changes

```bash
git diff --quiet origin/main...HEAD -- '*.md' 'docs/'
```

Use:

```bash
if git diff --quiet origin/main...HEAD -- '*.md' 'docs/'; then
    echo "No documentation changes"
else
    echo "Documentation changed"
fi
```

Branch state remains unchanged.

---

# 31.69 Detect Source Changes

Example:

```bash
git diff --quiet origin/main...HEAD -- 'src/' '*.c' '*.cpp' '*.h'
```

This can determine whether a source-code test suite should run.

---

# 31.70 Detect Infrastructure Changes

Example:

```bash
git diff --quiet origin/main...HEAD -- \
    'Dockerfile' \
    'docker/' \
    'k8s/' \
    'terraform/' \
    '.github/'
```

This can trigger infrastructure-specific CI jobs.

---

# 31.71 Detect Changes in a Directory

```bash
git diff --quiet origin/main...HEAD -- services/payment/
```

Example:

```bash
if ! git diff --quiet origin/main...HEAD -- services/payment/; then
    echo "Payment service changed"
fi
```

---

# 31.72 Generate Changed File List

```bash
git diff --name-only origin/main...HEAD > changed-files.txt
```

For safer filename handling:

```bash
git diff --name-only -z origin/main...HEAD > changed-files.bin
```

Use null-delimited processing if filenames can contain unusual characters.

---

# 31.73 Git-Based Versioning

Git can provide version information without maintaining a separate version file.

Common sources include:

```bash
git describe
git describe --tags
git describe --tags --always
```

This can produce values such as:

```text
v2.4.0-12-g7f8a9c1
```

Meaning approximately:

```text
12 commits after v2.4.0
at commit 7f8a9c1
```

---

# 31.74 Describe Current Version

```bash
git describe --tags --always
```

Example:

```text
v2.4.0-12-g7f8a9c1
```

For exact tags:

```bash
git describe --tags --exact-match
```

This fails if HEAD is not exactly at a tag.

---

# 31.75 Find Latest Tag

```bash
git describe --tags --abbrev=0
```

Example:

```text
v2.4.0
```

This is useful for determining the most recent reachable version tag.

---

# 31.76 Generate Version from Tag

Example:

```bash
VERSION="$(git describe --tags --abbrev=0)"
```

Then:

```bash
echo "$VERSION"
```

Output:

```text
v2.4.0
```

If no suitable tag exists, the command can fail, so automation should handle that case explicitly.

---

# 31.77 Generate Build Identifier

```bash
SHA="$(git rev-parse --short=12 HEAD)"
VERSION="$(git describe --tags --always)"
BUILD_ID="${VERSION}-${SHA}"
```

Example:

```text
v2.4.0-12-g7f8a9c1-7f8a9c123456
```

The exact format should be standardized by the project.

---

# 31.78 Generate Release Archive

```bash
git archive \
    --format=tar.gz \
    --prefix="project-${VERSION}/" \
    --output="project-${VERSION}.tar.gz" \
    HEAD
```

The archive contains files from the Git tree without the `.git` directory.

---

# 31.79 Generate Source Archive

```bash
git archive \
    --format=tar \
    --prefix="project-${VERSION}/" \
    HEAD \
    > "project-${VERSION}.tar"
```

Compress:

```bash
gzip "project-${VERSION}.tar"
```

Result:

```text
project-v2.4.0.tar.gz
```

---

# 31.80 Verify Release Archive

List contents:

```bash
tar -tzf "project-${VERSION}.tar.gz"
```

Check archive readability:

```bash
tar -tzf "project-${VERSION}.tar.gz" >/dev/null
```

CI:

```bash
if ! tar -tzf "$ARCHIVE" >/dev/null; then
    echo "Invalid release archive" >&2
    exit 1
fi
```

---

# 31.81 Generate Checksums

Git creates the source archive; Linux tools can generate checksums:

```bash
sha256sum "project-${VERSION}.tar.gz" > "project-${VERSION}.tar.gz.sha256"
```

Verify:

```bash
sha256sum -c "project-${VERSION}.tar.gz.sha256"
```

This is useful for release integrity.

---

# 31.82 Git Bundle for Backup

Create:

```bash
git bundle create repository.bundle --all
```

Verify:

```bash
git bundle verify repository.bundle
```

List heads:

```bash
git bundle list-heads repository.bundle
```

This creates a portable Git history bundle.

---

# 31.83 Git Worktree for Builds

Create isolated build directory:

```bash
git worktree add --detach /tmp/build "$COMMIT"
```

Build:

```bash
cd /tmp/build
./build.sh
```

The original checkout remains unchanged.

---

# 31.84 Clean CI Worktree

Remove worktree:

```bash
git worktree remove /tmp/build
```

List worktrees:

```bash
git worktree list
```

Prune stale metadata:

```bash
git worktree prune
```

---

# 31.85 Git LFS in CI

If a repository uses Git LFS:

```bash
git lfs install
```

Fetch LFS objects:

```bash
git lfs pull
```

Fetch all required LFS objects:

```bash
git lfs fetch
```

Check LFS status:

```bash
git lfs status
```

CI runners must have Git LFS installed if the build depends on LFS-managed files.

---

# 31.86 Submodules in CI

Clone with submodules:

```bash
git clone --recurse-submodules "$REPOSITORY" project
```

Initialize after clone:

```bash
git submodule update --init
```

Recursive:

```bash
git submodule update --init --recursive
```

---

# 31.87 Recursive Submodules

```bash
git submodule update --init --recursive
```

This initializes nested submodules as well.

Useful when a repository contains:

```text
project
├── dependency-a
│   └── dependency-a-child
└── dependency-b
```

---

# 31.88 Verify Submodules

```bash
git submodule status
```

Recursive:

```bash
git submodule status --recursive
```

A CI pipeline can use this to confirm that submodules are at expected commits.

---

# 31.89 Git Hooks in Automation

Git hooks may execute automatically during operations such as:

```text
commit
merge
rebase
push
```

CI environments should understand whether hooks exist and whether they are expected.

Inspect:

```bash
git config --get core.hooksPath
```

---

# 31.90 Disable Hooks for Controlled Automation

Some commands support:

```bash
--no-verify
```

For example:

```bash
git commit --no-verify -m "Automated update"
```

This bypasses applicable commit hooks.

Use this carefully.

Do not use it merely to bypass legitimate validation unless the automation intentionally replaces that validation elsewhere.

---

# 31.91 Repository Maintenance in CI

Inspect repository statistics:

```bash
git count-objects -vH
```

Verify repository:

```bash
git fsck --full
```

Maintenance:

```bash
git maintenance run
```

Automatic maintenance can be useful for long-lived CI workspaces.

Ephemeral CI clones generally do not require extensive manual maintenance.

---

# 31.92 Garbage Collection

```bash
git gc
```

More aggressive:

```bash
git gc --aggressive
```

Do not routinely run `--aggressive` in every CI job.

It can consume significant CPU and I/O.

For ephemeral runners, deleting the workspace is usually more appropriate.

---

# 31.93 Repository Integrity Check

```bash
git fsck --full
```

This checks Git object connectivity and integrity.

For CI validation:

```bash
if ! git fsck --full; then
    echo "Repository integrity check failed" >&2
    exit 1
fi
```

This can be expensive for large repositories.

---

# 31.94 Repository Statistics

```bash
git count-objects -vH
```

Example output:

```text
count: 12
size: 4.50 KiB
in-pack: 12543
packs: 3
size-pack: 15.20 MiB
prune-packable: 0
garbage: 0
size-garbage: 0 bytes
```

Useful for diagnosing repository growth.

---

# 31.95 Object Count

```bash
git count-objects -v
```

This provides information about loose and packed Git objects.

Useful when diagnosing:

* Repository size
* Pack files
* Garbage
* Maintenance issues

---

# 31.96 Git Trace in CI

Enable:

```bash
GIT_TRACE=1 git fetch origin
```

Use this only during diagnostics.

Trace output can increase CI logs significantly.

---

# 31.97 Git Trace2 in CI

```bash
GIT_TRACE2=1 git fetch origin
```

Performance:

```bash
GIT_TRACE2_PERF=1 git fetch origin
```

For persistent diagnostics:

```bash
GIT_TRACE2_PERF=/tmp/git-perf.log git fetch origin
```

Avoid publishing sensitive trace logs as build artifacts without reviewing their contents.

---

# 31.98 CI Authentication

For HTTPS remotes, authentication is commonly supplied by:

* CI credential stores
* Credential helpers
* Environment-specific authentication
* Temporary access tokens
* Platform-specific mechanisms

For SSH:

```bash
GIT_SSH_COMMAND="ssh -o BatchMode=yes" git fetch origin
```

`BatchMode=yes` prevents SSH from asking for interactive input.

---

# 31.99 Secure Credential Handling

Avoid:

```bash
git clone https://username:password@example.com/project.git
```

because credentials may become visible in:

* Shell history
* Process listings
* CI logs
* Configuration files
* Error messages

Prefer the CI platform's secure credential mechanism.

For SSH:

```bash
GIT_SSH_COMMAND="ssh -o BatchMode=yes" git fetch origin
```

For HTTPS, use the CI provider's supported secure authentication method.

---

# 31.100 CI Failure Handling

Use:

```bash
set -euo pipefail
```

Then explicitly handle expected failures.

Example:

```bash
if ! git diff --quiet; then
    echo "Changes detected"
fi
```

Do not treat every non-zero Git exit code as an unexpected failure.

Some commands intentionally use exit status to communicate conditions.

---

# 31.101 Reproducible CI Checkout

Recommended pattern:

```bash
set -euo pipefail

git fetch --prune origin

git rev-parse --verify "$CI_COMMIT_SHA^{commit}"

git checkout --detach "$CI_COMMIT_SHA"

test "$(git rev-parse HEAD)" = "$CI_COMMIT_SHA"
```

This validates that the working directory is actually at the expected commit.

---

# 31.102 CI Repository Validation Script

Example:

```bash
#!/usr/bin/env bash

set -euo pipefail

ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"

echo "Repository: $ROOT"

git rev-parse --verify HEAD >/dev/null

echo "Commit: $(git rev-parse HEAD)"

if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is dirty" >&2
    exit 1
fi

git diff --check

echo "Repository validation passed"
```

Expected state:

```text
Repository valid
Working tree clean
No whitespace errors
```

---

# 31.103 CI Build Script

Example:

```bash
#!/usr/bin/env bash

set -euo pipefail

ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"

COMMIT="$(git rev-parse HEAD)"
VERSION="$(git describe --tags --always)"

echo "Building:"
echo "  Commit: $COMMIT"
echo "  Version: $VERSION"

git status --porcelain=v2

./configure
make
make test
```

The build operates from the exact Git state checked out by the pipeline.

---

# 31.104 CI Release Script

Example:

```bash
#!/usr/bin/env bash

set -euo pipefail

ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"

VERSION="$(git describe --tags --exact-match)"
COMMIT="$(git rev-parse HEAD)"

ARCHIVE="project-${VERSION}.tar.gz"

echo "Release: $VERSION"
echo "Commit:  $COMMIT"

git archive \
    --format=tar.gz \
    --prefix="project-${VERSION}/" \
    --output="$ARCHIVE" \
    HEAD

tar -tzf "$ARCHIVE" >/dev/null

sha256sum "$ARCHIVE" > "${ARCHIVE}.sha256"

echo "Release artifacts:"
echo "  $ARCHIVE"
echo "  ${ARCHIVE}.sha256"
```

This creates:

```text
project-v2.4.0.tar.gz
project-v2.4.0.tar.gz.sha256
```

---

# 31.105 Common CI Git Command Reference

| Command                                   | Description                 | Example                                                    | Branch State Before and After command      | Output                  |
| ----------------------------------------- | --------------------------- | ---------------------------------------------------------- | ------------------------------------------ | ----------------------- |
| `git --version`                           | Show Git version            | `git --version`                                            | Unchanged                                  | Git version             |
| `git rev-parse --show-toplevel`           | Find repository root        | `git rev-parse --show-toplevel`                            | Unchanged                                  | Repository path         |
| `git rev-parse --is-inside-work-tree`     | Validate repository         | `git rev-parse --is-inside-work-tree`                      | Unchanged                                  | `true`/`false`          |
| `git branch --show-current`               | Get branch name             | `git branch --show-current`                                | Unchanged                                  | Branch                  |
| `git rev-parse HEAD`                      | Get exact commit            | `git rev-parse HEAD`                                       | Unchanged                                  | SHA                     |
| `git rev-parse --short HEAD`              | Get short SHA               | `git rev-parse --short HEAD`                               | Unchanged                                  | Short SHA               |
| `git log -1 --format='%s'`                | Get commit subject          | `git log -1 --format='%s'`                                 | Unchanged                                  | Subject                 |
| `git log -1 --format='%cI'`               | Get commit date             | `git log -1 --format='%cI'`                                | Unchanged                                  | ISO timestamp           |
| `git status --porcelain=v2`               | Machine-readable status     | `git status --porcelain=v2`                                | Unchanged                                  | Status                  |
| `git diff --check`                        | Validate whitespace         | `git diff --check`                                         | Unchanged                                  | Errors or none          |
| `git diff --name-only`                    | List changed files          | `git diff --name-only`                                     | Unchanged                                  | File names              |
| `git diff --quiet A B`                    | Test differences            | `git diff --quiet A B`                                     | Unchanged                                  | Usually none            |
| `git rev-list --count A..B`               | Count commits               | `git rev-list --count A..B`                                | Unchanged                                  | Integer                 |
| `git merge-base A B`                      | Find common ancestor        | `git merge-base A B`                                       | Unchanged                                  | SHA                     |
| `git merge-base --is-ancestor A B`        | Check ancestry              | `git merge-base --is-ancestor A B`                         | Unchanged                                  | Exit status             |
| `git fetch origin`                        | Fetch remote refs           | `git fetch origin`                                         | Current branch unchanged                   | Fetch information       |
| `git fetch --prune origin`                | Fetch and prune             | `git fetch --prune origin`                                 | Branch unchanged                           | Fetch information       |
| `git fetch --tags origin`                 | Fetch tags                  | `git fetch --tags origin`                                  | Branch unchanged                           | Fetch information       |
| `git clone URL`                           | Clone repository            | `git clone "$URL" project`                                 | New clone                                  | Clone information       |
| `git clone --depth 1 URL`                 | Shallow clone               | `git clone --depth 1 "$URL" project`                       | New clone                                  | Clone information       |
| `git clone --branch main URL`             | Clone branch                | `git clone --branch main "$URL" project`                   | New clone on `main`                        | Clone information       |
| `git clone --no-checkout URL`             | Clone without checkout      | `git clone --no-checkout "$URL" project`                   | No branch checkout                         | Clone information       |
| `git clone --filter=blob:none URL`        | Partial clone               | `git clone --filter=blob:none "$URL" project`              | New clone                                  | Clone information       |
| `git clone --sparse URL`                  | Sparse clone                | `git clone --sparse "$URL" project`                        | New clone                                  | Clone information       |
| `git checkout --detach SHA`               | Checkout exact commit       | `git checkout --detach "$SHA"`                             | Branch -> detached HEAD                    | Checkout information    |
| `git switch --detach SHA`                 | Modern exact checkout       | `git switch --detach "$SHA"`                               | Branch -> detached HEAD                    | Checkout information    |
| `git sparse-checkout set --cone PATH`     | Select sparse paths         | `git sparse-checkout set --cone src`                       | Branch unchanged                           | Usually none            |
| `git merge --ff-only REF`                 | Fast-forward only           | `git merge --ff-only origin/main`                          | Branch may advance                         | Merge information       |
| `git remote -v`                           | Show remotes                | `git remote -v`                                            | Unchanged                                  | URLs                    |
| `git remote get-url origin`               | Get remote URL              | `git remote get-url origin`                                | Unchanged                                  | URL                     |
| `git ls-remote origin`                    | Test/list remote refs       | `git ls-remote origin`                                     | Unchanged                                  | Remote refs             |
| `git ls-remote --heads origin`            | List remote branches        | `git ls-remote --heads origin`                             | Unchanged                                  | Branch refs             |
| `git ls-remote --tags origin`             | List remote tags            | `git ls-remote --tags origin`                              | Unchanged                                  | Tag refs                |
| `git push origin main`                    | Push branch                 | `git push origin main`                                     | Local branch unchanged; remote may advance | Push information        |
| `git push origin TAG`                     | Push tag                    | `git push origin "$TAG"`                                   | Branch unchanged; remote tag may advance   | Push information        |
| `git push origin HEAD:refs/heads/main`    | Push exact HEAD             | `git push origin HEAD:refs/heads/main`                     | Local branch unchanged                     | Push information        |
| `git push origin --delete BRANCH`         | Delete remote branch        | `git push origin --delete feature/x`                       | Local branch unchanged                     | Push information        |
| `git describe --tags --always`            | Generate Git version        | `git describe --tags --always`                             | Unchanged                                  | Version string          |
| `git describe --tags --abbrev=0`          | Find latest reachable tag   | `git describe --tags --abbrev=0`                           | Unchanged                                  | Tag                     |
| `git archive`                             | Create source artifact      | `git archive --format=tar.gz --output=release.tar.gz HEAD` | Unchanged                                  | Archive                 |
| `git bundle create`                       | Create Git bundle           | `git bundle create repo.bundle --all`                      | Unchanged                                  | Bundle                  |
| `git worktree add`                        | Create isolated checkout    | `git worktree add --detach /tmp/build HEAD`                | Current branch unchanged                   | Worktree                |
| `git worktree remove`                     | Remove worktree             | `git worktree remove /tmp/build`                           | Current branch unchanged                   | Usually none            |
| `git lfs pull`                            | Download LFS objects        | `git lfs pull`                                             | Branch unchanged                           | LFS information         |
| `git submodule update --init --recursive` | Initialize submodules       | `git submodule update --init --recursive`                  | Superproject branch unchanged              | Submodule information   |
| `git fsck --full`                         | Verify repository integrity | `git fsck --full`                                          | Unchanged                                  | Integrity information   |
| `git count-objects -vH`                   | Repository statistics       | `git count-objects -vH`                                    | Unchanged                                  | Object statistics       |
| `git gc`                                  | Repository maintenance      | `git gc`                                                   | Unchanged                                  | Maintenance information |
| `GIT_TERMINAL_PROMPT=0 git fetch`         | Disable terminal prompting  | `GIT_TERMINAL_PROMPT=0 git fetch`                          | Branch unchanged                           | Fetch/error information |
| `GIT_TRACE=1 git fetch`                   | Trace Git execution         | `GIT_TRACE=1 git fetch`                                    | Branch unchanged                           | Trace + command output  |
| `GIT_TRACE2_PERF=1 git fetch`             | Trace performance           | `GIT_TRACE2_PERF=1 git fetch`                              | Branch unchanged                           | Performance trace       |

---

# 31.106 High-Value DevOps Git Commands

## Identify exact repository

```bash
git rev-parse --show-toplevel
```

## Identify exact commit

```bash
git rev-parse HEAD
```

## Validate expected commit

```bash
test "$(git rev-parse HEAD)" = "$CI_COMMIT_SHA"
```

## Check clean repository

```bash
test -z "$(git status --porcelain)"
```

## Machine-readable status

```bash
git status --porcelain=v2
```

## Fetch latest remote state

```bash
git fetch --prune origin
```

## Fetch tags

```bash
git fetch --tags --prune origin
```

## Exact CI checkout

```bash
git checkout --detach "$CI_COMMIT_SHA"
```

## Check ancestry

```bash
git merge-base --is-ancestor origin/main HEAD
```

## Detect changed files

```bash
git diff --name-only origin/main...HEAD
```

## Detect changes in a component

```bash
git diff --quiet origin/main...HEAD -- services/payment/
```

## Generate Git version

```bash
git describe --tags --always
```

## Find latest release tag

```bash
git describe --tags --abbrev=0
```

## List remote branches

```bash
git ls-remote --heads origin
```

## Test remote access

```bash
git ls-remote origin
```

## Push exact HEAD

```bash
git push origin HEAD:refs/heads/main
```

## Create release archive

```bash
git archive \
    --format=tar.gz \
    --prefix="project-${VERSION}/" \
    --output="project-${VERSION}.tar.gz" \
    HEAD
```

## Create Git bundle

```bash
git bundle create repository.bundle --all
```

## Create isolated build directory

```bash
git worktree add --detach /tmp/build "$CI_COMMIT_SHA"
```

## Initialize submodules

```bash
git submodule update --init --recursive
```

## Fetch Git LFS objects

```bash
git lfs pull
```

## Verify repository integrity

```bash
git fsck --full
```

## Disable interactive prompting

```bash
GIT_TERMINAL_PROMPT=0 git fetch origin
```

## Enable Git diagnostics

```bash
GIT_TRACE=1 git fetch origin
```

---

# Recommended CI/CD Git Template

A general-purpose Linux CI checkout can be structured as:

```bash
#!/usr/bin/env bash

set -euo pipefail

ROOT="$(git rev-parse --show-toplevel)"
cd "$ROOT"

echo "Repository: $ROOT"

git fetch --prune origin

git rev-parse --verify "$CI_COMMIT_SHA^{commit}"

git checkout --detach "$CI_COMMIT_SHA"

ACTUAL_SHA="$(git rev-parse HEAD)"

if [ "$ACTUAL_SHA" != "$CI_COMMIT_SHA" ]; then
    echo "Unexpected commit checked out" >&2
    exit 1
fi

if [ -n "$(git status --porcelain)" ]; then
    echo "Working tree is dirty" >&2
    exit 1
fi

git diff --check

echo "CI checkout validated"
echo "Commit: $ACTUAL_SHA"
```

This provides a strong baseline for reproducible CI jobs.

---

# Recommended Release Pipeline

```text
                     Git Remote
                         |
                         v
                  git fetch --tags
                         |
                         v
                Resolve release tag
                         |
                         v
                 Verify commit
                         |
                         v
               Detached exact checkout
                         |
                         v
                    Run tests
                         |
                         v
                  Build project
                         |
                         v
                 git archive
                         |
                         v
                    SHA-256
                         |
                         v
                 Publish artifact
```

A release should ideally be associated with an immutable Git reference such as a tag pointing to a specific commit.

---

# Recommended DevOps Git Rules

```text
1. Never assume the current branch is correct.
2. Identify the exact commit.
3. Prefer detached exact-commit checkouts for reproducible builds.
4. Fetch explicitly.
5. Avoid unnecessary full repository history.
6. Use shallow clones only when history is not required.
7. Use sparse checkout for large monorepos where appropriate.
8. Use partial clone when object transfer is the bottleneck.
9. Use --porcelain or explicit format strings in scripts.
10. Check Git exit codes.
11. Disable interactive prompts in CI.
12. Never expose credentials in command-line arguments when avoidable.
13. Use explicit push refspecs in sensitive automation.
14. Verify release tags before publishing.
15. Generate checksums for release artifacts.
16. Use worktrees for isolated concurrent builds.
17. Initialize submodules explicitly when required.
18. Fetch Git LFS objects explicitly when required.
19. Keep CI workspaces disposable when practical.
20. Make destructive commands explicit.
```

---

# Next Part

**Next file:** `32-common-developer-workflows.md`

[Next: Common Developer Workflows](32-common-developer-workflows.md)
