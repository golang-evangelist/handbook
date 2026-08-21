# ⚙️ Parallel Tests (`t.Parallel`)

> Chapter 6 of the **Go Testing Handbook**

---

# 📖 Introduction

As projects grow, the number of tests usually grows as well.

A small package may contain:

```text
10 tests
```

A medium-sized project may contain:

```text
300 tests
```

A large production application can easily contain:

```text
10,000+ tests
```

Running all tests sequentially may become increasingly time-consuming.

Fortunately, Go's testing framework provides built-in support for **parallel execution**, allowing independent tests to run simultaneously and reducing the overall execution time.

---

# 🎯 Learning Objectives

By the end of this chapter, you will understand:

- what parallel tests are,
- how `t.Parallel()` works,
- when parallel execution is beneficial,
- when it should be avoided,
- common mistakes,
- best practices for writing safe parallel tests.

---

# 🤔 What Are Parallel Tests?

By default, Go executes tests **sequentially**.

Conceptually:

```text
TestA

↓

TestB

↓

TestC

↓

TestD
```

Only after one test completes does the next one begin.

When tests are independent, this sequential execution can waste available CPU resources.

Parallel tests allow multiple tests to execute concurrently.

```text
TestA ─────┐
           │
TestB ─────┤
           │──── Running
TestC ─────┤
           │
TestD ─────┘
```

If the machine has multiple CPU cores, this can significantly reduce the total test execution time.

---

# 🧠 What Does `t.Parallel()` Do?

Calling:

```go
t.Parallel()
```

marks the current test as eligible for parallel execution.

The Go test runner coordinates the scheduling.

For example:

```go
func TestAdd(t *testing.T) {
	t.Parallel()

	// test logic
}
```

This tells the testing framework:

> "This test does not depend on other tests and may run concurrently with other parallel tests."

---

# 🏗 Sequential vs Parallel Execution

Consider four independent tests.

Sequential execution:

```text
Time

▲
│
│
├── TestA
│
├────────── TestB
│
├────────────────── TestC
│
├────────────────────────── TestD
│
└──────────────────────────────────►
```

Parallel execution:

```text
Time

▲
│
│
├──────── TestA ────────┐
│                       │
├──────── TestB ────────┤
│                       │
├──────── TestC ────────┤
│                       │
├──────── TestD ────────┘
│
└──────────────────────────────────►
```

Notice how multiple tests overlap.

The total runtime can be much shorter.

---

# 📦 The Simplest Example

```go
func TestAdd(t *testing.T) {
	t.Parallel()

	got := Add(2, 3)

	if got != 5 {
		t.Fatal("unexpected result")
	}
}
```

A second test:

```go
func TestMultiply(t *testing.T) {
	t.Parallel()

	got := Multiply(2, 3)

	if got != 6 {
		t.Fatal("unexpected result")
	}
}
```

Both tests may execute at the same time.

---

# 🔍 What Happens Internally?

The execution process is roughly:

```text
Run Test

↓

Call t.Parallel()

↓

Pause Test

↓

Scheduler Collects Parallel Tests

↓

Resume Together

↓

Execute Concurrently
```

A subtle but important detail is that `t.Parallel()` does **not** immediately start parallel execution.

Instead, it informs the testing framework that the test may be scheduled alongside other parallel tests.

---

# 📈 Why Use Parallel Tests?

Imagine a package containing:

```text
200 independent tests
```

If each test takes:

```text
50 ms
```

Sequential execution:

```text
200 × 50 ms

=

10 seconds
```

With multiple CPU cores, parallel execution can reduce the total runtime considerably.

The exact improvement depends on:

- CPU resources,
- workload,
- synchronization,
- operating system scheduling.

---

# 🧪 Parallel Subtests

Parallel execution also works with **Subtests**.

Example:

```go
func TestAdd(t *testing.T) {
	tests := []struct {
		name     string
		a        int
		b        int
		expected int
	}{
		{
			name:     "positive",
			a:        2,
			b:        3,
			expected: 5,
		},
		{
			name:     "negative",
			a:        -2,
			b:        -3,
			expected: -5,
		},
	}

	for _, tc := range tests {
		tc := tc

		t.Run(tc.name, func(t *testing.T) {
			t.Parallel()

			got := Add(tc.a, tc.b)

			if got != tc.expected {
				t.Fatalf(
					"expected %d, got %d",
					tc.expected,
					got,
				)
			}
		})
	}
}
```

