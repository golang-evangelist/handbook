<div align="center">

# Go Path - Zero to Gopher

A detailed, thorough, and analytical course on the Go (Golang) programming language, in English.

![Go Version](https://img.shields.io/badge/Go-1.22+-00ADD8?style=for-the-badge&logo=go)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-goevangelist-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/goevangelist)
[![Instagram](https://img.shields.io/badge/Instagram-@go__evangelist-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/go_evangelist)
[![X](https://img.shields.io/badge/X-@go__evangelist-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/go_evangelist)

</div>

---

> The course takes you from the first program to services, tests, concurrency, secure code, profiling, and tools around the Go ecosystem. Each lesson explains the mechanism, shows code, and breaks down the decision: why Go works exactly that way and what trade-off that decision carries.
> 
> The target language version is **Go 1.22+**. Where a lesson depends on a newer API, that is noted in the text itself.
> 
> The course has **227 lessons** in three modules: `01-Basics`, `02-Intermediate`, and `03-Others`. The basics are areas `01` through `08`.
> 
> Starting lesson: [01.01 - Introduction](01-Basics/01-Go-Getting-Started/01.01-Introduction.md).

---

## How to learn

1. Read in order within an area. Skipping the basics later creates gaps in the mental model.
2. Type out every example. After reading, change one condition and run the program again.
3. Do the exercises before moving on to the next lesson.
4. When the same topic appears in a later area, that is not repetition for bulk. It is a deeper layer: first syntax, then design, then production context.

---

## What the course deliberately does

- Stays true to Go's explicitness: `error` is a value, `interface` is satisfied implicitly, composition replaces inheritance.
- Separates mechanism from advice. First how the runtime, compiler, or library works, and only then what to choose in a project.
- Security lessons teach defense: validation, output encoding, parameterized queries, TLS, sessions, and least privilege.
- Treats debugging and profiling tools as part of the craft, not as an add-on after a disaster.

---

## 01 - Basics

The foundation of the language, debugging tools, modules, the standard library, and the type system.

### 01 - Go: Getting Started

- [01.01 - Introduction](01-Basics/01-Go-Getting-Started/01.01-Introduction.md) — what Go is, why it was created, where it is used, anatomy of a program
- [01.02 - Starting a Project](01-Basics/01-Go-Getting-Started/01.02-Starting-a-Project.md) — modules, `go.mod`, `go run` and `go build`
- [01.03 - Working with Primitive Data Types](01-Basics/01-Go-Getting-Started/01.03-Working-with-Primitive-Data-Types.md) — numbers, string, rune, constants, pointer
- [01.04 - Working with Collections](01-Basics/01-Go-Getting-Started/01.04-Working-with-Collections.md) — array, slice, map, struct
- [01.05 - Creating Functions and Methods](01-Basics/01-Go-Getting-Started/01.05-Creating-Functions-and-Methods.md) — functions, multiple return values, method receiver
- [01.06 - Controlling Program Flow](01-Basics/01-Go-Getting-Started/01.06-Controlling-Program-Flow.md) — `if`, `switch`, `for`, introduction to `panic`

### 02 - Go Fundamentals

- [02.01 - Getting up and Running / Setting up Development Environment](01-Basics/02-Go-Fundamentals/02.01-Getting-up-and-Running-Setting-up-Development-Environment.md) — installation, `go env`, editor and `gopls`
- [02.02 - Variables and Simple Data Types](01-Basics/02-Go-Fundamentals/02.02-Variables-and-Simple-Data-Types.md) — zero value, `:=`, shadowing, scope
- [02.03 - Creating and Debugging Programs](01-Basics/02-Go-Fundamentals/02.03-Creating-and-Debugging-Programs.md) — compiler errors, `go vet`, reading the stack
- [02.04 - Aggregate Data Types](01-Basics/02-Go-Fundamentals/02.04-Aggregate-Data-Types.md) — slice growth, `copy`, map, embedding
- [02.05 - Control Flow: Branches](01-Basics/02-Go-Fundamentals/02.05-Control-Flow-Branches.md) — `if` with initialization, `switch`, `fallthrough`
- [02.06 - Control Flow: Loops](01-Basics/02-Go-Fundamentals/02.06-Control-Flow-Loops.md) — three forms of `for`, `range`, labels
- [02.07 - Organizing Programs](01-Basics/02-Go-Fundamentals/02.07-Organizing-Programs.md) — package, exported and unexported names, `internal`
- [02.08 - Functions](01-Basics/02-Go-Fundamentals/02.08-Functions.md) — function value, closure, `defer`
- [02.09 - Object Orientation and Polymorphism](01-Basics/02-Go-Fundamentals/02.09-Object-Orientation-and-Polymorphism.md) — struct, method, small interface
- [02.10 - Error Management](01-Basics/02-Go-Fundamentals/02.10-Error-Management.md) — `error`, `%w`, `errors.Is` and `errors.As`
- [02.11 - Concurrent Programming](01-Basics/02-Go-Fundamentals/02.11-Concurrent-Programming.md) — goroutine, channel, `WaitGroup`
- [02.12 - Testing](01-Basics/02-Go-Fundamentals/02.12-Testing.md) — `testing`, table tests, `go test`

### 03 - Debugging in Go

> **Start here.** First contact with Delve: why a debugger exists, how to install it, and a remote attach that stays on localhost. Area 04 is the same tool in more depth: DWARF, program state, breakpoints, and the command set.

- [03.01 - Getting Started With Go and Debugging](01-Basics/03-Debugging-in-Go/03.01-Getting-Started-With-Go-and-Debugging.md) — why a debugger, installing Delve
- [03.02 - Exploring Delve with Command Line and Editor Integration](01-Basics/03-Debugging-in-Go/03.02-Exploring-Delve-with-Command-Line-and-Editor-Integration.md) — `break`, `next`, `step`, VS Code
- [03.03 - Remote Debugging Container-Deployed Apps with Delve](01-Basics/03-Debugging-in-Go/03.03-Remote-Debugging-Container-Deployed-Apps-with-Delve.md) — headless Delve, localhost only or tunnel

### 04 - Debugging Go Application with Delve

> **After area 03.** DWARF, program state, breakpoints, and the command set. Install and the first session stay in area 03.

- [04.01 - Introduction](01-Basics/04-Debugging-Go-Application-with-Delve/04.01-Introduction.md) — Delve, DWARF, optimization and misleading display
- [04.02 - Exploring Application State](01-Basics/04-Debugging-Go-Application-with-Delve/04.02-Exploring-Application-State.md) — local variables, arguments, stack, goroutine
- [04.03 - Using Breakpoints](01-Basics/04-Debugging-Go-Application-with-Delve/04.03-Using-Breakpoints.md) — conditional breakpoint, tracepoint
- [04.04 - Delve Commands](01-Basics/04-Debugging-Go-Application-with-Delve/04.04-Delve-Commands.md) — command reference
- [04.05 - Editor Integration](01-Basics/04-Debugging-Go-Application-with-Delve/04.05-Editor-Integration.md) — `launch.json`, tests, attach

### 05 - Exploring Go Modules

- [05.01 - Introduction and Overview](01-Basics/05-Exploring-Go-Modules/05.01-Introduction-and-Overview.md) — why modules, `go.mod` and `go.sum`
- [05.02 - Creating and Maintaining Modules](01-Basics/05-Exploring-Go-Modules/05.02-Creating-and-Maintaining-Modules.md) — `go mod init`, `go get`, `go mod tidy`
- [05.03 - Understand Module Versioning](01-Basics/05-Exploring-Go-Modules/05.03-Understand-Module-Versioning.md) — semver, `/v2`, pseudo-versions
- [05.04 - Advanced Module Management Tools](01-Basics/05-Exploring-Go-Modules/05.04-Advanced-Module-Management-Tools.md) — `replace`, `vendor`, `GOPRIVATE`

### 06 - The Go Standard Library

- [06.01 - Creating CLI Applications](01-Basics/06-The-Go-Standard-Library/06.01-Creating-CLI-Applications.md) — `os.Args`, `flag`, exit codes
- [06.02 - Using the fmt Package](01-Basics/06-The-Go-Standard-Library/06.02-Using-the-fmt-Package.md) — verbs, `Stringer`, `Errorf`
- [06.03 - Using the Log Package](01-Basics/06-The-Go-Standard-Library/06.03-Using-the-Log-Package.md) — `log` and structured `log/slog`
- [06.04 - Working with the Time Package](01-Basics/06-The-Go-Standard-Library/06.04-Working-with-the-Time-Package.md) — `Time`, `Duration`, reference time for format
- [06.05 - Working with Strings in Go](01-Basics/06-The-Go-Standard-Library/06.05-Working-with-Strings-in-Go.md) — `strings`, `strconv`, UTF-8
- [06.06 - Implementing Reflection](01-Basics/06-The-Go-Standard-Library/06.06-Implementing-Reflection.md) — `reflect`, cost and justification

### 07 - Creating Custom Data Types With Go

- [07.01 - Interfaces and Structs](01-Basics/07-Creating-Custom-Data-Types-With-Go/07.01-Interfaces-and-Structs.md) — implicit satisfaction, nil pointer in `interface`
- [07.02 - Type Definitions and Type Aliases](01-Basics/07-Creating-Custom-Data-Types-With-Go/07.02-Type-Definitions-and-Type-Aliases.md) — `type T U` versus `type T = U`
- [07.03 - Composition with Embedded Types](01-Basics/07-Creating-Custom-Data-Types-With-Go/07.03-Composition-with-Embedded-Types.md) — embedding `struct` and `interface`, method promotion
- [07.04 - Comparable Types](01-Basics/07-Creating-Custom-Data-Types-With-Go/07.04-Comparable-Types.md) — `==`, `comparable`, `slices.Equal`
- [07.05 - Switching on Types](01-Basics/07-Creating-Custom-Data-Types-With-Go/07.05-Switching-on-Types.md) — type switch and type assert

### 08 - Advanced Branching and Looping in Go

- [08.01 - Learning Go Loops with the FOR Statement](01-Basics/08-Advanced-Branching-and-Looping-in-Go/08.01-Learning-Go-Loops-with-the-FOR-Statement.md) — three demos and a mini-project: word counting
- [08.02 - Learning Go Selections with if/Else Statements](01-Basics/08-Advanced-Branching-and-Looping-in-Go/08.02-Learning-Go-Selections-with-if-Else-Statements.md) — three demos and a mini-project: status classifier
- [08.03 - Learning Go Selections with the Switch Statement](01-Basics/08-Advanced-Branching-and-Looping-in-Go/08.03-Learning-Go-Selections-with-the-Switch-Statement.md) — three demos and a mini-project: CLI router

---

## 02 - Intermediate

Professional development: packages, HTTP, Gin, databases, concurrency, tests, security, distributed services, profiling, and the Go CLI.

### 01 - Deep dive into Go Packages

- [01.01 - Overview of a Package](02-Intermediate/01-Deep-Dive-into-Go-Packages/01.01-Overview-of-a-Package.md) — package as an API boundary and a single responsibility
- [01.02 - Working within a Package](02-Intermediate/01-Deep-Dive-into-Go-Packages/01.02-Working-within-a-Package.md) — files in the same package, exported names, `init`
- [01.03 - Using Other Packages](02-Intermediate/01-Deep-Dive-into-Go-Packages/01.03-Using-Other-Packages.md) — import paths, alias, blank import of a driver
- [01.04 - Preparing a Package to Be Used](02-Intermediate/01-Deep-Dive-into-Go-Packages/01.04-Preparing-a-Package-to-Be-Used.md) — stable API, `go doc`, examples

### 02 - Deep dive into Go Functions

- [02.01 - Function Parameters and Return Values](02-Intermediate/02-Deep-Dive-into-Go-Functions/02.01-Function-Parameters-and-Return-Values.md) — pass by value, multiple return values
- [02.02 - Control Flow in Functions](02-Intermediate/02-Deep-Dive-into-Go-Functions/02.02-Control-Flow-in-Functions.md) — early return, `defer` for cleanup
- [02.03 - Method Receivers on Structs and Scalar Types](02-Intermediate/02-Deep-Dive-into-Go-Functions/02.03-Method-Receivers-on-Structs-and-Scalar-Types.md) — value receiver and pointer receiver, named scalar types
- [02.04 - Function and Method Expressions](02-Intermediate/02-Deep-Dive-into-Go-Functions/02.04-Function-and-Method-Expressions.md) — function value, method value and method expression
- [02.05 - Closures](02-Intermediate/02-Deep-Dive-into-Go-Functions/02.05-Closures.md) — a closure captures the variable; Go 1.22 gives one loop variable per iteration

### 03 - Accessing Relational Databases in Go

- [03.01 - Getting Started with Go and Relational Database](02-Intermediate/03-Accessing-Relational-Databases-in-Go/03.01-Getting-Started-with-Go-and-Relational-Database.md) — `database/sql`, driver, connection pool
- [03.02 - Understanding RDBMS Transactions and Procedures with Go Language](02-Intermediate/03-Accessing-Relational-Databases-in-Go/03.02-Understanding-RDBMS-Transactions-and-Procedures-with-Go-Language.md) — transactions, `Commit`, `Rollback`, procedures
- [03.03 - Implementing CRUD Operations](02-Intermediate/03-Accessing-Relational-Databases-in-Go/03.03-Implementing-CRUD-Operations.md) — `Query`, `QueryRow`, `Exec`, placeholders, `ErrNoRows`

### 04 - Managing Go Projects

- [04.01 - Getting Started with Go Packages](02-Intermediate/04-Managing-Go-Projects/04.01-Getting-Started-with-Go-Packages.md) — `cmd`, `internal`, package organization
- [04.02 - Exploring Go Modules](02-Intermediate/04-Managing-Go-Projects/04.02-Exploring-Go-Modules.md) — `go.work`, `replace`, `tidy` in CI
- [04.03 - Embedding Resources in Go](02-Intermediate/04-Managing-Go-Projects/04.03-Embedding-Resources-in-Go.md) — `go:embed`, `embed.FS`, templates and migrations

### 05 - Creating Web Services with Go

- [05.01 - Getting Started](02-Intermediate/05-Creating-Web-Services-with-Go/05.01-Getting-Started.md) — `net/http` server, `ServeMux`, timeouts
- [05.02 - Handling HTTP Requests](02-Intermediate/05-Creating-Web-Services-with-Go/05.02-Handling-HTTP-Requests.md) — methods, path values, JSON, status codes
- [05.03 - Persisting Data](02-Intermediate/05-Creating-Web-Services-with-Go/05.03-Persisting-Data.md) — repository behind the handler, `context`
- [05.04 - Using Templating](02-Intermediate/05-Creating-Web-Services-with-Go/05.04-Using-Templating.md) — `html/template` and automatic encoding
- [05.05 - Using WebSockets](02-Intermediate/05-Creating-Web-Services-with-Go/05.05-Using-WebSockets.md) — WebSocket upgrade, one writer per connection

### 06 - Creating Web Applications with Go

- [06.01 - Introduction](02-Intermediate/06-Creating-Web-Applications-with-Go/06.01-Introduction.md) — request lifecycle, handler, middleware
- [06.02 - Handling Basic Requests](02-Intermediate/06-Creating-Web-Applications-with-Go/06.02-Handling-Basic-Requests.md) — route patterns, static files, redirects
- [06.03 - Working with HTTP Requests](02-Intermediate/06-Creating-Web-Applications-with-Go/06.03-Working-with-HTTP-Requests.md) — forms, cookies, body size limit
- [06.04 - Templating Basics](02-Intermediate/06-Creating-Web-Applications-with-Go/06.04-Templating-Basics.md) — template actions, pipeline, variables
- [06.05 - Creating Smart Templates](02-Intermediate/06-Creating-Web-Applications-with-Go/06.05-Creating-Smart-Templates.md) — `define`, `block`, layout, `FuncMap`
- [06.06 - Creating Middleware](02-Intermediate/06-Creating-Web-Applications-with-Go/06.06-Creating-Middleware.md) — middleware as a function that returns a handler
- [06.07 - Interacting with Database](02-Intermediate/06-Creating-Web-Applications-with-Go/06.07-Interacting-with-Database.md) — query within the request, `ErrNoRows` as 404
- [06.08 - Beyond HTTP/1.1](02-Intermediate/06-Creating-Web-Applications-with-Go/06.08-Beyond-HTTP-1.1.md) — HTTP/2 over TLS, graceful shutdown
- [06.09 - Testing Web Applications](02-Intermediate/06-Creating-Web-Applications-with-Go/06.09-Testing-Web-Applications.md) — `httptest`, recorder and server

### 07 - Gin: A Web Application Framework for Go

- [07.01 - Overview](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.01-Overview.md) — `Engine`, `gin.Default` and `gin.New`
- [07.02 - Routing](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.02-Routing.md) — routes, groups, path parameters
- [07.03 - Routing Requests](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.03-Routing-Requests.md) — versioning, `NoRoute`
- [07.04 - Working with Requests](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.04-Working-with-Requests.md) — `ShouldBindJSON`, binding tags
- [07.05 - Generating Responses](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.05-Generating-Responses.md) — JSON, status, `AbortWithStatusJSON`
- [07.06 - Working with Responses](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.06-Working-with-Responses.md) — headers, cookies, file, stream
- [07.07 - Middleware](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.07-Middleware.md) — `c.Next`, `c.Abort`, order
- [07.08 - Using Middleware](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.08-Using-Middleware.md) — Logger, Recovery, custom middleware
- [07.09 - Testing and Error Handling](02-Intermediate/07-Gin-A-Web-Application-Framework-for-Go/07.09-Testing-and-Error-Handling.md) — `httptest`, central errors, binding

### 08 - Concurrent Programming with Go

- [08.01 - Concurrency in Go](02-Intermediate/08-Concurrent-Programming-with-Go/08.01-Concurrency-in-Go.md) — concurrency and parallelism, `GOMAXPROCS`
- [08.02 - Goroutines](02-Intermediate/08-Concurrent-Programming-with-Go/08.02-Goroutines.md) — goroutine, leaks, `context`, `errgroup`
- [08.03 - The Sync Package](02-Intermediate/08-Concurrent-Programming-with-Go/08.03-The-Sync-Package.md) — `WaitGroup`, `Mutex`, `Once`, `atomic`
- [08.04 - Channels](02-Intermediate/08-Concurrent-Programming-with-Go/08.04-Channels.md) — buffer, direction, `close`, `select`
- [08.05 - Common Concurrency Patterns](02-Intermediate/08-Concurrent-Programming-with-Go/08.05-Common-Concurrency-Patterns.md) — pipeline, worker pool, fan-out
- [08.06 - Additional Tools to Support Concurrent Programming](02-Intermediate/08-Concurrent-Programming-with-Go/08.06-Additional-Tools-to-Support-Concurrent-Programming.md) — race detector, `singleflight`, goroutine profile

### 09 - Building Go Web Services and Applications

- [09.01 - Building a Web Service](02-Intermediate/09-Building-Go-Web-Services-and-Applications/09.01-Building-a-Web-Service.md) — small REST service, handlers, JSON
- [09.02 - Working with JSON](02-Intermediate/09-Building-Go-Web-Services-and-Applications/09.02-Working-with-JSON.md) — `encoding/json`, tags, `Decoder`
- [09.03 - Adding a Database](02-Intermediate/09-Building-Go-Web-Services-and-Applications/09.03-Adding-a-Database.md) — repository and migrations
- [09.04 - Creating a Web App](02-Intermediate/09-Building-Go-Web-Services-and-Applications/09.04-Creating-a-Web-App.md) — the same domain as HTML and as API
- [09.05 - Using Template](02-Intermediate/09-Building-Go-Web-Services-and-Applications/09.05-Using-Template.md) — `html/template` alongside the service

### 10 - Secure Coding in Go

- [10.01 - Software Security and Risk Principles](02-Intermediate/10-Secure-Coding-in-Go/10.01-Software-Security-and-Risk-Principles.md) — confidentiality, integrity, availability, least privilege
- [10.02 - General Coding Practices](02-Intermediate/10-Secure-Coding-in-Go/10.02-General-Coding-Practices.md) — dependencies, `govulncheck`, secrets outside source code
- [10.03 - Input Validation](02-Intermediate/10-Secure-Coding-in-Go/10.03-Input-Validation.md) — allowlists, lengths, `strconv`
- [10.04 - Output Encoding](02-Intermediate/10-Secure-Coding-in-Go/10.04-Output-Encoding.md) — `html/template`, JSON, not `text/template` for HTML
- [10.05 - Authentication and Password Management](02-Intermediate/10-Secure-Coding-in-Go/10.05-Authentication-and-Password-Management.md) — bcrypt or argon2id, `crypto/rand`
- [10.06 - Session Management](02-Intermediate/10-Secure-Coding-in-Go/10.06-Session-Management.md) — `HttpOnly`, `Secure`, `SameSite`, session rotation
- [10.07 - Access Control](02-Intermediate/10-Secure-Coding-in-Go/10.07-Access-Control.md) — authentication and authorization, deny by default
- [10.08 - Cryptographic Practices](02-Intermediate/10-Secure-Coding-in-Go/10.08-Cryptographic-Practices.md) — `crypto/rand`, TLS, AES-GCM
- [10.09 - Data Protection](02-Intermediate/10-Secure-Coding-in-Go/10.09-Data-Protection.md) — encryption at rest and in transit, key separation
- [10.10 - Communication Security](02-Intermediate/10-Secure-Coding-in-Go/10.10-Communication-Security.md) — TLS 1.2 at minimum, certificates
- [10.11 - System Configuration](02-Intermediate/10-Secure-Coding-in-Go/10.11-System-Configuration.md) — configuration from the environment, closed by default
- [10.12 - Database Security](02-Intermediate/10-Secure-Coding-in-Go/10.12-Database-Security.md) — parameterized queries, least database privilege
- [10.13 - File Management](02-Intermediate/10-Secure-Coding-in-Go/10.13-File-Management.md) — `filepath.Clean`, restriction to the base directory
- [10.14 - Error Handling and Logging](02-Intermediate/10-Secure-Coding-in-Go/10.14-Error-Handling-and-Logging.md) — internal log, generic message to the client

### 11 - Creating Well-Tested Applications in Go

- [11.01 - Introduction](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.01-Introduction.md) — why tests, what not to test
- [11.02 - Testing in Go](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.02-Testing-in-Go.md) — `testing` package, `Errorf` and `Fatalf`, `t.Helper`
- [11.03 - Creating and Running Tests](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.03-Creating-and-Running-Tests.md) — `go test`, `-run`, cache
- [11.04 - Unit Testing](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.04-Unit-Testing.md) — unit isolation, `interface` and fake
- [11.05 - Testing Business Logic](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.05-Testing-Business-Logic.md) — pure functions, table-driven rules
- [11.06 - Blackbox Testing](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.06-Blackbox-Testing.md) — external test package `foo_test`
- [11.07 - Example Tests](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.07-Example-Tests.md) — `Example` functions and `Output`
- [11.08 - Testing Code Samples](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.08-Testing-Code-Samples.md) — examples as executable documentation
- [11.09 - Testing Web Applications](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.09-Testing-Web-Applications.md) — `httptest` for handlers and templates
- [11.10 - Testing HTTP Services](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.10-Testing-HTTP-Services.md) — `httptest.Server` for client tests
- [11.11 - Code Coverage Analysis](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.11-Code-Coverage-Analysis.md) — what coverage does not prove
- [11.12 - Generating Code Coverage Reports](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.12-Generating-Code-Coverage-Reports.md) — `coverprofile`, `go tool cover`, `coverpkg`
- [11.13 - Performance Benchmarking](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.13-Performance-Benchmarking.md) — when to measure, and when not to
- [11.14 - Benchmark Testing](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.14-Benchmark-Testing.md) — `Benchmark`, `b.N`, `ReportAllocs`
- [11.15 - Fuzz Testing](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.15-Fuzz-Testing.md) — `Fuzz`, `f.Add`, corpus
- [11.16 - Benchmarking and Profiling](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.16-Benchmarking-and-Profiling.md) — `go test -cpuprofile` and `-memprofile`
- [11.17 - Performance Profiling](02-Intermediate/11-Creating-Well-Tested-Applications-in-Go/11.17-Performance-Profiling.md) — reading profiles from tests

### 12 - Building Distributed Applications with Go

- [12.01 - Elements of Distributed System](02-Intermediate/12-Building-Distributed-Applications-with-Go/12.01-Elements-of-Distributed-System.md) — partial failure, timeout, idempotency
- [12.02 - Service Registration](02-Intermediate/12-Building-Distributed-Applications-with-Go/12.02-Service-Registration.md) — registering an instance in the registry
- [12.03 - Service Discovery](02-Intermediate/12-Building-Distributed-Applications-with-Go/12.03-Service-Discovery.md) — finding healthy instances
- [12.04 - Status Monitoring](02-Intermediate/12-Building-Distributed-Applications-with-Go/12.04-Status-Monitoring.md) — liveness and readiness

### 13 - Managing Errors in Go

- [13.01 - Error Management Overview](02-Intermediate/13-Managing-Errors-in-Go/13.01-Error-Management-Overview.md) — `error` is a value, check immediately
- [13.02 - Error Types and Validation](02-Intermediate/13-Managing-Errors-in-Go/13.02-Error-Types-and-Validation.md) — sentinel, types, wrapping, validation
- [13.03 - Panic, Defer and Recover](02-Intermediate/13-Managing-Errors-in-Go/13.03-Panic-Defer-and-Recover.md) — `panic` for programmer errors, `recover` at the boundary
- [13.04 - Error Customization, Switching and Assertion](02-Intermediate/13-Managing-Errors-in-Go/13.04-Error-Customization-Switching-and-Assertion.md) — `errors.Is`, `errors.As`, `Unwrap`
- [13.05 - Testing Errors](02-Intermediate/13-Managing-Errors-in-Go/13.05-Testing-Errors.md) — table tests for error paths
- [13.06 - Responding to Channel Errors](02-Intermediate/13-Managing-Errors-in-Go/13.06-Responding-to-Channel-Errors.md) — channel for `error`, struct with value and `error`
- [13.07 - Handling Error Responses in Goroutines](02-Intermediate/13-Managing-Errors-in-Go/13.07-Handling-Error-Responses-in-Goroutines.md) — `errgroup`, cancel on first error
- [13.08 - Handling HTTP Errors and Statuses](02-Intermediate/13-Managing-Errors-in-Go/13.08-Handling-HTTP-Errors-and-Statuses.md) — mapping an error to an HTTP status
- [13.09 - Closures](02-Intermediate/13-Managing-Errors-in-Go/13.09-Closures.md) — closure, `defer` in a loop, middleware factory

### 14 - Profiling Go Applications

- [14.01 - Introduction to Profiling in Go Language](02-Intermediate/14-Profiling-Go-Applications/14.01-Introduction-to-Profiling-in-Go-Language.md) — measure first, CPU and memory
- [14.02 - Profiling with pprof](02-Intermediate/14-Profiling-Go-Applications/14.02-Profiling-with-pprof.md) — `pprof` on localhost only, `top`, `list`, `web`
- [14.03 - Performance Tuning in Real World](02-Intermediate/14-Profiling-Go-Applications/14.03-Performance-Tuning-in-Real-World.md) — allocations, algorithm before micro-optimization

### 15 - The Go CLI Playbook

- [15.01 - Introduction](02-Intermediate/15-The-Go-CLI-Playbook/15.01-Introduction.md) — the `go` command as a toolchain
- [15.02 - Modifying the Build Environment](02-Intermediate/15-The-Go-CLI-Playbook/15.02-Modifying-the-Build-Environment.md) — `GOOS`, `GOARCH`, `ldflags`, build tags
- [15.03 - Building and Running Program](02-Intermediate/15-The-Go-CLI-Playbook/15.03-Building-and-Running-Program.md) — `go build`, `go run`, `go install`
- [15.04 - Modifying the Runtime Environment](02-Intermediate/15-The-Go-CLI-Playbook/15.04-Modifying-the-Runtime-Environment.md) — `GOGC`, `GOMEMLIMIT`, `GOMAXPROCS`
- [15.05 - Interacting with the Environment](02-Intermediate/15-The-Go-CLI-Playbook/15.05-Interacting-with-the-Environment.md) — `Getenv`, `LookupEnv`
- [15.06 - Managing Workspaces](02-Intermediate/15-The-Go-CLI-Playbook/15.06-Managing-Workspaces.md) — `go.work`, `use`, `sync`
- [15.07 - Managing Projects](02-Intermediate/15-The-Go-CLI-Playbook/15.07-Managing-Projects.md) — `tidy`, `vendor`, `verify`
- [15.08 - Inspecting Projects](02-Intermediate/15-The-Go-CLI-Playbook/15.08-Inspecting-Projects.md) — `go list`, `go doc`, `go version -m`
- [15.09 - Testing Programs](02-Intermediate/15-The-Go-CLI-Playbook/15.09-Testing-Programs.md) — `-race`, `-cover`, `-shuffle`, `-timeout`
- [15.10 - Building Shared Libraries](02-Intermediate/15-The-Go-CLI-Playbook/15.10-Building-Shared-Libraries.md) — `c-shared` and `c-archive`, `//export`

---

## 03 - Others

Specialized topics: Go's object model, code generation, gRPC, microservices, Protocol Buffers, go-kit, Docker, GORM, and document databases.

### 01 - Object-Oriented Programming with Go

- [01.01 - Is Go an Object-Oriented Language](03-Others/01-Object-Oriented-Programming-with-Go/01.01-Is-Go-an-Object-Oriented-Language.md) — Go has no classes; it has type, method, and interface
- [01.02 - Using Methods to Define Behavior](03-Others/01-Object-Oriented-Programming-with-Go/01.02-Using-Methods-to-Define-Behavior.md) — receiver, method set, value and pointer
- [01.03 - Exploring Data Encapsulation Strategies](03-Others/01-Object-Oriented-Programming-with-Go/01.03-Exploring-Data-Encapsulation-Strategies.md) — unexported fields, `NewT`, interface as boundary
- [01.04 - Employing Interfaces to Add Polymorphism](03-Others/01-Object-Oriented-Programming-with-Go/01.04-Employing-Interfaces-to-Add-Polymorphism.md) — implicit satisfaction, small interface
- [01.05 - Adopting Generic Programming](03-Others/01-Object-Oriented-Programming-with-Go/01.05-Adopting-Generic-Programming.md) — type parameters, constraints, when generics

### 02 - Code Generation in Go

- [02.01 - Introduction and Overview](03-Others/02-Code-Generation-in-Go/02.01-Introduction-and-Overview.md) — `go generate` and `//go:generate`
- [02.02 - Review Generation Tools (Packaged)](03-Others/02-Code-Generation-in-Go/02.02-Review-Generation-Tools-Packaged.md) — `stringer` and `protoc-gen-go` as external generators
- [02.03 - Review Generation Tools (In-source)](03-Others/02-Code-Generation-in-Go/02.03-Review-Generation-Tools-In-source.md) — `go/ast`, `go/parser`, `go/format`
- [02.04 - Advanced Go Generate Flags](03-Others/02-Code-Generation-in-Go/02.04-Advanced-Go-Generate-Flags.md) — `-x`, `-n`, `-run`, `GOFILE`, `GOPACKAGE`
- [02.05 - Overview of Templates](03-Others/02-Code-Generation-in-Go/02.05-Overview-of-Templates.md) — `text/template` for code generation
- [02.06 - Templates - Variables](03-Others/02-Code-Generation-in-Go/02.06-Templates-Variables.md) — variables in the template
- [02.07 - Templates - Conditionals](03-Others/02-Code-Generation-in-Go/02.07-Templates-Conditionals.md) — `if`, `eq`, `with`
- [02.08 - Templates - Loop](03-Others/02-Code-Generation-in-Go/02.08-Templates-Loop.md) — `range`
- [02.09 - Templates - Functions](03-Others/02-Code-Generation-in-Go/02.09-Templates-Functions.md) — `FuncMap`
- [02.10 - Closure and Links](03-Others/02-Code-Generation-in-Go/02.10-Closure-and-Links.md) — the full generation pipeline and the transition to gRPC

### 03 - gRPC Fundamentals in Go

- [03.01 - Introduction](03-Others/03-gRPC-Fundamentals-in-Go/03.01-Introduction.md) — RPC, HTTP/2, protobuf, IDL
- [03.02 - Creating Servers](03-Others/03-gRPC-Fundamentals-in-Go/03.02-Creating-Servers.md) — server, service registration, `Serve`
- [03.03 - Creating Clients](03-Others/03-gRPC-Fundamentals-in-Go/03.03-Creating-Clients.md) — client, deadline, `Close` of the connection
- [03.04 - Working with Streaming Responses](03-Others/03-gRPC-Fundamentals-in-Go/03.04-Working-with-Streaming-Responses.md) — server, client, and bidirectional stream

### 04 - Building Microservices in Go

- [04.01 - Introduction](03-Others/04-Building-Microservices-in-Go/04.01-Introduction.md) — service boundaries, synchronous and asynchronous
- [04.02 - Creating HTTP Servers](03-Others/04-Building-Microservices-in-Go/04.02-Creating-HTTP-Servers.md) — HTTP server, timeouts, `Shutdown`
- [04.03 - Serving Static Content](03-Others/04-Building-Microservices-in-Go/04.03-Serving-Static-Content.md) — `FileServer` and `embed.FS`
- [04.04 - Routing Requests](03-Others/04-Building-Microservices-in-Go/04.04-Routing-Requests.md) — `ServeMux` in Go 1.22, path values
- [04.05 - JSON Messaging](03-Others/04-Building-Microservices-in-Go/04.05-JSON-Messaging.md) — DTO, versioning JSON messages
- [04.06 - Creating Middleware](03-Others/04-Building-Microservices-in-Go/04.06-Creating-Middleware.md) — logging, authorization, request ID
- [04.07 - gRPC Messaging](03-Others/04-Building-Microservices-in-Go/04.07-gRPC-Messaging.md) — when gRPC instead of JSON

### 05 - Enhancing Application Communication with gRPC in Go

- [05.01 - Introduction](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.01-Introduction.md) — the same contract in multiple languages
- [05.02 - gRPC Concepts](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.02-gRPC-Concepts.md) — channel, stub, status codes, metadata
- [05.03 - Preparing for gRPC](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.03-Preparing-for-gRPC.md) — `protoc` and Go plugins
- [05.04 - Creating gRPC Servers with Go](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.04-Creating-gRPC-Servers-with-Go.md) — Go server
- [05.05 - Creating gRPC Clients with Go](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.05-Creating-gRPC-Clients-with-Go.md) — Go client
- [05.06 - Creating gRPC Servers in Java](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.06-Creating-gRPC-Servers-in-Java.md) — Java server, `ServerBuilder`
- [05.07 - Creating gRPC Clients in Java](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.07-Creating-gRPC-Clients-in-Java.md) — Java client
- [05.08 - Creating gRPC Servers with C#](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.08-Creating-gRPC-Servers-with-CSharp.md) — C# server, `MapGrpcService`
- [05.09 - Creating gRPC Clients with C#](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.09-Creating-gRPC-Clients-with-CSharp.md) — C# client, `GrpcChannel`
- [05.10 - Creating gRPC Server with Node.js](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.10-Creating-gRPC-Server-with-Node.js.md) — Node server, `@grpc/grpc-js`
- [05.11 - Creating gRPC Clients with Node.js](03-Others/05-Enhancing-Application-Communication-with-gRPC-in-Go/05.11-Creating-gRPC-Clients-with-Node.js.md) — Node client

### 06 - Protocol Buffer Fundamentals

- [06.01 - Why Choose Protocol Buffers](03-Others/06-Protocol-Buffer-Fundamentals/06.01-Why-Choose-Protocol-Buffers.md) — schema, binary format, compatibility
- [06.02 - Creating Messages](03-Others/06-Protocol-Buffer-Fundamentals/06.02-Creating-Messages.md) — proto3, fields, enum, repeated, oneof
- [06.03 - Generating Source Code](03-Others/06-Protocol-Buffer-Fundamentals/06.03-Generating-Source-Code.md) — `protoc`, `protoc-gen-go`, `go_package`
- [06.04 - Versioning Message Definitions](03-Others/06-Protocol-Buffer-Fundamentals/06.04-Versioning-Message-Definitions.md) — `reserved`, do not change field numbers

### 07 - Go kit 0.12 Fundamentals

- [07.01 - The Framework and Expectations](03-Others/07-Go-kit-0.12-Fundamentals/07.01-The-Framework-and-Expectations.md) — service, endpoint, transport
- [07.02 - What Are the Challenges with Writing Services](03-Others/07-Go-kit-0.12-Fundamentals/07.02-What-Are-the-Challenges-with-Writing-Services.md) — operational service problems, not only business logic
- [07.03 - Setting up Your Environment](03-Others/07-Go-kit-0.12-Fundamentals/07.03-Setting-up-Your-Environment.md) — modules, dependencies, project layout
- [07.04 - Services Components and Principles](03-Others/07-Go-kit-0.12-Fundamentals/07.04-Services-Components-and-Principles.md) — layers and middleware around the service
- [07.05 - Building the Business Logic](03-Others/07-Go-kit-0.12-Fundamentals/07.05-Building-the-Business-Logic.md) — service interface and implementation
- [07.06 - Building the Request/Response Structure](03-Others/07-Go-kit-0.12-Fundamentals/07.06-Building-the-Request-Response-Structure.md) — request and response types
- [07.07 - Exposing the Endpoint](03-Others/07-Go-kit-0.12-Fundamentals/07.07-Exposing-the-Endpoint.md) — `endpoint.Endpoint`
- [07.08 - Transport and Communication](03-Others/07-Go-kit-0.12-Fundamentals/07.08-Transport-and-Communication.md) — HTTP transport, encode and decode
- [07.09 - Core Service Review](03-Others/07-Go-kit-0.12-Fundamentals/07.09-Core-Service-Review.md) — review of layers before logging
- [07.10 - Implement Transport Logging](03-Others/07-Go-kit-0.12-Fundamentals/07.10-Implement-Transport-Logging.md) — log at the transport boundary
- [07.11 - Implement Application Logging](03-Others/07-Go-kit-0.12-Fundamentals/07.11-Implement-Application-Logging.md) — log in the application
- [07.12 - Instrumenting the Service](03-Others/07-Go-kit-0.12-Fundamentals/07.12-Instrumenting-the-Service.md) — metrics
- [07.13 - Visibility and Diagnostics Review](03-Others/07-Go-kit-0.12-Fundamentals/07.13-Visibility-and-Diagnostics-Review.md) — what is visible in production
- [07.14 - Service Discovery and Load Balancing](03-Others/07-Go-kit-0.12-Fundamentals/07.14-Service-Discovery-and-Load-Balancing.md) — discovery: platform and `kit/sd`
- [07.15 - Distributed Tracing](03-Others/07-Go-kit-0.12-Fundamentals/07.15-Distributed-Tracing.md) — OpenTelemetry around the endpoint
- [07.16 - Reconstruct and Review Service Components](03-Others/07-Go-kit-0.12-Fundamentals/07.16-Reconstruct-and-Review-Service-Components.md) — reconstituting the full service
- [07.17 - Closure and Materials](03-Others/07-Go-kit-0.12-Fundamentals/07.17-Closure-and-Materials.md) — summary and transition to Docker

### 08 - Developing Go Apps with Docker

> **Image and delivery.** Multi-platform build, a small runtime image, Compose, Delve only in a debug image, and CI. Area 09 is the contract around that image: listen address and publish, tests in a container, `go:embed`, and the registry.

- [08.01 - Building a Multi-platform Docker Image with Dependencies](03-Others/08-Developing-Go-Apps-with-Docker/08.01-Building-a-Multi-platform-Docker-Image-with-Dependencies.md) — `buildx`, multiple platforms, downloading modules
- [08.02 - Optimizing the App Docker Image](03-Others/08-Developing-Go-Apps-with-Docker/08.02-Optimizing-the-App-Docker-Image.md) — multi-stage, `CGO_ENABLED=0`, distroless
- [08.03 - Managing the App Using Docker-compose](03-Others/08-Developing-Go-Apps-with-Docker/08.03-Managing-the-App-Using-Docker-compose.md) — Compose, healthcheck, env outside git
- [08.04 - Debugging and Maintaining the Containerized App](03-Others/08-Developing-Go-Apps-with-Docker/08.04-Debugging-and-Maintaining-the-Containerized-App.md) — logs, Delve only in the debug image
- [08.05 - Integrating the App with CI/CD Pipelines](03-Others/08-Developing-Go-Apps-with-Docker/08.05-Integrating-the-App-with-CI-CD-Pipelines.md) — CI: test, image, scan

### 09 - Docker for Software Development: Go Apps

> **Around the image.** Listen address and publish, tests in a container, `go:embed`, and the registry. Building and shipping the image is area 08.

- [09.01 - Building Multi-stage Dockerfiles for Go Applications](03-Others/09-Docker-for-Software-Development-Go-Apps/09.01-Building-Multi-stage-Dockerfiles-for-Go-Applications.md) — builder and runtime, module cache
- [09.02 - Configuring Ports and Environment Variables](03-Others/09-Docker-for-Software-Development-Go-Apps/09.02-Configuring-Ports-and-Environment-Variables.md) — `EXPOSE` and publish, env per the 12-factor model
- [09.03 - Testing Go Applications in Containers](03-Others/09-Docker-for-Software-Development-Go-Apps/09.03-Testing-Go-Applications-in-Containers.md) — `go test` in a container, Testcontainers
- [09.04 - Embedding Static Resources](03-Others/09-Docker-for-Software-Development-Go-Apps/09.04-Embedding-Static-Resources.md) — `go:embed` instead of copying static files
- [09.05 - Deploying Locally and to a Docker Registry](03-Others/09-Docker-for-Software-Development-Go-Apps/09.05-Deploying-Locally-and-to-a-Docker-Registry.md) — tag, push, digest

### 10 - GORM: An Object Relational Mapper for Go

- [10.01 - Introduction](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.01-Introduction.md) — ORM and `database/sql`, the trade-off
- [10.02 - Defining Schemas](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.02-Defining-Schemas.md) — models, tags, `AutoMigrate`
- [10.03 - Creating, Updating, and Deleting Records](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.03-Creating-Updating-and-Deleting-Records.md) — `Create`, `Save`, `Updates`, soft delete
- [10.04 - Querying the Database](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.04-Querying-the-Database.md) — `First`, `Find`, `Where`
- [10.05 - Working with Relationships](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.05-Working-with-Relationships.md) — has many, belongs to, `Preload`
- [10.06 - Modifying Schemas](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.06-Modifying-Schemas.md) — migrations, SQL in production
- [10.07 - Advanced Topics](03-Others/10-GORM-An-Object-Relational-Mapper-for-Go/10.07-Advanced-Topics.md) — hooks, `Transaction`, scopes

### 11 - Accessing No-Relational Database in Go

- [11.01 - Getting Started with Go and Non-relational Databases](03-Others/11-Accessing-No-Relational-Database-in-Go/11.01-Getting-Started-with-Go-and-Non-relational-Databases.md) — MongoDB driver, ping, BSON
- [11.02 - Implementing CRUD Operations](03-Others/11-Accessing-No-Relational-Database-in-Go/11.02-Implementing-CRUD-Operations.md) — `InsertOne`, `FindOne`, `UpdateOne`, `DeleteOne`
