# ⚡ Benchmark Tests

> Chapter 5 of the **Go Testing Handbook**

---

# 📖 Introduction

Until now, this handbook has focused on **correctness**.

We learned how to verify that our code behaves as expected by using:

- Table-Driven Testing,
- Subtests,
- Test Helpers,
- Coverage Analysis.

However, correctness is only one aspect of high-quality software.

A function may produce the correct result but still be:

- too slow,
- memory-intensive,
- inefficient,
- unsuitable for production workloads.

This raises another important question:

> **"How fast is my code?"**

The Go testing framework answers this question through **Benchmark Tests**.

---

# 🎯 Learning Objectives

By the end of this chapter, you will understand:

- what benchmarks are,
- how benchmark tests differ from unit tests,
- how Go executes benchmarks,
- how to write reliable benchmark functions,
- how to compare different implementations,
- how to interpret benchmark output,
- common benchmarking mistakes,
- best practices used by experienced Go developers.

---

# 🤔 What Is a Benchmark?

A **Benchmark** is a special type of test whose purpose is to **measure performance**.

Unlike a unit test, which verifies correctness, a benchmark measures characteristics such as:

- execution time,
- throughput,
- memory allocations,
- bytes allocated,
- scalability.

Conceptually:

```text
Unit Test

↓

Correct?

↓

PASS / FAIL
```

```text
Benchmark

↓

How Fast?

↓

Performance Metrics
```

Both are important, but they answer different questions.

---

# 🧠 Unit Tests vs Benchmarks

Suppose we have the following function.

```go
func Reverse(s string) string
```

A unit test asks:

> Does it return the correct string?

A benchmark asks:

> How long does it take?

These are fundamentally different goals.

---

# 📊 Comparison

| Unit Test | Benchmark |
|-----------|-----------|
| Verifies correctness | Measures performance |
| Passes or fails | Produces metrics |
| Usually executes once | Executes many times |
| Uses `*testing.T` | Uses `*testing.B` |
| Executed with `go test` | Executed with `go test -bench` |

Although benchmarks are part of the `testing` package, they serve a completely different purpose.

---

# 🏗 Benchmark Function Naming

Go automatically discovers benchmark functions using a naming convention.

Every benchmark must:

- start with `Benchmark`,
- accept a `*testing.B`,
- reside in a `_test.go` file.

Example:

```go
func BenchmarkReverse(b *testing.B) {

}
```

This is directly analogous to unit tests:

```go
func TestReverse(t *testing.T) {

}
```

The only difference is the parameter type.

---

# 📦 The `testing.B` Type

Just as unit tests receive a `*testing.T`, benchmarks receive a `*testing.B`.

The `testing.B` type provides functionality for:

- controlling benchmark execution,
- measuring elapsed time,
- reporting allocations,
- resetting timers,
- stopping and starting timers,
- running sub-benchmarks.

Think of it as the benchmarking counterpart of `testing.T`.

---

# 🔄 The Benchmark Loop

Every benchmark contains a loop driven by the value of `b.N`.

A minimal benchmark looks like this:

```go
func BenchmarkReverse(b *testing.B) {
	for i := 0; i < b.N; i++ {
		Reverse("Hello, World!")
	}
}
```

Notice that we never assign a value to `b.N`.

Go does that automatically.

---

# 🤔 What Is `b.N`?

One of the most common beginner questions is:

> **"Where does `b.N` come from?"**

The answer is:

**Go determines it automatically.**

The benchmark runner executes the function repeatedly.

If the benchmark completes too quickly, Go increases `b.N` and runs it again.

Conceptually:

```text
Run Benchmark

↓

100 iterations

↓

Too Fast?

↓

Increase Iterations

↓

1,000 iterations

↓

Still Too Fast?

↓

Increase Again

↓

100,000 iterations

↓

Collect Results
```

This adaptive process helps produce stable and meaningful measurements.

---

# 🎯 Why Repeat the Operation?

Suppose a function executes in only a few nanoseconds.

Measuring a single invocation would produce noisy and unreliable results.

Instead, Go executes the function many thousands—or even millions—of times.

For example:

```go
for i := 0; i < b.N; i++ {
	Reverse("Hello")
}
```

This allows Go to calculate an average execution time with much greater accuracy.

---

# 🧪 A Complete Benchmark Example

Suppose we have:

```go
func Add(a, b int) int {
	return a + b
}
```

A benchmark might look like this:

```go
func BenchmarkAdd(b *testing.B) {
	for i := 0; i < b.N; i++ {
		Add(10, 20)
	}
}
```

Although the function is simple, the structure shown here is representative of most benchmarks.

---

# ▶️ Running Benchmarks

Benchmarks are **not** executed by default.

To run them, use:

```bash
go test -bench=.
```

The `.` is a regular expression that matches every benchmark in the package.

If you have:

```go
BenchmarkAdd

BenchmarkMultiply

BenchmarkDivide
```

all three will execute.

---

# 📈 Example Output

A typical benchmark report looks like this:

```text
goos: linux
goarch: amd64
pkg: github.com/example/math

BenchmarkAdd-16        1000000000     0.278 ns/op

PASS
ok      github.com/example/math
```

At first glance, this output can appear cryptic.

In the next section, we will examine every part of the report in detail and learn how to interpret benchmark results correctly.

---

> **Best Practice**
>
> Before optimizing code, **measure it**.
>
> Benchmarks provide objective performance data, allowing you to make optimization decisions based on evidence rather than assumptions.

---

# 📖 Understanding Benchmark Output

The first time you run a benchmark, the output may look intimidating.

Example:

```text
goos: linux
goarch: amd64
pkg: github.com/example/project/math

BenchmarkAdd-16    1000000000    0.278 ns/op

PASS
ok      github.com/example/project/math
```

Let's examine each part individually.

---

# 🖥 `goos`

```text
goos: linux
```

This indicates the operating system on which the benchmark was executed.

Examples include:

```text
linux
windows
darwin
freebsd
```

Benchmark results are often influenced by the operating system because scheduling, memory management, and system calls differ across platforms.

---

# 💻 `goarch`

```text
goarch: amd64
```

This indicates the CPU architecture.

Common values include:

```text
amd64

arm64

386
```

Different architectures may produce different benchmark results due to variations in instruction sets, cache sizes, and processor optimizations.

---

# 📦 `pkg`

```text
pkg: github.com/example/project/math
```

This identifies the package containing the benchmark.

It is especially useful when running benchmarks across multiple packages.

---

# 🔍 Benchmark Name

```text
BenchmarkAdd-16
```

This consists of two parts.

### Benchmark Name

```text
BenchmarkAdd
```

The name of the benchmark function.

### Suffix

```text
-16
```

This is **not** the number of iterations.

It represents the value of `GOMAXPROCS` used during the benchmark.

For example:

```text
BenchmarkAdd-8
```

means:

```text
GOMAXPROCS = 8
```

Likewise:

```text
BenchmarkAdd-16
```

means:

```text
GOMAXPROCS = 16
```

This information is helpful when comparing results across machines with different CPU configurations.

---

# 🔄 Iteration Count

The next number is:

```text
1000000000
```

This is the final value of `b.N`.

Go automatically determined that executing the benchmark one billion times produced sufficiently stable measurements.

Remember:

You never assign this value yourself.

The benchmark runner decides it dynamically.

---

# ⏱ `ns/op`

The final metric:

```text
0.278 ns/op
```

means:

```text
nanoseconds per operation
```

This is the average execution time for a single iteration.

Smaller values indicate faster execution.

Examples:

| Result | Interpretation |
|---------|----------------|
| `0.5 ns/op` | Extremely fast |
| `50 ns/op` | Very fast |
| `5 µs/op` | Moderate |
| `2 ms/op` | Relatively slow |
| `150 ms/op` | Very slow |

Always compare benchmarks measuring the same operation under similar conditions.

---

# 🧠 Benchmark Output at a Glance

```text
BenchmarkAdd-16

│
├── Benchmark function name
│
└── GOMAXPROCS
```

```text
1000000000
```

↓

Number of iterations chosen by Go

```text
0.278 ns/op
```

↓

