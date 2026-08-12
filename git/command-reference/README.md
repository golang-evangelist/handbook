# Git Command Reference

Kompletan praktični **Git Command Reference** za **Developere, Software Engineers i DevOps Engineers**.

Ovaj repository predstavlja strukturisanu referencu Git komandi, organizovanu u **35 tematskih poglavlja**. Svako poglavlje nalazi se u zasebnom `*.md` fajlu i može se čitati nezavisno ili kao deo kompletnog Git vodiča.

---

## Sadržaj

* [O projektu](#o-projektu)
* [Cilj dokumentacije](#cilj-dokumentacije)
* [Za koga je namenjena](#za-koga-je-namenjena)
* [Kako je organizovana dokumentacija](#kako-je-organizovana-dokumentacija)
* [Format komandi](#format-komandi)
* [35 poglavlja](#35-poglavlja)
* [Preporučeni redosled učenja](#preporučeni-redosled-učenja)
* [Brza navigacija](#brza-navigacija)
* [Najvažnije Git oblasti](#najvažnije-git-oblasti)
* [Developer workflow](#developer-workflow)
* [DevOps workflow](#devops-workflow)
* [Bezbedno korišćenje Git-a](#bezbedno-korišćenje-git-a)
* [Dangerous Commands](#dangerous-commands)
* [Git Aliases](#git-aliases)
* [Kako koristiti ovaj repository kao referencu](#kako-koristiti-ovaj-repository-kao-referencu)
* [Komande koje vredi zapamtiti](#komande-koje-vredi-zapamtiti)
* [Kompletna struktura projekta](#kompletna-struktura-projekta)

---

# O projektu

Ovaj repository je zamišljen kao **praktični Git Command Reference**, a ne kao klasičan Git tutorial.

Cilj je da na jednom mestu objedini Git komande koje su potrebne tokom svakodnevnog rada na softverskim projektima:

* kreiranje repository-ja;
* konfiguracija Git-a;
* rad sa fajlovima;
* staging;
* commit;
* pregled izmena;
* branch management;
* merge;
* rebase;
* remote repository-ji;
* pull/push;
* stash;
* tagovi i release-ovi;
* pretraga istorije;
* rešavanje konflikata;
* cherry-pick;
* recovery;
* bisect;
* submodules;
* worktrees;
* Git LFS;
* hooks;
* repository maintenance;
* Git internals;
* sparse checkout;
* archive;
* automation;
* CI/CD;
* DevOps workflow;
* aliases.

Dokumentacija je organizovana tako da se može koristiti i kao:

> **Learning Guide + Daily Cheat Sheet + Command Reference + DevOps Reference**

---

# Cilj dokumentacije

Git je mnogo više od nekoliko komandi kao što su:

```bash
git add
git commit
git push
git pull
```

U realnom development okruženju potrebno je razumeti i:

* kako Git čuva istoriju;
* kako funkcionišu branch-evi;
* kako se istorija menja;
* kako se rešavaju konflikti;
* kako se pronalaze izgubljeni commit-i;
* kako se analiziraju promene;
* kako se radi sa remote repository-jima;
* kako Git funkcioniše unutar CI/CD sistema;
* kako se održava repository;
* kako se koriste Git objekti i internals;
* kako se automatizuju česte operacije.

Zbog toga je dokumentacija podeljena na 35 oblasti koje zajedno pokrivaju veliki deo praktičnog Git workflow-a.

---

# Za koga je namenjena

## Developers

Dokumentacija pokriva svakodnevne operacije:

```text
clone
status
add
commit
diff
branch
switch
merge
rebase
pull
push
stash
tag
log
```

Posebno su korisna poglavlja:

* `03-repository-status-and-information.md`
* `04-staging-and-committing.md`
* `05-diff-and-code-review.md`
* `06-branching.md`
* `07-merging.md`
* `08-rebasing.md`
* `09-remote-repositories.md`
* `10-undoing-changes.md`
* `11-stash.md`

---

## Software Engineers

Za napredniji development workflow posebno su korisna poglavlja koja obrađuju:

* history analysis;
* branch comparison;
* conflict resolution;
* cherry-pick;
* reflog;
* bisect;
* worktrees;
* repository internals;
* automation;
* aliases.

Preporučena poglavlja:

```text
13-searching-git-history.md
14-comparing-branches-and-commits.md
15-conflict-resolution.md
16-cherry-pick.md
17-reflog-and-recovery.md
18-git-bisect.md
20-worktrees.md
25-git-objects-internals.md
30-environment-and-automation.md
35-practical-git-aliases.md
```

---

## DevOps Engineers

Za DevOps i CI/CD workflow najvažnija su poglavlja koja obrađuju:

* automation;
* CI;
* repository diagnostics;
* Git internals;
* hooks;
* tags/releases;
* remote repository management;
* cleanup;
* maintenance;
* deterministic repository state.

Posebno:

```text
09-remote-repositories.md
12-tags-and-releases.md
21-git-lfs.md
22-git-hooks.md
23-repository-maintenance.md
24-repository-diagnostics.md
25-git-objects-internals.md
30-environment-and-automation.md
31-common-devops-ci-commands.md
32-common-developer-workflows.md
```

---

# Kako je organizovana dokumentacija

Svaka oblast nalazi se u posebnom Markdown fajlu.

Naming convention je:

```text
NN-topic-name.md
```

gde je:

* `NN` — redni broj poglavlja;
* `topic-name` — naziv oblasti pretvoren u lowercase kebab-case.

Na primer:

```text
01-configuration.md
03-repository-status-and-information.md
04-staging-and-committing.md
25-git-objects-internals.md
31-common-devops-ci-commands.md
```

Redni brojevi omogućavaju da se dokumentacija prirodno sortira u preporučenom redosledu.

---

# Format komandi

Komande su predstavljene u standardizovanom formatu:

| Command | Description | Example | Branch State Before and After command | Output |
| ------- | ----------- | ------- | ------------------------------------- | ------ |

Svaki red predstavlja konkretnu Git operaciju.

## Command

Sadrži Git komandu.

Primer:

```bash
git status
```

## Description

Objašnjava šta komanda radi i zašto se koristi.

## Example

Prikazuje realan primer upotrebe.

## Branch State Before and After command

Opisuje stanje branch-a pre i nakon izvršavanja komande.

Ovo je naročito važno za operacije kao što su:

```text
merge
rebase
reset
cherry-pick
revert
pull
push
```

## Output

Prikazuje tipičan rezultat komande.

Output može zavisiti od:

* Git verzije;
* repository-ja;
* trenutnog branch-a;
* konfiguracije;
* remote repository-ja;
* operativnog sistema.

Zbog toga output treba posmatrati kao **representative example**, a ne kao garantovani identičan rezultat.

---

# 35 poglavlja

## 01 — Configuration

**Fajl:**

[`01-configuration.md`](01-configuration.md)

Obrađuje Git konfiguraciju:

```text
git config
```

Uključuje:

* user configuration;
* global configuration;
* local configuration;
* aliases;
* default settings;
* configuration inspection.

---

## 02 — Creating Repositories

**Fajl:**

[`02-creating-repositories.md`](02-creating-repositories.md)

Obrađuje kreiranje i inicijalizaciju repository-ja:

```text
git init
git clone
```

---

## 03 — Repository Status & Information

**Fajl:**

[`03-repository-status-and-information.md`](03-repository-status-and-information.md)

Obrađuje inspekciju repository-ja:

```text
git status
git branch
git remote
git rev-parse
git show
```

---

## 04 — Staging & Committing

**Fajl:**

[`04-staging-and-committing.md`](04-staging-and-committing.md)

Osnovni Git development cycle:

```text
working tree
    ↓
staging area
    ↓
commit
```

Obrađuje:

```text
git add
git commit
```

---

## 05 — Diff & Code Review

**Fajl:**

[`05-diff-and-code-review.md`](05-diff-and-code-review.md)

Obrađuje analizu promena:

```text
git diff
git diff --cached
git show
```

Ovo poglavlje je posebno važno za code review i proveru sadržaja pre commit-a.

---

## 06 — Branching

**Fajl:**

[`06-branching.md`](06-branching.md)

Obrađuje:

```text
git branch
git switch
git checkout
```

i branch lifecycle.

---

## 07 — Merging

**Fajl:**

[`07-merging.md`](07-merging.md)

Obrađuje spajanje branch-eva:

```text
git merge
```

uključujući:

* fast-forward;
* three-way merge;
* merge conflicts;
* merge strategies.

---

## 08 — Rebasing

**Fajl:**

[`08-rebasing.md`](08-rebasing.md)

Obrađuje:

```text
git rebase
git rebase -i
```

kao i:

* history rewriting;
* interactive rebase;
* squash;
* fixup;
* reordering commits;
* conflict handling.

---

## 09 — Remote Repositories

**Fajl:**

[`09-remote-repositories.md`](09-remote-repositories.md)

Obrađuje rad sa remote repository-jima:

```text
git remote
git fetch
git pull
git push
```

---

## 10 — Undoing Changes

**Fajl:**

[`10-undoing-changes.md`](10-undoing-changes.md)

Jedno od najvažnijih poglavlja za svakodnevni rad.

Obrađuje:

```text
git restore
git reset
git revert
```

i razliku između:

```text
working tree
staging area
repository history
```

---

## 11 — Stash

**Fajl:**

[`11-stash.md`](11-stash.md)

Obrađuje privremeno čuvanje rada:

```text
git stash
git stash push
git stash pop
git stash apply
git stash list
```

---

## 12 — Tags & Releases

**Fajl:**

[`12-tags-and-releases.md`](12-tags-and-releases.md)

Obrađuje:

```text
git tag
```

i Git tag workflow za release management.

---

## 13 — Searching Git History

**Fajl:**

[`13-searching-git-history.md`](13-searching-git-history.md)

Obrađuje pretragu istorije pomoću:

```text
git log
git grep
git log -S
git log -G
```

---

## 14 — Comparing Branches & Commits

**Fajl:**

[`14-comparing-branches-and-commits.md`](14-comparing-branches-and-commits.md)

Obrađuje poređenje:

```text
branch ↔ branch
commit ↔ commit
local ↔ remote
```

pomoću `git diff`, `git log` i povezanih komandi.

---

## 15 — Conflict Resolution

**Fajl:**

[`15-conflict-resolution.md`](15-conflict-resolution.md)

Obrađuje Git konflikte nastale tokom:

```text
merge
rebase
cherry-pick
```

i proces njihovog rešavanja.

---

## 16 — Cherry-Pick

**Fajl:**

[`16-cherry-pick.md`](16-cherry-pick.md)

Obrađuje:

```bash
git cherry-pick
```

i selektivno prenošenje commit-a između branch-eva.

---

## 17 — Reflog & Recovery

**Fajl:**

[`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)

Jedno od najvažnijih poglavlja za recovery.

Obrađuje:

```bash
git reflog
```

kao i oporavak nakon:

* reset-a;
* rebase-a;
* pogrešnog branch switch-a;
* izgubljenog commit-a;
* drugih history-rewriting operacija.

---

## 18 — Git Bisect

**Fajl:**

[`18-git-bisect.md`](18-git-bisect.md)

Obrađuje pronalaženje commit-a koji je uveo bug:

```text
good commit
     ↓
bad commit
     ↓
binary search
     ↓
first bad commit
```

Glavna komanda:

```bash
git bisect
```

---

## 19 — Submodules

**Fajl:**

[`19-submodules.md`](19-submodules.md)

Obrađuje Git submodules:

```bash
git submodule
```

uključujući:

* initialization;
* update;
* synchronization;
* recursive operations.

---

## 20 — Worktrees

**Fajl:**

[`20-worktrees.md`](20-worktrees.md)

Obrađuje:

```bash
git worktree
```

Worktrees omogućavaju rad sa više branch-eva istog repository-ja u različitim direktorijumima.

---

## 21 — Git LFS

**Fajl:**

[`21-git-lfs.md`](21-git-lfs.md)

Obrađuje Git Large File Storage.

Koristi se za velike binarne fajlove koji nisu idealni za standardni Git object storage.

---

## 22 — Git Hooks

**Fajl:**

[`22-git-hooks.md`](22-git-hooks.md)

Obrađuje Git hooks i automatizaciju Git događaja.

Primeri:

```text
pre-commit
commit-msg
pre-push
post-merge
```

---

## 23 — Repository Maintenance

**Fajl:**

[`23-repository-maintenance.md`](23-repository-maintenance.md)

Obrađuje održavanje repository-ja:

```text
git gc
git maintenance
git prune
git repack
```

i povezane operacije.

---

## 24 — Repository Diagnostics

**Fajl:**

[`24-repository-diagnostics.md`](24-repository-diagnostics.md)

Obrađuje dijagnostiku problema:

```text
git fsck
git status
git count-objects
git rev-parse
```

---

## 25 — Git Objects / Internals

**Fajl:**

[`25-git-objects-internals.md`](25-git-objects-internals.md)

Obrađuje Git internals:

```text
blob
tree
commit
tag
HEAD
refs
objects
```

kao i:

```text
.git/
```

strukturu repository-ja.

---

## 26 — Ignoring Files

**Fajl:**

[`26-ignoring-files.md`](26-ignoring-files.md)

Obrađuje:

```text
.gitignore
.git/info/exclude
global gitignore
```

i pravila ignorisanja fajlova.

---

## 27 — File Tracking

**Fajl:**

[`27-file-tracking.md`](27-file-tracking.md)

Obrađuje lifecycle fajla:

```text
untracked
    ↓
staged
    ↓
committed
```

kao i:

```text
modified
deleted
renamed
copied
```

---

## 28 — Sparse Checkout

**Fajl:**

[`28-sparse-checkout.md`](28-sparse-checkout.md)

Obrađuje selektivno checkout-ovanje dela repository-ja:

```bash
git sparse-checkout
```

Posebno je korisno kod velikih repository-ja.

---

## 29 — Git Archives

**Fajl:**

[`29-git-archives.md`](29-git-archives.md)

Obrađuje:

```bash
git archive
```

za kreiranje source distribucija iz Git istorije.

---

## 30 — Environment & Automation

**Fajl:**

[`30-environment-and-automation.md`](30-environment-and-automation.md)

Obrađuje Git u automation okruženjima:

* environment variables;
* scripting;
* automation;
* non-interactive usage;
* machine-readable output.

---

## 31 — Common DevOps / CI Commands

**Fajl:**

[`31-common-devops-ci-commands.md`](31-common-devops-ci-commands.md)

Obrađuje Git komande korisne u:

```text
CI
CD
build systems
deployment pipelines
automation
```

---

## 32 — Common Developer Workflows

**Fajl:**

[`32-common-developer-workflows.md`](32-common-developer-workflows.md)

Obrađuje kompletne praktične workflow-e.

Na primer:

```text
feature development
bug fixing
code review
release workflow
hotfix workflow
branch synchronization
```

---

## 33 — High-Value Commands to Memorize

**Fajl:**

[`33-high-value-commands-to-memorize.md`](33-high-value-commands-to-memorize.md)

Kompaktna lista Git komandi koje imaju najveću praktičnu vrednost.

Ovo poglavlje je namenjeno brzom učenju i memorisanju.

---

## 34 — Dangerous Commands

**Fajl:**

[`34-dangerous-commands.md`](34-dangerous-commands.md)

Posebno obrađuje komande koje mogu:

* obrisati podatke;
* promeniti istoriju;
* prepisati remote istoriju;
* ukloniti lokalne promene.

Primeri:

```text
git reset --hard
git clean -fd
git clean -fdx
git push --force
git rebase
```

---

## 35 — Practical Git Aliases

**Fajl:**

[`35-practical-git-aliases.md`](35-practical-git-aliases.md)

Obrađuje praktične Git aliases za:

* svakodnevni development;
* history inspection;
* branch management;
* staging;
* commits;
* remote operations;
* stash;
* recovery;
* DevOps;
* CI.

Primer:

```bash
git config --global alias.st 'status -sb'
```

Nakon toga:

```bash
git st
```

---

# Preporučeni redosled učenja

Ako Git tek učite, preporučeni redosled je:

```text
01 Configuration
        ↓
02 Creating Repositories
        ↓
03 Repository Status & Information
        ↓
04 Staging & Committing
        ↓
05 Diff & Code Review
        ↓
06 Branching
        ↓
07 Merging
        ↓
08 Rebasing
        ↓
09 Remote Repositories
        ↓
10 Undoing Changes
        ↓
11 Stash
        ↓
12 Tags & Releases
```

Nakon savladavanja osnova:

```text
13 Searching Git History
14 Comparing Branches & Commits
15 Conflict Resolution
16 Cherry-Pick
17 Reflog & Recovery
18 Git Bisect
```

Za napredni Git:

```text
19 Submodules
20 Worktrees
21 Git LFS
22 Git Hooks
23 Repository Maintenance
24 Repository Diagnostics
25 Git Objects / Internals
```

Za automation i DevOps:

```text
26 Ignoring Files
27 File Tracking
28 Sparse Checkout
29 Git Archives
30 Environment & Automation
31 Common DevOps / CI Commands
32 Common Developer Workflows
```

Za konsolidaciju znanja:

```text
33 High-Value Commands to Memorize
34 Dangerous Commands
35 Practical Git Aliases
```

---

# Brza navigacija

## Osnovni Git

* [Configuration](01-configuration.md)
* [Creating Repositories](02-creating-repositories.md)
* [Repository Status & Information](03-repository-status-and-information.md)
* [Staging & Committing](04-staging-and-committing.md)
* [Diff & Code Review](05-diff-and-code-review.md)

## Branching i istorija

* [Branching](06-branching.md)
* [Merging](07-merging.md)
* [Rebasing](08-rebasing.md)
* [Searching Git History](13-searching-git-history.md)
* [Comparing Branches & Commits](14-comparing-branches-and-commits.md)

## Remote workflow

* [Remote Repositories](09-remote-repositories.md)
* [Tags & Releases](12-tags-and-releases.md)
* [Cherry-Pick](16-cherry-pick.md)

## Recovery

* [Undoing Changes](10-undoing-changes.md)
* [Stash](11-stash.md)
* [Conflict Resolution](15-conflict-resolution.md)
* [Reflog & Recovery](17-reflog-and-recovery.md)
* [Git Bisect](18-git-bisect.md)

## Advanced Git

* [Submodules](19-submodules.md)
* [Worktrees](20-worktrees.md)
* [Git LFS](21-git-lfs.md)
* [Git Hooks](22-git-hooks.md)
* [Repository Maintenance](23-repository-maintenance.md)
* [Repository Diagnostics](24-repository-diagnostics.md)
* [Git Objects / Internals](25-git-objects-internals.md)

## Repository management

* [Ignoring Files](26-ignoring-files.md)
* [File Tracking](27-file-tracking.md)
* [Sparse Checkout](28-sparse-checkout.md)
* [Git Archives](29-git-archives.md)

## Automation / DevOps

* [Environment & Automation](30-environment-and-automation.md)
* [Common DevOps / CI Commands](31-common-devops-ci-commands.md)
* [Common Developer Workflows](32-common-developer-workflows.md)

## Quick Reference

* [High-Value Commands to Memorize](33-high-value-commands-to-memorize.md)
* [Dangerous Commands](34-dangerous-commands.md)
* [Practical Git Aliases](35-practical-git-aliases.md)

---

# Najvažnije Git oblasti

Ako ne želite da čitate svih 35 poglavlja odmah, sledeće oblasti predstavljaju osnovni minimum za svakodnevni development:

```text
03 Repository Status & Information
04 Staging & Committing
05 Diff & Code Review
06 Branching
07 Merging
08 Rebasing
09 Remote Repositories
10 Undoing Changes
15 Conflict Resolution
17 Reflog & Recovery
32 Common Developer Workflows
33 High-Value Commands to Memorize
34 Dangerous Commands
```

---

# Developer workflow

Tipičan feature workflow izgleda ovako:

```text
Clone / existing repository
        ↓
Check status
        ↓
Create branch
        ↓
Modify files
        ↓
Review diff
        ↓
Stage changes
        ↓
Commit
        ↓
Fetch remote changes
        ↓
Rebase / merge
        ↓
Resolve conflicts if necessary
        ↓
Push branch
        ↓
Code review
        ↓
Merge
```

Primer osnovnog workflow-a:

```bash
git status

git switch -c feature/login

git status

git diff

git add --all

git diff --cached

git commit -m "Add login feature"

git fetch origin

git rebase origin/main

git push -u origin feature/login
```

Tačne strategije mogu zavisiti od pravila konkretnog projekta.

---

# DevOps workflow

U DevOps okruženju Git se često koristi kao deo automatizovanog pipeline-a:

```text
Repository
    ↓
Checkout
    ↓
Fetch
    ↓
Verify commit
    ↓
Build
    ↓
Test
    ↓
Package
    ↓
Deploy
```

Tipične komande uključuju:

```bash
git clone
git fetch
git checkout / git switch
git rev-parse HEAD
git describe
git diff
git status --porcelain
git tag
```

Za CI sisteme je naročito važno koristiti predvidiv i machine-readable output.

---

# Bezbedno korišćenje Git-a

Git omogućava veoma snažne operacije.

Zbog toga je važno razlikovati:

```text
read-only operations
```

od:

```text
history-changing operations
```

i:

```text
destructive operations
```

## Relativno bezbedne operacije

Primeri:

```bash
git status
git log
git diff
git show
git branch
git remote -v
git fetch
```

## Operacije koje menjaju lokalnu istoriju

Primeri:

```bash
git commit --amend
git reset
git rebase
git cherry-pick
```

## Operacije koje mogu menjati ili brisati podatke

Primeri:

```bash
git reset --hard
git clean -fd
git clean -fdx
git push --force
```

Pre izvršavanja destruktivne komande treba proveriti:

```bash
git status
git branch
git log
git reflog
```

---

# Dangerous Commands

Poglavlje:

[`34-dangerous-commands.md`](34-dangerous-commands.md)

treba koristiti kao bezbednosnu referencu pre izvršavanja komandi koje menjaju Git istoriju ili brišu podatke.

Posebno obratiti pažnju na:

```bash
git reset --hard
git clean -fd
git clean -fdx
git push --force
git push --force-with-lease
git rebase
```

Važno pravilo:

> **Nemojte koristiti destruktivnu Git komandu ako niste sigurni šta će biti obrisano ili koja će istorija biti promenjena.**

---

# Git Aliases

Poglavlje:

[`35-practical-git-aliases.md`](35-practical-git-aliases.md)

sadrži praktične aliases za svakodnevni rad.

Primer:

```bash
git config --global alias.st 'status -sb'
```

Nakon toga:

```bash
git st
```

Postaje kraći oblik za:

```bash
git status -sb
```

Korisni aliases mogu biti:

```text
git st
git lg
git br
git current
git aa
git ap
git dc
git fa
git plr
git psu
git sl
git ref
git conflicts
git root
```

Aliases treba koristiti tako da povećaju produktivnost, ali ne i da sakriju destruktivne operacije iza nejasnih naziva.

---

# Kako koristiti ovaj repository kao referencu

Postoje tri preporučena načina korišćenja.

## 1. Linearno učenje

Čitati:

```text
01 → 02 → 03 → ... → 35
```

Ovaj pristup je najbolji za sistematsko učenje Git-a.

---

## 2. Problem-driven learning

Ako imate konkretan problem, direktno otvorite odgovarajuće poglavlje.

Na primer:

### "Pogrešno sam uradio reset."

Otvorite:

[`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)

### "Imam merge conflict."

Otvorite:

[`15-conflict-resolution.md`](15-conflict-resolution.md)

### "Želim da pronađem commit koji je uveo bug."

Otvorite:

[`18-git-bisect.md`](18-git-bisect.md)

### "Želim da vidim šta sam promenio."

Otvorite:

[`05-diff-and-code-review.md`](05-diff-and-code-review.md)

### "Želim da radim sa više branch-eva paralelno."

Otvorite:

[`20-worktrees.md`](20-worktrees.md)

---

## 3. Quick Reference

Za svakodnevni rad najčešće je dovoljno koristiti:

```text
03
04
05
06
07
08
09
10
33
34
35
```

---

# Komande koje vredi zapamtiti

## Repository

```bash
git status
git log
git show
git diff
```

## Branches

```bash
git branch
git switch
git switch -c
```

## Staging

```bash
git add
git add --patch
git restore --staged
```

## Commit

```bash
git commit
git commit --amend
```

## Remote

```bash
git fetch
git pull
git push
```

## Integration

```bash
git merge
git rebase
git cherry-pick
```

## Recovery

```bash
git restore
git reset
git revert
git reflog
```

## Search

```bash
git log --grep
git log -S
git log -G
git grep
```

## Debugging

```bash
git bisect
git reflog
git fsck
```

---

# Kompletna struktura projekta

Repository treba da ima sledeću strukturu:

```text
.
├── README.md
│
├── 01-configuration.md
├── 02-creating-repositories.md
├── 03-repository-status-and-information.md
├── 04-staging-and-committing.md
├── 05-diff-and-code-review.md
├── 06-branching.md
├── 07-merging.md
├── 08-rebasing.md
├── 09-remote-repositories.md
├── 10-undoing-changes.md
├── 11-stash.md
├── 12-tags-and-releases.md
├── 13-searching-git-history.md
├── 14-comparing-branches-and-commits.md
├── 15-conflict-resolution.md
├── 16-cherry-pick.md
├── 17-reflog-and-recovery.md
├── 18-git-bisect.md
├── 19-submodules.md
├── 20-worktrees.md
├── 21-git-lfs.md
├── 22-git-hooks.md
├── 23-repository-maintenance.md
├── 24-repository-diagnostics.md
├── 25-git-objects-internals.md
├── 26-ignoring-files.md
├── 27-file-tracking.md
├── 28-sparse-checkout.md
├── 29-git-archives.md
├── 30-environment-and-automation.md
├── 31-common-devops-ci-commands.md
├── 32-common-developer-workflows.md
├── 33-high-value-commands-to-memorize.md
├── 34-dangerous-commands.md
└── 35-practical-git-aliases.md
```

---

# Koncept organizacije

Cela dokumentacija može se posmatrati kroz nekoliko nivoa.

```text
                    Git
                     │
       ┌─────────────┼─────────────┐
       │             │             │
    Basics        History       Collaboration
       │             │             │
       ↓             ↓             ↓
   01–05          13–18          06–12
       │             │             │
       └─────────────┼─────────────┘
                     │
                 Advanced
                     │
                  19–29
                     │
                     ↓
              Automation / DevOps
                  30–32
                     │
                     ↓
              Quick Reference
                  33–35
```

---

# Šta ova dokumentacija nije

Ovaj repository nije zamišljen kao potpuna zamena za zvaničnu Git dokumentaciju.

Git ima veliki broj naprednih opcija, konfiguracionih parametara, plumbing komandi i specifičnih edge-case scenarija koji nisu svi obrađeni u jednom praktičnom reference-u.

Ovaj repository je fokusiran na:

> **praktične komande i workflow-e koji imaju visoku vrednost u realnom development i DevOps radu.**

Za detaljno ponašanje pojedinačnih opcija uvek treba proveriti odgovarajuću verziju Git dokumentacije i `git help`:

```bash
git help <command>
```

ili:

```bash
git <command> --help
```

---

# Git Mental Model

Najvažniji koncept za razumevanje Git-a je razlikovanje tri osnovna nivoa:

```text
Working Tree
     │
     │ git add
     ↓
Staging Area
     │
     │ git commit
     ↓
Repository
```

Remote repository je dodatni sloj:

```text
Local Repository
       │
       │ git push
       ↓
Remote Repository
```

A `git fetch` prenosi informacije sa remote-a bez automatskog menjanja trenutnog working tree-ja:

```text
Remote Repository
       │
       │ git fetch
       ↓
Local Repository / Remote-tracking refs
```

Razumevanje ovog modela čini veliki broj Git komandi mnogo lakšim za razumevanje.

---

# Branch Mental Model

Branch nije zaseban folder sa kopijom projekta.

Branch je u osnovi referenca koja pokazuje na commit.

Pojednostavljeno:

```text
main
  │
  ▼
A ─── B ─── C
```

Kada napravimo novi branch:

```text
             feature
                │
                ▼
A ─── B ─── C
```

Novi commit na feature branch-u:

```text
             feature
                │
                ▼
A ─── B ─── C ─── D
          │
         main
```

Ovaj mentalni model je ključan za razumevanje:

```text
branch
merge
rebase
reset
reflog
cherry-pick
```

---

# History Rewriting

Neke Git komande menjaju istoriju:

```text
rebase
reset
commit --amend
cherry-pick
```

Posebno treba razlikovati:

```text
history-preserving operations
```

od:

```text
history-rewriting operations
```

Na primer:

```bash
git revert
```

dodaje novi commit koji poništava prethodni commit.

Nasuprot tome:

```bash
git reset
```

pomera reference i može ukloniti commit iz trenutne branch istorije.

Ova razlika je detaljnije obrađena u odgovarajućim poglavljima.

---

# Final Workflow Reference

Za svakodnevni development:

```bash
git status
git switch -c feature/name
git diff
git add --all
git diff --cached
git commit -m "Description"
git fetch origin
git rebase origin/main
git push -u origin feature/name
```

Za pregled:

```bash
git status
git log --oneline --graph --decorate --all
git diff
git diff --cached
git branch -vv
```

Za recovery:

```bash
git reflog
git status
git log
git branch
```

Za debugging:

```bash
git bisect start
git bisect bad
git bisect good <commit>
```

Za rešavanje konflikta:

```bash
git status
git diff
git add <resolved-file>
git rebase --continue
```

ili:

```bash
git add <resolved-file>
git commit
```

u zavisnosti od operacije koja je izazvala konflikt.

---

# Zaključak

Ovaj repository predstavlja kompletan praktični put kroz Git:

```text
Configuration
      ↓
Repository Creation
      ↓
Working Tree
      ↓
Staging
      ↓
Commits
      ↓
Branches
      ↓
Merge / Rebase
      ↓
Remote Repositories
      ↓
History Analysis
      ↓
Recovery
      ↓
Advanced Git
      ↓
Automation
      ↓
DevOps / CI
      ↓
Workflows
      ↓
Quick Reference
      ↓
Aliases
```

Ako Git koristite svakodnevno, nije potrebno memorisati svih 35 poglavlja.

Cilj je da:

1. razumete Git mentalni model;
2. znate koje komande postoje;
3. znate kada koju komandu koristiti;
4. razumete posledice komande;
5. znate kako da proverite stanje repository-ja;
6. znate kako da se oporavite od greške;
7. znate kako Git funkcioniše u development i DevOps workflow-ima.

Za svakodnevni rad, prvo savladajte:

```text
03 → 04 → 05 → 06 → 07 → 08 → 09 → 10
```

zatim:

```text
15 → 17 → 18
```

a nakon toga:

```text
19 → 20 → 21 → 22 → 23 → 24 → 25
```

Za DevOps i CI/CD:

```text
30 → 31 → 32
```

Za brzo ponavljanje:

```text
33 → 34 → 35
```

---

## Kompletna navigacija

|  # | Oblast                          | Fajl                                                                                 |
| -: | ------------------------------- | ------------------------------------------------------------------------------------ |
| 01 | Configuration                   | [`01-configuration.md`](01-configuration.md)                                         |
| 02 | Creating Repositories           | [`02-creating-repositories.md`](02-creating-repositories.md)                         |
| 03 | Repository Status & Information | [`03-repository-status-and-information.md`](03-repository-status-and-information.md) |
| 04 | Staging & Committing            | [`04-staging-and-committing.md`](04-staging-and-committing.md)                       |
| 05 | Diff & Code Review              | [`05-diff-and-code-review.md`](05-diff-and-code-review.md)                           |
| 06 | Branching                       | [`06-branching.md`](06-branching.md)                                                 |
| 07 | Merging                         | [`07-merging.md`](07-merging.md)                                                     |
| 08 | Rebasing                        | [`08-rebasing.md`](08-rebasing.md)                                                   |
| 09 | Remote Repositories             | [`09-remote-repositories.md`](09-remote-repositories.md)                             |
| 10 | Undoing Changes                 | [`10-undoing-changes.md`](10-undoing-changes.md)                                     |
| 11 | Stash                           | [`11-stash.md`](11-stash.md)                                                         |
| 12 | Tags & Releases                 | [`12-tags-and-releases.md`](12-tags-and-releases.md)                                 |
| 13 | Searching Git History           | [`13-searching-git-history.md`](13-searching-git-history.md)                         |
| 14 | Comparing Branches & Commits    | [`14-comparing-branches-and-commits.md`](14-comparing-branches-and-commits.md)       |
| 15 | Conflict Resolution             | [`15-conflict-resolution.md`](15-conflict-resolution.md)                             |
| 16 | Cherry-Pick                     | [`16-cherry-pick.md`](16-cherry-pick.md)                                             |
| 17 | Reflog & Recovery               | [`17-reflog-and-recovery.md`](17-reflog-and-recovery.md)                             |
| 18 | Git Bisect                      | [`18-git-bisect.md`](18-git-bisect.md)                                               |
| 19 | Submodules                      | [`19-submodules.md`](19-submodules.md)                                               |
| 20 | Worktrees                       | [`20-worktrees.md`](20-worktrees.md)                                                 |
| 21 | Git LFS                         | [`21-git-lfs.md`](21-git-lfs.md)                                                     |
| 22 | Git Hooks                       | [`22-git-hooks.md`](22-git-hooks.md)                                                 |
| 23 | Repository Maintenance          | [`23-repository-maintenance.md`](23-repository-maintenance.md)                       |
| 24 | Repository Diagnostics          | [`24-repository-diagnostics.md`](24-repository-diagnostics.md)                       |
| 25 | Git Objects / Internals         | [`25-git-objects-internals.md`](25-git-objects-internals.md)                         |
| 26 | Ignoring Files                  | [`26-ignoring-files.md`](26-ignoring-files.md)                                       |
| 27 | File Tracking                   | [`27-file-tracking.md`](27-file-tracking.md)                                         |
| 28 | Sparse Checkout                 | [`28-sparse-checkout.md`](28-sparse-checkout.md)                                     |
| 29 | Git Archives                    | [`29-git-archives.md`](29-git-archives.md)                                           |
| 30 | Environment & Automation        | [`30-environment-and-automation.md`](30-environment-and-automation.md)               |
| 31 | Common DevOps / CI Commands     | [`31-common-devops-ci-commands.md`](31-common-devops-ci-commands.md)                 |
| 32 | Common Developer Workflows      | [`32-common-developer-workflows.md`](32-common-developer-workflows.md)               |
| 33 | High-Value Commands to Memorize | [`33-high-value-commands-to-memorize.md`](33-high-value-commands-to-memorize.md)     |
| 34 | Dangerous Commands              | [`34-dangerous-commands.md`](34-dangerous-commands.md)                               |
| 35 | Practical Git Aliases           | [`35-practical-git-aliases.md`](35-practical-git-aliases.md)                         |
