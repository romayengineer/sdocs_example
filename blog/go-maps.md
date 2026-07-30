# Maps in Go

📌 **Status: published**

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | beginner |
| **Reading time** | 4 min |
| **Word count** | ~600 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |


## Prerequisites

- Basic Go syntax
- Slices in Go


## Overview

Maps are Go's built-in associative data type, mapping keys to values.

## Basic Usage

```go
// Nil map (cannot write to it)
var m map[string]int

// Using make
m = make(map[string]int)

// Using literal
m := map[string]int{
    "alice": 30,
    "bob":   25,
}
```


## Examples

```go
m["alice"] = 31
age := m["alice"]

// "ok" pattern to check existence
age, ok := m["charlie"]
if !ok {
    fmt.Println("charlie not found")
}

// Delete
delete(m, "bob")
```

Map iteration order is random in Go:

```go
for k, v := range m {
    fmt.Printf("%s: %d\n", k, v)
}

// Keys only
for k := range m {
    fmt.Println(k)
}
```

```go
// Map of slices
teams := map[string][]string{
    "dev":  {"alice", "bob"},
    "ops":  {"charlie"},
}

// Map of maps (nested)
scores := map[string]map[string]int{
    "alice": {"math": 95, "science": 88},
}
```


## Common Pitfalls

- Writing to a nil map causes a panic — always initialize with `make` or a literal
- Reading from a nil map returns the zero value (no panic)
- Maps are reference types — assigning or passing a map shares the underlying data
- Not safe for concurrent use without synchronization


## Advanced Topics

Maps are implemented as hash tables. Key types must be comparable (using `==`). Slices, maps, and functions cannot be used as keys.


## Summary

Maps are efficient, flexible, and widely used across Go programs for lookups, caches, and grouping data.

## Related Posts

- Slices and Arrays in Go
- Structs and Methods in Go


**Tags:** `go` `maps` `data-structures` 

## References

 - [Go blog: Maps](https://go.dev/blog/maps)
 - [Go by example: Maps](https://gobyexample.com/maps)

