# Defer, Panic, and Recover


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 6 min |
| **Word count** | ~950 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.22 |
| **License** | CC-BY-4.0 |

## Prerequisites

- Basic Go functions and control flow

## Overview

`defer`, `panic`, and `recover` form Go's mechanism for cleanup and exceptional control flow.

## Basic Usage

`defer` schedules a function call to run when the enclosing function returns:

```go
func readFile(path string) ([]byte, error) {
    f, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    return io.ReadAll(f)
}
```

`f.Close()` runs when `readFile` returns, regardless of the return path.

### Defer Semantics

- Arguments are **evaluated immediately**, not at defer time:

```go
func example() {
    x := 1
    defer fmt.Println(x) // prints "1", not "2"
    x = 2
}
```

- Deferred calls execute in **LIFO order** (last in, first out):

```go
defer fmt.Println("first")  // prints second
defer fmt.Println("second") // prints first
```

### Common Use Cases

- Closing files, connections, or other resources
- Unlocking mutexes:

```go
mu.Lock()
defer mu.Unlock()
```

- Timing functions:

```go
func timed() {
    defer log.Printf("took %v", time.Since(time.Now()))
    // ...
}
```

Wait — arguments are evaluated immediately, so `time.Now()` is captured at the call site. To defer a time measurement:

```go
func timed() {
    start := time.Now()
    defer func() {
        log.Printf("took %v", time.Since(start))
    }()
    // ...
}
```

- Recovering from panics (see below)

## Best Practices

- Always pair `Lock`/`Unlock`, `Open`/`Close` with `defer`
- Keep deferred functions small and simple
- Don't `defer` inside loops — resources accumulate until the function returns
- Use `recover` sparingly, mainly to prevent server crashes in HTTP handlers
- Never `panic` in library code unless you document it explicitly

## Advanced Topics

`panic` stops the normal flow and begins unwinding the stack:

```go
func must(err error) {
    if err != nil {
        panic(err)
    }
}
```

As the stack unwinds, deferred functions execute. If no `recover` catches the panic, the program crashes with a stack trace.

**When to use panic:**
- Truly unrecoverable states (nil dereference, impossible invariants)
- Developer mistakes during early development
- Initialization failures in `init` functions

**When NOT to use panic:**
- Expected errors (use `error` return values instead)
- In library code intended for general consumption

`recover` regains control of a panicking goroutine:

```go
func safeCall(fn func()) (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("panic: %v", r)
        }
    }()
    fn()
    return nil
}
```

`recover` only works inside a deferred function. It returns the panic value, or `nil` if no panic is in progress.

### Named Return Values and Defer

Deferred functions can read and modify named return values:

```go
func get() (result string, err error) {
    defer func() {
        if err != nil {
            result = ""
        }
    }()
    result = doSomething()
    return
}
```

This pattern is useful for error wrapping or cleanup.

## Related Posts

- Error Handling in Go
- Goroutines and Channels

**Tags:** `go` `defer` `panic` `recover` `cleanup` 

## References

 - [Effective Go: Defer](https://go.dev/doc/effective_go#defer)
 - [Go blog: Defer](https://go.dev/blog/defer-panic-and-recover)

