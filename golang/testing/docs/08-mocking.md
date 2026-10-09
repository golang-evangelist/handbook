# 🎭 Mocking

> Chapter 8 of the **Go Testing Handbook**

---

# 📖 Introduction

As applications grow, individual components rarely operate in isolation.

A service may depend on:

- a database,
- an HTTP API,
- a cache,
- a message queue,
- a file system,
- a payment provider,
- an authentication service.

Testing such components directly against real external systems introduces several challenges.

For example:

- tests become slower,
- failures depend on network availability,
- databases must be prepared,
- external services may change,
- tests become non-deterministic.

This is where **Mocking** becomes an essential testing technique.

---

# 🎯 Learning Objectives

By the end of this chapter, you will understand:

- what mocking is,
- why mocking is useful,
- how Go approaches mocking,
- why interfaces are important,
- how to create manual mocks,
- common mocking patterns,
- when mocking is appropriate,
- when mocking should be avoided,
- best practices for maintainable tests.

---

# 🤔 What Is Mocking?

A **Mock** is an object that replaces a real dependency during testing.

Instead of communicating with the real dependency, the code interacts with a controlled test implementation.

Conceptually:

```text
Production

Application

↓

Database
```

During testing:

```text
Tests

↓

Application

↓

Mock Database
```

The application does not know the difference.

It simply communicates through an interface.

---

# 🧠 Why Mocking Exists

Suppose we have a service that loads a user.

```go
func GetUser(id int)
```

In production, the service performs:

```text
Application

↓

Database

↓

Result
```

Running a unit test against a real database requires:

- starting the database,
- preparing test data,
- cleaning up afterwards,
- ensuring network availability.

The test becomes slower and more complicated.

With mocking:

```text
Application

↓

Mock

↓

Predefined Result
```

The dependency is replaced with predictable behavior.

---

# 📦 The Role of Interfaces

In Go, mocking is almost always based on **interfaces**.

Consider:

```go
type UserRepository interface {
	Find(id int) (User, error)
}
```

The service depends on the interface rather than a concrete implementation.

```go
type UserService struct {
	repo UserRepository
}
```

Now the repository can be:

- a real database,
- an in-memory implementation,
- a mock,
- a fake,
- a stub.

The service remains unchanged.

---

# 🏗 Production Implementation

Example:

```go
type SQLRepository struct {
	// database connection
}

func (r *SQLRepository) Find(id int) (User, error) {
	// query database
}
```

Production code uses:

```text
UserService

↓

SQLRepository

↓

Database
```

---

# 🧪 Test Implementation

During testing:

```go
type MockRepository struct{}

func (m *MockRepository) Find(id int) (User, error) {
	return User{
		ID:   1,
		Name: "Alice",
	}, nil
}
```

Now the dependency graph becomes:

```text
UserService

↓

MockRepository

↓

Predefined Data
```

No database is required.

---

# 🎯 Dependency Injection

Mocks become possible because dependencies are **injected** rather than created internally.

Good design:

```go
type UserService struct {
	repo UserRepository
}
```

Poor design:

```go
type UserService struct {
	db *sql.DB
}
```

The first design allows different implementations.

The second tightly couples the service to a specific database.

Dependency Injection and interfaces are therefore fundamental concepts for effective mocking in Go.

---

# 🔍 A Complete Example

Suppose we have:

```go
type UserRepository interface {
	Find(id int) (User, error)
}
```

Production implementation:

```go
type SQLRepository struct{}
```

Mock implementation:

```go
type MockRepository struct{}
```

The service:

```go
type UserService struct {
	repo UserRepository
}
```

During testing:

```text
Test

↓

UserService

↓

MockRepository

↓

Static Result
```

During production:

```text
Application

↓

UserService

↓

SQLRepository

↓

Database
```

The service code itself does not change.

---

# 💡 Advantages of Mocking

Mocking offers several important benefits.

| Benefit | Description |
|----------|-------------|
| Fast | No external systems are contacted. |
| Deterministic | Results are fully controlled. |
| Isolated | Only the component under test is exercised. |
| Repeatable | Tests behave consistently. |
| Flexible | Various scenarios are easy to simulate. |

