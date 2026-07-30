# Panic and Recover in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | advanced |
| **Reading time** | 6 min |
| **Word count** | ~850 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |


## Prerequisites

- Error Handling in Go
- Defer in Go

Go uses `panic` and `recover` for exceptional situations. Unlike exceptions in other languages, they are reserved for truly unrecoverable errors.

## Panic

A panic stops normal execution and begins unwinding the stack:

```go
func main() {
    fmt.Println("start")
    panic("something went wrong")
    fmt.Println("end") // never reached
}
```

Panics occur automatically on:
- Index out of bounds
- Nil pointer dereference
- Sending on a closed channel
- Type assertion failure
- Writing to a nil map

## Recover

`recover` regains control of a panicking goroutine. It only works inside a deferred function:

```go
func safeCall() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("recovered from:", r)
        }
    }()
    panic("oh no!")
}
// safeCall returns normally
```

## Deferred Recovery Pattern

```go
func handleRequest() {
    defer func() {
        if r := recover(); r != nil {
            log.Printf("panic recovered: %v", r)
            // Optionally re-panic with more context
            panic(fmt.Errorf("handler panic: %w", r))
        }
    }()
    processRequest()
}
```

## Use Cases

Recover is appropriate in:
- HTTP middleware (prevent a single handler crash from taking down the server)
- Long-running workers (restart a failed task)
- Top-level goroutine launchers

```go
func serve() {
    for {
        conn := accept()
        go func() {
            defer func() {
                if r := recover(); r != nil {
                    log.Printf("connection handler panic: %v", r)
                }
            }()
            handleConnection(conn)
        }()
    }
}
```

## Re-panicking

Sometimes you want to log and re-throw:

```go
defer func() {
    if r := recover(); r != nil {
        log.Printf("panic: %v\n%s", r, debug.Stack())
        panic(r) // re-panic after logging
    }
}()
```

## Best Practices

- **Don't panic for regular errors** — use `error` return values
- **Don't recover blindly** — recovering from nil pointer dereferences hides bugs
- **Do recover at goroutine boundaries** — prevent one panic from crashing the whole program
- **Do log the stack trace** — use `debug.Stack()` when recovering
- **Do let the program crash** when in an unrecoverable state (e.g., corrupted data)

```go
// Good: recover at package boundary
func (s *Server) handle(conn net.Conn) {
    defer func() {
        if r := recover(); r != nil {
            s.log.Printf("recovered: %v", r)
        }
    }()
    s.serve(conn)
}
```

The standard library uses panic internally for things like `json.Marshal` with cyclic data, but these are always caught internally. Your application code should rarely call `panic` directly.


## Related Posts

- Error Handling in Go
- Defer in Go


**Tags:** `go` `panic` `recover` `error-handling` 

## References

 - [Go blog: Defer, Panic, Recover](https://go.dev/blog/defer-panic-and-recover)
 - [Go by example: Recover](https://gobyexample.com/recover)

