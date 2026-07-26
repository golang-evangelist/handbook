# 🧪 Table-Driven Testing

> Chapter 1 of the **Go Testing Handbook**

---

# 📖 Introduction

Testing is one of the most important aspects of software development.

A well-tested application is generally:

- more reliable,
- easier to maintain,
- safer to refactor,
- less likely to contain regressions,
- easier for new developers to understand.

The Go programming language was designed with testing in mind.

Unlike many programming languages where testing relies heavily on external frameworks, Go provides a powerful testing framework as part of the standard library.

```text
Go Standard Library

├── testing
├── fuzzing
├── benchmarking
└── coverage
```

One of the most recognizable testing styles in the Go ecosystem is **Table-Driven Testing**.

If you browse popular Go projects such as Kubernetes, Docker, Prometheus, HashiCorp projects, or the Go standard library itself, you will quickly notice that this testing approach appears almost everywhere.

Understanding it is therefore one of the most valuable skills a Go developer can acquire.

Table-Driven Testing is one of the most important and widely used testing patterns in the Go programming language.

It is not a special Go feature or a testing framework. Instead, it is a **testing design pattern** that uses Go's native data structures (mainly slices of structs) to define multiple test scenarios in a clean, readable, and maintainable way.

The core idea is simple:

> Instead of writing many separate test functions for every scenario, define your test cases as data and execute the same test logic against all of them.

This approach is considered one of the most idiomatic ways of writing tests in Go.

---

# 🎯 Learning Objectives

By the end of this chapter you will understand:

- what Table-Driven Testing is,
- why it exists,
- why it became the de facto testing style in Go,
- how to organize test cases,
- how to avoid duplicated test code,
- how to write readable and maintainable tests,
- when this approach should be used,
- when another testing strategy may be a better choice.

---

# 🤔 What Is Table-Driven Testing?

A traditional unit test usually looks like this:

```go
func TestAdd(t *testing.T) {
	
	got := Add(2, 3)

	if got != 5 {
		t.Fatalf("expected 5, got %d", got)
	}

}
```

This test verifies only one scenario:

```text
Input:
2 + 3

Expected result:
5
```

Now imagine that you want to test:

* positive numbers,
* negative numbers,
* zero values,
* large numbers,
* edge cases.

You might start writing:

```go
func TestAddPositive(t *testing.T) {
	// test positive numbers
}

func TestAddNegative(t *testing.T) {
	// test negative numbers
}

func TestAddZero(t *testing.T) {
	// test zero values
}

func TestAddLargeNumbers(t *testing.T) {
	// test large numbers
}
```

This works.

However, as the number of scenarios grows, problems appear.

Table-Driven Testing is a testing technique where all test scenarios are stored in a collection (usually a slice), and each scenario is executed by iterating over that collection.

Instead of writing many independent test functions, we describe the input and the expected output in a table.

The test logic itself is written only once.

The execution flow usually looks like this:

```text
       Test Cases
       
           │
           ▼
           
+----------------------+
|       Case #1        |
|       Case #2        |
|       Case #3        |
|       Case #4        |
+----------------------+

           │
           ▼
           
      for ... range
      
           │
           ▼
           
    Execute Test Logic
    
           │
           ▼
           
      Compare Result
```

This pattern eliminates duplicated code and keeps tests consistent.

---

# ⚠️ Problems With Traditional Tests

## 1. Code Duplication

Each test usually repeats the same structure:

```go
result := Function(input)

if result != expected {
	t.Fatalf(...)
}
```

Only the data changes.

The test logic itself is duplicated.

---

## 2. Difficult Maintenance

Imagine changing the error message:

Before:

```go
t.Fatalf("wrong result")
```

After:

```go
t.Fatalf("expected %v, got %v", expected, result)
```

With many duplicated tests, you must update every test manually.

---

## 3. Poor Visibility of Test Scenarios

When scenarios are separated into multiple functions:

```text
TestAddPositive
TestAddNegative
TestAddZero
TestAddLargeNumbers
```

it is harder to quickly see:

* what inputs are tested,
* what outputs are expected,
* which edge cases exist.

---

# 💡 Why Does This Pattern Exist?

Imagine testing a simple function.

```go
func Add(a, b int) int
```

A beginner often starts like this:

```go
func TestAddPositiveNumbers(t *testing.T) {
	got := Add(2, 3)

	if got != 5 {
		t.Fatal("expected 5")
	}
}

func TestAddNegativeNumbers(t *testing.T) {
	got := Add(-2, -3)

	if got != -5 {
		t.Fatal("expected -5")
	}
}

func TestAddZero(t *testing.T) {
	got := Add(0, 10)

	if got != 10 {
		t.Fatal("expected 10")
	}
}
```

At first glance, everything seems fine.

However, look closely.

Every test repeats exactly the same sequence:

1. Prepare input.
2. Execute the function.
3. Compare the result.
4. Report an error.

Only the values are different.

This duplication becomes increasingly problematic as the number of test cases grows.

---

# ❌ The Problem with Repetition

Imagine that your function needs to be tested with:

- positive numbers,
- negative numbers,
- zero,
- maximum integer,
- minimum integer,
- random values,
- overflow scenarios,
- edge cases,
- invalid inputs.

Instead of three tests, you may end up writing twenty or fifty nearly identical functions.

Your test file begins to look like this:

```text
TestAddPositive()

TestAddNegative()

TestAddZero()

TestAddLargeNumbers()

TestAddMinInt()

TestAddMaxInt()

TestAddOverflow()

TestAddRandom()

...
```

Most of the code is duplicated.

Only the input values change.

This is exactly the problem that Table-Driven Testing solves.

---

# 🧠 The Core Idea

Instead of duplicating the testing logic, separate:

- **the data**, and
- **the algorithm that executes the test**.

Visually:

```text
Traditional Testing

Test #1
    │
    ├── Setup
    ├── Execute
    └── Verify

Test #2
    │
    ├── Setup
    ├── Execute
    └── Verify

Test #3
    │
    ├── Setup
    ├── Execute
    └── Verify
```

Notice how the same steps are repeated over and over again.

Now compare that to the Table-Driven approach:

```text
        Test Cases
        
            │
            ▼
     
+------------------------+
| Input                  |
| Expected Output        |
+------------------------+

            │
            ▼

     Single Test Loop
    
            │
            ▼

        Assertions
```

The logic exists only once.

The data changes.

Instead of writing many functions, we define our scenarios as a table.

Example:

```go
tests := []struct {
	name string
	a    int
	b    int
	want int
}{
	{
		name: "positive numbers",
		a:    2,
		b:    3,
		want: 5,
	},
	{
		name: "negative numbers",
		a:    -2,
		b:    -3,
		want: -5,
	},
}
```

This table contains:

| Field  | Purpose                     |
| ------ | --------------------------- |
| `name` | Description of the scenario |
| `a`    | First input value           |
| `b`    | Second input value          |
| `want` | Expected result             |

Now the test logic can be executed for every row.

---

# 🏗 The "Table"

The word **table** does not necessarily mean a database table or a spreadsheet.

In Go, a "table" is typically a slice of structures.

Conceptually:

| Input A | Input B | Expected |
|----------|----------|----------|
| 2 | 3 | 5 |
| 5 | 7 | 12 |
| -2 | -3 | -5 |
| 0 | 10 | 10 |

This is simply data.

The testing algorithm reads one row at a time.

---

# 📦 Representing the Table in Go

The previous conceptual table naturally maps to a slice of structs.

```go
tests := []struct {
	a        int
	b        int
	expected int
}{
	{2, 3, 5},
	{5, 7, 12},
	{-2, -3, -5},
	{0, 10, 10},
}
```

Notice how the focus shifts away from the testing logic.

Instead, we are simply describing different scenarios.

This makes the test easier to read because the data itself tells a story.

---

# 🧩 Complete First Example

Imagine we have this function:

```go
func Add(a int, b int) int {
	return a + b
}
```

A table-driven test:

```go
func TestAdd(t *testing.T) {

	tests := []struct {
		name string
		a    int
		b    int
		want int
	}{
		{
			name: "positive numbers",
			a:    2,
			b:    3,
			want: 5,
		},
		{
			name: "negative numbers",
			a:    -2,
			b:    -3,
			want: -5,
		},
		{
			name: "zero values",
			a:    0,
			b:    0,
			want: 0,
		},
	}

	for _, tt := range tests {

		got := Add(tt.a, tt.b)

		if got != tt.want {
			t.Fatalf(
				"%s: expected %d, got %d",
				tt.name,
				tt.want,
				got,
			)
		}

	}

}
```

