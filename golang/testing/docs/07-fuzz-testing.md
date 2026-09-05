# 🧬 Fuzz Testing

> Chapter 7 of the **Go Testing Handbook**

---

# 📖 Introduction

Traditional unit tests verify a **fixed set of predefined inputs**.

For example:

```go
func TestReverse(t *testing.T) {
	tests := []struct {
		input    string
		expected string
	}{
		{"Go", "oG"},
		{"Hello", "olleH"},
		{"", ""},
	}

	for _, tc := range tests {
		got := Reverse(tc.input)

		if got != tc.expected {
			t.Fatalf("expected %q, got %q", tc.expected, got)
		}
	}
}
```

These tests are valuable, but they can only verify the scenarios that **we explicitly think of**.

This raises an important question:

> **"What happens if the input is something I never anticipated?"**

That is exactly the problem **Fuzz Testing** is designed to solve.

---

# 🎯 Learning Objectives

By the end of this chapter, you will understand:

- what Fuzz Testing is,
- why it is useful,
- how fuzzing differs from unit testing,
- how Go performs fuzzing,
- how to write fuzz tests,
- how seed inputs work,
- how Go discovers unexpected failures,
- common mistakes,
- best practices.

---

# 🤔 What Is Fuzz Testing?

Fuzz Testing (often shortened to **fuzzing**) is an automated testing technique in which the testing framework continuously generates large numbers of inputs and executes your code with them.

Instead of writing:

```text
Input A

Input B

Input C
```

Go generates inputs like:

```text
A

AA

😀

123

-100

0

VeryLongString...

RandomUnicode...

EmptyString

InvalidUTF8...

ThousandsMore...
```

The goal is not to verify specific business cases.

The goal is to discover inputs that cause:

- crashes,
- panics,
- infinite loops,
- unexpected behavior,
- invalid assumptions,
- security vulnerabilities.

---

# 🧠 Traditional Testing vs Fuzz Testing

Traditional unit testing:

```text
Developer

↓

Chooses Inputs

↓

Runs Test

↓

Checks Result
```

Fuzz Testing:

```text
Developer

↓

Provides Seed Inputs

↓

Go Generates Thousands of Variations

↓

Runs Continuously

↓

Reports Failures
```

The difference is who generates the inputs.

With unit tests, **the developer chooses them**.

With fuzzing, **Go generates many additional inputs automatically**.

---

# 📦 Why Fuzz Testing Matters

Imagine a parser.

```go
func Parse(data string) error
```

You might write tests for:

```text
{}

[]

{"name":"John"}

{"id":1}
```

But what about:

```text
{{{{{

😀😀😀😀😀

\x00\x01\x02

10 MB string

invalid UTF-8

unexpected control characters
```

Would you think of every possible input?

Probably not.

A fuzzing engine can explore countless combinations automatically.

---

# ⚠️ Bugs Often Hide in Edge Cases

Many production bugs are not caused by common inputs.

They appear only for unusual values.

Examples include:

- empty strings,
- extremely long strings,
- zero,
- negative numbers,
- maximum integer values,
- malformed JSON,
- corrupted binary data,
- unexpected Unicode characters,
- invalid encodings.

These are precisely the kinds of inputs fuzzing excels at generating.

---

# 🏗 Fuzz Tests in Go

Go introduced built-in fuzz testing in **Go 1.18**.

A fuzz test is recognized by its naming convention.

Instead of:

```go
func TestReverse(t *testing.T)
```

you write:

```go
func FuzzReverse(f *testing.F)
```

Notice two differences:

- the function starts with `Fuzz`,
- it receives `*testing.F`.

---

# 🧪 The Simplest Fuzz Test

Suppose we have:

```go
func Reverse(s string) string
```

A minimal fuzz test looks like this:

```go
func FuzzReverse(f *testing.F) {
	f.Add("Go")

	f.Fuzz(func(t *testing.T, input string) {
		Reverse(input)
	})
}
```

Although simple, this example demonstrates the essential structure of every fuzz test.

---

# 📖 Understanding the Structure

Let's examine the components.

```go
func FuzzReverse(f *testing.F)
```

This defines the fuzz test.

Next:

```go
f.Add("Go")
```

adds an initial **seed input**.

Finally:

```go
f.Fuzz(func(t *testing.T, input string) {

})
```

defines the function that Go repeatedly executes using generated inputs.

---

# 🌱 What Are Seed Inputs?

Seed inputs are the initial examples supplied by the developer.

Example:

```go
f.Add("")

f.Add("Go")

f.Add("Hello")

f.Add("こんにちは")

f.Add("😀")
```