---

# 🎭 Types of Test Doubles

The term *mock* is often used broadly, but there are several kinds of test doubles.

| Type | Purpose |
|------|---------|
| Dummy | Passed only to satisfy an API. |
| Stub | Returns predefined values. |
| Fake | A simplified working implementation (for example, an in-memory repository). |
| Mock | Verifies interactions and expectations. |
| Spy | Records calls for later inspection. |

In everyday Go development, developers often refer to all of these simply as "mocks," even though their roles differ.

---

# ⚠️ Why Not Always Use Real Dependencies?

Imagine a unit test that performs:

```text
HTTP Request

↓

Authentication

↓

Load Configuration

↓

Connect Database

↓

Run SQL Query

↓

Return Result
```

Such a test may require:

- several seconds,
- complex setup,
- network access,
- infrastructure,
- maintenance.

A mock can reduce the same dependency chain to:

```text
Return User
```

The test becomes significantly faster and easier to understand.

---

# 🧩 Manual Mocks

One of Go's strengths is that many mocks can be written manually.

Example:

```go
type MockClock struct{}

func (m *MockClock) Now() time.Time {
	return time.Date(
		2026,
		1,
		1,
		0,
		0,
		0,
		0,
		time.UTC,
	)
}
```

This implementation always returns the same time.

Such deterministic behavior is ideal for testing.

---

> **Best Practice**
>
> Design your application around **small, focused interfaces**.
>
> Small interfaces are easier to understand, easier to mock, and make both production code and test code significantly more maintainable.

---

# 🧪 Writing Manual Mocks

One of the distinguishing characteristics of Go is that **manual mocks are often simple enough that no external library is required**.

Suppose we have the following interface:

```go
type EmailSender interface {
	Send(to, subject, body string) error
}
```

Our production implementation might send a real email through an SMTP server or a third-party provider.

For testing, however, we can replace it with a mock.

```go
type MockEmailSender struct{}

func (m *MockEmailSender) Send(
	to,
	subject,
	body string,
) error {
	return nil
}
```

This mock never contacts an external system.

Instead, it behaves in a predictable and deterministic manner.

---

# 🎯 Verifying Behavior

Sometimes it is not enough to know **what** was returned.

We also want to know **how** the dependency was used.

For example:

```text
Application

↓

Email Sender

↓

Was Send() Called?
```

A mock can record this information.

```go
type MockEmailSender struct {
	Called bool
}

func (m *MockEmailSender) Send(
	to,
	subject,
	body string,
) error {
	m.Called = true
	return nil
}
```

The test can then verify:

```go
if !mock.Called {
	t.Fatal("expected Send to be called")
}
```

This allows the test to verify interactions in addition to returned values.

---

# 📦 Returning Different Results

Mocks are also useful for simulating different scenarios.

Successful request:

```go
return nil
```

Failure:

```go
return errors.New("network error")
```

Timeout:

```go
return context.DeadlineExceeded
```

Authentication error:

```go
return ErrUnauthorized
```

By controlling the mock's behavior, you can exercise error paths that may be difficult or impossible to reproduce with real systems.

---

# 🔄 Configurable Mocks

Instead of hardcoding behavior, many manual mocks expose configurable fields.

Example:

```go
type MockRepository struct {
	User User
	Err  error
}

func (m *MockRepository) Find(id int) (User, error) {
	return m.User, m.Err
}
```

Different tests can now configure the same mock differently.

Example:

```go
mock := MockRepository{
	User: User{
		ID:   1,
		Name: "Alice",
	},
}
```

or

```go
mock := MockRepository{
	Err: errors.New("database unavailable"),
}
```

This approach keeps the mock reusable while allowing each test to describe its own scenario clearly.

---

# 🧠 Mocking vs Fakes vs Stubs

These terms are often used interchangeably, but understanding the distinction is useful.

### Stub

A stub simply returns predefined values.

```text
Input

↓

Stub

↓

Fixed Output
```

---

### Fake

