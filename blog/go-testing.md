# Testing in Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 8 min |
| **Word count** | ~1200 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.22 |
| **License** | CC-BY-4.0 |
| **Series** | Go Fundamentals (Part 4) |

## Prerequisites

- Go Modules and Packages
- Basic Go syntax

## Overview

Go has a built-in testing framework in the standard library — no external dependencies needed.

## Basic Usage

```go
// math.go
func Add(a, b int) int {
    return a + b
}

// math_test.go
package main

import "testing"

func TestAdd(t *testing.T) {
    got := Add(2, 3)
    want := 5
    if got != want {
        t.Errorf("Add(2, 3) = %d; want %d", got, want)
    }
}
```

Run tests:

```sh
go test
go test -v        # verbose
go test ./...     # all packages
```

### Table-Driven Tests

The idiomatic Go testing pattern:

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name string
        a, b int
        want int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, 1, 0},
        {"zero", 0, 0, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := Add(tt.a, tt.b)
            if got != tt.want {
                t.Errorf("got %d, want %d", got, tt.want)
            }
        })
    }
}
```

`t.Run` creates sub-tests with their own `*testing.T`, improving error reporting and allowing selective execution.

### Test Helpers

Mark helpers with `t.Helper()` so failures point to the caller, not the helper:

```go
func assertEqual(t *testing.T, got, want int) {
    t.Helper()
    if got != want {
        t.Errorf("got %d, want %d", got, want)
    }
}
```

### Coverage

```sh
go test -cover
go test -coverprofile=coverage.out
go tool cover -html=coverage.out    # visualize in browser
```

### Fuzzing (Go 1.18+)

Automatically generates random inputs to find edge cases:

```go
func FuzzAdd(f *testing.F) {
    f.Add(2, 3)           // seed corpus
    f.Fuzz(func(t *testing.T, a, b int) {
        result := Add(a, b)
        if result != a+b {
            t.Errorf("unexpected result")
        }
    })
}
```

Run with:

```sh
go test -fuzz=FuzzAdd
```

### Test Caching

Go caches test results when neither code nor tests changed. Skip with:

```sh
go test -count=1
```

## Best Practices

- One test file per package: `*_test.go`
- Use table-driven tests as the default pattern
- Use `t.Errorf` for non-fatal failures, `t.Fatalf` when continued testing makes no sense
- External test packages (`package main_test`) avoid import cycles and test the public API
- Run `go test -race` to detect data races
- Keep tests deterministic and fast

## Related Posts

- Go Modules and Packages
- Error Handling in Go

**Tags:** `go` `testing` `fuzzing` `quality` 

## References

 - [Go blog: Fuzzing](https://go.dev/blog/fuzz)
 - [Go blog: Table-driven tests](https://go.dev/wiki/TableDrivenTests)
 - [Go testing package docs](https://pkg.go.dev/testing)

