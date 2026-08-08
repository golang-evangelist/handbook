# 📊 Coverage Analysis

> Chapter 4 of the **Go Testing Handbook**

---

# 📖 Introduction

Writing tests is an essential part of software development.

However, simply having tests is **not enough**.

A natural question follows:

> **"How much of my code is actually being tested?"**

This is precisely the question that **Coverage Analysis** answers.

Coverage Analysis helps developers understand which parts of the source code are executed by tests and which parts have never been exercised.

It is one of the most useful tools for evaluating the completeness of a test suite.

---

# 🎯 Learning Objectives

By the end of this chapter, you will understand:

- what code coverage is,
- how Go measures coverage,
- how to generate coverage reports,
- how to interpret coverage percentages,
- common misconceptions,
- limitations of coverage analysis,
- best practices for improving test quality.

---

# 🤔 What Is Code Coverage?

Code Coverage measures **how much of your source code is executed while running your tests**.

Imagine the following function:

```go
func Max(a, b int) int {
	if a > b {
		return a
	}

	return b
}
```

This function contains two possible execution paths.

```text
        Max()
          │
          │
        a > b ?
         / \  
        /   \
     Yes     No
      │       │
      ▼       ▼
 return a  return b
```

If your tests only verify:

```go
Max(10, 5)
```

then only the **left branch** is executed.

The other branch remains untested.

Coverage analysis identifies situations like this.

---

# 🧠 What Does "Covered" Mean?

A line of code is considered **covered** if it is executed during at least one test.

For example:

```go
func Abs(x int) int {
	if x < 0 {
		return -x
	}

	return x
}
```

Suppose your tests are:

```go
Abs(10)
```

Execution:

```text
Abs()

↓

if x < 0

↓

false

↓

return x
```

The `return -x` statement is never executed.

Therefore, that part of the function is **not covered**.

---

# 📦 Why Coverage Matters

Coverage analysis helps answer questions such as:

- Which functions have never been tested?
- Which branches are never executed?
- Which packages require more tests?
- Which recently added code lacks tests?
- Which refactoring introduced untested paths?

Instead of guessing where tests are missing, developers can rely on measurable information.

---

# ⚠️ A Common Misconception

Many beginners believe:

> **100% coverage means bug-free software.**

This is **false**.

Coverage tells us only that code has been executed.

It does **not** guarantee that the code was verified correctly.

Consider this example.

```go
func TestAdd(t *testing.T) {
	Add(2, 3)
}
```

The function is executed.

Coverage increases.

However, nothing is asserted.

The test would pass even if `Add` returned an incorrect result.

Coverage alone says nothing about correctness.

---

# 🎯 Coverage Measures Execution, Not Quality

Imagine two different test suites.

### Test Suite A

```go
func TestAdd(t *testing.T) {
	Add(2, 3)
}
```

Coverage:

```text
100%
```

Quality:

```text
Very poor
```

---

### Test Suite B

```go
func TestAdd(t *testing.T) {
	got := Add(2, 3)

	if got != 5 {
		t.Fatal("unexpected result")
	}
}
```

Coverage:

```text
100%
```

Quality:

```text
High
```

Both tests execute exactly the same code.

Only one actually verifies behavior.

---

> **Important**
>
> Coverage indicates **what code ran**.
>
> It does **not** indicate whether the behavior was validated correctly.

---

# 🏗 How Go Measures Coverage

When coverage is enabled, Go automatically instruments your code before compiling it.

Conceptually, the process looks like this:

```text
Source Code

↓

Instrumentation

↓

Run Tests

↓

Collect Execution Data

↓

Generate Report
```

The instrumentation process inserts invisible counters throughout the compiled code.

Every time an instrumented section executes, the corresponding counter increases.

After all tests finish, Go calculates the percentage of executed code.

---

# 📋 Types of Coverage

Different programming languages support different kinds of coverage.

Common categories include:

| Type | Description |
|------|-------------|
| Statement Coverage | Measures executed statements. |
| Branch Coverage | Measures executed decision branches. |
| Condition Coverage | Measures evaluated boolean conditions. |
| Path Coverage | Measures all possible execution paths. |

Go primarily reports **statement coverage**.

