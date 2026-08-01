# 🌿 Subtests (`t.Run`)

> Chapter 2 of the **Go Testing Handbook**

---

# 📖 Introduction

In the previous chapter, we learned how **Table-Driven Testing** eliminates duplicated test logic by treating test cases as data.

However, there is one limitation in the implementation we used.

Consider the following example:

```go
func TestAdd(t *testing.T) {
	tests := []struct {
		name     string
		a        int
		b        int
		expected int
	}{
		{
			name:     "positive numbers",
			a:        2,
			b:        3,
			expected: 5,
		},
		{
			name:     "negative numbers",
			a:        -2,
			b:        -3,
			expected: -5,
		},
	}

	for _, tc := range tests {
		got := Add(tc.a, tc.b)

		if got != tc.expected {
			t.Fatalf(
				"%s: expected %d, got %d",
				tc.name,
				tc.expected,
				got,
			)
		}
	}
}
```

This test is already much better than writing multiple independent test functions.

Nevertheless, it still has an important drawback.

---

# 🤔 The Problem

Imagine that one of the test cases fails.

The output might look like this:

```text
--- FAIL: TestAdd
    add_test.go:27:
        negative numbers: expected -5, got -4

FAIL
```

Although the error message includes the test case name, Go still reports only **one failed test**:

```text
TestAdd
```

From the perspective of the Go testing framework, everything inside the loop belongs to a single test function.

---

# 🎯 What Are Subtests?

A **Subtest** is a test that executes inside another test.

Instead of treating all scenarios as one large test, Go allows every scenario to become its own independent test.

Conceptually:

```text
Without Subtests

TestAdd
│
├── Scenario 1
├── Scenario 2
├── Scenario 3
└── Scenario 4
```

With Subtests:

```text
TestAdd
│
├── positive numbers
├── negative numbers
├── zero value
└── large numbers
```

Each scenario now has its own identity.

---

# 🧠 The `t.Run` Function

Subtests are created using the `Run` method provided by the `testing.T` type.

Its basic form is:

```go
t.Run(name, func(t *testing.T) {

})
```

The two arguments are:

- a descriptive test name,
- a function containing the test logic.

Every call to `t.Run` creates a brand-new test.

---

# 📦 The Simplest Example

```go
func TestMath(t *testing.T) {
	t.Run("addition", func(t *testing.T) {
		// test addition
	})

	t.Run("subtraction", func(t *testing.T) {
		// test subtraction
	})
}
```

Visually:

```text
TestMath
│
├── addition
└── subtraction
```

Although there is only one top-level test function, Go internally treats these as separate tests.

---

# 🔄 Combining Table-Driven Testing with Subtests

This is where the true power of Go testing becomes apparent.

Instead of executing each table entry directly, we wrap it in `t.Run`.

Without Subtests:

```go
for _, tc := range tests {
	got := Add(tc.a, tc.b)

	if got != tc.expected {
		t.Fatalf("expected %d, got %d", tc.expected, got)
	}
}
```

With Subtests:

```go
for _, tc := range tests {
	t.Run(tc.name, func(t *testing.T) {
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
```

Notice that the only structural difference is the call to `t.Run`.

This small change significantly improves how tests are organized and reported.

---

# 📊 Test Hierarchy

After introducing Subtests, the execution hierarchy becomes:

```text
TestAdd
│
├── positive numbers
│
├── negative numbers
│
├── zero value
│
└── large numbers
```

Instead of one anonymous loop, each scenario becomes an individually named test.

---

# 🧪 Real Example

```go
func TestAdd(t *testing.T) {
	tests := []struct {
		name     string
		a        int
		b        int
		expected int
	}{
		{
			name:     "positive numbers",
			a:        2,
			b:        3,
			expected: 5,
		},
		{
			name:     "negative numbers",
			a:        -2,
			b:        -3,
			expected: -5,
		},
		{
			name:     "zero value",
			a:        0,
			b:        10,
			expected: 10,
		},
	}

	for _, tc := range tests {
		t.Run(tc.name, func(t *testing.T) {
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

This pattern is so common that many Go developers consider it the default way to write Table-Driven Tests.

---

# 📈 Improved Test Output

One of the biggest advantages becomes obvious when running the tests.

Instead of:

```text
--- FAIL: TestAdd
```

Go now reports:

```text
=== RUN   TestAdd
=== RUN   TestAdd/positive_numbers
=== RUN   TestAdd/negative_numbers
=== RUN   TestAdd/zero_value
```

If one scenario fails:

```text
=== RUN   TestAdd/negative_numbers