---

# 🔬 Breaking Down The Example

Let's analyze each part.

---

## 1. Defining The Test Table

```go
tests := []struct {
	name string
	a    int
	b    int
	want int
}{}
```

This creates a slice of anonymous structs.

Each struct represents one test case.

You can think of it as:

```text
Test Case #1
--------------
Name:
positive numbers

Input:
a = 2
b = 3

Expected:
5
```

---

## 2. Adding Test Cases

Example:

```go
{
	name: "positive numbers",
	a:    2,
	b:    3,
	want: 5,
}
```

is one row in our testing table.

Adding a new scenario does not require changing the testing logic.

You only add another row.

Example:

```go
{
	name: "large numbers",
	a:    1000000,
	b:    2000000,
	want: 3000000,
}
```

---

## 3. Executing The Test Table

The loop:

```go
for _, tt := range tests {
```

goes through every test case.

First iteration:

```text
tt = positive numbers
```

Second iteration:

```text
tt = negative numbers
```

Third iteration:

```text
tt = zero values
```

---

## 4. Running The Function

```go
got := Add(tt.a, tt.b)
```

The values come from the current table row.

Example:

```go
tt.a = 2
tt.b = 3
```

becomes:

```go
Add(2, 3)
```

---

## 5. Comparing Results

```go
if got != tt.want {
```

The actual result is compared with the expected result.

Example:

```text
Expected:
5

Received:
6
```

The test fails.

---

# 📌 Thinking in Scenarios Instead of Test Functions

A common beginner mindset is:

> "I need one test function for every scenario."

An experienced Go developer usually thinks differently:

> "I need one test function that executes many scenarios."

This change in mindset is one of the biggest conceptual shifts when learning Go testing.

Instead of asking:

> "How many test functions do I need?"

Ask:

> "How many scenarios should this function verify?"

That question naturally leads to Table-Driven Testing.

---

# 🏗 Why Is This Approach So Popular In Go?

Go encourages simplicity.

The standard testing package is intentionally small.

You do not need:

* external testing frameworks,
* complex assertion libraries,
* special annotations.

The language itself provides enough tools:

```go
testing.T
```

combined with:

```go
slices
structs
loops
```

to create powerful tests.

---

# 🌱 The Core Philosophy

Traditional testing:

```text
One test function
        |
        |
        +---- One scenario
```

Table-driven testing:

```text
One test function
        |
        |
        +---- Scenario 1
        |
        +---- Scenario 2
        |
        +---- Scenario 3
        |
        +---- Scenario 4
```

The test behavior stays the same.

Think of test cases as data instead of test functions.

---

# ✅ Benefits at a Glance

Table-Driven Testing provides several important advantages.

| Benefit | Description |
|----------|-------------|
| Less duplication | Test logic is written only once. |
| Better readability | Test cases are grouped together. |
| Easier maintenance | New scenarios are added by inserting another row. |
| Better scalability | Hundreds of cases can share the same logic. |
| Consistency | Every scenario follows the same execution path. |

These benefits explain why this pattern has become the standard approach throughout the Go ecosystem.

## Less Duplicate Code

Instead of:

```go
TestAddPositive()
TestAddNegative()
TestAddZero()
```

you have:

```go
TestAdd()
```

with multiple scenarios.

---

## Easier Expansion

Adding a new test case:

Before:

```go
func TestSomethingNew(t *testing.T)
```

After:

```go
{
	name: "new scenario",
	input: value,
	want: expected,
}
```

---

## Better Readability

All scenarios are visible in one place.

Example:

```go
tests := []struct{
	name string
	input string
	want bool
}{
	...
}
```

A developer immediately understands what behavior is expected.

---

## Easier Refactoring

The test logic exists only once.

If implementation changes, the test structure usually remains unchanged.

---

> **Best Practice**
>
> When writing tests, try to think of **test cases as data**, not as individual test functions.
>
> If multiple tests differ only by input values and expected results, they are strong candidates for Table-Driven Testing.

---

# 🧱 Designing a Good Test Table

