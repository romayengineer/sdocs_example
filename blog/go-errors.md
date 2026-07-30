# Error Handling in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | beginner |
| **Reading time** | 5 min |
| **Word count** | ~700 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.20 |
| **License** | CC-BY-4.0 |


## Prerequisites

- Interfaces in Go

Go treats errors as values. Instead of exceptions, functions return an `error` value that the caller checks explicitly.

## The Error Interface

```go
type error interface {
    Error() string
}
```

Any type with an `Error() string` method satisfies the interface:

```go
type ValidationError struct {
    Field string
    Msg   string
}

func (e ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Msg)
}
```

## Sentinel Errors

Predeclared errors used with `==`:

```go
var ErrNotFound = errors.New("not found")

func Find(id int) (Item, error) {
    // ...
    return Item{}, ErrNotFound
}
```

## Wrapping Errors

Go 1.13 introduced error wrapping with `%w`:

```go
if err := doSomething(); err != nil {
    return fmt.Errorf("doSomething failed: %w", err)
}
```

## errors.Is and errors.As

Replace `==` with `errors.Is` for wrapped errors:

```go
if errors.Is(err, ErrNotFound) {
    // handle not found
}
```

Use `errors.As` to extract a specific error type:

```go
var ve *ValidationError
if errors.As(err, &ve) {
    fmt.Println(ve.Field, ve.Msg)
}
```

## errors.Join

Go 1.20 introduced joining multiple errors:

```go
err1 := errors.New("first")
err2 := errors.New("second")
joined := errors.Join(err1, err2)
```

## Best Practices

- Always check errors — unhandled errors are silent bugs
- Wrap errors with context using `%w`
- Use sentinel errors only for expected, recoverable cases
- For unexpected errors, use custom types
- Never `panic` in library code

Go's error handling forces you to think about failure modes explicitly, leading to more robust software.


## Related Posts

- Generics in Go
- Interfaces in Go


**Tags:** `go` `errors` `error-handling` `best-practices` 

## References

 - [Go blog: Error handling](https://go.dev/blog/go1.13-errors)
 - [Package errors docs](https://pkg.go.dev/errors)

