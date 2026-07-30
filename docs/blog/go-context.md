# Mastering Go's Context Package

⭐ **Featured Post**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Difficulty** | intermediate |
| **Reading time** | 8 min |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |


## Prerequisites

- Goroutines and channels
- Basic Go syntax

The `context` package is Go's standard mechanism for carrying deadlines, cancellation signals, and request-scoped values across API boundaries and goroutines.

## Why Context?

In any nontrivial Go program — especially HTTP servers, RPC handlers, and CLI tools — you need a way to:

- **Cancel** a long-running operation (user interrupts, timeouts)
- **Propagate deadlines** to downstream calls
- **Pass request-scoped data** (trace IDs, auth tokens) without polluting function signatures

Context makes all of this explicit and composable.

## Creating Contexts

The root of any context chain is `context.Background()`:

```go
ctx := context.Background()
```

For test or ad-hoc use, there is `context.TODO()` — same thing, but signals intent.

## WithCancel

Returns a derived context and a cancel function:

```go
ctx, cancel := context.WithCancel(context.Background())
defer cancel() // always call to release resources

go func() {
    select {
    case <-ctx.Done():
        return
    case <-time.After(2 * time.Second):
        fmt.Println("work done")
    }
}()

cancel() // signals the goroutine to stop
```

## WithTimeout and WithDeadline

Automatically cancel after a duration or at a specific time:

```go
// 100ms timeout
ctx, cancel := context.WithTimeout(context.Background(), 100*time.Millisecond)
defer cancel()

// or with a deadline
// ctx, cancel = context.WithDeadline(context.Background(), time.Now().Add(100*time.Millisecond))
```

## WithValue

Pass request-scoped data through the context chain:

```go
type traceKey struct{}

func WithTraceID(ctx context.Context, id string) context.Context {
    return context.WithValue(ctx, traceKey{}, id)
}

func TraceIDFromContext(ctx context.Context) (string, bool) {
    id, ok := ctx.Value(traceKey{}).(string)
    return id, ok
}
```

Use unexported struct keys to avoid collisions between packages.

## Checking Cancellation

Two patterns for respecting context cancellation:

### Channel-based (preferred)

```go
select {
case <-ctx.Done():
    return ctx.Err()
case result := <-ch:
    return result, nil
}
```

### Deadline check

```go
if deadline, ok := ctx.Deadline(); ok && time.Now().After(deadline) {
    return nil, context.DeadlineExceeded
}
```

## Best Practices

- Context is the first parameter of any function that may need cancellation
- Never store a context in a struct — pass it explicitly
- Always call the cancel function returned by `WithCancel` / `WithTimeout`
- Use context values only for request-scoped data, not optional parameters
- `context.Background()` is the root — use it in `main()` and top-level handlers

The context package is small but essential. Mastering it means writing Go programs that are safe, composable, and responsive to cancellation.



**Tags:** `go` `context` `concurrency` `best-practices` 


## References

 - [Go blog: Context](https://go.dev/blog/context)
 - [Package context docs](https://pkg.go.dev/context)