This means it measures whether individual statements have been executed during testing.

---

# 🔍 Statement Coverage

Consider the following function.

```go
func Sign(x int) int {
	if x > 0 {
		return 1
	}

	if x < 0 {
		return -1
	}

	return 0
}
```

Execution paths:

```text
            Sign()
              │
              │
              │
            x > 0 ?
             /  \  
            /    \
          Yes    No
           │      │
           ▼      ▼
     return 1   x < 0 ?
                 / \  
                /   \
              Yes    No
               │      │
               ▼      ▼
          return -1 return 0
```

If your tests verify only:

```go
Sign(10)
```

then only the first return statement is executed.

The remaining branches remain uncovered.

---

# 📦 Running Coverage Analysis

The simplest way to measure coverage is:

```bash
go test -cover
```

Example output:

```text
PASS
coverage: 87.3% of statements
ok      example/math     0.003s
```

The reported percentage indicates the proportion of executed statements.

---

# 📊 Coverage by Package

Running:

```bash
go test -cover ./...
```

executes tests for **all packages** within the current module.

Typical output:

```text
ok      example/math        coverage: 92.4%
ok      example/parser      coverage: 81.7%
ok      example/service     coverage: 96.1%
ok      example/storage     coverage: 74.5%
```

This provides a high-level overview of test completeness across the entire project.

---

# 📈 Is Higher Coverage Always Better?

Higher coverage is generally desirable because it means more code has been exercised.

However, there is an important distinction:

```text
Higher Coverage

≠

Higher Quality
```

Coverage should be viewed as a **diagnostic metric**, not as the ultimate goal.

A well-designed test suite with 85% coverage is often more valuable than a poorly designed suite reporting 100%.

---

> **Best Practice**
>
> Use coverage to **discover untested code**, not as a competition to reach 100%.
>
> Focus on verifying important behaviors, edge cases, and business rules rather than increasing the percentage at any cost.

---

# 📄 Generating a Coverage Profile

The `-cover` flag provides only a summary.

For deeper analysis, Go can generate a **coverage profile** containing detailed execution information.

The most common command is:

```bash
go test -coverprofile=coverage.out
```

Example output:

```text
PASS
coverage: 91.8% of statements
ok      github.com/example/project/internal/math     0.004s
```

After the tests finish, a new file appears in the project directory.

```text
.
├── go.mod
├── go.sum
├── coverage.out
├── main.go
└── ...
```

The file `coverage.out` stores execution data that can be processed by other Go tools.

---

# 🔍 Viewing Coverage in the Terminal

To see coverage statistics by function, use:

```bash
go tool cover -func=coverage.out
```

Example output:

```text
github.com/example/project/math/add.go:8:     Add         100.0%
github.com/example/project/math/sub.go:8:     Subtract    100.0%
github.com/example/project/math/div.go:10:    Divide       83.3%
github.com/example/project/math/max.go:7:     Max          66.7%

total:                                        (statements) 91.8%
```

This report immediately highlights functions that deserve additional tests.

---

# 🌈 Generating an HTML Coverage Report

One of Go's most useful features is its built-in HTML coverage visualization.

Generate it with:

```bash
go tool cover -html=coverage.out
```

Your default web browser will open an interactive report.

Conceptually, it looks like this:

```text
func Max(a, b int) int {

🟩 if a > b {

🟩     return a

🟥 }

🟥 return b

}
```

Where:

- 🟩 Green = executed during tests.
- 🟥 Red = never executed.

This visual representation makes it easy to identify missing test scenarios.

---

# 🧠 Reading the HTML Report

Imagine the following function.

```go
func IsEven(x int) bool {
	if x%2 == 0 {
		return true
	}

	return false
}
```

Suppose the only test is:

```go
IsEven(10)
```

The report would resemble:

```text
func IsEven(x int) bool {

🟩 if x%2 == 0 {

🟩     return true

🟥 }

🟥 return false

}
```

The uncovered branch immediately suggests the missing test:

```go
IsEven(11)
```

Coverage reports are excellent tools for discovering forgotten scenarios.

---

# 📊 Coverage Workflow

A typical development workflow looks like this:

```text
    Write Code
         │
         ▼
    Write Tests
         │
         ▼
     Run Tests
         │
         ▼
  Generate Coverage
         │
         ▼
 Inspect Missing Areas
         │
         ▼
    Add More Tests
         │
         ▼
       Repeat
```

Coverage should be part of an iterative process rather than a one-time measurement.

---

# 🎯 Using Coverage During Refactoring

Coverage becomes particularly valuable when refactoring.

Suppose you simplify a function or optimize an algorithm.

Before making changes:

1. Run the existing tests.
2. Generate a coverage report.
3. Refactor the implementation.
4. Run the tests again.
5. Verify that the same areas remain covered.

This workflow increases confidence that important behavior has not been lost.

---

# ⚠️ Chasing 100% Coverage

A common anti-pattern is treating coverage as the primary objective.

Developers may write artificial tests simply to increase the reported percentage.

For example:

```go
func TestAdd(t *testing.T) {
	Add(2, 3)
	Add(5, 7)
	Add(10, 20)
}
```

Coverage increases.

Confidence does not.

No assertions verify correctness.

---

> **Warning**
>
> Never write tests **for the purpose of increasing coverage alone**.
>
> Write tests to verify behavior.
>
> Coverage is a consequence of good testing—not its purpose.

---

# 📈 Which Code Should Receive the Most Attention?

Not all code carries the same level of risk.

For example:

```text
    Critical Business Logic

            ↑
            
       High Priority
```

```text
      Simple Getters

           ↓

      Lower Priority
```

Prioritize coverage improvements in areas such as:

- authentication,
- authorization,
- payment processing,
- financial calculations,
- security-sensitive code,
- parsers,
- business rules,
- data validation.

These areas typically benefit the most from thorough testing.

---

# 🚫 What Coverage Does NOT Measure

Coverage is often misunderstood.

It does **not** measure:

- correctness,
- code quality,
- readability,
- maintainability,
- performance,
- concurrency safety,
- absence of race conditions,
- absence of bugs.

Coverage answers only one question:

> **"Was this statement executed during testing?"**

Everything else requires additional testing techniques.

---

# 🧩 Coverage and Table-Driven Testing

Table-Driven Testing and Coverage Analysis complement each other naturally.

Imagine this table:

```go
tests := []struct {
	name     string
	input    int
	expected int
}{
	{"positive", 10, 10},
	{"negative", -5, 5},
	{"zero", 0, 0},
}
```

Each additional row exercises another execution path.

As more scenarios are added, coverage often increases organically.

This is one reason why Table-Driven Testing is so effective for achieving broad behavioral coverage without duplicating test logic.

---

# 💡 Coverage Best Practices

Experienced Go developers generally follow these guidelines:

- Measure coverage regularly.
- Review uncovered code before merging changes.
- Prefer meaningful assertions over artificial coverage.
- Focus on critical business logic.
- Include edge cases.
- Combine coverage with Table-Driven Tests and Subtests.
- Treat coverage as a diagnostic tool—not as a KPI.
- Review coverage trends over time rather than obsessing over a single percentage.

---

# 📝 Chapter Summary

Coverage Analysis helps developers understand **which parts of the codebase are exercised by tests**.

The key ideas from this chapter are:

- Coverage measures **executed statements**, not correctness.
- Go provides built-in support through the `testing` toolchain.
- `go test -cover` gives a quick summary.
- `go test -coverprofile=coverage.out` generates detailed execution data.
- `go tool cover -func` displays per-function coverage.
- `go tool cover -html` creates an interactive visual report.
- High coverage is beneficial, but meaningful assertions are even more important.
- Coverage should guide testing efforts, not dictate them.

When used appropriately, Coverage Analysis is an excellent companion to Table-Driven Testing, Subtests, and Test Helpers, helping you identify untested code and build a more reliable test suite.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Benchmark Testing`](05-benchmarks.md)**

In the next part, we will cover:

* what benchmarks are,
* how benchmark tests differ from unit tests,
* how Go executes benchmarks,
* how to write reliable benchmark functions,
* how to compare different implementations,
* how to interpret benchmark output,
* common benchmarking mistakes,
* best practices used by experienced Go developers.