Each subtest becomes eligible for parallel execution.

This combination—**Table-Driven Testing + Subtests + Parallel Tests**—is common in modern Go codebases.

---

# 🎯 When Are Parallel Tests Beneficial?

Parallel tests are particularly effective when individual tests are:

- independent,
- deterministic,
- CPU-bound,
- read-only,
- free of shared mutable state.

Examples include:

- mathematical functions,
- parsers,
- validators,
- encoders,
- decoders,
- string manipulation,
- business rule evaluation.

These tests rarely interfere with one another.

---

# ⚠️ Independence Is the Key Requirement

Before enabling parallel execution, ask yourself:

> **"Can this test influence another test?"**

If the answer is:

```text
No
```

then the test is usually a good candidate for `t.Parallel()`.

If the answer is:

```text
Yes
```

parallel execution may introduce intermittent failures and race conditions.

---

> **Best Practice**
>
> Only mark a test as parallel when it is completely independent of every other test.
>
> Parallel execution should improve performance **without changing the correctness or determinism of the test suite**.

---

# ⚠️ When Should You NOT Use `t.Parallel()`?

Although parallel execution can significantly reduce the runtime of a test suite, **not every test is a good candidate**.

The most important rule is:

> **Parallel tests must be independent.**

If multiple tests interact with the same mutable resource, running them concurrently may produce unpredictable results.

Common shared resources include:

- files,
- databases,
- environment variables,
- global variables,
- network ports,
- caches,
- shared in-memory state.

Whenever tests modify shared state, parallel execution requires careful synchronization—or should simply be avoided.

---

# 🚫 Shared Global Variables

Consider the following example.

```go
var counter int

func TestIncrement(t *testing.T) {
	t.Parallel()

	counter++
}

func TestReset(t *testing.T) {
	t.Parallel()

	counter = 0
}
```

Both tests modify the same variable.

Possible execution:

```text
TestIncrement

↓

counter++

↓

TestReset

↓

counter = 0
```

Or:

```text
TestReset

↓

counter = 0

↓

TestIncrement

↓

counter++
```

The final result depends on scheduling.

This makes the tests unreliable.

---

# 🚫 Shared Files

Suppose two tests write to the same file.

```go
func TestWriteA(t *testing.T) {
	t.Parallel()

	os.WriteFile("output.txt", ...)
}
```

```go
func TestWriteB(t *testing.T) {
	t.Parallel()

	os.WriteFile("output.txt", ...)
}
```

Possible outcome:

```text
Test A

↓

Overwrite File

↓

Test B

↓

Overwrite Again
```

The file content now depends on execution order.

Instead, each test should use its own temporary file.

For example:

```go
tmp := t.TempDir()
```

or create uniquely named files inside a temporary directory.

---

# 🚫 Shared Database State

Database tests often look independent but may not be.

Example:

```go
func TestCreateUser(t *testing.T) {
	t.Parallel()

	// INSERT user
}
```

```go
func TestDeleteUser(t *testing.T) {
	t.Parallel()

	// DELETE user
}
```

If both tests operate on the same records, they may interfere with each other.

Safer approaches include:

- isolated test databases,
- database transactions,
- automatic rollback,
- unique test data,
- separate schemas.

---

# 🚫 Environment Variables

Environment variables are global to the process.

Incorrect:

```go
func TestProduction(t *testing.T) {
	t.Parallel()

	os.Setenv("MODE", "production")
}
```

```go
func TestDevelopment(t *testing.T) {
	t.Parallel()

	os.Setenv("MODE", "development")
}
```

The tests race to modify the same value.

Instead, avoid shared environment modifications or use APIs such as `t.Setenv()` where appropriate, while remembering that changes to process-wide state still require careful consideration when tests run concurrently.

---

# 🚫 Fixed Network Ports

Consider:

```go
func TestHTTPServerA(t *testing.T) {
	t.Parallel()

	http.ListenAndServe(":8080", ...)
}
```

```go
func TestHTTPServerB(t *testing.T) {
	t.Parallel()

	http.ListenAndServe(":8080", ...)
}
```

Only one process can bind to the port.

The second test fails.

Better options include:

- letting the operating system choose an available port,
- using `httptest.Server`,
- allocating unique ports for each test.

---

# 🧠 Race Conditions