--- FAIL: TestAdd/negative_numbers
```

The failing case is immediately visible.

No manual searching is required.

---

# 💡 Why This Matters

Imagine a table containing 150 test cases.

Without Subtests:

```text
FAIL TestParser
```

You must inspect the error message to determine which scenario failed.

With Subtests:

```text
FAIL TestParser/missing_header
```

The failing scenario is immediately identified by its name.

This makes debugging significantly faster.

---

# ✅ Benefits of Subtests

Using `t.Run` provides several advantages:

| Benefit | Description |
|----------|-------------|
| Better reporting | Every scenario appears as an individual test. |
| Easier debugging | Failing cases are immediately identifiable. |
| Improved readability | Test output mirrors the logical structure of the test. |
| Better tooling support | IDEs and CI systems recognize individual subtests. |
| Foundation for advanced features | Parallel execution and selective execution build on Subtests. |

---

> **Best Practice**
>
> Whenever a Table-Driven Test contains a descriptive `name` field, that name should almost always be used as the first argument to `t.Run`.
>
> This makes test output self-documenting and greatly improves maintainability.

---

# 🔍 Understanding the `t.Run` Function

At first glance, `t.Run` may appear to be nothing more than a helper function that executes another function.

In reality, it does much more than that.

When you call `t.Run`, the Go testing framework:

- creates a new subtest,
- assigns it a unique name,
- tracks its execution independently,
- records whether it passed or failed,
- includes it in the final test report,
- enables selective execution,
- allows parallel execution (which will be covered in a later chapter).

Conceptually, every call to `t.Run` creates a child test.

```text
Parent Test
     │
     ▼
+---------------------+
|      TestAdd        |
+---------------------+
     │
     ├─────────────┐
     ▼                     ▼
positive                negative
     │                     │
     ▼                     ▼
 PASS                     FAIL
```

Although all subtests belong to the same parent test, each one is treated as an independent testing unit.

---

# 🧠 Anatomy of `t.Run`

The function signature is:

```go
func (t *T) Run(name string, f func(t *T)) bool
```

Let's break it down.

## The `name`

```go
t.Run("positive numbers", ...)
```

This is the name that appears in test output.

For example:

```text
=== RUN   TestAdd/positive numbers
```

A descriptive name makes debugging much easier.

---

## The Callback Function

```go
func(t *testing.T) {

}
```

This function contains the logic for exactly one subtest.

Every invocation receives its own `*testing.T`.

That means each subtest has its own:

- failure state,
- log output,
- cleanup functions,
- execution context.

Subtests are therefore isolated from one another.

---

## The Return Value

Many developers overlook the return value of `t.Run`.

```go
ok := t.Run(...)
```

The function returns:

```text
true
```

if the subtest passed, and

```text
false
```

if the subtest failed.

Although most tests ignore this value, it can be useful in advanced scenarios where later execution depends on the success of earlier subtests.

---

# 🌳 Parent Tests vs Child Tests

Consider the following hierarchy.

```go
func TestMath(t *testing.T) {
	t.Run("addition", func(t *testing.T) {})

	t.Run("subtraction", func(t *testing.T) {})
}
```

The hierarchy looks like this:

```text
TestMath
│
├── addition
│
└── subtraction
```

Notice that `TestMath` itself contains no assertions.

Its primary responsibility is organizing and executing child tests.

This is a common and recommended pattern.

---

# 📋 Organizing Large Test Suites

Imagine testing a package that validates user input.

Without Subtests you might write:

```text
TestValidEmail
TestInvalidEmail
TestEmptyEmail
TestWhitespaceEmail
TestLongEmail
TestUnicodeEmail
```

With Subtests:

```text
TestEmailValidation
│
├── valid email
├── invalid email
├── empty email
├── whitespace
├── unicode
└── long email
```

The second structure is usually easier to navigate because all related scenarios are grouped under one parent.

---

# 🎯 Why `name` Is So Important

Consider these two tables.

Poor naming:

```go
tests := []struct {
	name string
}{
	{"case1"},
	{"case2"},
	{"case3"},
}
```

Test output:

```text
FAIL TestParser/case2
```

This tells us almost nothing.

Now compare it with descriptive names.

```go
tests := []struct {
	name string
}{
	{"empty input"},
	{"missing delimiter"},
	{"invalid UTF-8"},
}
```

Output:

```text
FAIL TestParser/invalid UTF-8
```

Immediately, the failing scenario becomes obvious.

---

> **Best Practice**
>
> A test case name should describe **the behavior being verified**, not simply identify the case with a number.
>
> Good names explain *what* is being tested.

---

# 📦 Subtests and Table-Driven Testing

These two concepts complement each other perfectly.

Table-Driven Testing answers the question:

> "How do I organize many test scenarios?"

Subtests answer:

> "How do I execute and report each scenario independently?"

Together they produce one of the most recognizable Go testing patterns.

```text
Test Function
      │
      ▼
