# 28. Sparse Checkout

Sparse checkout allows Git to maintain a working tree containing only a selected subset of files from a repository.

This is particularly useful for:

* Large monorepos
* Developer environments that need only one project
* CI/CD pipelines
* Large documentation repositories
* Repositories containing unrelated applications
* Partial working trees
* Reducing local filesystem usage

Sparse checkout does **not** remove files from Git history or from the repository.

It controls which tracked files are populated in the working tree.

---

# Table of Contents

* [28.1 What Is Sparse Checkout?](#281-what-is-sparse-checkout)
* [28.2 Sparse Checkout Mental Model](#282-sparse-checkout-mental-model)
* [28.3 Check Sparse Checkout Status](#283-check-sparse-checkout-status)
* [28.4 Enable Sparse Checkout](#284-enable-sparse-checkout)
* [28.5 Cone Mode](#285-cone-mode)
* [28.6 Non-Cone Mode](#286-non-cone-mode)
* [28.7 Select a Directory](#287-select-a-directory)
* [28.8 Select Multiple Directories](#288-select-multiple-directories)
* [28.9 Add Additional Directories](#289-add-additional-directories)
* [28.10 Remove a Directory](#2810-remove-a-directory)
* [28.11 Set the Complete Sparse Configuration](#2811-set-the-complete-sparse-configuration)
* [28.12 Disable Sparse Checkout](#2812-disable-sparse-checkout)
* [28.13 Reapply Sparse Checkout](#2813-reapply-sparse-checkout)
* [28.14 Sparse Checkout with Clone](#2814-sparse-checkout-with-clone)
* [28.15 Sparse Checkout with a Remote Repository](#2815-sparse-checkout-with-a-remote-repository)
* [28.16 Sparse Checkout in a Monorepo](#2816-sparse-checkout-in-a-monorepo)
* [28.17 Sparse Checkout and Branches](#2817-sparse-checkout-and-branches)
* [28.18 Sparse Checkout and Switching Branches](#2818-sparse-checkout-and-switching-branches)
* [28.19 Sparse Checkout and Staging](#2819-sparse-checkout-and-staging)
* [28.20 Sparse Checkout and Commits](#2820-sparse-checkout-and-commits)
* [28.21 Sparse Checkout Configuration](#2821-sparse-checkout-configuration)
* [28.22 Sparse-Checkout File](#2822-sparse-checkout-file)
* [28.23 Inspect Sparse Rules](#2823-inspect-sparse-rules)
* [28.24 Sparse Checkout with Git Worktrees](#2824-sparse-checkout-with-git-worktrees)
* [28.25 Sparse Checkout and Submodules](#2825-sparse-checkout-and-submodules)
* [28.26 Sparse Checkout and CI/CD](#2826-sparse-checkout-and-cicd)
* [28.27 Sparse Checkout Troubleshooting](#2827-sparse-checkout-troubleshooting)
* [28.28 Sparse Checkout vs Shallow Clone](#2828-sparse-checkout-vs-shallow-clone)
* [28.29 Sparse Checkout vs Partial Clone](#2829-sparse-checkout-vs-partial-clone)
* [28.30 Complete Sparse Checkout Command Reference](#2830-complete-sparse-checkout-command-reference)
* [28.31 High-Value Commands to Memorize](#2831-high-value-commands-to-memorize)

---

# 28.1 What Is Sparse Checkout?

Normally, after cloning a repository:

```bash
git clone https://example.com/company/project.git
```

Git populates the working tree with the files belonging to the checked-out commit.

For a large repository:

```text
project/
├── backend/
├── frontend/
├── mobile/
├── infrastructure/
├── documentation/
├── examples/
└── tools/
```

A developer working only on `backend/` may not need all of these directories.

Sparse checkout can produce:

```text
project/
└── backend/
```

while Git still knows about the complete repository.

The repository's history and tracked objects are not automatically reduced simply because the working tree is sparse.

---

# 28.2 Sparse Checkout Mental Model

Normal checkout:

```text
Repository
    |
    +--> backend/
    +--> frontend/
    +--> mobile/
    +--> docs/
    +--> infrastructure/
```

Sparse checkout:

```text
Repository
    |
    +--> backend/        populated
    |
    +--> frontend/       omitted from working tree
    +--> mobile/         omitted from working tree
    +--> docs/           omitted from working tree
    +--> infrastructure/ omitted from working tree
```

The important distinction is:

```text
Repository contents
        !=
Working-tree contents
```

Sparse checkout changes the second.

---

# 28.3 Check Sparse Checkout Status

Check the repository configuration:

```bash
git config --get core.sparseCheckout
```

If enabled, output may be:

```text
true
```

Check sparse checkout rules:

```bash
git sparse-checkout list
```

Example:

```text
backend
```

Another example:

```text
backend
infrastructure
```

If sparse checkout is not configured, the command may report that sparse checkout is not enabled.

Check repository status:

```bash
git status
```

The current branch remains unchanged by merely inspecting sparse checkout.

---

# 28.4 Enable Sparse Checkout

The recommended command is:

```bash
git sparse-checkout init
```

Modern Git supports:

```bash
git sparse-checkout set backend
```

which initializes sparse checkout and selects the requested directory.

Example:

```bash
git sparse-checkout set backend
```

After this, only the selected paths are populated in the working tree.

Check:

```bash
git sparse-checkout list
```

Output:

```text
backend
```

Branch state:

```text
Before: main -> commit X
After:  main -> commit X
```

The branch pointer does not move.

---

# 28.5 Cone Mode

Cone mode is the recommended mode for most directory-based sparse checkouts.

Enable:

```bash
git sparse-checkout set --cone backend
```

This selects:

```text
backend/
```

and the files needed for the sparse directory structure.

For multiple directories:

```bash
git sparse-checkout set --cone backend frontend
```

Cone mode is easier to understand and generally more efficient for common directory-oriented use cases.

---

# 28.6 Non-Cone Mode

Non-cone mode allows more expressive path patterns.

Example:

```bash
git sparse-checkout set --no-cone '/*.md'
```

This can select Markdown files matching the specified sparse-checkout pattern.

Another example:

```bash
git sparse-checkout set --no-cone '/backend/**'
```

Non-cone mode is useful when the required working-tree layout cannot be represented conveniently with cone mode.

However, cone mode should generally be preferred when directory selection is sufficient.

---

# 28.7 Select a Directory

Suppose the repository contains:

```text
backend/
frontend/
docs/
infra/
```

Select only `backend`:

```bash
git sparse-checkout set --cone backend
```

Check:

```bash
git sparse-checkout list
```

Output:

```text
backend
```

The working tree becomes approximately:

```text
backend/
```

while the repository itself still contains the other tracked paths.

---

# 28.8 Select Multiple Directories

Select:

```bash
git sparse-checkout set --cone backend docs
```

Result:

```text
backend/
docs/
```

Another example:

```bash
git sparse-checkout set --cone backend infrastructure tools
```

Working tree:

```text
backend/
infrastructure/
tools/
```

Branch state remains unchanged.

---

# 28.9 Add Additional Directories

Use:

```bash
git sparse-checkout add --cone docs
```

If the current sparse configuration is:

```text
backend
```

after:

```bash
git sparse-checkout add --cone docs
```

the configuration becomes:

```text
backend
docs
```

The existing sparse paths are preserved.

---

# 28.10 Remove a Directory

Use:

```bash
git sparse-checkout reapply
```

to reapply current rules, but to change the selected set most reliably use `set`.

For example, if the desired final configuration is only `backend`:

```bash
git sparse-checkout set --cone backend
```

If the previous configuration was:

```text
backend
docs
tools
```

the new working tree will contain only:

```text
backend
```

The branch pointer remains unchanged.

---

# 28.11 Set the Complete Sparse Configuration

The `set` command replaces the current sparse-checkout selection.

Example:

```bash
git sparse-checkout set --cone backend docs
```

If previously:

```text
backend
frontend
infra
```

afterwards:

```text
backend
docs
```

This is useful when you want the sparse configuration to be declarative:

```text
Desired paths:
    backend
    docs
```

rather than incrementally modifying the previous state.

---

# 28.12 Disable Sparse Checkout

To return to a normal working tree:

```bash
git sparse-checkout disable
```

Git repopulates files that were previously omitted.

Check:

```bash
git status
```

The branch remains on the same commit.

Conceptually:

```text
Before:
Sparse working tree

After:
Complete working tree
```

The repository history is not rewritten.

---

# 28.13 Reapply Sparse Checkout

Use:

```bash
git sparse-checkout reapply
```

This reapplies sparse-checkout rules to the working tree.

It can be useful after operations that modify the working tree or index.

Example:

```bash
git sparse-checkout reapply
```

Branch state:

```text
Before: main -> X
After:  main -> X
```

---

# 28.14 Sparse Checkout with Clone

A convenient workflow is to clone and immediately configure sparse checkout.

For example:

```bash
git clone --no-checkout https://example.com/company/project.git project
cd project
git sparse-checkout set --cone backend
git checkout main
```

A more direct modern workflow can use:

```bash
git clone --sparse https://example.com/company/project.git project
```

This creates a sparse checkout with the default sparse configuration.

Then:

```bash
cd project
```

and add directories as needed:

```bash
git sparse-checkout add --cone backend
```

---

# 28.15 Sparse Checkout with a Remote Repository

Sparse checkout does not prevent fetching commits.

For example:

```bash
git fetch origin
```

still updates remote-tracking references and downloads required objects according to the repository's fetch configuration.

Sparse checkout primarily determines what is populated in the working tree.

This distinction is important:

```text
git fetch
    |
    v
Repository objects / references

git sparse-checkout
    |
    v
Working-tree population
```

---

# 28.16 Sparse Checkout in a Monorepo

Consider:

```text
company/
├── services/
│   ├── payments/
│   ├── users/
│   └── orders/
├── frontend/
├── mobile/
├── infrastructure/
└── documentation/
```

A payments developer can use:

```bash
git sparse-checkout set --cone services/payments
```

A DevOps engineer might use:

```bash
git sparse-checkout set --cone infrastructure documentation
```

A frontend developer might use:

```bash
git sparse-checkout set --cone frontend documentation
```

This reduces working-tree clutter and can simplify local development.

---

# 28.17 Sparse Checkout and Branches

Sparse checkout is a working-tree configuration and is not itself a branch.

Suppose:

```text
main -> A
```

with:

```text
backend/
```

selected.

Switching branches:

```bash
git switch feature/login
```

does not inherently disable sparse checkout.

The sparse configuration continues to influence which paths are populated.

The branch pointer changes:

```text
main
 |
 A

feature/login
 |
 B
```

but sparse checkout remains a working-tree concern.

---

# 28.18 Sparse Checkout and Switching Branches

Example:

```bash
git sparse-checkout set --cone backend
git switch feature/api
```

The working tree remains sparse.

If `backend/` exists in the new branch, Git populates the appropriate files.

If paths differ between branches, the resulting working tree reflects both:

```text
Current branch contents
+
Current sparse rules
```

If the working tree appears inconsistent after complex operations:

```bash
git sparse-checkout reapply
```

---

# 28.19 Sparse Checkout and Staging

Sparse checkout does not eliminate Git's index.

You can still stage files normally:

```bash
git add backend/
```

Check staged changes:

```bash
git diff --cached --name-only
```

Sparse paths not populated in the working tree cannot simply be edited there because they are absent from the working tree.

This makes sparse checkout useful for focused development.

---

# 28.20 Sparse Checkout and Commits

Commits still represent repository snapshots.

Suppose the repository contains:

```text
backend/
frontend/
docs/
```

but sparse checkout exposes only:

```text
backend/
```

A commit created from changes in `backend/` does not mean the repository now contains only `backend/`.

The other paths remain in the repository unless they are explicitly changed or deleted.

Sparse checkout is therefore **not** a repository filtering or history rewriting mechanism.

---

# 28.21 Sparse Checkout Configuration

Inspect the configuration:

```bash
git config --get core.sparseCheckout
```

Inspect the sparse-checkout mechanism:

```bash
git config --get core.sparseCheckoutCone
```

Possible output:

```text
true
```

List configuration:

```bash
git config --local --list
```

Filter:

```bash
git config --local --list | grep sparse
```

Potential output:

```text
core.sparsecheckout=true
core.sparsecheckoutcone=true
```

---

# 28.22 Sparse-Checkout File

Git stores sparse-checkout patterns under:

```text
.git/info/sparse-checkout
```

Inspect it:

```bash
cat .git/info/sparse-checkout
```

Example:

```text
/*
!/*/
/backend/
```

The exact contents depend on the mode and selected paths.

Do not casually edit this file while using `git sparse-checkout`; prefer the command-line interface:

```bash
git sparse-checkout set
git sparse-checkout add
git sparse-checkout reapply
git sparse-checkout disable
```

---

# 28.23 Inspect Sparse Rules

List configured sparse paths:

```bash
git sparse-checkout list
```

Inspect the underlying file:

```bash
cat .git/info/sparse-checkout
```

Check configuration:

```bash
git config --get core.sparseCheckout
```

Check cone mode:

```bash
git config --get core.sparseCheckoutCone
```

A useful diagnostic sequence is:

```bash
git sparse-checkout list
git config --get core.sparseCheckout
git config --get core.sparseCheckoutCone
cat .git/info/sparse-checkout
```

---

# 28.24 Sparse Checkout with Git Worktrees

Git worktrees allow multiple working trees from one repository.

Sparse checkout can be combined with worktrees, but each worktree has its own working-tree state and configuration considerations.

Example:

```bash
git worktree add ../backend-worktree feature/backend
```

Then:

```bash
cd ../backend-worktree
git sparse-checkout set --cone backend
```

This allows one worktree to focus on one part of a repository.

Another worktree can have a different sparse configuration.

This is particularly useful in large monorepos.

---

# 28.25 Sparse Checkout and Submodules

Sparse checkout can affect paths containing submodules.

Example repository:

```text
project/
├── application/
├── infrastructure/
└── vendor/
    └── external-module/
```

If `vendor/` is omitted by sparse checkout, the submodule path may not be populated as it would be in a normal working tree.

When troubleshooting:

```bash
git submodule status
```

and:

```bash
git sparse-checkout list
```

Check whether the submodule path is included in the sparse configuration.

---

# 28.26 Sparse Checkout and CI/CD

Sparse checkout can reduce the amount of working-tree data used by CI jobs.

Example:

```bash
git clone --sparse "$REPOSITORY" project
cd project
git sparse-checkout set --cone services/payment
```

A CI pipeline that only needs:

```text
services/payment/
```

does not need to populate unrelated application directories.

A common pattern:

```bash
git fetch origin main
git sparse-checkout set --cone services/payment infrastructure
git checkout main
```

This can simplify and accelerate jobs that operate on specific repository areas.

However, sparse checkout should not be confused with partial clone.

---

# 28.27 Sparse Checkout Troubleshooting

## Problem: Files are missing

Check:

```bash
git sparse-checkout list
```

If sparse checkout is enabled, the missing files may simply be outside the selected paths.

Disable it temporarily:

```bash
git sparse-checkout disable
```

If the files appear, sparse checkout was responsible.

---

## Problem: A directory cannot be added

Check whether cone mode is enabled:

```bash
git config --get core.sparseCheckoutCone
```

For normal directories, use:

```bash
git sparse-checkout set --cone directory
```

---

## Problem: Working tree looks inconsistent

Run:

```bash
git sparse-checkout reapply
```

Then:

```bash
git status
```

---

## Problem: Sparse checkout is preventing access to a file

Add its parent directory:

```bash
git sparse-checkout add --cone path/to/directory
```

Or replace the sparse configuration:

```bash
git sparse-checkout set --cone path/to/directory
```

---

## Problem: Need the complete repository working tree again

Run:

```bash
git sparse-checkout disable
```

---

# 28.28 Sparse Checkout vs Shallow Clone

These solve different problems.

### Sparse Checkout

Controls:

```text
Which files are populated in the working tree
```

Example:

```bash
git sparse-checkout set --cone backend
```

### Shallow Clone

Controls:

```text
How much commit history is initially fetched
```

Example:

```bash
git clone --depth 1 URL
```

They can be combined.

For example:

```bash
git clone --depth 1 --sparse URL project
cd project
git sparse-checkout set --cone backend
```

Conceptually:

```text
Sparse checkout
    -> fewer working-tree paths

Shallow clone
    -> less commit history
```

---

# 28.29 Sparse Checkout vs Partial Clone

Partial clone and sparse checkout are also different.

Sparse checkout:

```text
Controls working-tree paths
```

Partial clone:

```text
Can avoid downloading some Git objects until they are needed
```

Example partial clone:

```bash
git clone --filter=blob:none URL project
```

Example sparse checkout:

```bash
git sparse-checkout set --cone backend
```

They can be combined:

```bash
git clone --filter=blob:none --sparse URL project
```

Then:

```bash
cd project
git sparse-checkout set --cone backend
```

This combination can be very useful for large repositories.

---

# 28.30 Complete Sparse Checkout Command Reference

| Command                                      | Description                                         | Example                                         | Branch State Before and After command                     | Output               |
| -------------------------------------------- | --------------------------------------------------- | ----------------------------------------------- | --------------------------------------------------------- | -------------------- |
| `git sparse-checkout init`                   | Initialize sparse checkout                          | `git sparse-checkout init`                      | Branch unchanged                                          | Usually no output    |
| `git sparse-checkout set --cone path`        | Select directory in cone mode                       | `git sparse-checkout set --cone backend`        | Branch unchanged; working tree changes                    | Usually no output    |
| `git sparse-checkout set --cone path1 path2` | Select multiple directories                         | `git sparse-checkout set --cone backend docs`   | Branch unchanged; working tree changes                    | Usually no output    |
| `git sparse-checkout set --no-cone pattern`  | Configure pattern-based sparse checkout             | `git sparse-checkout set --no-cone '/*.md'`     | Branch unchanged; working tree changes                    | Usually no output    |
| `git sparse-checkout add --cone path`        | Add a directory to sparse selection                 | `git sparse-checkout add --cone docs`           | Branch unchanged; working tree changes                    | Usually no output    |
| `git sparse-checkout add --no-cone pattern`  | Add a non-cone pattern                              | `git sparse-checkout add --no-cone '/tools/**'` | Branch unchanged; working tree changes                    | Usually no output    |
| `git sparse-checkout list`                   | Show sparse paths                                   | `git sparse-checkout list`                      | Branch unchanged                                          | Sparse paths         |
| `git sparse-checkout reapply`                | Reapply current sparse rules                        | `git sparse-checkout reapply`                   | Branch unchanged; working tree may change                 | Usually no output    |
| `git sparse-checkout disable`                | Disable sparse checkout                             | `git sparse-checkout disable`                   | Branch unchanged; working tree becomes complete           | Usually no output    |
| `git clone --sparse URL`                     | Clone with sparse checkout enabled                  | `git clone --sparse URL repo`                   | New local branch created at cloned commit                 | Clone progress       |
| `git clone --no-checkout URL`                | Clone without populating working tree               | `git clone --no-checkout URL repo`              | New repository; branch may point to default remote branch | Clone progress       |
| `git config --get core.sparseCheckout`       | Check sparse configuration                          | `git config --get core.sparseCheckout`          | Branch unchanged                                          | `true` or no output  |
| `git config --get core.sparseCheckoutCone`   | Check cone mode                                     | `git config --get core.sparseCheckoutCone`      | Branch unchanged                                          | `true` or no output  |
| `cat .git/info/sparse-checkout`              | Inspect sparse patterns                             | `cat .git/info/sparse-checkout`                 | Branch unchanged                                          | Sparse patterns      |
| `git status`                                 | Inspect resulting working tree                      | `git status`                                    | Branch unchanged                                          | Repository status    |
| `git fetch origin`                           | Fetch remote updates                                | `git fetch origin`                              | Branch unchanged; remote-tracking refs may move           | Fetch output         |
| `git switch branch`                          | Switch branch while respecting sparse configuration | `git switch feature`                            | Branch changes                                            | Branch-switch output |
| `git checkout branch`                        | Older branch-switch/checkout workflow               | `git checkout feature`                          | Branch changes                                            | Branch-switch output |
| `git worktree add PATH branch`               | Create another worktree                             | `git worktree add ../work feature`              | Main branch unchanged; new worktree created               | Worktree information |

---

# 28.31 High-Value Commands to Memorize

## Enable/select a directory

```bash
git sparse-checkout set --cone backend
```

## Select multiple directories

```bash
git sparse-checkout set --cone backend docs
```

## Add another directory

```bash
git sparse-checkout add --cone tools
```

## Show selected paths

```bash
git sparse-checkout list
```

## Reapply sparse rules

```bash
git sparse-checkout reapply
```

## Disable sparse checkout

```bash
git sparse-checkout disable
```

## Clone with sparse checkout

```bash
git clone --sparse URL
```

## Initialize without checking out files

```bash
git clone --no-checkout URL
```

## Inspect sparse configuration

```bash
git config --get core.sparseCheckout
```

## Inspect cone mode

```bash
git config --get core.sparseCheckoutCone
```

## Inspect sparse patterns

```bash
cat .git/info/sparse-checkout
```

---

# Practical Monorepo Example

Suppose the repository is:

```text
company-platform/
├── services/
│   ├── auth/
│   ├── payments/
│   ├── orders/
│   └── notifications/
├── frontend/
├── mobile/
├── infrastructure/
├── docs/
└── tools/
```

Clone:

```bash
git clone --sparse https://example.com/company/company-platform.git
cd company-platform
```

Select payments:

```bash
git sparse-checkout set --cone services/payments
```

Add infrastructure:

```bash
git sparse-checkout add --cone infrastructure
```

Add documentation:

```bash
git sparse-checkout add --cone docs
```

Check configuration:

```bash
git sparse-checkout list
```

Output:

```text
services/payments
infrastructure
docs
```

The working tree is approximately:

```text
company-platform/
├── services/
│   └── payments/
├── infrastructure/
└── docs/
```

The other repository paths remain outside the populated sparse working tree.

---

# Sparse Checkout Best Practices

## 1. Prefer cone mode

For directory-oriented selection:

```bash
git sparse-checkout set --cone path
```

is usually the simplest approach.

## 2. Use `set` when you want a known final state

For example:

```bash
git sparse-checkout set --cone backend docs
```

makes the desired state explicit.

## 3. Use `add` for incremental expansion

```bash
git sparse-checkout add --cone tools
```

## 4. Use `list` when debugging

```bash
git sparse-checkout list
```

## 5. Do not confuse sparse checkout with history reduction

Sparse checkout controls the working tree.

It does not by itself remove Git history.

## 6. Combine with partial clone when appropriate

For very large repositories:

```bash
git clone --filter=blob:none --sparse URL
```

can be substantially more efficient than a traditional full clone.

## 7. Use sparse checkout carefully in automation

CI jobs should explicitly define which paths they require.

## 8. Reapply rules after unusual working-tree operations

```bash
git sparse-checkout reapply
```

---

# Sparse Checkout Mental Model

Remember:

```text
             Git Repository
                   |
        +----------+----------+
        |                     |
     History              Working Tree
                              |
                       Sparse Checkout
                              |
                 +------------+------------+
                 |                         |
             Included                   Omitted
               paths                     paths
```

The branch still points to a normal commit:

```text
main
 |
 v
A
```

Sparse checkout simply determines which parts of that commit are populated locally.

---

# Final Summary

The core sparse-checkout commands are:

```bash
git sparse-checkout set --cone backend
git sparse-checkout add --cone docs
git sparse-checkout list
git sparse-checkout reapply
git sparse-checkout disable
git clone --sparse URL
```

The most important conceptual distinction is:

```text
Sparse checkout
    =
selective working-tree population
```

not:

```text
Sparse checkout
    =
deleting files from repository history
```

For large repositories and monorepos, sparse checkout can provide a much cleaner development environment while preserving access to the repository's normal Git history and branch structure.

---

# Next Part

**Next file:** `29-git-archives.md`

[Next: Git Archives](29-git-archives.md)