Now that we understand the basic idea behind Table-Driven Testing, it is time to learn how to design a good test table.

Although a table can contain only input values and expected results, experienced Go developers often include additional information that makes tests easier to understand and maintain.

A well-designed test table is descriptive, readable, and easy to extend.

---

# 📋 The Simplest Test Table

A minimal table usually looks like this:

```go
tests := []struct {
	a        int
	b        int
	expected int
}{
	{2, 3, 5},
	{5, 7, 12},
	{-2, -3, -5},
}
```

This works perfectly for very small examples.

However, in real-world projects we can improve it considerably.

---

# 🏷 Adding a Name to Each Test Case

One of the first improvements is giving every test case a descriptive name.

```go
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
```

At first this may seem unnecessary.

In practice, it is one of the most useful additions you can make.

---

# 🎯 Why Test Names Matter

Suppose a test fails.

Without descriptive names, the output may only indicate that something failed during one of the iterations.

With named test cases, the failure immediately tells you which scenario produced the unexpected result.

Instead of thinking:

> "Which input caused this failure?"

you immediately know:

> "The **negative numbers** scenario failed."

This becomes even more valuable when a table contains dozens or even hundreds of test cases.

---

# 📦 Named Fields vs Positional Fields

Go allows both positional and named struct initialization.

Positional initialization:

```go
{2, 3, 5}
```

Named initialization:

```go
{
	a:        2,
	b:        3,
	expected: 5,
}
```

Although positional initialization is shorter, named fields are usually preferred in production code.

---

# 💡 Why Named Fields Are Better

Imagine extending the structure.

Originally:

```go
type testCase struct {
	a        int
	b        int
	expected int
}
```

Later you decide to add another field.

```go
type testCase struct {
	name     string
	a        int
	b        int
	expected int
}
```

With positional initialization every test case must now be rewritten.

With named fields, only the new field needs to be added where appropriate.

This makes future refactoring much safer.

---

> **Best Practice**
>
> Prefer named struct fields for medium and large test tables.
>
> The slight increase in typing is usually worth the significant improvement in readability.

---

# 🔄 Executing the Table

The real power of Table-Driven Testing comes from iterating over the table.

The execution pattern is almost always the same.

```go
for _, tc := range tests {
	got := Add(tc.a, tc.b)

	if got != tc.expected {
		t.Fatalf(
			"expected %d, got %d",
			tc.expected,
			got,
		)
	}
}
```

Notice that the testing logic appears only once.

The loop simply executes the same algorithm for every scenario.

---

# 🧠 Understanding the Loop

The loop can be visualized like this:

```text
tests

├── Case 1
│     │
│     ▼
│   Execute
│
├── Case 2
│     │
│     ▼
│   Execute
│
├── Case 3
│     │
│     ▼
│   Execute
│
└── ...
```

Every scenario follows exactly the same execution path.

Only the input data changes.

---

# 📌 The Three-Step Pattern

Almost every Table-Driven Test follows the same sequence.

```text
Read Test Case
        │
        ▼
Call Function
        │
        ▼
Verify Result
```

Regardless of what is being tested, the structure remains almost identical.

After writing enough Go tests, this pattern becomes second nature.

---

# 🧩 The Table Describes Behavior

One of the greatest strengths of Table-Driven Testing is that the table itself documents the expected behavior.

Consider this example.

```go
tests := []struct {
	name     string
	input    string
	expected bool
}{
	{
		name:     "valid email",
		input:    "john@example.com",
		expected: true,
	},
	{
		name:     "missing at sign",
		input:    "johnexample.com",
		expected: false,
	},
	{
		name:     "empty string",
		input:    "",
		expected: false,
	},
}
```

Even before reading the test logic, you already understand what the function is expected to do.

The table acts as executable documentation.

---

# 📈 Growing Without Growing the Code

Suppose tomorrow you discover five additional edge cases.

Without Table-Driven Testing you might need five more test functions.

With Table-Driven Testing you usually only add five more rows.

The testing algorithm remains exactly the same.

```text
Yesterday

3 rows

↓

Today

8 rows

↓

Tomorrow

25 rows

↓

Same testing logic
```

This is one of the main reasons why the pattern scales so well.

---

# ⚠ A Common Beginner Mistake

