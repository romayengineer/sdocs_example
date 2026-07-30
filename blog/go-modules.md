# Go Modules and Packages

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | beginner |
| **Reading time** | 7 min |
| **Word count** | ~1100 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.22 |
| **License** | CC-BY-4.0 |
| **Series** | Go Fundamentals (Part 3) |


## Prerequisites

- Basic Go syntax and project setup

Go modules are the built-in dependency management system introduced in Go 1.11 and made default in Go 1.16.

## Initializing a Module

```go
go mod init github.com/user/myproject
```

This creates a `go.mod` file:

```
module github.com/user/myproject

go 1.22
```

## Adding Dependencies

```go
go get github.com/gorilla/mux@v1.8.1
```

This updates `go.mod` and creates `go.sum` with checksums for reproducible builds.

## go.mod Structure

```
module github.com/user/myproject

go 1.22

require (
    github.com/gorilla/mux v1.8.1
    golang.org/x/text v0.14.0
)

exclude github.com/old/module v0.1.0

replace github.com/original => ./local/fork
```

- `require` — direct dependencies
- `exclude` — versions to skip
- `replace` — substitute a module (useful for local development)

## go mod tidy

```sh
go mod tidy
```

Cleans up dependencies — adds missing ones, removes unused ones.

## Module Workspaces (Go 1.18+)

Workspaces let you work on multiple modules simultaneously:

```sh
go work init ./module1 ./module2
```

Creates a `go.work` file that overrides individual `go.mod` files during development — no need for `replace` directives.

## Packages

A package is a directory of Go files with the same `package` declaration:

```
myproject/
├── go.mod
├── main.go              # package main
├── handler/
│   ├── handler.go       # package handler
│   └── handler_test.go  # package handler (or handler_test for external tests)
└── internal/
    └── auth/
        └── auth.go      # package auth (internal — only accessible within myproject)
```

## Import Paths

```go
import (
    "fmt"                                    // standard library
    "github.com/user/myproject/handler"      // local package
    "github.com/gorilla/mux"                 // external dependency
)
```

## Naming Conventions

- Package names are lowercase, single word preferred
- No underscores or mixedCaps — use `httputil` not `http_util`
- The package name should match the directory name

## Internal Packages

Packages under `internal/` are only importable by code rooted at the parent:

```
myproject/
  internal/auth/   → only importable by myproject/...
```

This enforces encapsulation boundaries.

## Vendoring

Save exact copies of dependencies:

```sh
go mod vendor
```

Useful for CI reproducibility or air-gapped environments.


## Related Posts

- Structs and Methods in Go
- Testing in Go


**Tags:** `go` `modules` `packages` `dependencies` 

## References

 - [Go blog: Modules](https://go.dev/blog/using-go-modules)
 - [Go modules reference](https://go.dev/ref/mod)

