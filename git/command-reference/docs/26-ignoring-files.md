# 26. Ignoring Files

Git's ignore mechanism controls which files Git should **not normally report as untracked**.

Ignoring files is essential for:

* Build artifacts
* Dependencies
* IDE metadata
* Operating-system files
* Logs
* Temporary files
* Local configuration
* Generated files
* Secrets that should never be committed
* Developer-specific files

The primary mechanism is the `.gitignore` file.

> **Important:** `.gitignore` does not remove files that are already tracked by Git. If a file is already tracked, you must remove it from the index separately.

---

# Table of Contents

* [26.1 What `.gitignore` Does](#261-what-gitignore-does)
* [26.2 Basic `.gitignore`](#262-basic-gitignore)
* [26.3 Ignore a Specific File](#263-ignore-a-specific-file)
* [26.4 Ignore a Directory](#264-ignore-a-directory)
* [26.5 Ignore File Extensions](#265-ignore-file-extensions)
* [26.6 Wildcards](#266-wildcards)
* [26.7 Directory Patterns](#267-directory-patterns)
* [26.8 Root-Relative Patterns](#268-root-relative-patterns)
* [26.9 Negation Patterns](#269-negation-patterns)
* [26.10 Comments](#2610-comments)
* [26.11 Multiple `.gitignore` Files](#2611-multiple-gitignore-files)
* [26.12 Global Gitignore](#2612-global-gitignore)
* [26.13 Repository-Local Exclude Rules](#2613-repository-local-exclude-rules)
* [26.14 `.git/info/exclude`](#2614-gitinfoexclude)
* [26.15 `core.excludesFile`](#2615-coreexcludesfile)
* [26.16 `git check-ignore`](#2616-git-check-ignore)
* [26.17 Why a File Is Ignored](#2617-why-a-file-is-ignored)
* [26.18 Check Ignored Files](#2618-check-ignored-files)
* [26.19 Ignored vs Tracked Files](#2619-ignored-vs-tracked-files)
* [26.20 Stop Tracking an Ignored File](#2620-stop-tracking-an-ignored-file)
* [26.21 Remove Tracked Files While Keeping Them Locally](#2621-remove-tracked-files-while-keeping-them-locally)
* [26.22 Force Add an Ignored File](#2622-force-add-an-ignored-file)
* [26.23 Common Language-Specific Patterns](#2623-common-language-specific-patterns)
* [26.24 IDE Files](#2624-ide-files)
* [26.25 Operating-System Files](#2625-operating-system-files)
* [26.26 Build Artifacts](#2626-build-artifacts)
* [26.27 Logs and Temporary Files](#2627-logs-and-temporary-files)
* [26.28 Environment and Secret Files](#2628-environment-and-secret-files)
* [26.29 Generated Files](#2629-generated-files)
* [26.30 Gitignore Templates](#2630-gitignore-templates)
* [26.31 Common Gitignore Mistakes](#2631-common-gitignore-mistakes)
* [26.32 Gitignore and Git Status](#2632-gitignore-and-git-status)
* [26.33 Gitignore and Git Add](#2633-gitignore-and-git-add)
* [26.34 Gitignore and Git Clean](#2634-gitignore-and-git-clean)
* [26.35 Gitignore Commands Reference](#2635-gitignore-commands-reference)
* [26.36 High-Value Patterns to Memorize](#2636-high-value-patterns-to-memorize)

---

# 26.1 What `.gitignore` Does

A `.gitignore` file contains patterns describing files that Git should ignore.

Example:

```gitignore
node_modules/
dist/
.env
*.log
```

Suppose the working tree contains:

```text
project/
├── .git/
├── .gitignore
├── src/
├── node_modules/
├── dist/
├── .env
└── application.log
```

Git can ignore:

```text
node_modules/
dist/
.env
application.log
```

while continuing to track:

```text
.gitignore
src/
```

Check status:

```bash
git status
```

The ignored files normally do not appear under "Untracked files".

---

# 26.2 Basic `.gitignore`

Create a `.gitignore` file:

```bash
touch .gitignore
```

Example:

```gitignore
node_modules/
dist/
*.log
.env
```

Then:

```bash
git status
```

Ignored files should no longer appear as ordinary untracked files.

A `.gitignore` file itself is normally committed:

```bash
git add .gitignore
git commit -m "Add Git ignore rules"
```

---

# 26.3 Ignore a Specific File

To ignore a specific file:

```gitignore
config.local.json
```

This pattern can match that filename according to the `.gitignore` pattern rules.

Example:

```text
config.local.json
```

Command:

```bash
git status --short
```

Before `.gitignore`:

```text
?? config.local.json
```

After adding the rule:

```text
```

The file is ignored.

---

# 26.4 Ignore a Directory

Use a trailing `/`:

```gitignore
node_modules/
```

This indicates a directory pattern.

Common examples:

```gitignore
dist/
build/
coverage/
.cache/
.tmp/
```

Example:

```text
project/
├── src/
├── build/
│   ├── app.js
│   └── app.css
└── .gitignore
```

With:

```gitignore
build/
```

the entire `build/` directory is ignored.

---

# 26.5 Ignore File Extensions

Use `*`:

```gitignore
*.log
```

This ignores files ending in `.log`.

Examples:

```text
application.log
debug.log
server.log
```

Other examples:

```gitignore
*.tmp
*.cache
*.bak
*.swp
```

Multiple extensions:

```gitignore
*.log
*.tmp
*.bak
```

---

# 26.6 Wildcards

Gitignore patterns support wildcard matching.

Common patterns:

| Pattern | Meaning                                                         |
| ------- | --------------------------------------------------------------- |
| `*`     | Matches many characters except `/` in pattern matching contexts |
| `?`     | Matches one character                                           |
| `[...]` | Character class                                                 |
| `**`    | Matches across directory levels                                 |
| `/`     | Directory/path separator                                        |
| `!`     | Negates an ignore pattern                                       |

Examples:

```gitignore
*.log
```

```gitignore
temp?.txt
```

```gitignore
**/cache/
```

```gitignore
*.local.*
```

---

# 26.7 Directory Patterns

To ignore directories named `cache`:

```gitignore
cache/
```

To ignore directories named `cache` anywhere below the relevant `.gitignore` scope:

```gitignore
**/cache/
```

Examples:

```text
src/cache/
test/cache/
tools/cache/
```

A common pattern is:

```gitignore
**/node_modules/
```

However, in many repositories simply using:

```gitignore
node_modules/
```

is sufficient and clearer.

---

# 26.8 Root-Relative Patterns

A leading `/` anchors the pattern relative to the `.gitignore` file's directory.

For example:

```gitignore
/build/
```

This can target a `build` directory at the root of that `.gitignore` scope.

Compare:

```gitignore
build/
```

with:

```gitignore
/build/
```

The second form is explicitly anchored to the directory containing that `.gitignore`.

Example repository:

```text
project/
├── build/
├── src/
│   └── build/
└── .gitignore
```

With:

```gitignore
/build/
```

the root-level `build/` directory is targeted.

---

# 26.9 Negation Patterns

A pattern beginning with `!` can re-include a path that was previously ignored.

Example:

```gitignore
*.log
!important.log
```

This means:

```text
*.log
```

is ignored, except:

```text
important.log
```

Another example:

```gitignore
*.json
!config.example.json
```

This ignores JSON files while allowing `config.example.json`.

> **Important:** Negation rules cannot always re-include a file if one of its parent directories is itself excluded. The parent directory must be traversable.

---

# 26.10 Comments

Lines beginning with `#` are comments.

Example:

```gitignore
# Dependencies
node_modules/

# Build output
dist/
build/

# Environment configuration
.env
```

Comments are useful for keeping large `.gitignore` files understandable.

---

# 26.11 Multiple `.gitignore` Files

A repository can contain multiple `.gitignore` files.

Example:

```text
project/
├── .gitignore
├── src/
│   └── .gitignore
└── tests/
    └── .gitignore
```

Rules can therefore be scoped to different directories.

For example:

```text
src/.gitignore
```

could contain:

```gitignore
generated/
```

This applies within that directory's scope.

---

# 26.12 Global Gitignore

Git can use a global ignore file for files that should generally be ignored across repositories.

Typical examples:

```text
.DS_Store
Thumbs.db
.idea/
.vscode/
```

Configure a global ignore file:

```bash
git config --global core.excludesFile ~/.config/git/ignore
```

Create it:

```bash
mkdir -p ~/.config/git
touch ~/.config/git/ignore
```

Edit it:

```bash
nano ~/.config/git/ignore
```

Example:

```gitignore
.DS_Store
Thumbs.db
*.swp
```

Check configuration:

```bash
git config --global --get core.excludesFile
```

---

# 26.13 Repository-Local Exclude Rules

Git provides repository-local ignore rules that do not need to be committed.

These are useful for personal development files.

The main file is:

```text
.git/info/exclude
```

Example:

```gitignore
my-local-notes/
local-config.json
```

These rules affect only your local repository.

Unlike `.gitignore`, they are not normally shared through commits.

---

# 26.14 `.git/info/exclude`

Inspect:

```bash
cat .git/info/exclude
```

Edit:

```bash
nano .git/info/exclude
```

Example:

```gitignore
# Local-only files
local.env
notes/
debug-output/
```

This is particularly useful when:

* You do not want to modify the repository's `.gitignore`
* The ignored files are personal
* The pattern is specific to your local environment

---

# 26.15 `core.excludesFile`

Inspect:

```bash
git config --get core.excludesFile
```

Global value:

```bash
git config --global --get core.excludesFile
```

Set it:

```bash
git config --global core.excludesFile ~/.config/git/ignore
```

Remove it:

```bash
git config --global --unset core.excludesFile
```

This configuration points Git to a global ignore file.

---

# 26.16 `git check-ignore`

`git check-ignore` determines whether a path is ignored.

Example:

```bash
git check-ignore build/app.js
```

If ignored, output can be:

```text
build/app.js
```

If you want to see the rule responsible:

```bash
git check-ignore -v build/app.js
```

Example:

```text
.gitignore:5:build/    build/app.js
```

This is one of the most important debugging commands for `.gitignore`.

---

# 26.17 Why a File Is Ignored

Use:

```bash
git check-ignore -v -- path/to/file
```

Example:

```bash
git check-ignore -v -- .env
```

Possible output:

```text
.gitignore:12:.env    .env
```

This tells you:

```text
.gitignore
   |
   +-- line 12
   |
   +-- pattern: .env
   |
   +-- matched file: .env
```

For debugging ignore rules, prefer:

```bash
git check-ignore -v
```

over guessing.

---

# 26.18 Check Ignored Files

Show ignored files with status:

```bash
git status --ignored
```

Short form:

```bash
git status --short --ignored
```

Example:

```text
!! node_modules/
!! .env
!! dist/
```

The `!!` status indicates an ignored path.

---

# 26.19 Ignored vs Tracked Files

This distinction is critical.

Suppose:

```text
config.local.json
```

is already tracked.

Then adding:

```gitignore
config.local.json
```

does **not** stop Git from tracking it.

Check:

```bash
git ls-files -- config.local.json
```

If it returns:

```text
config.local.json
```

the file is tracked.

`.gitignore` primarily affects **untracked** files.

---

# 26.20 Stop Tracking an Ignored File

Suppose:

```text
.env
```

was accidentally committed.

Add:

```gitignore
.env
```

Then remove it from the index while keeping the local file:

```bash
git rm --cached .env
```

Check:

```bash
git status
```

The file should now appear as deleted from the index but remain physically present.

Commit:

```bash
git commit -m "Stop tracking local environment file"
```

After that, `.env` remains ignored locally.

---

# 26.21 Remove Tracked Files While Keeping Them Locally

For a directory:

```bash
git rm -r --cached build/
```

For multiple files:

```bash
git rm --cached file1 file2 file3
```

Then:

```bash
git status
```

Commit the change:

```bash
git commit -m "Stop tracking generated files"
```

The files remain in the working tree but are no longer tracked.

---

# 26.22 Force Add an Ignored File

Sometimes you intentionally need to commit a file that is normally ignored.

Use:

```bash
git add -f file.txt
```

Example:

```bash
git add -f generated/example.txt
```

Check:

```bash
git status
```

The file can now be staged despite matching an ignore rule.

Use this sparingly.

---

# 26.23 Common Language-Specific Patterns

## Node.js

```gitignore
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*
dist/
coverage/
.env
```

## Python

```gitignore
__pycache__/
*.py[cod]
.venv/
venv/
.env
.pytest_cache/
.mypy_cache/
dist/
build/
*.egg-info/
```

## Java

```gitignore
target/
*.class
*.jar
.idea/
*.iml
```

## Go

```gitignore
bin/
*.test
coverage.out
```

## Rust

```gitignore
target/
Cargo.lock
```

> For Rust applications, `Cargo.lock` is normally committed. Libraries often follow different conventions.

## C/C++

```gitignore
build/
CMakeFiles/
CMakeCache.txt
*.o
*.obj
*.so
*.dll
*.exe
```

## .NET

```gitignore
bin/
obj/
.vs/
*.user
*.suo
```

---

# 26.24 IDE Files

Common IDE-specific files include:

```gitignore
.idea/
.vscode/
*.iml
*.ipr
*.iws
```

However, some projects intentionally commit selected IDE configuration.

For example:

```text
.vscode/
```

may contain useful shared configuration such as:

```text
.vscode/
├── settings.json
├── tasks.json
└── launch.json
```

Do not automatically ignore every IDE directory without considering the project's requirements.

A more selective approach may be:

```gitignore
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
```

---

# 26.25 Operating-System Files

Common macOS files:

```gitignore
.DS_Store
.AppleDouble
.LSOverride
```

Common Windows files:

```gitignore
Thumbs.db
Desktop.ini
```

Linux/editor temporary files:

```gitignore
*~
*.swp
*.swo
```

Example global ignore file:

```gitignore
# macOS
.DS_Store

# Windows
Thumbs.db
Desktop.ini

# Editors
*.swp
*.swo
*~
```

---

# 26.26 Build Artifacts

Build output is commonly ignored.

Examples:

```gitignore
build/
dist/
out/
target/
bin/
obj/
```

Example:

```text
project/
├── src/
├── build/
├── dist/
└── .gitignore
```

`.gitignore`:

```gitignore
/build/
/dist/
```

This prevents generated artifacts from appearing as untracked files.

---

# 26.27 Logs and Temporary Files

Typical rules:

```gitignore
*.log
*.tmp
*.temp
*.cache
*.bak
```

Application-specific:

```gitignore
logs/
tmp/
cache/
```

Example:

```gitignore
# Logs
*.log
logs/

# Temporary files
tmp/
*.tmp

# Cache
.cache/
*.cache
```

---

# 26.28 Environment and Secret Files

Common local environment files:

```gitignore
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

A safer approach is often:

```gitignore
.env
.env.local
```

while committing:

```text
.env.example
```

Example:

```gitignore
.env
.env.local
.env.*.local
```

Then:

```text
.env.example
```

can document required configuration without containing real secrets.

> **Important:** `.gitignore` is not a security mechanism for secrets that have already been committed. If a secret has entered Git history, removing the current file is not sufficient; the history may still contain the secret.

---

# 26.29 Generated Files

Generated source or documentation can be ignored if it is reproducible.

Examples:

```gitignore
generated/
coverage/
docs/generated/
openapi/generated/
```

Before ignoring generated files, determine whether the project intentionally commits them.

Some projects commit generated artifacts because:

* Consumers do not have the generator
* Builds depend on committed generated files
* Releases require generated files
* Generated output is part of distribution

---

# 26.30 Gitignore Templates

A `.gitignore` should reflect the actual repository.

A generic example:

```gitignore
# Dependencies
node_modules/

# Build output
dist/
build/

# Coverage
coverage/

# Environment
.env
.env.local

# Logs
*.log

# IDE
.idea/
.vscode/

# OS
.DS_Store
Thumbs.db

# Temporary files
*.tmp
*.swp
```

Avoid blindly copying very large templates when only a few rules are necessary.

A smaller, intentional `.gitignore` is easier to maintain.

---

# 26.31 Common Gitignore Mistakes

## Mistake 1: Expecting `.gitignore` to untrack files

Incorrect assumption:

```text
Adding a filename to .gitignore removes it from Git.
```

It does not.

Use:

```bash
git rm --cached file
```

---

## Mistake 2: Ignoring secrets after they were committed

Adding:

```gitignore
.env
```

does not remove `.env` from historical commits.

If a secret was committed, rotate/revoke it and then handle history separately.

---

## Mistake 3: Incorrect directory pattern

Instead of:

```gitignore
build
```

you may prefer:

```gitignore
build/
```

when your intent is specifically a directory.

---

## Mistake 4: Negating a parent directory incorrectly

Example:

```gitignore
build/
!build/important.txt
```

The negation may not work as intended because `build/` itself is excluded.

The parent directory must be traversable for Git to re-include a file beneath it.

---

## Mistake 5: Ignoring files that should be shared

Global ignores should generally be used for personal/environment-specific files, not project requirements.

---

# 26.32 Gitignore and Git Status

Normal status:

```bash
git status
```

Ignored files are normally hidden.

Show ignored files:

```bash
git status --ignored
```

Short format:

```bash
git status --short --ignored
```

Example:

```text
 M src/main.c
?? README.md
!! build/
!! .env
```

Interpretation:

```text
 M   = modified tracked file
??   = untracked file
!!   = ignored file
```

---

# 26.33 Gitignore and Git Add

Normally:

```bash
git add .
```

does not stage ignored files.

Example:

```gitignore
*.log
```

Then:

```bash
git add .
```

does not stage:

```text
debug.log
```

Check:

```bash
git status
```

To intentionally add it:

```bash
git add -f debug.log
```

---

# 26.34 Gitignore and Git Clean

`git clean` can remove untracked files.

Preview:

```bash
git clean -n
```

Preview ignored files:

```bash
git clean -ndX
```

Remove ignored files:

```bash
git clean -fdX
```

Remove ignored directories too:

```bash
git clean -fdX
```

Important distinction:

```text
-d = include directories
-f = actually remove
-X = only ignored files
```

A safer workflow is:

```bash
git clean -ndX
```

first.

Then, only if the output is correct:

```bash
git clean -fdX
```

> **WARNING:** `git clean -fdX` permanently deletes ignored files from the working tree. Do not run it without reviewing the dry-run output.

---

# 26.35 Gitignore Commands Reference

| Command                                     | Description                                          | Example                                     | Branch State Before and After command  | Output                    |
| ------------------------------------------- | ---------------------------------------------------- | ------------------------------------------- | -------------------------------------- | ------------------------- |
| `git status`                                | Show normal status                                   | `git status`                                | Unchanged                              | Status information        |
| `git status --ignored`                      | Show ignored files                                   | `git status --ignored`                      | Unchanged                              | Ignored paths             |
| `git status --short --ignored`              | Compact status including ignored files               | `git status --short --ignored`              | Unchanged                              | `!!` entries              |
| `git check-ignore file`                     | Test whether a file is ignored                       | `git check-ignore .env`                     | Unchanged                              | Path if ignored           |
| `git check-ignore -v file`                  | Show matching ignore rule                            | `git check-ignore -v .env`                  | Unchanged                              | Rule and path             |
| `git check-ignore -n file`                  | Show line number information                         | `git check-ignore -vn .env`                 | Unchanged                              | Rule source information   |
| `git ls-files --ignored --exclude-standard` | List ignored files known through standard exclusions | `git ls-files --ignored --exclude-standard` | Unchanged                              | Ignored paths             |
| `git add -f file`                           | Force-add ignored file                               | `git add -f config.example`                 | Branch unchanged; index changes        | Usually no output         |
| `git rm --cached file`                      | Stop tracking file while keeping it locally          | `git rm --cached .env`                      | Branch unchanged; index changes        | Removal staged            |
| `git rm -r --cached directory/`             | Stop tracking directory                              | `git rm -r --cached build/`                 | Branch unchanged; index changes        | Removed paths             |
| `git clean -n`                              | Preview removal of untracked files                   | `git clean -n`                              | Unchanged                              | Candidate files           |
| `git clean -ndX`                            | Preview removal of ignored files                     | `git clean -ndX`                            | Unchanged                              | Ignored files/directories |
| `git clean -fdX`                            | Remove ignored files/directories                     | `git clean -fdX`                            | Branch unchanged; working tree changes | Removal information       |

---

# 26.36 High-Value Patterns to Memorize

## Ignore a directory

```gitignore
node_modules/
```

## Ignore a file

```gitignore
.env
```

## Ignore an extension

```gitignore
*.log
```

## Ignore build output

```gitignore
build/
dist/
```

## Ignore a directory at the current `.gitignore` root

```gitignore
/build/
```

## Ignore directories recursively

```gitignore
**/cache/
```

## Ignore everything matching a pattern

```gitignore
*.local
```

## Re-include one file

```gitignore
*.json
!config.example.json
```

## Ignore operating-system files

```gitignore
.DS_Store
Thumbs.db
```

## Ignore editor temporary files

```gitignore
*.swp
*~
```

## Ignore environment files

```gitignore
.env
.env.local
```

## Debug an ignore rule

```bash
git check-ignore -v path/to/file
```

## Show ignored files

```bash
git status --ignored
```

## Stop tracking an already-tracked file

```bash
git rm --cached file
```

## Force-add an ignored file

```bash
git add -f file
```

## Preview deletion of ignored files

```bash
git clean -ndX
```

---

# `.gitignore` Decision Tree

Use this mental model when a file appears unexpectedly.

```text
Is the file tracked?
       |
   +---+---+
   |       |
  YES      NO
   |       |
   |       v
   |   Is it ignored?
   |       |
   |   +---+---+
   |   |       |
   |  YES      NO
   |   |       |
   |   |       v
   |   |   Git reports it
   |   |
   |   v
   |  Git normally hides it
   |
   v
.gitignore does not stop tracking it
```

If a file is already tracked:

```bash
git rm --cached file
```

Then commit the change.

---

# `.gitignore` Debugging Workflow

When Git does not behave as expected:

### Step 1 — Check status

```bash
git status --short --ignored
```

### Step 2 — Check whether the file is tracked

```bash
git ls-files -- path/to/file
```

### Step 3 — Check ignore rules

```bash
git check-ignore -v -- path/to/file
```

### Step 4 — Inspect `.gitignore`

```bash
cat .gitignore
```

### Step 5 — Inspect local exclude rules

```bash
cat .git/info/exclude
```

### Step 6 — Check global ignore configuration

```bash
git config --show-origin --get core.excludesFile
```

This workflow usually identifies why Git is or is not ignoring a file.

---

# `.gitignore` vs `.git/info/exclude` vs Global Ignore

| Mechanism           | Shared through Git? | Scope                | Typical Use                        |
| ------------------- | ------------------: | -------------------- | ---------------------------------- |
| `.gitignore`        |                 Yes | Repository           | Project-specific rules             |
| `.git/info/exclude` |                  No | One local repository | Personal repository-local rules    |
| Global ignore       |                  No | User/system          | Personal files across repositories |

A useful rule of thumb:

```text
Project requirement
    -> .gitignore

Personal rule for one repository
    -> .git/info/exclude

Personal rule for all repositories
    -> global ignore
```

---

# Final Gitignore Checklist

Before committing `.gitignore` changes:

```bash
git status
```

Check the actual rule:

```bash
git check-ignore -v -- path/to/file
```

Check ignored files:

```bash
git status --ignored
```

Check whether a supposedly ignored file is already tracked:

```bash
git ls-files -- path/to/file
```

If necessary:

```bash
git rm --cached path/to/file
```

Then:

```bash
git add .gitignore
git commit -m "Update Git ignore rules"
```

---

# Key Concepts to Remember

```text
.gitignore
    |
    +--> controls untracked paths
    |
    +--> does NOT untrack existing files
    |
    +--> supports patterns
    |
    +--> supports negation
    |
    +--> can be repository-wide
    |
    +--> can exist at different directory levels
```

The most important commands are:

```bash
git check-ignore -v -- path/to/file
git status --ignored
git ls-files -- path/to/file
git rm --cached path/to/file
git add -f path/to/file
git clean -ndX
```

And the most important distinction is:

```text
IGNORED != UNTRACKED != TRACKED
```

An ignored file is generally an **untracked file that Git has been instructed not to report normally**.

A tracked file remains tracked even if its name appears in `.gitignore`.

---

# Next Part

**Next file:** `27-file-tracking.md`

[Next: File Tracking](27-file-tracking.md)