A fake contains a simplified working implementation.

Example:

```text
Real Database

↓

In-Memory Database
```

The fake behaves like the real dependency but is much simpler.

---

### Mock

A mock not only returns values but also verifies interactions.

Example:

```text
Was Find() Called?

How Many Times?

With Which Arguments?
```

---

### Spy

A spy records information for later inspection.

Example:

```text
Record Calls

↓

Verify Later
```

Although the terminology differs, Go developers frequently implement these concepts using ordinary structs.

---

# ⚠️ Avoid Over-Mocking

One of the most common mistakes is mocking **everything**.

Imagine:

```text
Service

↓

Mock

↓

Mock

↓

Mock

↓

Mock

↓

Mock
```

Eventually, the unit test verifies only that the mocks behave as configured.

Very little real application logic is exercised.

This produces brittle tests that are difficult to maintain.

---

# 🎯 What Should Be Mocked?

Good candidates include:

- HTTP clients,
- databases,
- message queues,
- payment providers,
- SMTP servers,
- file storage services,
- cloud APIs,
- authentication providers,
- time providers,
- random number generators.

These are external dependencies whose behavior should be isolated during unit testing.

---

# 🚫 What Should Usually NOT Be Mocked?

Avoid mocking:

- simple value objects,
- standard library types,
- business entities,
- pure functions,
- immutable data structures,
- small utility functions.

Mocking these often increases complexity without improving test quality.

---

# 🧩 Interfaces Should Be Small

Large interfaces are difficult to mock.

Example:

```go
type Repository interface {
	Create(...)
	Update(...)
	Delete(...)
	Find(...)
	FindAll(...)
	Search(...)
	Count(...)
	Close(...)
}
```

A manual mock must implement every method, even if a particular test only needs one.

A better approach is to define focused interfaces.

Example:

```go
type UserFinder interface {
	Find(id int) (User, error)
}
```

Smaller interfaces make both production code and tests easier to maintain.

This philosophy aligns with Go's preference for **small, behavior-oriented interfaces**.

---

# 📋 Mocking Best Practices

Experienced Go developers generally follow these guidelines:

- Depend on interfaces rather than concrete implementations.
- Keep interfaces small and focused.
- Prefer manual mocks for simple cases.
- Mock only external dependencies.
- Verify meaningful interactions.
- Keep mocks deterministic.
- Avoid unnecessary complexity.
- Use integration tests when real systems must be exercised.

---

# ⚠️ Common Beginner Mistakes

## ❌ Mocking Everything

Not every dependency requires a mock.

Sometimes a real implementation is simpler and provides stronger confidence.

---

## ❌ Creating Large Interfaces Just for Testing

Interfaces should model behavior needed by the application—not exist solely to make testing easier.

---

## ❌ Ignoring Integration Tests

Mocks are excellent for unit testing.

However, they do **not** verify that your application communicates correctly with real databases, APIs, or external services.

Integration tests remain an essential complement to unit tests.

---

# 📝 Chapter Summary

Mocking is a technique for replacing real dependencies with controlled test implementations.

The key ideas from this chapter are:

- Mocking isolates the component under test.
- Interfaces are the foundation of mocking in Go.
- Dependency Injection makes mocking straightforward.
- Manual mocks are often sufficient and idiomatic.
- Mocks can simulate both successful and failing scenarios.
- Small interfaces are easier to mock and maintain.
- Mock external dependencies rather than business logic.
- Unit tests using mocks should be complemented by integration tests against real systems.

When combined with Table-Driven Testing, Subtests, Test Helpers, Coverage Analysis, Benchmark Tests, Parallel Tests, and Fuzz Testing, mocking enables you to build fast, deterministic, and maintainable test suites while still validating real integrations where appropriate.

---

### ▶ Next Chapter

Continue with:

**➡️ [`Best Practices`](09-best-practices.md)**

In the next part, we will cover:

* the recommended order for learning Go testing,
* the recommended workflow for writing tests,
* how the different testing techniques complement one another,
* common mistakes to avoid,
* useful `go test` commands,
* official Go documentation and learning resources.
