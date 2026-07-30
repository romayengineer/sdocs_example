# Slices and Arrays in Go


📌 **Status: published`

| | |
|---|---|
| **Date** | 2026-07-30 |
| **Last updated** | 2026-07-30 |
| **Category** | tutorial |
| **Difficulty** | beginner |
| **Reading time** | 5 min |
| **Word count** | ~750 |
| **Author** | Structured Docs |
| **Language** | Go |
| **Version** | Go 1.21 |
| **License** | CC-BY-4.0 |

## Prerequisites

- Basic Go syntax

## Overview

Arrays and slices are fundamental data structures in Go. Arrays have a fixed size, while slices are dynamically-sized views into arrays.

## Basic Usage

## Arrays

Arrays are declared with a fixed length:

```go
var nums [3]int = [3]int{1, 2, 3}
letters := [5]string{"a", "b", "c", "d", "e"}
```

Array length is part of the type — `[3]int` and `[4]int` are different types.

## Slices

Slices are more flexible. They are declared without a length:

```go
nums := []int{1, 2, 3}
var s []int // nil slice, len=0
```

### Make

Use `make` to create slices with a backing array:

```go
s := make([]int, 5)       // len=5, cap=5
s := make([]int, 3, 5)    // len=3, cap=5
```

### Append

`append` grows the slice dynamically:

```go
var s []int
s = append(s, 1)
s = append(s, 2, 3, 4)
s = append(s, []int{5, 6}...)
```

### Slice Expressions

Slicing creates a new slice backed by the same array:

```go
a := []int{0, 1, 2, 3, 4}
b := a[1:4] // [1, 2, 3]
c := a[:2]  // [0, 1]
d := a[3:]  // [3, 4]
```

### Copy

`copy` copies elements between slices:

```go
src := []int{1, 2, 3}
dst := make([]int, len(src))
n := copy(dst, src) // n = 3
```

## Capacity and Growth

A slice has both length and capacity. When `append` exceeds capacity, Go allocates a new backing array (typically doubling the capacity):

```go
s := make([]int, 0, 2)
s = append(s, 1) // cap=2
s = append(s, 2) // cap=2
s = append(s, 3) // cap=4 (doubled)
```

## Common Pitfalls

- **Aliasing**: Two slices may share the same backing array — modifying one affects the other
- **Nil vs empty**: A nil slice has no backing array; `len(nil) == 0` but `nil != []int{}`
- **Append not always safe**: When slicing, ensure you understand whether append will overwrite shared memory

Slices are the idiomatic choice in Go — arrays are rarely used directly.

## Related Posts

- Maps in Go
- Pointers in Go

**Tags:** `go` `slices` `arrays` `data-structures` 

## References

 - [Go blog: Slices](https://go.dev/blog/slices-intro)
 - [Go by example: Slices](https://gobyexample.com/slices)

