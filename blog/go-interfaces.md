# Interfaces in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | intermediate |
| **Reading time** | 7 min |
| **Word count** | ~1000 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |
| **Series** | Go Fundamentals (Part 1) |


## Prerequisites

- Basic Go types and functions


## Overview

Interfaces in Go define behavior through method sets. Unlike many languages, interface satisfaction is implicit — no `implements` keyword needed.

## Basic Usage

```go
type Stringer interface {
    String() string
}
```

Any type that has the required methods automatically satisfies the interface:

```go
type Book struct {
    Title  string
    Author string
}

func (b Book) String() string {
    return fmt.Sprintf("%s by %s", b.Title, b.Author)
}

func print(s Stringer) {
    fmt.Println(s.String())
}
```

`interface{}` (or `any` in Go 1.18+) accepts values of any type:

```go
var x any = 42
x = "hello"
x = Book{"The Go Programming Language", "Donovan & Kernighan"}
```


## Examples

Extract the concrete value from an interface:

```go
var i any = "hello"
s, ok := i.(string)
if ok {
    fmt.Println(s) // "hello"
}
```

Handle multiple concrete types cleanly:

```go
func describe(i any) {
    switch v := i.(type) {
    case int:
        fmt.Printf("integer: %d\n", v)
    case string:
        fmt.Printf("string: %q\n", v)
    default:
        fmt.Printf("unknown type: %T\n", v)
    }
}
```

The stdlib is full of small, focused interfaces:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

Any type with a `Read([]byte) (int, error)` method is an `io.Reader` — `os.File`, `strings.Reader`, `bytes.Buffer`, and more.


## Advanced Topics

Interfaces can embed other interfaces:

```go
type ReadWriter interface {
    Reader
    Writer
}
```


## Summary

Go's interface system encourages small, composable abstractions. The rule of thumb: *accept interfaces, return concrete types.*

## Related Posts

- Structs and Methods in Go
- Goroutines and Channels


**Tags:** `go` `interfaces` `types` `design` 

## References

 - [Effective Go: Interfaces](https://go.dev/doc/effective_go#interfaces)
 - [Go blog: Interfaces](https://go.dev/blog/declaration-syntax)