Some beginners write something like this:

```go
func TestPositiveNumbers(t *testing.T) {}

func TestNegativeNumbers(t *testing.T) {}

func TestZero(t *testing.T) {}

func TestLargeNumbers(t *testing.T) {}
```

Although technically correct, this approach duplicates the same execution logic many times.

When you notice that only the input values change, it is usually a sign that the scenarios belong in a single Table-Driven Test.

---

> **Important**
>
> Table-Driven Testing is not about reducing the number of test functions.
>
> It is about eliminating duplicated **test logic** while keeping individual scenarios easy to read and extend.

---

# ⚖️ Comparing Values Correctly

The last missing piece of a Table-Driven Test is verifying that the actual result matches the expected result.

For primitive values such as integers, booleans, or strings, a simple equality comparison is usually sufficient.

```go
if got != tc.expected {
	t.Fatalf("expected %v, got %v", tc.expected, got)
}
```

This approach is straightforward, efficient, and should always be preferred whenever possible.

---

# 🔍 Comparing Complex Values

Real-world applications rarely return only integers.

Functions often return:

- slices
- arrays
- maps
- structs
- nested structures
- pointers
- interfaces

Consider the following function:

```go
func Split(s string) []string
```

Suppose we want to verify this result.

```go
expected := []string{"Go", "is", "awesome"}
got := Split("Go is awesome")
```

A beginner may try this:

```go
if got != expected {
	t.Fatal("unexpected result")
}
```

Unfortunately, this code does **not compile**.

---

# 🤔 Why Doesn't It Work?

Slices in Go cannot be compared using `==` or `!=` (except against `nil`).

```go
a := []int{1, 2, 3}
b := []int{1, 2, 3}

fmt.Println(a == b)
```

Compilation error:

```text
invalid operation: a == b (slice can only be compared to nil)
```

The same limitation applies to maps.

---

# 🧠 Deep Equality

When comparing complex values, we usually care about **content**, not memory addresses.

Conceptually:

```text
Slice A

[1][2][3]

Slice B

[1][2][3]

↓

Same contents

↓

Equal
```

The Go standard library provides the `reflect.DeepEqual` function for this purpose.

---

# 📦 Using `reflect.DeepEqual`

```go
import "reflect"

if !reflect.DeepEqual(got, expected) {
	t.Fatalf(
		"expected %v, got %v",
		expected,
		got,
	)
}
```

`reflect.DeepEqual` recursively compares values and determines whether they contain equivalent data.

It supports many Go types, including:

- slices
- arrays
- maps
- structs
- nested structs
- pointers
- interfaces

---

> **Note**
>
> `reflect.DeepEqual` is convenient and widely used in tests, but it is **not always the best choice**.
>
> For production-grade projects, many teams prefer dedicated comparison libraries such as `go-cmp`, because they provide clearer output and more flexible comparison options.

---

# 📊 Choosing the Right Comparison

| Value Type | Recommended Comparison |
|------------|------------------------|
| `int` | `==` |
| `string` | `==` |
| `bool` | `==` |
| `float64` | Depends on precision requirements |
| `slice` | `reflect.DeepEqual` or `go-cmp` |
| `map` | `reflect.DeepEqual` or `go-cmp` |
| `struct` | `==` (if comparable) or `reflect.DeepEqual` |
| Nested structures | `reflect.DeepEqual` or `go-cmp` |

Choosing the simplest comparison that correctly verifies the behavior usually leads to more readable tests.

---

# 🧪 Testing Edge Cases

One of the biggest advantages of Table-Driven Testing is how naturally it encourages thinking about edge cases.

Instead of asking:

> "Does the function work?"

Ask:

> "Under which conditions could the function fail?"

Common categories include:

- zero values,
- empty collections,
- `nil` values,
- invalid input,
- boundary values,
- maximum and minimum values,
- duplicated values,
- Unicode input,
- whitespace,
- extremely large datasets.

These scenarios can simply be added as new rows in the table.

---

# 📈 Example: Extending the Table

Suppose we already have this table.

```go
tests := []struct {
	name     string
	a        int
	b        int
	expected int
}{
	{"positive", 2, 3, 5},
	{"negative", -2, -3, -5},
}
```

Adding another scenario requires only one more row.