Average execution time per operation

---

# 📊 Comparing Two Implementations

Suppose we have two implementations.

Version A:

```go
func ReverseA(s string) string
```

Version B:

```go
func ReverseB(s string) string
```

Benchmarks:

```text
BenchmarkReverseA-16    5000000     260 ns/op

BenchmarkReverseB-16    8000000     155 ns/op
```

Comparison:

| Implementation | Performance |
|---------------|-------------|
| ReverseA | 260 ns/op |
| ReverseB | 155 ns/op |

Version B is significantly faster because each operation requires fewer nanoseconds.

---

# 🎯 Benchmarking Multiple Algorithms

One of the primary purposes of benchmarks is comparing competing implementations.

For example:

```text
String Builder

↓

Benchmark
```

```text
String Concatenation

↓

Benchmark
```

```text
bytes.Buffer

↓

Benchmark
```

The benchmark results provide objective data for choosing the most efficient implementation.

---

# 📦 Reporting Memory Allocations

Execution time is only part of the story.

Memory usage also affects performance.

Go can report allocation statistics using:

```bash
go test -bench=. -benchmem
```

Example:

```text
BenchmarkReverse-16

5000000

210 ns/op

64 B/op

2 allocs/op
```

Additional metrics:

```text
B/op
```

↓

Average bytes allocated per operation.

```text
allocs/op
```

↓

Average number of memory allocations per operation.

Generally:

- fewer allocations,
- fewer allocated bytes,

lead to better performance and lower pressure on the garbage collector.

---

# 🔬 Measuring Allocations

Suppose two functions have identical execution times.

Implementation A:

```text
200 ns/op

512 B/op

6 allocs/op
```

Implementation B:

```text
200 ns/op

64 B/op

1 alloc/op
```

Although both execute equally quickly, Implementation B is usually preferable because it allocates significantly less memory.

Reduced allocations often improve scalability and reduce GC overhead.

---

# ⚠️ Common Benchmark Mistakes

## ❌ Benchmarking Setup Code

Incorrect:

```go
func BenchmarkParser(b *testing.B) {
	for i := 0; i < b.N; i++ {
		file := os.ReadFile("large.json")

		Parse(file)
	}
}
```

This benchmark measures:

- disk I/O,
- file loading,
- parsing.

It does **not** isolate parser performance.

Instead:

```go
func BenchmarkParser(b *testing.B) {
	file := os.ReadFile("large.json")

	b.ResetTimer()

	for i := 0; i < b.N; i++ {
		Parse(file)
	}
}
```

Now only the parser is measured.

---

## ❌ Ignoring Benchmark Results

Benchmarks should inform engineering decisions.

If you optimize an algorithm but never compare the results, the benchmark provides little value.

Always compare:

- before,
- after,

to determine whether the optimization produced a measurable improvement.

---

> **Best Practice**
>
> Benchmark only the code you intend to measure.
>
> Exclude expensive setup work such as file loading, network requests, or database initialization whenever possible. Use methods like `b.ResetTimer()` to ensure measurements focus on the operation under test.

---

# 📝 Summary

Benchmark Tests provide a reliable way to measure the performance characteristics of Go code.

The key ideas from this chapter are:

- Benchmarks measure performance rather than correctness.
- Benchmark functions use `*testing.B`.
- Go automatically determines the appropriate value of `b.N`.
- Results report average execution time in `ns/op`.
- The `-benchmem` flag reports memory allocations.
- Benchmarks are ideal for comparing alternative implementations.
- Setup code should be excluded from measurements whenever possible.
- Optimization decisions should be guided by benchmark data, not intuition.

Together with Unit Tests, Table-Driven Testing, Subtests, Test Helpers, and Coverage Analysis, benchmarks complete another important part of Go's built-in testing ecosystem by helping you write not only **correct** software, but also **efficient** software.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Parallel Tests (`t.Parallel`)`](06-parallel-tests.md)**

In the next part, we will cover:

* what parallel tests are,
* how `t.Parallel()` works,
* when parallel execution is beneficial,
* when it should be avoided,
* common mistakes,
* best practices for writing safe parallel tests.
