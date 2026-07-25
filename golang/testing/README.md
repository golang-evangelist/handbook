<div align="center">

# 🧪 Go Testing Handbook

### A complete guide to testing in Go (Golang)

*A practical handbook covering the most important testing techniques used in modern Go projects.*

![Go Version](https://img.shields.io/badge/Go-1.24+-00ADD8?style=for-the-badge&logo=go)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-goevangelist-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/goevangelist)
[![Instagram](https://img.shields.io/badge/Instagram-@go__evangelist-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/go_evangelist)
[![X](https://img.shields.io/badge/X-@go__evangelist-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/go_evangelist)

</div>

---

# 📖 About

This repository contains a comprehensive tutorial dedicated to testing in the **Go (Golang)** programming language.

The goal is not only to explain how to write tests, but also to explain:

* why a particular testing technique exists,
* when it should be used,
* when it should be avoided,
* and how experienced Go developers organize tests in production projects.

The documentation gradually progresses from basic concepts to advanced topics and is intended for developers who already know the Go language and want to improve the quality of their code.

---

# 🎯 Who is this tutorial for?

This handbook is intended for:

* Go beginners who already understand the language basics.
* Backend developers building REST or gRPC services.
* Developers preparing for Go technical interviews.
* Engineers working on production Go applications.
* Anyone who wants to learn professional testing practices used by the Go community.

---

# 📚 What you will learn

By the end of this handbook you will understand:

* how to write clean and maintainable unit tests,
* how to avoid duplicated test code,
* how to organize complex test suites,
* how to measure test coverage,
* how to benchmark Go code,
* how to test concurrent code,
* how to automatically discover edge cases,
* how to isolate external dependencies using mocks.

---

# 📂 Documentation Structure

```text
README.md
docs/
├── 01-table-driven-testing.md
├── 02-subtests.md
├── 03-test-helpers.md
├── 04-coverage.md
├── 05-benchmarks.md
├── 06-parallel-tests.md
├── 07-fuzz-testing.md
├── 08-mocking.md
└── 09-best-practices.md
```

---

# 📑 Table of Contents

## Introduction

* [Introduction](README.md)

## Core Testing Concepts

* [Table-Driven Testing)](docs/01-table-driven-testing.md)
* [Subtests (`t.Run`)](docs/02-subtests.md)
* [Test Helpers (`t.Helper`)](docs/03-test-helpers.md)

## Test Analysis

* [Coverage Analysis](docs/04-coverage.md)
* [Benchmark Testing](docs/05-benchmarks.md)

## Advanced Testing

* [Parallel Tests (`t.Parallel`)](docs/06-parallel-tests.md)
* [Fuzz Testing](docs/07-fuzz-testing.md)
* [Mocking](docs/08-mocking.md)

## Best Practices

* [Testing Best Practices](docs/09-best-practices.md)

---

# 🧭 Recommended Learning Order

It is recommended to read the documentation in the following order:

1. Table-Driven Testing
2. Subtests (`t.Run`)
3. Test Helpers (`t.Helper`)
4. Coverage Analysis
5. Benchmark Testing
6. Parallel Tests (`t.Parallel`)
7. Fuzz Testing
8. Mocking
9. Best Practices

Each chapter builds upon knowledge introduced in previous chapters.

---

# 🛠 Prerequisites

Before reading this tutorial, you should already know:

* Variables
* Constants
* Functions
* Methods
* Structs
* Interfaces
* Packages
* Modules
* Error handling
* Pointers
* Slices
* Maps
* Goroutines (basic understanding)

Knowledge of the standard Go testing package is helpful but not required.

---

# 💻 Requirements

Recommended environment:

| Component        | Version                      |
| ---------------- | ---------------------------- |
| Go               | 1.24+                        |
| Operating System | Linux / macOS / Windows      |
| Editor           | VS Code, GoLand, Vim, Neovim |

---

# 🚀 Running Tests

Run all tests:

```bash
go test ./...
```

Run tests with verbose output:

```bash
go test -v ./...
```

Run only one package:

```bash
go test ./internal/service
```

Run a single test:

```bash
go test -run TestAdd
```

---

# 📌 Repository Goals

The purpose of this repository is to provide explanations that are:

* beginner-friendly,
* practical,
* production-oriented,
* easy to understand,
* based on idiomatic Go practices.

The focus is not only on *how* to write tests, but also on *why* experienced Go developers choose certain approaches.

---

# 📖 Recommended Reading Strategy

Do not rush through the documentation.

For every chapter:

1. Read the theory.
2. Study every example.
3. Rewrite the examples manually.
4. Experiment with the code.
5. Write your own examples.
6. Continue to the next chapter.

Testing is a practical skill that improves primarily through repetition.

---

# 🤝 Contributing

Suggestions, improvements, corrections and discussions are always welcome.

If you discover an error or have an idea that could improve the documentation, feel free to open an Issue or submit a Pull Request.

---

# 📄 License

This project is released under MIT License

---

### ▶ Next Chapter

Continue with:

**➡️ [`Table-Driven Testing`](docs/01-table-driven-testing.md)**

The next chapter introduces the Go testing philosophy, explains why testing matters, and provides an overview of the standard `testing` package before moving on to Table-Driven Testing.