```go
{
	name:     "zero",
	a:        0,
	b:        10,
	expected: 10,
}
```

No additional test logic is necessary.

This is one of the primary reasons why Table-Driven Testing scales so well.

---

# 🚫 Common Mistakes

Although the pattern is simple, several mistakes appear frequently.

## ❌ Duplicating Test Logic

Instead of:

```go
func TestCase1(t *testing.T) {}
func TestCase2(t *testing.T) {}
func TestCase3(t *testing.T) {}
```

Prefer:

```go
func TestSomething(t *testing.T) {
	// Execute all scenarios from one table.
}
```

---

## ❌ Using Meaningless Test Names

Poor names:

```text
test1
case2
example
sample
```

Better names:

```text
positive numbers
empty input
invalid email
missing separator
unicode string
```

Good names make failing tests immediately understandable.

---

## ❌ Mixing Unrelated Behaviors

A single table should verify **one behavior**.

For example, a table that mixes:

- addition,
- subtraction,
- multiplication,
- division,

is usually a sign that multiple test functions would be more appropriate.

Keep each table focused on one responsibility.

---

## ❌ Ignoring Edge Cases

Many beginners only test the "happy path."

Professional test suites also verify unusual and potentially problematic inputs.

Examples include:

- empty strings,
- `nil`,
- zero values,
- maximum values,
- minimum values,
- malformed input.

---

# ✅ Best Practices

The following guidelines are widely accepted in the Go community.

- Keep one table focused on one behavior.
- Use descriptive test names.
- Prefer named struct fields for readability.
- Add new scenarios instead of duplicating test functions.
- Cover both happy paths and edge cases.
- Use the simplest comparison that correctly verifies the result.
- Keep test data close to the test that uses it.
- Write tests that are easy to extend.

---

# 📚 When Should You Use Table-Driven Testing?

Table-Driven Testing is an excellent choice when:

- many scenarios share the same execution logic,
- only the input and expected output differ,
- you are validating pure functions,
- you need to cover many edge cases,
- the function is deterministic,
- you want tests that are easy to read and maintain,
- new scenarios are expected to be added over time.

Examples include:

- parsers,
- validators,
- formatters,
- converters,
- string manipulation,
- mathematical functions,
- business rules,
- serialization and deserialization.

---

# 🚫 When Is Another Approach Better?

Although Table-Driven Testing is extremely versatile, it is not the best fit for every situation.

Consider alternative approaches when:

- every scenario requires significantly different setup,
- complex mocking dominates the test,
- asynchronous workflows require specialized coordination,
- the behavior depends heavily on external systems,
- each scenario follows a fundamentally different execution path.

In these situations, separate test functions—or other testing techniques—may produce clearer and more maintainable code.

---

# 📝 Chapter Summary

Throughout this chapter, we learned that Table-Driven Testing is much more than placing data into a slice.

It represents a way of thinking about tests:

- describe scenarios as **data**,
- write the testing logic only once,
- keep tests readable,
- eliminate duplication,
- make adding new scenarios effortless,
- treat the test table as executable documentation.

Because of these characteristics, Table-Driven Testing has become the de facto standard for unit testing in Go and is used extensively throughout the Go standard library and the wider Go ecosystem.

Once this pattern becomes familiar, it naturally serves as the foundation for more advanced techniques such as **Subtests (`t.Run`)**, where each table entry can be executed as an independently reported test case.

Table-Driven Testing is a Go testing pattern where:

* test cases are stored as data,
* each test scenario is represented as a table row,
* the same test logic executes against every scenario.

The basic structure is:

```go
func TestSomething(t *testing.T) {

	tests := []struct {
		// test data
	}{}

	for _, tt := range tests {

		// execute test

	}

}
```

This approach is one of the foundations of idiomatic Go testing and will appear frequently in professional Go codebases.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Subtests (`t.Run`)`](02-subtests.md)**

In the next part, we will cover:

* `t.Run` creates a child test.
* Each subtest has its own execution context.
* Descriptive names greatly improve test output.
* Subtests organize related scenarios under a common parent.
* They integrate seamlessly with Table-Driven Testing.
* They enable selective execution using `go test -run`.
* They provide the foundation for parallel test execution, which will be covered in a later chapter.