These values serve as the starting point for the fuzzing engine.

Go then produces numerous variations derived from them.

Think of seed inputs as the initial population from which many new test cases evolve.

---

# 🔄 How Fuzzing Works

Conceptually, the process looks like this:

```text
Seed Inputs

↓

Mutation

↓

Generate New Inputs

↓

Execute Code

↓

Crash?

│
│
├── No
│
│     ↓
│
│  Generate More Inputs
│
│
│
└── Yes

      ↓

Report Failure
```

The process repeats continuously while fuzzing is running.

---

# 💥 What Happens When Go Finds a Failure?

Suppose one generated input causes:

```go
panic(...)
```

Go immediately:

1. stops the fuzzing process,
2. reports the failing input,
3. saves the input so the failure can be reproduced later.

This reproducibility is one of fuzz testing's greatest strengths.

A randomly discovered failure becomes a permanent regression test.

---

# 🎯 What Should Fuzz Tests Verify?

A fuzz test should verify **properties** rather than individual examples.

For example:

Instead of asking:

> Does `"Go"` reverse correctly?

Ask:

> Does reversing a string twice produce the original string?

Example:

```go
func FuzzReverse(f *testing.F) {
	f.Add("Go")

	f.Fuzz(func(t *testing.T, input string) {
		result := Reverse(Reverse(input))

		if result != input {
			t.Fatalf("unexpected result")
		}
	})
}
```

This property remains valid regardless of the generated input.

Property-based thinking is one of the most powerful aspects of fuzz testing.

---

# 📌 Typical Candidates for Fuzz Testing

Fuzz testing is particularly valuable for components that process external input.

Examples include:

- parsers,
- encoders,
- decoders,
- JSON processing,
- XML processing,
- URL parsing,
- regular expressions,
- compression algorithms,
- cryptographic functions,
- network protocols,
- file format readers,
- text processing.

These components often encounter unpredictable data in production.

---

> **Best Practice**
>
> Fuzz tests should focus on discovering **unexpected behavior** rather than verifying a handful of predefined examples.
>
> The most effective fuzz tests define general properties that should remain true for every valid input.

---

# ▶️ Running Fuzz Tests

Unlike ordinary unit tests, fuzz tests are **not executed continuously by default**.

You explicitly start the fuzzing engine using the `-fuzz` flag.

For example:

```bash
go test -fuzz=Fuzz
```

The value passed to `-fuzz` is treated as a regular expression.

Examples:

Run every fuzz test:

```bash
go test -fuzz=Fuzz
```

Run only one specific fuzz test:

```bash
go test -fuzz=FuzzReverse
```

Run every fuzz test whose name begins with `FuzzJSON`:

```bash
go test -fuzz=FuzzJSON
```

---

# ⏱ Controlling Fuzzing Duration

By default, fuzzing continues until you stop it or until it reaches an internal stopping condition.

In practice, you often limit the execution time.

Example:

```bash
go test -fuzz=Fuzz -fuzztime=30s
```

Other examples:

```bash
go test -fuzz=Fuzz -fuzztime=1m
```

```bash
go test -fuzz=Fuzz -fuzztime=5m
```

Longer runs allow Go to explore a much larger input space.

---

# 🔍 What Happens Internally?

A simplified view of the fuzzing engine looks like this:

```text
Developer

↓

Seed Inputs

↓

Mutation Engine

↓

Generated Inputs

↓

Execute Target Function

↓

Failure?

│
│
├───────── No
│
│           ↓
│
│   Generate More Inputs
│
│
│
└───────── Yes

            ↓
      
          Report
     
            ↓
     
    Save Failing Input
```

The mutation engine continuously transforms previous inputs into new ones.

The exact mutation strategy is intentionally abstracted away—you only need to provide good seed inputs and meaningful properties to verify.

---

# 💾 Saving Failing Inputs

One of the strongest features of Go's fuzzing support is that failures are reproducible.

Suppose Go generates a string that triggers a panic.

Instead of losing that input, Go stores it.

Conceptually:

```text
Crash

↓

Save Input

↓

Future Test Runs

↓

Replay Same Input
```

This prevents bugs from disappearing simply because the random input cannot be generated again.

---

# 🧪 Example: Finding a Panic

Imagine the following implementation.

```go
func First(s string) byte {
	return s[0]
}
```

Looks simple.

However:

```go
First("")
```

causes:

```text
panic: runtime error

index out of range
```

A traditional unit test might overlook this scenario.

A fuzz test could discover it automatically by generating an empty string.

---

# ✅ Correcting the Implementation

A safer version might be:

```go
func First(s string) (byte, bool) {
	if len(s) == 0 {
		return 0, false
	}

	return s[0], true
}
```

Once fixed, rerunning the fuzz test helps verify that the crash no longer occurs.

---

# 🧠 Thinking in Properties

Traditional testing often verifies:

```text
Input

↓

Expected Output
```

Fuzz testing encourages a different mindset:

```text
Input

↓

Invariant

↓

Always True?
```

The focus shifts from specific examples to rules that should hold for **every** valid input.

---

# 📋 Examples of Useful Properties

### Reverse

```text
Reverse(Reverse(x))

=

x
```

---

### Sort

```text
Length Before

=

Length After
```

and

```text
Output Is Ordered
```

---

### JSON

```text
Encode

↓

Decode

↓

Original Value
```

---

### Compression

```text
Compress

↓

Decompress

↓

Original Data
```

---

### URL Parsing

```text
Parse

↓

Serialize

↓

Equivalent URL
```

Properties like these allow fuzzing to validate thousands of generated inputs with a single invariant.

---

# ⚠️ Common Beginner Mistakes

## ❌ Expecting Exact Outputs

A fuzz test is usually **not** another Table-Driven Test.

Instead of writing:

```go
if input == "Go" {
	...
}
```

focus on rules that should hold regardless of the generated input.

---

## ❌ Forgetting Edge Cases in Seed Inputs

Although Go generates new values automatically, good seed inputs still matter.

Include examples such as:

```text
""

"Go"

"😀"

"こんにちは"

VeryLongString

InvalidData
```

Well-chosen seeds help the engine explore the input space more effectively.

---

## ❌ Using Fuzzing for Everything

Not every function benefits from fuzz testing.

Simple arithmetic functions often gain little value from fuzzing.

Instead, fuzzing shines when processing complex or unpredictable input.

---

# 🎯 When Should You Use Fuzz Testing?

Fuzz testing is an excellent choice for:

- parsers,
- serializers,
- deserializers,
- file readers,
- protocol implementations,
- networking code,
- compilers,
- interpreters,
- compression libraries,
- cryptographic utilities,
- Unicode processing,
- input validation.

These areas commonly receive data from external or untrusted sources.

---

# 🚫 When Fuzz Testing Is Usually Unnecessary

Fuzzing is often unnecessary for:

- trivial getters,
- simple setters,
- straightforward mathematical operations,
- constant-returning functions,
- thin wrappers around well-tested library functions,
- code with no meaningful input variability.

For such code, conventional unit tests are usually sufficient.

---

# 💡 Best Practices

Experienced Go developers typically follow these guidelines:

- Start with meaningful seed inputs.
- Test invariants instead of individual examples.
- Keep fuzz functions deterministic.
- Let panics fail the test naturally.
- Preserve discovered failing inputs.
- Combine fuzz tests with traditional unit tests.
- Use Table-Driven Tests for known scenarios and fuzzing for unknown ones.
- Regularly rerun fuzz tests as the code evolves.

---

# 🔄 Unit Tests and Fuzz Tests Work Together

These techniques complement one another rather than compete.

| Unit Tests | Fuzz Tests |
|------------|------------|
| Verify known examples | Explore unknown inputs |
| Confirm expected behavior | Search for unexpected behavior |
| Deterministic | Broad input exploration |
| Business scenarios | Robustness and resilience |

A mature Go test suite frequently contains **both**.

---

# 📝 Chapter Summary

Fuzz Testing is a powerful technique for discovering bugs that traditional unit tests may never encounter.

The key ideas from this chapter are:

- Go introduced built-in fuzz testing in **Go 1.18**.
- Fuzz tests begin with `Fuzz` and receive a `*testing.F`.
- Seed inputs provide starting examples for the mutation engine.
- Go automatically generates thousands of additional inputs.
- Fuzz tests should verify **properties and invariants**, not isolated examples.
- When a failure is found, Go preserves the input so the bug can be reproduced.
- Fuzz testing is particularly valuable for parsers, protocols, file formats, serialization, and other code that processes unpredictable input.

When combined with Table-Driven Testing, Subtests, Test Helpers, Coverage Analysis, Benchmark Tests, and Parallel Tests, Fuzz Testing significantly increases confidence that your software behaves correctly—even when faced with inputs you never anticipated.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Mocking`](08-mocking.md)**

In the next part, we will cover:

* what mocking is,
* why mocking is useful,
* how Go approaches mocking,
* why interfaces are important,
* how to create manual mocks,
* common mocking patterns,
* when mocking is appropriate,
* when mocking should be avoided,
* best practices for maintainable tests.
