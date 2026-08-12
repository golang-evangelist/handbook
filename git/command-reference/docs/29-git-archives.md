# 29. Git Archives

Git archives allow you to export a snapshot of files from a Git repository into a distributable archive such as `.tar`, `.tar.gz`, or `.zip`.

Git archives are useful for:

* Creating source-code release packages
* Producing deployment artifacts
* Exporting a specific tag or commit
* Creating source distributions
* Generating CI/CD artifacts
* Providing source code without the `.git` directory
* Packaging a repository for external systems
* Reproducible build workflows
* Creating release bundles

Unlike `git clone`, an archive contains the selected files but does **not** contain the Git repository metadata and history.

---

# Table of Contents

* [29.1 What Is Git Archive?](#291-what-is-git-archive)
* [29.2 Basic git archive Syntax](#292-basic-git-archive-syntax)
* [29.3 Archive the Current HEAD](#293-archive-the-current-head)
* [29.4 Archive a Branch](#294-archive-a-branch)
* [29.5 Archive a Tag](#295-archive-a-tag)
* [29.6 Archive a Commit](#296-archive-a-commit)
* [29.7 Create a TAR Archive](#297-create-a-tar-archive)
* [29.8 Create a TAR.GZ Archive](#298-create-a-targz-archive)
* [29.9 Create a ZIP Archive](#299-create-a-zip-archive)
* [29.10 Specify the Output File](#2910-specify-the-output-file)
* [29.11 Specify an Archive Prefix](#2911-specify-an-archive-prefix)
* [29.12 Archive a Specific Directory](#2912-archive-a-specific-directory)
* [29.13 Archive Specific Files](#2913-archive-specific-files)
* [29.14 Exclude Files from an Archive](#2914-exclude-files-from-an-archive)
* [29.15 Use .gitattributes with Archives](#2915-use-gitattributes-with-archives)
* [29.16 Export Substituted Archive Metadata](#2916-export-substituted-archive-metadata)
* [29.17 Inspect Archive Contents](#2917-inspect-archive-contents)
* [29.18 Extract a TAR Archive](#2918-extract-a-tar-archive)
* [29.19 Extract a TAR.GZ Archive](#2919-extract-a-targz-archive)
* [29.20 Extract a ZIP Archive](#2920-extract-a-zip-archive)
* [29.21 Archive from a Remote Repository](#2921-archive-from-a-remote-repository)
* [29.22 Remote Archive with git archive --remote](#2922-remote-archive-with-git-archiveremote)
* [29.23 Archive a GitHub/GitLab-style Remote](#2923-archive-a-githubgitlab-style-remote)
* [29.24 Archive a Release Tag](#2924-archive-a-release-tag)
* [29.25 Archive for CI/CD](#2925-archive-for-cicd)
* [29.26 Archive for Deployment](#2926-archive-for-deployment)
* [29.27 Archive a Subdirectory](#2927-archive-a-subdirectory)
* [29.28 Archive Multiple Paths](#2928-archive-multiple-paths)
* [29.29 Archive and Standard Output](#2929-archive-and-standard-output)
* [29.30 Archive Format Configuration](#2930-archive-format-configuration)
* [29.31 Archive and Branch State](#2931-archive-and-branch-state)
* [29.32 Archive vs Clone](#2932-archive-vs-clone)
* [29.33 Archive vs Bundle](#2933-archive-vs-bundle)
* [29.34 Archive Troubleshooting](#2934-archive-troubleshooting)
* [29.35 Complete Git Archive Command Reference](#2935-complete-git-archive-command-reference)
* [29.36 High-Value Commands to Memorize](#2936-high-value-commands-to-memorize)

---

# 29.1 What Is Git Archive?

`git archive` creates an archive containing files from a specific Git tree.

Basic example:

```bash
git archive HEAD
```

The command writes the archive to standard output unless an output file is specified.

A common usage is:

```bash
git archive --format=zip --output=source.zip HEAD
```

This produces:

```text
source.zip
```

containing the files represented by `HEAD`.

The archive does **not** contain:

```text
.git/
```

and therefore does not contain the repository's Git history.

Conceptually:

```text
Git repository
    |
    +-- commits
    +-- branches
    +-- tags
    +-- objects
    +-- .git/
    |
    +-- working tree
            |
            +-- git archive
                    |
                    v
               source archive
```

---

# 29.2 Basic git archive Syntax

The general syntax is:

```bash
git archive [options] <tree-ish> [--] [path...]
```

`<tree-ish>` can be:

```text
HEAD
main
feature/login
v1.2.0
abc1234
```

Examples:

```bash
git archive HEAD
```

```bash
git archive main
```

```bash
git archive v1.0.0
```

```bash
git archive abc1234
```

The optional path arguments restrict the archive to specific paths.

---

# 29.3 Archive the Current HEAD

To archive the current commit:

```bash
git archive HEAD
```

This writes a tar archive to standard output.

To save it:

```bash
git archive --output=source.tar HEAD
```

Branch state:

```text
Before:
main -> A

After:
main -> A
```

`git archive` does not create a commit and does not move a branch.

---

# 29.4 Archive a Branch

Archive the current tip of `main`:

```bash
git archive --format=tar --output=main.tar main
```

Archive another branch:

```bash
git archive --format=tar --output=feature.tar feature/login
```

The archive represents the tree associated with the selected branch tip.

The branch itself is not modified.

Example:

```text
main -> A
feature/login -> B
```

Running:

```bash
git archive feature/login
```

archives commit `B`'s tree.

---

# 29.5 Archive a Tag

Tags are especially useful for release archives.

Example:

```bash
git archive --format=tar --output=project-v1.0.0.tar v1.0.0
```

For gzip compression:

```bash
git archive --format=tar.gz --output=project-v1.0.0.tar.gz v1.0.0
```

For ZIP:

```bash
git archive --format=zip --output=project-v1.0.0.zip v1.0.0
```

This is a common release workflow because a tag represents a stable, immutable reference to a specific Git object.

---

# 29.6 Archive a Commit

You can archive any commit:

```bash
git archive --format=tar --output=commit.tar abc1234
```

A full SHA can also be used:

```bash
git archive --format=tar --output=commit.tar 4f8b2f4c...
```

The archive represents the tree associated with that commit.

The current branch does not need to point to the commit.

For example:

```text
main -> C

A -> B -> C
     |
     +--- D
```

You can archive `B` without checking it out:

```bash
git archive B
```

---

# 29.7 Create a TAR Archive

The TAR format is commonly used on Linux and Unix systems.

```bash
git archive --format=tar --output=project.tar HEAD
```

Inspect it:

```bash
tar -tf project.tar
```

Example output:

```text
project/
project/README.md
project/src/
project/src/main.c
project/LICENSE
```

A TAR archive normally provides packaging without compression.

---

# 29.8 Create a TAR.GZ Archive

Create a gzip-compressed TAR archive:

```bash
git archive --format=tar.gz --output=project.tar.gz HEAD
```

Equivalent pipeline:

```bash
git archive HEAD | gzip > project.tar.gz
```

The explicit `--format=tar.gz` form is generally clearer.

Inspect:

```bash
tar -tzf project.tar.gz
```

Extract:

```bash
tar -xzf project.tar.gz
```

---

# 29.9 Create a ZIP Archive

Create a ZIP archive:

```bash
git archive --format=zip --output=project.zip HEAD
```

Inspect:

```bash
unzip -l project.zip
```

Extract:

```bash
unzip project.zip
```

ZIP archives are useful when artifacts need to be consumed by:

* Windows users
* CI systems
* Release platforms
* Build systems
* Systems where ZIP is the expected distribution format

---

# 29.10 Specify the Output File

Use `--output`:

```bash
git archive --output=project.tar HEAD
```

Short form:

```bash
git archive -o project.tar HEAD
```

ZIP:

```bash
git archive -o project.zip --format=zip HEAD
```

TAR.GZ:

```bash
git archive -o project.tar.gz --format=tar.gz HEAD
```

The output file is created independently of the current branch state.

---

# 29.11 Specify an Archive Prefix

Use `--prefix` to place all archived files under a directory.

Example:

```bash
git archive \
  --format=tar.gz \
  --prefix=project-1.0.0/ \
  --output=project-1.0.0.tar.gz \
  v1.0.0
```

Without a prefix, archive entries might look like:

```text
README.md
src/
src/main.c
```

With:

```text
--prefix=project-1.0.0/
```

they become:

```text
project-1.0.0/README.md
project-1.0.0/src/
project-1.0.0/src/main.c
```

This is highly recommended for source release archives.

---

# 29.12 Archive a Specific Directory

You can archive only a specific path.

Example:

```bash
git archive --format=tar.gz --output=backend.tar.gz HEAD backend/
```

This produces an archive containing the selected `backend/` tree.

Another example:

```bash
git archive --format=zip --output=docs.zip HEAD docs/
```

The branch remains unchanged.

---

# 29.13 Archive Specific Files

You can specify individual files.

Example:

```bash
git archive \
  --format=zip \
  --output=release-files.zip \
  HEAD \
  README.md \
  LICENSE
```

The archive contains only:

```text
README.md
LICENSE
```

This is useful for generating small distribution artifacts.

---

# 29.14 Exclude Files from an Archive

`git archive` does not provide a general-purpose `--exclude` option equivalent to some filesystem archiving tools.

Instead, use `.gitattributes` with the `export-ignore` attribute.

For example:

```gitattributes
.gitignore export-ignore
.gitattributes export-ignore
.github/ export-ignore
tests/ export-ignore
```

Then:

```bash
git archive --format=tar.gz --output=release.tar.gz HEAD
```

will omit paths marked with `export-ignore`.

This is the preferred Git-native mechanism for controlling release archives.

---

# 29.15 Use .gitattributes with Archives

A `.gitattributes` file can define:

```gitattributes
path/to/file export-ignore
```

For example:

```gitattributes
.gitignore export-ignore
.gitattributes export-ignore
.github export-ignore
tests export-ignore
docs/internal export-ignore
```

Then:

```bash
git archive --format=tar.gz --output=release.tar.gz v1.0.0
```

will respect these archive-specific attributes.

This is particularly useful for:

* Source releases
* Public distributions
* Package creation
* Deployment artifacts

---

# 29.16 Export Substituted Archive Metadata

Git supports the `export-subst` attribute.

Example:

```gitattributes
README.md export-subst
```

When archiving, Git can substitute certain `$Format:` placeholders in the file.

For example:

```text
Version: $Format:%h$
Commit: $Format:%H$
Date: $Format:%ci$
```

With:

```gitattributes
README.md export-subst
```

an archive can contain values corresponding to the archived commit.

This can be useful for embedding version information in generated source distributions.

---

# 29.17 Inspect Archive Contents

For TAR:

```bash
tar -tf project.tar
```

For TAR.GZ:

```bash
tar -tzf project.tar.gz
```

For ZIP:

```bash
unzip -l project.zip
```

You can also use:

```bash
file project.tar.gz
```

Example:

```text
project.tar.gz: gzip compressed data
```

---

# 29.18 Extract a TAR Archive

Extract:

```bash
tar -xf project.tar
```

Extract into a specific directory:

```bash
mkdir extracted
tar -xf project.tar -C extracted
```

List contents without extraction:

```bash
tar -tf project.tar
```

---

# 29.19 Extract a TAR.GZ Archive

Extract:

```bash
tar -xzf project.tar.gz
```

Extract to a directory:

```bash
mkdir extracted
tar -xzf project.tar.gz -C extracted
```

List:

```bash
tar -tzf project.tar.gz
```

---

# 29.20 Extract a ZIP Archive

Extract:

```bash
unzip project.zip
```

Extract into a directory:

```bash
unzip project.zip -d extracted
```

List contents:

```bash
unzip -l project.zip
```

---

# 29.21 Archive from a Remote Repository

A common approach is to clone or fetch the repository locally and then run:

```bash
git archive
```

For example:

```bash
git clone https://example.com/company/project.git
cd project
git archive --format=tar.gz --output=project.tar.gz v1.0.0
```

This gives you a local Git object database from which the archive can be generated.

However, Git also supports remote archive mechanisms for certain server configurations.

---

# 29.22 Remote Archive with git archive --remote

The syntax is:

```bash
git archive --remote=<repository> <tree-ish>
```

Example:

```bash
git archive --remote=ssh://git@example.com/project.git HEAD
```

Specify an output file:

```bash
git archive \
  --remote=ssh://git@example.com/project.git \
  --format=tar \
  --output=project.tar \
  HEAD
```

Remote archive support depends on the remote Git server's configuration.

In particular, the server-side `git-upload-archive` service must be available.

Not every hosting provider or Git transport supports arbitrary remote archive requests.

---

# 29.23 Archive a GitHub/GitLab-style Remote

Many Git hosting platforms expose their own archive/download mechanisms.

For a local Git workflow, the portable approach is:

```bash
git clone URL
cd repository
git archive --format=tar.gz --output=source.tar.gz TAG
```

For automation, use the hosting platform's documented release/archive API when appropriate.

Do not assume that:

```bash
git archive --remote=HTTPS_URL
```

will work against arbitrary hosting services.

Remote archive support is server-dependent.

---

# 29.24 Archive a Release Tag

A recommended release workflow is:

```bash
git switch main
git pull --ff-only
git tag v1.2.0
git archive \
  --format=tar.gz \
  --prefix=project-1.2.0/ \
  --output=project-1.2.0.tar.gz \
  v1.2.0
```

The resulting archive:

```text
project-1.2.0.tar.gz
```

contains:

```text
project-1.2.0/
├── README.md
├── LICENSE
├── src/
└── ...
```

This produces a clean source distribution without:

```text
.git/
.git/objects/
.git/refs/
```

---

# 29.25 Archive for CI/CD

CI/CD systems can generate archives directly from Git references.

Example:

```bash
git archive \
  --format=tar.gz \
  --prefix="${CI_PROJECT_NAME}-${CI_COMMIT_TAG}/" \
  --output="${CI_PROJECT_NAME}-${CI_COMMIT_TAG}.tar.gz" \
  "$CI_COMMIT_TAG"
```

A generic CI workflow might be:

```bash
git fetch --tags
git archive \
  --format=tar.gz \
  --prefix=release/ \
  --output=release.tar.gz \
  "$RELEASE_REF"
```

The artifact can then be:

* Uploaded to an artifact repository
* Published as a release asset
* Passed to another pipeline stage
* Used as a deployment package

---

# 29.26 Archive for Deployment

Suppose an application must be deployed without its `.git` directory.

Create:

```bash
git archive \
  --format=tar.gz \
  --prefix=application/ \
  --output=application.tar.gz \
  HEAD
```

Transfer:

```bash
scp application.tar.gz server:/tmp/
```

Then extract on the deployment system:

```bash
tar -xzf application.tar.gz
```

This is often preferable to deploying the entire Git working directory when Git metadata is unnecessary.

---

# 29.27 Archive a Subdirectory

For a monorepo:

```text
repository/
├── services/
│   ├── payments/
│   ├── users/
│   └── orders/
├── frontend/
└── infrastructure/
```

Archive only payments:

```bash
git archive \
  --format=tar.gz \
  --prefix=payments/ \
  --output=payments.tar.gz \
  HEAD \
  services/payments/
```

The resulting archive contains the selected subtree.

Depending on the desired output structure, choose the prefix carefully.

---

# 29.28 Archive Multiple Paths

You can specify multiple paths:

```bash
git archive \
  --format=tar.gz \
  --output=release.tar.gz \
  HEAD \
  README.md \
  LICENSE \
  src/ \
  config/
```

The archive contains:

```text
README.md
LICENSE
src/
config/
```

This is useful when a release requires a carefully selected set of repository paths.

---

# 29.29 Archive and Standard Output

Without `--output`, `git archive` writes the archive to standard output.

For example:

```bash
git archive HEAD
```

The output is binary data and should normally be redirected or piped.

Example:

```bash
git archive HEAD > source.tar
```

Compressed:

```bash
git archive HEAD | gzip > source.tar.gz
```

This allows Git archives to participate in Unix pipelines.

For example:

```bash
git archive HEAD | gzip | sha256sum
```

This calculates a checksum of the generated compressed stream.

---

# 29.30 Archive Format Configuration

Supported formats depend on the Git build and environment, but common formats include:

```text
tar
tar.gz
tgz
zip
```

Specify explicitly:

```bash
git archive --format=tar HEAD
```

```bash
git archive --format=tar.gz HEAD
```

```bash
git archive --format=zip HEAD
```

To see available archive formats:

```bash
git archive --list
```

Depending on the Git version, this may show:

```text
tar
tgz
tar.gz
zip
```

---

# 29.31 Archive and Branch State

`git archive` does not modify branch state.

Example:

```text
Before:

main
 |
 v
A
```

Run:

```bash
git archive --format=tar --output=main.tar HEAD
```

After:

```text
main
 |
 v
A
```

No new commit is created.

No branch moves.

No working-tree changes are required.

You can archive another reference without checking it out:

```bash
git archive v1.0.0
```

This is one of the most useful properties of `git archive`.

---

# 29.32 Archive vs Clone

## `git clone`

Creates a Git repository:

```text
project/
├── .git/
├── README.md
├── src/
└── ...
```

It provides:

* Git history
* Branches
* Tags
* Remote configuration
* Git objects
* Working tree

## `git archive`

Creates a source snapshot:

```text
project.tar.gz
```

It provides:

* Files
* Directory structure
* Selected snapshot
* No `.git` directory
* No Git history

Conceptually:

```text
clone
    =
repository + history + working tree

archive
    =
selected source snapshot
```

---

# 29.33 Archive vs Bundle

Git bundle is different from Git archive.

## Git archive

```bash
git archive --format=tar.gz --output=source.tar.gz HEAD
```

Purpose:

```text
Distribute source files
```

## Git bundle

```bash
git bundle create repository.bundle --all
```

Purpose:

```text
Transport Git objects and history
```

A bundle can be used as a Git repository transfer mechanism.

An archive cannot normally be used as a Git repository.

---

# 29.34 Archive Troubleshooting

## Problem: Archive contains unwanted files

Check `.gitattributes`:

```bash
cat .gitattributes
```

Use:

```gitattributes
path/to/file export-ignore
```

Then regenerate the archive.

---

## Problem: `.git` directory is missing

This is expected.

`git archive` intentionally does not include the repository's `.git` metadata.

---

## Problem: Archive is empty or contains unexpected content

Check the reference:

```bash
git rev-parse HEAD
```

Check the target:

```bash
git show --stat HEAD
```

For a tag:

```bash
git rev-parse v1.0.0
```

Then regenerate the archive.

---

## Problem: Tag does not exist

Check:

```bash
git tag --list
```

or:

```bash
git show-ref --tags
```

Fetch tags:

```bash
git fetch --tags
```

Then:

```bash
git archive v1.0.0
```

---

## Problem: Remote archive does not work

Check whether the remote server supports `git-upload-archive`.

Instead, clone/fetch the repository and archive locally:

```bash
git clone URL
cd repository
git archive --format=tar.gz --output=source.tar.gz TAG
```

---

## Problem: Archive prefix is wrong

Check:

```bash
git archive \
  --format=tar.gz \
  --prefix=project-1.0.0/ \
  --output=project.tar.gz \
  v1.0.0
```

The prefix should normally end with `/`.

---

# 29.35 Complete Git Archive Command Reference

| Command                                    | Description                        | Example                                                    | Branch State Before and After command            | Output                          |
| ------------------------------------------ | ---------------------------------- | ---------------------------------------------------------- | ------------------------------------------------ | ------------------------------- |
| `git archive HEAD`                         | Archive the current `HEAD`         | `git archive HEAD`                                         | Branch unchanged                                 | TAR data on stdout              |
| `git archive main`                         | Archive the tip of `main`          | `git archive main`                                         | Current branch unchanged                         | TAR data on stdout              |
| `git archive v1.0.0`                       | Archive a tag                      | `git archive v1.0.0`                                       | Branch unchanged                                 | TAR data on stdout              |
| `git archive abc1234`                      | Archive a commit                   | `git archive abc1234`                                      | Branch unchanged                                 | TAR data on stdout              |
| `git archive -o file.tar HEAD`             | Save archive to a file             | `git archive -o source.tar HEAD`                           | Branch unchanged                                 | `source.tar`                    |
| `git archive --output=file.tar HEAD`       | Save archive to a file             | `git archive --output=source.tar HEAD`                     | Branch unchanged                                 | `source.tar`                    |
| `git archive --format=tar HEAD`            | Explicitly create TAR              | `git archive --format=tar HEAD`                            | Branch unchanged                                 | TAR stream                      |
| `git archive --format=tar.gz HEAD`         | Create gzip-compressed TAR         | `git archive --format=tar.gz HEAD`                         | Branch unchanged                                 | TAR.GZ stream                   |
| `git archive --format=zip HEAD`            | Create ZIP archive                 | `git archive --format=zip HEAD`                            | Branch unchanged                                 | ZIP stream                      |
| `git archive --prefix=name/ HEAD`          | Add directory prefix               | `git archive --prefix=project/ HEAD`                       | Branch unchanged                                 | Prefixed archive                |
| `git archive HEAD path/`                   | Archive one path                   | `git archive HEAD src/`                                    | Branch unchanged                                 | Selected path archive           |
| `git archive HEAD file`                    | Archive one file                   | `git archive HEAD README.md`                               | Branch unchanged                                 | Selected file archive           |
| `git archive HEAD file1 file2`             | Archive multiple paths             | `git archive HEAD README.md LICENSE`                       | Branch unchanged                                 | Selected paths                  |
| `git archive --list`                       | List supported formats             | `git archive --list`                                       | Branch unchanged                                 | Format list                     |
| `git archive --remote=URL HEAD`            | Request remote archive             | `git archive --remote=ssh://git@example.com/repo.git HEAD` | Branch unchanged                                 | Remote archive stream           |
| `git archive HEAD > source.tar`            | Redirect archive to file           | `git archive HEAD > source.tar`                            | Branch unchanged                                 | `source.tar`                    |
| `git archive HEAD \| gzip > source.tar.gz` | Compress archive through gzip      | `git archive HEAD \| gzip > source.tar.gz`                 | Branch unchanged                                 | `source.tar.gz`                 |
| `tar -tf source.tar`                       | List TAR contents                  | `tar -tf source.tar`                                       | Git branch unchanged                             | File listing                    |
| `tar -tzf source.tar.gz`                   | List TAR.GZ contents               | `tar -tzf source.tar.gz`                                   | Git branch unchanged                             | File listing                    |
| `tar -xf source.tar`                       | Extract TAR                        | `tar -xf source.tar`                                       | Git branch unchanged                             | Extracted files                 |
| `tar -xzf source.tar.gz`                   | Extract TAR.GZ                     | `tar -xzf source.tar.gz`                                   | Git branch unchanged                             | Extracted files                 |
| `unzip -l source.zip`                      | List ZIP contents                  | `unzip -l source.zip`                                      | Git branch unchanged                             | File listing                    |
| `unzip source.zip`                         | Extract ZIP                        | `unzip source.zip`                                         | Git branch unchanged                             | Extracted files                 |
| `git config --get tar.<format>.command`    | Inspect configured archive command | `git config --get tar.<format>.command`                    | Branch unchanged                                 | Configured command or no output |
| `cat .gitattributes`                       | Inspect archive-related attributes | `cat .gitattributes`                                       | Branch unchanged                                 | Attribute rules                 |
| `git fetch --tags`                         | Fetch remote tags before archiving | `git fetch --tags`                                         | Current branch unchanged; remote refs may update | Fetch output                    |
| `git rev-parse TAG`                        | Resolve a tag to an object ID      | `git rev-parse v1.0.0`                                     | Branch unchanged                                 | Object ID                       |
| `git show --stat TAG`                      | Inspect target before archiving    | `git show --stat v1.0.0`                                   | Branch unchanged                                 | Commit statistics               |

---

# 29.36 High-Value Commands to Memorize

## Archive current HEAD

```bash
git archive --format=tar.gz --output=source.tar.gz HEAD
```

## Archive a release tag

```bash
git archive \
  --format=tar.gz \
  --prefix=project-1.0.0/ \
  --output=project-1.0.0.tar.gz \
  v1.0.0
```

## Create ZIP

```bash
git archive \
  --format=zip \
  --output=project.zip \
  HEAD
```

## Archive a directory

```bash
git archive \
  --format=tar.gz \
  --output=backend.tar.gz \
  HEAD \
  backend/
```

## Archive selected files

```bash
git archive \
  --format=zip \
  --output=files.zip \
  HEAD \
  README.md \
  LICENSE
```

## Archive with a release directory prefix

```bash
git archive \
  --format=tar.gz \
  --prefix=project-1.2.0/ \
  --output=project-1.2.0.tar.gz \
  v1.2.0
```

## Inspect TAR

```bash
tar -tf source.tar
```

## Inspect TAR.GZ

```bash
tar -tzf source.tar.gz
```

## Inspect ZIP

```bash
unzip -l source.zip
```

## Extract TAR.GZ

```bash
tar -xzf source.tar.gz
```

## Extract ZIP

```bash
unzip source.zip
```

## Exclude files from release archives

Add to `.gitattributes`:

```gitattributes
.gitignore export-ignore
.gitattributes export-ignore
.github/ export-ignore
tests/ export-ignore
```

Then:

```bash
git archive --format=tar.gz --output=release.tar.gz HEAD
```

---

# Practical Release Example

A typical release process can be:

```bash
git switch main
git pull --ff-only
git tag v1.0.0
git push origin v1.0.0
```

Generate the source archive:

```bash
git archive \
  --format=tar.gz \
  --prefix=my-project-1.0.0/ \
  --output=my-project-1.0.0.tar.gz \
  v1.0.0
```

Verify:

```bash
tar -tzf my-project-1.0.0.tar.gz
```

Calculate a checksum:

```bash
sha256sum my-project-1.0.0.tar.gz
```

The final artifact can then be uploaded to a release system or artifact repository.

---

# Git Archive in DevOps

A typical CI/CD sequence is:

```text
Git repository
      |
      v
Git tag / commit
      |
      v
git archive
      |
      v
source.tar.gz
      |
      +----> artifact repository
      |
      +----> release asset
      |
      +----> deployment system
      |
      +----> downstream pipeline
```

Advantages include:

* No `.git` directory
* No repository history
* Deterministic Git reference
* Easy distribution
* Simple deployment packaging
* Native integration with `.gitattributes`
* Easy integration into shell scripts and CI systems

---

# Important Distinction

Use:

```bash
git archive
```

when you need:

```text
Source snapshot
```

Use:

```bash
git clone
```

when you need:

```text
A Git repository
```

Use:

```bash
git bundle
```

when you need:

```text
Git objects and history transported as a file
```

A useful mental model is:

```text
git archive
    -> files

git clone
    -> repository + history

git bundle
    -> Git objects + history
```

---

# Final Summary

The most important `git archive` concepts are:

```text
git archive
    |
    +-- archive HEAD
    +-- archive branch
    +-- archive tag
    +-- archive commit
    +-- archive selected paths
    +-- add archive prefix
    +-- create TAR
    +-- create TAR.GZ
    +-- create ZIP
```

For source releases, the most useful pattern is:

```bash
git archive \
  --format=tar.gz \
  --prefix=project-version/ \
  --output=project-version.tar.gz \
  TAG
```

For excluding repository-development files, use:

```gitattributes
path export-ignore
```

inside `.gitattributes`.

For deployment and CI/CD, `git archive` provides a clean source snapshot without exposing the Git repository metadata.

---

# Next Part

**Next file:** `30-environment-and-automation.md`

[Next: Environment & Automation](30-environment-and-automation.md)
