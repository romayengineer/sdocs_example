# Generics in Go

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
| **Version** | Go 1.18 |
| **License** | CC-BY-4.0 |


## Prerequisites

- Interfaces in Go
- Structs and Methods in Go


## Overview

Generics were added in Go 1.18, introducing type parameters for functions and types.

## Basic Usage

```go
func Reverse[T any](s []T) []T {
    n := len(s)
    out := make([]T, n)
    for i, v := range s {
        out[n-1-i] = v
    }
    return out
}

// Usage
ints := Reverse([]int{1, 2, 3})
strs := Reverse([]string{"a", "b", "c"})
```


## Best Practices

- Functions that operate on slices, maps, or channels of any type
- Data structures (stacks, trees, graphs) that hold any type
- Eliminating duplicated code for different types


## Common Pitfalls

- When `any` and type assertions suffice
- For simple functions where readability suffers
- In API surfaces meant for wide consumption (adds complexity)


## Advanced Topics

Constraints limit what types a type parameter can accept:

```go
// Built-in: any, comparable
func Index[T comparable](s []T, v T) int {
    for i, e := range s {
        if e == v {
            return i
        }
    }
    return -1
}
```

Custom constraints are interface types:

```go
type Number interface {
    ~int | ~int64 | ~float64
}

func Sum[T Number](values []T) T {
    var sum T
    for _, v := range values {
        sum += v
    }
    return sum
}
```

The `~` allows types with the same underlying type:

```go
type MyInt int // also satisfies Number
```

Types can also be parameterized:

```go
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(v T) {
    s.items = append(s.items, v)
}

func (s *Stack[T]) Pop() (T, bool) {
    if len(s.items) == 0 {
        var zero T
        return zero, false
    }
    v := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return v, true
}
```


## Summary

Generics make Go more expressive without sacrificing its simplicity or performance.

## Related Posts

- Error Handling in Go
- Structs and Methods in Go


**Tags:** `go` `generics` `type-parameters` `constraints` 

## References

 - [Go 1.18 release notes](https://go.dev/doc/go1.18)
 - [Go blog: Generics](https://go.dev/blog/intro-generics)