Table of Test Cases
      │
      ▼
for ... range
      │
      ▼
t.Run(...)
      │
      ▼
Assertions
```

This structure is used extensively throughout the Go standard library and many open-source Go projects.

---

# ⚠️ A Very Common Mistake: Capturing the Loop Variable

One of the most common mistakes when using `t.Run` involves the loop variable.

Consider this code:

```go
for _, tc := range tests {
	t.Run(tc.name, func(t *testing.T) {
		got := Add(tc.a, tc.b)

		if got != tc.expected {
			t.Fatalf("unexpected result")
		}
	})
}
```

At first glance, it looks perfectly correct.

However, when subtests are later executed in parallel (using `t.Parallel()`), this pattern can lead to unexpected behavior because the anonymous function closes over the loop variable.

The recommended solution is to create a new variable inside the loop.

```go
for _, tc := range tests {
	tc := tc

	t.Run(tc.name, func(t *testing.T) {
		got := Add(tc.a, tc.b)

		if got != tc.expected {
			t.Fatalf("unexpected result")
		}
	})
}
```

This ensures that each subtest works with its own copy of the test case.

> **Note**
>
> Starting with **Go 1.22**, the semantics of loop variables in `for ... range` were changed, eliminating this particular issue in many common cases.
>
> Nevertheless, you will still encounter the `tc := tc` pattern in a large amount of existing code, older Go versions, and many educational resources. Understanding why it existed remains valuable, especially when maintaining legacy projects.

---

# 📚 Selectively Running Subtests

One major advantage of Subtests is that they integrate with the `go test` command.

Suppose we have:

```text
TestParser
│
├── empty input
├── invalid header
├── malformed body
└── unicode input
```

Instead of running the entire test suite, we can execute only specific tests or subtests.

For example:

```bash
go test -run TestParser
```

or a specific subtest:

```bash
go test -run "TestParser/empty input"
```

This is extremely useful when debugging a single scenario.

---

# 📊 Comparing Traditional Tests and Subtests

| Feature | Traditional Tests | Subtests (`t.Run`) |
|---------|-------------------|--------------------|
| Independent reporting | ❌ | ✅ |
| Descriptive hierarchy | ❌ | ✅ |
| Selective execution | Limited | ✅ |
| Natural fit for Table-Driven Testing | ❌ | ✅ |
| Parallel execution support | Limited | ✅ |

---

# 📝 Summary

Subtests extend the power of Table-Driven Testing by turning each table entry into an independently managed test.

The key ideas introduced in this chapter are:

- `t.Run` creates a child test.
- Each subtest has its own execution context.
- Descriptive names greatly improve test output.
- Subtests organize related scenarios under a common parent.
- They integrate seamlessly with Table-Driven Testing.
- They enable selective execution using `go test -run`.
- They provide the foundation for parallel test execution, which will be covered in a later chapter.

Mastering `t.Run` is an important milestone in becoming proficient with Go's testing framework, as it unlocks cleaner organization, better reporting, and more scalable test suites.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Test Helpers (`t.Helper`)`](03-test-helpers.md)**

In the next part, we will cover:

* Helper functions reduce duplicated test logic.
* Helpers that report failures should call `t.Helper()`.
* `t.Helper()` hides helper stack frames from failure reports.
* The reported file and line number point to the calling test instead of the helper.
* Multiple nested helpers can all call `t.Helper()`.
* Small, focused helpers improve readability and maintainability.