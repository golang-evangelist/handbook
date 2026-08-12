# 18. Git Bisect

`git bisect` is a Git debugging tool that uses **binary search** to identify the commit that introduced a bug.

Instead of manually checking every commit between a known-good and known-bad state, Git repeatedly selects a commit approximately halfway between them.

For example:

```text
GOOD                              BAD
  |                                |
  v                                v
A --- B --- C --- D --- E --- F --- G
              ^
              |
          possible bug
```

Git tests approximately half of the remaining commits on each iteration.

Typical workflow:

```bash
git bisect start
git bisect bad
git bisect good <commit>
```

Git checks out a candidate commit.

You test it and tell Git:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

After enough iterations, Git identifies the first bad commit.

---

# Table of Contents

* [18.1 What Is Git Bisect?](#181-what-is-git-bisect)
* [18.2 Why Use Git Bisect?](#182-why-use-git-bisect)
* [18.3 Basic Bisect Workflow](#183-basic-bisect-workflow)
* [18.4 Starting a Bisect Session](#184-starting-a-bisect-session)
* [18.5 Marking a Commit as Bad](#185-marking-a-commit-as-bad)
* [18.6 Marking a Commit as Good](#186-marking-a-commit-as-good)
* [18.7 Checking the Current Bisect State](#187-checking-the-current-bisect-state)
* [18.8 Testing the Candidate Commit](#188-testing-the-candidate-commit)
* [18.9 Continuing the Bisect](#189-continuing-the-bisect)
* [18.10 Finding the First Bad Commit](#1810-finding-the-first-bad-commit)
* [18.11 Ending a Bisect Session](#1811-ending-a-bisect-session)
* [18.12 Aborting a Bisect Session](#1812-aborting-a-bisect-session)
* [18.13 Resetting After Bisect](#1813-resetting-after-bisect)
* [18.14 Bisect Status](#1814-bisect-status)
* [18.15 Bisect Log](#1815-bisect-log)
* [18.16 Bisect Replay](#1816-bisect-replay)
* [18.17 Bisect Terms](#1817-bisect-terms)
* [18.18 Marking Multiple Good Commits](#1818-marking-multiple-good-commits)
* [18.19 Marking Multiple Bad Commits](#1819-marking-multiple-bad-commits)
* [18.20 Bisecting With Tags](#1820-bisecting-with-tags)
* [18.21 Bisecting With Branches](#1821-bisecting-with-branches)
* [18.22 Automated Bisect](#1822-automated-bisect)
* [18.23 git bisect run](#1823-git-bisect-run)
* [18.24 Exit Codes for Automated Bisect](#1824-exit-codes-for-automated-bisect)
* [18.25 Shell Script Automation](#1825-shell-script-automation)
* [18.26 Testing Applications](#1826-testing-applications)
* [18.27 Bisecting Build Failures](#1827-bisecting-build-failures)
* [18.28 Bisecting Test Failures](#1828-bisecting-test-failures)
* [18.29 Handling Untestable Commits](#1829-handling-untestable-commits)
* [18.30 Skipping Commits](#1830-skipping-commits)
* [18.31 Bisecting Merge Commits](#1831-bisecting-merge-commits)
* [18.32 Bisecting Large Histories](#1832-bisecting-large-histories)
* [18.33 CI/CD and DevOps Use Cases](#1833-cicd-and-devops-use-cases)
* [18.34 Practical Bisect Workflows](#1834-practical-bisect-workflows)
* [18.35 High-Value Bisect Commands](#1835-high-value-bisect-commands)
* [18.36 Bisect Cheat Sheet](#1836-bisect-cheat-sheet)

---

# 18.1 What Is Git Bisect?

Git bisect performs a binary search through commit history.

Assume a project has 64 commits between a known-good and known-bad state.

A linear search could require checking up to 64 commits.

Binary search requires approximately:

```text
log2(64) = 6
```

tests.

The process looks like:

```text
64 commits
    |
    v
32 commits
    |
    v
16 commits
    |
    v
 8 commits
    |
    v
 4 commits
    |
    v
 2 commits
    |
    v
 1 commit
```

The goal is to identify the **first bad commit**.

---

# 18.2 Why Use Git Bisect?

Use `git bisect` when:

* A bug was introduced somewhere in a long history.
* You know a commit where the project worked.
* You know a later commit where it does not work.
* The regression can be tested repeatedly.
* You want Git to efficiently narrow the search.

Typical examples:

```text
Application stopped starting
API response changed
Unit test started failing
Performance regression appeared
Build started failing
Database migration broke
Feature disappeared
Production behavior changed
```

---

# 18.3 Basic Bisect Workflow

The standard workflow is:

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
```

Git checks out a candidate commit.

Run the test.

If the candidate works:

```bash
git bisect good
```

If the candidate is broken:

```bash
git bisect bad
```

Repeat until Git identifies the first bad commit.

Finish:

```bash
git bisect reset
```

The complete workflow:

```text
Start
  |
  v
git bisect start
  |
  v
Mark bad
  |
  v
Mark good
  |
  v
Git selects candidate
  |
  v
Run test
 / \
/   \
Good Bad
 |    |
 v    v
git bisect good
git bisect bad
  |
  v
Repeat
  |
  v
First bad commit
  |
  v
git bisect reset
```

---

# 18.4 Starting a Bisect Session

Start:

```bash
git bisect start
```

The current `HEAD` can then be marked bad:

```bash
git bisect bad
```

Specify a known-good commit:

```bash
git bisect good <commit>
```

Example:

```bash
git bisect start
git bisect bad
git bisect good v2.0.0
```

Git then checks out a commit approximately halfway between the good and bad states.

---

| Command                  | Description              | Example                        | Branch State Before and After command  | Output                  |
| ------------------------ | ------------------------ | ------------------------------ | -------------------------------------- | ----------------------- |
| `git bisect start`       | Start bisect session     | `git bisect start`             | Current branch enters bisect state     | Bisect session started  |
| `git bisect bad`         | Mark current commit bad  | `git bisect bad`               | Current branch remains in bisect state | Candidate range reduced |
| `git bisect good COMMIT` | Mark commit good         | `git bisect good v2.0.0`       | Git checks out next candidate          | Bisect candidate        |
| `git bisect good`        | Mark current commit good | `git bisect good`              | Candidate changes                      | Remaining commits       |
| `git bisect bad`         | Mark current commit bad  | `git bisect bad`               | Candidate changes                      | Remaining commits       |
| `git bisect status`      | Show current state       | `git bisect status`            | Unchanged                              | Bisect state            |
| `git bisect log`         | Show decisions           | `git bisect log`               | Unchanged                              | Bisect history          |
| `git bisect reset`       | End session              | `git bisect reset`             | Original branch state restored         | Reset to original HEAD  |
| `git bisect skip`        | Skip current commit      | `git bisect skip`              | Candidate range changes                | Next candidate          |
| `git bisect run CMD`     | Automate testing         | `git bisect run ./test.sh`     | Git tests candidates automatically     | Bisect result           |
| `git bisect replay FILE` | Replay decisions         | `git bisect replay bisect.log` | Bisect state recreated                 | Replay progress         |

---

# 18.5 Marking a Commit as Bad

If the current candidate reproduces the bug:

```bash
git bisect bad
```

You can also specify a commit:

```bash
git bisect bad <commit>
```

Example:

```bash
git bisect bad HEAD
```

or:

```bash
git bisect bad a1b2c3d
```

After marking the commit bad, Git narrows the search to the relevant portion of history.

---

# 18.6 Marking a Commit as Good

If the current candidate does not contain the bug:

```bash
git bisect good
```

Or:

```bash
git bisect good <commit>
```

Example:

```bash
git bisect good v1.5.0
```

The good commit must represent a state where the bug does not occur.

---

# 18.7 Checking the Current Bisect State

Use:

```bash
git bisect status
```

Example:

```text
status: waiting for both good and bad commits
```

During a session you may see information about:

* Current good commit
* Current bad commit
* Remaining revisions
* Current candidate

You can also use:

```bash
git status
```

Git may indicate that a bisect operation is in progress.

---

# 18.8 Testing the Candidate Commit

After Git checks out a candidate:

```bash
git status
```

Then run the appropriate test.

For example:

```bash
./run-tests.sh
```

or:

```bash
npm test
```

or:

```bash
pytest
```

or:

```bash
make test
```

If the test passes:

```bash
git bisect good
```

If the test fails:

```bash
git bisect bad
```

The test must be deterministic enough to classify the commit reliably.

---

# 18.9 Continuing the Bisect

After every classification:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Git selects another candidate.

Repeat:

```bash
./run-tests.sh
git bisect good
```

or:

```bash
./run-tests.sh
git bisect bad
```

until Git reports the first bad commit.

---

# 18.10 Finding the First Bad Commit

At the end Git reports something similar to:

```text
a1b2c3d is the first bad commit
commit a1b2c3d
Author: Developer
Date: ...

    Change API behavior
```

Inspect it:

```bash
git show a1b2c3d
```

Inspect metadata:

```bash
git show --no-patch --format=fuller a1b2c3d
```

Inspect the files changed:

```bash
git show --stat a1b2c3d
```

Inspect the patch:

```bash
git show --patch a1b2c3d
```

---

# 18.11 Ending a Bisect Session

When the first bad commit has been identified:

```bash
git bisect reset
```

This exits bisect mode and returns the repository to the state it had before the bisect session.

Example:

```bash
git bisect reset
```

Output may resemble:

```text
Previous HEAD position was ...
Switched to branch 'main'
```

Always run `git bisect reset` after completing a manual bisect.

---

# 18.12 Aborting a Bisect Session

If you want to abandon the investigation:

```bash
git bisect reset
```

This is also the standard way to abort a bisect session.

For example:

```bash
git bisect start
git bisect bad
git bisect good v1.0.0

# Decide to stop

git bisect reset
```

---

# 18.13 Resetting After Bisect

After:

```bash
git bisect reset
```

verify:

```bash
git status
```

Check the current branch:

```bash
git branch --show-current
```

Check the current commit:

```bash
git rev-parse HEAD
```

If you need to verify the original position:

```bash
git reflog
```

---

# 18.14 Bisect Status

Use:

```bash
git bisect status
```

This helps determine whether a bisect session is active.

Example:

```bash
git bisect status
```

Possible information includes:

```text
status: waiting for good commit(s), bad commit known
```

The exact output varies depending on the state of the session.

---

# 18.15 Bisect Log

Git records the decisions made during a bisect session.

Display them:

```bash
git bisect log
```

Example:

```text
git bisect start
# status: waiting for both good and bad commits
git bisect bad a1b2c3d
git bisect good f6e7d8c
git bisect good 1234567
git bisect bad 7654321
```

The log is useful for:

* Auditing the investigation
* Reproducing the search
* Debugging incorrect classifications
* Sharing the investigation with teammates

Save it:

```bash
git bisect log > bisect.log
```

---

# 18.16 Bisect Replay

A saved bisect log can be replayed.

Example:

```bash
git bisect replay bisect.log
```

This can reproduce the sequence of good/bad decisions.

A typical workflow:

```bash
git bisect log > bisect.log
```

Later:

```bash
git bisect start
git bisect replay bisect.log
```

This is useful for reproducible debugging investigations.

---

# 18.17 Bisect Terms

Important terminology:

| Term             | Meaning                                                  |
| ---------------- | -------------------------------------------------------- |
| Good             | Commit where the bug is known not to exist               |
| Bad              | Commit where the bug is known to exist                   |
| Candidate        | Commit Git selected for testing                          |
| First bad commit | Earliest commit identified as introducing the bug        |
| Bisect session   | Period between `git bisect start` and `git bisect reset` |
| Skip             | Candidate that cannot be reliably classified             |
| Bisect log       | Record of good/bad decisions                             |
| Automated bisect | Bisect controlled by a command/script                    |

---

# 18.18 Marking Multiple Good Commits

You can specify multiple good commits.

Example:

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
git bisect good v1.1.0
```

Multiple known-good references can help Git understand the search space.

You can also use explicit commit IDs:

```bash
git bisect good a1b2c3d
git bisect good e4f5g6h
```

---

# 18.19 Marking Multiple Bad Commits

Similarly, multiple bad references can be provided:

```bash
git bisect start
git bisect bad main
git bisect bad release
git bisect good v1.0.0
```

This is useful when the search involves more complex history.

---

# 18.20 Bisecting With Tags

Tags are convenient boundaries.

Example:

```bash
git bisect start
git bisect bad HEAD
git bisect good v2.0.0
```

If the bug appeared between releases:

```text
v2.0.0                 v2.1.0
  |                       |
  v                       v
GOOD ------------------- BAD
```

You can bisect between the releases.

Example:

```bash
git bisect start
git bisect bad v2.1.0
git bisect good v2.0.0
```

---

# 18.21 Bisecting With Branches

Branches can also be used as boundaries.

Example:

```bash
git bisect start
git bisect bad main
git bisect good release/1.0
```

The important requirement is that the selected good and bad states provide a meaningful ordering for the regression being investigated.

---

# 18.22 Automated Bisect

Manual testing is useful when determining whether a bug is present requires human judgment.

But many regressions can be tested automatically.

For example:

```bash
./test-regression.sh
```

If the script returns:

```text
0
```

for a good commit and:

```text
1
```

for a bad commit, Git can automate the entire bisect.

Use:

```bash
git bisect run ./test-regression.sh
```

Git will:

1. Select a candidate.
2. Run the script.
3. Interpret the exit status.
4. Mark the candidate.
5. Select another candidate.
6. Repeat.
7. Identify the first bad commit.

---

# 18.23 git bisect run

Basic:

```bash
git bisect run ./test.sh
```

With arguments:

```bash
git bisect run ./test.sh --regression
```

With a command:

```bash
git bisect run make test
```

With an environment variable:

```bash
git bisect run env MODE=regression ./test.sh
```

The command is executed at each candidate commit.

---

# 18.24 Exit Codes for Automated Bisect

Automated bisect relies on exit statuses.

Common interpretation:

```text
0       Good
1-124   Bad
125     Skip
126     Cannot execute
127     Command not found
128+    Error
```

The important special status is:

```text
125
```

which tells Git that the current commit cannot be tested and should be skipped.

Example shell script:

```bash
#!/usr/bin/env bash

if ./build.sh; then
    ./regression-test.sh
else
    exit 125
fi
```

This allows build failures that are unrelated to the regression to be skipped.

---

# 18.25 Shell Script Automation

Example:

```bash
#!/usr/bin/env bash

set -e

make clean
make

./test-regression
```

Make executable:

```bash
chmod +x bisect-test.sh
```

Run:

```bash
git bisect run ./bisect-test.sh
```

The script should:

* Be deterministic
* Return `0` for good
* Return a non-zero status for bad
* Return `125` for untestable commits
* Avoid modifying Git history
* Avoid interactive prompts

---

# 18.26 Testing Applications

A regression test might be:

```bash
#!/usr/bin/env bash

curl -fsS http://localhost:8080/api/health
```

If the endpoint works:

```text
exit 0
```

If it fails:

```text
exit 1
```

Then:

```bash
git bisect run ./test-health.sh
```

Another example:

```bash
#!/usr/bin/env bash

python -m pytest tests/test_regression.py
```

Run:

```bash
git bisect run ./test-regression.sh
```

---

# 18.27 Bisecting Build Failures

Suppose the current release builds successfully:

```text
GOOD
```

but a later commit fails:

```text
BAD
```

Start:

```bash
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
```

Test:

```bash
make clean
make
```

If build succeeds:

```bash
git bisect good
```

If build fails:

```bash
git bisect bad
```

Automate:

```bash
git bisect run make
```

Only use this if the exit status of `make` accurately represents the regression you are investigating.

---

# 18.28 Bisecting Test Failures

For a specific regression test:

```bash
git bisect run pytest tests/test_api.py
```

Or:

```bash
git bisect run npm test -- --runInBand
```

Or:

```bash
git bisect run ./run-regression-test.sh
```

The test must consistently classify each commit.

If the test is flaky, automated bisect results may be unreliable.

---

# 18.29 Handling Untestable Commits

Sometimes a candidate cannot be tested.

Examples:

* Dependencies are missing.
* The project does not compile.
* Required infrastructure is unavailable.
* The test was introduced later.
* A migration cannot run.
* The environment is incompatible.

Use:

```bash
git bisect skip
```

Git then selects another candidate.

You can skip a specific commit:

```bash
git bisect skip <commit>
```

---

# 18.30 Skipping Commits

Example:

```bash
git bisect skip
```

Git may report:

```text
Bisecting: ... revisions left to test after this
```

If several commits are known to be untestable:

```bash
git bisect skip a1b2c3d
git bisect skip d4e5f6a
```

After skipping too many commits, Git may be unable to determine a unique first bad commit.

In that situation, manually inspect the remaining candidates.

---

# 18.31 Bisecting Merge Commits

Merge-heavy histories can make regression analysis more complicated.

A merge commit can contain changes from multiple parents.

Inspect:

```bash
git show --summary <merge-commit>
```

Show parents:

```bash
git rev-list --parents -n 1 <merge-commit>
```

Inspect first-parent history:

```bash
git log --first-parent
```

If the regression is related to a merge, determine whether the problem was introduced:

* In the merge itself
* In the first parent
* In the merged branch
* By an interaction between changes

For complex merge histories, bisect may require more careful interpretation.

---

# 18.32 Bisecting Large Histories

For a large repository:

```text
1000 commits
```

binary search needs approximately:

```text
log2(1000) ≈ 10
```

tests.

For:

```text
10,000 commits
```

approximately:

```text
log2(10000) ≈ 14
```

tests.

This is why bisect is powerful for long histories.

The quality of the result still depends on having reliable good/bad classifications.

---

# 18.33 CI/CD and DevOps Use Cases

`git bisect` can help investigate:

* Build regressions
* Test regressions
* Container build failures
* Deployment failures
* API behavior changes
* Performance regressions
* Dependency-related regressions
* Infrastructure-as-code changes
* Configuration regressions

Example CI regression test:

```bash
#!/usr/bin/env bash

set -e

docker build -t regression-test .
docker run --rm regression-test ./run-regression-tests.sh
```

Then:

```bash
git bisect run ./ci-regression-test.sh
```

Be careful with tests that depend on:

* External services
* Current time
* Network state
* Mutable databases
* Non-deterministic infrastructure
* Production systems

---

# 18.34 Practical Bisect Workflows

## Workflow A — Manual Regression Investigation

```bash
git status

git bisect start

git bisect bad HEAD

git bisect good v2.0.0

# Test candidate

./run-tests.sh

git bisect good
```

or:

```bash
./run-tests.sh

git bisect bad
```

Repeat until Git identifies the first bad commit.

Then:

```bash
git show <commit>
git bisect reset
```

---

## Workflow B — Automated Regression Test

```bash
git bisect start
git bisect bad HEAD
git bisect good v2.0.0

git bisect run ./regression-test.sh

git bisect reset
```

---

## Workflow C — Build Regression

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.9.0

git bisect run make

git bisect reset
```

---

## Workflow D — Test Regression

```bash
git bisect start
git bisect bad HEAD
git bisect good v3.0.0

git bisect run pytest tests/test_api.py

git bisect reset
```

---

## Workflow E — Untestable Commits

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Candidate cannot be tested

git bisect skip
```

Continue until the search completes.

---

# 18.35 High-Value Bisect Commands

| Command                     | Description                  | Example                        | Branch State Before and After command | Output             |
| --------------------------- | ---------------------------- | ------------------------------ | ------------------------------------- | ------------------ |
| `git bisect start`          | Start bisect                 | `git bisect start`             | Bisect begins                         | Session state      |
| `git bisect start BAD GOOD` | Start with boundaries        | `git bisect start HEAD v1.0.0` | Git enters bisect state               | Candidate selected |
| `git bisect bad`            | Mark current commit bad      | `git bisect bad`               | Search range narrowed                 | Next candidate     |
| `git bisect bad COMMIT`     | Mark specified commit bad    | `git bisect bad HEAD`          | Search range narrowed                 | Candidate          |
| `git bisect good`           | Mark current commit good     | `git bisect good`              | Search range narrowed                 | Next candidate     |
| `git bisect good COMMIT`    | Mark specified commit good   | `git bisect good v1.0.0`       | Search range narrowed                 | Candidate          |
| `git bisect skip`           | Skip candidate               | `git bisect skip`              | Candidate skipped                     | Next candidate     |
| `git bisect status`         | Show session state           | `git bisect status`            | Unchanged                             | Bisect state       |
| `git bisect log`            | Show decisions               | `git bisect log`               | Unchanged                             | Decision log       |
| `git bisect run CMD`        | Automate testing             | `git bisect run ./test.sh`     | Multiple candidates tested            | First bad commit   |
| `git bisect replay FILE`    | Replay a log                 | `git bisect replay bisect.log` | Session recreated                     | Replay result      |
| `git bisect reset`          | End bisect                   | `git bisect reset`             | Original state restored               | Reset message      |
| `git show COMMIT`           | Inspect identified commit    | `git show a1b2c3d`             | Unchanged                             | Commit diff        |
| `git show --stat COMMIT`    | Show changed-file statistics | `git show --stat a1b2c3d`      | Unchanged                             | File statistics    |

---

# 18.36 Bisect Cheat Sheet

## Manual

```bash
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

# Test candidate

git bisect good
# or
git bisect bad

# Repeat until Git finds the first bad commit

git show <bad-commit>
git bisect reset
```

---

## Automated

```bash
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

git bisect run ./test.sh

git bisect reset
```

---

## Skip

```bash
git bisect skip
```

---

## Inspect State

```bash
git bisect status
```

---

## View Decisions

```bash
git bisect log
```

---

## Save Decisions

```bash
git bisect log > bisect.log
```

---

## Replay Decisions

```bash
git bisect replay bisect.log
```

---

## Inspect Result

```bash
git show <commit>
```

---

## Complete Recovery-Oriented Workflow

```bash
git status

git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

# Candidate 1
./test.sh
git bisect good

# Candidate 2
./test.sh
git bisect bad

# Candidate 3
./test.sh
git bisect good

# Continue...

git bisect status
git bisect log

# After Git identifies the first bad commit
git show <commit>

# Exit bisect mode
git bisect reset
```

---

# Git Bisect Golden Rules

```text
1. Choose a genuinely known-good commit.
2. Choose a genuinely known-bad commit.
3. Use the same test criteria for every candidate.
4. Avoid flaky tests.
5. Use git bisect skip when a commit cannot be tested.
6. Save git bisect log for important investigations.
7. Inspect the identified commit with git show.
8. Always exit the session with git bisect reset.
9. Do not modify unrelated files during the investigation.
10. Prefer automation when the regression can be reliably tested by a script.
```

The core commands to remember are:

```bash
git bisect start
git bisect bad
git bisect good <commit>
git bisect skip
git bisect status
git bisect log
git bisect run <command>
git bisect reset
```

The essential mental model is:

```text
Known GOOD
    |
    +-------------------------------+
                                    |
                              Git chooses
                              midpoint
                                    |
                                    v
                              Test candidate
                               /          \
                            GOOD          BAD
                              |             |
                              v             v
                         Search half    Search half
                              \             /
                               \           /
                                \         /
                                 v       v
                              First BAD
                                COMMIT
```

---

## Next Part

**Next file:** `19-submodules.md`

[Next: Submodules](19-submodules.md)
