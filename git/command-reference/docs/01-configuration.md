# 1. Configuration

Git configuration controls identity, behavior, defaults, aliases, editors, merge/rebase preferences, credential handling, hooks, and repository-specific settings.

---

## Table of Contents

* [1.1 Configuration Levels](#11-configuration-levels)
* [1.2 User Identity](#12-user-identity)
* [1.3 Reading Configuration](#13-reading-configuration)
* [1.4 Writing Configuration](#14-writing-configuration)
* [1.5 Removing Configuration](#15-removing-configuration)
* [1.6 Default Branch Configuration](#16-default-branch-configuration)
* [1.7 Pull Configuration](#17-pull-configuration)
* [1.8 Push Configuration](#18-push-configuration)
* [1.9 Editor Configuration](#19-editor-configuration)
* [1.10 Diff and Merge Tools](#110-diff-and-merge-tools)
* [1.11 Line Ending Configuration](#111-line-ending-configuration)
* [1.12 File Permissions](#112-file-permissions)
* [1.13 Aliases](#113-aliases)
* [1.14 Credential Configuration](#114-credential-configuration)
* [1.15 Remote Configuration](#115-remote-configuration)
* [1.16 Repository-Specific Configuration](#116-repository-specific-configuration)
* [1.17 Configuration Sources](#117-configuration-sources)
* [1.18 Configuration Debugging](#118-configuration-debugging)
* [1.19 Configuration Includes](#119-configuration-includes)
* [1.20 Useful Configuration Presets](#120-useful-configuration-presets)
* [1.21 Configuration Command Summary](#121-configuration-command-summary)

---

## 1.1 Configuration Levels

Git configuration can exist at several levels.

| Level    | Command                 | Description                                 | Example                        | Branch State Before and After command | Output                   |
| -------- | ----------------------- | ------------------------------------------- | ------------------------------ | ------------------------------------- | ------------------------ |
| System   | `git config --system`   | Configures Git for all users on the machine | `git config --system --list`   | `main` → `main`                       | System configuration     |
| Global   | `git config --global`   | Configures Git for the current user         | `git config --global --list`   | `main` → `main`                       | Global configuration     |
| Local    | `git config --local`    | Configures the current repository           | `git config --local --list`    | `main` → `main`                       | Repository configuration |
| Worktree | `git config --worktree` | Configures a specific worktree              | `git config --worktree --list` | `main` → `main`                       | Worktree configuration   |

### Configuration precedence

When the same key exists at multiple levels, the more specific configuration normally takes precedence:

```text
System
  ↓
Global
  ↓
Local
  ↓
Worktree
  ↓
Command-line configuration
```

For most developers, the most commonly used levels are:

```bash
git config --global
git config --local
```

---

## 1.2 User Identity

Git stores author and committer information inside commits.

### Set global username

| Command                                  | Description                     | Example                                 | Branch State Before and After command | Output    |
| ---------------------------------------- | ------------------------------- | --------------------------------------- | ------------------------------------- | --------- |
| `git config --global user.name "<name>"` | Sets the Git username globally  | `git config --global user.name "Marko"` | `main` → `main`                       | No output |
| `git config user.name`                   | Displays the effective username | `git config user.name`                  | `main` → `main`                       | `Marko`   |

```bash
git config --global user.name "Marko"
```

Verify:

```bash
git config user.name
```

Example output:

```text
Marko
```

### Set global email

| Command                                    | Description                  | Example                                            | Branch State Before and After command | Output            |
| ------------------------------------------ | ---------------------------- | -------------------------------------------------- | ------------------------------------- | ----------------- |
| `git config --global user.email "<email>"` | Sets the Git email globally  | `git config --global user.email "dev@example.com"` | `main` → `main`                       | No output         |
| `git config user.email`                    | Displays the effective email | `git config user.email`                            | `main` → `main`                       | `dev@example.com` |

```bash
git config --global user.email "dev@example.com"
```

Verify:

```bash
git config user.email
```

Example:

```text
dev@example.com
```

### Configure identity only for the current repository

This is useful when one repository requires a different identity.

| Command                           | Description                       | Example                                    | Branch State Before and After command | Output    |
| --------------------------------- | --------------------------------- | ------------------------------------------ | ------------------------------------- | --------- |
| `git config user.name "<name>"`   | Sets repository-specific username | `git config user.name "Work User"`         | `main` → `main`                       | No output |
| `git config user.email "<email>"` | Sets repository-specific email    | `git config user.email "work@example.com"` | `main` → `main`                       | No output |

Example:

```bash
git config user.name "Work User"
git config user.email "work@example.com"
```

The local repository configuration overrides the global configuration.

---

## 1.3 Reading Configuration

### Display all configuration

| Command                        | Description                       | Example                        | Branch State Before and After command | Output                |
| ------------------------------ | --------------------------------- | ------------------------------ | ------------------------------------- | --------------------- |
| `git config --list`            | Displays available configuration  | `git config --list`            | `main` → `main`                       | Configuration entries |
| `git config -l`                | Short form of `--list`            | `git config -l`                | `main` → `main`                       | Configuration entries |
| `git config --global --list`   | Displays global configuration     | `git config --global --list`   | `main` → `main`                       | Global entries        |
| `git config --local --list`    | Displays repository configuration | `git config --local --list`    | `main` → `main`                       | Local entries         |
| `git config --system --list`   | Displays system configuration     | `git config --system --list`   | `main` → `main`                       | System entries        |
| `git config --worktree --list` | Displays worktree configuration   | `git config --worktree --list` | `main` → `main`                       | Worktree entries      |

Basic command:

```bash
git config --list
```

Example:

```text
user.name=Marko
user.email=dev@example.com
init.defaultbranch=main
pull.rebase=true
core.editor=vim
```

### Read a specific configuration key

| Command                     | Description                 | Example                         | Branch State Before and After command | Output           |
| --------------------------- | --------------------------- | ------------------------------- | ------------------------------------- | ---------------- |
| `git config <key>`          | Reads a configuration value | `git config user.name`          | `main` → `main`                       | Configured value |
| `git config --global <key>` | Reads global value          | `git config --global user.name` | `main` → `main`                       | Global value     |
| `git config --local <key>`  | Reads local value           | `git config --local user.name`  | `main` → `main`                       | Local value      |

Examples:

```bash
git config user.name
git config user.email
git config init.defaultBranch
git config pull.rebase
```

### Get all values for a key

Some configuration keys can occur multiple times.

```bash
git config --get-all remote.origin.fetch
```

Example output:

```text
+refs/heads/*:refs/remotes/origin/*
```

---

## 1.4 Writing Configuration

### Basic configuration syntax

```bash
git config <key> <value>
```

Example:

```bash
git config user.name "Marko"
```

Global:

```bash
git config --global user.name "Marko"
```

Local:

```bash
git config --local user.name "Marko"
```

### Boolean configuration

Git supports boolean values such as:

```text
true
false
yes
no
on
off
```

Example:

```bash
git config --global pull.rebase true
```

Read it:

```bash
git config --global --get pull.rebase
```

Output:

```text
true
```

### Numeric configuration

Some Git configuration values accept numbers.

Example:

```bash
git config --global core.abbrev 12
```

Read it:

```bash
git config --global --get core.abbrev
```

Output:

```text
12
```

---

## 1.5 Removing Configuration

| Command                             | Description                       | Example                                      | Branch State Before and After command | Output    |
| ----------------------------------- | --------------------------------- | -------------------------------------------- | ------------------------------------- | --------- |
| `git config --unset <key>`          | Removes a local configuration key | `git config --unset user.name`               | `main` → `main`                       | No output |
| `git config --global --unset <key>` | Removes global configuration      | `git config --global --unset user.name`      | `main` → `main`                       | No output |
| `git config --system --unset <key>` | Removes system configuration      | `git config --system --unset user.name`      | `main` → `main`                       | No output |
| `git config --unset-all <key>`      | Removes all occurrences           | `git config --unset-all remote.origin.fetch` | `main` → `main`                       | No output |

Example:

```bash
git config --global --unset user.name
```

Verify:

```bash
git config --global user.name
```

If no value exists, Git normally produces no value.

---

## 1.6 Default Branch Configuration

Modern Git repositories commonly use `main` as the default initial branch.

### Set default branch

| Command                                          | Description                              | Example                                          | Branch State Before and After command       | Output    |
| ------------------------------------------------ | ---------------------------------------- | ------------------------------------------------ | ------------------------------------------- | --------- |
| `git config --global init.defaultBranch main`    | Sets default branch for new repositories | `git config --global init.defaultBranch main`    | No repository branch → No repository branch | No output |
| `git config --global init.defaultBranch develop` | Sets another default branch              | `git config --global init.defaultBranch develop` | No repository branch → No repository branch | No output |

Example:

```bash
git config --global init.defaultBranch main
```

Now:

```bash
mkdir project
cd project
git init
```

will normally initialize the repository with:

```text
main
```

instead of:

```text
master
```

---

## 1.7 Pull Configuration

### Configure pull to use rebase

| Command                                 | Description                    | Example                                 | Branch State Before and After command | Output    |
| --------------------------------------- | ------------------------------ | --------------------------------------- | ------------------------------------- | --------- |
| `git config --global pull.rebase true`  | Makes `git pull` use rebase    | `git config --global pull.rebase true`  | `main` → `main`                       | No output |
| `git config --global pull.rebase false` | Makes `git pull` use merge     | `git config --global pull.rebase false` | `main` → `main`                       | No output |
| `git config --global pull.ff only`      | Allows only fast-forward pulls | `git config --global pull.ff only`      | `main` → `main`                       | No output |

Recommended developer configuration:

```bash
git config --global pull.rebase true
```

Then:

```bash
git pull
```

behaves approximately like:

```bash
git fetch
git rebase
```

instead of automatically creating merge commits.

### Rebase only specific branches

You can also configure rebase behavior per branch:

```bash
git config branch.main.rebase true
```

---

## 1.8 Push Configuration

### Set automatic upstream behavior

| Command                                         | Description                                              | Example                                         | Branch State Before and After command | Output    |
| ----------------------------------------------- | -------------------------------------------------------- | ----------------------------------------------- | ------------------------------------- | --------- |
| `git config --global push.autoSetupRemote true` | Automatically creates upstream tracking for new branches | `git config --global push.autoSetupRemote true` | `feature/login` → `feature/login`     | No output |
| `git config --global push.default simple`       | Uses simple push behavior                                | `git config --global push.default simple`       | `main` → `main`                       | No output |
| `git config --global push.default current`      | Pushes current branch                                    | `git config --global push.default current`      | `feature/x` → `feature/x`             | No output |
| `git config --global push.followTags true`      | Pushes relevant annotated tags                           | `git config --global push.followTags true`      | `main` → `main`                       | No output |

A useful modern configuration:

```bash
git config --global push.autoSetupRemote true
```

Then:

```bash
git switch -c feature/login
git push
```

can automatically establish the upstream branch on supported Git versions.

---

## 1.9 Editor Configuration

Git opens an editor for operations such as:

```bash
git commit
git tag -a
git rebase -i
```

### Configure Vim

```bash
git config --global core.editor vim
```

### Configure Nano

```bash
git config --global core.editor nano
```

### Configure Visual Studio Code

```bash
git config --global core.editor "code --wait"
```

### Configure Neovim

```bash
git config --global core.editor nvim
```

| Command                                         | Description  | Example                                         | Branch State Before and After command | Output    |
| ----------------------------------------------- | ------------ | ----------------------------------------------- | ------------------------------------- | --------- |
| `git config --global core.editor vim`           | Uses Vim     | `git config --global core.editor vim`           | `main` → `main`                       | No output |
| `git config --global core.editor nano`          | Uses Nano    | `git config --global core.editor nano`          | `main` → `main`                       | No output |
| `git config --global core.editor "code --wait"` | Uses VS Code | `git config --global core.editor "code --wait"` | `main` → `main`                       | No output |
| `git config --global core.editor nvim`          | Uses Neovim  | `git config --global core.editor nvim`          | `main` → `main`                       | No output |

Verify:

```bash
git config --global core.editor
```

---

## 1.10 Diff and Merge Tools

Git can be configured to use external diff and merge tools.

### Configure a diff tool

```bash
git config --global diff.tool vimdiff
```

Run:

```bash
git difftool
```

### Configure a merge tool

```bash
git config --global merge.tool vimdiff
```

Run:

```bash
git mergetool
```

| Command                                  | Description                 | Example                                  | Branch State Before and After command             | Output                  |
| ---------------------------------------- | --------------------------- | ---------------------------------------- | ------------------------------------------------- | ----------------------- |
| `git config --global diff.tool vimdiff`  | Sets default diff tool      | `git config --global diff.tool vimdiff`  | `main` → `main`                                   | No output               |
| `git config --global merge.tool vimdiff` | Sets default merge tool     | `git config --global merge.tool vimdiff` | `main` → `main`                                   | No output               |
| `git difftool`                           | Opens configured diff tool  | `git difftool`                           | `main` → `main`                                   | External diff interface |
| `git mergetool`                          | Opens configured merge tool | `git mergetool`                          | Conflict state → Conflict resolved when completed | Merge tool              |

---

## 1.11 Line Ending Configuration

Line endings differ between operating systems.

Linux and macOS normally use:

```text
LF
```

Windows commonly uses:

```text
CRLF
```

### Linux configuration

For a Linux development environment:

```bash
git config --global core.autocrlf input
```

This converts CRLF to LF when committing but does not automatically convert LF files to CRLF when checking them out.

### Windows configuration

A common Windows configuration is:

```bash
git config --global core.autocrlf true
```

### Disable automatic conversion

```bash
git config --global core.autocrlf false
```

| Command                                   | Description                                                | Example                                   | Branch State Before and After command | Output    |
| ----------------------------------------- | ---------------------------------------------------------- | ----------------------------------------- | ------------------------------------- | --------- |
| `git config --global core.autocrlf input` | Converts CRLF to LF on commit                              | `git config --global core.autocrlf input` | `main` → `main`                       | No output |
| `git config --global core.autocrlf true`  | Converts line endings according to Git's platform behavior | `git config --global core.autocrlf true`  | `main` → `main`                       | No output |
| `git config --global core.autocrlf false` | Disables automatic conversion                              | `git config --global core.autocrlf false` | `main` → `main`                       | No output |

Check:

```bash
git config --global core.autocrlf
```

---

## 1.12 File Permissions

Git can track executable-bit changes.

### Ignore executable-bit changes

```bash
git config core.fileMode false
```

This is sometimes useful on filesystems where permission changes are not meaningful.

### Enable executable-bit tracking

```bash
git config core.fileMode true
```

| Command                          | Description                           | Example                          | Branch State Before and After command | Output    |
| -------------------------------- | ------------------------------------- | -------------------------------- | ------------------------------------- | --------- |
| `git config core.fileMode true`  | Tracks executable permission changes  | `git config core.fileMode true`  | `main` → `main`                       | No output |
| `git config core.fileMode false` | Ignores executable permission changes | `git config core.fileMode false` | `main` → `main`                       | No output |

---

## 1.13 Aliases

Aliases create shortcuts for frequently used commands.

### Simple alias

```bash
git config --global alias.st status
```

Then:

```bash
git st
```

is equivalent to:

```bash
git status
```

### Common aliases

| Command                                                                 | Description                 | Example               | Branch State Before and After command | Output          |
| ----------------------------------------------------------------------- | --------------------------- | --------------------- | ------------------------------------- | --------------- |
| `git config --global alias.st status`                                   | Creates `git st`            | `git st`              | `main` → `main`                       | Git status      |
| `git config --global alias.co checkout`                                 | Creates `git co`            | `git co main`         | `feature/x` → `main`                  | Branch switched |
| `git config --global alias.sw switch`                                   | Creates `git sw`            | `git sw main`         | `feature/x` → `main`                  | Branch switched |
| `git config --global alias.br branch`                                   | Creates `git br`            | `git br`              | `main` → `main`                       | Branch list     |
| `git config --global alias.ci commit`                                   | Creates `git ci`            | `git ci -m "Fix bug"` | `main` → `main`                       | Commit created  |
| `git config --global alias.last "log -1 HEAD"`                          | Creates `git last`          | `git last`            | `main` → `main`                       | Last commit     |
| `git config --global alias.lg "log --oneline --graph --decorate --all"` | Creates graphical log alias | `git lg`              | `main` → `main`                       | Commit graph    |
| `git config --global alias.unstage "restore --staged"`                  | Creates unstage shortcut    | `git unstage app.js`  | `main` → `main`                       | File unstaged   |

Recommended aliases:

```bash
git config --global alias.st "status -sb"
git config --global alias.co checkout
git config --global alias.sw switch
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.last "log -1 HEAD"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.unstage "restore --staged"
```

### Shell aliases vs Git aliases

A Git alias:

```bash
git config --global alias.st status
```

is invoked as:

```bash
git st
```

A shell alias such as:

```bash
alias gs='git status'
```

belongs to your shell and is invoked as:

```bash
gs
```

They are separate mechanisms.

---

## 1.14 Credential Configuration

Git can use credential helpers to avoid repeatedly entering credentials.

### Inspect credential configuration

```bash
git config --global credential.helper
```

### Configure a credential helper

| Command                                                        | Description                     | Example                                                        | Branch State Before and After command | Output    |
| -------------------------------------------------------------- | ------------------------------- | -------------------------------------------------------------- | ------------------------------------- | --------- |
| `git config --global credential.helper store`                  | Stores credentials on disk      | `git config --global credential.helper store`                  | `main` → `main`                       | No output |
| `git config --global credential.helper cache`                  | Temporarily caches credentials  | `git config --global credential.helper cache`                  | `main` → `main`                       | No output |
| `git config --global credential.helper "cache --timeout=3600"` | Caches credentials for one hour | `git config --global credential.helper "cache --timeout=3600"` | `main` → `main`                       | No output |

### Security note

Avoid blindly using:

```bash
git config --global credential.helper store
```

because credentials may be stored in plaintext depending on the environment.

Prefer an OS credential manager or a secure credential helper where available.

---

## 1.15 Remote Configuration

Git remote behavior can also be configured through Git configuration.

### View remote URL

```bash
git config --get remote.origin.url
```

Example:

```text
git@github.com:user/project.git
```

### View remote fetch specification

```bash
git config --get-all remote.origin.fetch
```

Example:

```text
+refs/heads/*:refs/remotes/origin/*
```

### View remote configuration

```bash
git config --get-regexp '^remote\.'
```

Example:

```text
remote.origin.url git@github.com:user/project.git
remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
```

| Command                                    | Description                | Example                                    | Branch State Before and After command | Output               |
| ------------------------------------------ | -------------------------- | ------------------------------------------ | ------------------------------------- | -------------------- |
| `git config --get remote.origin.url`       | Displays origin URL        | `git config --get remote.origin.url`       | `main` → `main`                       | Remote URL           |
| `git config --get-all remote.origin.fetch` | Displays fetch rules       | `git config --get-all remote.origin.fetch` | `main` → `main`                       | Fetch specification  |
| `git config --get-regexp '^remote\.'`      | Lists remote configuration | `git config --get-regexp '^remote\.'`      | `main` → `main`                       | Remote configuration |

---

## 1.16 Repository-Specific Configuration

Global settings apply to repositories owned by the current user.

Local settings apply only to the current repository.

Example:

```bash
cd my-project
git config user.name "Work User"
git config user.email "work@example.com"
```

Check:

```bash
git config --local --list
```

Example output:

```text
user.name=Work User
user.email=work@example.com
```

### Global vs local identity

Global:

```bash
git config --global user.email "personal@example.com"
```

Repository-specific:

```bash
git config user.email "work@example.com"
```

Inside this repository:

```text
work@example.com
```

Outside this repository:

```text
personal@example.com
```

---

## 1.17 Configuration Sources

Git configuration can originate from multiple files.

Common locations include:

```text
/etc/gitconfig
~/.gitconfig
~/.config/git/config
<repository>/.git/config
<worktree-specific configuration>
```

The exact files used depend on the operating system and Git configuration.

### Show configuration source

| Command                                        | Description                        | Example                                        | Branch State Before and After command | Output                 |
| ---------------------------------------------- | ---------------------------------- | ---------------------------------------------- | ------------------------------------- | ---------------------- |
| `git config --show-origin --list`              | Shows where each setting came from | `git config --show-origin --list`              | `main` → `main`                       | Source + key/value     |
| `git config --show-scope --list`               | Shows configuration scope          | `git config --show-scope --list`               | `main` → `main`                       | Scope + key/value      |
| `git config --show-origin --show-scope --list` | Shows source and scope             | `git config --show-origin --show-scope --list` | `main` → `main`                       | Scope + source + value |

Example:

```bash
git config --show-origin --show-scope --list
```

Possible output:

```text
global  file:/home/user/.gitconfig  user.name=Marko
global  file:/home/user/.gitconfig  user.email=dev@example.com
local   file:.git/config            remote.origin.url=...
```

---

## 1.18 Configuration Debugging

### Find where a specific configuration value comes from

```bash
git config --show-origin --get user.name
```

Example:

```text
file:/home/user/.gitconfig    Marko
```

### Show configuration scope

```bash
git config --show-scope --get user.name
```

Example:

```text
global  Marko
```

### Show both

```bash
git config --show-origin --show-scope --get user.name
```

Example:

```text
global  file:/home/user/.gitconfig    Marko
```

### Debug Git environment

```bash
GIT_TRACE=1 git status
```

This can display detailed internal Git execution information.

For transport debugging:

```bash
GIT_TRACE=1 GIT_CURL_VERBOSE=1 git fetch
```

Use verbose network tracing carefully because it can expose sensitive information in logs.

---

## 1.19 Configuration Includes

Git can include another configuration file.

### Include a configuration file

```bash
git config --global include.path ~/.gitconfig-work
```

This allows configurations to be separated.

For example:

```text
~/.gitconfig
~/.gitconfig-work
~/.gitconfig-personal
```

A configuration file can contain:

```ini
[user]
    name = Marko
    email = work@example.com
```

### Conditional includes

Git supports conditional configuration based on repository location.

Example:

```ini
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

This allows different identities and settings to be automatically selected based on the repository path.

---

## 1.20 Useful Configuration Presets

### Recommended Linux developer configuration

```bash
git config --global init.defaultBranch main
git config --global pull.rebase true
git config --global push.autoSetupRemote true
git config --global push.default simple
git config --global core.autocrlf input
git config --global core.editor vim
```

Configure identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Useful aliases:

```bash
git config --global alias.st "status -sb"
git config --global alias.co checkout
git config --global alias.sw switch
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.last "log -1 HEAD"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.unstage "restore --staged"
```

### Verify the configuration

```bash
git config --global --list
```

For a complete view:

```bash
git config --show-origin --show-scope --list
```

---

## 1.21 Configuration Command Summary

| Command                                         | Description                          | Example                                            | Branch State Before and After command | Output                  |
| ----------------------------------------------- | ------------------------------------ | -------------------------------------------------- | ------------------------------------- | ----------------------- |
| `git config --global user.name "<name>"`        | Set global username                  | `git config --global user.name "Marko"`            | `main` → `main`                       | No output               |
| `git config --global user.email "<email>"`      | Set global email                     | `git config --global user.email "dev@example.com"` | `main` → `main`                       | No output               |
| `git config user.name`                          | Read effective username              | `git config user.name`                             | `main` → `main`                       | Username                |
| `git config user.email`                         | Read effective email                 | `git config user.email`                            | `main` → `main`                       | Email                   |
| `git config --list`                             | List configuration                   | `git config --list`                                | `main` → `main`                       | Configuration           |
| `git config --global --list`                    | List global configuration            | `git config --global --list`                       | `main` → `main`                       | Global configuration    |
| `git config --local --list`                     | List local configuration             | `git config --local --list`                        | `main` → `main`                       | Local configuration     |
| `git config --system --list`                    | List system configuration            | `git config --system --list`                       | `main` → `main`                       | System configuration    |
| `git config --show-origin --list`               | Show configuration sources           | `git config --show-origin --list`                  | `main` → `main`                       | Source + values         |
| `git config --show-scope --list`                | Show configuration scopes            | `git config --show-scope --list`                   | `main` → `main`                       | Scope + values          |
| `git config --show-origin --show-scope --list`  | Show complete configuration metadata | `git config --show-origin --show-scope --list`     | `main` → `main`                       | Scope + source + values |
| `git config --global init.defaultBranch main`   | Set default initial branch           | `git config --global init.defaultBranch main`      | No branch → No branch                 | No output               |
| `git config --global pull.rebase true`          | Configure pull to rebase             | `git config --global pull.rebase true`             | `main` → `main`                       | No output               |
| `git config --global pull.ff only`              | Allow only fast-forward pulls        | `git config --global pull.ff only`                 | `main` → `main`                       | No output               |
| `git config --global push.autoSetupRemote true` | Automatically configure upstream     | `git config --global push.autoSetupRemote true`    | `main` → `main`                       | No output               |
| `git config --global core.editor vim`           | Configure Vim as editor              | `git config --global core.editor vim`              | `main` → `main`                       | No output               |
| `git config --global core.autocrlf input`       | Configure line endings               | `git config --global core.autocrlf input`          | `main` → `main`                       | No output               |
| `git config --global core.fileMode false`       | Ignore executable-bit changes        | `git config --global core.fileMode false`          | `main` → `main`                       | No output               |
| `git config --global alias.st status`           | Create Git alias                     | `git config --global alias.st status`              | `main` → `main`                       | No output               |
| `git config --global --unset <key>`             | Remove global setting                | `git config --global --unset user.name`            | `main` → `main`                       | No output               |
| `git config --unset <key>`                      | Remove local setting                 | `git config --unset user.name`                     | `main` → `main`                       | No output               |
| `git config --get <key>`                        | Read a setting                       | `git config --get user.name`                       | `main` → `main`                       | Value                   |
| `git config --get-all <key>`                    | Read all values                      | `git config --get-all remote.origin.fetch`         | `main` → `main`                       | Values                  |
| `git config --show-origin --get <key>`          | Show value source                    | `git config --show-origin --get user.name`         | `main` → `main`                       | Source + value          |
| `git config --show-scope --get <key>`           | Show value scope                     | `git config --show-scope --get user.name`          | `main` → `main`                       | Scope + value           |

---

## Essential Configuration Checklist

For a new Linux development machine, configure at minimum:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global pull.rebase true
git config --global push.autoSetupRemote true
git config --global core.autocrlf input
```

Then verify:

```bash
git config --show-origin --show-scope --list
```

A typical setup should contain values similar to:

```text
global  user.name=Your Name
global  user.email=you@example.com
global  init.defaultbranch=main
global  pull.rebase=true
global  push.autosetupremote=true
global  core.autocrlf=input
```

> **Important:** Git configuration commands generally do **not** change the current branch or working tree. They modify Git's configuration rather than repository history.

---

## Next Part

**Next file:** `02-creating-repositories.md`

[Next: Creating Repositories](../02-creating-repositories.md)