Parallel execution often exposes hidden race conditions.

Conceptually:

```text
Shared Value

↓

Test A Reads

↓

Test B Writes

↓

Test A Writes

↓

Unexpected Result
```

The outcome depends on timing rather than logic.

Race conditions are notoriously difficult to reproduce consistently.

---

# 🔍 Detecting Data Races

Go provides a built-in race detector.

Run your tests with:

```bash
go test -race
```

The race detector monitors memory accesses and reports conflicting reads and writes.

Example output:

```text
WARNING: DATA RACE

Read at ...

Previous write at ...
```

Whenever you introduce parallel execution, running the test suite with `-race` is highly recommended.

---

# 📊 Safe vs Unsafe Parallel Tests

| Safe Candidates | Unsafe Candidates |
|-----------------|-------------------|
| Pure functions | Global mutable variables |
| String utilities | Shared files |
| Parsers | Shared databases |
| Validators | Shared caches |
| Mathematical functions | Environment variables |
| Read-only logic | Fixed network ports |

A useful rule of thumb:

> If a test modifies shared state, think carefully before making it parallel.

---

# 🧪 Parallel Table-Driven Tests

The following pattern is commonly seen in production Go code.

```go
func TestReverse(t *testing.T) {
	tests := []struct {
		name     string
		input    string
		expected string
	}{
		{
			name:     "empty",
			input:    "",
			expected: "",
		},
		{
			name:     "word",
			input:    "Go",
			expected: "oG",
		},
	}

	for _, tc := range tests {
		tc := tc

		t.Run(tc.name, func(t *testing.T) {
			t.Parallel()

			got := Reverse(tc.input)

			if got != tc.expected {
				t.Fatalf(
					"expected %q, got %q",
					tc.expected,
					got,
				)
			}
		})
	}
}
```

This approach combines:

- Table-Driven Testing,
- Subtests,
- Parallel execution.

When each test case is independent, this pattern provides excellent scalability and fast execution.

---

# 💡 Best Practices

Experienced Go developers generally follow these guidelines when using `t.Parallel()`:

- Use it only for independent tests.
- Avoid shared mutable state.
- Prefer temporary resources over shared resources.
- Run the test suite with `go test -race`.
- Use descriptive Subtest names.
- Keep test setup deterministic.
- Ensure that tests can execute in any order.
- Treat intermittent failures as a warning sign.

---

# ⚠️ Common Beginner Mistakes

## ❌ Making Every Test Parallel

Not every test benefits from parallel execution.

Correctness and reliability always take precedence over speed.

---

## ❌ Ignoring Shared State

Parallel execution frequently reveals assumptions that were hidden during sequential execution.

If tests unexpectedly fail only sometimes, investigate shared state first.

---

## ❌ Forgetting the Loop Variable

When writing Table-Driven Tests with Subtests, remember to create a local copy of the loop variable for compatibility with older Go versions and legacy code:

```go
for _, tc := range tests {
	tc := tc

	t.Run(tc.name, func(t *testing.T) {
		t.Parallel()

		// test logic
	})
}
```

As discussed in the Subtests chapter, Go 1.22 changed `for ... range` loop variable semantics, but this pattern remains common and is still worth understanding.

---

# 📝 Chapter Summary

Parallel Tests allow Go to execute independent tests concurrently, reducing the overall runtime of large test suites.

The key ideas from this chapter are:

- `t.Parallel()` marks a test or subtest as eligible for concurrent execution.
- Parallel execution improves performance for independent workloads.
- Tests must not interfere through shared mutable state.
- Common shared resources include files, databases, global variables, environment variables, and network ports.
- The `go test -race` command helps detect data races introduced by concurrent execution.
- Table-Driven Tests, Subtests, and Parallel Tests work exceptionally well together when each test case is independent.

When used appropriately, `t.Parallel()` can significantly accelerate a test suite while preserving correctness. When used carelessly, however, it can introduce flaky tests that are difficult to diagnose. Understanding the distinction is an essential skill for every Go developer.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Fuzz Testing`](07-fuzz-testing.md)**

In the next part, we will cover:

* what Fuzz Testing is,
* why it is useful,
* how fuzzing differs from unit testing,
* how Go performs fuzzing,
* how to write fuzz tests,
* how seed inputs work,
* how Go discovers unexpected failures,
* common mistakes,
* best practices.